---
status: drafts
resume: false
priority: HIGH  
type: feature
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded (dashboard + CSS integration only, no wormholes/blueprints/admin)
- [ ] Dependencies listed and verified
- [ ] No contradictions in specification
- [ ] Review agent disposition: GO

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/drafts/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/drafts/[FILENAME].md \
         projects/galaxy-game-ui/tasks/active/[FILENAME].md
  Then open the moved file and change: status: drafts → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: drafts → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: Implement Dashboard with GameState + Role-Based Views
**Status**: DRAFTS  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-10-23  
**Last Updated**: 2026-10-23  

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md`
3. **Context**: See Status & Context below

---

## Context

After Phase 4 (Landing Page), the app has a proper public entry point with Sign In → login form and About page. Now we need to build the authenticated Dashboard — the player's command center after logging in. The dashboard loads global game world data from a shared `GameState` record.

**Architecture decision**: The game world is multi-user and shared. All players see the same systems, settlements, and market data. Player-specific views (missions, inventory) are filtered from global game state. This mirrors the galaxyGame pattern where global models (SolarSystem, Settlement) are queried, not stored per-player.

We need to:
1. Create `GameState` model with JSONB `world_data` field (singleton record, id=1)
2. Create `GameDataJsonLoader` service to load `/data/imports/game_data.json` into GameState
3. Create sample `game_data.json` with systems, settlements, missions, market data
4. Update `db/seeds.rb` to call GameDataJsonLoader after UserCsvLoader
5. Update `DashboardController` to query `GameState.current.world_data`
6. Create role-based views: `dashboard/player.html.erb` (command center) + `dashboard/admin.html.erb` (telemetry)
7. Apply EVE Dashboard CSS foundation to both views
8. Set root redirect to dashboard after login

**Important**: Game state is stored as JSONB in the `GameState` record. This allows:
- Fast prototype iteration (no DB migrations for each data field)
- Random mutation for UI testing (e.g., settlement counts change for demo)
- Later migration to real Settlement/SolarSystem models without changing the loader

---

## Current State

### GameState Model Does Not Exist
The fresh app has no GameState model yet. We need to create it with:
```ruby
class GameState < ApplicationRecord
  # Singleton record (always id=1)
  self.table_name = 'game_states'
  
  # Simulation time tracking
  validates :year, :day, :speed, presence: true
  
  # World data stored as JSONB
  # world_data = {
  #   systems: [...],
  #   settlements: [...],
  #   market_orders: [...],
  #   missions: [...]
  # }
  
  def self.current
    find_or_create_by(id: 1)
  end
end
```

### Dashboard Routes Exist but Redirect is Missing
Post-authentication redirect needs to point to `/dashboard`, not stay on landing page.

### Dashboard Views Partially Started
Dashboard view HTML exists but:
- References non-existent MockDataHelper
- Has minimal styling (flat panels)
- Not split into player vs admin views

### Root Route
Currently `root "landing#index"` — after login, the user should land on dashboard, not landing. The auth redirect will handle this via Devise-style pattern or custom session controller logic.

---

## Implementation Steps

### Step 1: Create GameState Model & Migration
Create migration:
```bash
bin/rails generate model GameState year:integer day:float running:boolean speed:integer last_updated_at:datetime world_data:jsonb
```

Modify model file `app/models/game_state.rb`:
```ruby
class GameState < ApplicationRecord
  # Singleton pattern — GameState.current always returns id=1
  def self.current
    find_or_create_by(id: 1) do |gs|
      gs.year = Date.today.year
      gs.day = Date.today.yday
      gs.speed = 3
      gs.running = false
      gs.last_updated_at = Time.current
      gs.world_data = { systems: [], settlements: [], market_orders: [], missions: [] }
    end
  end

  validates :year, :day, :speed, presence: true
  validates :speed, numericality: { greater_than: 0, less_than_or_equal_to: 5 }

  # Game time tracking methods
  def toggle_running!
    self.running = !self.running
    self.last_updated_at = Time.current if self.running
    save!
  end

  def update_time!
    return unless running
    return if last_updated_at.nil?
    
    time_elapsed_seconds = Time.current - last_updated_at
    days_elapsed = time_elapsed_seconds / seconds_per_game_day
    
    self.day += days_elapsed
    while self.day >= 365
      self.year += 1
      self.day -= 365
    end
    
    self.last_updated_at = Time.current
    save!
  end

  def seconds_per_game_day
    case speed
    when 1 then 300  # 5 min = 1 day
    when 2 then 120  # 2 min = 1 day
    when 3 then 60   # 1 min = 1 day
    when 4 then 30   # 30 sec = 1 day
    when 5 then 10   # 10 sec = 1 day
    else 60
    end
  end
end
```

### Step 2: Create GameDataJsonLoader Service
Create `app/services/game_data_json_loader.rb`:
```ruby
class GameDataJsonLoader
  Result = Struct.new(:success?, :record_count, :message)

  DEFAULT_PATH = Rails.root.join('data', 'imports', 'game_data.json').freeze

  def self.call(path = DEFAULT_PATH)
    new(path).call
  end

  def initialize(path = DEFAULT_PATH)
    @path = path
  end

  def call
    return Result.new(false, 0, "File not found: #{@path}") unless File.exist?(@path)

    begin
      data = JSON.parse(File.read(@path))
      game_state = GameState.current
      game_state.update!(world_data: data)
      
      Result.new(true, data.size, "GameState loaded with #{data.keys.join(', ')}")
    rescue JSON::ParseError => e
      Result.new(false, 0, "JSON parse error: #{e.message}")
    rescue StandardError => e
      Result.new(false, 0, "Error: #{e.message}")
    end
  end
end
```

### Step 3: Create Sample game_data.json
Create `data/imports/game_data.json`:
```json
{
  "systems": [
    {
      "id": 1,
      "name": "Proxima Centauri",
      "type": "star",
      "habitable_zones": 2,
      "settlements_count": 3
    },
    {
      "id": 2,
      "name": "TRAPPIST-1",
      "type": "star",
      "habitable_zones": 4,
      "settlements_count": 5
    }
  ],
  "settlements": [
    {
      "id": 1,
      "name": "Settlement Alpha",
      "system_id": 1,
      "population": 5000,
      "resources": { "regolith": 10000, "water": 5000, "metals": 2000 }
    },
    {
      "id": 2,
      "name": "Outpost Beta",
      "system_id": 1,
      "population": 1200,
      "resources": { "regolith": 3000, "water": 1500, "metals": 500 }
    },
    {
      "id": 3,
      "name": "Colony Gamma",
      "system_id": 2,
      "population": 8000,
      "resources": { "regolith": 15000, "water": 8000, "metals": 4000 }
    }
  ],
  "missions": [
    {
      "id": 1,
      "title": "Scout Settlement Alpha",
      "type": "reconnaissance",
      "status": "available",
      "reward": 5000
    },
    {
      "id": 2,
      "title": "Transport Supplies to Outpost Beta",
      "type": "logistics",
      "status": "available",
      "reward": 3000
    },
    {
      "id": 3,
      "title": "Explore TRAPPIST-1 System",
      "type": "exploration",
      "status": "available",
      "reward": 10000
    }
  ],
  "market_orders": [
    {
      "id": 1,
      "type": "buy",
      "asset": "Regolith",
      "quantity": 1000,
      "price_per_unit": 50,
      "posted_by": "Settlement Alpha"
    },
    {
      "id": 2,
      "type": "sell",
      "asset": "Water",
      "quantity": 500,
      "price_per_unit": 75,
      "posted_by": "Outpost Beta"
    },
    {
      "id": 3,
      "type": "buy",
      "asset": "Rare Metals",
      "quantity": 100,
      "price_per_unit": 500,
      "posted_by": "Colony Gamma"
    }
  ]
}
```

### Step 4: Update db/seeds.rb
Add GameDataJsonLoader call after UserCsvLoader:
```ruby
# Load demo users
user_result = UserCsvLoader.call(Rails.root.join('data/imports/users.csv'))
puts "✅ Users loaded: #{user_result.message}"

# Load game world data into GameState
game_result = GameDataJsonLoader.call(Rails.root.join('data/imports/game_data.json'))
puts "✅ Game data loaded: #{game_result.message}"
```

### Step 5: Create DashboardController with Role-Based Routing
Update `app/controllers/dashboard_controller.rb`:
```ruby
class DashboardController < ApplicationController
  before_action :authenticate_user!

  def index
    @game_state = GameState.current
    @world_data = @game_state.world_data
    
    if current_user.admin?
      render :admin_dashboard
    else
      render :player_dashboard
    end
  end

  private

  def authenticate_user!
    redirect_to session_new_path unless current_user
  end
end
```

### Step 6: Create Role-Based Views
Create `app/views/dashboard/player_dashboard.html.erb`:
```erb
<div class="dashboard-container">
  <h1>Command Center</h1>
  
  <!-- Systems Panel -->
  <div class="panel">
    <h2>Known Systems</h2>
    <div class="panel-content">
      <% @world_data['systems']&.each do |system| %>
        <div class="system-card">
          <h3><%= system['name'] %></h3>
          <p>Type: <%= system['type'] %></p>
          <p>Habitable Zones: <%= system['habitable_zones'] %></p>
          <p>Settlements: <%= system['settlements_count'] %></p>
        </div>
      <% end %>
    </div>
  </div>

  <!-- Settlements Panel -->
  <div class="panel">
    <h2>Settlements</h2>
    <div class="panel-content">
      <% @world_data['settlements']&.each do |settlement| %>
        <div class="settlement-card">
          <h3><%= settlement['name'] %></h3>
          <p>System: <%= @world_data['systems']&.find { |s| s['id'] == settlement['system_id'] }&.dig('name') %></p>
          <p>Population: <%= settlement['population'] %></p>
        </div>
      <% end %>
    </div>
  </div>

  <!-- Missions Panel -->
  <div class="panel">
    <h2>Available Missions</h2>
    <div class="panel-content">
      <% @world_data['missions']&.each do |mission| %>
        <div class="mission-card">
          <h3><%= mission['title'] %></h3>
          <p>Type: <%= mission['type'] %></p>
          <p>Reward: <%= mission['reward'] %> credits</p>
          <span class="badge"><%= mission['status'] %></span>
        </div>
      <% end %>
    </div>
  </div>

  <!-- Market Panel -->
  <div class="panel">
    <h2>Market Orders</h2>
    <div class="panel-content">
      <% @world_data['market_orders']&.each do |order| %>
        <div class="market-card">
          <h3><%= order['asset'] %></h3>
          <p><%= order['type'].upcase %>: <%= order['quantity'] %> @ <%= order['price_per_unit'] %> credits/unit</p>
          <p>Posted by: <%= order['posted_by'] %></p>
        </div>
      <% end %>
    </div>
  </div>
</div>

<style>
  :root {
    --bg: #0e1114;
    --panel: #161b21;
    --panel2: #1d242c;
    --line: #2a333d;
    --text: #d8dee6;
    --muted: #8a97a6;
    --accent: #4aa8ff;
  }

  .dashboard-container {
    background: var(--bg);
    color: var(--text);
    padding: 2rem;
  }

  .panel {
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 10px;
    padding: 1.5rem;
    margin-bottom: 2rem;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  }

  .panel h2 {
    color: var(--accent);
    margin-bottom: 1rem;
  }

  .panel-content {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 1rem;
  }

  .system-card, .settlement-card, .mission-card, .market-card {
    background: var(--panel2);
    border: 1px solid var(--line);
    border-radius: 6px;
    padding: 1rem;
  }

  .system-card h3, .settlement-card h3, .mission-card h3, .market-card h3 {
    color: var(--accent);
    margin-bottom: 0.5rem;
  }

  .system-card p, .settlement-card p, .mission-card p, .market-card p {
    color: var(--muted);
    font-size: 0.9rem;
    margin: 0.25rem 0;
  }

  .badge {
    display: inline-block;
    background: var(--accent);
    color: var(--bg);
    padding: 0.25rem 0.5rem;
    border-radius: 3px;
    font-size: 0.8rem;
    margin-top: 0.5rem;
  }
</style>
```

Create `app/views/dashboard/admin_dashboard.html.erb`:
```erb
<div class="admin-dashboard">
  <h1>Admin Control Center</h1>
  
  <!-- Stats Overview -->
  <div class="stats-panel">
    <div class="stat-card">
      <h3>Total Systems</h3>
      <p class="stat-value"><%= @world_data['systems']&.length %></p>
    </div>
    <div class="stat-card">
      <h3>Total Settlements</h3>
      <p class="stat-value"><%= @world_data['settlements']&.length %></p>
    </div>
    <div class="stat-card">
      <h3>Active Users</h3>
      <p class="stat-value"><%= User.count %></p>
    </div>
    <div class="stat-card">
      <h3>Total Missions</h3>
      <p class="stat-value"><%= @world_data['missions']&.length %></p>
    </div>
  </div>

  <!-- Admin Sections -->
  <div class="admin-sections">
    <%= link_to 'View All Systems', '#', class: 'admin-link' %>
    <%= link_to 'Manage Settlements', '#', class: 'admin-link' %>
    <%= link_to 'Manage Users', '#', class: 'admin-link' %>
    <%= link_to 'Game Time Controls', '#', class: 'admin-link' %>
  </div>
</div>

<style>
  :root {
    --bg: #0e1114;
    --panel: #161b21;
    --panel2: #1d242c;
    --line: #2a333d;
    --text: #d8dee6;
    --muted: #8a97a6;
    --accent: #4aa8ff;
  }

  .admin-dashboard {
    background: var(--bg);
    color: var(--text);
    padding: 2rem;
  }

  .admin-dashboard h1 {
    color: var(--accent);
    margin-bottom: 2rem;
  }

  .stats-panel {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1rem;
    margin-bottom: 2rem;
  }

  .stat-card {
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 10px;
    padding: 1.5rem;
    text-align: center;
  }

  .stat-card h3 {
    color: var(--muted);
    font-size: 0.9rem;
    margin-bottom: 0.5rem;
  }

  .stat-value {
    font-size: 2rem;
    color: var(--accent);
    font-weight: bold;
  }

  .admin-sections {
    display: flex;
    gap: 1rem;
    flex-wrap: wrap;
  }

  .admin-link {
    background: var(--accent);
    color: var(--bg);
    padding: 0.75rem 1.5rem;
    border-radius: 6px;
    text-decoration: none;
    font-weight: bold;
  }

  .admin-link:hover {
    opacity: 0.9;
  }
</style>
```

### Step 7: Update Routes
Update `config/routes.rb`:
```ruby
Rails.application.routes.draw do
  # ... existing routes ...
  
  get '/dashboard', to: 'dashboard#index'
  root 'dashboard#index'  # Changed from landing#index
end
```

### Step 8: Update Session Controller Redirect (optional)
If the session controller manually redirects after login, update it to redirect to `/dashboard`:
```ruby
# In app/controllers/sessions_controller.rb
redirect_to dashboard_path  # instead of root or landing
```

### Step 9: Verify
```bash
# Run migrations
docker exec galaxy_game bash -c "bin/rails db:migrate"

# Seed data
docker exec galaxy_game bash -c "bin/rails db:seed"

# Test GameState.current
docker exec galaxy_game bash -c "bin/rails runner 'puts GameState.current.world_data.keys'"

# Start rails server and visit http://localhost:3000/dashboard
```

---

## Gotchas

1. **GameState is singleton**: Always use `GameState.current` to fetch/create id=1. Never create multiple GameState records.

2. **world_data is JSONB**: Treat it as a plain Hash in controllers/views. Rails handles JSON serialization automatically.

3. **game_data.json must exist**: If the file is missing or malformed, GameDataJsonLoader will fail. The file path is `/data/imports/game_data.json` and is mounted into the Docker container at `/home/galaxy_game/data/imports/game_data.json`.

4. **CSV vs JSON**: UserCsvLoader reads `/data/imports/users.csv`. GameDataJsonLoader reads `/data/imports/game_data.json`. Both files must exist before running `db:seed`.

5. **Role-based rendering**: DashboardController detects `current_user.admin?` and renders different templates. Ensure User model has `role` enum with `admin` and `player` values.

6. **Post-login redirect**: If using Devise, override `after_sign_in_path_for` to return `/dashboard`. If using custom session controller, update the redirect manually.

7. **CSS conflicts**: EVE Dashboard CSS variables should not conflict with Tailwind. If they do, apply EVE styles inline in the view or create a separate stylesheet that loads after Tailwind.

8. **Empty game state**: On first run, if game_data.json is empty or has no data, panels will render empty but not error. This is fine for MVP.

---

## Acceptance Criteria

- [ ] GameState model created with year, day, speed, running, last_updated_at, world_data (JSONB) fields
- [ ] Migration created and run successfully
- [ ] GameDataJsonLoader service created and loads game_data.json into GameState.current.world_data
- [ ] game_data.json sample file created with systems, settlements, missions, market_orders
- [ ] db/seeds.rb updated to call GameDataJsonLoader.call after UserCsvLoader
- [ ] DashboardController routes to player_dashboard or admin_dashboard based on user role
- [ ] player_dashboard.html.erb displays systems, settlements, missions, market panels
- [ ] admin_dashboard.html.erb displays stats overview (total systems, settlements, users, missions)
- [ ] EVE CSS variables applied to both views (--bg, --panel, --text, --accent, etc.)
- [ ] Panel styling includes borders, border-radius, box-shadow
- [ ] Root route changed to dashboard#index (post-login landing point)
- [ ] Login → Dashboard redirect works without errors
- [ ] GET /dashboard renders without errors (logged in as player)
- [ ] GET /dashboard renders admin view (logged in as admin)
- [ ] No MockDataHelper exists (old approach removed)

---

## Dependencies

**Depends on**: 
- Task `2026-10-23-CRITICAL-SEEDS-CLEANUP-DEFAULT-USERS` (default accounts must exist)
- Task `2026-10-23-CRITICAL-LANDING-PAGE-FOR-PUBLIC-ACCESS` (root route → landing for public, login flow via /session/new)

**Blocks**: 
- Future dashboard features (missions detail, settlement management, market trading)
- Role-based admin pages (user management, system telemetry)

---

## Synthesis Report Template

```markdown
# Task Synthesis Report — Dashboard with GameState + Role-Based Views

## Completed
| Item | Status | Notes |
|------|--------|-------|
| GameState model created | ✅ / ❌ | Singleton pattern, world_data JSONB |
| Migration run | ✅ / ❌ | db/schema.rb updated |
| GameDataJsonLoader service created | ✅ / ❌ | Loads game_data.json into GameState |
| game_data.json sample created | ✅ / ❌ | systems, settlements, missions, market_orders |
| db/seeds.rb updated | ✅ / ❌ | UserCsvLoader → GameDataJsonLoader chain |
| DashboardController updated | ✅ / ❌ | Role-based rendering (player vs admin) |
| player_dashboard.html.erb created | ✅ / ❌ | 4 panels: systems, settlements, missions, market |
| admin_dashboard.html.erb created | ✅ / ❌ | Stats overview + admin links |
| EVE CSS styling applied | ✅ / ❌ | Variables, borders, shadows, colors |
| Root route → dashboard#index | ✅ / ❌ | Post-login landing |

## Verification Results
| Check | Result |
|-------|--------|
| GameState.current returns id=1 record | ✅ / ❌ |
| world_data contains systems/settlements/missions | ✅ / ❌ |
| db:seed runs without errors | ✅ / ❌ |
| Login → GET /dashboard (player) renders without errors | ✅ / ❌ |
| GET /dashboard (admin) renders admin view | ✅ / ❌ |
| CSS styling visible in browser | ✅ / ❌ |
| All 4 panels render data from world_data | ✅ / ❌ |

## Issues
- [If any issues, describe here]

## Commit Hash:
```
