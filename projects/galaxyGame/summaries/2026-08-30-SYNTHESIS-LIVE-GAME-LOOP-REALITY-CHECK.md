# STATUS SYNTHESIS REPORT — Live Game-Loop Reality Check

**Task**: 2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md
**Agent**: Qwen local via Copilot (Implementation Agent)
**Date**: 2026-08-30
**Type**: Research / architecture (read-only, no code changes)

> Saved to summaries folder per dispatch interface ("Chat is for questions only —
> never paste synthesis into chat"). This is the initial-reconnaissance synthesis
> posted BEFORE the full findings pass, per the task's MANDATORY synthesis gate.

---

## What I found so far (file:line for each)

- **Q1 (Game#advance_by_days side effects)**: FOUND — real side effects, not just a counter.
  `galaxy_game/app/models/game.rb:42` `advance_by_days` → `process_settlements` (line 52),
  `process_units` (line 53), `process_planets` (line 54). `process_units` (line 104-109)
  iterates `Units::BaseUnit.all` and calls `unit.operate(time_skipped)`.

- **Q2 (scheduled job/worker ticking assets)**: FOUND — a real background live loop exists.
  `galaxy_game/app/jobs/game_simulation_job.rb` (Sidekiq, line 8) calls
  `game.advance_by_days(days_to_simulate)` (line 25) and self-reschedules
  `GameSimulationJob.perform_in(1.minute)` (line 40). Bootstrapped by
  `galaxy_game/config/initializers/game_background.rb:2-6` (after_initialize,
  `perform_in(10.seconds)` unless test/console). Gated on `GameState#running`
  (default false — `game_state.rb:17`). `config/schedule.rb` game-tick crons are
  all commented out (lines 9-23); only maintenance is active.

- **Q3 (craft transit state machine)**: NOT FOUND as a time-advancing state machine.
  `craft/transport/cycler.rb` stores static `orbital_period` + scheduled
  arrivals/departures (lines 14-15, 41-52). `scheduled_trip.rb:13-16`
  `next_destination` is an unimplemented stub. `docked_craft_trip.rb` only stores
  docking datetimes. Craft inherit from `ApplicationRecord`/`Craft::BaseCraft`,
  NOT `Units::BaseUnit` — so `Game#process_units` does NOT tick craft.

- **Q4 (asset wiring — precursor / GCC sat / Venus skimmer)**: NOT WIRED into the live loop.
  Grep of `game.rb`, `game_simulation_job.rb`, `game_service.rb`, `game_simulation.rb`
  for AIManager/precursor/gcc/skimmer → ZERO matches. All three are driven by bespoke
  rake/AIManager paths (see findings).

- **Q5 (PSR ice mining / deposit spawning)**: PARTIAL — data model + analysis exist,
  no functioning live extraction loop. `crater.rb:43-45,79-85` (PSR + ice tons),
  `crater_generator.rb:39` (spawns PSR craters), `hydrosphere_analyzer.rb:224,299`
  (identifies ice_mining sites). No live-tick ice extraction consuming deposits found.

- **Q6 (raw vs processed gas storage distinction)**: PARTIAL — exists only as a
  unit-level production pattern for the Luna regolith case, NOT a general Inventory
  category, and NOT implemented for the Venus skimmer. Precedent:
  `gas_separator_unit_bp.json` (`mixed_volatiles` → O2/H2O/CO2/H2/CH4/N2).
  `inventory.rb:147-149,224-230` distinguishes material TYPE (liquid/gas/fuel), not
  raw-vs-processed. `atmospheric_harvester_service.rb` treats skimmer gases as flat
  quantities with no raw/processed split.

---

## Critical Gotchas I Will Avoid
- ❌ Citing `luna_operations_simulation.rake`'s loop as proof of a real game loop —
  instead ✅ confirmed the real loop is `GameSimulationJob` → `Game#advance_by_days`,
  and separately confirmed what it does/doesn't tick.
- ❌ Trusting a prior handoff's "this works" claim — instead ✅ every finding above is
  confirmed live against current code with file:line references.
- ❌ Treating "skimmer arrives" and "skimmer produces gases" as one state — instead ✅
  recognizing the distinct sub-states (arrival / transit-tank transfer / atmospheric
  intake / onboard CO2→CO+O2 / departure-readiness) for Q4/Q6.

## Key Nuance (drives the final findings)
A real live loop DOES exist (contradicting the assumption that it may not), BUT it only
ticks settlements, `Units::BaseUnit`, and celestial bodies — it does NOT tick craft and
does NOT invoke the AIManager-driven assets (Luna precursor / GCC sat / Venus skimmer).
So the loop exists for settlement/unit/planet simulation, while the specific assets in
question remain driven by bespoke rake/AIManager scripts.

---

**SYNTHESIS COMPLETE.** Proceeding to full research pass + final findings doc.
