# PVE Operational Data Schema Fix + MaterialProcessingService Validation

**Date**: 2026-08-14  
**Task**: 2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md  
**Status**: COMPLETED  

---

## What Was Changed

### 1. Mk2 Operational Data (`planetary_volatiles_extractor_mk2_data.json`)
- `extracted_gases` → `mixed_volatiles`
- `extracted_water` → `H2O`
- `depleted_regolith` kept as-is (correct)

### 2. Mk3 Operational Data (`planetary_volatiles_extractor_mk3_data.json`)
- `extracted_gases` → `mixed_volatiles`
- `extracted_water` → `H2O`
- `trace_minerals` kept as-is (Mk3-specific, silently ignored by code — fine)
- `depleted_regolith` kept as-is (correct)

### 3. MaterialProcessingService (`material_processing_service.rb`)
Added ±5% random variation to all Case B volatile extraction handlers:
- **H2O case**: `variation = 1.0 + (rand * 0.10 - 0.05)` applied to produced amount
- **mixed_volatiles case**: same variation formula per-volatile
- **depleted_regolith case**: same variation formula in the total_extracted calculation

### 4. Spec Tolerances (`material_processing_service_spec.rb`)
Updated test tolerances to account for ±5% random variation:
- H2O test: `a_value_within(0.01)` → `a_value_within(0.05)` (base value 0.75)
- CO2/N2 test: `a_value_within(0.01)` → `a_value_within(0.5)` (base values 9.0/6.0)
- depleted_regolith test: `a_value_within(0.01)` → `a_value_within(1.0)` (base value 5.0)

---

## Test Results
```
7 examples, 0 failures
```
All PVE tests pass. No regressions in TEU or other material processing specs.

---

## Issues Discovered
- None. Implementation was straightforward — all three changes were exactly as specified in the task file.

## Follow-up Tasks Needed
- Consider making ±5% variation configurable per-unit (currently hardcoded)
- Mk3 `trace_minerals` is silently ignored by the code — if this should produce output, a case handler is needed

## Lessons Learned
- When adding randomness to deterministic tests, widen tolerances proportionally to the base value range
- The existing test structure was well-designed; only tolerance values needed adjustment

---

**galaxyGame commits**: (to be committed)
