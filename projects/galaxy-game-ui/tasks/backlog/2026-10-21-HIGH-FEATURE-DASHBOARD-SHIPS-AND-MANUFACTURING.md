---
status: backlog
resume: false
priority: HIGH
type: feature
system_domain: CONTROLLERS | UI
mvp_alignment: GALAXY_GAME_UI
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/[SUBFOLDER]/2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/[SUBFOLDER]/2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md \
         projects/galaxy-game-ui/tasks/active/2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Dashboard Ships & Active Units + Manufacturing Queue Panels
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-10-21
**Last Updated**: 2026-10-21

## Context
This task implements two panel pairs for the dashboard: **Ships & Active Units** (Panel 2, right column of top row) and **Pending Operations** (Panel 3, bottom-left with tab toggle between Manufacturing Queue and Research Queue). These panels balance operational fleet status with production timeline visibility. All data comes from `MockDataHelper` module methods; all panels render in `app/views/dashboard/index.html.erb`.

**Approved Design**: `/projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md` (Sections A, C, D)
**Dispatch Spec**: `/projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md` (Task 3)

## Dependencies
- **Blocks**: Task 4 (Orders & Market) — all controller instance variables must be set together in `DashboardController#index`
- **Depends On**: Task 2 completed first to establish the base layout grid

## Architecture Gotchas

⚠️ **GOTCHA 1: Tab toggle requires Stimulus JS, not vanilla JS**
- ❌ Wrong: Inline `<script>` tags or jQuery for tab switching
- ✅ Right: Use a Stimulus controller (`dashboard-tabs`) with `data-action="click->dashboard-tabs#toggle"`
- Why: The approved design explicitly requires Stimulus JS. Create the controller at `app/javascript/controllers/dashboard_tabs_controller.js` and import it in `application.js`.

⚠️ **GOTCHA 2: @ships variable name conflict**
- The current DashboardController uses `@player_units = MockDataHelper.active_units`.
- ✅ Right: Rename to `@ships = MockDataHelper.player_ships` — this is the correct variable per the approved design (Deliverable C: Data Binding Map). Also keep `@player_units` as an alias if other views reference it.
- Why: The data binding map maps Panel 2 → `@ships` from `MockDataHelper.player_ships`. Update the controller to match the spec exactly.

⚠️ **GOTCHA 3: Grid layout must accommodate all 5 panels**
- Top row: Header (full-width) → Bay Inventory | Ships
- Bottom row: Manufacturing + Research tabs | Open Orders
- Market footer: full-width at bottom
- ✅ Right: Use `lg:col-span-2` for header and market footer; 2-column grid for inner panels.
- Why: Deviating from the approved layout (Section B of design output) breaks the visual hierarchy.

## Implementation Steps

### Step 1 — Update Controller instance variables (app/controllers/dashboard_controller.rb)
Ensure these are set in `index`:
```ruby
@ships     = MockDataHelper.player_ships       # Panel 2: Ships & Active Units
@mfg       = MockDataHelper.manufacturing_queue # Panel 3: Manufacturing Queue
@research  = MockDataHelper.research_queue      # Panel 3: Research Queue (tab)
@orders    = MockDataHelper.npc_buy_orders      # Task 4: Open Orders (Buy Orders tab)
@missions  = MockDataHelper.active_missions     # Task 4: Missions tab
```

### Step 2 — Create Ships & Active Units Panel (right column of top row)
Add to `app/views/dashboard/index.html.erb` as the right panel in the 2-column grid:
```erb
<div class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4 hover:border-cyan-500/50 transition-colors">
  <h3 class="text-lg font-semibold text-cyan-400 mb-3">Ships & Active Units</h3>
  <% @ships.each do |ship| %>
    <div class="mb-3 pb-3 border-b border-gray-800 last:border-0">
      <div class="flex justify-between items-center mb-1">
        <span class="text-[#e0e0e0] font-medium"><%= ship[:name] %></span>
        <span class="<% case ship[:status] %>
          <% when 'Docked' %> bg-green-900/50 text-green-400 px-2 py-0.5 rounded text-xs <% when 'In Transit' %> bg-cyan-900/50 text-cyan-400 px-2 py-0.5 rounded text-xs <% else %> bg-gray-800/50 text-gray-400 px-2 py-0.5 rounded text-xs <% end %>"><%= ship[:status] %></span>
      </div>
      <p class="text-sm text-gray-500"><%= ship[:type] %></p>
      <% if ship[:location] %>
        <p class="text-xs text-gray-600">Location: <%= ship[:location] %></p>
      <% end %>
      <% if ship[:destination] && ship[:eta] %>
        <p class="text-xs text-gray-600">Destination: <%= ship[:destination] %> (ETA: <%= ship[:eta] %>)</p>
      <% end %>
    </div>
  <% end %>
</div>
```

### Step 3 — Create Pending Operations Panel with Tab Toggle (bottom-left)
Add Stimulus controller file `app/javascript/controllers/dashboard_tabs_controller.js`:
```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["panel"]

  connect() {
    // Default to Manufacturing tab on load
    this.toggle(0)
  }

  toggle(index) {
    this.panelTargets.forEach((target, i) => {
      target.style.display = i === index ? "block" : "none"
      target.dataset.active = i === index ? "true" : "false"
    })
  }
}
```

Import in `app/javascript/application.js`:
```js
import "./controllers/dashboard_tabs_controller"
```

Add the tabbed panel to `index.html.erb`:
```erb
<div data-controller="dashboard-tabs" class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4 hover:border-cyan-500/50 transition-colors">
  <h3 class="text-lg font-semibold text-cyan-400 mb-3">Pending Operations</h3>

  <!-- Tab buttons -->
  <div class="flex gap-2 mb-3 border-b border-gray-700">
    <button data-action="dashboard-tabs#toggle" data-index="0" class="tab-btn px-3 py-1 text-sm rounded-t border-cyan-400 bg-[rgba(26,26,46,0.9)] text-cyan-400">Manufacturing</button>
    <button data-action="dashboard-tabs#toggle" data-index="1" class="tab-btn px-3 py-1 text-sm rounded-t border-transparent text-gray-500 hover:text-cyan-400">Research</button>
  </div>

  <!-- Manufacturing Queue -->
  <div data-dashboard-tabs-target="panel" data-active="true">
    <% @mfg.each do |job| %>
      <div class="mb-3 pb-3 border-b border-gray-800 last:border-0">
        <div class="flex justify-between items-center mb-1">
          <span class="text-[#e0e0e0] font-medium"><%= job[:job] %></span>
          <span class="<% case job[:status] %>
            <% when 'In Progress' %> bg-cyan-900/50 text-cyan-400 px-2 py-0.5 rounded text-xs <% else %> bg-yellow-900/50 text-yellow-400 px-2 py-0.5 rounded text-xs <% end %>"><%= job[:status] %></span>
        </div>
        <p class="text-sm text-gray-500">Facility: <%= job[:facility] %></p>
        <div class="w-full bg-gray-800 rounded-full h-2 overflow-hidden mt-1">
          <div class="bg-gradient-to-r from-cyan-600 to-purple-600 h-2 rounded-full" style="width: <%= job[:progress] %>%"></div>
        </div>
        <p class="text-xs text-gray-600 mt-1">ETA: <%= job[:eta] %> | Progress: <%= job[:progress] %>%</p>
      </div>
    <% end %>
  </div>

  <!-- Research Queue -->
  <div data-dashboard-tabs-target="panel" style="display:none">
    <% @research.each do |r| %>
      <div class="mb-3 pb-3 border-b border-gray-800 last:border-0">
        <span class="text-[#e0e0e0] font-medium"><%= r[:blueprint] %> (Lv.<%= r[:level] %>)</span>
        <div class="w-full bg-gray-800 rounded-full h-2 overflow-hidden mt-1">
          <div class="bg-gradient-to-r from-cyan-600 to-purple-600 h-2 rounded-full" style="width: <%= r[:progress] %>%"></div>
        </div>
        <p class="text-xs text-gray-600 mt-1">ETA: <%= r[:eta] %> | Progress: <%= r[:progress] %>%</p>
      </div>
    <% end %>
  </div>
</div>
```

### Step 4 — Verify Styling Conventions (from approved design)
| Element | CSS Class / Pattern | ✅ Verified |
|---------|-------------------|-------------|
| Panel bg | `rgba(26,26,46,0.85)` + `border-cyan-900/30` | ✅ |
| Status Docked | `bg-green-900/50 text-green-400` | ✅ |
| Status In Transit | `bg-cyan-900/50 text-cyan-400` | ✅ |
| Status Other (Queued/Idle) | `bg-gray-800/50 text-gray-400` | ✅ |
| Progress gradient | `from-cyan-600 to-purple-600` | ✅ |
| Tab active border | `border-cyan-400 bg-[rgba(26,26,46,0.9)]` text-cyan-400 | ✅ |

## Acceptance Criteria
- [ ] Ships panel displays all 3 ships: Heavy Transport Tug Alpha, Logistics Hauler Beta, Scout Vessel Gamma
- [ ] Docked ships show green status badge (`bg-green-900/50 text-green-400`)
- [ ] In Transit ship shows cyan status badge (`bg-cyan-900/50 text-cyan-400`) with destination + ETA
- [ ] Manufacturing tab shows 2 jobs with correct progress percentages (72%, 0%) and ETAs
- [ ] Research tab is hidden by default, visible when toggled
- [ ] Stimulus tab toggle works correctly between Manufacturing ↔ Research
- [ ] Progress bars render with correct fill widths (manufacturing: 72% / 0%; research: 65% / 40%)
- [ ] Status badges color-code correctly per approved mapping
- [ ] Mobile responsive: panels stack vertically, tabs are full-width on small screens

## Progress / Resume
- **Done so far**: Task created from approved design + dispatch spec
- **Next step**: Implement Ships panel (right column) + Pending Operations panel with tab toggle
- **Blocked by**: Task 2 must be completed first (base grid established)
