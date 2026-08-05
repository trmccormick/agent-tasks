# 2026-08-04 DIAGNOSIS: Slow base_satellite_spec.rb Runtime (CORRECTED)

## Profile Results (2026-08-04)

**Before (2026-07-25)**: 37 minutes 43 seconds for 13 examples
**After (2026-08-04)**: 1 minute 12 seconds for 13 examples — **97% improvement**

The original issue was likely environmental (container resource starvation, Bootsnap cache state, or cold Docker layer reads). The dramatic drop suggests the root cause was NOT code-level but infrastructure-level.

## Current Per-Example Breakdown

| Rank | Example | Time | % of Total |
|------|---------|------|-----------|
| 1 | `valid_deployment_location?` permits general space-based locations | 6.57s | 9.2% |
| 2 | `has the correct current location string` | 6.54s | 9.1% |
| 3 | `#needs_atmosphere? returns false` | 6.03s | 8.4% |
| 4 | `can access its location helper` | 5.91s | 8.2% |
| 5 | `#deploy raises if given invalid deployment location string` | 5.84s | 8.1% |
| 6-12 | Other `valid_deployment_location?` examples | 5.54–5.76s each | ~7.9% each |
| 13 | `#overclock temporarily increases mining rate` | 0.41s | 0.6% |

**Average per satellite example**: 5.85 seconds
**Non-satellite example** (`#overclock`): 0.41 seconds — runs in isolation, no factory setup

## Root Cause Analysis

### CRITICAL CORRECTION: CraftLookupService is stubbed in tests

The spec has a `before(:each)` block (lines 18-30) that stubs out BOTH lookup services:

```ruby
before(:each) do
  lookup_service = instance_double(Lookup::CraftLookupService)
  allow(Lookup::CraftLookupService).to receive(:new).and_return(lookup_service)
  # ...
  unit_lookup_service = instance_double(Lookup::UnitLookupService)
  allow(Lookup::UnitLookupService).to receive(:new).and_return(unit_lookup_service)
  # ...
end
```

**Therefore:**
- `CraftLookupService.new` is NEVER called during tests — it returns a double
- `UnitLookupService.new` is also stubbed — never hits disk
- My previous diagnosis about "37 JSON files scanned per instantiation" was **incorrect**
- Fix 1 (add caching to CraftLookupService) would have **zero effect** on this spec's runtime

### Actual Bottleneck: DB operations from `let!` blocks

Each example triggers multiple `let!` blocks that run BEFORE the example (due to `!`):

| `let!` block | What it creates | DB writes |
|--------------|-----------------|-----------|
| `celestial_body` | `CelestialBody.find_by!('LUNA-01')` | 1 SELECT |
| `owner` | `create(:player)` | INSERT player |
| `lunar_orbit_location` | `create(:celestial_location)` | INSERT location + FK to celestial_body |
| `satellite_with_specific_deployment_data` | `create(:base_satellite)` + `build_units_and_modules` | INSERT satellite, base_units, base_modules, base_rigs |
| `satellite_with_generic_deployment_data` | `create(:base_satellite)` + `build_units_and_modules` | INSERT satellite, base_units, base_modules, base_rigs |
| `satellite_with_recommended_units` | `create(:base_satellite)` + `build_units_and_modules` | INSERT satellite, base_units, base_modules, base_rigs |

**Per example:** 2 satellites × (1 satellite + N units + M modules + K rigs) = **~10-30 DB writes per example**

The `after_create :build_units_and_modules` callback still runs even though lookup services are stubbed. The method falls back to `operational_data` when available, but the DB operations (destroy_all, create!) still execute.

### Why the dramatic improvement?

The original 37-minute run was likely caused by:
1. **Cold Docker layer reads** — first run after container restart
2. **Container resource limits** — CPU/memory throttling in dev environment
3. **Bootsnap cache state** — the same Bootsnap-cache fix that resolved the LoadError may have warmed the cache

The current 70-second runtime is still high for 13 examples but is likely due to DB transaction overhead, not file I/O.

## Proposed Fixes (Not Applied — Diagnosis Only)

### Fix 1: Move `let!` to `let` where possible (MEDIUM IMPACT)

Change satellite factories from `let!` to `let` so they're only instantiated when referenced:

```ruby
# Before (runs before EVERY example):
let!(:satellite_with_specific_deployment_data) { ... }

# After (runs only when accessed):
let(:satellite_with_specific_deployment_data) { ... }
```

This would eliminate ~2 satellite creations per example for tests that don't need them.

### Fix 2: Use `build_stubbed` for satellites where DB persistence isn't needed (HIGH IMPACT)

Replace `create(:base_satellite)` with `build_stubbed(:base_satellite)` in factories or test setup where the record doesn't need to be persisted. This avoids the `after_create :build_units_and_modules` callback entirely.

### Fix 3: Add database cleaner strategy or transactional fixtures (LOW RISK)

Ensure `config.use_transactional_fixtures = true` is set in rails_helper.rb to wrap each example in a transaction that's rolled back after — this avoids permanent DB writes and speeds up cleanup.

## Next Steps Needed

1. **Profile without stubs** — temporarily comment out the `before(:each)` stubs and re-run to see if lookup services are actually the bottleneck
2. **Use stackprof** — add `require 'stackprof'` to profile CPU time during a single example run
3. **Check DB query count** — enable ActiveRecord logging in test env to count queries per example

---

**Diagnosed by**: Implementation Agent
**Date**: 2026-08-04
**Status**: DIAGNOSIS INCOMPLETE — needs deeper profiling (DB queries, stackprof)
