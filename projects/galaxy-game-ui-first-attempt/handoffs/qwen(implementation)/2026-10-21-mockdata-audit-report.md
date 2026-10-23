# MockDataHelper Audit Report — 2026-10-21

**Audit Type**: Synthesis Cross-Check (read-only investigation)
**Auditor**: Qwen (Implementation Agent)
**Date**: 2026-10-21
**Status**: COMPLETE — awaiting human disposition

---

## Executive Summary

MockDataHelper service exists at `galaxy-game-ui/app/services/mock_data_helper.rb` with **21 public `def self.*` methods** (4 aliases). Cross-checking against available synthesis reports reveals **method naming gaps and missing synthesis files**.

### Top-Line Findings
- **21 methods** in MockDataHelper total (including 4 aliases)
- **0 disputed method names** — grep found zero matches for all suspicious names (`player_contracts`, `pending_operations`, `ai_manager_priorities`, `terraforming_scenarios`, `digital_twins`) across the entire project
- **Missing syntheses**: Admin panel synthesis, Wormholes panel synthesis do NOT exist in `/summaries/` (only Dashboard + Setup syntheses found)
- **Potential semantic gaps**: Dashboard synthesis uses terms that map to existing methods with different names

---

## 1. Complete Method Inventory (21 methods)

All from `galaxy-game-ui/app/services/mock_data_helper.rb`:

| # | Method Name | Line | Alias Of | Purpose |
|---|-------------|------|----------|---------|
| 1 | `system_status` | 3 | — | Dashboard header: player info, location, ISK, EAP |
| 2 | `character_at_settlement(settlement_id)` | 14 | — | Character/settlement data (parameterized) |
| 3 | `research_queue` | 24 | — | Blueprint research progress |
| 4 | `local_facilities` | 32 | — | Factory/refinery/lab utilization |
| 5 | `bay_inventory` | 41 | — | Local resource inventory with quantities |
| 6 | `player_inventory` | 53 | → `bay_inventory` | Alias for bay_inventory |
| 7 | `manufacturing_queue` | 58 | — | Manufacturing jobs progress |
| 8 | `player_ships` | 69 | — | Player vessels with status/location |
| 9 | `active_units` | 82 | → `player_ships` | Alias for player_ships |
| 10 | `npc_buy_orders` | 87 | — | NPC corporation buy orders |
| 11 | `active_missions` | 98 | — | Player missions (escalated buy orders) |
| 12 | `wormhole_network` | 106 | — | Wormhole/stability/decay data |
| 13 | `astrolift_routes` | 117 | — | Transit routes between systems |
| 14 | `transit_routes` | 129 | → `astrolift_routes` | Alias for astrolift_routes |
| 15 | `npc_corporations` | 134 | — | NPC corporation statuses/values |
| 16 | `ai_manager_status` | 145 | — | AI directives, priorities, next tick |
| 17 | `digital_twin_scenarios` | 158 | — | Digital twin simulation scenarios |
| 18 | `buy_order_status` | 169 | — | Buy order fulfillment status (admin) |
| 19 | `economic_indices` | 182 | — | PVE, energy trade, EAP indices (admin) |
| 20 | `sim_engine_events` | 193 | — | Simulation engine event log (admin) |
| 21 | `market_quick_panel` | 204 | → `npc_buy_orders + active_missions` | Combined market data for dashboard |

**Aliases**: 4 total (`player_inventory`, `active_units`, `transit_routes`, `market_quick_panel`)

---

## 2. Dashboard Synthesis — Cross-Check

**Source**: `/summaries/2026-10-20-DASHBOARD-SYNTHESIS.md`

### What Dashboard Synthesis References:
The synthesis describes building **4 panels**:
1. Location & Character Header
2. Local Inventory Panel
3. Ships Panel
4. Pending Operations (open orders, contracts, missions)

### Cross-Check Table:

| Synthesis Claim | Actual Code Status | Line | Notes |
|-----------------|--------------------|------|-------|
| `system_status` | ✅ EXISTS | 3 | Matches exactly — used for Location & Character Header |
| `character_at_settlement` | ✅ EXISTS | 14 | Related to dashboard header (not named in synthesis but functionally relevant) |
| `bay_inventory` | ✅ EXISTS | 41 | Used for Local Inventory Panel |
| `player_inventory` | ✅ EXISTS (alias) | 53 | Alias of bay_inventory — both work |
| `active_units` | ✅ EXISTS (alias) | 82 | Alias of player_ships — synthesis uses this name |
| `player_ships` | ✅ EXISTS | 69 | Source for Ships Panel (active_units alias) |
| `market_quick_panel` | ✅ EXISTS | 204 | Used for Market Quick-Panel |
| `npc_buy_orders` | ✅ EXISTS | 87 | Feed into market_quick_panel |
| `active_missions` | ✅ EXISTS | 98 | Feed into market_quick_panel (as missions) |
| **`player_contracts`** | ❌ NOT IN CODE | — | Mentioned in synthesis as concept ("contracts") but never calls a method by this name. Semantic match: `active_missions` or `npc_buy_orders`? |
| **`pending_operations`** | ❌ NOT IN CODE (as method) | — | Synthesis section 4 title says "Pending Operations" — this is a PANEL TITLE, not a method call. Semantically maps to: `manufacturing_queue` + `active_missions` + `buy_order_status` |

### Dashboard Findings Summary:
- **0 methods are truly missing** from MockDataHelper for dashboard use
- The term "Pending Operations" in the synthesis is a **panel label**, not a method name — it's composed of 3 existing methods: `manufacturing_queue`, `active_missions`, `buy_order_status`
- No new methods needed for Dashboard panel if synthesis treats "pending operations" as a composite view

---

## 3. Admin Synthesis — Cross-Check

**Source**: DOES NOT EXIST — no file found in `/summaries/` matching `*ADMIN*` or `*TELEMETRY*`

### Grep Results for Suspicious Names:
Searched entire `projects/galaxy-game-ui/` tree:
- `ai_manager_priorities` → **0 matches**
- `terraforming_scenarios` → **0 matches**
- `digital_twins` (without _scenarios) → **0 matches**

### What DOES Exist in MockDataHelper (Admin-relevant):

| Actual Method | Line | Admin Relevance | Synthesis Claim Status |
|---------------|------|-----------------|------------------------|
| `ai_manager_status` | 145 | ✅ Core admin method | EXISTS — may have been misremembered as `ai_manager_priorities` |
| `digital_twin_scenarios` | 158 | ✅ Admin simulation | EXISTS — synthesis may claim "digital_twins" (missing _scenarios) |
| `buy_order_status` | 169 | ✅ Admin order tracking | EXISTS |
| `economic_indices` | 182 | ✅ Admin economy panel | EXISTS |
| `sim_engine_events` | 193 | ✅ Admin event log | EXISTS |

### Admin Findings Summary:
- **No admin synthesis file exists** — any claims about `ai_manager_priorities`, `terraforming_scenarios`, etc. are from memory/task files, not verified syntheses
- The actual methods (`ai_manager_status`, `digital_twin_scenarios`) ARE in code
- **Task file's concern is valid**: if prior agent syntheses referenced non-existent names, those syntheses need updating — but the method itself exists

---

## 4. Wormholes Synthesis — Cross-Check

**Source**: DOES NOT EXIST — no file found matching `*WORMHOLE*` in `/summaries/`

### What DOES Exist:
| Actual Method | Line | Wormhole Relevance | Status |
|---------------|------|---------------------|--------|
| `wormhole_network` | 106 | ✅ Core wormhole data (systems, type, stability, decay, EM potential) | EXISTS |
| `astrolift_routes` | 117 | ✅ Related transit routes | EXISTS |
| `transit_routes` | 129 | ✅ Alias of astrolift_routes | EXISTS |

### Wormholes Findings Summary:
- **No wormhole synthesis file exists** — unable to cross-check claims
- Only 1 method directly needed: `wormhole_network` — which EXISTS

---

## 5. Container Testing

**Status**: SKIPPED — no running container available in current session. Terminal `docker` shows exit code 1 (container not accessible). 

### Recommended Test Commands (when container is running):
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui
docker exec -it galaxy_game bin/rails console << 'EOF'
# Dashboard methods
MockDataHelper.system_status
MockDataHelper.bay_inventory
MockDataHelper.active_units
MockDataHelper.market_quick_panel
MockDataHelper.active_missions

# Admin methods
MockDataHelper.ai_manager_status
MockDataHelper.digital_twin_scenarios
MockDataHelper.buy_order_status
MockDataHelper.economic_indices
MockDataHelper.sim_engine_events

# Wormhole methods
MockDataHelper.wormhole_network
MockDataHelper.astrolift_routes

# Aliases
MockDataHelper.player_inventory
MockDataHelper.transit_routes
MockDataHelper.character_at_settlement(:antollare)

# All return nil?
puts [system_status, bay_inventory, active_units, market_quick_panel, 
      active_missions, ai_manager_status, digital_twin_scenarios,
      buy_order_status, economic_indices, sim_engine_events,
      wormhole_network, astrolift_routes, transit_routes].map(&:class)
EOF
```

---

## 6. Discrepancy Summary

### Category A: Method Names — Synthesis Claims ≠ Actual (Naming Fixes Needed)

| Synthesis Claims | Actual Code | Resolution | Priority |
|------------------|-------------|------------|----------|
| `digital_twins` (implied by task file) | `digital_twin_scenarios` | **Reword synthesis** or **rename method** to `digital_twins` | Medium |

### Category B: Concepts Referenced — No Direct Method (Semantic Mapping OK)

| Synthesis Concept | Actual Methods (composite) | Resolution | Priority |
|-------------------|--------------------------|------------|----------|
| "Pending Operations" (panel title) | `manufacturing_queue` + `active_missions` + `buy_order_status` | **Document in synthesis** that this is a composite panel, not a single method | Low |
| "Contracts" (Dashboard context) | `active_missions` or `npc_buy_orders` | Clarify terminology in synthesis | Low |

### Category C: Missing Synthesis Files (Need to Be Created)

| Missing File | Should Reference Methods From |
|--------------|------------------------------|
| Admin panel synthesis | `ai_manager_status`, `digital_twin_scenarios`, `buy_order_status`, `economic_indices`, `sim_engine_events` |
| Wormholes panel synthesis | `wormhole_network`, `astrolift_routes` |

### Category D: No Discrepancies

All core MockDataHelper methods (21 total) **exist and are well-named**. The actual method inventory is complete for both Dashboard and Admin use cases.

---

## 7. Recommendations

### Immediate (No Code Changes):
1. **Create missing syntheses** — Write Admin and Wormholes panel synthesis files using actual method names from MockDataHelper
2. **Fix Dashboard synthesis** — Add note that "Pending Operations" is a composite of `manufacturing_queue` + `active_missions` + `buy_order_status`, not a single method
3. **Standardize naming** — When agents reference Admin methods, use exact names: `ai_manager_status` (not `ai_manager_priorities`), `digital_twin_scenarios` (not `digital_twins`)

### Medium-Term (Optional Code Changes):
4. **Consider adding aliases** if synthesis panels use different names:
   - `def self.pending_operations` → could alias to a composite of manufacturing_queue + active_missions
   - `def self.player_contracts` → could alias to npc_buy_orders or active_missions
5. **Rename vs. keep**: Current names are descriptive. Aliases (pattern already used) is cleaner than renaming

### Testing Priority:
6. Run container tests from Section 5 before any synthesis file creation to confirm all methods are callable

---

## Appendix: Full Grep Results

Commands run and their results:
```bash
# All disputed names — zero matches
grep -r "player_contracts" projects/galaxy-game-ui/ → 0 matches
grep -r "pending_operations" projects/galaxy-game-ui/ → 0 matches
grep -r "ai_manager_priorities" projects/galaxy-game-ui/ → 0 matches
grep -r "terraforming_scenarios" projects/galaxy-game-ui/ → 0 matches
grep -r "digital_twins" projects/galaxy-game-ui/ → 0 matches

# Available syntheses in /summaries/
ls summaries/ → 2026-10-20-DASHBOARD-SYNTHESIS.md, 2026-10-20-SETUP-SYNTHESIS.md
```

---

**Audit complete.** All MockDataHelper methods verified as existing and correctly named. Synthesis files may reference wrong names or be missing entirely. Recommend fixing synthesis documentation before proceeding with Dashboard/Admin/Wormhole implementation tasks.

Awaiting human disposition.
