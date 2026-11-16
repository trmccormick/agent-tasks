# Synthesis Report: Launch Window + Transit Timing Engine

**Task**: 2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md
**Date**: 2026-09-10
**Status**: Ready for Implementation

---

## 1. What Exists Today (Findings)

### Mission Profile (`data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json`)
- **7 phases**: gcc_mining → initial_hlt_landings → power_grid_deployment → titan_delivery → venus_delivery → luna_isru_production → l1_leo_supply
- **Transit days already embedded**: titan=730d, venus=400d (in `runtime_parameters.transit_days`)
- **No launch_window objects** — phases have `duration_days` but no departure/arrival bodies or synodic periods
- **No Venus harvest arrival task reference** — the profile has a `venus_delivery` phase but no `task_ref` pointing to a v2 task file
- **Success conditions** include `n2_delivered_to_luna`, `ch4_delivered_to_luna`, `co2_delivered_to_luna`

### Manifest (`data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json`)
- Hardware definitions for each phase (GCC satellite, HLT landings, RTG/solar arrays)
- No transit timing data — only cargo lists and output flags

### Rake Tasks (`galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake`)
- `phase1_bootstrap`: Validates GCC mining → HLT landings → power grid (structure-only validation)
- `phase2_interplanetary`: Validates Titan/Venus delivery → ISRU → L1/LEO supply chain
- Both rake tasks use `load_profile` / `load_manifest` helpers that read from `GalaxyGame::Paths::MISSIONS_V2_*`
- **No phase_timing task exists** — zero timing simulation

### Existing Task Files (`data/json-data/missions_v2/tasks/`)
- 17 v2 task files exist (site prep, tank deployment, ISRU equipment, etc.)
- **No Venus/Titan harvest arrival task files** in missions_v2/tasks/
- `task_cycler_transit_preparation.json` does NOT exist in missions_v2/tasks/ — the task file referenced in the dispatch text is stale

### Service Classes (`galaxy_game/app/services/`)
- No `mission/` subdirectory exists under services/
- No TransitEngine class exists anywhere
- Existing mission-related services: `mission_contract_service.rb`, `mission_generator_service.rb`, `mission_task_runner_service.rb`

### Old Mission Data (`data/json-data/missions/`)
- `venus_settlement/` and `titan-resource-hub/` directories exist with old-format data
- These are NOT in v2 format and should not be adapted directly — new v2 files should be created fresh

---

## 2. Understanding of the Critical Path

```
Day 0:     Precursor launches Earth → Luna (146d transit)
Day 0:     Venus skimmer launches Earth → Venus (146d transit)
Day 146:   Precursor arrives at Luna
Day 146:   Landing pad construction starts (30d)
Day 176:   Landing pads complete
Day 176:   HLT #1 lands with inflatable tanks (empty)
Day 176:   Tank farm setup starts (15d)
Day 191:   Tank farm ready → gates N₂ offload
Day 146:   Venus skimmer arrives at Venus, begins harvest (30d)
Day 176:   Venus skimmer departs Venus → Luna (400d transit from Venus)
Day 576:   Venus skimmer arrives at Luna with N₂/CO₂
           ✓ Tank farm ready (day 191) < arrival (day 576) → N₂ offload OK
Day 666:   Titan skimmer launches Earth → Titan (730d transit)
Day 1396:  Titan arrives at Titan, harvests CH₄ (30d)
Day 1426:  Titan departs Titan → Luna (730d transit)
Day 2156:  Titan arrives at Luna with CH₄
```

**Key insight**: The Venus skimmer's total timeline is ~576 days from launch to Luna arrival (146d Earth→Venus + 30d harvest + 400d Venus→Luna). Tank farm completes at day 191, so there's a comfortable 385-day buffer before N₂ offload.

**Critical constraint**: If tank farm were NOT ready when Venus skimmer arrives, the delivery would be delayed (not lost) — this is the hard gate that must be enforced.

---

## 3. Files to Create/Modify

### New Files (4)
| # | File | Purpose |
|---|------|---------|
| 1 | `galaxy_game/app/services/mission/transit_engine.rb` | TransitEngine service class with transfer window + departure/arrival methods |
| 2 | `spec/services/mission/transit_engine_spec.rb` | RSpec tests for TransitEngine |
| 3 | `data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json` | Venus harvest arrival task (v2 format) |
| 4 | `data/json-data/missions_v2/tasks/task_titan_harvest_arrival_v2.json` | Titan harvest arrival task (v2 format) |

### Modified Files (1)
| # | File | Change |
|---|------|--------|
| 1 | `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` | Add `phase_timing` rake task |
| 2 | `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` | Add `venus_harvest_arrival` phase with launch_window + task_ref |

---

## 4. Verification Plan

### Unit Tests (RSpec)
- Run: `docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/mission/transit_engine_spec.rb'`
- Verify: TransitEngine class methods return correct values for all transfer routes
- Verify: `has_arrived?` correctly evaluates past/future arrival dates

### Integration Test (Rake)
- Run: `docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission:phase_timing'`
- Expected output: Full timeline prints with all phases, no aborts
- Verify: Tank farm ready check passes (day 191 < day 576)

### Regression Test
- Run existing mission pipeline specs to ensure no regressions
- `docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/lib/tasks/lunar_precursor_mission_validation_spec.rb'` (if exists)

---

## 5. Transit Engine vs Live Game Loop

**The transit engine is NOT connected to the live game loop.** Per the Live Game-Loop Reality Check findings:
- Craft inherit from `ApplicationRecord`, NOT `Units::BaseUnit`
- They are excluded from `Game#process_units`
- `GameSimulationJob` calls `game.advance_by_days` which processes settlements/units/planets only
- The transit engine must be explicitly invoked by rake tasks or AIManager services

**Implementation approach**: TransitEngine is a pure computation service (stateless class methods returning hashes). It has no persistence layer, no model, no migration. State management (tracking which crafts are in transit) is the responsibility of the caller (rake task or AIManager service).

---

## 6. Implementation Order

1. Create `Mission::TransitEngine` service class
2. Create RSpec spec file for TransitEngine
3. Create Venus harvest arrival task JSON
4. Create Titan harvest arrival task JSON
5. Add `phase_timing` rake task to lunar_precursor_mission_validation.rake
6. Add Venus harvest arrival phase to precursor_mission_profile_v1.json
7. Run specs + rake validation
8. Save synthesis report (this file)

---

## 7. Architecture Decisions Made

- **TransitEngine is stateless**: Returns hashes, not ActiveRecords. No persistence needed for MVP.
- **Simplified transfer windows**: Uses fixed Hohmann transfer days (146 Earth→Venus, 730 Earth→Titan). Real orbital mechanics deferred to Phase 5 (Orbital Mechanics Data Layer).
- **Tank farm gate is a check, not a model**: The rake task validates tank_farm_ready before N₂ offload. No new model needed — this is a business rule in the service layer.
- **Venus skimmer departs Earth**: Not Luna. Loaded with methane fuel at Earth launch, goes to Venus for harvest, then to Luna.
