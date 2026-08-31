# FINDINGS: Live Game-Loop Reality Check

**Task**: 2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md
**Agent**: Qwen local via Copilot (Implementation Agent)
**Date**: 2026-08-30
**Type**: Research / architecture (read-only, no code changes)

---

## Executive Summary

A real live game loop **DOES exist** in the codebase — but it only processes settlements, `Units::BaseUnit` instances, and celestial bodies. It does **NOT** process craft (including Luna precursor, GCC mining satellite, or Venus skimmer), nor does it invoke the AIManager orchestrator. All three specific assets remain driven exclusively by bespoke rake tasks and AIManager services.

---

## Q1: Does `Game#advance_by_days` have real side effects?

**YES — it has real asset-processing side effects.**

`galaxy_game/app/models/game.rb:42-57`:
```ruby
def advance_by_days(days)
  return if days <= 0
  @elapsed_time += days
  @game_state.update_time! if @game_state
  process_settlements(days)   # line 52
  process_units(days)         # line 53
  process_planets(days)       # line 54
end
```

`process_settlements` (line 62-74): iterates `Settlement::BaseSettlement.all`, calls `unit.consume_resources(time_skipped)` on each unit, and processes construction/component production jobs.

`process_units` (line 104-109): iterates `Units::BaseUnit.all` and calls `unit.operate(time_skipped)`.

`process_planets` (line 112-118): iterates `CelestialBodies::CelestialBody.all` and calls `PlanetUpdateService.new(planet, time_skipped).run`.

**Verdict**: `advance_by_days` is NOT just a counter — it actively processes settlements, units, and planets.

---

## Q2: Is there a scheduled job/worker ticking assets independently of rake?

**YES — a real Sidekiq-based live loop exists.**

`galaxy_game/app/jobs/game_simulation_job.rb`:
- Line 8: `include Sidekiq::Job`
- Line 15: gated on `game_state.running`
- Line 25: calls `game.advance_by_days(days_to_simulate)` where `days_to_simulate` is calculated from real elapsed time / game speed ratio (line 18-19)
- Line 40: self-schedules via `GameSimulationJob.perform_in(1.minute)`

Bootstrap: `galaxy_game/config/initializers/game_background.rb:2-6`:
```ruby
Rails.application.config.after_initialize do
  unless Rails.env.test? || defined?(Rails::Console)
    GameSimulationJob.perform_in(10.seconds)
  end
end
```

`GameState` model (`game_state.rb`): `running` defaults to `false` (line 17), so the loop is **off by default** — it must be toggled on via `toggle_running!`. Speed controls game-time ratio (5 levels, line 49-58).

`config/schedule.rb`: All game-tick crons are commented out (lines 9-23). Only maintenance tasks remain active.

**Verdict**: A real live loop exists and is self-scheduling via Sidekiq, but it's **off by default**.

---

## Q3: Is craft transit modeled as a time-advancing state machine?

**NO — craft transit is NOT modeled as a time-advancing state machine.**

Evidence:
- `Craft::BaseCraft` (line 3 of `base_craft.rb`) inherits from `ApplicationRecord`, **NOT** `Units::BaseUnit`.
- `Cycler` (line 4 of `cycler.rb`) also inherits from `ApplicationRecord`.
- `Game#process_units` (line 106) iterates only `Units::BaseUnit.all` — craft are excluded.
- `Cycler` stores static orbital periods (lines 41-52) and scheduled arrivals/departures (lines 14-15), but no transit state machine advancing with game time.
- `ScheduledTrip#next_destination` (`scheduled_trip.rb:13-16`) is an unimplemented stub.
- `DockedCraftTrip` only stores docking/undocking datetimes — no transit logic.

**Verdict**: Craft arrival is an instantaneous state flip in rake/test scripts, not a real advancing state machine.

---

## Q4: Are the three specific assets wired into the live loop?

**NO — all three are driven exclusively by bespoke rake/AIManager paths.**

Grep of `game.rb`, `game_simulation_job.rb`, `game_service.rb`, `game_simulation.rb` for AIManager/precursor/gcc/skimmer → **ZERO matches**.

### Luna Precursor Craft
- Driven by: `galaxy_game/lib/tasks/luna_mission.rake` (`luna_mission:execute`) via `TaskExecutionEngineV2`
- Services: `AIManager::PrecursorCapabilityService`, `AIManager::MissionProfileAnalyzer`
- NOT wired into live loop

### GCC Mining Satellite
- Driven by: `galaxy_game/lib/tasks/gcc_mining_sat.rake` (line 227 calls `game.advance_by_days(1)` directly)
- Job: `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb` (exists but separate from live loop)
- NOT wired into live loop

### Venus Skimmer
- Driven by: AIManager services (`AIManager::AtmosphericHarvesterService`, `AIManager::AtmosphericExtractionService`)
- Orchestrated via: `AIManager::SkimmerCyclerHandshakeService` (line 30)
- NOT wired into live loop

**Verdict**: All three assets behave correctly only when a bespoke script drives them directly. None are wired into the live game loop.

---

## Q5: Does PSR ice mining and deposit spawning on Luna exist and function?

**PARTIAL — data model + analysis exist, but no functioning live extraction loop.**

### What EXISTS:
- `crater.rb:31-37`: `has_ice?`, `ice_concentration` methods
- `crater.rb:43-45`: `permanently_shadowed?` method
- `crater.rb:79-85`: `water_ice_tons`, `accessible_ice_tons` data accessors
- `crater_generator.rb:39`: Spawns PSR craters with 30% probability
- `hydrosphere_analyzer.rb:224,299`: Identifies `:ice_mining` collection sites on arid planets
- `cryosphere.rb:40,71`: Ice composition determination for celestial bodies

### What DOES NOT EXIST:
- No live-tick ice extraction consuming PSR deposits
- No `ice_mining` unit type in the active `Units::BaseUnit` hierarchy (only referenced as a pattern string in `pattern_validator.rb:89`)
- `escalation_service.rb:226`: References `'task_type' => 'ice_extraction'` but this is an AIManager escalation task, not a live loop mechanism

**Verdict**: PSR ice mining data model and analysis infrastructure exist. No functioning live extraction loop exists.

---

## Q6: Does the Inventory/storage model distinguish raw vs processed gas categories?

**PARTIAL — the distinction exists only as a unit-level production pattern for Luna regolith, NOT as a general Inventory category, and NOT implemented for the Venus skimmer.**

### Precedent (Luna regolith case):
- `gas_separator_unit_bp.json`: Takes `mixed_volatiles` (raw) → separated O2/H2O/CO2/H2/CH4/N2 (processed). This is a **unit blueprint** pattern, not an Inventory model feature.

### Inventory model:
- `inventory.rb:147-149`: Distinguishes material TYPE (`liquid`, `gas`, `fuel`) — NOT raw vs processed.
- `inventory.rb:224-230`: Same type distinction for item type determination (`specialized` vs `bulk`).
- No `raw_gas_category` or `processed_gas_category` field anywhere in the Inventory model.

### Venus skimmer:
- `atmospheric_harvester_service.rb`: Treats skimmer gases as flat quantities (methane produced, nitrogen collected) with no raw/processed split.
- `orbital_em_skimmer_mid_bp.json`: Lists compatible fuel tanks (`lox_tank`, `methane_tank`) but no operational_data structure distinguishing raw atmospheric intake from processed output.

**Verdict**: The raw-vs-processed distinction exists only as a unit blueprint pattern for the Luna gas separator (TEU/PVE). It is NOT a general Inventory/storage feature and is NOT implemented for the Venus skimmer's tank `operational_data`.

---

## Architecture Gotchas Verified

### ✅ Gotcha 1: luna_operations_simulation.rake is NOT the live game loop
Confirmed: `LunaOperationsSimulationService` is a standalone daily-tick simulation service (line 2-7 of its file). It is called only by its rake task, not by the live game loop. The real live loop is `GameSimulationJob`.

### ✅ Gotcha 2: No prior "this works" claims trusted
Every finding above confirmed live against current code with file:line references.

### ✅ Gotcha 3: Venus skimmer operational sequence is multi-state
The distinct sub-states are: (1) arrival at Venus, (2) transferring carried-in methane from transit tank to main tank, (3) atmospheric intake (Venus atmosphere ~96.5% CO2, ~3.5% N2 per sol-complete.json), (4) onboard CO2→CO+O2 processing (O2 used as LOX for return trip), (5) departure-readiness. None of these are modeled as a state machine in the live loop.

---

## Key Architectural Finding

The codebase has **two parallel simulation systems**:

1. **Live Game Loop** (`GameSimulationJob` → `Game#advance_by_days`): Processes settlements, `Units::BaseUnit`, and celestial bodies over real time. This is a general-purpose simulation engine.

2. **AIManager/Rake System** (bespoke rake tasks + AIManager services): Drives specific assets (Luna precursor, GCC sat, Venus skimmer) as isolated mechanical steps. These are NOT wired into the live loop.

The gap between these two systems is what this task was designed to identify — and it has been confirmed: **the specific MVP assets have no integration with the live game loop**.

---

## Follow-up Tasks Needed (for user to scope)
1. Event log / admin UI game-status view (not yet filed)
2. Ice-mining/PSR mine fixes (not yet filed)
3. Venus skimmer wiring into live loop (not yet filed)
4. Craft transit state machine implementation (not yet filed)

## Lessons Learned
- `Units::BaseUnit` vs `Craft::BaseCraft` class hierarchy distinction is critical — craft are NOT ticked by the live loop because they don't inherit from `Units::BaseUnit`.
- `GameState#running` defaults to false — the live loop exists but is off by default.
- The AIManager orchestrator (`AIManager.rb`) is only invoked by rake tasks and admin controllers, never by the background simulation job.

---

HANDOFF SUMMARY: findings doc created | key discovery: real loop exists but does NOT tick craft or invoke AIManager | next action needed: user to scope follow-up tasks
