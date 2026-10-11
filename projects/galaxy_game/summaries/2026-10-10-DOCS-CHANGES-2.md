# Docs Changes — 2026-10-10 (C2 Session)

## git status --short

```
 M docs/wiki_reorganization/transportation/GAPS.md
 M docs/wiki_reorganization/transportation/README.md
```

## git diff (full)

### GAPS.md

```diff
--- a/docs/wiki_reorganization/transportation/GAPS.md
+++ b/docs/wiki_reorganization/transportation/GAPS.md
@@ -54,32 +54,30 @@
 
 ## Gap D: TransitEngine Topology Containment — Phase 1 Verified Gaps (UNRESOLVED)
 
-**Description**: The TransitEngine `calculate_transfer_window` method includes a Phase 1 topology containment proxy guard, but multiple gaps remain between the guard's intent and its actual behavior. All items below are OBSERVED from source code inspection; none are claims of physical correctness or recommendations to fix.
+**Description**: `Mission::TransitEngine.calculate_transfer_window` includes a Phase 1 topology containment proxy guard. Several gaps remain between that guard and a correct route-planning system. Items 1–5 are residual limits of the Phase 1 design; items 6–8 are observed pre-existing behavior that this work did not fix. Nothing here is a claim of physical correctness.
 
-**Evidence**:
-- `galaxy_game/app/services/mission/transit_engine.rb` (uncommitted working tree):
-  - Lines 52–74: Phase 1 proxy guard accepts only `CelestialBodies::Planets::Planet` instances with `parent_celestial_body_id.nil?` and `solar_system_id.present?`; raises `Mission::UnsupportedTransferError` for anything else.
-  - Lines 67, 74: Two `raise Mission::UnsupportedTransferError` calls (guard count = 2).
-  - Lines 458–465: Legacy hardcoded constants (`earth_to_venus_transit_days`, `luna_to_venus_transit_days`, etc.) remain unguarded and directly callable.
-  - Line 53: `launch_date` defaults to `Time.current.to_date`.
-  - Line 506: `orbit_radius_km` reads `orbitals[:semi_major_axis]` (symbol key); `orbital_data` (line 217) normalizes keys to snake_case strings (`"semi_major_axis"`), so the symbol lookup yields `nil` → `to_f` → `0.0`.
-  - Lines 416–439: `fallback_transit_days` returns `0` when both `r1.zero? && r2.zero?`, causing eligible routes to return `transit_days = 0` via the nested fallback.
-- `galaxy_game/app/services/mission/unsupported_transfer_error.rb` (untracked): `class Mission::UnsupportedTransferError < StandardError; end` — 242 bytes.
-- `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` (modified, uncommitted): Earth→Luna uses explicitly labeled "static scenario" with 7-game-day duration; TIMELINE SUMMARY shows Landing Pads Complete and Tank Farm Ready on different days in body output vs. summary table (pre-existing).
-- Prior session spec run (`$SUM/2026-10-06-SPECS-FINAL.txt`): 51 total examples, 45 passed, 6 failed — all caused by `orbit_radius_km` symbol-key mismatch yielding `transit_days = 0`.
+**Evidence** (paths under `galaxy_game/`):
+- `app/services/mission/transit_engine.rb`: the guard accepts only `CelestialBodies::Planets::Planet` bodies with `parent_celestial_body_id.nil?`, a present `solar_system_id`, and a resolving `solar_system`; when both endpoints resolve they must share a `solar_system_id`. Otherwise it raises `Mission::UnsupportedTransferError` (one raise for a failing resolved endpoint, one for a different-system pair). Unresolvable identifiers keep the legacy fallback when every resolved endpoint passes.
+- `app/services/mission/unsupported_transfer_error.rb`: `class Mission::UnsupportedTransferError < StandardError; end`.
+- Legacy route-table helpers (`earth_to_venus_transit_days`, `luna_to_venus_transit_days`, etc.) remain unguarded and directly callable.
+- `calculate_transfer_window` defaults `launch_date` to `Time.current.to_date`.
+- `orbit_radius_km` reads `orbitals[:semi_major_axis]` (symbol key) while `orbital_data` returns string keys. Read-only check for EARTH-01 in the test DB: string key = 149597870700.0, symbol key = nil, `orbit_radius_km` = 0.0. `fallback_transit_days` returns 0 when both radii are zero. Not yet checked outside the test DB.
+- `lib/tasks/lunar_precursor_mission_validation.rake`: the `luna_mission:phase_timing` Earth→Luna precursor uses an explicitly labeled static 7-game-day duration; Earth→Venus remains on the engine path.
+- Spec run of `spec/services/mission/transit_engine_spec.rb` after this change: 51 examples, 45 passed, 6 failed (see item 7).
 
-**Gaps Recorded (observed, not fixed)**:
+**Gaps Recorded**:
 1. No governing-primary or reference-frame association for any resolved body.
-2. No per-primary gravitational parameter (`mu`); `MU_SUN` applies to every pair that passes the proxy.
+2. No per-primary gravitational parameter (`mu`); fixed `MU_SUN` applies to every pair that passes the proxy.
 3. Direct legacy helpers remain unguarded, including `luna_to_venus_transit_days`.
 4. `calculate_transfer_window` keeps its `Time.current.to_date` default.
-5. Same-system eligibility is a topology containment check only — not multi-star/non-Sol/generated-system/physical correctness.
-6. `orbit_radius_km` reads `orbitals[:semi_major_axis]` (symbol key) while `orbital_data` returns string keys; seeded test bodies yield 0.0 radius → eligible routes return `transit_days = 0` via nested fallback. The actual `semi_major_axis` value of seeded bodies was NOT confirmed.
-7. Six pre-existing failing examples in `transit_engine_spec.rb` (Earth→Venus, Earth→Mars, `schedule_departure` structure, `has_arrived?`, two `days_remaining`) caused by gap 6.
+5. Same-system eligibility is a topology containment check only; it does not establish multi-star, non-Sol, generated-system, or physical transfer correctness.
+6. (Observed) `orbit_radius_km` reads a symbol key from a string-keyed hash, yielding a 0.0 radius for the checked seeded body; eligible routes then return `transit_days = 0` via the nested fallback. Confirmed for EARTH-01 in the test DB only.
+7. (Observed) Six pre-existing failing examples in `transit_engine_spec.rb` (Earth→Venus, Earth→Mars, `schedule_departure` structure, `has_arrived?`, two `days_remaining`) are caused by item 6.
+8. (Observed) `luna_mission:phase_timing` prints "Landing Pads Complete" and "Tank Farm Ready" on different days in its body output than in its TIMELINE SUMMARY (pre-existing).
 
-**Impact**: The Phase 1 proxy is temporary containment, not a route-planning system. No physical correctness claims are made or implied. Future work needs reference-frame metadata, central-body association, per-body `mu`, parent/SOI transitions, and multi-leg routing — all human-filed follow-up work, not something the implementer creates or dispatches.
+**Impact**: The Phase 1 proxy is temporary containment, not a route-planning system. Future work needs reference-frame metadata, central-body association, per-body `mu`, parent/SOI transitions, and multi-leg routing; this is human-filed follow-up work, not something the implementer creates or dispatches.
 
-**Backlog Coverage**: None — no active or backlog task addresses these gaps. Human-filed follow-up items only.
+**Backlog Coverage**: None. No active or backlog task addresses these gaps.
```

### README.md

```diff
--- a/docs/wiki_reorganization/transportation/README.md
+++ b/docs/wiki_reorganization/transportation/README.md
@@ -65,14 +65,12 @@
 
 ## TransitEngine Topology Containment — Phase 1 Notes
 
-**Location**: See [GAPS.md — Gap D](./GAPS#gap-d-transitengine-topology-containment) for full gap tracking.
-
-The `Mission::TransitEngine` class includes a Phase 1 topology containment proxy guard in `calculate_transfer_window` (lines 52–74 of the uncommitted working tree). This guard:
-
-- Accepts only `CelestialBodies::Planets::Planet` instances with no parent and a present solar system
-- Raises `Mission::UnsupportedTransferError` for moons, satellites, dwarf/minor bodies, or cross-system pairs
-- Makes NO claim of physical correctness, governing primary, reference frame, or Hohmann feasibility
-
-The proxy is temporary containment only. It does not constitute a route-planning system. Future work requires reference-frame metadata, central-body association, per-body `mu`, parent/SOI transitions, and multi-leg routing — all human-filed follow-up items.
+**Gap tracking**: see [GAPS.md — Gap D](./GAPS.md#gap-d-transitengine-topology-containment--phase-1-verified-gaps-unresolved).
+
+- The retained legacy dynamic path in `Mission::TransitEngine.calculate_transfer_window` is reachable only for the same-system, parentless planet-lineage topology proxy; passing the proxy is not a claim of support or physical correctness.
+- Moon/satellite, parented, non-planet, dwarf/minor-body, and otherwise nonconforming resolved bodies raise `Mission::UnsupportedTransferError` in this path. Unresolvable identifiers keep the legacy fallback when every resolved endpoint passes.
+- The precursor Earth→Luna scenario in `luna_mission:phase_timing` uses an explicitly labeled static 7-game-day duration. Earth→Venus remains on the engine path.
+- The proxy is temporary containment, not a route-planning system. Future work (reference-frame metadata, central-body association, per-body `mu`, parent/SOI transitions, multi-leg routing) is human-filed follow-up work, not something the implementer creates or dispatches.
```
