=== QWEN IMPLEMENTATION AGENT — TASK 2–4 REWRITE PHASE ===

**Date**: 2026-10-21  
**Previous Output**: DESIGN SESSION (approved 2026-10-21)  
**Current Role**: Implementation Agent (translate approved design into task files)  
**Duration**: ~2–3 hours  
**Output**: Three task files (Task 2, Task 3, Task 4) — each ≤150 lines, ready for implementation dispatch

---

## INPUT: APPROVED DESIGN SPECIFICATIONS

**Design Status**: ✅ APPROVED by Gemini (2026-10-21)

All 4 deliverables approved:
1. ✅ Dashboard Panel List (6 panels + header)
2. ✅ Layout Diagram (2-column responsive grid)
3. ✅ Data Binding Map (template variables → MockDataHelper methods)
4. ✅ Styling Notes (color palette, Tailwind classes, status badges)

**Your approved design is in**: `/projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md`

---

## YOUR ROLE: REWRITE TASKS 2–4 FROM APPROVED DESIGN

**Non-Negotiable Constraints**:
- ✅ ZERO deviation from approved design
- ✅ Reference ONLY the approved panel list (6 panels)
- ✅ Use ONLY the data binding map provided (exact MockDataHelper methods)
- ✅ Apply ONLY the approved styling conventions (Tailwind classes, colors)
- ✅ Each task file ≤150 lines (one file per panel/feature)
- ✅ Follow TASK_TEMPLATE.md exactly
- ✅ All routes already exist: GET /dashboard (public)

---

## TASK 2: DASHBOARD — LOCATION HEADER & BAY INVENTORY

**Scope**: Implement panels H (header) + 1 (inventory)

**Task File Name**: `2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md`

**Location**: `projects/galaxy-game-ui/tasks/backlog/`

**What to Include**:

### A. Task Description
Implement the Location & Character Header and Local Bay Inventory panels on the dashboard. This is the first pair of panels the player sees at login — identity anchor + critical operational data.

### B. Acceptance Criteria (from approved design)

**Header Panel**:
1. Display character name (from `MockDataHelper.system_status[:player_name]`)
2. Display current settlement (from `MockDataHelper.system_status[:system_name]`)
3. Display GCC balance (from `MockDataHelper.system_status[:player_gcc]`)
4. Display timestamp (from `MockDataHelper.system_status[:timestamp]`)
5. Use glassmorphic styling: `bg-gradient-to-r from-purple-900/40 to-cyan-900/30`
6. Apply header banner padding: `px-6 py-4`

**Bay Inventory Panel**:
1. Iterate through `MockDataHelper.bay_inventory` array
2. Display each resource with format: "Resource: quantity/max_qty" (e.g., "Water Ice: 500/800")
3. Render progress bars for each resource (width = quantity/max_qty * 100%)
4. Use glassmorphic panel styling: `bg-[rgba(26,26,46,0.85)] border border-cyan-900/30`
5. Apply progress bar color: `bg-gradient-to-r from-cyan-600 to-purple-600`

### C. Data Binding
```erb
<%# Header Data -->
<%# @system_status = MockDataHelper.system_status %>

<%# Bay Inventory Data -->
<%# @bay_items = MockDataHelper.bay_inventory %>

<%# template variables are set in controller before render %>
```

### D. Implementation Notes
- Place both panels in `app/views/dashboard/index.html.erb`
- Set `@system_status` and `@bay_items` in `DashboardController#index`
- Wrap in container: `<div class="grid grid-cols-1 lg:grid-cols-2 gap-4">`
- Header spans full width (use appropriate Tailwind grid span if needed)
- Bay Inventory occupies left column of 2-column grid (on desktop)

### E. Testing (Acceptance)
- [ ] GET /dashboard returns 200
- [ ] Header displays Commander Nova's name + settlement + GCC
- [ ] Bay Inventory shows all 4 resources (Water Ice, Oxygen O2, Deuterium, Regolith)
- [ ] Progress bars render with correct fill percentages
- [ ] Glassmorphic styling applied (inspect CSS classes in browser dev tools)
- [ ] Mobile responsive: stacks to single column on screens < 1024px

---

## TASK 3: DASHBOARD — SHIPS & MANUFACTURING QUEUE

**Scope**: Implement panels 2 (ships) + 3 (manufacturing queue + research tab toggle)

**Task File Name**: `2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md`

**Location**: `projects/galaxy-game-ui/tasks/backlog/`

**What to Include**:

### A. Task Description
Implement the Ships & Active Units panel and the Pending Operations panel (Manufacturing Queue with Research Queue accessible via toggle). This pair balances operational fleet status (left) with production timeline (right).

### B. Acceptance Criteria (from approved design)

**Ships & Active Units Panel**:
1. Iterate through `MockDataHelper.player_ships` array
2. Display each ship with format: "Ship Name — Status (ETA if in transit)"
3. Render status badges with color coding:
   - Docked → `bg-green-900/50 text-green-400`
   - In Transit → `bg-cyan-900/50 text-cyan-400`
   - Other status → `bg-gray-800/50 text-gray-400`
4. Use glassmorphic panel styling: `bg-[rgba(26,26,46,0.85)] border border-cyan-900/30`

**Pending Operations Panel (Tabbed)**:
1. Display two tabs: "Manufacturing" and "Research"
2. Default to Manufacturing tab on load
3. Manufacturing tab iterates through `MockDataHelper.manufacturing_queue`:
   - Display job name, facility, progress %, ETA
   - Render progress bars: `bg-gradient-to-r from-cyan-600 to-purple-600`
   - Status badge for each job (In Progress / Queued)
4. Research tab iterates through `MockDataHelper.research_queue`:
   - Display blueprint name, progress %, ETA, level
   - Render progress bars with same styling
5. Tab toggle controlled by Stimulus JS (`toggle-research` action)

### C. Data Binding
```erb
<%# Ships Data -->
<%# @ships = MockDataHelper.player_ships %>

<%# Manufacturing Data -->
<%# @mfg = MockDataHelper.manufacturing_queue %>

<%# Research Data -->
<%# @research = MockDataHelper.research_queue %>
```

### D. Implementation Notes
- Place both panels in `app/views/dashboard/index.html.erb`
- Set `@ships`, `@mfg`, `@research` in `DashboardController#index`
- Ships panel occupies right column of top 2-column grid (on desktop)
- Manufacturing/Research panels occupy bottom row (full width)
- Use Stimulus controller `dashboard-tabs` with toggle action for research tab
- Tab indicator: highlight active tab with cyan border (`border-cyan-400`)

### E. Testing (Acceptance)
- [ ] Ships panel displays all ships with correct status badges
- [ ] Manufacturing tab shows all jobs with progress bars
- [ ] Research tab is hidden by default (toggle to reveal)
- [ ] Toggle between Manufacturing ↔ Research tabs works via Stimulus JS
- [ ] Progress bars render with correct percentages
- [ ] Status badges color-code correctly (green/cyan/gray)
- [ ] Mobile responsive: tabs stack vertically, full width

---

## TASK 4: DASHBOARD — OPEN ORDERS & MARKET FOOTER

**Scope**: Implement panels 4 (open orders with buy orders/missions tabs) + 5 (market quick-panel footer)

**Task File Name**: `2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md`

**Location**: `projects/galaxy-game-ui/tasks/backlog/`

**What to Include**:

### A. Task Description
Implement the Open Orders panel (Buy Orders + Missions tabs) and the Market Quick-Panel footer. This pair surfaces economy opportunities (NPC demand + player missions) and provides real-time market reference data.

### B. Acceptance Criteria (from approved design)

**Open Orders Panel (Tabbed)**:
1. Display two tabs: "Buy Orders" and "Missions"
2. Default to Buy Orders tab on load
3. Buy Orders tab iterates through `MockDataHelper.npc_buy_orders`:
   - Display corporation name, item needed, quantity requested, price per unit, total value
   - Format: "LDC needs Water Ice: 500 units @ 1,200 GCC = 600,000 GCC total"
   - Use table or card layout (consistent with design)
4. Missions tab iterates through `MockDataHelper.active_missions`:
   - Display mission name, offer (bounty/reward), deadline, status
   - Status badges: Available / In Progress (color-coded as cyan/yellow)
5. Tab toggle controlled by Stimulus JS

**Market Quick-Panel Footer**:
1. Display full-width footer section below all panels
2. Render compressed buy/sell spreads via `MockDataHelper.market_quick_panel`
3. Show commodity name, current buy price, current sell price, trend indicator
4. Use minimal styling (condensed table or horizontal scrollable list)
5. Header row: "Commodity | Buy Price | Sell Price | Trend"

### C. Data Binding
```erb
<%# Buy Orders Data -->
<%# @orders = MockDataHelper.npc_buy_orders %>

<%# Missions Data -->
<%# @missions = MockDataHelper.active_missions %>

<%# Market Footer Data -->
<%# @market = MockDataHelper.market_quick_panel %>
```

### D. Implementation Notes
- Place both panels in `app/views/dashboard/index.html.erb`
- Set `@orders`, `@missions`, `@market` in `DashboardController#index`
- Open Orders panel occupies full width below Manufacturing/Research
- Market Quick-Panel is a footer section (full width, spans bottom)
- Use Stimulus controller `dashboard-tabs` for order/mission toggle
- Table styling: use Tailwind classes from approved design (dark borders, cyan headers)
- Footer padding: `px-4 py-3`

### E. Testing (Acceptance)
- [ ] Buy Orders tab displays all NPC orders with price breakdowns
- [ ] Missions tab shows all active missions with deadlines
- [ ] Toggle between Buy Orders ↔ Missions tabs works via Stimulus JS
- [ ] Market Quick-Panel footer displays all commodities + prices
- [ ] Table headers use cyan accent color (`text-cyan-400`)
- [ ] Trend indicators display correctly (up/down/neutral)
- [ ] Mobile responsive: tables scroll horizontally, footer readable on small screens

---

## POST-TASK WORKFLOW

### After All Three Tasks Are Committed:
1. Stage all three task files: `git add projects/galaxy-game-ui/tasks/active/*.md`
2. Commit with message: `task: Dashboard implementation tasks 2-4 (header, ships, orders) [2026-10-21]`
3. Create session closeout via `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md`
   - Reference all 3 approved tasks
   - Link to approved design output
   - List any blockers or deviations (should be zero)
   - Include synthesis report of implementation

### Review Gate
Each task will go through:
1. Dispatch → Implementation Agent reads task
2. Synthesis report created (task understanding summary)
3. Implementation (build ERB templates, set controller variables, add Stimulus controllers)
4. Review (verify against design specs)
5. Approval (GO / REVISE / STOP)

### Important Reminders
- Do NOT deviate from approved design
- Do NOT add new panels or change layout
- Do NOT use database queries (MockDataHelper only)
- Do NOT change resource names or currency terminology
- Do NOT add EVE Online concepts

---

## STARTING NOW

1. Open `/projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md` (your approved design spec)
2. Reference the data binding map and styling notes throughout
3. Create Task 2: `2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md`
4. Create Task 3: `2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md`
5. Create Task 4: `2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md`
6. Each task follows TASK_TEMPLATE.md format exactly (steps, acceptance, synthesis, dispatch interface)
7. No implementation yet — just task file creation
8. Post synthesis reports to chat (one per task) before committing

**Synthesis reports should state**: "Task X ready for implementation dispatch per approved design [link to design output]"

---

**Ready to begin task file creation?**
