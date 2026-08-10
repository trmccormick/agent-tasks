# Synthesis: Spatial Boundary Validation for UniverseRegistrationJob

**Date**: 2026-08-10
**Task**: 2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md

## Analysis

### Current State
- `UniverseRegistrationJob` does NOT exist — must be created from scratch
- Seed format uses **string keys** (confirmed in `procedural_generator.rb` line 85+)
- Planets stored in: `system_seed["celestial_bodies"]["terrestrial_planets"]` with `"orbits"` → `[{"semi_major_axis_au" => ...}]`
- No wormholes field in current seed format — validation must handle nil/missing gracefully

### Constants (game_constants.rb)
- `SAFE_DISTANCE_FROM_STAR = 1.496e8` (1 AU in meters)
- `MAX_DISTANCE_FROM_STAR = 1.496e10` (100 AU in meters)
- `MAX_WORMHOLES_PER_SYSTEM = 3`

### Implementation Plan
1. Create `app/jobs/universe_registration_job.rb` with:
   - `InvalidSystemBoundariesError` exception class
   - `validate_system_envelope!(system_seed)` method handling both simplified (`:planets`) and full seed (`"celestial_bodies"`) formats
   - AU → meters conversion: `au * 1.496e11`
   - Wormhole count validation (nil-safe)
2. Create `spec/jobs/universe_registration_job_spec.rb` with 5 test cases
3. Run specs via Docker

### Risks
- Seed format may vary between JSON-path and procedural AR-path systems
- Task file spec uses symbol keys but actual seed uses string keys — implementation must handle both
