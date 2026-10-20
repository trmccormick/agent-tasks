---
status: backlog
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

The prototype is a **frontend design sandbox** with NO database models, NO migrations, and NO background workers. All data is mocked in-memory within controllers and helper modules.

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

## Implementation Steps

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

Create and save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/2026-10-20-SETUP-SYNTHESIS.md` using the template above.

Post this in chat: *"Synthesis report created at [path]. All prerequisites met. Ready to initialize Rails app."*

Wait for approval before proceeding to Step 2.

---

### Step 2 — Initialize Rails 8 app

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# Initialize new Rails 8 app with Tailwind CSS
rails new . --css tailwind --skip-test --force

# This creates:
# - Gemfile with Rails 8, Tailwind, Stimulus
# - config/routes.rb (to be modified)
# - Standard Rails directory structure
# - app/assets/stylesheets/application.tailwind.css
# - config/tailwind.config.js
```

Verify output: should see "create" messages for all standard Rails directories and config files.

---

### Step 3 — Create the three controllers with stubbed actions

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# Generate the three controllers with index action
bin/rails g controller Dashboard index --skip-routes
bin/rails g controller Wormholes index --skip-routes
bin/rails g controller Admin index --skip-routes

# This creates:
# - app/controllers/dashboard_controller.rb
# - app/controllers/wormholes_controller.rb
# - app/controllers/admin_controller.rb
# - app/views/dashboard/index.html.erb (empty stub)
# - app/views/wormholes/index.html.erb (empty stub)
# - app/views/admin/index.html.erb (empty stub)
```

Verify: three controller files exist and each has an `index` action defined.

---

### Step 4 — Configure routes

**Edit**: `config/routes.rb`

**Replace** the default routes section with:
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

Verify: routes file looks correct, no syntax errors.

---

### Step 5 — Create MockData helper module

**Create**: `app/helpers/mock_data.rb`

```ruby
module MockDataHelper
  # Returns mock system status for Dashboard feature
  def self.system_status
    {
      system_name: "ANTOLLARE III",
      security_level: "1.2",
      eap: 452.5,  # Earth Anchor Price
      player_isk: 15_000_000,
      player_gcc: 2_500
    }
  end

  # Returns mock active sector units for Dashboard feature
  def self.active_units
    [
      {
        name: "PVE Array-01",
        type: "Planetary Volatiles Extractor",
        status: "Harvesting",
        location: "ANTOLLARE III - Bay 4",
        yield: "450 m³/hour"
      },
      {
        name: "Heavy Transport Tug Alpha",
        type: "Heavy Transport",
        status: "In Transit",
        location: "Wormhole Gate - 3 hops away",
        cargo: "2500 m³ (Compressed Blue Ice)"
      },
      {
        name: "Logistics Hauler Beta",
        type: "Light Transport",
        status: "Docked",
        location: "ANTOLLARE III - Docking Bay",
        cargo: "Empty"
      }
    ]
  end

  # Returns mock bay inventory for Dashboard feature
  def self.bay_inventory
    [
      { resource: "Compressed Blue Ice", quantity: 850, max: 1000, unit: "units" },
      { resource: "Oxygen Isotopes", quantity: 620, max: 800, unit: "units" },
      { resource: "Heavy Water", quantity: 340, max: 500, unit: "units" },
      { resource: "Exotic Matter (EM)", quantity: 45, max: 200, unit: "units" }
    ]
  end

  # Returns mock market quick-panel data for Dashboard feature
  def self.market_quick_panel
    [
      { commodity: "Nitrogen", buy_spread: "420-435 ISK", sell_spread: "410-425 ISK", volume: "15k units available" },
      { commodity: "Cthonic Attar", buy_spread: "5200-5450 ISK", sell_spread: "5100-5400 ISK", volume: "2.3k units available" },
      { commodity: "Structural I-Beams", buy_spread: "1200-1350 ISK", sell_spread: "1150-1300 ISK", volume: "8.7k units available" }
    ]
  end

  # Returns mock wormhole network data for Wormholes feature
  def self.wormhole_network
    [
      { system: "ANTOLLARE III", type: "Natural", mass_stability: "95%", decay_timer: "22 hours", em_potential: "High", hops_to: "Jita Hub" },
      { system: "Enedore", type: "Artificial", mass_stability: "100%", decay_timer: "N/A", em_potential: "Medium", hops_to: "Jita Hub" },
      { system: "Jita Hub", type: "Artificial", mass_stability: "100%", decay_timer: "N/A", em_potential: "None", hops_to: "Trade Hub" }
    ]
  end

  # Returns mock route data for Wormhole Transit Planner
  def self.transit_routes
    [
      { route: "ANTOLLARE III → Enedore → Jita Hub", hops: 2, estimated_time: "4.5 hours", unit_mass_restriction: "Max 50,000 kg" },
      { route: "ANTOLLARE III → Jita Hub (direct)", hops: 1, estimated_time: "2.0 hours", unit_mass_restriction: "Max 30,000 kg" }
    ]
  end

  # Returns mock AI Manager directives for Admin Panel
  def self.ai_manager_status
    {
      directives: [
        { directive: "Alpha Centauri Expansion", status: "Active", progress: "67%" },
        { directive: "Wormhole Node Scanning", status: "Active", progress: "42%" },
        { directive: "Resource Rebalancing", status: "Idle", progress: "100%" }
      ],
      next_tick: "2026-10-20 18:00 UTC",
      tick_duration: "6 hours"
    }
  end

  # Returns mock economic indices for Admin Panel
  def self.economic_indices
    {
      global_eap: 452.5,
      scc_surcharge: "0.5%",
      broker_fee: "0.3%",
      sales_tax: "3.37%"
    }
  end

  # Returns mock sim engine event log for Admin Panel
  def self.sim_engine_events
    [
      { timestamp: "2026-10-20 12:15 UTC", event: "Tick #4521 processed", details: "6 systems updated" },
      { timestamp: "2026-10-20 12:10 UTC", event: "Extraction event", details: "PVE Array-01 yielded 450 m³ Compressed Blue Ice" },
      { timestamp: "2026-10-20 12:05 UTC", event: "Trade execution", details: "Sell order matched: 500 units Nitrogen @ 425 ISK" },
      { timestamp: "2026-10-20 12:00 UTC", event: "Wormhole decay", details: "Natural wormhole in Enedore decayed after 24h" }
    ]
  end
end
```

Verify: file exists at `app/helpers/mock_data.rb` with all methods defined.

---

### Step 6 — Customize Tailwind config for dark sci-fi theme

**Edit**: `config/tailwind.config.js`

**Replace** the theme section with (inspired by EVE Dashboard `/app/static/style.css`):
```javascript
module.exports = {
  content: [
    './app/views/**/*.{erb,html}',
    './app/components/**/*.{erb,html}',
    './app/helpers/**/*.{rb}',
  ],
  theme: {
    extend: {
      colors: {
        'eve-dark': '#0e1114',      // EVE Dashboard main background
        'eve-panel': '#161b21',     // EVE Dashboard panel background
        'eve-panel2': '#1d242c',    // EVE Dashboard alt panel background
        'eve-line': '#2a333d',      // EVE Dashboard borders/lines
        'eve-text': '#d8dee6',      // EVE Dashboard text color
        'eve-muted': '#8a97a6',     // EVE Dashboard muted text
        'eve-accent': '#4aa8ff',    // EVE Dashboard bright cyan accent
        'eve-green': '#3fb950',     // EVE Dashboard green accent
        'eve-red': '#f85149',       // EVE Dashboard red accent
        'eve-gold': '#e3b341',      // EVE Dashboard gold accent
        'neon-cyan': '#00d9ff',     // Bright cyan for Galaxy Game highlights
        'neon-purple': '#b366ff',   // Purple for admin/special sections
        'neon-gold': '#ffd700',     // Gold for economic indicators
      },
      backgroundColor: {
        'glass': 'rgba(22, 27, 33, 0.6)',   // EVE panel with transparency
      },
      borderColor: {
        'glass': 'rgba(74, 168, 255, 0.2)',  // EVE accent with transparency
      },
    },
  },
  plugins: [],
}
```

**Reference**: See `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css` (lines 1-20) for official EVE Dashboard color palette.

Verify: Tailwind config is valid JavaScript syntax (no errors on save).

---

### Step 7 — Update base layout template

**Edit**: `app/views/layouts/application.html.erb`

**Replace** the entire file with:
```erb
<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
  <head>
    <title>Galaxy Game UI — Prototype</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body class="bg-eve-dark text-eve-text antialiased">
    <!-- Starfield background -->
    <div class="fixed inset-0 bg-gradient-to-br from-eve-dark via-eve-panel to-eve-dark pointer-events-none"></div>

    <!-- Navigation bar -->
    <nav class="relative z-50 bg-eve-panel border-b border-eve-line shadow-lg">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center h-16">
          <div class="flex items-center space-x-2">
            <span class="text-xl font-bold text-eve-accent">⚛ Galaxy Game</span>
          </div>
          <div class="flex space-x-6">
            <%= link_to "Dashboard", dashboard_path, class: "hover:text-eve-accent transition" %>
            <%= link_to "Wormholes", wormholes_path, class: "hover:text-eve-accent transition" %>
            <%= link_to "Admin", admin_path, class: "hover:text-eve-accent transition" %>
          </div>
        </div>
      </div>
    </nav>

    <!-- Main content -->
    <main class="relative z-10 max-w-7xl mx-auto px-4 py-8">
      <%= yield %>
    </main>

    <!-- Footer -->
    <footer class="relative z-10 border-t border-eve-line mt-12 py-6 text-center text-eve-muted">
      <p>Galaxy Game UI Prototype — Design & Interaction Sandbox</p>
    </footer>
  </body>
</html>
```

Verify: file looks correct, no ERB syntax errors.

---

### Step 8 — Add minimal content to view templates

**Edit**: `app/views/dashboard/index.html.erb`
```erb
<div class="space-y-4">
  <h1 class="text-3xl font-bold text-neon-cyan">Player Command Center</h1>
  <p class="text-gray-300">Dashboard feature — Views will be populated in next task.</p>
</div>
```

**Edit**: `app/views/wormholes/index.html.erb`
```erb
<div class="space-y-4">
  <h1 class="text-3xl font-bold text-neon-purple">Wormhole Network & Transit Hub</h1>
  <p class="text-gray-300">Wormhole feature — Views will be populated in next task.</p>
</div>
```

**Edit**: `app/views/admin/index.html.erb`
```erb
<div class="space-y-4">
  <h1 class="text-3xl font-bold text-neon-gold">Admin Telemetry & Control Panel</h1>
  <p class="text-gray-300">Admin feature — Views will be populated in next task.</p>
</div>
```

Verify: all three templates exist and contain placeholder text.

---

### Step 9 — Verify app setup with local server

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# Install dependencies (if not already done)
bundle install

# Start Rails server
bin/rails s

# Expected: server starts on http://localhost:3000
# [2026-10-20 HH:MM:SS] INFO  WEBrick 1.8.x started
```

In a separate terminal, verify routes:
```bash
curl -s http://localhost:3000 | head -20
curl -s http://localhost:3000/dashboard | head -20
curl -s http://localhost:3000/wormholes | head -20
curl -s http://localhost:3000/admin | head -20
```

All should return HTML with no errors. Paste the curl output for at least one route in your completion report.

---

### Step 10 — Commit changes to main app repository

Run on **host** (not in container):
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# Stage all changes
git add .

# Check status
git status

# Commit
git commit -m "feat: Rails 8 project setup with Tailwind, routes, and mock data module"

# Verify
git log --oneline -1
```

Paste the commit hash in your completion report.

---

## Acceptance Criteria
- [x] Rails 8 app initialized with Tailwind CSS
- [x] Three controllers (Dashboard, Wormholes, Admin) with `index` actions
- [x] Root route redirects to `/dashboard`
- [x] All three routes return HTML with no errors
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
