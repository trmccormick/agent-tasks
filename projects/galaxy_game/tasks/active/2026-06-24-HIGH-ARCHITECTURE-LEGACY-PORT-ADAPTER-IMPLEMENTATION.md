# TASK: Implement LegacyPortAdapter (Option C — Bus-Topology Bridge)
**Status**: ACTIVE
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-06-24
**Last Updated**: 2026-06-24

---
status: active
priority: HIGH
type: architecture
system_domain: UNITS
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-06-24-HIGH-ARCHITECTURE-LEGACY-PORT-ADAPTER-IMPLEMENTATION.md

READ FIRST: Task file contains all prerequisites, gotchas, and verification steps. Also
read PORT_CONNECTION_SYSTEM.md (System of Record) and the v1.9 connection_schema interface
contract before starting.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template below).
```

**That's it.** Everything else is in this task file.

---

## Local Worker Triage Report
*Skipped — this task requires running code/tests and modifying engine files; not
local-worker-safe (read-only triage). Goes straight to an implementation agent.*

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen3.6-27B or 35B local (primary)
**Why This Agent**: Requires real engine code changes, blueprint JSON edits, and rake/spec
execution across two repos. Not a planning/review task.
**Local attempts before cloud**: N/A — first dispatch of this specific task
**Supervision Level**: Watched carefully

> Note: a related fix (old port-counting model, now superseded by this task) was completed
> earlier today by a qwen session that started before the Option C decision was finalized.
> That work is real and not being discarded — see Context below. This is a fresh dispatch,
> not a retry of a failure.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `agent-tasks/REVIEW_AGENT_GUIDE.md` (EXECUTOR role, Rule 0)
2. **Project Guide**: `agent-tasks/projects/galaxy_game/GALAXY_GAME_CONTEXT.md`
3. **System of Record**: `PORT_CONNECTION_SYSTEM.md` — read in full, including "Handling
   Existing Schemas" and "Handling Port-less Units" sections
4. **Interface Contract**: the v1.9 `connection_schema` specification (mounting_slots,
   utility_ports, formula-first validation, Zero-Drift Enforcement) — read in full before
   writing any blueprint changes
5. **This Task File**: everything below

> Agent MUST read in this order. Synthesis report goes in chat BEFORE starting work.

---

## Context

A qwen session completed a fix for the gas-separator/cryo-tank port blocker earlier today,
**before** the Option C bus-topology decision was finalized with Gemini. That fix used the
*old* flat-integer port-counting model:

- **Engine change** (galaxyGame repo): `check_and_decrement_port` modified to use
  `unit.unit_type` for blueprint lookup. Real, committed — confirm exact commit hash in the
  galaxyGame repo as your first step; not yet confirmed in this task file.
- **Blueprint change** (galaxyGame repo, **local only, not committed** — per project's
  "no raw JSON commits" rule; Time Machine covers this, no git history exists or is needed):
  `inflatable_cryo_tank_bp.json` got a flat `ports` block added in the old style.
- **Result at the time**: `deploy_gas_separator` passed, 12/13 Luna V2 rake tasks (1
  pre-existing, out-of-scope gap: [3c] tank-stage advancement).

**This work is real and valid under the model in use at the time — it is not a mistake.**
It simply predates Option C. Your job is to reconcile and migrate it forward, not discard it.

**Relevant Architecture Docs** — read before starting:
- `PORT_CONNECTION_SYSTEM.md` — locked Option C decision, gap resolutions
- The v1.9 interface contract spec — exact field definitions for `connection_schema`
- `docs/new_agent/rules/DECISIONS.md` — check for any other locked decisions this touches

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: `inflatable_gas_storage_bp.json` already uses a typed-port schema
(`input_ports`/`output_ports`/`utility_ports` with `type: fluid_in/fluid_out` and
`compatible_gases`) that predates v1.9.
- ❌ Wrong: migrating this file to the new `connection_schema` format, or having the adapter
  ignore/flatten its typed structure back into a bare integer count.
- ✅ Right: this file **stays as-is**. The `LegacyPortAdapter` must read this schema natively
  and map its `fluid_in`/`fluid_out` types directly to Bus-Topology — confirmed resolution
  per `PORT_CONNECTION_SYSTEM.md`.
- Why: this was already the closest existing schema to the v1.9 intent. Forcing a migration
  or flattening it would destroy data that's already correctly typed.

⚠️ **GOTCHA 2**: Port-less units (e.g. `inflatable_pressure_tank`, bulk storage with no
live flow-through connections) must not be forced into having ports.
- ❌ Wrong: adding a non-zero port count to satisfy a lookup, or having the adapter assume
  every unit has ≥1 port.
- ✅ Right: zero ports is a valid, handled state. The adapter returns `0` for legacy
  port-count queries on these units; the engine does not error or force a non-zero count.
- Why: this was the original bug that triggered this entire investigation — patching around
  it again here would just reintroduce it one layer down.

⚠️ **GOTCHA 3**: `allowed_formulas` on a `utility_port` must use chemical formulas (`CH4`,
`LOX`, `H2O`), not free-text gas names — this project is "formula-first" per
`PORT_CONNECTION_SYSTEM.md`'s stated philosophy.
- ❌ Wrong: `"allowed_formulas": ["methane", "oxygen"]`
- ✅ Right: `"allowed_formulas": ["CH4", "LOX"]`
- Why: systemic consistency — formula-based validation is the documented standard, not an
  optional style choice.

⚠️ **GOTCHA 4**: Zero-Drift Enforcement — no `ports` or `connections` blocks are permitted
anywhere outside the `connection_schema` object once a file is migrated to v1.9.
- ❌ Wrong: leaving a legacy flat `ports` block alongside a new `connection_schema` block
  "just in case."
- ✅ Right: fully remove the old `ports` block when migrating `inflatable_cryo_tank_bp.json`
  — the adapter handles backward-compatible *queries*, the blueprint file itself should not
  carry both formats simultaneously.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

```
## STATUS SYNTHESIS REPORT

**Task**: LegacyPortAdapter (Option C — Bus-Topology Bridge)
**Status**: active
**Date**: 2026-06-24

### What I'm About to Do
[2-3 sentences: confirm current state of the existing engine/blueprint changes, build the
LegacyPortAdapter per the v1.9 spec, migrate the cryo tank blueprint, rewrite the gas
separator task logic, validate against the rake suite + new targeted unit tests.]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/task_execution_engine_v2.rb` | Confirm/evaluate existing check_and_decrement_port fix | pending |
| `inflatable_cryo_tank_bp.json` | Migrate to v1.9 connection_schema | pending |
| `inflatable_gas_storage_bp.json` | Read-only reference — confirm adapter compatibility | pending |
| `inflatable_pressure_tank.json` | Read-only reference — confirm zero-port handling | pending |
| `[deploy_gas_separator task file]` | Rewrite connection logic to bus registration | pending |

### Prerequisites Completed
- ✅ Read REVIEW_AGENT_GUIDE.md (Rule 0)
- ✅ Read GALAXY_GAME_CONTEXT.md
- ✅ Read PORT_CONNECTION_SYSTEM.md in full
- ✅ Read the v1.9 connection_schema interface contract in full
- ✅ Read this task file, including all four gotchas above

### Expected Outcomes
LegacyPortAdapter implemented and passing targeted tests for both confirmed edge cases
(gas-storage typed-port passthrough, zero-port handling). Cryo tank blueprint fully migrated
to v1.9 with no legacy `ports` block remaining. Gas separator task using bus registration.
Rake suite at least matching the existing 12/13 baseline.

### Critical Gotchas I Will Avoid
- ❌ Migrating or flattening inflatable_gas_storage_bp.json — instead ✅ adapter reads it natively
- ❌ Forcing non-zero ports on port-less units — instead ✅ adapter returns 0, no error
- ❌ Free-text gas names in allowed_formulas — instead ✅ chemical formulas only
- ❌ Leaving old + new port blocks both present — instead ✅ full removal of legacy block on migration

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The port-counting system has at least four mutually inconsistent schemas in active use
across existing blueprints (flat integer counts in two different shapes, a minimal
internal/external count, and one already-typed fluid/power/control schema), plus units that
legitimately have zero ports under an engine that assumed every unit has at least one. This
caused real blocking failures in Luna V2 rake validation (shell-printing naming mismatch,
gas separator port allocation, missing pressure-tank port definitions). Option C
(LegacyPortAdapter bridging old port-counting to a new v1.9 Bus-Topology `connection_schema`)
was selected as the resolution after design review with Gemini and qwen.

**Current behavior**: Inconsistent, partially-patched port resolution across different
blueprint schemas; one unit type forced into having ports it shouldn't need.
**Expected behavior**: A single adapter layer resolves both legacy and v1.9-native blueprints
correctly, including the two confirmed edge cases (gas-storage typed passthrough, zero-port
units), with the engine never assuming a non-zero port count.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/task_execution_engine_v2.rb` | Port lookup/decrement logic | `check_and_decrement_port` |
| `app/services/lookup/blueprint_lookup_service.rb` | Blueprint resolution | wrap for v1.9/legacy dual-mode |
| `data/json-data/.../inflatable_cryo_tank_bp.json` | Migrate to v1.9 `connection_schema` | full ports block replacement |
| `[deploy_gas_separator task file — confirm path]` | Connection logic | shift to `register_to_bus` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `inflatable_gas_storage_bp.json` | Confirm adapter reads existing typed-port schema correctly without modification |
| `inflatable_pressure_tank.json` | Confirm zero-port handling without modification |
| `PORT_CONNECTION_SYSTEM.md` | System of Record for Option C |
| v1.9 interface contract spec | Exact field definitions (`mounting_slots`, `utility_ports`, `allowed_formulas`, `throughput_limit`, `mounted_to_slot`) |

### Migration (if needed)
- [x] No migration needed — this is blueprint/JSON data and engine logic, not a DB schema change

---

## Implementation Steps

> ⚠️ Synthesis report must be posted and approved before any of these steps begin.

### Step 1 — Confirm current state (read-only)
- Confirm exact galaxyGame commit hash for the `check_and_decrement_port` change in
  `app/services/ai_manager/task_execution_engine_v2.rb`.
- **Note**: this same file contains a separate, already-known unresolved issue —
  `task_execution_engine_v2.rb:107` is flagged elsewhere as a CRITICAL BLOCKER where
  `BaseSettlement.create!` bypasses `SettlementDeploymentService`. That issue is OUT OF SCOPE
  for this task. Do not touch it, but if your changes land anywhere near line 107, note that
  explicitly in your synthesis report so it's not accidentally disturbed or conflated with
  this task's port-adapter work.
- Open `inflatable_cryo_tank_bp.json`, confirm its current local-only port structure.
- Re-run the Luna V2 rake suite, confirm the 12/13 baseline holds before changing anything.
  If it doesn't match, STOP and report — do not proceed on an unverified baseline.

### Step 2 — Evaluate the existing engine fix against the adapter design
```ruby
# In app/services/ai_manager/task_execution_engine_v2.rb:
# Confirm: does the existing check_and_decrement_port change (using unit.unit_type for
# blueprint lookup) work as-is inside a LegacyPortAdapter wrapper, need modification, or
# conflict with the wrapper approach? Check it directly against how
# app/services/lookup/blueprint_lookup_service.rb will need to be wrapped for v1.9/legacy
# dual-mode resolution per PORT_CONNECTION_SYSTEM.md. Do not assume compatibility.
```
If unsure which applies, STOP and report findings rather than guessing.

### Step 3 — Implement `LegacyPortAdapter`
- Project v1.9 `connection_schema` data into legacy port-count queries for old-style
  blueprints.
- Handle Gotcha 1 (gas-storage passthrough) and Gotcha 2 (zero-port units) exactly as
  specified above — these are required behaviors, not optional.

### Step 4 — Migrate `inflatable_cryo_tank_bp.json` to v1.9
- Replace the local-only legacy `ports` block entirely with a proper `connection_schema`
  block: `mounting_slots` (id, type, location, max_mass_kg), `utility_ports` (id, type,
  bus_id, `allowed_formulas` using chemical formulas per Gotcha 3, `throughput_limit`,
  `mounted_to_slot`).
- Confirm Zero-Drift Enforcement (Gotcha 4): no leftover legacy `ports` block once migrated.
- No git history to preserve for this file — Time Machine has a local backup of the prior
  state independent of this task. Rewrite cleanly.

### Step 5 — Rewrite gas separator task logic
- Shift `deploy_gas_separator`'s `connect_units(unit1, port1, unit2, port2)` calls to
  `register_to_bus(bus_id: cryo_grid)` or equivalent.

### Step 6 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Add and run targeted tests (not just the rake pass) asserting:
- A port-less unit (`inflatable_pressure_tank`) returns `0` ports, no error.
- `inflatable_gas_storage_bp.json`'s typed port data (`fluid_in`/`fluid_out`/
  `compatible_gases`) is read correctly through the adapter, unmodified.

Then run the full Luna V2 rake suite, confirm at minimum the same 12/13 baseline ([3c] gap
remains pre-existing/out of scope — do not attempt to fix it here).

Paste actual output for both targeted tests and the rake summary — not a summary claiming
success.

### Step 7 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Component: LegacyPortAdapter + cryo tank migration + gas separator task rewrite
Existing engine fix evaluation: [a — compatible as-is / b — modified / c — reverted, with reasoning]

CHANGES MADE
[list]

TEST RESULTS
[paste actual output — targeted tests + rake]

RISK
[any shared code affected]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] LegacyPortAdapter implemented and reading both legacy and v1.9-native blueprints correctly
- [ ] Gas-storage typed-port schema confirmed unmodified and correctly read through adapter
- [ ] Zero-port units confirmed handled without forcing a non-zero count
- [ ] Cryo tank blueprint fully migrated to v1.9, no legacy `ports` block remaining
- [ ] Gas separator task using `register_to_bus` instead of `connect_units`
- [ ] Targeted unit tests for both edge cases passing (actual output pasted)
- [ ] Luna V2 rake suite at least matching 12/13 baseline (actual output pasted)
- [ ] No regressions in specs not touched by this task

---

## Stop Conditions — escalate to user immediately if:
- Anything about the existing engine fix or local cryo tank blueprint edit doesn't match
  this task file's description — report actual state, don't silently reconcile on your own
- The v1.9 interface contract is ambiguous about anything not explicitly covered here —
  ask, don't invent a resolution and present it as following spec
- Fix causes new failures in specs you did not touch
- Root cause turns out to be in a shared concern/base class used across many unit types
  beyond what this task anticipated
- Any architectural decision is required beyond what's already locked in
  `PORT_CONNECTION_SYSTEM.md`
- You are ever unsure which of the two repos (agent-tasks vs. galaxyGame) a given command
  should run against — confirm before running anything

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "architecture: implement LegacyPortAdapter (Option C bus-topology bridge)"
git push
```

**Task file move on completion — use git mv, never copy:**
```bash
git mv projects/galaxy_game/tasks/active/2026-06-24-HIGH-ARCHITECTURE-LEGACY-PORT-ADAPTER-IMPLEMENTATION.md projects/galaxy_game/tasks/completed/2026-06/
git commit -m "chore: move LegacyPortAdapter task to completed/"
```

---

## Documentation
- [ ] Update `PORT_CONNECTION_SYSTEM.md` — mark Option C implementation complete, link commit hash
- [ ] Flag doc gap if `DECISIONS.md` doesn't yet reference this as a locked decision — do not
  create/edit DECISIONS.md yourself, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: any future gas-separator, cryo-tank, or unit-connection work in phase5+/phase6+
**Related tasks**: earlier today's cryo-tank port fix (superseded, not discarded — see Context)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
