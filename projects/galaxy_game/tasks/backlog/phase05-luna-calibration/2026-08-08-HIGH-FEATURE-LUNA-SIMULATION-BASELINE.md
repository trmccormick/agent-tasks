# TASK: Luna Simulation Baseline — Re-establish Known-Good State & Report Against Phase 5 Criteria

**Task ID:** `2026-08-08-HIGH-FEATURE-LUNA-SIMULATION-BASELINE`
**Date Created:** 2026-08-08
**Priority:** HIGH
**Type:** feature
**System Domain:** TERRA_SIM
**MVP Alignment:** AI_MANAGER_LUNA_SETTLEMENT
**Local Worker Safe:** true

---

## Context

This is a **re-establishment of known-good baseline**, not new development. The last confirmed run was 17/17 on 2026-07-26 (per handoff `2026-07-26-EVENING-HANDOFF.md`). Since then, multiple sessions have touched manufacturing services, ECLSS docs, and phase structure — none should affect the simulation, but we need to verify.

**Phase 5 is a validation phase, not a feature phase.** The goal is to run the simulation, collect tick-by-tick metrics, and report pass/fail against the acceptance criteria in `LUNA-MVP-SIMULATION-DESIGN.md`.

---

## Prerequisites

1. Docker stack is running (`docker-compose -f docker-compose.dev.yml ps` should show web as healthy)
2. A Luna settlement exists (if not, `luna_mission:execute` creates one)

---

## Execution Steps

### Step 1 — Verify Stack Health

```bash
docker-compose -f docker-compose.dev.yml ps | grep web
```

Confirm the web container is up and healthy. If not, bring it up first:

```bash
docker-compose -f docker-compose.dev.yml up -d
```

Wait for PostgreSQL to be ready before proceeding.

### Step 2 — Run `luna_mission:execute` (End-to-End Mission)

```bash
cd /Users/tam0013/Documents/git/galaxyGame && docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails luna_mission:execute' 2>&1 | tee /tmp/luna_mission_execute.log
```

**Capture from output:**
- Target body (should be `LUNA-01`)
- Profile path resolved (v2 or legacy)
- Settlement name and ID
- Any FATAL errors or missing profile warnings

### Step 3 — Run `luna:simulate_operations` with Explicit Settlement ID

From Step 2 output, extract the settlement ID. Then run:

```bash
cd /Users/tam0013/Documents/git/galaxyGame && docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails luna:simulate_operations[50,<SETTLEMENT_ID>]' 2>&1 | tee /tmp/luna_sim_output.log
```

Replace `<SETTLEMENT_ID>` with the actual ID from Step 2.

### Step 4 — Collect Tick-by-Tick Metrics

From the simulation output, extract and report:

**Production metrics:**
- LOX production rate (kg/day) per tick
- TEU regolith processing rate (kg/day)
- PVU oxygen extraction rate (kg/day)
- Gas separator throughput (if deployed)
- Cryo tank storage levels over time

**Stockpile accumulation:**
- Final inventory snapshot (from `FINAL INVENTORY SNAPSHOT` section)
- Rate of stockpile growth for each tracked resource
- Whether stockpiles reach meaningful thresholds by tick 50

**Import decisions:**
- Count and timing of IMPORT decisions
- Resources imported, costs per kg
- Whether imports decline over time as local production ramps up

### Step 5 — Report Against Phase 5 Acceptance Criteria

Use the checklist from `LUNA-MVP-SIMULATION-DESIGN.md` (lines ~290-340). Report **PASS / FAIL / NOT_TESTED** for each:

#### Build Sequence Validation
| Criterion | Result | Notes |
|-----------|--------|-------|
| Precursor dependency chain completes in correct order | | |
| Landing pad and tank farm operational before first skimmer arrival | | (N/A if no skimmers yet) |
| No skimmer enters orbital holding due to late pad construction | | (N/A if no skimmers yet) |

#### Propellant Economics
| Criterion | Result | Notes |
|-----------|--------|-------|
| LOX production crossover: local LOX offsets Earth imports within expected timeline | | Report tick when this occurs, or "not reached in 50 ticks" |
| CH4 bridge period economically survivable (GCC stays positive) | | Report GCC balance at tick 50 |
| Venus skimmer ROI positive per cycle at current EAP values | | NOT_TESTED (no Venus skimmers in MVP scope) |
| Titan arrival triggers measurable drop in Earth CH4 import orders | | NOT_TESTED (no Titan skimmers in MVP scope) |

#### Tank Farm Coordination
| Criterion | Result | Notes |
|-----------|--------|-------|
| AI Manager correctly pre-positions propellant | | NOT_TESTED (no inbound skimmers in MVP scope) |
| No turnaround delayed due to wrong propellant | | NOT_TESTED |
| Tank farm capacity sufficient for concurrent HLT + skimmer ops | | NOT_TESTED |

#### ImportRequestGenerator Behavior
| Criterion | Result | Notes |
|-----------|--------|-------|
| N2 orders from Earth decline as local production ramps | | Report import count by tick range (1-10, 11-20, etc.) |
| No Earth LOX imports (locked out by 90% EAP pricing) | | Check all IMPORT decisions for LOX |
| Inbound skimmer manifests suppress redundant Earth imports | | NOT_TESTED |

#### Economic Viability
| Criterion | Result | Notes |
|-----------|--------|-------|
| LDC GCC balance stays positive through entire arc | | Report GCC at tick 0 and tick 50 |
| LOX revenue offsets import costs at some point | | Report cumulative revenue vs cost |
| Post-Titan: operating cash-flow positive on local production + LOX sales | | NOT_TESTED (no Titan skimmer) |

---

## Stop Conditions

- **STOP** if `luna_mission:execute` produces a FATAL error — report the error and stop. Do not proceed to simulation.
- **STOP** if no Luna settlement is found after `luna_mission:execute` — report the issue and stop.
- **DO NOT** attempt to fix any failures. Report them and stop. This is a baseline measurement, not a debugging session.

---

## Output Format

Return a structured report with these sections:

1. **Stack health:** web container status
2. **Mission execution:** profile resolved, settlement created/found, any warnings/errors
3. **Simulation summary:** tick count, duration, key production rates
4. **Tick-by-tick metrics table:** LOX rate, stockpile levels, GCC balance at ticks 0, 10, 20, 30, 40, 50
5. **Acceptance criteria matrix:** PASS/FAIL/NOT_TESTED per criterion with notes
6. **Observations:** any anomalies, unexpected behavior, or insights worth flagging

---

## References

- `LUNA-MVP-SIMULATION-DESIGN.md` — Phase 5 acceptance criteria (authoritative)
- `COMPLETE_PHASE_STRUCTURE.md` — Phase structure (canonical for Phase 6+)
- `luna_mission.rake` — Mission execution task (`galaxy_game/lib/tasks/luna_mission.rake`)
- `luna_operations_simulation.rake` — Simulation loop task (`galaxy_game/lib/tasks/luna_operations_simulation.rake`)
- `luna_base_profile_v2.json` — Profile data (`data/json-data/missions_v2/profiles/`)
- `luna_precursor_mission_plan_v2.json` — Mission plan with phases (`data/json-data/missions_v2/mission_plans/`)

---

## Minimal Handoff Block

```
You are the Implementation Agent.

Project: galaxy_game
Task: Re-establish Luna simulation baseline and report against Phase 5 criteria
Previous confirmed state: 17/17 on 2026-07-26 (luna_mission:execute)
Target files to execute:
  - galaxy_game/lib/tasks/luna_mission.rake (rake luna_mission:execute)
  - galaxy_game/lib/tasks/luna_operations_simulation.rake (rake luna:simulate_operations[50,<id>])

Scope: Run the simulation, collect metrics, report against acceptance criteria.
Do NOT fix any failures — report them and stop.
Do NOT modify any code or data.

Return structured report per task specification above.
```
