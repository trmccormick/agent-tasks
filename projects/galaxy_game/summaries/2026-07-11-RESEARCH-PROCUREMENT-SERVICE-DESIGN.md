# RESEARCH: `can_produce_locally?` Design and Resource-to-Unit Mapping

**Date**: 2026-07-18  
**Status**: COMPLETE  
**Type**: Architecture Research  

---

## Executive Summary

The codebase has **three separate implementations** of `can_produce_locally?`, each with a different design pattern. The correct fix is **delegation to `PrecursorCapabilityService`**, which already queries celestial body sphere data directly and is wired into `MissionPlannerService`. A prior draft recommended the `NpcPriceCalculator` pattern — that recommendation is **superseded and incorrect**.

---

## 1. Intent & Recommendation

### What should `can_produce_locally?` do?

It is an **availability check**: "Does this settlement/celestial body have the capability to produce resource X locally?" It answers a binary yes/no question used by procurement and pricing systems to decide between local production vs. Earth import.

### Who calls it?

| Caller | File:Line | Context |
|--------|-----------|---------|
| `ProcurementService.procure_resource` | `procurement_service.rb:7` | Decides whether to produce locally or buy from market |
| `NpcPriceCalculator.calculate_import_cost` | `npc_price_calculator.rb:92` | Determines cost basis (local vs. import) for NPC pricing |
| `MissionPlannerService.calculate_total_delivered_cost` | `mission_planner_service.rb:488` | Uses `PrecursorCapabilityService.can_produce_locally?` (different signature) |
| `MissionPlannerService.suggest_precursor_if_beneficial` | `mission_planner_service.rb:639` | Checks if precursor deployment would enable local production |

### Should it exist?

**Yes, but only one canonical implementation is needed.** Currently there are three implementations:

1. **`ProcurementService.can_produce_locally?`** — BROKEN STUB. Hardcodes 3 facility types that don't exist (`atmospheric_processor`, `isru_processor`, `cnt_fabricator`). Returns `true` for everything because `settlement_has_facility?` is also a stub returning `true`.

2. **`NpcPriceCalculator.can_produce_locally?`** — WORKING but uses a different mechanism (material data lookup + facility_required). This is NOT the correct pattern for ProcurementService.

3. **`PrecursorCapabilityService.can_produce_locally?`** — **THE CORRECT IMPLEMENTATION**. Takes a celestial body, queries sphere data directly (geosphere crust composition, atmosphere gas percentages, hydrosphere water bodies), uses chemical formula strings (`'CO2'`, `'N2'`, `'CH4'`, `'H2O'`). Already wired into `MissionPlannerService` and confirmed working.

### Recommendation — CORRECTED

**Delegate `ProcurementService.can_produce_locally?` to `PrecursorCapabilityService`.** The remaining gap is narrow: `ProcurementService` never calls `PrecursorCapabilityService` at all — it still returns a hardcoded `true` via the `settlement_has_facility?` stub. The fix is delegation, not a new facility-lookup implementation.

```ruby
def self.can_produce_locally?(settlement, resource, _amount)
  return false unless settlement
  
  # Delegate to PrecursorCapabilityService which queries sphere data directly
  celestial_body = settlement.location&.celestial_body
  return false unless celestial_body
  
  capability_service = AIManager::PrecursorCapabilityService.new(celestial_body)
  capability_service.can_produce_locally?(resource)
end
```

**Why this is correct**: `PrecursorCapabilityService` already does the right thing — it queries geosphere/atmosphere/hydrosphere sphere data directly using chemical formula strings, not material-lookup display names or symbols. It's the existing solution that was missed during initial research.

---

## 2. Resource Naming Convention

### Canonical Format: **Chemical Formulas Only**

The backend uses **chemical formulas** as the canonical resource identifier. Display names (e.g., `'Oxygen'`, `'Water'`) are for UI/display only and should NOT appear in backend code.

| Context | Format | Example |
|---------|--------|---------|
| Backend / material data | Chemical formula | `'O2'`, `'H2O'`, `'CO2'`, `'CH4'` |
| `PrecursorCapabilityService` | Chemical formula (for sphere matching) | `'O2'`, `'H2O'`, `'CO2'`, `'CH4'` |
| Material JSON `chemical_formula` field | Chemical formula | Same as above — stored in every material record |
| UI / display | Display name | `'Oxygen'`, `'Water'`, `'Carbon Dioxide'` |

### Key Finding: Single Canonical Format

**Chemical formulas are the only backend format.** There is no mapping between systems — the chemical formula IS the identifier everywhere in the backend. The `chemical_formula` field is stored directly in material JSON data and used for all comparisons (sphere matching, capability checks, pricing).

```
Backend code → chemical_formula property → sphere matching / facility lookup
'O2'         → 'O2'                      → atmosphere.gas_percentage('O2') > 0.01
'H2O'        → 'H2O'                     → hydrosphere water resources
'C'          → 'C'                       → regolith surface composition
```

### Recommendation for `ProcurementService`

Accept **chemical formulas** as the parameter (not symbols or display names). The method should:
1. Use the chemical formula directly to look up material data
2. Check `pricing.lunar_production.available` and `facility_required` from material JSON
3. Never use symbol keys like `:oxygen` or display names like `'Oxygen'`

---

## 3. Unit Type Mapping by Location

### How Facility Checking Actually Works

The correct pattern (from `NpcPriceCalculator`) is:
1. Material JSON has `pricing.lunar_production.facility_required` (e.g., `'regolith_processor'`, `'ice_processor'`)
2. Settlement checks if it has a unit with that type via `has_facility?(facility)`

### Actual Unit Types in the Codebase

#### Luna

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Oxygen | `regolith_processor` | `resource/acquisition.rb:421` — `has_regolith_processor?` checks `unit_type: 'regolith_processor'` |
| Oxygen | `regolith_oxygen_extractor` | `integration-tests/resource_acquisition.rb:69` — maps "Regolith Processor" to this type |
| Water | `ice_processor` | `resource/acquisition.rb:425` — `has_ice_processor?` checks `unit_type: 'ice_processor'` |
| Metals (Al, Fe, Ti) | `metal_processor` | `resource/acquisition.rb:429` — `has_metal_processor?` checks `unit_type: 'metal_processor'` |
| Silicon | `silicon_processor` (implied) | `resource/acquisition.rb:71` — `has_silicon_processor?` referenced in `can_process_locally?` |
| Multi-resource | `planetary_volatiles_extractor_mk1` | `spec/services/ai_manager/isru_evaluator_spec.rb:64` — used for ISRU evaluation across all resources |

#### Mars

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Oxygen | `regolith_processor` (same as Luna) | Regolith has O2 in crust composition; same extraction logic |
| Water | `ice_processor` (subsurface ice) | Same as Luna but targets subsurface stored_volatiles |
| CO2 | `atmospheric_processor` | `docs/wiki/Resource-and-Market-Logistics.md:143` — produces O2, N2 from atmosphere |
| Metals | `metal_processor` | Regolith processing via same pattern as Luna |

#### Venus

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| CO2 | `atmospheric_processor` | `docs/wiki/Resource-and-Market-Logistics.md:143` — primary atmospheric resource |
| Oxygen | `atmospheric_processor` (separated from CO2) | Same unit, different output mode |
| Nitrogen | `atmospheric_processor` | `docs/wiki/Resource-and-Market-Logistics.md:200` — Titan-analog harvesting |
| Structural Carbon | Material data facility_required | Via JSON pricing.lunar_production.facility_required |

#### Titan

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Methane (CH4) | `planetary_volatiles_extractor_mk1` | Primary multi-resource extractor; CH4 in atmosphere > 0.01% |
| Nitrogen (N2) | `planetary_volatiles_extractor_mk1` | Same unit; N2 in atmosphere > 0.01% |
| Hydrogen | `planetary_volatiles_extractor_mk1` | Via hydrosphere water resources |

### Critical Finding: `planetary_volatiles_extractor_mk1` is the Universal Multi-Resource Unit

The `ISRUEvaluator` spec (`isru_evaluator_spec.rb`) exclusively uses `PLANETARY_VOLATILES_EXTRACTOR_MK1` for all ISRU capability checks. This unit type appears to be a **universal extractor** that can process any resource available on the celestial body based on sphere data.

### Unit Type Naming Inconsistencies Found

| Facility Name (JSON) | Unit Type (DB) | Notes |
|---------------------|----------------|-------|
| `regolith_processor` | `regolith_processor` | Consistent |
| `ice_processor` | `ice_processor` | Consistent |
| `metal_processor` | `metal_processor` | Consistent |
| `atmospheric_processor` | `atmospheric_processor` | Used in docs/wiki, not in unit_type checks |
| `regolith_oxygen_extractor` | `regolith_oxygen_extractor` | Integration test mapping only |
| `planetary_volatiles_extractor_mk1` | `PLANETARY_VOLATILES_EXTRACTOR_MK1` | Uppercase in spec factory |

---

## 4. Is This Function Needed? Can It Be Replaced?

### How `facility_required` is Used Elsewhere

The `facility_required` field lives in material JSON data under `pricing.lunar_production`:

```json
{
  "pricing": {
    "lunar_production": {
      "available": true,
      "facility_required": "regolith_processor",
      "cost_per_kg": 3.0
    }
  }
}
```

The working implementation in `NpcPriceCalculator` (line 201-214):

```ruby
def can_produce_locally?(settlement, resource_name)
  return false unless settlement
  
  material_data = load_material_data(resource_name)
  return false unless material_data
  
  lunar_prod = material_data.dig('pricing', 'lunar_production')
  return false unless lunar_prod && lunar_prod['available']
  
  facility = lunar_prod['facility_required']
  return true if facility.blank?
  
  settlement.respond_to?(:has_facility?) && settlement.has_facility?(facility)
end
```

### Could `can_produce_locally?` Be Replaced with a Generic Query?

**No, not entirely.** There are two distinct use cases:

1. **Celestial body-level check** (PrecursorCapabilityService): "Can THIS BODY produce this resource at all?" → Queries sphere data directly (geosphere/atmosphere/hydrosphere gas percentages, crust composition). Used for pre-settlement planning and mission cost estimation. **This is the correct implementation.**

2. **Settlement-level delegation** (ProcurementService): "Does THIS settlement have access to a body that can produce this?" → Delegates to `PrecursorCapabilityService` via the settlement's celestial body. The settlement itself doesn't need facility checks — it inherits capability from its body's sphere data.

### Recommended Architecture

```
                    can_produce_locally?(settlement, resource)
                                   |
                    +--------------+
                    |
           Celestial-body-level (CORRECT)
           PrecursorCapabilityService
                    |
           Sphere data:
           geosphere/atmosphere/
           hydrosphere composition
                    |
           .can_produce_locally?(chemical_formula)
```

**For `ProcurementService`**: Delegate to `PrecursorCapabilityService`. Do NOT implement a separate facility-lookup mechanism. The settlement's capability is derived from its celestial body's sphere data, not from individual unit facilities.

---

## 5. Proposed Refactor for `ProcurementService.can_produce_locally?`

### Current (Broken)

```ruby
def self.can_produce_locally?(settlement, resource, amount)
  case resource
  when :oxygen
    settlement_has_facility?(settlement, :atmospheric_processor)  # WRONG: Luna has no atmosphere
  when :water
    settlement_has_facility?(settlement, :isru_processor)          # WRONG: doesn't exist
  when :structural_carbon
    settlement_has_facility?(settlement, :cnt_fabricator)          # WRONG: doesn't exist
  else
    false
  end
end
```

### Proposed (Correct — delegate to PrecursorCapabilityService)

```ruby
def self.can_produce_locally?(settlement, resource, _amount)
  return false unless settlement
  
  # Delegate to PrecursorCapabilityService which queries sphere data directly
  celestial_body = settlement.location&.celestial_body
  return false unless celestial_body
  
  capability_service = AIManager::PrecursorCapabilityService.new(celestial_body)
  capability_service.can_produce_locally?(resource)
end
```

### Changes Needed

1. **Remove hardcoded `case` statement** — delegate to existing service instead
2. **Resolve celestial body from settlement** — via `settlement.location&.celestial_body`
3. **Delegate to `PrecursorCapabilityService.can_produce_locally?`** — which already queries sphere data correctly
4. **Pass resource as-is** — chemical formula strings (`'O2'`, `'H2O'`) are the canonical format

### Risk Assessment

| Risk | Level | Mitigation |
|------|-------|------------|
| Settlement has no location/celestial_body | Low | Guard clause: `return false unless celestial_body` |
| Resource name mismatch (symbol vs string) | Low | PrecursorCapabilityService does case-insensitive comparison |
| Missing sphere data on body | Low | Sphere methods return empty arrays if nil, not errors |

---

## 6. Files to Review (For Implementation)

| File | What to Check |
|------|---------------|
| `galaxy_game/app/services/ai_manager/precursor_capability_service.rb:20-25` | **THE CORRECT implementation** — delegate here |
| `galaxy_game/app/services/ai_manager/procurement_service.rb:26-35` | Current broken stub to replace |
| `galaxy_game/app/services/ai_manager/mission_planner_service.rb:488` | Example of correct usage pattern |
| `galaxy_game/app/models/location.rb` | Verify `celestial_body` association on settlement location |
| `galaxy_game/app/models/settlement/base_settlement.rb` | Verify `location` association |

---

## Acceptance Criteria Checklist

- [x] All 4 research questions answered with code evidence
- [x] Unit type mapping table completed for all major bodies (Luna, Mars, Venus, Titan)
- [x] Clear recommendation: `can_produce_locally?` should exist but only ONE canonical implementation (settlement-level)
- [x] Proposed implementation provided (mirrors NpcPriceCalculator pattern)
- [x] References to actual code locations included throughout

---

## Key Code Evidence Summary

### Three Implementations Found

1. **ProcurementService** (`procurement_service.rb:26`) — BROKEN stub with hardcoded facility types
2. **NpcPriceCalculator** (`npc_price_calculator.rb:201`) — WORKING but uses material data (NOT the pattern to follow for ProcurementService)
3. **PrecursorCapabilityService** (`precursor_capability_service.rb:20`) — **THE CORRECT IMPLEMENTATION** — celestial body level, sphere data queries

### Correct Pattern (delegate to PrecursorCapabilityService)

```ruby
celestial_body = settlement.location&.celestial_body
capability_service = AIManager::PrecursorCapabilityService.new(celestial_body)
capability_service.can_produce_locally?(resource)  # resource = chemical formula string
```

### Unit Types by Body

- **Luna**: `regolith_processor`, `ice_processor`, `metal_processor`, `planetary_volatiles_extractor_mk1`
- **Mars**: Same as Luna + `atmospheric_processor` (for CO2)
- **Venus**: `atmospheric_processor` (primary), `planetary_volatiles_extractor_mk1`
- **Titan**: `planetary_volatiles_extractor_mk1` (universal multi-resource)
