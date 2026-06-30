[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md]
[RATIONALE: Luna surface/ISRU work — belongs in Phase 6, not sim calibration]
# TASK_TEMPLATE: MaterialProcessingService — PVE Volatile Output Validation

**Phase**: phase5
**Priority**: HIGH
**Type**: BUGFIX  
**Status**: BACKLOG
**Created**: 2026-04-04
**Last Updated**: 2026-06-22
**Estimate**: 6 hours (diagnosis + spec fix + validation)
**Relocated**: From reorganization_attempt_3 on 2026-06-22 (confirmed phase5 Luna bootstrap blocker)

---

## 🎯 Objective

Verify and complete MaterialProcessingService PVE (Planetary Volatiles Extractor) output logic. The service was partially refactored to output volatiles by chemical formula (H2O, CO2, N2, etc.) instead of generic `extracted_water` and `mixed_volatiles`, but tests may not reflect current implementation.

**Luna Mission Impact**: PVE deployment is Phase 2 of Luna mission. If this service doesn't output correctly, regolith cannot be processed into oxygen/water/He3 needed for settlement survival.

---

## 📋 Problem Statement

| Aspect | Status | Details |
|--------|--------|---------|
| **Code State** | ⏳ Partial | Service updated with H2O/mixed_volatiles handling (lines 128-157) |
| **Test State** | ❓ Unknown | Spec may reference old output IDs (`extracted_water`) |
| **PVE Output** | ⏳ Needs verification | Outputs should use chemical formulas from geosphere data |
| **Variation** | ✅ Designed | ±5% variation modeled but not yet implemented |
| **Luna Blocking** | 🔴 YES | Phase 2 ISRU deployment depends on this working |

---

## 🔍 Technical Context

### Current Implementation (lines 94-157)

**Case A: Non-zero output amount** (lines 110-114)
- Scales by input amount and efficiency
- ✅ Working

**Case B: Zero output amount** (lines 115-157)
- Reads geosphere `stored_volatiles` 
- Converts to percentages
- Case handlers for H2O, mixed_volatiles, depleted_regolith
- ⏳ Needs verification

### Expected PVE Output Model

```
Input: regolith (kg)
Outputs (from geosphere crust_composition.volatiles):
  H2O    → regolith × (geosphere.volatiles['H2O'] %) × efficiency × variation(±5%)
  CO2    → regolith × (geosphere.volatiles['CO2'] %) × efficiency × variation(±5%)
  N2     → regolith × (geosphere.volatiles['N2'] %) × efficiency × variation(±5%)
  [each volatile in geosphere data]
  depleted_regolith → input - sum(extracted volatiles)
```

### Variation Formula

```ruby
# Not yet implemented
variation = 1.0 + (rand * 0.10 - 0.05)  # ±5% uniform random
produced = base_amount * variation
```

---

## ✅ Acceptance Criteria

- [ ] Spec tests for PVE complete_job pass (all 4+ test cases)
- [ ] PVE outputs chemical formulas (H2O, CO2, N2, CH4, etc.) — NOT `extracted_water`
- [ ] Depleted regolith correctly calculated as input minus extracted
- [ ] Variation (±5%) implemented or documented as future enhancement
- [ ] Luna test: `rake luna_mission:execute` Phase 2 deploy_pve_unit passes
- [ ] No regressions in other material processing specs

---

## 📂 Files to Check/Edit

### Primary (Likely to Edit)
- [galaxy_game/app/services/manufacturing/material_processing_service.rb](galaxy_game/app/services/manufacturing/material_processing_service.rb)
  - Lines 94-157: `complete_job` method, PVE output handling
  - Verify variation (±5%) is implemented
  - Check geosphere data reading is correct

- [galaxy_game/spec/services/manufacturing/material_processing_service_spec.rb](galaxy_game/spec/services/manufacturing/material_processing_service_spec.rb)
  - Verify test expectations match chemical formula outputs (H2O, not extracted_water)
  - Test for depleted_regolith output calculation
  - Test variation behavior (if implemented)

### Reference (Check But Don't Edit)
- [data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json](data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json)
- [data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json](data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json)
- [data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json](data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json)

---

## 🚧 Implementation Checklist

### Phase 1: Diagnosis
- [ ] Read full `complete_job` implementation (lines 94-157)
- [ ] Check geosphere model for `stored_volatiles` structure
- [ ] Read PVE operational data files (Mk1, Mk2, Mk3)
- [ ] Check current spec expectations (what outputs does it expect?)

### Phase 2: Verify/Fix Code
- [ ] Confirm H2O case handler reads from geosphere correctly
- [ ] Confirm mixed_volatiles case handler outputs all volatiles except H2O
- [ ] Confirm depleted_regolith calculation: `input - sum(extracted)`
- [ ] Add ±5% variation if not present: `1.0 + (rand * 0.10 - 0.05)`
- [ ] Verify output IDs match geosphere volatile names

### Phase 3: Update Specs
- [ ] Update test expectations to expect H2O, CO2, etc. (not extracted_water)
- [ ] Test depleted_regolith output value
- [ ] Test with various geosphere compositions
- [ ] Test for edge cases (geosphere with no volatiles, etc.)

### Phase 4: Integration Test
- [ ] Run: `rspec spec/services/manufacturing/material_processing_service_spec.rb`
- [ ] Verify all tests pass
- [ ] Run Luna mission: `rake luna_mission:execute`
- [ ] Check Phase 2 PVE deployment succeeds

### Phase 5: Commit
- [ ] Commit: `fix: MaterialProcessingService — PVE volatile outputs and variation`

---

## 🔗 Dependencies

- **Blocked By**: None
- **Blocks**: Luna Phase 2 (ISRU deployment) 🔴 CRITICAL
- **Related**: Thermal Extraction Unit (TEU) similar logic

---

## 📝 Completion Template

*Agent completes this section upon implementation*

**Status**: [NOT STARTED / IN PROGRESS / COMPLETED / BLOCKED]
**Completion Date**: [YYYY-MM-DD]
**Git Commit**: [commit hash]
**Test Results**: [spec/services/manufacturing/material_processing_service_spec.rb — X passed, 0 failed]
**Luna Test**: [Phase 2 deploy_pve_unit — PASSED / FAILED]
**Notes**: [Any issues found, variation implementation status]
