# Dashboard Design Session Output — Commander Nova Login View

**Date**: 2026-10-21  
**Session Type**: Information Architecture + Visual Layout Design  
**Status**: AWAITING APPROVAL (Gemini review + user confirmation)  
**Approver**: Pending design session output review

---

## Design Questions Answered

### Q1: Which panels? (5 from 9 options)

From the full set of possible panels, **6 go on the dashboard** (header + 5 panels):

| # | Panel | MockDataHelper Method(s) | Why It's In |
|---|-------|--------------------------|-------------|
| H | Location & Character Header | `system_status` + `character_at_settlement` | Always visible — tells Commander Nova where they are, who they are |
| 1 | Local Bay Inventory | `bay_inventory` (fallback: `player_inventory`) | Critical operational data — what resources exist at current settlement |
| 2 | Ships & Active Units | `player_ships` (fallback: `active_units`) | Fleet status — docked vs in-transit with ETAs |
| 3 | Manufacturing Queue | `manufacturing_queue` | Production pipeline — what's being built right now |
| 4 | Open Orders | `npc_buy_orders` + `active_missions` combined | Economy opportunities — NPC purchase demand + player missions |
| T | Pending Operations Tab | `research_queue` (toggle within Panel 3) | Research progress — secondary but important, accessed via tab on Panel 3 |

**Excluded from Dashboard** (available for Wormholes/Admin views):
- `wormhole_network`, `astrolift_routes` → dedicated /wormholes view
- `ai_manager_status`, `economic_indices`, `sim_engine_events`, `buy_order_status`, `digital_twin_scenarios` → dedicated /admin view
- `npc_corporations` → context on wormholes/transit views, not dashboard

### Q2: Layout — 2-column grid with full-width header

```
┌──────────────────────────────────────────────────┐
│  HEADER: Location & Character Banner              │
│  (full-width, always visible)                     │
│  Commander Nova — Luna Settlement Alpha            │
├─────────────────────┬────────────────────────────┤
│ PANEL 1             │  PANEL 2                    │
│ Local Bay Inventory │  Ships & Active Units       │
│ Water Ice: ████░░   │  Heavy Transport Tug A      │
│ Oxygen (O2): ███░░  │    → Docked                │
│ Deuterium: ██░░░░   │  Logistics Hauler B         │
│ Regolith: █████░░   │    → Docked                │
│                     │  Scout Vessel C             │
│                     │    → In Transit (2h)       │
├─────────────────────┼────────────────────────────┤
│ PANEL 3             │  PANEL 4                    │
│ Manufacturing ┌────▶│  Open Orders                │
│ Pending Opers │     │  LDC needs Water Ice        │
│ (tab toggle)  │     │    → 500 units @ 1,200      │
│               │     │  AstroLift needs Oxygen (O2) │
├───────────────┴─────┴────────────────────────────┤
│ PANEL 5: Market Quick-Panel (full-width bottom)   │
│ Compressed Buy/Sell Spreads across all commodities │
└──────────────────────────────────────────────────┘
```

**Mobile behavior**: All panels stack vertically (1-column). Header stays at top. Tab toggle on Panel 3 becomes a horizontal accordion.

### Q3: Visual Hierarchy

1. **Header loads first** — character name + settlement at largest font (`text-2xl`). This is the player's identity anchor.
2. **Bay Inventory** — immediately below header, left side. Resources are the most actionable data (you can sell them).
3. **Ships & Units** — right side, equal visual weight. Status badges make scanning fast.
4. **Manufacturing Queue + Open Orders** — below the fold on smaller screens. These scroll into view naturally.
5. **Research Queue** — hidden behind "Pending Operations" tab. Secondary to manufacturing but one click away.
6. **Market Quick-Panel** — full-width footer, always visible at bottom for quick market reference.

### Q4: Tabs & Expandable Sections

| Element | Location | Behavior |
|---------|----------|----------|
| **"Pending Operations" tab** | Top of Panel 3, left side | Toggles between Manufacturing Queue and Research Queue tabs (Stimulus JS controlled) |
| **Collapse/expand toggle** | Each panel's top-right corner | Click to collapse a panel to its header row only |
| **Status badges** | Embedded in each data row | Color-coded: Green = Active/Docked, Cyan = In Progress, Yellow = Queued, Gray = Idle/Awaiting |

### Q5: Data Relationships

- **Bay Inventory ↔ Open Orders**: Show which NPC corps want what resources — a filled bar in Bay panel highlights when an Open Order matches that resource
- **Open Orders ↔ Active Missions**: Missions are escalated buy orders with bounties — shown as "Missions" sub-tab within Open Orders panel
- **Ships ↔ Transit Routes**: Ships "In Transit" link to the wormholes/transit data (viewable via hover tooltip showing ETA route)
- **Manufacturing Queue ↔ NPC Buy Orders**: Completed jobs will fulfill pending orders — shown as a subtle count: "3/5 jobs complete, enough for 2 orders"

---

## Deliverable A: Dashboard Panel List (Final)

```
Panel H (Header): Location & Character Banner
  Data: @system_status = MockDataHelper.system_status
        @character = MockDataHelper.character_at_settlement

Panel 1: Local Bay Inventory
  Data: @bay_items = MockDataHelper.bay_inventory
  Fallback: @player_inv = MockDataHelper.player_inventory

Panel 2: Ships & Active Units
  Data: @ships = MockDataHelper.player_ships
  Fallback: @units = MockDataHelper.active_units

Panel 3: Pending Operations (Tabbed)
  Manufacturing tab: @mfg = MockDataHelper.manufacturing_queue
  Research tab:      @research = MockDataHelper.research_queue

Panel 4: Open Orders (Tabbed)
  Buy Orders tab:    @orders = MockDataHelper.npc_buy_orders
  Missions tab:      @missions = MockDataHelper.active_missions

Panel 5 (Footer): Market Quick-Panel
  Data: @market = MockDataHelper.market_quick_panel
```

---

## Deliverable B: Layout Diagram

(See Q2 above for the full layout diagram.)

---

## Deliverable C: Data Binding Map

| Template Variable | Source Method | Returned Structure | Used In Panel |
|-------------------|---------------|--------------------|---------------|
| `@system_status` | `MockDataHelper.system_status` | `{system_name, player_gcc, player_name, location, timestamp}` | Header |
| `@character` | `MockDataHelper.character_at_settlement` | `{character_name, settlement, isk_balance, location}` | Header (overlay) |
| `@bay_items` | `MockDataHelper.bay_inventory` | `[{resource, quantity, max_qty, unit}]` | Panel 1 |
| `@ships` | `MockDataHelper.player_ships` | `[{name, type, status, location/destination, eta}]` | Panel 2 |
| `@mfg` | `MockDataHelper.manufacturing_queue` | `[{job, facility, progress, eta, status}]` | Panel 3 (tab) |
| `@research` | `MockDataHelper.research_queue` | `[{blueprint, progress, eta, level}]` | Panel 3 (tab) |
| `@orders` | `MockDataHelper.npc_buy_orders` | `[{corporation, needs, quantity, price_per_unit, total}]` | Panel 4 (tab) |
| `@missions` | `MockDataHelper.active_missions` | `[{mission, offer, reward, deadline, status}]` | Panel 4 (tab) |
| `@market` | `MockDataHelper.market_quick_panel` | array of merged buy/sell data | Panel 5 |

---

## Deliverable D: Styling Notes

### Color Palette (Galaxy Game Only)
```css
--bg-base: #0a0a0a          — Page background
--panel-bg: rgba(26, 26, 46, 0.85) — Card/glassmorphic panel background
--border-cyan: rgba(0, 212, 255, 0.3) — Panel borders on hover/focus
--text-primary: #e0e0e0     — Body text
--text-secondary: #9ca3af   — Labels, secondary info
--cyan-accent: #00d4ff      — Primary action buttons, headers
--purple-accent: #9d4edd    — Secondary accents, progress bars
--gold-accent: #fbbf24      — Important highlights (missions, bounties)
```

### Tailwind Class Conventions

**Panel cards:**
```erb
<div class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4 hover:border-cyan-500/50 transition-colors">
```

**Header banner:**
```erb
<div class="bg-gradient-to-r from-purple-900/40 to-cyan-900/30 border-b border-cyan-900/30 px-6 py-4">
```

**Status badges (color mapping):**
| Status | Badge Class | Color Meaning |
|--------|-------------|---------------|
| Active / Docked | `bg-green-900/50 text-green-400` | Green = ready, operational |
| In Progress | `bg-cyan-900/50 text-cyan-400` | Cyan = active work in flight |
| Queued | `bg-yellow-900/50 text-yellow-400` | Yellow = waiting, not started |
| Idle / Awaiting | `bg-gray-800/50 text-gray-400` | Gray = inactive, no action |

**Progress bars (inventory fill, manufacturing progress):**
```erb
<div class="w-full bg-gray-800 rounded-full h-2 overflow-hidden">
  <div class="bg-gradient-to-r from-cyan-600 to-purple-600 h-2 rounded-full" style="width: <%= @item[:quantity].to_f / @item[:max_qty] * 100 %>%"></div>
</div>
```

**Typography:**
```erb
<h1 class="text-2xl font-bold text-[#e0e0e0]"><%= @system_status[:player_name] %></h1>           <!-- Character name -->
<h2 class="text-lg font-semibold text-cyan-400">Location: <%= @system_status[:system_name] %></h2> <!-- Settlement -->
<p class="text-sm text-gray-500">GCC <%= number_with_delimiter(@system_status[:player_gcc]) %></p>  <!-- Currency -->
```

**Layout grid:**
```erb
<div class="grid grid-cols-1 lg:grid-cols-2 gap-4 p-4">
  <!-- Panels stack on mobile, side-by-side on desktop (≥1024px) -->
</div>
```

---

## Post-Design Action Plan

### Step 1: Design Approval
- [ ] Review this document
- [ ] Confirm panel list is correct
- [ ] Confirm layout diagram matches vision
- [ ] Approve data binding map
- [ ] Approve styling conventions

### Step 2: Task Rewriting (After Design Approved)
Once design is approved by you, Planning Agent will:
1. Rewrite Task 2–4 with exact panel specs from this document
2. Break into subtasks: one file per panel/feature (≤150 lines each)
3. Follow TASK_TEMPLATE.md exactly
4. Reference ONLY the verified MockDataHelper methods in this document

### Step 3: Implementation
Each approved subtask follows the standard workflow:
- Dispatch → Implement → Review → Approve → Commit

---

**Design session output by**: Qwen Design Agent  
**Date**: 2026-10-21  
**Status**: AWAITING APPROVAL — no implementation started  
**Next action**: Human/Gemini review + design approval
