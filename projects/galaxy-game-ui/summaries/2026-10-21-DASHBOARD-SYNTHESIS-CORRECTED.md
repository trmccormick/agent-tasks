# Dashboard Synthesis Report — Corrected for Real MockDataHelper Methods

**Date**: 2026-10-21 (corrected)  
**Source**: Original 2026-10-20 synthesis corrected against live code audit

## Method Inventory — Verified Against Code

| Panel | Data Source | Real Method? | Notes |
|-------|-------------|--------------|-------|
| Location & Character Header | `system_status` | ✅ Yes | Returns system_name, player_gcc, player_name, location, timestamp |
| Location & Character Header | `character_at_settlement` | ✅ Yes | Alias: character_name, settlement, isk_balance, location |
| Local Bay Inventory | `bay_inventory` | ✅ Yes | Water Ice, Oxygen (O2), Deuterium, Regolith with quantity/max_qty/kg units |
| Local Bay Inventory | `player_inventory` | ✅ Yes | Alias for bay_inventory |
| Ships / Active Units | `player_ships` | ✅ Yes | Heavy Transport Tug Alpha, Logistics Hauler Beta, Scout Vessel Gamma |
| Ships / Active Units | `active_units` | ✅ Yes | Alias for player_ships |
| Open Orders / NPC Buy Orders | `npc_buy_orders` | ✅ Yes | LDC, AstroLift, MDC buy orders for Water Ice, Oxygen (O2), Deuterium |
| Pending Operations (composited) | **No single method** | ❌ N/A | Composed from `manufacturing_queue` + `research_queue` |
| Market Quick-Panel | `market_quick_panel` | ✅ Yes | npc_buy_orders + active_missions combined |

## Corrections Applied

### 1. `pending_operations` → Composition (not a real method)
**Was**: "Data sourced from MockDataHelper.pending_operations"  
**Now**: Data composed from three methods:
- `MockDataHelper.manufacturing_queue` — In-progress and queued manufacturing jobs
- `MockDataHelper.research_queue` — Blueprint research progress
- `MockDataHelper.active_missions` — Active player missions

### 2. `player_contracts` → Clarified (not a method)
**Was**: "MockDataHelper.player_contracts for NPC buy requests"  
**Now**: Use `MockDataHelper.npc_buy_orders` directly for NPC purchase demand; use `MockDataHelper.active_missions` for player missions/contracts

### 3. Method location corrected
**Was**: `app/helpers/mock_data.rb` (helpers directory)  
**Now**: `app/services/mock_data_helper.rb` (services directory)

## Complete Panel-to-Method Map

```
Dashboard View (GET /dashboard)
├── Location & Character Header
│   ├── @system_status = MockDataHelper.system_status          → system_name, player_gcc, player_name, location
│   └── @character = MockDataHelper.character_at_settlement    → character_name, settlement, isk_balance
├── Local Bay Inventory Panel
│   └── @bay_items = MockDataHelper.bay_inventory              → resource, quantity, max_qty, unit
├── Ships Panel (Active Units)
│   └── @ships = MockDataHelper.player_ships                   → name, type, status, location/destination, eta
├── Manufacturing Queue
│   └── @manufacturing = MockDataHelper.manufacturing_queue    → job, facility, progress, eta, status
├── Research Queue
│   └── @research = MockDataHelper.research_queue              → blueprint, progress, eta, level
├── NPC Buy Orders (Open Orders)
│   └── @orders = MockDataHelper.npc_buy_orders                → corporation, needs, quantity, price_per_unit, total
├── Active Missions
│   └── @missions = MockDataHelper.active_missions             → mission, offer, reward, deadline, status
└── Market Quick-Panel
    └── @market = MockDataHelper.market_quick_panel            → npc_buy_orders + active_missions merged
```

## Data Terminology (Galaxy Game Only)

- **Currency**: GCC (not ISK — ISK is from character_at_settlement for backward compat, but primary display should use player_gcc)
- **Resources**: Water Ice, Oxygen (O2), Deuterium, Regolith (NOT "Compressed Blue Ice" or "Heavy Water")
- **Corporations**: LDC (Lunar Development Corp), AstroLift Logistics, MDC (Mars Development Corp)
- **Settlements**: Luna Settlement Alpha, Mars Cycler Station, Luna Station Prime
- **Units**: Measured in kg for bulk resources

## No Changes Needed to Code

All 21 methods exist and work. The issue was only in synthesis documentation claiming non-existent composite methods as single sources. Dashboard implementation should compose from the real methods listed above.

---

**Corrections applied by**: Qwen planning agent (audit reconciliation)  
**Original synthesis date**: 2026-10-20, corrected: 2026-10-21
