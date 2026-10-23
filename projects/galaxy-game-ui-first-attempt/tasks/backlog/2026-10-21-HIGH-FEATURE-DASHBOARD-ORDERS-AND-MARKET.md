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
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/[SUBFOLDER]/2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/[SUBFOLDER]/2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md \
         projects/galaxy-game-ui/tasks/active/2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md"
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

# TASK: Dashboard Open Orders & Market Quick-Panel Footer
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-10-21
**Last Updated**: 2026-10-21

## Context
This task implements the final two dashboard panels: **Open Orders** (Panel 4, bottom row with Buy Orders + Missions tabs) and **Market Quick-Panel** (Panel 5, full-width footer). These panels surface economy opportunities — NPC purchase demand, player missions with bounties, and real-time market reference data. All data comes from `MockDataHelper` module methods; all panels render in `app/views/dashboard/index.html.erb`.

**Approved Design**: `/projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md` (Sections A, C, D)
**Dispatch Spec**: `/projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md` (Task 4)

## Dependencies
- **Blocks**: None — this is the final task in the sequence
- **Depends On**: Tasks 2 + 3 completed first (base grid layout, controller variables, Stimulus tabs controller must all exist)

## Architecture Gotchas

⚠️ **GOTCHA 1: Market Quick-Panel is a footer, NOT a panel**
- ❌ Wrong: Putting the market in the 2-column grid as another panel row
- ✅ Right: Market Quick-Panel spans full-width at the BOTTOM of the dashboard — it's a `<footer>` element outside the grid
- Why: Design Deliverable B explicitly shows "PANEL 5 (Footer): Market Quick-Panel" spanning the bottom row

⚠️ **GOTCHA 2: Stimulus tabs controller from Task 3 is shared**
- The `dashboard-tabs` Stimulus controller created in Task 3 should be reused — do NOT create a new one.
- ✅ Right: Reference the same `data-controller="dashboard-tabs"` and `data-action="click->dashboard-tabs#toggle"` patterns
- Why: DRY principle + consistent tab behavior across the dashboard

⚠️ **GOTCHA 3: Mission deadlines need urgency styling**
- Missions with "Available" status should use yellow accent (gold) — they're bounties waiting to be claimed.
- ✅ Right: `bg-yellow-900/50 text-yellow-400` for Available; `bg-cyan-900/50 text-cyan-400` for In Progress
- Why: Approved design mapping — Available missions are like queued buy orders (opportunity)

## Implementation Steps

### Step 1 — Ensure Controller Has All Variables (app/controllers/dashboard_controller.rb)
All variables must be present in `index`:
```ruby
@orders   = MockDataHelper.npc_buy_orders      # Panel 4: Buy Orders tab
@missions = MockDataHelper.active_missions     # Panel 4: Missions tab
@market   = MockDataHelper.market_quick_panel  # Panel 5: Market Quick-Panel (footer)
```

### Step 2 — Create Open Orders Panel with Tab Toggle (bottom row)
Add to `app/views/dashboard/index.html.erb` below the Manufacturing/Research panel:
```erb
<div data-controller="dashboard-tabs" class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4 hover:border-cyan-500/50 transition-colors">
  <h3 class="text-lg font-semibold text-cyan-400 mb-3">Open Orders</h3>

  <!-- Tab buttons -->
  <div class="flex gap-2 mb-3 border-b border-gray-700">
    <button data-action="dashboard-tabs#toggle" data-index="0" class="tab-btn px-3 py-1 text-sm rounded-t border-cyan-400 bg-[rgba(26,26,46,0.9)] text-cyan-400">Buy Orders</button>
    <button data-action="dashboard-tabs#toggle" data-index="1" class="tab-btn px-3 py-1 text-sm rounded-t border-transparent text-gray-500 hover:text-cyan-400">Missions</button>
  </div>

  <!-- Buy Orders Tab -->
  <div data-dashboard-tabs-target="panel" data-active="true">
    <% @orders.each do |order| %>
      <div class="mb-3 pb-3 border-b border-gray-800 last:border-0">
        <div class="flex justify-between items-center mb-1">
          <span class="text-[#e0e0e0] font-medium"><%= order[:corporation] %></span>
          <span class="bg-cyan-900/50 text-cyan-400 px-2 py-0.5 rounded text-xs">Buy Order</span>
        </div>
        <p class="text-sm text-gray-300"><%= order[:needs] %>: <%= number_with_delimiter(order[:quantity]) %> units @ <%= number_with_delimiter(order[:price_per_unit]) %> GCC = <span class="text-gold-accent font-semibold"><%= number_with_delimiter(order[:total]) %> GCC</span> total</p>
      </div>
    <% end %>
  </div>

  <!-- Missions Tab -->
  <div data-dashboard-tabs-target="panel" style="display:none">
    <% @missions.each do |mission| %>
      <div class="mb-3 pb-3 border-b border-gray-800 last:border-0">
        <div class="flex justify-between items-center mb-1">
          <span class="text-[#e0e0e0] font-medium"><%= mission[:mission] %></span>
          <span class="<% case mission[:status] %>
            <% when 'Available' %> bg-yellow-900/50 text-yellow-400 px-2 py-0.5 rounded text-xs <% when 'In Progress' %> bg-cyan-900/50 text-cyan-400 px-2 py-0.5 rounded text-xs <% else %> bg-gray-800/50 text-gray-400 px-2 py-0.5 rounded text-xs <% end %>"><%= mission[:status] %></span>
        </div>
        <p class="text-sm text-gray-300">Offer: <%= mission[:offer] %></p>
        <p class="text-sm text-gray-300"><span class="text-[#fbbf24] font-semibold">Reward:</span> <%= number_with_delimiter(mission[:reward]) %> GCC</p>
        <p class="text-xs text-gray-600">Deadline: <%= mission[:deadline] %></p>
      </div>
    <% end %>
  </div>
</div>
```

### Step 3 — Create Market Quick-Panel Footer (full-width at bottom)
Add after the closing `</div>` of the grid, as a footer:
```erb
<footer class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4 mt-4">
  <h3 class="text-md font-semibold text-cyan-400 mb-2" style="padding: px-4 py-3">Market Quick-Panel</h3>
  <div class="overflow-x-auto">
    <table class="w-full text-sm">
      <thead>
        <tr class="border-b border-gray-700 text-cyan-400">
          <th class="text-left py-2 px-3 font-semibold">Commodity</th>
          <th class="text-right py-2 px-3 font-semibold">Buy Price</th>
          <th class="text-right py-2 px-3 font-semibold">Sell Price</th>
          <th class="text-center py-2 px-3 font-semibold">Trend</th>
        </tr>
      </thead>
      <% @market.each do |commodity| %>
        <tbody class="border-b border-gray-800 last:border-0">
          <tr>
            <td class="py-2 px-3 text-[#e0e0e0]"><%= commodity[:name] %></td>
            <td class="py-2 px-3 text-right text-green-400"><%= number_with_delimiter(commodity[:buy_price]) %> GCC</td>
            <td class="py-2 px-3 text-right text-red-400"><%= number_with_delimiter(commodity[:sell_price]) %> GCC</td>
            <td class="py-2 px-3 text-center">
              <% trend_class = case commodity[:trend] %>
                <% when 'up' %> text-green-400 %up-arrow%; when 'down' %> text-red-400 %>↓; else %> text-gray-500 %>→ <% end %>
              <span class="<%= trend_class %>"><%= trend_symbol(commodity[:trend]) %></span>
            </td>
          </tr>
        </tbody>
      <% end %>
    </table>
  </div>
</footer>
```

### Step 4 — Verify Styling Conventions (from approved design)
| Element | CSS Class / Pattern | ✅ Verified |
|---------|-------------------|-------------|
| Panel bg | `rgba(26,26,46,0.85)` + `border-cyan-900/30` | ✅ |
| Buy Order badge | `bg-cyan-900/50 text-cyan-400` | ✅ |
| Available mission | `bg-yellow-900/50 text-yellow-400` | ✅ (gold accent) |
| In Progress mission | `bg-cyan-900/50 text-cyan-400` | ✅ |
| Reward highlight | `text-[#fbbf24]` | ✅ (gold accent per design) |
| Table header | `text-cyan-400` | ✅ |
| Footer padding | `px-4 py-3` | ✅ |
| Trend colors | Green ↑ / Red ↓ / Gray → | ✅ |

## Acceptance Criteria
- [ ] Buy Orders tab displays all 3 NPC orders: LDC, AstroLift, MDC with correct prices
- [ ] Each order shows format "Corporation needs Resource: qty @ price = total GCC"
- [ ] Total values formatted with `number_with_delimiter` (e.g., "600,000")
- [ ] Missions tab shows 2 missions with rewards and deadlines
- [ ] Available missions show yellow status badge; In Progress shows cyan
- [ ] Stimulus tab toggle works correctly between Buy Orders ↔ Missions
- [ ] Market Quick-Panel footer displays all commodities with buy/sell prices
- [ ] Table headers use `text-cyan-400` (cyan accent color)
- [ ] Trend indicators display correctly (up ↑ / down ↓ / neutral →)
- [ ] Mobile responsive: tables scroll horizontally, footer readable on small screens

## Progress / Resume
- **Done so far**: Task created from approved design + dispatch spec
- **Next step**: Implement Open Orders panel (tabbed Buy Orders/Missions) + Market Quick-Panel footer
- **Blocked by**: Tasks 2 + 3 must be completed first (Stimulus controller, grid layout, all controller variables)
