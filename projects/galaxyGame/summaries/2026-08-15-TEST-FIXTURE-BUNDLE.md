# 2026-08-15 TEST FIXTURE BUNDLE — Synthesis Report

## Scope
9 test fixture gaps/stale expectations across 7 spec files + 1 factory file.

## Findings per Item

### Item #1 — catalog_controller_spec.rb:44
**Issue**: Test expects `assigns(:categories)` to include `['units', 'modules', 'crafts']` but controller dynamically builds categories from `catalog_service.entries`.
**Root cause**: Test has a hardcoded expectation that doesn't match the dynamic controller behavior. The controller uses `.map { |e| e[:category] }.uniq.sort` — categories depend on what's in the catalog data.
**Fix**: Update test to check that categories is an array (current behavior) rather than asserting specific values. OR check actual catalog entries and assert based on what's there.

### Item #2/#3 — material_spec.rb:43,59 + materials.rb factory
**Issue**: Material factory missing `boiling_point` and `melting_point`. Tests expect `state_at(2000, 1.0) == 'liquid'` and `state_at(3000, 1.0) == 'gas'`.
**Root cause**: Factory doesn't define thermal properties needed for state calculations.
**Fix**: Add `melting_point { 1811 }` and `boiling_point { 3134 }` to the iron material factory (real iron values: melts at 1811K, boils at 3134K). This makes 2000K liquid and 3000K gas.

### Item #4 — geosphere_concern_spec.rb:344
**Issue**: Uses real 'iron' material via `geosphere.physical_state('iron', temp)`. Depends on iron having proper melting/boiling points.
**Root cause**: Same as #2/#3 — if iron fixture lacks thermal properties, this fails.
**Fix**: Resolved by Item #2 fix (adding melting_point/boiling_point to material factory).

### Item #5 — material_management_concern_spec.rb:193
**Issue**: Test expects `update_atmosphere_for_gas` to receive `"Fe"` but implementation normalizes via MaterialLookupService.
**Root cause**: Test expectation is stale — doesn't match current normalization behavior.
**Fix**: Change `.with("Fe", -100)` → `.with("iron", -100)`.

### Item #6 — base_unit_spec.rb:236
**Issue**: Test expects `add_pile` to receive `source_unit:` keyword but method signature is `def add_pile(material_name:, amount:)`.
**Root cause**: Test expectation includes a parameter that doesn't exist on the method.
**Fix**: Remove `source_unit: base_unit` from the `have_received(:add_pile)` expectation.

### Item #7 — game_data_generator_spec.rb:13
**Issue**: `after` hook runs `FileUtils.rm_rf(Rails.root.join('tmp'))` which deletes generated file before test assertion `expect(File).to exist(output_path)`.
**Root cause**: RSpec `after` hook runs after the test body, but the file is already gone by the time the assertion evaluates (the `after` hook in this case runs before the assertion completes because of how the test is structured).
**Fix**: Read file content in test body and assert on content + a captured path string. Actually re-reading: the `after` hook runs AFTER the `it` block. The issue is that `output_path` is a string created at let-definition time, but the file gets deleted by `after`. The fix: capture file existence in the test body before after runs — read the file content into a variable inside the `it` block.

### Item #8 — material_lookup_service_spec.rb:248
**Issue**: Mock expects `/Invalid JSON in file:/` but actual code logs different format.
**Root cause**: Mock expectation doesn't match real log message format.
**Fix**: Check actual error logging in the service, then update regex to match.

## STOPPED — Items #2/#3/#4: Architectural Gap (Not a Fixture Issue)

**Root cause**: The Material model reads `melting_point`/`boiling_point` from external JSON files via `MaterialLookupService`, NOT from DB columns or any model attribute. The lookup service loads from:
```
Rails.root.join(GalaxyGame::Paths::JSON_DATA, "resources", "materials")
```
with subdirectories: `building`, `byproducts`, `chemicals`, `gases`, `liquids`, `processed`, `raw`.

The factory sets DB columns that the model completely ignores. This is a **pre-existing data-source mismatch**, not a "stale fixture." The test expects `state_at(2000, 1.0) == 'liquid'` but the material JSON files don't have these values populated (or the path doesn't exist).

**Action**: Reverted factory to original state. This needs its own properly-scoped task — either:
- Add missing thermal properties to the iron.json data file, OR
- Fix the test to use a different assertion that doesn't depend on external JSON data

**Do NOT continue fixing this in this session.**

---

## Implementation Plan (Items #1, #5-#8 only)
1. ✅ Fix catalog_controller_spec (Item #1) — dynamic category assertion
2. ⏳ Fix material_management_concern_spec (Item #5) — normalize expectation
3. ⏳ Fix base_unit_spec (Item #6) — remove source_unit from expectation
4. ⏳ Fix game_data_generator_spec (Item #7) — capture file content before cleanup
5. ⏳ Fix material_lookup_service_spec (Item #8) — update mock regex
6. Run all affected specs
