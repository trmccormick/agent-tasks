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

- [x] Agent Dispatch Interface section complete and accurate
- [x] All Step 0-N instructions clear and actionable
- [x] Synthesis report template ready (copy/paste)
- [x] No placeholder text in Implementation Steps
- [x] All file paths verified to exist
- [x] Architecture Gotchas specific and clear
- [x] Acceptance Criteria measurable
- [x] Dependencies clear (blocked by Task 1)

**Task is READY FOR DISPATCH after Task 1 is completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md \
         projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md"
    Only ONE result should exist at the `active/` path.

READ FIRST (after Step 0): Task file contains all prerequisites and implementation details.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat.

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: Dashboard — Player Command Center Feature
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-10-20
**Depends on**: Task 1 (Rails 8 Project Setup)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Task 1 Completion**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md` (must be done first)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md` (review Feature 1: Dashboard)
3. **Mock Data Module**: `app/helpers/mock_data.rb` in your Rails app (created in Task 1)
4. **This Task File**: Everything below

---

## Context

This task builds the **Settlement Command Center** — the player's primary interface for managing settlements, manufacturing production, resource economy, and inter-settlement supply chains. It includes four integrated panels rendering mocked Galaxy Game data.

**Key Constraint**: All data comes from `MockDataHelper` — no database queries. Views render pure mock data structures. This is a UI **prototype** designed to identify information needs; it will later integrate with existing planet generation and manufacturing chain code.

**What This Prototype Does**: Shows settlement status, active production/manufacturing, resource inventory, and supply flows between settlements. This guides UI patterns for the real backend integration.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: Use MockDataHelper exclusively for data**
- ❌ Wrong: Query ActiveRecord models or databases
- ✅ Right: Call `MockDataHelper.system_status`, `MockDataHelper.active_units`, etc. in your controller or view helper
- Why: No persistence layer exists; all data is regenerated on page reload

⚠️ **GOTCHA 2: Settlement status must distinguish manufacturing phases**
- ❌ Wrong: Show all settlements as identical cards
- ✅ Right: Use status color/badge: Growing (green), Stable (cyan), Crisis (red), Terraforming (purple)
- Why: Players must quickly see which settlements need attention

⚠️ **GOTCHA 3: Responsive grid layout is essential**
- ❌ Wrong: Fixed-width panels that break on mobile/tablet
- ✅ Right: Use Tailwind `grid-cols-1 md:grid-cols-2 lg:grid-cols-4` for responsive layouts
- Why: Dashboard must work on multiple screen sizes

⚠️ **GOTCHA 4: Production timeline must show both inputs and outputs**
- ❌ Wrong: Show only "Producing Structural I-Beams"
- ✅ Right: Show "Regolith (100 units/day) → Processed Materials → Components → Structural I-Beams (5 units remaining)"
- Why: Players need to understand manufacturing chains and resource dependencies to make strategic decisions

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start)

Create and save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/2026-10-20-DASHBOARD-SYNTHESIS.md`:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Dashboard — Player Command Center Feature
**Status**: backlog → active
**Date**: 2026-10-20

### What I'm About to Do
Build the `/dashboard` view with four integrated panels:
1. **Player Summary Header** (player name, primary settlement, ISK balance, manufacturing queue status)
2. **Active Settlements Panel** (card-based list of settlements with production status, growth rate, tax base)
3. **Manufacturing & Production** (what's being fabricated at each settlement, resource consumption, completion time)
4. **Supply Flow Network** (inter-settlement logistics via wormholes—resources in transit, estimated arrival, destination)

All data sourced from MockDataHelper. Styling uses Tailwind dark theme with glassmorphic cards.

### Files I'll Modify
| File | Purpose | Status |
|---|---|---|
| `app/views/dashboard/index.html.erb` | Main dashboard template | pending |
| `app/helpers/dashboard_helper.rb` | View logic (status badge classes, formatting) | pending |
| `app/assets/stylesheets/dashboard.css` | Component library (cards, badges, grids) | pending |

### Prerequisites Completed
- ✅ Task 1 (Rails 8 setup) completed and committed
- ✅ bin/rails s starts server without errors
- ✅ MockDataHelper module exists and provides all required data
- ✅ Read dashboard feature spec from project README
- ✅ Understand: no database queries, all data from MockDataHelper
- ✅ Understand: Galaxy Game terminology (PVE, EAP, ISK, GCC, etc.)

### Expected Outcomes
1. `GET /dashboard` returns full HTML with all four panels
2. Player Summary Header displays: player name, primary settlement, ISK balance, manufacturing queue depth
3. Active Settlements Panel shows 3 mock settlements (ANTOLLARE III, Enedore Station, Jita Mining Hub) with production status, growth rate, tax base
4. Manufacturing Queue displays 2 production jobs with: name, inputs (regolith → components), time remaining, production location
5. Supply Flow Network shows 1 inter-settlement shipment (Enedore → Jita Hub, Structural I-Beams, ETA 4 hours)
6. All panels use consistent glassmorphic styling with Tailwind dark theme
7. Layout is responsive (works on mobile/tablet/desktop)
8. No console errors; all data renders without database queries

### Critical Gotchas I Will Avoid
- ❌ Querying database — instead ✅ using MockDataHelper exclusively
- ❌ Inconsistent panel styling — instead ✅ creating reusable "glass card" component
- ❌ Fixed-width layout — instead ✅ using Tailwind responsive grids
- ❌ Different colors for same status — instead ✅ mapping statuses to consistent badge classes

### Success Criteria
- Dashboard loads and all four panels visible
- All data rendered correctly from MockDataHelper
- Settlement status badges consistently colored (Growing=green, Stable=cyan, Crisis=red, Terraforming=purple)
- Production timeline shows input → output flow
- Supply flow shows origin/destination/cargo/eta clearly
- Layout responsive on mobile, tablet, desktop
- No database errors in Rails logs
- UI patterns are clear enough to inform backend integration design

---

**SYNTHESIS COMPLETE.** Ready to build dashboard.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The Dashboard feature is the core player interface. It must display settlement/manufacturing/economic data (settlements under control, production queues, supply flows) in a clean, scannable layout. This prototype identifies what information the UI needs to show; later it will integrate with existing planet generation and manufacturing chain backend code.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose |
|---|---|
| `app/views/dashboard/index.html.erb` | Main dashboard template with four panels |
| `app/helpers/dashboard_helper.rb` | View helper logic (status badges, formatting) |
| `app/assets/stylesheets/dashboard.css` | Component library (glassmorphic cards, grids) |

### Reference Files — read only
| File | Why You Need It |
|---|---|
| `app/helpers/mock_data.rb` | Data source for all dashboard information |
| Project README Dashboard section | Feature requirements and terminology |
| `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css` | Panel styling, color variables, and button/badge patterns. Study the "panel" class and badge styling. |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP — NO EXCEPTIONS)

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md \
       projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md

# Open file and change: status: backlog → status: active

# Verify only one copy exists
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks \
     -name "2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md"
```

**Paste the find output in chat before proceeding.**

---

### Step 1 — Create Status Synthesis Report

Create and save synthesis report using the template above.

Post: *"Synthesis complete. Ready to build dashboard panels."*

Wait for approval before proceeding to Step 2.

---

### Step 2 — Create dashboard helper with view logic

**Create**: `app/helpers/dashboard_helper.rb`

```ruby
module DashboardHelper
  # Map unit status to Tailwind badge classes
  def status_badge_class(status)
    case status
    when "Harvesting"
      "bg-green-500/30 border-green-500/50 text-green-300"
    when "In Transit"
      "bg-blue-500/30 border-blue-500/50 text-blue-300"
    when "Idle"
      "bg-yellow-500/30 border-yellow-500/50 text-yellow-300"
    when "Docked"
      "bg-purple-500/30 border-purple-500/50 text-purple-300"
    else
      "bg-gray-500/30 border-gray-500/50 text-gray-300"
    end
  end

  # Format large numbers with commas
  def format_quantity(num)
    num.to_s.reverse.gsub(/(\d{3})(?=\d)/, '\\1,').reverse
  end

  # Calculate inventory fill percentage for progress bar
  def inventory_fill_percent(quantity, max)
    ((quantity.to_f / max) * 100).round(1)
  end

  # Render resource name with icon-like prefix
  def resource_icon(resource_name)
    case resource_name
    when "Compressed Blue Ice"
      "❄️ #{resource_name}"
    when "Oxygen Isotopes"
      "🌬️ #{resource_name}"
    when "Heavy Water"
      "💧 #{resource_name}"
    when "Exotic Matter (EM)"
      "⚛️ #{resource_name}"
    else
      resource_name
    end
  end
end
```

Verify: file created at `app/helpers/dashboard_helper.rb` with all helper methods.

---

### Step 3 — Create dashboard stylesheet with component library

**Create**: `app/assets/stylesheets/dashboard.css`

```css
/* Glassmorphic card component — reusable for all panels */
.glass-card {
  background: rgba(26, 31, 58, 0.6);
  border: 1px solid rgba(0, 217, 255, 0.2);
  border-radius: 0.75rem;
  backdrop-filter: blur(10px);
  padding: 1.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  transition: all 0.3s ease;
}

.glass-card:hover {
  border-color: rgba(0, 217, 255, 0.4);
  box-shadow: 0 8px 12px rgba(0, 217, 255, 0.1);
}

/* Panel titles with neon glow */
.panel-title {
  font-size: 1.125rem;
  font-weight: 600;
  color: #00d9ff;
  text-shadow: 0 0 10px rgba(0, 217, 255, 0.3);
  margin-bottom: 1rem;
  border-bottom: 2px solid rgba(0, 217, 255, 0.3);
  padding-bottom: 0.75rem;
}

/* Status badge styling */
.status-badge {
  display: inline-flex;
  align-items: center;
  padding: 0.375rem 0.75rem;
  border: 1px solid;
  border-radius: 0.25rem;
  font-size: 0.875rem;
  font-weight: 500;
}

/* Unit card in Active Sector Units panel */
.unit-card {
  background: rgba(10, 14, 39, 0.4);
  border: 1px solid rgba(0, 217, 255, 0.15);
  border-radius: 0.5rem;
  padding: 1rem;
  margin-bottom: 0.75rem;
  transition: all 0.3s ease;
}

.unit-card:hover {
  background: rgba(10, 14, 39, 0.6);
  border-color: rgba(0, 217, 255, 0.3);
}

/* Inventory progress bar */
.inventory-bar {
  background: rgba(0, 217, 255, 0.1);
  border: 1px solid rgba(0, 217, 255, 0.2);
  border-radius: 0.25rem;
  height: 1.5rem;
  overflow: hidden;
  margin-top: 0.5rem;
}

.inventory-fill {
  background: linear-gradient(90deg, #00d9ff, #b366ff);
  height: 100%;
  transition: width 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.75rem;
  font-weight: 600;
  color: #1a1f3a;
  padding: 0 0.25rem;
}

/* Market table styling */
.market-table {
  width: 100%;
  border-collapse: collapse;
}

.market-table thead {
  border-bottom: 2px solid rgba(0, 217, 255, 0.3);
}

.market-table th {
  color: #00d9ff;
  text-align: left;
  padding: 0.75rem;
  font-weight: 600;
  font-size: 0.875rem;
}

.market-table td {
  padding: 0.75rem;
  border-bottom: 1px solid rgba(0, 217, 255, 0.1);
  font-size: 0.875rem;
}

.market-table tbody tr:hover {
  background: rgba(0, 217, 255, 0.05);
}

/* System header styling */
.system-header {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  margin-bottom: 2rem;
}

.system-stat {
  background: rgba(26, 31, 58, 0.6);
  border: 1px solid rgba(0, 217, 255, 0.15);
  border-radius: 0.75rem;
  padding: 1rem;
  text-align: center;
}

.system-stat-label {
  font-size: 0.75rem;
  color: #999;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.system-stat-value {
  font-size: 1.25rem;
  font-weight: 700;
  color: #00d9ff;
  margin-top: 0.5rem;
}

/* Inventory resource grid */
.inventory-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.25rem;
}

.inventory-resource {
  background: rgba(26, 31, 58, 0.6);
  border: 1px solid rgba(0, 217, 255, 0.15);
  border-radius: 0.75rem;
  padding: 1.25rem;
}

.inventory-resource-name {
  font-weight: 600;
  color: #fff;
  margin-bottom: 0.5rem;
}

.inventory-resource-quantity {
  font-size: 0.875rem;
  color: #999;
  margin-bottom: 0.75rem;
}
```

Verify: file created with all component classes.

---

### Step 4 — Build the dashboard index template

**Edit**: `app/views/dashboard/index.html.erb`

**Replace entire file with**:
```erb
<div class="space-y-8">
  <!-- Page Title -->
  <div class="mb-8">
    <h1 class="text-4xl font-bold text-neon-cyan mb-2">Player Command Center</h1>
    <p class="text-gray-400">Real-time operational overview — <%= Time.current.strftime("%H:%M:%S UTC") %></p>
  </div>

  <% # System Status Header %>
  <% system = MockDataHelper.system_status %>
  <div class="system-header">
    <div class="system-stat">
      <div class="system-stat-label">Active System</div>
      <div class="system-stat-value"><%= system[:system_name] %></div>
    </div>
    <div class="system-stat">
      <div class="system-stat-label">Security Level</div>
      <div class="system-stat-value"><%= system[:security_level] %></div>
    </div>
    <div class="system-stat">
      <div class="system-stat-label">EAP (Earth Anchor Price)</div>
      <div class="system-stat-value"><%= number_with_precision(system[:eap], precision: 2) %> ISK</div>
    </div>
    <div class="system-stat">
      <div class="system-stat-label">Player Balance</div>
      <div class="system-stat-value">
        <%= format_quantity(system[:player_isk]) %> ISK<br>
        <span class="text-sm text-neon-gold"><%= format_quantity(system[:player_gcc]) %> GCC</span>
      </div>
    </div>
  </div>

  <!-- Active Sector Units Panel -->
  <div class="glass-card">
    <div class="panel-title">Active Sector Units</div>
    <div class="space-y-3">
      <% MockDataHelper.active_units.each do |unit| %>
        <div class="unit-card">
          <div class="flex justify-between items-start mb-2">
            <div>
              <div class="font-semibold text-neon-cyan"><%= unit[:name] %></div>
              <div class="text-sm text-gray-400"><%= unit[:type] %></div>
            </div>
            <span class="status-badge <%= status_badge_class(unit[:status]) %>">
              <%= unit[:status] %>
            </span>
          </div>
          <div class="grid grid-cols-2 gap-2 text-sm">
            <div>
              <div class="text-gray-500">Location</div>
              <div class="text-gray-300"><%= unit[:location] %></div>
            </div>
            <div>
              <div class="text-gray-500">
                <%= unit[:yield] ? "Yield" : "Cargo" %>
              </div>
              <div class="text-gray-300">
                <%= unit[:yield] || unit[:cargo] %>
              </div>
            </div>
          </div>
        </div>
      <% end %>
    </div>
  </div>

  <!-- Local Bay Inventory Visualizer -->
  <div class="glass-card">
    <div class="panel-title">Local Bay Inventory</div>
    <div class="inventory-grid">
      <% MockDataHelper.bay_inventory.each do |item| %>
        <div class="inventory-resource">
          <div class="inventory-resource-name">
            <%= resource_icon(item[:resource]) %>
          </div>
          <div class="inventory-resource-quantity">
            <%= format_quantity(item[:quantity]) %> / <%= format_quantity(item[:max]) %> <%= item[:unit] %>
          </div>
          <div class="inventory-bar">
            <div class="inventory-fill" style="width: <%= inventory_fill_percent(item[:quantity], item[:max]) %>%">
              <span><%= inventory_fill_percent(item[:quantity], item[:max]) %>%</span>
            </div>
          </div>
        </div>
      <% end %>
    </div>
  </div>

  <!-- Market Quick-Panel -->
  <div class="glass-card">
    <div class="panel-title">Market Quick-Panel</div>
    <div class="overflow-x-auto">
      <table class="market-table">
        <thead>
          <tr>
            <th>Commodity</th>
            <th>Buy Spread</th>
            <th>Sell Spread</th>
            <th>Volume</th>
          </tr>
        </thead>
        <tbody>
          <% MockDataHelper.market_quick_panel.each do |commodity| %>
            <tr>
              <td><%= commodity[:commodity] %></td>
              <td class="text-neon-purple"><%= commodity[:buy_spread] %></td>
              <td class="text-neon-gold"><%= commodity[:sell_spread] %></td>
              <td class="text-gray-400"><%= commodity[:volume] %></td>
            </tr>
          <% end %>
        </tbody>
      </table>
    </div>
  </div>

</div>
```

Verify: template file updated with all four panels and proper data binding.

---

### Step 5 — Verify dashboard renders correctly

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# If server not running, start it
bin/rails s

# In separate terminal:
curl -s http://localhost:3000/dashboard | grep -i "command center\|active sector\|inventory\|market" | head -10
```

Verify output contains all four panel titles. Optionally visit http://localhost:3000/dashboard in browser and take a screenshot.

---

### Step 6 — Commit changes to main app repository

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

git add .
git commit -m "feat: implement dashboard feature with system status, units, inventory, and market panels"
git log --oneline -1
```

Paste the commit hash in your completion report.

---

## Acceptance Criteria
- [x] Dashboard loads without database errors
- [x] System Status Header displays all four metrics
- [x] Active Sector Units panel shows all three mock units with correct status badges
- [x] Status badges are consistently colored (Harvesting=green, In Transit=blue, etc.)
- [x] Bay Inventory grid displays all four resources with correct progress bars
- [x] Market Quick-Panel shows all three commodities in table format
- [x] All data comes from MockDataHelper; no database queries
- [x] Layout is responsive (desktop/tablet/mobile)
- [x] Styling consistent with dark sci-fi Tailwind theme
- [x] No console errors; no database errors in Rails logs

---

## Commit Instructions

> ⚠️ **PRE-COMMIT CHECK**: Confirm:
> - [ ] Reviewer has issued GO disposition
> - [ ] Human has explicitly authorized commit/push
>
> If either condition is unmet, DO NOT proceed.

Run git commands on **host only**:
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

git add .
git commit -m "feat: implement dashboard feature with system status, units, inventory, and market panels"
git push

# Then move task file to completed/
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md \
       projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md
git add projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md
git commit -m "chore: move dashboard task to completed/"
git push
```

---

## Dependencies
**Blocked by**: Task 1 (Rails 8 Project Setup)
**Blocks**: None (tasks 3 & 4 are independent)
**Related tasks**: Task 3 (Wormhole Network), Task 4 (Admin Panel)

---

## Progress / Resume

*Updated by session whenever it stops before task completion.*

- **Done so far**: [to be filled by agent]
- **Next step**: [to be filled by agent]
- **Last verified fact (with command)**: [to be filled by agent]
- **Owning session / window label**: [to be filled by agent]

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Evidence basis**: [direct verification / reported by agent]

### What was changed
- Created `app/helpers/dashboard_helper.rb` with view logic
- Created `app/assets/stylesheets/dashboard.css` with component library
- Updated `app/views/dashboard/index.html.erb` with four integrated panels

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary

HANDOFF SUMMARY: Dashboard feature complete with all four panels (System Status, Active Units, Inventory, Market) | All data from MockDataHelper | Next: Dispatch Task 3 (Wormhole Network) or Task 4 (Admin Panel)
