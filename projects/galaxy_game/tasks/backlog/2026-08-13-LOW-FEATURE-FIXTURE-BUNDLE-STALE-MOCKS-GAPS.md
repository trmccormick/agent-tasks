---
status: backlog
priority: LOW
type: TEST_FIXTURE_BUNDLE
system_domain: Test Infrastructure
mvp_alignment: TEST_RELIABILITY
local_worker_safe: true
created: 2026-08-13
last_updated: 2026-08-13
---

## Fixture Bundle — 9 Stale/Incomplete Test Fixtures

### Overview
Bundle of 9 low-priority test fixture gaps and stale expectations across the spec suite. None are blockers; all are maintenance items to align tests with current implementation.

### Items

#### 1. admin/catalog_controller_spec.rb:44 — Missing category assignments
- **Issue**: Controller's `#index` only assigns `categories: ['crafts']`. Test expects `['units', 'modules', 'crafts']`.
- **Root cause**: Controller is incomplete (missing units/modules category logic), not a test bug.
- **Priority**: Low — nothing currently depends on it.

#### 2. celestial_bodies/material_spec.rb:43 — Missing boiling_point fixture
- **Issue**: Test fixture doesn't provide `boiling_point`, causing `expected nil.present? to be truthy`.
- **Fix**: Add `boiling_point` to the material factory/fixture.

#### 3. celestial_bodies/material_spec.rb:59 — Missing melting_point fixture
- **Issue**: Same root cause as #2 — missing `melting_point` causes `state_at(2000, 1.0)` to return `"gas"` instead of expected `"liquid"`.
- **Fix**: Add `melting_point` to the material factory/fixture.

#### 4. geosphere_concern_spec.rb:344 — Same root cause as #2/#3
- **Issue**: `physical_state` returns `"gas"` instead of `"liquid"` due to missing melting/boiling point fixture data.
- **Fix**: Ensure geosphere test fixtures include proper melting/boiling points.

#### 5. material_management_concern_spec.rb:193 — Stale expectation
- **Issue**: Test expects `remove_material` to receive raw symbol `"Fe"`, but implementation correctly normalizes via `MaterialLookupService` to `"iron"`.
- **Fix**: Update test expectation from `"Fe"` to `"iron"`.

#### 6. base_unit_spec.rb:236 — Stale method argument
- **Issue**: Test calls `surface_store.add_pile(material_name:, amount:, source_unit:)` but `Storage::SurfaceStorage#add_pile` doesn't accept a `source_unit:` keyword.
- **Fix**: Remove `source_unit:` from the test call or update the method signature.

#### 7. game_data_generator_spec.rb:13 — Test/hook ordering issue
- **Issue**: Test asserts a file exists at `tmp/generated_item.json`, but the `after` hook runs `FileUtils.rm_rf(Rails.root.join('tmp'))` which deletes it before the assertion can verify.
- **Fix**: Capture the file content in the test before the `after` hook runs, or adjust hook ordering.

#### 8. material_lookup_service_spec.rb:248 — Mock expectation mismatch
- **Issue**: Mock expects `Rails.logger.error` to receive message matching `/Invalid JSON in file:/`, but actual code logs `"Error loading #{file}: #{e.message}"`.
- **Fix**: Update mock expectation to match real log format.

#### 9. HarvesterCompletionJob — Fixture/seeding gap (not a real bug)
- **Issue**: `expect(...).to be > 0, got 0.0` on oxygen after job completion.
- **Root cause**: Test likely doesn't seed the harvester/order fixtures correctly, or the `travel_to` block doesn't properly advance the job queue for the job to actually run before the assertion.
- **Fix**: Verify fixture seeding and job queue advancement in the test setup.

### Acceptance Criteria
- [ ] All 9 items resolved or documented as known limitations
- [ ] No regressions in existing passing specs
- [ ] Test suite remains green after fixes
