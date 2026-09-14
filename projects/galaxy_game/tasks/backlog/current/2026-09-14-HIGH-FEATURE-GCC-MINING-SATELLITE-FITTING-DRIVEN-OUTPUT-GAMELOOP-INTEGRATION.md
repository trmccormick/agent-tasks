---
date: 2026-09-14
updated: 2026-09-14
status: backlog
priority: P0 — highest
type: feature + architecture
system_domain: ai-manager / game-loop
mvp_alignment: Phase 5 (Luna bootstrap) — GCC is the first deployable asset and funding source for all precursor missions
agent_dispatch_interface: |
  You are the Implementation Agent.

  Project: Galaxy Game
  Task: GCC Mining Satellite — Fitting-Driven Output & Game-Loop Integration

  Read these files in order:
  1. This task file (full context)
  2. /Users/tam0013/Documents/git/galaxyGame/galaxy_game/lib/tasks/gcc_mining_sat.rake (current rake — what we're replacing)
  3. /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/game.rb (advance_by_days + process_units)
  4. /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/jobs/game_simulation_job.rb (Sidekiq job that triggers the loop)
  5. /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/game_simulation.rb (alternative GameSimulation class)
  6. /Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/integration/game_loop_integration_spec.rb (existing integration test — what was proven, what wasn't)
  7. Any satellite/craft model files in galaxy_game/app/models/ that define mine_gcc or inherit from ApplicationRecord
  8. /Users/tam0013/Documents/git/galaxyGame/data/json-data/blueprints/crafts/space/satellites/generic_satellite_bp.json (compatible_units whitelist)
  9. /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/craft/satellites/crypto_mining_satellite_data.json (base_mining_rate_gcc_per_hour)
  10. /Users/tam0013/Documents/git/galaxyGame/data/json-data/blueprints/units/energy/satellite_battery_bp.json (battery blueprint)

  Key gotchas from review (READ FIRST):
  - Craft inherit ApplicationRecord, NOT Units::BaseUnit — this is the deepest part of the fix
  - The rake calls mine_gcc manually beside advance_by_days — two independent calls stapled together
  - crypto_mining_satellite_data.json has a flat base_mining_rate_gcc_per_hour: 1000 — confirm whether mine_gcc reads this or fitted-unit hash rates
  - generic_satellite_bp.json compatible_units whitelist does NOT include satellite_battery — confirm if enforced
  - GameState#running defaults false, nothing auto-enables it — confirm whether it gates anything in the rake path
  - Do NOT change GameSimulationJob or advance_by_days themselves unless a stop condition is hit
  - Scope to satellite ONLY — do NOT build settlement computing → GCC linkage (that's a Gemini follow-up)

  Return format:
  - Confirmation of which fields mine_gcc actually reads today
  - Your implementation plan (confirm before coding)
  - After completion: RSpec output + git diff summary
---

# GCC Mining Satellite — Fitting-Driven Output & Game-Loop Integration

## Context

The GCC mining satellite is the **first deployable asset** in the game. It generates GCC (the initial currency) that funds all subsequent Luna precursor missions and Venus skimmer deployment. Without a working GCC loop, nothing downstream functions.

Currently, GCC mining exists only as a rake-driven simulation (`gcc_mining_sat.rake`). The rake calls `game.advance_by_days(1)` (the real production tick path) once per simulated day, but separately and manually calls `satellite.mine_gcc` in the same loop iteration — mining is not actually driven by the tick. The two calls sit next to each other but are independent: if you removed `mine_gcc`, the tick would still run; if you removed `advance_by_days`, mining would still work (when called manually).

This is because craft (satellites) inherit `ApplicationRecord`, not `Units::BaseUnit`. The game loop's `process_units` walk (`Units::BaseUnit.all.each { |u| u.operate(days) }`) never reaches satellites.

Separately, the design intent (confirmed 2026-09-14) is that GCC generation scales with **installed computing capacity** — not a flat constant. The satellite's mining output should be computed from its actual fitted units/rigs (hash rates, GPU coprocessor effects), not from `base_mining_rate_gcc_per_hour`. This same principle will eventually apply to settlement-side computing infrastructure (data centers, mainframes), but that is a separate follow-up task.

The integration test completed 2026-09-03 proved GCC mining works on tick 1 when invoked inline via `CraftFactory.build_from_blueprint` + manual `mine_gcc`. It did NOT prove multi-tick behavior through the game loop, because the satellite was never wired into `process_units`.

## Problem Statement

**Current**: Mining output is produced by a manual method call (`satellite.mine_gcc`) outside the tick loop, and it's unconfirmed whether that output is actually derived from the satellite's fitted units/rigs or from a static constant.

**Expected**: The satellite mines GCC as a real consequence of the game's tick loop processing it, with output computed from its actual installed configuration (units + rig effects), not a flat rate. A test drives the satellite through multiple real ticks via `GameState#running` + `GameSimulationJob`, showing GCC output accumulating tick over tick with no manual `mine_gcc` call anywhere in the test.

## Gotchas

1. **Craft are `ApplicationRecord`, not `Units::BaseUnit`** — This is the deepest part of the fix. Confirm whether making the satellite (or a mining-capable component of it) a real `Units::BaseUnit` subclass with `operate()` is the right shape, or whether `process_units` should be extended to also walk Craft-type records. Don't assume the former without checking which is less invasive to existing craft behavior.

2. **Confirm what `mine_gcc` actually reads today** — Before assuming fitting-driven computation needs to be built from scratch vs. just wired into the tick, confirm which field(s) `Satellite#mine_gcc` (or wherever the method actually lives) currently reads: flat `base_mining_rate_gcc_per_hour`, aggregated fitted-unit `mining.hash_rate` values, rig effects, or some combination.

3. **`generic_satellite_bp.json` compatible_units mismatch** — The whitelist doesn't include `satellite_battery` even though the recommended fit installs one. Confirm whether this whitelist is actually enforced anywhere before treating it as a blocker.

4. **`GameState#running` defaults false** — Nothing auto-enables it. The rake calls `advance_by_days` directly rather than through `GameSimulationJob`, so confirm whether `running` gates anything in this path or is a no-op here. Don't assume either way without checking.

5. **Scope to satellite only** — Do NOT attempt to generalize to settlement computing infrastructure (server_farm/mainframe_computer → GCC) in the same task. That's a real follow-up but a separate, larger piece that likely belongs with Gemini's economic-capacity work. Flag it as a recommended follow-up; don't build it here.

6. **Don't touch `GameSimulationJob` or `advance_by_days`** — This task is about the satellite/craft side. Only change these if a stop condition below is hit.

## Acceptance Criteria

1. **Tick-driven mining**: Satellite mining is triggered by the real tick mechanism (`GameSimulationJob` → `advance_by_days` → whatever the satellite now participates in), not a manual method call sitting beside it.

2. **Fitting-dependent output**: Mining output for a given day is demonstrably a function of the satellite's actual installed units/rigs at that time. Verified by fitting two different configurations and observing different output (not just asserted).

3. **Multi-tick test**: A rewritten test (rake and/or RSpec) drives the satellite through multiple real ticks — via `GameState#running` + `GameSimulationJob`, not a hand-rolled loop — and shows GCC output accumulating tick over tick with no manual `mine_gcc` call anywhere in the test.

4. **Full RSpec green**: No regressions to existing satellite/craft specs.

## Stop Conditions (Escalate Rather Than Proceed)

- If fixing this requires changing `Game#advance_by_days` or `process_units` itself (vs. changing how the satellite participates)
- If the `compatible_units/recommended_fit` mismatch turns out to reflect a real enforced constraint that blocks the existing recommended fit from working at all
- If mining-rate computation is found to depend on other unmigrated/stubbed services

## Files Involved — To Be Confirmed by Implementation Agent

| File | Role | Status |
|---|---|---|
| `galaxy_game/app/models/` (satellite/craft model) | Where satellite is defined; where `mine_gcc` lives | [FILL IN] |
| `galaxy_game/app/models/units/base_unit.rb` | `Units::BaseUnit` — potential parent class for mining component | [FILL IN] |
| `galaxy_game/app/models/game.rb` | `advance_by_days` + `process_units` — DO NOT CHANGE unless stop condition hit | Exists, confirmed |
| `galaxy_game/app/jobs/game_simulation_job.rb` | Sidekiq job that triggers the loop — DO NOT CHANGE unless stop condition hit | Exists, confirmed |
| `galaxy_game/lib/tasks/gcc_mining_sat.rake` | Current rake — being replaced/rewritten | Exists, confirmed |
| `galaxy_game/spec/integration/game_loop_integration_spec.rb` | Existing integration test — being extended/rewritten | Exists, confirmed |
| `data/json-data/blueprints/crafts/space/satellites/generic_satellite_bp.json` | Compatible units whitelist | Exists, confirmed |
| `data/json-data/operational_data/craft/satellites/crypto_mining_satellite_data.json` | Base mining rate (flat constant) | Exists, confirmed |
| `data/json-data/blueprints/units/energy/satellite_battery_bp.json` | Battery blueprint | Exists, confirmed |

## Recommended Follow-Up (Not In Scope)

**Settlement Computing Infrastructure → GCC Linkage**: Generalize the fitting-driven GCC output pattern to settlement-side computing infrastructure (`server_farm_bp.json`, `mainframe_computer_bp.json`). These have TFLOPS/module_slots fields but no visible GCC output link. This belongs with Gemini's economic-capacity work, not this task.

## Dependencies

- None — this is a standalone foundational fix
- Blocks: All precursor mission validation (P1), Venus skimmer deployment (P2)
- Unblocks: GCC power/battery bug diagnosis (the bug can't be diagnosed until the satellite is in the loop)
