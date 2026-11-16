# STATUS SYNTHESIS REPORT — Sabatier Reactor CH4 Production Chain

**Date**: 2026-07-01
**Task**: 2026-07-01-MEDIUM-FEATURE-SABATIER-REACTOR-CH4-PRODUCTION.md
**Status**: AWAITING APPROVAL

---

## Pre-check Results

### 1. Does Sabatier Reactor already exist in blueprints?
**NO.** No existing unit blueprint references "sabatier" or "CH4" in the units directory. However, `methane.json` already has `"facility_type": "sabatier_reactor"` in its production section, confirming the concept was planned but never implemented as a unit.

### 2. Correct Material IDs (confirmed via grep)
| Resource | Material ID | File Path |
|----------|-------------|-----------|
| CH4 | `methane` | `data/json-data/resources/materials/gases/compound/methane.json` |
| CO2 | `carbon_dioxide` | `data/json-data/resources/materials/gases/compound/carbon_dioxide.json` |
| H2 | `hydrogen` | `data/json-data/resources/materials/gases/compound/hydrogen.json` |
| H2O | `water` | `data/json-data/resources/materials/liquids/reagents/water.json` |

**Convention confirmed**: Chemical formula IDs (CH4, CO2, H2, H2O) — use the material ID names.

### 3. NpcPriceCalculator can_produce_locally? Logic
```ruby
def can_produce_locally?(settlement, resource_name)
  material_data = load_material_data(resource_name)
  lunar_prod = material_data.dig('pricing', 'lunar_production')
  return false unless lunar_prod && lunar_prod['available']
  
  facility = lunar_prod['facility_required']
  return true if facility.blank?
  settlement.respond_to?(:has_facility?) && settlement.has_facility?(facility)
end
```

**Implication**: To make `can_produce_locally?("methane")` return true, the methane.json material file needs:
```json
"pricing": {
  "lunar_production": {
    "available": true,
    "facility_required": "sabatier_reactor",
    "cost_per_kg": <value>
  }
}
```

---

## Implementation Plan

### Files to Create (3 new files)

#### File 1: `data/json-data/blueprints/units/production/refineries/sabatier_reactor_bp.json`
- **Template**: Based on `gas_separator_unit_bp.json` (unit_blueprint v1.3)
- **Category**: production / refinery
- **Inputs**: CO2 (fluid_in), H2 (fluid_in), power_input
- **Outputs**: CH4 (fluid_out), H2O (fluid_out — routed back to electrolysis)
- **Production**: CO2 + 4H2 → CH4 + 2H2O (Sabatier reaction stoichiometry)
- **Energy**: ~1.2 kWh/kg (matches methane.json production energy)
- **Physical**: Compact unit, similar footprint to gas separator (~4m x 3m x 4.5m)

#### File 2: `data/json-data/operational_data/units/production/refineries/sabatier_reactor_op.json`
- **Template**: Based on `harvesting_rover_data.json` (craft_operational_data)
- **Processing capabilities**: Sabatier reaction parameters
- **Input resources**: carbon_dioxide, hydrogen
- **Output resources**: methane (primary), water (byproduct)
- **H2O routing**: Explicitly routed back to electrolysis loop via hub
- **Energy consumption**: 1.2 kWh/kg CH4 produced

#### File 3: `data/json-data/missions/tasks_v2/task_deploy_sabatier_reactor.json`
- **Template**: Based on `task_deploy_gas_separator.json` (generic_repeatable_task)
- **Effects**: deploy_unit → register_to_bus → set_unit_state
- **Hub routing**: CO2 input from Venus dump/cryo grid, H2 input from electrolysis/water cryo, CH4 output to fuel storage, H2O output to water cryo (electrolysis loop)

### Files to Update (1 existing file)

#### File 4: `data/json-data/resources/materials/gases/compound/methane.json`
- **Add**: `pricing.lunar_production` section with:
  - `available: true`
  - `facility_required: "sabatier_reactor"`
  - `cost_per_kg`: derived from existing cost_data (currently $1.85/kg purchase)

---

## Architecture Decisions

### Decision 1: Subcategory = "refinery"
The Sabatier Reactor is a chemical processing unit, not an extractor or fabricator. The `refineries` subdirectory already contains gas_separator_unit, gas_processing_plant, and gas_conversion_unit — all chemically adjacent. This is the correct location.

### Decision 2: H2O Byproduct Routing
Per GOTCHA 2 in task file, H2O must feed back into electrolysis loop via hub bus routing:
```json
{ "port_type": "water_output", "hub_port": "cryo_grid_water", "direction": "out" }
```

### Decision 3: CO2 Source Flexibility
Per GOTCHA 3, the reactor blueprint does NOT hardcode a CO2 source. The deployment task wires it to `cryo_grid_co2` which can be fed by either Venus skimmer dump (mid-game) or Earth import (early game). This is handled at the hub routing level, not the unit level.

### Decision 4: Material IDs Use Chemical Formula Convention
Confirmed from methane.json production section — input/output material references use the material ID (`carbon_dioxide`, `hydrogen`), NOT chemical formulas directly. The byproduct water uses `"id": "water"`.

---

## Testing Plan

### Step 6: Targeted Spec
Create `spec/services/ai_manager/sabatier_reactor_spec.rb` covering:
1. Sabatier Reactor blueprint loads correctly
2. Blueprint inputs match expected materials (CO2, H2)
3. Blueprint outputs include CH4 and H2O byproduct
4. Operational data energy consumption matches spec
5. Deployment task uses register_to_bus action
6. Hub routing wires H2O back to electrolysis loop

### Step 7: NpcPriceCalculator Verification
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails runner "puts Market::NpcPriceCalculator.calculate_bid(Settlement::BaseSettlement.first, \"methane\").inspect" 2>&1'
```

---

## Risks & Gotchas

1. **Hub Bus Routing prerequisite**: Per GOTCHA 1, must confirm `register_to_bus` engine action exists before deployment task will work. If not yet implemented, the task file will reference it but won't execute until that architecture task is complete.

2. **Methane.json pricing structure**: The current methane.json uses `cost_data.purchase_cost` format. Need to verify if `pricing.lunar_production` is the correct key path by checking how other materials with lunar production set up their pricing.

3. **Water material location**: Water is in `liquids/reagents/water.json`, not in `gases/`. The Sabatier output produces H2O which may be liquid or gas depending on reactor temperature. Need to verify the operational data references the correct water material ID.

---

## What I Will NOT Do (Out of Scope)

- Luna ice mining (blocked by DepositSpawner — separate task)
- Hub Bus Routing architecture (separate HIGH priority task)
- Full RSpec suite run (Rule 3 — targeted specs only)
- Migration files (no database changes needed — JSON data only)
- Venus skimmer or Titan skimmer modifications (out of scope)

---

## Estimated Scope
- **Files created**: 3
- **Files updated**: 1
- **Lines of code**: ~200-300 total
- **Test file**: ~50-80 lines
- **Risk level**: LOW (JSON data only, no Ruby code changes)

---

## Approval Required Before Proceeding
Please review and approve:
1. File locations and naming conventions
2. Blueprint structure (inputs/outputs/stoichiometry)
3. H2O byproduct routing to electrolysis loop
4. methane.json pricing update approach
5. Testing scope
