=== git status --short ===
 M docs/wiki_reorganization/transportation/GAPS.md
 M docs/wiki_reorganization/transportation/README.md
 M galaxy_game/app/services/mission/transit_engine.rb
 M galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake
 M galaxy_game/spec/services/mission/transit_engine_spec.rb
?? galaxy_game/app/services/mission/unsupported_transfer_error.rb
?? qwen-session-closout-log.md

=== git diff docs/wiki_reorganization/transportation/ ===
diff --git a/docs/wiki_reorganization/transportation/GAPS.md b/docs/wiki_reorganization/transportation/GAPS.md
index 51cf83a0..44abe83e 100644
--- a/docs/wiki_reorganization/transportation/GAPS.md
+++ b/docs/wiki_reorganization/transportation/GAPS.md
@@ -49,3 +49,35 @@
 **Impact**: Craft lifecycle behavior (maintenance, repair, decommissioning) cannot be documented as implemented gameplay. Any future documentation must await source evidence before representing these as operational systems.
 
 **Backlog Coverage**: None — no active or backlog task addresses craft lifecycle implementation or verification.
+
+---
+
+## Gap D: TransitEngine Topology Containment — Phase 1 Verified Gaps (UNRESOLVED)
+
+**Description**: The TransitEngine `calculate_transfer_window` method includes a Phase 1 topology containment proxy guard, but multiple gaps remain between the guard's intent and its actual behavior. All items below are OBSERVED from source code inspection; none are claims of physical correctness or recommendations to fix.
+
+**Evidence**:
+- `galaxy_game/app/services/mission/transit_engine.rb` (uncommitted working tree):
+  - Lines 52–74: Phase 1 proxy guard accepts only `CelestialBodies::Planets::Planet` instances with `parent_celestial_body_id.nil?` and `solar_system_id.present?`; raises `Mission::UnsupportedTransferError` for anything else.
+  - Lines 67, 74: Two `raise Mission::UnsupportedTransferError` calls (guard count = 2).
+  - Lines 458–465: Legacy hardcoded constants (`earth_to_venus_transit_days`, `luna_to_venus_transit_days`, etc.) remain unguarded and directly callable.
+  - Line 53: `launch_date` defaults to `Time.current.to_date`.
+  - Line 506: `orbit_radius_km` reads `orbitals[:semi_major_axis]` (symbol key); `orbital_data` (line 217) normalizes keys to snake_case strings (`"semi_major_axis"`), so the symbol lookup yields `nil` → `to_f` → `0.0`.
+  - Lines 416–439: `fallback_transit_days` returns `0` when both `r1.zero? && r2.zero?`, causing eligible routes to return `transit_days = 0` via the nested fallback.
+- `galaxy_game/app/services/mission/unsupported_transfer_error.rb` (untracked): `class Mission::UnsupportedTransferError < StandardError; end` — 242 bytes.
+- `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` (modified, uncommitted): Earth→Luna uses explicitly labeled "static scenario" with 7-game-day duration; TIMELINE SUMMARY shows Landing Pads Complete and Tank Farm Ready on different days in body output vs. summary table (pre-existing).
+- Prior session spec run (`$SUM/2026-10-06-SPECS-FINAL.txt`): 51 total examples, 45 passed, 6 failed — all caused by `orbit_radius_km` symbol-key mismatch yielding `transit_days = 0`.
+
+**Gaps Recorded (observed, not fixed)**:
+1. No governing-primary or reference-frame association for any resolved body.
+2. No per-primary gravitational parameter (`mu`); `MU_SUN` applies to every pair that passes the proxy.
+3. Direct legacy helpers remain unguarded, including `luna_to_venus_transit_days`.
+4. `calculate_transfer_window` keeps its `Time.current.to_date` default.
+5. Same-system eligibility is a topology containment check only — not multi-star/non-Sol/generated-system/physical correctness.
+6. `orbit_radius_km` reads `orbitals[:semi_major_axis]` (symbol key) while `orbital_data` returns string keys; seeded test bodies yield 0.0 radius → eligible routes return `transit_days = 0` via nested fallback. The actual `semi_major_axis` value of seeded bodies was NOT confirmed.
+7. Six pre-existing failing examples in `transit_engine_spec.rb` (Earth→Venus, Earth→Mars, `schedule_departure` structure, `has_arrived?`, two `days_remaining`) caused by gap 6.
+8. `luna_mission:phase_timing` prints "Landing Pads Complete" and "Tank Farm Ready" on different days in its body output than in its TIMELINE SUMMARY (pre-existing).
+
+**Impact**: The Phase 1 proxy is temporary containment, not a route-planning system. No physical correctness claims are made or implied. Future work needs reference-frame metadata, central-body association, per-body `mu`, parent/SOI transitions, and multi-leg routing — all human-filed follow-up work, not something the implementer creates or dispatches.
+
+**Backlog Coverage**: None — no active or backlog task addresses these gaps. Human-filed follow-up items only.
diff --git a/docs/wiki_reorganization/transportation/README.md b/docs/wiki_reorganization/transportation/README.md
index 8b70b256..e8ade180 100644
--- a/docs/wiki_reorganization/transportation/README.md
+++ b/docs/wiki_reorganization/transportation/README.md
@@ -60,3 +60,17 @@ This hub organizes all craft, satellite, and logistics documentation for Galaxy
 
 - **2026-09-15**: Created hub page; linked craft taxonomy and GCC mining satellite entries (Phase 4).
 - **2026-09-15**: Normalized to Economy hub pattern: architecture overview, doc map grouped by purpose, Key Models & Services table, cross-domain references (Transportation normalization).
+
+---
+
+## TransitEngine Topology Containment — Phase 1 Notes
+
+**Location**: See [GAPS.md — Gap D](./GAPS#gap-d-transitengine-topology-containment) for full gap tracking.
+
+The `Mission::TransitEngine` class includes a Phase 1 topology containment proxy guard in `calculate_transfer_window` (lines 52–74 of the uncommitted working tree). This guard:
+
+- Accepts only `CelestialBodies::Planets::Planet` instances with no parent and a present solar system
+- Raises `Mission::UnsupportedTransferError` for moons, satellites, dwarf/minor bodies, or cross-system pairs
+- Makes NO claim of physical correctness, governing primary, reference frame, or Hohmann feasibility
+
+The proxy is temporary containment only. It does not constitute a route-planning system. Future work requires reference-frame metadata, central-body association, per-body `mu`, parent/SOI transitions, and multi-leg routing — all human-filed follow-up items.
