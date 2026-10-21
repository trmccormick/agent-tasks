# Galaxy Game UI — Dashboard Design Session Brief

**Date**: 2026-10-21  
**Session Type**: Information Architecture + Visual Layout Design  
**Duration Target**: ~1–2 hours  
**Role**: Design Agent (Human or Gemini)  
**Output Expected**: Approved dashboard layout, panel list, data binding map

---

## 1. Project Scope (What This Is — and Isn't)

### What This Prototype IS
- A **frontend design sandbox** for information architecture exploration
- Tests how complex game data (economy, logistics, character management) should be presented to players
- Uses placeholders and mock data — we're answering "how should this look?" not "does this work perfectly?"
- Validates patterns that will guide main game development

### What This Prototype IS NOT
- ❌ A backend or game engine — no database models, migrations, or workers
- ❌ Production-ready code — refactoring happens later
- ❌ A port of full game logic — we only show what the player needs to see

### Tech Stack (Non-Negotiable)
| Component | Constraint |
|-----------|------------|
| Framework | Ruby on Rails 8 |
| Styling | Tailwind CSS v4 (dark theme: #0a0a0a base, cyan #00d4ff primary, purple #9d4edd secondary) |
| Frontend | Hotwire (Turbo + Stimulus JS) |
| Data | In-memory MockDataHelper only — NO ActiveRecord queries |
| Asset Pipeline | Importmaps / Propshaft |

---

## 2. Current State (Everything Past Sessions Produced)

### ✅ Infrastructure — Complete and Committed
- Rails 8 app initialized at `/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui`
- Three routes: `GET /dashboard` (public), `GET /wormholes` (public), `GET /admin` (login required)
- Tailwind CSS v4 configured with dark sci-fi palette
- Base layout (`app/views/layouts/application.html.erb`) with nav, Tailwind, Stimulus tags

### ✅ Task 1 File Remediation — Complete
- Duplicate EVE-themed Steps 7–10 removed (~130 lines)
- Stray `eve-line:` CSS fragment removed
- Acceptance contradiction fixed: `GET /` returns 200 (direct render), `/admin` returns 302
- Auth scope clarified: login required only for `/admin`

### ✅ MockDataHelper Audit — Complete
All 21 public methods verified in codebase and callable. No fiction, no hallucination. **This is the single source of truth for all dashboard data.**

---

## 3. MOCKDATAHELPER — The Data Contract (Use This Exactly)

### Complete Method Inventory (Verified Against Code)

#### Dashboard Core Methods
| Method | Returns | Use in Dashboard |
|--------|---------|------------------|
| `system_status` | `{system_name, player_gcc, player_name, location, timestamp}` | Location & Character Header |
| `character_at_settlement` | `{character_name, settlement, isk_balance, location}` | Character info overlay |
| `bay_inventory` | Array of `{resource, quantity, max_qty, unit}` (Water Ice, Oxygen O2, Deuterium, Regolith) | Local Bay Inventory panel |
| `player_inventory` | Alias for bay_inventory | Same as bay_inventory |
| `player_ships` | Array of `{name, type, status, location/destination, eta}` | Ships / Active Units panel |
| `active_units` | Alias for player_ships | Same as player_ships |

#### Manufacturing & Research Methods
| Method | Returns | Use in Dashboard |
|--------|---------|------------------|
| `manufacturing_queue` | Array of `{job, facility, progress, eta, status}` (In Progress / Queued) | Pending Operations — Manufacturing tab |
| `research_queue` | Array of `{blueprint, progress, eta, level}` | Pending Operations — Research tab |

#### Economy Methods
| Method | Returns | Use in Dashboard |
|--------|---------|------------------|
| `npc_buy_orders` | Array of `{corporation, needs, quantity, price_per_unit, total}` (LDC, AstroLift, MDC) | Open Orders / Buy Orders panel |
| `active_missions` | Array of `{mission, offer, reward, deadline, status}` (Available / In Progress) | Player Missions panel |
| `market_quick_panel` | `npc_buy_orders + active_missions` merged | Market Quick-Panel overlay |

#### Wormholes & Logistics Methods
| Method | Returns | Use in Dashboard |
|--------|---------|------------------|
| `wormhole_network` | Array of `{system, type, stability, decay_timer, em_potential}` | Wormholes Network view |
| `astrolift_routes` | Array of `{route, hops, time, price, capacity}` | Transit routes table |
| `transit_routes` | Alias for astrolift_routes | Same as astrolift_routes |

#### Admin Methods (Not on Dashboard)
| Method | Returns | Use in Admin Panel |
|--------|---------|-------------------|
| `ai_manager_status` | `{directives, priorities, next_tick}` | AI Manager Overview |
| `economic_indices` | `{pve_index, energy_trade_index, eap_index, em_trade_value}` | Economic Dashboard |
| `sim_engine_events` | Array of `{timestamp, event, detail}` | Simulation Events Log |
| `buy_order_status` | Array of `{item, quantity, npc_offering, status}` | Buy Order Tracking |
| `digital_twin_scenarios` | Array of `{name, status, outcome, time_to_stable}` | Digital Twin Scenarios |

#### Context Methods
| Method | Returns | Use in Dashboard |
|--------|---------|------------------|
| `npc_corporations` | Array of `{name, status, current_activity, inventory_value}` | NPC Corps at connected nodes |

---

## 4. Key Corrections From Previous Sessions (Do NOT Revert)

### Resource Names — Galaxy Game ONLY (NOT EVE Online)
| ❌ WRONG (EVE terminology) | ✅ CORRECT (Galaxy Game) |
|---|---|
| Compressed Blue Ice | Water Ice |
| Oxygen Isotopes | Oxygen (O2) |
| Heavy Water | Deuterium |
| Exotic Matter (EM) | Regolith |

### Currency Terminology
- **Primary**: GCC (`player_gcc` from `system_status`) — use for all monetary display
- ISK exists in `character_at_settlement` but is backward-compat only; prefer `player_gcc`

### Data Location
- MockDataHelper lives at: `app/services/mock_data_helper.rb` (NOT `app/helpers/`)
- All methods are `def self.*` — call as `MockDataHelper.method_name`, NOT as view helper

---

## 5. Design Problem Statement

**"Commander Nova has just logged in to Galaxy Game. What does the player see first?"**

### Constraints on the Design
1. The dashboard must be a **character location view** (what you see when you log in), NOT a settlement management screen
2. Everything shown must come from MockDataHelper — no database queries, no ActiveRecord
3. All panels use glassmorphic styling: rgba backgrounds, neon borders, dark space theme (#0a0a0a base)
4. Layout must be responsive (mobile → tablet → desktop via Tailwind grid classes)
5. **Galaxy Game terminology only** — never EVE Online concepts in the UI

### Design Questions to Answer
1. **What panels go on the dashboard?** Pick from: Location Header, Local Bay Inventory, Ships/Units, Manufacturing Queue, Research Queue, Open Orders/Buy Orders, Player Missions, Market Quick-Panel, Settlement Status
2. **How many panels and in what layout?** 4-panel grid? Vertical scroll sections? Tabbed? Collapsible sidebar?
3. **What's the visual hierarchy?** What does Commander Nova see first on load? What scrolls away or tucks into tabs?
4. **Where do tabs, toggles, and expandable sections go?** (e.g., Manufacturing + Research could share a "Pending Operations" tab)
5. **How are data relationships shown?** (e.g., Buy orders → tie to which NPC corps? Show on the network map?)

---

## 6. EVE Dashboard Inspiration Notes

From the reference: EVE-style dashboards show:
- Real-time telemetry (status bars, progress indicators, decay timers)
- Dense information tables (buy/sell spreads, inventory fill bars)
- Neon accents on dark backgrounds (#0a0a0a base, #1a1a2e panels)
- Badge system for status (Docked / In-Transit / Available / Active / Queued)
- Glassmorphic card panels with subtle borders and rgba backgrounds

---

## 7. Deliverables Expected from Design Session

After this session completes, we expect:

### A. Dashboard Panel List (Final, No Ambiguity)
```
Panel 1: [Name] — data from MockDataHelper.method()
Panel 2: [Name] — data from MockDataHelper.method()
...etc.
```

### B. Layout Diagram (Text-based is fine)
```
┌──────────────────────────────────────┐
│  Panel 1 (full width, always visible) │
├──────────────┬───────────────────────┤
│ Panel 2      │   Panel 3             │
├──────────────┼───────────────────────┤
│ Panel 4      │   Panel 5             │
└──────────────┴───────────────────────┘
```

### C. Data Binding Map (Method → Template Variable)
```erb
<!-- Example format -->
<%# @system_status = MockDataHelper.system_status %>
<h1><%= @system_status[:player_name] %> — <%= @system_status[:system_name] %></h1>
```

### D. Styling Notes (Tailwind classes to use consistently)
- Panel backgrounds: `bg-[rgba(26,26,46,0.85)]`, borders: `border border-cyan-900/50`
- Status badges: color mapping (Green = Active/Docked, Cyan = In Progress, Yellow = Queued/Awaiting)
- Text hierarchy: `text-[#e0e0e0]` for body, `text-cyan-400` for headers, `text-gray-500` for labels

---

## 8. Post-Design Actions (After You Approve)

Once the design is approved by you:
1. Planning Agent rewrites Task 2–4 with the approved layout, panel list, and data binding map
2. Each task broken into small subtasks (one file each, ≤150 lines)
3. Implementation sessions follow strictly from the approved design — no deviation

---

**End of Brief**. All MockDataHelper methods verified. No fiction. Ready for design input.
