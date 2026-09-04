---
status: backlog
priority: MEDIUM
type: research
system_domain: AI_MANAGER
mvp_alignment: POWER_SYSTEM_VALIDATION
local_worker_safe: true
---

## RESEARCH TASK: GCC Satellite Power/Battery Discrepancy

**Context**: During verification of the real game loop integration test (`2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md`), an arithmetic discrepancy was discovered in mining success/failure patterns across game ticks.

---

## Problem Statement

### Observed Behavior
- **Tick 1**: Mining succeeds, returns 100.0 GCC ✅
- **Tick 2-3**: Mining blocked, returns 0 ❌
- **Power state**: Identical across all ticks — has_sufficient_power? = false, solar 150 kW vs consumption 285 kW (deficit -135 kW)

### Key Discovery
Satellite has implicit 100 kWh default battery:
- **Tick 1 battery**: 100.0 → 30.0 kWh (consumed 70 kWh) → mining succeeds
- **Tick 2-3 battery**: 30.0 kWh (unchanged) → mining blocked

### The Discrepancy
**Claimed calculation (unverified):**
```
Power deficit: 135 kW
Tick span: 20 game days
Battery drain: 135 kW × 20 days = 67.5 kWh per tick? ❌ (units don't match)
```

**Problem:** Unit mismatch — kW (power) × days (time) ≠ kWh (energy) without proper conversion through seconds_per_game_day.

---

## Research Objectives

1. **Verify battery drain calculation**
   - Trace through code: How much energy does battery actually consume per tick?
   - What's the actual formula in `BatteryManagement` concern?
   - Does it account for `GameState#seconds_per_game_day` and tick duration?
   - Confirm: Is 70 kWh the expected drain for a 20-day tick? (What about intermediate hours?)

2. **Confirm power flow in mine_gcc**
   - When `has_sufficient_power? = false`, does `mine_gcc` ALWAYS try battery?
   - Is there a minimum battery threshold that blocks mining?
   - Could tick 2-3 return 0 for a different reason (e.g., account state changed, mining units disappeared)?

3. **Determine if this is production bug or test artifact**
   - Is 150 kW solar + 285 kW consumption a realistic satellite configuration?
   - Real production satellites provisioned with adequate solar (e.g., 300+ kW)?
   - Is this a test setup issue (under-provisioned) or a production code bug (battery over-discharged)?

4. **Distinguish root cause categories**
   - **Test setup bug**: Wrong operational_data paths or unrealistic power config
   - **Production code issue**: Battery depletes too fast, mining gate is too aggressive, or stale satellite instance
   - **Design-correct behavior**: Satellite is properly sized below solar, battery depletion is expected

---

## Background Context

### From Previous Session
- Test: `galaxy_game/spec/integration/game_loop_integration_spec.rb` (PASSING, 2 examples)
- Setup: Creates satellite via `Manufacturing::CraftFactory.build_from_blueprint("generic_satellite", ...)`
- Units installed: advanced_computer (100 kW consumption), solar_panel (150 kW generation)
- Power paths (ISSUE): Test overrides operational_data['power']['generation_kw'] but code reads operational_data['operational_properties']['power_generation_kw'] → test overrides ignored, actual unit JSON values used instead
- No explicit battery unit installed, but satellite responds to `battery_level` → implicit default battery from BatteryManagement concern

### Files to Reference
- `app/models/concerns/battery_management.rb` — battery initialization, discharge logic
- `app/models/concerns/energy_management.rb` — power calculation, `has_sufficient_power?` gate
- `app/models/concerns/cryptocurrency_mining.rb` — line 14 power gate, battery fallback path
- `galaxy_game/spec/integration/game_loop_integration_spec.rb` — test setup, observed behavior
- `data/json-data/units/` — actual unit power specs (150 kW solar, etc.)

---

## Success Criteria

- [ ] Battery drain calculation verified: Confirm exact kWh consumed per tick (either 70 kWh or correct value)
- [ ] Power flow traced: Confirm exact line in code where tick 2-3 mining gets blocked
- [ ] Root cause classified: Is this test artifact, production bug, or design-correct behavior?
- [ ] Evidence documented: Provide specific file:line references + actual calculated/observed values
- [ ] Recommendation provided: Should power config be fixed, battery enlarged, or test revised?

---

## Investigation Steps

**Step 1:** Read battery discharge logic
- `BatteryManagement#discharge_battery` or `consume_battery` — does it exist?
- What's the discharge rate per game tick?
- How does it scale with tick duration (seconds_per_game_day)?

**Step 2:** Trace tick 1 vs tick 2 mining
- Add logging to `mine_gcc` before line 14 power gate (does gate return 0?)
- Log actual battery_level and power_required_for_mining on each tick
- Confirm: Does tick 2 reach battery check or fail at has_sufficient_power? gate?

**Step 3:** Calculate expected drain
- Get `seconds_per_game_day` from GameState setup (3 in test)
- Get tick span (20 game days in test loop)
- Get power deficit (-135 kW in test)
- Calculate: Expected kWh drain = |deficit| × tick_span × seconds_per_game_day / 3600
- Compare to observed: 70 kWh

**Step 4:** Classify root cause
- If calculation matches 70 kWh: Design is correct, battery depletion is expected
- If calculation doesn't match: Battery discharge logic is buggy or not accounting for time properly
- If power gate happens on tick 1 too: Satellite instance is stale or power methods not responding

---

## Pending Investigation Artifacts

- Power diagnostics from test run (battery levels logged per tick)
- Full test output with [POWER] tags showing generation, usage, battery state
- Battery capacity and drain_rate from GameState/satellite initialization

---

## Notes

- This task does NOT require code changes — only research and root cause identification
- Do not implement fixes until root cause is confirmed
- Do not assume this is a bug — it may be design-correct behavior
- Compare to production rake scripts (gcc_mining_sat.rake) — do they have similar power constraints?
- Distinguish between "what the code does" (observation) and "whether that's correct" (analysis)

---

## Blocked By

- None (independent research task)

## Blocks

- Any power/battery system improvements until root cause confirmed
- Follow-up: `2026-09-??-[TYPE]-GCC-SAT-POWER-PROVISIONING-FIX.md` (TBD after research complete)
