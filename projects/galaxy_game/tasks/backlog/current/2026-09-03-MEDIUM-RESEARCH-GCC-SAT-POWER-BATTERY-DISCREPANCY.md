---
status: backlog
priority: MEDIUM
type: research
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate — **EXCEPT** `[project]`/`[SUBFOLDER]` path segments; fill in before dispatch
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template provided
- [ ] All file paths are verified to exist — **NOT DONE**, Claude has no filesystem access; Step 1 is exactly that verification
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-03-MEDIUM-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  Confirm whether this file is tracked in git first (`git ls-files <path>`) —
  if tracked, use `git mv`; if untracked, use `mv` then `git add` the final
  path. Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis
  until this is done.

LIFECYCLE: backlog → active → completed
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-03-MEDIUM-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: GCC Mining Satellite Power/Battery Discrepancy — Real Root Cause
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research (may become a small fix once root cause is confirmed)
**Created**: 2026-09-03

---

## Context
The real-loop integration test (`game_loop_integration_spec.rb`) dispatches a GCC mining satellite's `mine_gcc` alongside real game-loop ticks. Result: tick 1 mines 100.0 GCC, ticks 2 and 3 return 0. A prior investigation (Claude Haiku) attributed this to battery depletion, with this arithmetic:

```
System consumption: 285 kW
Solar generation:  150 kW
Deficit:          -135 kW per hour
Per 20-day tick (~60 seconds): 135 kW × 20 days = 67.5 kWh needed
```

**This arithmetic does not hold up.** kW × days does not convert to kWh without multiplying by 24 (hours/day) — `135 kW × 20 days × 24 = 64,800 kWh`, not 67.5 kWh. The 67.5 kWh figure was not shown to come from any actual code — it appears to have been back-derived to fit the observed 100/0/0 result rather than computed from a real formula. This needs to be resolved with real evidence, not accepted as-is.

Separately, the prior investigation claimed the satellite has "an implicit 100 kWh default battery" — but the reference implementation (`gcc_mining_sat.rake`) explicitly installs a `satellite_battery` unit configured at `capacity_kwh: 2000.0`, `current_kwh: 1500.0`. If this test's satellite setup doesn't install an equivalent real battery unit, that's a test-setup gap (satellite build path not matching the canonical unit-installation sequence used elsewhere), not evidence that "the test is just under-provisioned by design."

**Design facts already confirmed, not up for debate in this task:**
- The battery's entire purpose is to guarantee effectively constant power in orbit — solar panels are intentionally sized above normal draw specifically so surplus charges the battery to cover brief eclipse periods (which are short and intermittent in a normal Luna/Earth orbit, never extended).
- A sustained zero-power state across 40 simulated days (ticks 2-3, given `game_state.day` jumped 20 days per tick) is not physically plausible under this design — no real orbit stays in eclipse that long.
- Mining output should be gated on real power availability (this coupling is correct design, not something to remove) — the question is why power/battery state isn't behaving as designed, not whether the gate itself is wrong.

## Problem Statement
Determine the REAL cause of the tick 1 success / ticks 2-3 failure pattern, with evidence traced to actual code — not asserted arithmetic.

**Current state**: Unconfirmed. Leading hypothesis (not yet proven): the satellite's power grid and/or battery charge state isn't being recalculated fresh each tick across large simulated-day jumps — possibly because the test's satellite setup skips a power-grid refresh step the reference rake script does explicitly (`satellite.instance_variable_set(:@power_grid, nil)` before each deploy).

**Expected output**: A real, evidenced explanation — quoted code, real numbers, no back-derived math — for why power/battery state produces this exact pattern, and confirmation of whether this is a test-setup-only issue or something that would also affect real gameplay.

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: Do not accept any energy/power arithmetic without showing the actual code it comes from. If a real time-scaling factor exists (e.g. "20 game-days" translates to some shorter real-world tick duration for power-draw purposes), that conversion must be quoted directly from code — not asserted.

⚠️ **GOTCHA 2**: Confirm what battery unit (if any) is actually installed on this specific test's satellite — do not assume a config value without reading the test's actual satellite-setup code and comparing it line-by-line against `gcc_mining_sat.rake`'s setup sequence (build → install units → install rigs → `base_units.reload` / `base_rigs.reload` / clear `@power_grid` / `deploy`).

⚠️ **GOTCHA 3**: Do not propose changing panel/battery sizing, reducing tick count, or any other test-parameter tweak until the root cause (stale state vs. genuine under-provisioning vs. something else) is confirmed with real evidence. A fix that makes the symptom disappear without confirming the cause risks masking a real production bug (see Gotcha 4).

⚠️ **GOTCHA 4**: This may not be a test-only issue. If `power_generation`/battery charge state isn't refreshed across large time-skips in the ACTUAL production code path (not just this test), that's a real gameplay bug affecting any craft that goes a long time between ticks — explicitly check and report which case this is.

## Implementation Steps

### Step 0 — Move task file to active/ and update status
(See Agent Dispatch Interface above.)

### Step 1 — Verify referenced file paths exist
Confirm `game_loop_integration_spec.rb`, `cryptocurrency_mining.rb`, `energy_management.rb`, and `gcc_mining_sat.rake` all exist at their expected locations before proceeding.

### Step 2 — Trace the real energy/power calculation
Read the actual code path `mine_gcc` → `has_sufficient_power?` → `power_generation`/`power_usage` computation, and any battery consume/charge methods. Quote the real formulas. Confirm or refute the 135 kW deficit and whatever the real per-tick energy requirement actually is (with correct units).

### Step 3 — Compare satellite setup: test vs. reference
Read the integration test's satellite-building code and compare directly, line by line, against `gcc_mining_sat.rake`'s setup sequence. Identify any missing steps (battery unit installation, power-grid cache clearing, `deploy` call, `reload` calls).

### Step 4 — Determine scope
Confirm: is the root cause specific to this test's setup, or does the same stale-state issue exist in the production code path used by real gameplay? Check whether anything in the live game loop (not just tests) would hit the same large-time-skip scenario.

### Step 5 — Synthesis Report
Real evidence only: quoted code, quoted numbers, correct unit math. State the confirmed root cause and whether a fix is needed in test setup only, or in production code.

## Acceptance Criteria
- [ ] Real code quoted for the energy/power calculation — no asserted or back-derived arithmetic
- [ ] Confirmed what battery unit (if any) exists on the test's satellite, compared directly against the reference rake script's setup
- [ ] Root cause identified: stale power-grid/battery state vs. genuine under-provisioning vs. something else — with evidence for whichever it is
- [ ] Explicit statement on whether this is a test-only issue or also affects production gameplay
- [ ] No fix proposed/applied until root cause is confirmed

## Stop Conditions
- If tracing the real code reveals a production bug (not just a test issue), stop and report — do not attempt to fix a production gameplay bug as a side effect of this research task without explicit approval
- If the root cause can't be pinned down with real evidence after reasonable investigation, report what was found and what remains uncertain — don't guess to produce a tidy answer

## Dependencies
**Blocked by**: none
**Blocks**: full confidence in the real-loop integration test's craft-dispatch results
**Related**: `2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md` (completed, this task follows up on its findings)

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
HANDOFF SUMMARY: [root cause confirmed y/n] | [test-only or production issue] | [fix needed y/n]
