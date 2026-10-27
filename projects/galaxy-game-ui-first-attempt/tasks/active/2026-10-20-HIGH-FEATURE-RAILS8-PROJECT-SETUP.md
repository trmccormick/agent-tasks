---
status: active
resume: false
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md \
         projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: Rails 8 Project Setup — Galaxy Game UI Prototype
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-10-20
**Last Updated**: 2026-10-20

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md`
2. **Workflow Guide**: `/Users/tam0013/Documents/git/agent-tasks/QUICK_START_PLANNING_SESSION.md` (EXECUTOR section)
3. **This Task File**: Everything below

---

## Context

This task initializes the Rails 8 application foundation for the Galaxy Game UI prototype. All subsequent features (Dashboard, Wormhole Network, Admin Panel) depend on this setup being complete.

The prototype is a **frontend design sandbox** with NO database models, NO migrations, and NO background workers. All data is mocked in-memory within controllers and helper modules. Authentication required only for /admin panel; public access for /dashboard and /wormholes.

**Relevant Architecture Docs**:
- Project README: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md` — domain and design constraints
- Status tracker: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/status.md` — progress and gotchas

---

## Critical Information for This Task

### Target Application Directories
| Directory | Purpose | Status |
|-----------|---------|--------|
| `/Users/tam0013/Documents/git/galaxy-game-ui` | Local Rails 8 app (empty, ready for init) | Ready |
| `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui` | Task tracking and project docs | Ready |

### Architecture Gotchas (CRITICAL TO UNDERSTAND BEFORE STARTING)

⚠️ **GOTCHA 1: No database persistence allowed**
- ❌ Wrong: Create Rails models (`rails g model Unit`), run migrations, use ActiveRecord queries
- ✅ Right: Create a `MockData` helper module that returns mocked objects; mock data is regenerated on each request
- Why: This is a frontend prototype, not a game backend. All persistence logic belongs in the main Galaxy Game app.

⚠️ **GOTCHA 2: Design terminology MUST be Galaxy Game, NOT EVE Online**
- ❌ Wrong: Name units "Venture Class", "Orca", "Raven"; use EVE terminology
- ✅ Right: Name units "PVE Array-01", "Heavy Transport Tug Alpha"; use Galaxy Game specs from README
- Why: This prototype will be migrated into Galaxy Game. Using EVE concepts causes semantic mismatch.

⚠️ **GOTCHA 3: Rails 8 uses Importmaps by default**
- ❌ Wrong: Add Webpack, Esbuild, or other bundlers
- ✅ Right: Use Rails 8 Importmaps/Propshaft as-is; keep asset pipeline defaults
- Why: Overriding defaults adds complexity to a prototype that should be lightweight.

⚠️ **GOTCHA 4: Tailwind config must match dark sci-fi aesthetic**
- ❌ Wrong: Use default Tailwind colors (light blues, grays)
- ✅ Right: Customize Tailwind config with dark space theme, neon accents (purples, cyans, golds)
- Why: Design consistency with Galaxy Game brand identity (see reference dashboard in eve_dashboard folder for inspiration).

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/`, do NOT paste in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Rails 8 Project Setup — Galaxy Game UI Prototype
**Status**: backlog → active
**Date**: 2026-10-20

### What I'm About to Do
Initialize a Rails 8 application at `/Users/tam0013/Documents/git/galaxy-game-ui` with Tailwind CSS, basic routing (dashboard, wormholes, admin), and a MockData helper module. No database models or migrations. All views will render mocked data structures that match Galaxy Game domain terminology.

### Files I'll Create/Modify
| File | Purpose | Status |
|---|---|---|
| `Gemfile` | Ruby dependencies (Rails 8, Tailwind, Stimulus) | pending |
| `config/routes.rb` | Root route + three main controllers | pending |
| `app/controllers/dashboard_controller.rb` | Dashboard view handler | pending |
| `app/controllers/wormholes_controller.rb` | Wormhole view handler | pending |
| `app/controllers/admin_controller.rb` | Admin view handler | pending |
| `app/helpers/mock_data.rb` | In-memory data factory (no DB) | pending |
| `config/tailwind.config.js` | Dark theme customization | pending |
| `app/views/layouts/application.html.erb` | Base layout template | pending |

### Prerequisites Completed
- ✅ Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Read README.md (project guide, constraints, domain reference)
- ✅ Read project status.md (gotchas, blocking issues)
- ✅ Understand: no database models allowed
- ✅ Understand: Galaxy Game terminology only (not EVE)
- ✅ Know: Rails 8 Importmaps/Propshaft defaults are non-negotiable

### Expected Outcomes
1. Rails 8 app initialized at `/Users/tam0013/Documents/git/galaxy-game-ui`
2. `bin/rails s` starts server on http://localhost:3000
3. `GET /` redirects to `/dashboard`
4. `GET /dashboard`, `GET /wormholes`, `GET /admin` all return HTML (no errors)
5. Views render mocked data (System Status, Units, Inventory, Market Quick-Panel data visible)
6. Tailwind CSS loads and applies dark theme styling
7. All three controllers have stubbed `index` actions
8. MockData module provides reusable data structures for all features

### Critical Gotchas I Will Avoid
- ❌ Creating ActiveRecord models — instead ✅ using MockData helper module
- ❌ Running migrations — instead ✅ keeping db/ empty
- ❌ Using EVE terminology — instead ✅ using Galaxy Game domain terms (PVE Array, Heavy Transport Tug, EAP, etc.)
- ❌ Adding bundlers/Webpack — instead ✅ keeping Rails 8 Importmaps defaults

### Success Criteria
- All 3 routes respond with HTML (curl or browser verification pasted in chat)
- No ActiveRecord errors in logs
- Tailwind classes render correctly (inspect page source in browser)
- MockData module provides sample data for all three features
- No database migrations or models present in codebase

---

**SYNTHESIS COMPLETE.** Ready to proceed with Rails 8 initialization.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

**You MAY NOT commit, push, or mark any task step as "complete" until BOTH of the following conditions are met:**

1. **Reviewer has issued a GO disposition** — a reviewer has assessed your work and disposed it as one of: `GO`, `REVISE`, or `STOP`. See [REVIEW_AGENT_GUIDE.md](/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md) for reviewer authority definitions.
2. **Human has authorized commit/push/completion** — even after reviewer GO, you may not proceed to commit or push without explicit human authorization.

### What This Means In Practice

| You CAN do | You CANNOT do |
|---|---|
| Implement changes per task instructions | Commit/push/mark-complete without both conditions above |
| Start local Rails server and verify routes | Assume reviewer GO is sufficient to proceed to commit |
| Create synthesis and evidence artifacts | Override a reviewer's REVISE or STOP disposition |
| Report results and request review | Proceed to next task before review complete |

### Review-Gate Workflow

1. **Complete all implementation and verification steps** (see below)
2. **Create evidence artifacts** (curl output, browser screenshots, test verification)
3. **STOP** — paste your evidence in chat and state: *"All implementation complete. Requesting reviewer assessment."*
4. **Wait for reviewer disposition** (GO / REVISE / STOP)
5. If **REVISE**: address issues, re-verify, return to step 3
6. If **STOP**: do not proceed; await human guidance
7. If **GO**: wait for explicit human authorization to commit/push
8. Once **both** conditions satisfied: proceed to Commit Instructions below

---

## Problem Statement

The Galaxy Game UI prototype needs a Rails 8 foundation with:
- Basic routing for three main features (Dashboard, Wormhole Network, Admin Panel)
- In-memory mock data system (no database)
- Tailwind CSS configured for dark sci-fi aesthetic
- Controller structure ready for subsequent feature development

This is the prerequisite for all downstream work.

---

## Files Involved

### Primary Files — you will create these
| File | Purpose |
|---|---|
| `Gemfile` | Ruby gem dependencies (Rails 8, Tailwind, Stimulus) |
| `config/routes.rb` | Root route + three main controllers |
| `app/controllers/dashboard_controller.rb` | Dashboard feature handler |
| `app/controllers/wormholes_controller.rb` | Wormhole feature handler |
| `app/controllers/admin_controller.rb` | Admin feature handler |
| `app/helpers/mock_data.rb` | In-memory data factory |
| `config/tailwind.config.js` | Dark theme customization |
| `app/views/layouts/application.html.erb` | Base layout with Tailwind + Stimulus |
| `app/views/dashboard/index.html.erb` | Dashboard template stub |
| `app/views/wormholes/index.html.erb` | Wormhole template stub |
| `app/views/admin/index.html.erb` | Admin template stub |

### Reference Files — read only
| File | Why You Need It |
|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md` | Domain terminology and design constraints |
| `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css` | EVE Dashboard color palette and styling patterns (dark theme reference) |
| `/Users/tam0013/Documents/git/wvu-moonshot/` | Existing Rails 8 prototype to reference for Docker, Gemfile structure, controllers, helpers pattern, and Stimulus setup. See `WVU-MOONSHOT-SETUP-REFERENCE.md` for what to adapt. |
| `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/` | Existing Galaxy Game UI patterns (colors, components, views, helpers). See `GALAXY-GAME-UI-EXTRACTION-ANALYSIS.md` for which files to study: _variables.scss, _cards.scss, _buttons.scss, admin sidebar, landing/index.html.erb view templates. |

---

## Implementation Steps (Clone-and-Adapt Approach)

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP — NO EXCEPTIONS)

```bash
cd /Users/tam0013/Documents/git/agent-tasks

# Move file to active folder
git mv projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md \
       projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md

# Update YAML status
# Open the file and change: status: backlog → status: active

# Verify only one copy exists
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks \
     -name "2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result at `active/` path.

---

### Step 1 — Create Status Synthesis Report

Create and save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/2026-10-20-SETUP-SYNTHESIS.md` using the template from earlier in this task file.

Post this in chat: *"Synthesis report created at [path]. All prerequisites met. Ready to clone and adapt WVU Moonshot."*

Wait for approval before proceeding to Step 2.

---

### Step 2 — Clone WVU Moonshot as Galaxy Game UI base

**RATIONALE**: WVU Moonshot already has working Rails 8 setup, Docker, Tailwind, Stimulus patterns, and no database — exactly what we need. Adapting is much faster than starting from scratch.

```bash
# Clone WVU Moonshot to galaxy-game-ui
cd /Users/tam0013/Documents/git
git clone https://github.com/wvulibraries/wvu-moonshot.git galaxy-game-ui

cd galaxy-game-ui

# Remove WVU-specific git history
rm -rf .git
git init
git add .
git commit -m "Initial clone of WVU Moonshot adapted for Galaxy Game UI prototype"
```

Verify: `/Users/tam0013/Documents/git/galaxy-game-ui` exists with Rails 8 structure intact.

---

### Step 3 — Remove WVU-specific code and branding

**Edit**: `Gemfile`
- Remove: Any WVU-specific gems (auth, wvu-utilities, etc.)
- Keep: Rails 8, Tailwind CSS 4, Stimulus JS, Propshaft, Importmaps
- Add: `# No database needed for prototype` (comment)

**Delete these directories** (WVU-specific content):
```bash
rm -rf app/services/wvu_*
rm -rf app/views/wvu_*
rm -rf app/models/ (if it exists and contains WVU models)
rm -rf app/mailers/
rm -rf app/jobs/
rm -rf db/migrate/ (keep db/ folder empty)
```

**Edit**: `app/views/layouts/application.html.erb`
- Remove: WVU branding, WVU logos, WVU-specific navigation
- Keep: Basic layout structure, Tailwind classes, Stimulus support, asset pipeline

Verify: Gemfile has no WVU-specific dependencies; WVU directories removed.

---

### Step 4 — Rename and customize controllers for Galaxy Game

**Rename controllers to match Galaxy Game features:**
```bash
# If WVU had app/controllers with WVU names, rename them:
# The goal: three controllers for Dashboard, Wormholes, Admin

# Create Galaxy Game controllers if needed:
cd /Users/tam0013/Documents/git/galaxy-game-ui

# Only create if they don't exist from clone
bin/rails g controller Dashboard index --skip-routes
bin/rails g controller Wormholes index --skip-routes
bin/rails g controller Admin index --skip-routes
```

**Edit**: `app/controllers/dashboard_controller.rb`
```ruby
class DashboardController < ApplicationController
  def index
    @system_status = MockDataHelper.system_status
    @player_inventory = MockDataHelper.bay_inventory
    @player_units = MockDataHelper.active_units
    @market_panel = MockDataHelper.market_quick_panel
  end
end
```

**Edit**: `app/controllers/wormholes_controller.rb`
```ruby
class WormholesController < ApplicationController
  def index
    @wormhole_network = MockDataHelper.wormhole_network
    @transit_routes = MockDataHelper.transit_routes
  end
end
```

**Edit**: `app/controllers/admin_controller.rb`
```ruby
class AdminController < ApplicationController
  def index
    @ai_manager = MockDataHelper.ai_manager_status
    @economic_indices = MockDataHelper.economic_indices
    @event_log = MockDataHelper.sim_engine_events
  end
end
```

Verify: Each controller has an index action; data passed to views via instance variables.

---

### Step 5 — Configure routes

**Edit**: `config/routes.rb`

Replace with:
```ruby
Rails.application.routes.draw do
  # Root route
  root "dashboard#index"

  # Feature routes
  get "dashboard", to: "dashboard#index"
  get "wormholes", to: "wormholes#index"
  get "admin", to: "admin#index"

  # Health check
  get "up" => "rails/health#show", as: :rails_health_check
end
```

Verify: routes file is correct, no syntax errors.

---

### Step 6 — Create MockDataHelper module

**Create**: `app/helpers/mock_data.rb`

```ruby
module MockDataHelper
  # Returns mock character and settlement data
  def self.character_at_settlement(settlement_id = :antollare)
    {
      character_name: "Commander Nova",
      settlement: "ANTOLLARE III",
      isk_balance: 15_000_000,
      location: "Bay 4, Dock Station A"
    }
  end

  # Returns mock research queue
  def self.research_queue
    [
      { blueprint: "Structural I-Beams", progress: 65, eta: "2h 15m", level: 2 },
      { blueprint: "Refined Copper Sheets", progress: 40, eta: "5h 30m", level: 3 }
    ]
  end

  # Returns mock local facilities
  def self.local_facilities
    [
      { name: "Factory Unit Alpha", specializes_in: "Structural Components", status: "Manufacturing", utilization: 85 },
      { name: "Refinery Module Beta", specializes_in: "Ores & Metals", status: "Idle", utilization: 0 },
      { name: "Research Lab Gamma", specializes_in: "Blueprints", status: "Active", utilization: 100 }
    ]
  end

  # Returns mock local inventory
  def self.bay_inventory
    [
      { resource: "Compressed Blue Ice", quantity: 850, max: 1000, unit: "units" },
      { resource: "Oxygen Isotopes", quantity: 620, max: 800, unit: "units" },
      { resource: "Heavy Water", quantity: 340, max: 500, unit: "units" },
      { resource: "Exotic Matter (EM)", quantity: 45, max: 200, unit: "units" }
    ]
  end

  # Returns mock manufacturing queue
  def self.manufacturing_queue
    [
      { job: "Structural I-Beams x500", facility: "Factory Unit Alpha", progress: 72, eta: "1h 45m", status: "In Progress" },
      { job: "Refined Copper x200", facility: "Factory Unit Alpha", progress: 0, eta: "3h 20m", status: "Queued" }
    ]
  end

  # Returns mock player ships
  def self.player_ships
    [
      { name: "Heavy Transport Tug Alpha", type: "Transport", status: "Docked", location: "ANTOLLARE III" },
      { name: "Logistics Hauler Beta", type: "Light Transport", status: "Docked", location: "ANTOLLARE III" },
      { name: "Scout Vessel Gamma", type: "Scout", status: "In Transit", destination: "Jita Hub", eta: "4h 20m" }
    ]
  end

  # Returns mock NPC buy orders
  def self.npc_buy_orders
    [
      { corporation: "LDC (Lunar Development Corp)", needs: "Compressed Blue Ice", quantity: 500, price_per_unit: 1200, total: 600_000 },
      { corporation: "AstroLift Logistics", needs: "Oxygen Isotopes", quantity: 300, price_per_unit: 850, total: 255_000 },
      { corporation: "MDC (Mars Development Corp)", needs: "Heavy Water", quantity: 200, price_per_unit: 2100, total: 420_000 }
    ]
  end

  # Returns mock player missions (escalated buy orders)
  def self.active_missions
    [
      { mission: "Secure Ice Delivery", offer: "LDC", reward: 150_000, deadline: "6 hours", status: "Available" },
      { mission: "Emergency Isotope Supply", offer: "AstroLift", reward: 85_000, deadline: "2 hours", status: "In Progress" }
    ]
  end

  # Returns mock wormhole network
  def self.wormhole_network
    [
      { system: "ANTOLLARE III", type: "Natural", stability: "95%", decay_timer: "22 hours", em_potential: "High" },
      { system: "Enedore", type: "Artificial", stability: "100%", decay_timer: "N/A", em_potential: "Medium" },
      { system: "Jita Hub", type: "Artificial", stability: "100%", decay_timer: "N/A", em_potential: "None" }
    ]
  end

  # Returns mock AstroLift routes
  def self.astrolift_routes
    [
      { route: "ANTOLLARE III → Enedore → Jita Hub", hops: 2, time: "4.5h", price: 50_000, capacity: "5000 m³" },
      { route: "ANTOLLARE III → Jita Hub (direct)", hops: 1, time: "2h", price: 85_000, capacity: "3000 m³" }
    ]
  end

  # Returns mock NPC corporations
  def self.npc_corporations
    [
      { name: "AstroLift", status: "Operating", current_activity: "Scheduling cycler departures", inventory_value: 2_500_000 },
      { name: "LDC", status: "Operating", current_activity: "Importing compressed blue ice", inventory_value: 1_800_000 },
      { name: "MDC", status: "Operating", current_activity: "Harvesting metals", inventory_value: 3_200_000 }
    ]
  end

  # Returns mock AI Manager status
  def self.ai_manager_status
    {
      directives: [
        { directive: "Expansion to Alpha Centauri", status: "Active", progress: "67%" },
        { directive: "Market Stabilization", status: "Active", progress: "42%" }
      ],
      priorities: { harvester: 60, market_import: 30, expansion: 10 },
      next_tick: "2026-10-20 18:00 UTC"
    }
  end

  # Returns mock Digital Twin scenarios
  def self.digital_twin_scenarios
    [
      { name: "Scenario A: 60% Harvester Focus", status: "Simulated", outcome: "Higher production, lower NPC fulfillment", time_to_stable: "12 days" },
      { name: "Scenario B: Balanced AI", status: "Simulated", outcome: "Mixed results, economy stable", time_to_stable: "8 days" }
    ]
  end

  # Returns mock buy order status
  def self.buy_order_status
    [
      { item: "Compressed Blue Ice", quantity: 500, npc_offering: "LDC", status: "Unfulfilled", escalation_eta: "2h" },
      { item: "Oxygen Isotopes", quantity: 300, npc_offering: "AstroLift", status: "Mission Posted", reward: 85_000 },
      { item: "Heavy Water", quantity: 200, npc_offering: "MDC", status: "AI Fulfilling (Harvester)", eta: "3h" }
    ]
  end
end
```

Verify: file exists at `app/helpers/mock_data.rb` with all methods defined.

---

### Step 7 — Customize Tailwind config

**Edit**: `config/tailwind.config.js`

Ensure these colors are in the theme extend section:
```javascript
colors: {
  'galaxy-dark': '#0a0a0a',      // Primary background
  'galaxy-panel': '#1a1a2e',     // Panel background
  'galaxy-accent': '#00d4ff',    // Cyan accent (primary)
  'galaxy-secondary': '#9d4edd', // Purple accent
  'galaxy-text': '#e0e0e0',      // Primary text
  'galaxy-text-dim': '#8892b0',  // Dim text
  'galaxy-success': '#06ffa5',   // Success green
  'galaxy-warning': '#ffd60a',   // Warning yellow
  'galaxy-danger': '#ff006e'     // Danger pink
}
```

Verify: config file has Galaxy Game color palette defined.

---

### Step 8 — Create base layout template

**Edit**: `app/views/layouts/application.html.erb`

Ensure it includes:
```erb
<!DOCTYPE html>
<html>
  <head>
    <title>Galaxy Game UI Prototype</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body class="bg-galaxy-dark text-galaxy-text">
    <!-- Navigation -->
    <nav class="border-b border-galaxy-accent/30 bg-galaxy-panel">
      <div class="max-w-7xl mx-auto px-4 py-4 flex gap-6">
        <%= link_to "Dashboard", dashboard_path, class: "hover:text-galaxy-accent" %>
        <%= link_to "Wormholes", wormholes_path, class: "hover:text-galaxy-accent" %>
        <%= link_to "Admin", admin_path, class: "hover:text-galaxy-accent" %>
      </div>
    </nav>

    <!-- Main content -->
    <main class="max-w-7xl mx-auto px-4 py-8">
      <%= yield %>
    </main>
  </body>
</html>
```

Verify: layout template has navigation bar and renders child views.

---

### Step 9 — Create view stubs for three features

**Create**: `app/views/dashboard/index.html.erb`
```erb
<div class="space-y-6">
  <h1 class="text-3xl font-bold text-galaxy-accent">Player Dashboard</h1>
  
  <div class="bg-galaxy-panel border border-galaxy-accent/30 rounded p-4">
    <h2 class="text-xl font-semibold text-galaxy-accent mb-2">Character Status</h2>
    <p class="text-galaxy-text"><strong>Settlement:</strong> <%= @system_status[:system_name] %></p>
    <p class="text-galaxy-text"><strong>ISK Balance:</strong> <%= @system_status[:player_isk] %></p>
  </div>

  <div class="bg-galaxy-panel border border-galaxy-accent/30 rounded p-4">
    <h2 class="text-xl font-semibold text-galaxy-accent mb-2">Mock Data Test</h2>
    <p class="text-galaxy-text">Dashboard loaded successfully. See console for data structures.</p>
    <pre class="bg-galaxy-dark p-2 mt-2 text-xs text-galaxy-text-dim overflow-auto">
      <%= @system_status.inspect %>
    </pre>
  </div>
</div>
```

**Create**: `app/views/wormholes/index.html.erb`
```erb
<div class="space-y-6">
  <h1 class="text-3xl font-bold text-galaxy-accent">Wormhole Network</h1>
  
  <div class="bg-galaxy-panel border border-galaxy-accent/30 rounded p-4">
    <h2 class="text-xl font-semibold text-galaxy-accent mb-2">Settlement Network Map</h2>
    <p class="text-galaxy-text">Wormhole network feature placeholder. Data loaded from MockDataHelper.</p>
  </div>
</div>
```

**Create**: `app/views/admin/index.html.erb`
```erb
<div class="space-y-6">
  <h1 class="text-3xl font-bold text-galaxy-accent">Admin Control Suite</h1>
  
  <div class="bg-galaxy-panel border border-galaxy-accent/30 rounded p-4">
    <h2 class="text-xl font-semibold text-galaxy-accent mb-2">AI Manager Status</h2>
    <p class="text-galaxy-text">Admin panel feature placeholder. Data loaded from MockDataHelper.</p>
  </div>
</div>
```

Verify: all three view files exist and render successfully.

---

### Step 10 — Start Rails server and verify routes

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# Install dependencies
bundle install
yarn install

# Start Rails server
bin/rails s

# In another terminal, verify routes work
curl http://localhost:3000/dashboard
curl http://localhost:3000/wormholes
curl http://localhost:3000/admin

# Copy curl output into chat with screenshot of browser showing Dashboard view
```

Verify:
- `bin/rails s` starts without errors
- All three routes return HTML (not 404)
- Tailwind CSS classes render (inspect page source in browser)
- MockDataHelper provides data visible in views
- No ActiveRecord errors in server logs

---

## Acceptance Criteria
- [x] Rails 8 app initialized with Tailwind CSS
- [x] Three controllers (Dashboard, Wormholes, Admin) with `index` actions
- [ ] GET / returns 200 and renders dashboard directly (no redirect)
- [ ] GET /admin returns 302 redirect to login (authentication required)
- [ ] All three routes return HTML with no errors
- [x] MockData helper module provides reusable data for all features
- [x] Tailwind config customized for dark sci-fi aesthetic
- [x] Base layout template renders with navigation
- [x] No ActiveRecord models, migrations, or database references
- [x] Local Rails server starts and serves all routes
- [x] Changes committed to galaxy-game-ui main repo

---

## Commit Instructions

> ⚠️ **PRE-COMMIT CHECK**: Before running any commands below, confirm:
> - [ ] Reviewer has issued GO disposition
> - [ ] Human has explicitly authorized commit/push
>
> If either condition is unmet, DO NOT proceed.

Run git commands on **host only**:
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

git add .
git commit -m "feat: Rails 8 project setup with Tailwind, routes, and mock data module"
git push

# Then move task file to completed/
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md \
       projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md
git add projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md
git commit -m "chore: move Rails 8 setup task to completed/"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: All downstream tasks (Dashboard, Wormhole Network, Admin Panel)
**Related tasks**: Dashboard feature, Wormhole Network feature, Admin Panel feature

---

## Progress / Resume

*Updated by session whenever it stops before task completion.*

- **Done so far**: [to be filled by agent]
- **Next step**: [to be filled by agent]
- **Last verified fact (with command)**: [to be filled by agent]
- **Local-only commits (`git log --oneline origin/main..HEAD`)**: [to be filled by agent]
- **Owning session / window label**: [to be filled by agent]

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Evidence basis**: [direct verification / reported by agent]

### What was changed
- Initialized Rails 8 app at `/Users/tam0013/Documents/git/galaxy-game-ui`
- Created three controllers: Dashboard, Wormholes, Admin
- Configured Tailwind CSS with dark sci-fi theme
- Created MockData helper module with sample data structures
- Updated routing to support three main features
- Base layout template with navigation

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary

HANDOFF SUMMARY: Rails 8 app initialized with routing, Tailwind config, MockData module | Three controllers ready for feature development | Next: Dispatch Task 2 (Dashboard feature implementation)
