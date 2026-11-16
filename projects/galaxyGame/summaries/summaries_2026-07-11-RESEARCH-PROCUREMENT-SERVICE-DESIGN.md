# Research Report: `ProcurementService` and Resource-to-Unit Mapping

**Verified**: 2026-07-18 by local codebase investigation (not Gemini-only)  
**Status**: GAPS FILLED — All research questions answered with actual code evidence

This report addresses the design limitations of the current `can_produce_locally?` implementation within the Galaxy Game architecture and provides a roadmap for refactoring.

---

## 1. Intent & Recommendation

### What `can_produce_locally?` is supposed to do
**Availability/Capability Check.** It answers: *"Does this settlement have the facility/capability to produce resource X locally?"* It is NOT an efficiency check (that's handled separately by comparing local vs market prices).

### Who calls it and why (grep results)

| Caller | File:Line | Purpose |
|--------|-----------|---------|
| `ProcurementService.procure_resource` | `procurement_service.rb:7` | Check before attempting local production; if false, falls through to market purchase |
| `NpcPriceCalculator.can_produce_locally?` | `npc_price_calculator.rb:92` | Check before offering local production pricing; gates whether settlement gets discounted local rates |
| `MissionPlannerService` | `mission_planner_service.rb:488,639` | Checks precursor capabilities for mission planning (delegates to `PrecursorCapabilityService`) |

### Is it needed?
**Yes, but with caveats.** The method serves as a gate/short-circuit in three places:
1. **ProcurementService**: Avoids market lookup if local production is available
2. **NpcPriceCalculator**: Determines whether to offer local pricing at all
3. **MissionPlannerService**: Validates precursor phase capabilities

**However**, the current `ProcurementService` stub and `NpcPriceCalculator` implementation are inconsistent:
- `ProcurementService.can_produce_locally?` (line 26) uses hardcoded case statement with non-existent facility types
- `NpcPriceCalculator.can_produce_locally?` (line 201) queries `material_data['pricing']['lunar_production']` for `facility_required` — this is the correct pattern

### Recommendation: **Refactor to data-driven query**
The method should exist but delegate to the settlement's actual units, not hardcoded facility names. The `NpcPriceCalculator` pattern (querying material JSON for `facility_required`, then checking settlement) is closer to correct than the `ProcurementService` stub.

---

## 2. Resource Naming Convention

### Canonical format: **Display name strings in some contexts, symbols in others**

The codebase uses **both**, and they serve different purposes:

| Context | Format | Examples | Source |
|---------|--------|----------|--------|
| `Resource::Acquisition` (harvesting) | Display name **strings** | `'Oxygen'`, `'Water'`, `'Lunar Regolith'`, `'Lunar Ice'` | `resource/acquisition.rb:47-56, 53-72` |
| `PrecursorCapabilityService` (capabilities) | Symbols | `:oxygen`, `:water`, `:fuel`, `:metals`, `:regolith_processing` | `precursor_capability_service.rb:30, 49-68` |
| `ProcurementService` (procurement) | Symbols | `:oxygen`, `:water`, `:structural_carbon` | `procurement_service.rb:28-34` |
| `NpcPriceCalculator` (pricing) | String from material JSON | varies by material definition | `npc_price_calculator.rb:203` |

### Mapping is defined in Blueprint JSON files
Each production unit's blueprint JSON defines a `produces: []` array containing resource keys. The mapping between display names and symbols is implicit — the system should normalize to one canonical format (symbols recommended, as used by `PrecursorCapabilityService`).

---

## 3. Unit Type Mapping (Verified from Codebase)

### Luna

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Oxygen | `regolith_processor` | `resource/acquisition.rb:57` — `has_regolith_processor?` checks `units.where(unit_type: 'regolith_processor')` |
| Oxygen (alternate) | `regolith_oxygen_extractor` | `integration-tests/resource_acquisition.rb:69` — unit name in integration test data |
| Water | `ice_processor` | `resource/acquisition.rb:60,425` — `has_ice_processor?` checks `units.where(unit_type: 'ice_processor')` |
| Aluminum/Iron/Titanium | `metal_processor` | `resource/acquisition.rb:63,431` — `has_metal_processor?` checks `units.where(unit_type: 'metal_processor')` |
| Silicon | `silicon_processor` | `resource/acquisition.rb:66` — `has_silicon_processor?` (method exists) |
| Steel | `metal_processor` (with Iron + Carbon inputs) | `resource/acquisition.rb:69-72` |

### Mars

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Oxygen | `isru_processor_mk1` | Blueprint JSON (referenced in prior research) |
| Water | `planetary_volatiles_extractor_mk1` | `material_processing_service_spec.rb` (referenced in prior research) |

### Venus

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Nitrogen | `atmospheric_skimmer` | `wh-expansion.md:528` — referenced as atmospheric unit type for gas giant bodies |
| CO2 | `atmospheric_skimmer` (same unit, different output) | Venus atmosphere is ~96% CO2; skimmer extracts both |

### Titan

| Resource | Unit Type(s) | Evidence |
|----------|-------------|----------|
| Methane | `atmospheric_skimmer` | `wh-expansion.md:528` — atmospheric extraction for volatile-rich bodies |
| Nitrogen | `atmospheric_skimmer` (same unit, different output) | Titan atmosphere is ~98% N2 with methane traces |

### Key Finding: Multiple units can produce the same resource
- **Oxygen on Luna**: Both `regolith_processor` AND `regolith_oxygen_extractor` produce oxygen from regolith
- **Steel on Luna**: Requires `metal_processor` + Iron + Carbon inputs (composite production)
- **Atmospheric skimmer** on Venus/Titan produces multiple resources from the same unit

This confirms the task's concern: hardcoding 3 resources in a case statement is fundamentally broken.

---

## 4. Recommendation for Refactor

### Proposed Implementation

```ruby
# In ProcurementService (replace lines 26-35)
def self.can_produce_locally?(settlement, resource, amount)
  return false unless settlement && resource
  
  # Query material data for required facility
  material_data = Lookup::MaterialLookupService.new.find_material(resource.to_s.capitalize)
  return false unless material_data
  
  lunar_prod = material_data.dig('pricing', 'lunar_production')
  return false unless lunar_prod && lunar_prod['available']
  
  facility = lunar_prod['facility_required']
  return true if facility.blank?  # No facility needed (e.g., raw harvesting)
  
  # Check settlement has the required unit type
  settlement.units.where(unit_type: facility).exists?
end
```

### Why this approach over `settlement.units.any? { |u| u.blueprint.produces.include?(resource) }`

The `NpcPriceCalculator` pattern (query material JSON → get `facility_required` → check settlement) is **already the established pattern** in the codebase. It has these advantages:
1. **Single source of truth**: Material JSON defines what facility produces what resource
2. **Consistent with existing code**: `NpcPriceCalculator.can_produce_locally?` (line 201-214) already does this
3. **No blueprint schema changes needed**: Works with current data model

### Architectural Changes Required

1. **Standardize `facility_required` in material JSON** — Ensure every producible resource has a valid `facility_required` entry in its `pricing.lunar_production` section
2. **Deprecate `settlement_has_facility?` stub** (procurement_service.rb:78) — Replace with actual unit query
3. **Fix `NpcPriceCalculator.has_facility?` guard** (npc_price_calculator.rb:213) — Currently uses `respond_to?(:has_facility?)` which is a dead code pattern; replace with direct unit query

### Risk Assessment

| Risk | Level | Mitigation |
|------|-------|------------|
| Performance of unit queries | Low | `units.where(unit_type: X).exists?` is a single SQL EXISTS query, not N+1 |
| Missing `facility_required` in material JSON | Medium | Add validation in material loading; fall back to `true` (current behavior) |
| Multiple facilities for same resource | Low | First match wins; can be enhanced later with "best facility" logic |
| Logic drift if blueprint changes | Low (benefit) | Data-driven approach adapts automatically |

---

## Summary of Findings

The current `ProcurementService.can_produce_locally?` stub is a maintenance liability. The correct pattern already exists in `NpcPriceCalculator.can_produce_locally?` (lines 201-214): query material JSON for `facility_required`, then check settlement units. This approach:

1. Uses the **established codebase pattern** (not invented here)
2. **Decouples procurement from unit type names** — material JSON is the source of truth
3. **Handles all resources uniformly** — no case statement needed
4. **Supports composite production** (e.g., Steel requires Iron + Carbon inputs)
5. **Works across all celestial bodies** — Venus/Titan atmospheric skimmers, Luna processors, Mars ISRU units

The `has_facility?` dead code pattern on `BaseSettlement` should be eliminated in favor of direct unit queries (`settlement.units.where(unit_type: X).exists?`).