# Synthesis: Shell Printing Service — Pre-existing Test Failures

**Date**: 2026-08-09
**Task**: 2026-08-06-MEDIUM-BUGFIX-SHELL-PRINTING-SERVICE-PREEXISTING-TEST-FAILURES.md

---

## Analysis Summary

### Bug 1: Job creation test (line 133)
- **Test**: `xit 'creates a shell printing job'` expects `job.target_unit == inflatable_tank`
- **Model**: `ConstructionJob` has `belongs_to :target_unit, foreign_key: 'inflatable_id'`
- **Service**: `create_shell_printing_job` sets `inflatable_id: inflatable_tank&.id`
- **Assessment**: Data flow is correct. The test should pass once un-skipped. No code changes needed for Bug 1.

### Bug 2: Composition storage (line 155)
- **Test**: `xit 'stores material composition in job metadata'` expects `job.materials_consumed['inert_waste']['composition']` to contain `{ 'SiO2' => 43.0, 'Al2O3' => 24.0 }`
- **Data flow traced**:
  1. `calculate_shell_materials`: builds `materials[name] = { amount: needed, composition: item[:composition] }` — composition IS captured from inventory item
  2. `ensure_materials_available`: strips `:missing` flag only — composition preserved
  3. `create_shell_printing_job`: stores in `target_values['materials_consumed']` — JSON serialization converts symbol keys to strings
- **Assessment**: Composition data IS captured and stored. The test may pass un-skipped, OR the issue could be that `find_regolith_material` returns a different material name than 'inert_waste', causing the wrong key in the hash.

### Plan
1. Un-skip both tests (xit → it)
2. Run spec to see actual results
3. If Bug 1 fails: debug factory/setup for `inflatable_id` FK
4. If Bug 2 fails: add debug logging to trace material name key and composition value, then fix

---

## Files to Edit
- `galaxy_game/spec/services/manufacturing/shell_printing_service_spec.rb` — un-skip lines 130, 155
- `galaxy_game/app/services/manufacturing/shell_printing_service.rb` — only if Bug 2 composition data flow is broken

## Risks
- Low risk: Both fixes are isolated to shell printing service and spec
- No model changes needed (per gotcha 1)
- No migration needed
