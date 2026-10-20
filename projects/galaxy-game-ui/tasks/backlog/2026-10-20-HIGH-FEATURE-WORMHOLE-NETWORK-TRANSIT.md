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
- [x] Synthesis report template ready
- [x] No placeholder text in Implementation Steps
- [x] All file paths verified to exist
- [x] Architecture Gotchas specific and clear
- [x] Acceptance Criteria measurable
- [x] Dependencies clear (depends on Task 1)

**Task is READY FOR DISPATCH after Task 1 is completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md \
         projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md"
    Only ONE result should exist at the `active/` path.

READ FIRST (after Step 0): Task file contains all prerequisites and implementation details.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/
  Chat is for questions only — never paste synthesis into chat.

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: Wormhole Network & Transit Hub Feature
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-10-20
**Depends on**: Task 1 (Rails 8 Project Setup)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Task 1 Completion**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md` (must be done first)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md` (review Feature 2: Wormhole Network)
3. **Mock Data Module**: `app/helpers/mock_data.rb` in your Rails app (created in Task 1)
4. **This Task File**: Everything below

---

## Context

This task builds the **Supply Route Planner** feature — a network view of inter-settlement connectivity and logistics. It includes two main sections:
1. **Settlement Network Map**: High-density list of connected settlements, wormhole types (Natural/Artificial), stability status, decay timers, and Exotic Matter (EM) potential
2. **Route Planner & Shipment Tracking**: Active supply routes between settlements, transit times, cargo capacity limits, and in-flight shipments

This feature enables players to understand settlement-to-settlement connectivity and plan resource logistics via wormholes. All data is mocked; later it will integrate with existing wormhole generation code and planet data.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: Network visualization can be complex — keep it simple**
- ❌ Wrong: Implement a full SVG/Canvas network graph with animated links
- ✅ Right: Use a high-density HTML table for Wormhole Node Map + ASCII-art route display
- Why: Prototype phase; visual complexity should not block feature development. Upgrade to SVG/D3 after validation.

⚠️ **GOTCHA 2: Route planner must show unit mass restrictions**
- ❌ Wrong: Show only route names and hop count
- ✅ Right: Include unit mass limit per route; highlight if player's heaviest unit exceeds limit
- Why: This is a core gameplay mechanic in Galaxy Game.

⚠️ **GOTCHA 3: Wormhole stations vs. natural wormholes are different**
- ❌ Wrong: Use generic "Wormhole" terminology for both types
- ✅ Right: Clearly distinguish "Natural" (decaying) vs. "Artificial" (persistent) in UI labels
- Why: Affects player strategy and risk assessment.

⚠️ **GOTCHA 4: Exotic Matter (EM) is a Galaxy Game concept, NOT EVE ore**
- ❌ Wrong: Reference EVE ores (Veldspar, Kernite, etc.)
- ✅ Right: Use Galaxy Game resources only (Compressed Blue Ice, Oxygen Isotopes, EM, etc.)
- Why: Terminology consistency with game domain.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start)

Create and save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/2026-10-20-WORMHOLE-SYNTHESIS.md`:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Wormhole Network & Transit Hub Feature
**Status**: backlog → active
**Date**: 2026-10-20

### What I'm About to Do
Build the `/wormholes` view with two sections:
1. **Settlement Network Map** — table showing connected settlements, wormhole types (Natural/Artificial), mass stability, decay timers, EM potential
2. **Route Planner & Shipment Tracking** — active supply routes with hop counts, estimated transit times, cargo capacity limits, in-flight shipments

All data sourced from MockDataHelper. Layout uses dark theme with clear settlement/route hierarchy.

### Files I'll Modify
| File | Purpose | Status |
|---|---|---|
| `app/views/wormholes/index.html.erb` | Main wormholes template with two sections | pending |
| `app/helpers/wormholes_helper.rb` | View logic (node type icons, route formatting) | pending |
| `app/assets/stylesheets/wormholes.css` | Styling (node cards, route cards, tables) | pending |

### Prerequisites Completed
- ✅ Task 1 (Rails 8 setup) completed and committed
- ✅ bin/rails s starts server without errors
- ✅ MockDataHelper.wormhole_network() and MockDataHelper.transit_routes() exist
- ✅ Read wormhole feature spec from project README
- ✅ Understand: Natural vs. Artificial wormhole distinction
- ✅ Understand: Galaxy Game terminology only (no EVE ores, no EVE routes)

### Expected Outcomes
1. `GET /wormholes` returns full HTML with Node Map and Route Planner
2. Settlement Network Map displays 3 mock settlements (ANTOLLARE III, Enedore, Jita Hub) with type (Natural/Artificial), stability, decay timer, EM data
3. Settlement type icons clearly distinguish Natural (⚡) vs. Artificial (🔧) wormholes
4. Route Planner displays 2 mock routes with hop count, estimated transit time, cargo capacity limit
5. Shipment Tracking shows 1-2 in-flight shipments (origin, destination, cargo, ETA)
6. All sections use consistent glassmorphic styling with Tailwind dark theme
7. Routes clearly show cargo capacity limits and constraints
8. Layout responsive (mobile/tablet/desktop)
8. No console errors; all data from MockDataHelper

### Critical Gotchas I Will Avoid
- ❌ Implementing full SVG network graph — instead ✅ using HTML table + ASCII-art route display
- ❌ Showing only route names — instead ✅ including unit mass restrictions per route
- ❌ Generic "Wormhole" labels — instead ✅ clearly labeling Natural vs. Artificial
- ❌ EVE ore terminology — instead ✅ using Galaxy Game resources only

### Success Criteria
- Wormhole view loads and both sections visible
- Node Map shows all systems with correct wormhole type, stability, decay, EM data
- Route Planner shows all routes with unit mass restrictions highlighted
- Layout responsive on all screen sizes
- No database errors in Rails logs
- All data from MockDataHelper

---

**SYNTHESIS COMPLETE.** Ready to build wormhole network.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The Wormhole Network feature enables players to explore system connections, assess route safety (wormhole decay, mass stability), and plan transit with unit mass constraints. It must present network topology clearly without requiring a full interactive SVG graph (that's a future enhancement).

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose |
|---|---|
| `app/views/wormholes/index.html.erb` | Main wormhole template with Node Map and Route Planner |
| `app/helpers/wormholes_helper.rb` | View helper logic (node type icons, route formatting) |
| `app/assets/stylesheets/wormholes.css` | Component library (node cards, route cards, tables) |

### Reference Files — read only
| File | Why You Need It |
|---|---|
| `app/helpers/mock_data.rb` | Data source (wormhole_network, transit_routes) |
| Project README Wormholes section | Feature requirements and terminology |
| `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css` | Table styling, panel effects, and badge patterns. Study table layout and row hover effects. |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP — NO EXCEPTIONS)

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/galaxy-game-ui/tasks/backlog/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md \
       projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md

# Open file and change: status: backlog → status: active

# Verify only one copy exists
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks \
     -name "2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md"
```

**Paste the find output in chat before proceeding.**

---

### Step 1 — Create Status Synthesis Report

Create and save synthesis report using the template above.

Post: *"Synthesis complete. Ready to build wormhole network feature."*

Wait for approval before proceeding to Step 2.

---

### Step 2 — Create wormholes helper with view logic

**Create**: `app/helpers/wormholes_helper.rb`

```ruby
module WormholesHelper
  # Return icon/emoji for wormhole type
  def wormhole_type_icon(wormhole_type)
    case wormhole_type
    when "Natural"
      "⚡"
    when "Artificial"
      "🔧"
    else
      "?"
    end
  end

  # Color class for stability percentage
  def stability_color_class(stability_str)
    stability = stability_str.to_i
    case stability
    when 90..100
      "text-green-400"
    when 70..89
      "text-yellow-400"
    when 50..69
      "text-orange-400"
    else
      "text-red-400"
    end
  end

  # Color class for EM potential
  def em_potential_class(potential)
    case potential
    when "High"
      "text-neon-purple"
    when "Medium"
      "text-neon-cyan"
    when "Low"
      "text-yellow-400"
    else
      "text-gray-400"
    end
  end

  # Format route with arrow symbols
  def format_route_path(route_str)
    route_str.gsub("→", "→").html_safe
  end

  # Return badge class for mass restriction
  def mass_restriction_badge_class(restriction)
    "bg-neon-red/20 border-neon-red/50 text-neon-red px-2 py-1 text-xs rounded border"
  end
end
```

Verify: file created at `app/helpers/wormholes_helper.rb`.

---

### Step 3 — Create wormholes stylesheet

**Create**: `app/assets/stylesheets/wormholes.css`

```css
/* Node map table styling */
.node-map-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 2rem;
}

.node-map-table thead {
  border-bottom: 2px solid rgba(0, 217, 255, 0.3);
}

.node-map-table th {
  color: #00d9ff;
  text-align: left;
  padding: 1rem;
  font-weight: 600;
  font-size: 0.875rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.node-map-table td {
  padding: 1rem;
  border-bottom: 1px solid rgba(0, 217, 255, 0.1);
  font-size: 0.875rem;
}

.node-map-table tbody tr:hover {
  background: rgba(0, 217, 255, 0.05);
}

/* Node type badge */
.node-type-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.375rem 0.75rem;
  border-radius: 0.25rem;
  font-size: 0.875rem;
  font-weight: 500;
}

.node-type-natural {
  background: rgba(255, 165, 0, 0.2);
  border: 1px solid rgba(255, 165, 0, 0.4);
  color: #ffa500;
}

.node-type-artificial {
  background: rgba(0, 217, 255, 0.2);
  border: 1px solid rgba(0, 217, 255, 0.4);
  color: #00d9ff;
}

/* Route card in Transit Planner */
.route-card {
  background: rgba(26, 31, 58, 0.6);
  border: 1px solid rgba(0, 217, 255, 0.15);
  border-radius: 0.75rem;
  padding: 1.5rem;
  margin-bottom: 1.25rem;
  transition: all 0.3s ease;
}

.route-card:hover {
  background: rgba(26, 31, 58, 0.8);
  border-color: rgba(0, 217, 255, 0.3);
  box-shadow: 0 4px 12px rgba(0, 217, 255, 0.1);
}

.route-path {
  font-family: 'Courier New', monospace;
  font-size: 1rem;
  color: #00d9ff;
  font-weight: 600;
  margin-bottom: 1rem;
}

.route-details {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  margin-bottom: 1rem;
}

.route-detail-item {
  background: rgba(10, 14, 39, 0.4);
  border-left: 2px solid rgba(0, 217, 255, 0.3);
  padding: 0.75rem;
}

.route-detail-label {
  font-size: 0.75rem;
  color: #999;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.25rem;
}

.route-detail-value {
  font-size: 1rem;
  font-weight: 600;
  color: #fff;
}

.route-restriction {
  padding: 0.75rem;
  background: rgba(255, 0, 110, 0.15);
  border-left: 2px solid #ff006e;
  border-radius: 0.25rem;
}

.route-restriction-label {
  font-size: 0.75rem;
  color: #ff006e;
  text-transform: uppercase;
  font-weight: 600;
}

/* Section heading */
.section-heading {
  font-size: 1.5rem;
  font-weight: 700;
  color: #00d9ff;
  margin-bottom: 1.5rem;
  text-shadow: 0 0 10px rgba(0, 217, 255, 0.2);
  padding-bottom: 0.75rem;
  border-bottom: 2px solid rgba(0, 217, 255, 0.3);
}

/* Stability bar indicator */
.stability-indicator {
  display: inline-block;
  height: 0.5rem;
  border-radius: 0.25rem;
  min-width: 80px;
  background: rgba(0, 217, 255, 0.1);
  border: 1px solid rgba(0, 217, 255, 0.2);
  overflow: hidden;
}

.stability-fill {
  height: 100%;
  background: linear-gradient(90deg, #00d9ff, #b366ff);
  transition: width 0.3s ease;
}
```

Verify: file created with all styling components.

---

### Step 4 — Build the wormholes index template

**Edit**: `app/views/wormholes/index.html.erb`

**Replace entire file with**:
```erb
<div class="space-y-10">
  <!-- Page Title -->
  <div class="mb-8">
    <h1 class="text-4xl font-bold text-neon-purple mb-2">Wormhole Network & Transit Hub</h1>
    <p class="text-gray-400">System connections, routing, and transit planning</p>
  </div>

  <!-- Wormhole Node Map Section -->
  <div class="glass-card">
    <h2 class="section-heading">Wormhole Node Map</h2>
    <p class="text-gray-400 mb-4">Connected systems and wormhole stability information</p>
    
    <div class="overflow-x-auto">
      <table class="node-map-table">
        <thead>
          <tr>
            <th>System</th>
            <th>Wormhole Type</th>
            <th>Mass Stability</th>
            <th>Decay Timer</th>
            <th>EM Potential</th>
            <th>Route To Hub</th>
          </tr>
        </thead>
        <tbody>
          <% MockDataHelper.wormhole_network.each do |node| %>
            <tr>
              <td class="font-semibold text-neon-cyan"><%= node[:system] %></td>
              <td>
                <span class="node-type-badge <%= node[:type] == 'Natural' ? 'node-type-natural' : 'node-type-artificial' %>">
                  <%= wormhole_type_icon(node[:type]) %>
                  <%= node[:type] %>
                </span>
              </td>
              <td>
                <div class="flex items-center gap-2">
                  <div class="stability-indicator">
                    <div class="stability-fill <%= stability_color_class(node[:mass_stability]) %>" 
                         style="width: <%= node[:mass_stability].to_i %>%"></div>
                  </div>
                  <span class="<%= stability_color_class(node[:mass_stability]) %>">
                    <%= node[:mass_stability] %>
                  </span>
                </div>
              </td>
              <td class="text-gray-300">
                <% if node[:decay_timer] == "N/A" %>
                  <span class="text-gray-500">—</span>
                <% else %>
                  <%= node[:decay_timer] %>
                <% end %>
              </td>
              <td class="<%= em_potential_class(node[:em_potential]) %>">
                <%= node[:em_potential] %>
              </td>
              <td class="text-neon-gold"><%= node[:hops_to] %></td>
            </tr>
          <% end %>
        </tbody>
      </table>
    </div>
  </div>

  <!-- Route & Transit Planner Section -->
  <div class="glass-card">
    <h2 class="section-heading">Route & Transit Planner</h2>
    <p class="text-gray-400 mb-4">System jump routes and unit transit constraints</p>
    
    <div class="space-y-4">
      <% MockDataHelper.transit_routes.each do |route| %>
        <div class="route-card">
          <div class="route-path">
            <%= format_route_path(route[:route]) %>
          </div>
          
          <div class="route-details">
            <div class="route-detail-item">
              <div class="route-detail-label">Hops</div>
              <div class="route-detail-value"><%= route[:hops] %></div>
            </div>
            <div class="route-detail-item">
              <div class="route-detail-label">Estimated Transit Time</div>
              <div class="route-detail-value"><%= route[:estimated_time] %></div>
            </div>
          </div>

          <div class="route-restriction">
            <div class="route-restriction-label">⚠️ Unit Mass Restriction</div>
            <div class="mt-2 text-sm text-gray-300">
              <%= route[:unit_mass_restriction] %>
            </div>
          </div>
        </div>
      <% end %>
    </div>
  </div>

</div>
```

Verify: template file updated with Node Map table and Route Planner cards.

---

### Step 5 — Verify wormholes feature renders correctly

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

# If server not running, start it
bin/rails s

# In separate terminal:
curl -s http://localhost:3000/wormholes | grep -i "wormhole\|node map\|route planner\|mass restriction" | head -10
```

Verify output contains section titles and data. Optionally visit http://localhost:3000/wormholes in browser.

---

### Step 6 — Commit changes to main app repository

```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui

git add .
git commit -m "feat: implement wormhole network feature with node map and transit planner"
git log --oneline -1
```

Paste the commit hash in your completion report.

---

## Acceptance Criteria
- [x] Wormholes view loads without database errors
- [x] Node Map displays all systems with type, stability, decay, EM data
- [x] Wormhole type clearly distinguishes Natural (⚡) vs. Artificial (🔧)
- [x] Stability indicators show correct percentages and color coding
- [x] Route Planner displays all routes with hop count, estimated time
- [x] Unit mass restrictions clearly displayed per route
- [x] All data comes from MockDataHelper; no database queries
- [x] Layout responsive (desktop/tablet/mobile)
- [x] Styling consistent with dark sci-fi theme
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
git commit -m "feat: implement wormhole network feature with node map and transit planner"
git push

# Then move task file to completed/
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy-game-ui/tasks/active/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md \
       projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md
git add projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md
git commit -m "chore: move wormhole network task to completed/"
git push
```

---

## Dependencies
**Blocked by**: Task 1 (Rails 8 Project Setup)
**Blocks**: None (tasks 2 & 4 are independent)
**Related tasks**: Task 2 (Dashboard), Task 4 (Admin Panel)

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
- Created `app/helpers/wormholes_helper.rb` with view logic
- Created `app/assets/stylesheets/wormholes.css` with component library
- Updated `app/views/wormholes/index.html.erb` with Node Map and Route Planner

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary

HANDOFF SUMMARY: Wormhole Network feature complete with Node Map table and Route Planner cards | Natural/Artificial wormhole distinction clear | Unit mass restrictions highlighted | Next: Dispatch Task 4 (Admin Panel)
