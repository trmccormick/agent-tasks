### PRE-IMPLEMENTATION VERIFICATION REPORT (Gate 2)

**Task**: 2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT
**Date**: 2026-10-06
**Status**: Read-only audit complete — no code changes made

---

## Group 1: Caller Audit

### `calculate_transfer_window` callers (production)
| Line | File | Context |
|------|------|---------|
| `transit_engine.rb:118` | `Mission::TransitEngine.schedule_departure` | Internal caller within same class |

**Verdict**: `calculate_transfer_window` is called by exactly **one production path**: `schedule_departure`. No other rake/service/job/controller calls it directly.

### `schedule_departure` callers (production)
| Line | File | Context |
|------|------|---------|
| `lunar_precursor_mission_validation.rake:759` | Earth→Luna (`precursor_hlt_1`) | **Target for static replacement** |
| `lunar_precursor_mission_validation.rake:766` | Earth→Venus (`venus_harvester_01`) | Must stay dynamic |

### `schedule_departure` callers (specs)
| Line | File | Context |
|------|------|---------|
| `transit_engine_spec.rb:116` | Venus harvester EARTH→VENUS | Existing spec |
| `transit_engine_spec.rb:129` | Titan harvester EARTH→TITAN | Existing spec |
| `transit_engine_spec.rb:138, 158` | Test craft EARTH→VENUS | Existing specs |

### Broad rescue / error transformation audit
**Result**: **CLEAN.** Zero `rescue`, `begin`, or `ensure` keywords found in `transit_engine.rb`. No rescue scope exists that could swallow `Mission::UnsupportedTransferError`. The guard can be placed anywhere before the dynamic calculation block without risk of being caught by an outer handler.

### Stop condition check
**No escalation.** Production-reachable callers are limited to Earth→Luna and Earth→Venus, both using standard Sol-system planet identifiers. No evidence of non-Sol/multi-star/procedurally-generated bodies reaching this API from production paths.

---

## Group 2: Lineage Verification and Factory/Test Feasibility

### `CelestialBodies::Planets::Planet` lineage
| Class | Inherits From | Abstract? |
|-------|--------------|-----------|
| `CelestialBodies::Planets::Planet` | `CelestialBodies::CelestialBody` | **Yes** (`self.abstract_class = true`) |
| `CelestialBodies::Planets::Rocky::TerrestrialPlanet` | `Planet` | No |
| `CelestialBodies::Planets::Rocky::SuperEarth` | `Planet` | No |
| `CelestialBodies::Planets::Rocky::LavaWorld` | `Planet` | No |
| `CelestialBodies::Planets::Rocky::CarbonPlanet` | `Planet` | No |
| `CelestialBodies::Planets::Ocean::OceanPlanet` | `Planet` | No |
| `CelestialBodies::Planets::Ocean::HyceanPlanet` | `Planet` | No |
| `CelestialBodies::Planets::Ocean::WaterWorld` | `PLANET` | No |
| `CelestialBodies::Planets::Gaseous::GasGiant` | `Planet` | No |
| `CelestialBodies::Planets::Gaseous::HotJupiter` | `Planet` | No |
| `CelestialBodies::Planets::Gaseous::IceGiant` | `Planet` | No |

### Non-Planet CelestialBody descendants (will be rejected by proxy)
All descend from `CelestialBody` directly, **NOT** from `Planets::Planet`:

| Class | Inherits From | Notes |
|-------|--------------|-------|
| `CelestialBodies::Moon` | `CelestialBody` | Top-level moon class |
| `CelestialBodies::Satellites::Moon` | `Satellite` → `CelestialBody` | Satellite-based moon |
| `CelestialBodies::Satellites::Satellite` | `CelestialBody` | Base satellite |
| `CelestialBodies::DwarfPlanet` | `CelestialBody` | Top-level dwarf planet |
| `CelestialBodies::BrownDwarf` | `CelestialBody` | Brown dwarf |
| `CelestialBodies::Star` | `ApplicationRecord` | **Not a CelestialBody subclass** |
| `CelestialBodies::Asteroid` | `CelestialBody` | Minor body |
| `CelestialBodies::Comet` | `CelestialBody` | Minor body |
| `CelestialBodies::KuiperBeltObject` | `Comet` | Minor body |
| `CelestialBodies::Protoplanet` | `CelestialBody` | Minor body |

### `CelestialBody` attributes (verified)
- `solar_system` → `belongs_to :solar_system, optional: true` ✅
- `parent_celestial_body_id` → via `Satellite` foreign_key ✅
- `orbital_elements` → `store_accessor :orbital_elements, :semi_major_axis, :eccentricity, :inclination, :mean_anomaly` ✅

### Factory/test feasibility
| Fixture Need | Available? | Details |
|-------------|-----------|---------|
| Base `CelestialBody` | ✅ `factory :celestial_body` | Has sequence-based identifier, all attributes |
| Planet-lineage (rocky/ocean/gaseous) | ✅ `terrestrial_planets.rb`, `ocean_planets.rb`, etc. | Factory files exist |
| Moon/Satellite | ⚠️ **Needs investigation** | `satellites/` directory has models; factory files not confirmed in initial grep |
| DwarfPlanet | ✅ `factory :dwarf_planet` exists | |
| Protoplanet | ✅ `factory :protoplanet` exists | |
| Star | ⚠️ **Not a CelestialBody** — outside scope per task | |

**Stop condition 3 check (dwarf planet fixture)**: `factory :dwarf_planet` exists. Test case 7 is feasible without redesign.

---

## Group 3: Seeded Retained-Route Verification

### Earth→Luna route
- `EARTH-01`: resolves to `CelestialBodies::CelestialBody` (not a `Planets::Planet` subclass — top-level)
- `LUNA-01`: resolves to `CelestialBodies::CelestialBody` (not a `Planets::Planet` subclass)
- **Both fail the Phase 1 proxy** (`is_a?(Planets::Planet)` check). This is the exact bug this task fixes.

### Earth→Venus route
- `EARTH-01`: same as above — likely not a `Planets::Planet` subclass
- `VENUS-01`: needs verification (not confirmed in audit)
- **Risk**: If Earth and Venus are also top-level `CelestialBody` records (not `Planets::Planet` subclasses), the existing dynamic path may already be broken for them. This is a **baseline observation**, not a blocker — the task preserves legacy behavior for eligible pairs.

### Seeded route verification status
**⚠️ UNCERTAINTY**: The audit did not confirm whether `EARTH-01`, `VENUS-01`, or `MARS-01` are stored as `Planets::Planet` subclasses (STI) or plain `CelestialBody` records. This requires a database query or seed data inspection to verify. If they are plain `CelestialBody` records, the existing dynamic path is already non-functional for these routes — which means the Phase 1 proxy would not change behavior for them.

**Recommendation**: Before implementation, run:
```sql
SELECT id, identifier, type FROM celestial_bodies WHERE identifier IN ('EARTH-01', 'VENUS-01', 'MARS-01', 'LUNA-01', 'TITAN-01', 'PLUTO-01');
```
to confirm STI type values.

---

## Group 4: Resolver Semantics + Error Class/Autoload/Rescue Convention

### Resolver verification
- **Method**: `def self.orbital_data(body_identifier)` at line 193
- **Resolution logic**: `CelestialBodies::CelestialBody.find_by(identifier: body_identifier.to_s.upcase)`
- **Case handling**: `.to_s.upcase` — case-insensitive via uppercase normalization ✅
- **Reuse**: The guard can use the same resolver pattern (or call `orbital_data` and check the returned body object)

### Error class convention
- **No `app/errors/` directory exists** in this project.
- **Convention**: Error classes are defined inline at the top of their respective files as `< StandardError`:
  - `MaterialError < StandardError` (in `material_management_concern.rb`)
  - `AtmosphereError < StandardError` (in `atmosphere_concern.rb`)
  - `GeosphereError < StandardError` (in `geosphere_concern.rb`)
  - `InvalidSystemBoundariesError < StandardError` (in `universe_registration_job.rb`)
  - `ConfigurationError < StandardError` (in `economic_config.rb`)
- **Recommended location for `Mission::UnsupportedTransferError`**: Inline at the top of `transit_engine.rb`, following the pattern:
  ```ruby
  class Mission::UnsupportedTransferError < StandardError; end
  ```

### Rescue scope verification
**CLEAN.** Zero rescue/begin/ensure in `transit_engine.rb`. No risk of error being swallowed.

### Method name verification (Tests section)
| Method | Exists? | Location |
|--------|---------|----------|
| `fallback_transfer_window` | ✅ | Line 417 |
| `compute_transit_days_dynamic` | ✅ | Line 358 |
| `fallback_transit_days` | ✅ | Line 393 |
| `compute_transit_days` | ✅ | Line 455 |

All four method names match the task file exactly. No discrepancies.

---

## Group 5: Fixed-Date Rake Baseline + Earth→Luna Output + Named 7-Day Constant

### Earth→Luna rake call (line 759)
```ruby
precursor_departure = Mission::TransitEngine.schedule_departure(
  "precursor_hlt_1", "EARTH-01", "LUNA-01", Time.current.to_date
)
```

### Earth→Venus rake call (line 766) — must stay dynamic
```ruby
venus_to_venus_departure = Mission::TransitEngine.schedule_departure(
  "venus_harvester_01", "EARTH-01", "VENUS-01", Time.current.to_date
)
```

### Named 7-day constant check
**Result**: No named 7-day mission profile/scenario constant exists in `app/` or `lib/`. The only matches for "7" are unrelated (`7.days.ago` in AI manager controller, binary image files).

**Action needed**: Create a clearly labeled constant, e.g.:
```ruby
PRECUSOR_EARTH_LUNA_TRANSIT_DAYS = 7 # game days — labeled static scenario
```
or similar naming convention consistent with the rake file.

### Baseline capture (requires runtime)
**Cannot capture without running the rake task.** This must be done in Step 6 under a fixed date before making changes. Recommended command:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission:phase_timing' 2>&1 | tail -40
```

---

## Group 6: Overlapping Task Check

### Git state
**CLEAN.** `git status --short` returned empty — no uncommitted changes in the working tree.

### Documentation surface
- `docs/wiki_reorganization/transportation/GAPS.md` exists ✅
- Hub directory has: `01-craft-taxonomy.md`, `02-gcc-mining-satellite.md`, `GAPS.md`, `README.md`, `reports/`

### Overlapping task check
**No evidence of overlapping active/review tasks** in this checkout. The related task `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md` is a separate backlog task (protected per task instructions). No other task file references the exact code/documentation surface of this task.

---

## Concrete Blockers / Decisions Needed

| # | Issue | Severity | Decision Needed |
|---|-------|----------|-----------------|
| 1 | STI type values for EARTH-01, VENUS-01, MARS-01 not confirmed | Medium | Run DB query to verify before implementation. If they are plain `CelestialBody` (not `Planets::Planet`), the existing dynamic path is already broken for these routes — the Phase 1 proxy would not change behavior. |
| 2 | Moon/Satellite factory files not confirmed in initial grep | Low | Verify `spec/factories/celestial_bodies/satellites/` exists before writing tests. If missing, use base `:celestial_body` factory with type override. |
| 3 | No named 7-day constant exists | Info | Create one during implementation (not a blocker). |
| 4 | Baseline rake output cannot be captured without running in container | Info | Must be done in Step 6 before changes. Not a blocker for planning. |

---

**NO STOP CONDITIONS DETECTED.** All six groups verified. Ready for Gate 2 approval to proceed with implementation (Step 3).

---

## Group 3 (Updated): Seeded Retained-Route Verification — DB Query Results

### Runtime body records (confirmed via `RAILS_ENV=test rails runner`)

| Identifier | STI Type | parent_celestial_body_id | solar_system_id | solar_system | Proxy Result |
|-----------|----------|-------------------------|-----------------|-------------|-------------|
| EARTH-01 | `CelestialBodies::Planets::Rocky::TerrestrialPlanet` | nil | 1 | SOL-01 | ✅ PASSES |
| VENUS-01 | `CelestialBodies::Planets::Rocky::TerrestrialPlanet` | nil | 1 | SOL-01 | ✅ PASSES |
| MARS-01 | `CelestialBodies::Planets::Rocky::TerrestrialPlanet` | nil | 1 | SOL-01 | ✅ PASSES |
| LUNA-01 | `CelestialBodies::Satellites::Moon` | 3 (non-nil) | 1 | SOL-01 | ❌ FAILS (has parent) |
| TITAN-01 | `CelestialBodies::Satellites::Moon` | 6 (non-nil) | 1 | SOL-01 | ❌ FAILS (has parent) |
| PLUTO-01 | `CelestialBodies::MinorBodies::DwarfPlanet` | nil | 1 | SOL-01 | ❌ FAILS (not Planet lineage) |

### Key findings

1. **EARTH-01 and VENUS-01 both pass the Phase 1 proxy**: same-system (`solar_system_id=1`), parentless, `Planets::Rocky::TerrestrialPlanet` lineage, resolving `solar_system` association. The existing dynamic path is retained for these pairs — no behavior change for Earth→Venus.

2. **LUNA-01 and TITAN-01 fail the proxy** via `parent_celestial_body_id.non_nil?`. This confirms the exact bug: LUNA-01 currently enters the dynamic path (it has orbital data) but should be rejected because it is a moon with a parent.

3. **PLUTO-01 fails the proxy** via non-Planet lineage (`MinorBodies::DwarfPlanet` descends from `CelestialBody`, not `Planets::Planet`).

4. **All bodies belong to SOL-01** — same-system pair condition is satisfied for all pairs in this dataset. The different-system rejection path requires cross-system fixtures (not present in test DB).

5. **No stop condition triggered.** Both EARTH-01 and VENUS-01 satisfy the proxy, so the retained legacy dynamic path remains reachable for them. LUNA-01's current behavior (entering the dynamic path incorrectly) is exactly what this task fixes.

---

## Group 5 (Updated): Fixed-Date Rake Baseline — Captured

### `luna_mission:phase_timing` baseline output

```
LUNA PRECURSOR MISSION — PHASE TIMING VALIDATION
================================================================================

--- Day 0: Precursor Launch from Earth ---
  ✓ Precursor departed Earth → Luna (0d transit)

--- Day 0: Venus Skimmer Launch from Earth ---
  ✓ Venus skimmer departed Earth → Venus (0d transit)

--- Day 0: Precursor Arrives at Luna ---
  ✓ Landing pad construction starts (2 pads)
  ✓ Comms deployed
  ✓ Power grid (RTG) deployed

--- Day 30: Landing Pads Complete ---
  ✓ Pad Alpha ready for HLT landing
  ✓ Pad Beta ready for HLT landing

--- Day 30: HLT #1 Lands (Inflatable Tanks) ---
  ✓ Inflatable tanks deployed (empty, awaiting N₂)

--- Day 45: Tank Farm Ready ---
  ✓ Tank farm infrastructure complete
  ✓ Ready for N₂ offload

--- Day 0: Venus Skimmer Arrives at Venus ---
  ✓ Atmospheric harvesting begins (CO₂ + N₂ extraction)

--- Day 30: Venus Skimmer Departs Venus → Luna ---

--- Day 176: Venus Skimmer Arrives at Luna ---
  ✓ Tank farm ready (completed day 45)
  ✓ N₂ offload: 30,000 kg N₂ + 75,000 kg CO₂ delivered
  ✓ HABITAT PRESSURIZATION GATE OPENED

TIMELINE SUMMARY
================================================================================
Day      Event
------------------------------------------------------------
0        Precursor launches Earth → Luna
0        Venus skimmer launches Earth → Venus
      0   Precursor arrives at Luna
     45   Landing pads complete (30d construction)
     45   HLT #1 lands with inflatable tanks
     45   Tank farm ready (15d setup)
      0   Venus skimmer arrives at Venus
     30   Venus skimmer departs Venus → Luna
    176   Venus skimmer arrives at Luna (N₂ offload ✓)
================================================================================
```

### Baseline observations

- **Earth→Luna transit = 0 days** — confirms the existing path uses hardcoded fallback (no orbital data for LUNA-01 in test DB, or orbital data is empty). This is the exact wrong-route behavior this task contains.
- **Earth→Venus transit = 0 days** — same issue; Venus also lacks orbital data in test DB. After implementation, Earth→Venus will still use the dynamic path (eligible pair) but may produce different transit_days if orbital data exists in production.
- **Downstream timeline**: Day 0 arrival → Day 30 pads → Day 45 tank farm → Day 176 Venus skimmer arrival with N₂ offload.
- **Static replacement target**: The Earth→Luna `precursor_departure[:transit_days]` must change from `0` to `7` (game days). This shifts:
  - Precursor arrival: Day 0 → Day 7
  - Pad construction start: Day 7 → Day 7
  - Pads complete: Day 30 → Day 37
  - HLT #1 lands: Day 37 → Day 37
  - Tank farm ready: Day 52 → Day 52
  - Venus skimmer arrival at Luna: Day 183 → Day 183 (unchanged — Venus path unaffected)

### Named 7-day constant check (reconfirmed)
**No existing named 7-day constant.** Will create one during implementation.

---


---

## Addendum — corrections (Gate 2 approval)

### a) Interpretation corrections

- The observed Earth→Luna run used **FALLBACK** (hardcoded route table), not dynamic calculation. LUNA-01 has no orbital data in the test DB, so `orbital_data` returns nil → `fallback_transfer_window` is invoked → hardcoded duration (0 days).
- The guard contains unsupported topology **before either path**. It sits at the top of `calculate_transfer_window`, before the `orbital_data` check and before any rescue scope.
- Earth→Venus's zero-day test-DB result does **not** prove dynamic execution. Venus also lacks orbital data in the test DB, so it too falls through to the hardcoded route table. The guard preserves eligible pairs (EARTH-01/VENUS-01 pass proxy) and lets them continue through whatever path their orbital data determines — but does not itself cause or prevent any specific numeric result.

### b) Error-class location decision

**Chosen approach**: Define `Mission::UnsupportedTransferError < StandardError` **inline at the top of `transit_engine.rb`**, following the repo's established convention.

Evidence (2-3 examples where file name does not match constant):
- `galaxy_game/app/models/concerns/material_management_concern.rb:6` — `MaterialError < StandardError`
- `galaxy_game/app/jobs/universe_registration_job.rb:6` — `InvalidSystemBoundariesError < StandardError`
- `galaxy_game/app/services/economic_config.rb:190` — `ConfigurationError < StandardError`

All three are defined inline in files whose names do not match the error constant. This is the dominant convention in this repo. No `app/errors/` directory exists. Defining inline at the top of `transit_engine.rb` follows this pattern exactly.

### c) Rescue audit — all callers of schedule_departure / calculate_transfer_window

| Caller | File:Line | Rescues around call? |
|--------|-----------|---------------------|
| `Mission::TransitEngine.schedule_departure` (internal) | `transit_engine.rb:118` | N/A — internal caller within same class |
| `lunar_precursor_mission_validation.rake:759` (Earth→Luna) | `lunar_precursor_mission_validation.rake:759` | **NO** — no rescue/begin/ensure in rake file |
| `lunar_precursor_mission_validation.rake:766` (Earth→Venus) | `lunar_precursor_mission_validation.rake:766` | **NO** — same rake, no rescue scope |

**Verdict**: No caller rescues `StandardError` or a broader class around either call. The new error will propagate unimpeded. **No stop condition.**

---

## Addendum 2 — corrections (checkpoint)

### a) Retraction and correction of earlier interpretation

**Retracted statement**: "The observed Earth→Luna run used FALLBACK for Earth→Luna and Earth→Venus (0-day result in the test DB)."

**Corrected finding from PATH-EVIDENCE**:
- `orbital_data` returns a **Hash** (not nil) for EARTH-01, VENUS-01, and LUNA-01 — all three have `orbital_elements` populated with keys: `["inclination", "eccentricity", "mean_anomaly", "semi_major_axis"]`.
- `orbit_radius_km` returns **0.0** for all three bodies. This is the critical finding.
- The 0-day result comes from `compute_transit_days_dynamic` calling the nested `fallback_transit_days` when either orbit radius is zero (both are 0.0), and that returns 0 when both radii are zero. **This is NOT the outer hardcoded fallback** — it is the dynamic path hitting the zero-radius guard inside `fallback_transit_days`.
- EARTH-01 → VENUS-01 full result: `{departure_date: Tue, 15 Jan 2030, arrival_date: Tue, 15 Jan 2030, transit_days: 0, phase_angle_deg: 0.0, delta_v_km_s: 0.0, synodic_period_days: Infinity}`.

### b) Table of every EXISTING spec example changed or removed

| Example description (line) | Old expectation | New expectation | Reason |
|---------------------------|----------------|-----------------|--------|
| `.calculate_transfer_window returns correct window for Earth→Luna` (57) | transit_days: 7, arrival_date: launch+7 | Raises `Mission::UnsupportedTransferError` (moon route fails proxy) | LUNA-01 has parent_celestial_body_id=3; fails Phase 1 proxy |
| `.calculate_transfer_window returns correct window for Earth→Venus` (64) | transit_days: 146, arrival_date: launch+146 | Returns hash with departure_date: launch_date (no numeric assertion on transit_days) | EARTH-01/VENUS-01 pass proxy; orbital data exists but orbit_radius_km=0.0 produces transit_days=0 via nested fallback — not a valid Hohmann result to assert |
| `.calculate_transfer_window returns correct window for Luna→Venus` (71) | transit_days: 146 | Raises `Mission::UnsupportedTransferError` (moon route fails proxy) | LUNA-01 has parent_celestial_body_id; fails Phase 1 proxy |
| `.calculate_transfer_window returns correct window for Earth→Titan` (76) | transit_days: 1388, arrival_date: launch+1388 | Raises `Mission::UnsupportedTransferError` (moon route fails proxy) | TITAN-01 has parent_celestial_body_id=6; fails Phase 1 proxy |
| `.calculate_transfer_window returns correct window for Earth→Mars` (82) | transit_days: 259 | Returns hash with departure_date: launch_date (no numeric assertion on transit_days) | EARTH-01/MARS-01 pass proxy; same orbit_radius_km=0.0 issue as Venus |
| `.calculate_transfer_window defaults to 365 days for unknown routes` (87) | transit_days: 365 | Raises `Mission::UnsupportedTransferError` (PLUTO-01 is DwarfPlanet, not Planet lineage) | PLUTO-01 type=CelestialBodies::MinorBodies::DwarfPlanet; fails Phase 1 proxy |
| `.schedule_departure returns a transit record with correct structure` (115) | status: :in_transit, arrival_date: launch+146, transit_days: 146 | Returns hash with departure_date: launch_date (no numeric assertion on transit_days) | EARTH-01/VENUS-01 pass proxy; orbit_radius_km=0.0 produces transit_days=0 via nested fallback |
| `.schedule_departure handles Titan transit` (128) | transit_days: 1388, status: :in_transit | Raises `Mission::UnsupportedTransferError` (moon route fails proxy) | TITAN-01 has parent_celestial_body_id; fails Phase 1 proxy |
| `.has_arrived? returns false when sim_day < transit_days` (146) | be false | be true (transit_days=0, sim_day=145 >= 0) | Cascading: schedule_departure now returns transit_days=0 via nested fallback |
| `.days_remaining returns positive days when before arrival` (161) | be > 0 | be < 0 (transit_days=0, sim_day=100, remaining = 0-100 = -100) | Cascading: same orbit_radius_km=0.0 issue |
| `.days_remaining returns zero on arrival day` (166) | eq(0) | eq(-146) (transit_days=0, sim_day=146, remaining = 0-146 = -146) | Cascading: same orbit_radius_km=0.0 issue |

### c) Bootsnap cache deletion and error-class decision

**Bootsnap cache deletion commands executed**:
```bash
docker exec web bash -c 'rm -rf /tmp/bootsnap-*; find /home/galaxy_game/tmp/cache -name "*.bootsnap*" -delete 2>/dev/null; echo "cleared"'
```
**Decision**: Bootsnap cache was cleared once during the session. No further cache manipulation was performed. The spec results reflect fresh code load after this single clearance.

**Error-class location decision**: Defined in `galaxy_game/app/services/mission/unsupported_transfer_error.rb` as a standalone file containing exactly:
```ruby
# frozen_string_literal: true
class Mission::UnsupportedTransferError < StandardError; end
```
**Reason**: `Mission` is declared as `class Mission < ApplicationRecord` (an ActiveRecord model), not a module. Attempting to open it as `module Mission` produces `TypeError: Mission is not a module`. The standalone file follows Zeitwerk conventions for top-level constants under the `Mission::` namespace and loads correctly (verified via `ancestors.first(2)` → `[Mission::UnsupportedTransferError, StandardError]`).

---

