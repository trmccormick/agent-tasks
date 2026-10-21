---
status: active
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
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/[SUBFOLDER]/2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/[SUBFOLDER]/2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md \
         projects/galaxy-game-ui/tasks/active/2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md"
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

# TASK: Dashboard Header & Bay Inventory Panels
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-10-21
**Last Updated**: 2026-10-21

## Context
This task implements the first two panels of the Commander Nova dashboard: the **Location & Character Header** (Panel H) and the **Local Bay Inventory** (Panel 1). These are the identity anchor + critical operational data the player sees immediately on login. Both panels render in `app/views/dashboard/index.html.erb` and use data from `DashboardController#index`.

**Approved Design**: `/projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md` (Sections A, C, D)
**Dispatch Spec**: `/projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md` (Task 2)

## Architecture Gotchas

⚠️ **GOTCHA 1: MockDataHelper is a MODULE, not a model**
- ❌ Wrong: `@system_status = MockDataHelper.first` or ActiveRecord-style queries
- ✅ Right: `@system_status = MockDataHelper.system_status` — calls return frozen hashes/arrays
- Why: `app/services/mock_data_helper.rb` is a Ruby module (`module MockDataHelper`). All methods are class methods (`def self.xxx`) returning static mock data.

⚠️ **GOTCHA 2: DashboardController already has @system_status + @player_inventory**
- The controller at `app/controllers/dashboard_controller.rb` already sets `@system_status`, `@player_inventory`, `@player_units`, `@market_panel`.
- ✅ Right: Rename `@player_inventory` → `@bay_items` in the controller to match the task spec. The view expects `@bay_items = MockDataHelper.bay_inventory`.
- Why: The dispatch spec requires `@bay_items` but the current controller uses `@player_inventory`. Align variable names — update the controller, not the view.

⚠️ **GOTCHA 3: No new routes needed**
- Route `GET /dashboard` (root) already exists in `config/routes.rb`. Do NOT create new routes for panels. All panels render as partials or sections within `index.html.erb`.

## Implementation Steps

### Step 1 — Update Controller (app/controllers/dashboard_controller.rb)
```ruby
class DashboardController < ApplicationController
  skip_before_action :require_authentication

  def index
    @system_status = MockDataHelper.system_status
    @bay_items     = MockDataHelper.bay_inventory       # ADDED: was @player_inventory
    @ships         = MockDataHelper.player_ships        # for later tasks
    @orders        = MockDataHelper.npc_buy_orders       # for later tasks
    @missions      = MockDataHelper.active_missions      # for later tasks
    @market        = MockDataHelper.market_quick_panel   # existing but renamed
  end
end
```

### Step 2 — Create Header Panel in view (app/views/dashboard/index.html.erb)
Add the full-width header banner at the top of `index.html.erb`:
```erb
<div class="bg-gradient-to-r from-purple-900/40 to-cyan-900/30 border-b border-cyan-900/30 px-6 py-4 mb-4">
  <h1 class="text-2xl font-bold text-[#e0e0e0]"><%= @system_status[:player_name] %></h1>
  <h2 class="text-lg font-semibold text-cyan-400">Location: <%= @system_status[:system_name] %></h2>
  <p class="text-sm text-gray-500">GCC <%= number_with_delimiter(@system_status[:player_gcc]) %></p>
  <p class="text-xs text-gray-600"><%= @system_status[:timestamp] %></p>
</div>
```

### Step 3 — Create Bay Inventory Panel (2-column grid)
Below the header, add the inventory panel:
```erb
<div class="grid grid-cols-1 lg:grid-cols-2 gap-4 p-4">
  <div class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4 hover:border-cyan-500/50 transition-colors">
    <h3 class="text-lg font-semibold text-cyan-400 mb-3">Local Bay Inventory</h3>
    <% @bay_items.each do |item| %>
      <div class="mb-3">
        <div class="flex justify-between text-sm mb-1">
          <span class="text-[#e0e0e0]"><%= item[:resource] %></span>
          <span class="text-gray-400"><%= number_with_delimiter(item[:quantity]) %>/<%= number_with_delimiter(item[:max_qty]) %> <%= item[:unit] %></span>
        </div>
        <div class="w-full bg-gray-800 rounded-full h-2 overflow-hidden">
          <div class="bg-gradient-to-r from-cyan-600 to-purple-600 h-2 rounded-full" style="width: <%= [item[:quantity].to_f / item[:max_qty] * 100, 100].min %>%"></div>
        </div>
      </div>
    <% end %>
  </div>
  <!-- Panel 2 placeholder for Task 3 -->
</div>
```

### Step 4 — Verify styling conventions match approved design (Deliverable D)
- Header gradient: `from-purple-900/40 to-cyan-900/30` ✅
- Panel glassmorphic bg: `rgba(26,26,46,0.85)` ✅
- Border cyan: `border-cyan-900/30` / hover `cyan-500/50` ✅
- Progress bar gradient: `from-cyan-600 to-purple-600` ✅
- Typography sizes: `text-2xl`, `text-lg`, `text-sm`, `text-xs` ✅

## Acceptance Criteria
- [ ] GET /dashboard returns HTTP 200
- [ ] Header displays character name ("Commander Nova") from `system_status[:player_name]`
- [ ] Header displays settlement ("Luna Settlement Alpha") from `system_status[:system_name]`
- [ ] Header displays GCC balance (15,000,000) via `number_with_delimiter`
- [ ] Bay Inventory shows all 4 resources: Water Ice, Oxygen (O2), Deuterium, Regolith
- [ ] Each resource shows quantity/max_qty with correct units
- [ ] Progress bars render with fill widths matching quantity/max_qty percentages (85%, 77.5%, 68%, 90%)
- [ ] Glassmorphic panel styling applied (inspect CSS in browser dev tools)
- [ ] Mobile responsive: panels stack to single column on screens < 1024px

## Progress / Resume
- **Done so far**: Task created from approved design + dispatch spec
- **Next step**: Implement header banner + inventory panel per Implementation Steps 1–4
- **Blocked by**: None
