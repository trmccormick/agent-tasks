---
status: active
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase6+/2026-06-27-MEDIUM-FEATURE-INFLATABLE-TANK-SHELL-STATUS-TRACKING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase6+/2026-06-27-MEDIUM-FEATURE-INFLATABLE-TANK-SHELL-STATUS-TRACKING.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-06-27-MEDIUM-FEATURE-INFLATABLE-TANK-SHELL-STATUS-TRACKING.md
  Then open the moved file and change: status: active → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-06-27-MEDIUM-FEATURE-INFLATABLE-TANK-SHELL-STATUS-TRACKING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

CRITICAL: This task touches task_execution_engine_v2.rb — a shared file edited by multiple sessions. Per GUARDRAILS Rule 17, synthesis approval is mandatory before any code changes.
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Add shell status/thickness tracking to printed inflatable tank shells
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-27
**Last Updated**: 2026-06-27

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen3.5-27B local (primary)
**Why This Agent**: Small, well-scoped additive change to a known method; no cloud escalation needed unless local fails twice.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully (shared file, first dispatch of this specific task)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Synthesis report goes in chat BEFORE starting work.

---

## Context

`task_print_inflatable_tank_shells.json` fires an `advance_deployment_stages` effect
(handled by `AIManager::TaskExecutionEngineV2#advance_deployment_stages_from_effect`)
that advances a tank's internal deployment progress — `stages`, `current_stage`,
`progress_percent`, `time_remaining_hours` — all nested under `operational_data['deployment']`.

This was confirmed by direct code read on 2026-06-26/27 during the Luna Precursor V2
Sequence Reconciliation task (gap [3c]). The mechanical stage-tracking works correctly.
**What's missing**: nothing in the handler writes a field representing the *physical
existence and condition* of the printed shell itself — e.g. a thickness value or a
damaged/intact status — the kind of thing a future maintenance-robot repair task would
need to read. Right now, nothing in the codebase can answer "does this tank have a shell,
and is it damaged?"

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/adrs/GUARDRAILS.md` — execution rules (Rule 17, Rule 25 apply directly)
- `docs/architecture/systems/PORT_CONNECTION_SYSTEM.md` — for the general fidelity standard
  used elsewhere in this system (see Gotcha 2 below)

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1 — Additive only, do not touch existing tracking fields**
- ❌ Wrong: Renaming, restructuring, or removing `stages`/`current_stage`/`progress_percent`/`time_remaining_hours`.
- ✅ Right: Add new fields alongside the existing `deployment` hash, or as a sibling key
  on `operational_data` (e.g. `operational_data['shell']`). Existing tracking already
  works and other code may depend on its current shape.
- Why: This task is scoped narrowly. Breaking working tracking to "clean up" the schema
  is out of scope and risks regressing the reconciliation work just closed.

⚠️ **GOTCHA 2 — Keep it loose, this is a capacity-style flag, not a simulation**
- ❌ Wrong: Modeling shell degradation over time, stress calculations, or anything that
  requires a tick/update loop.
- ✅ Right: A simple status value (e.g. `"intact"` / `"damaged"`) and a thickness number,
  set once when the shell-print stage completes. Damage, if ever modeled, would be set by
  a separate explicit event (e.g. a hazard effect), not computed continuously.
- Why: Confirmed standing design principle for this system — capacity/status checks are
  deliberately loose, not full physics. Don't over-build.

⚠️ **GOTCHA 3 — Gas type (raw vs. separated O2/LOX) is explicitly OUT of scope**
- ❌ Wrong: Adding logic that branches on what gas the tank will eventually hold.
- ✅ Right: This task only concerns the shell itself. Gas type is a separate, already-
  existing inventory/resource concern (inflatable_gas_storage vs. inflatable_cryo_tank).
- Why: Explicit scope boundary from the human reviewer — do not pull this thread in here.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Use the standard template from TASK_TEMPLATE.md. Specifically confirm in your synthesis:
- You have located the actual current `advance_deployment_stages_from_effect` method body
  (line numbers will differ from any prior session's reference — re-locate, don't assume).
- You understand Gotchas 1–3 above.
- Your proposed field name(s) do not collide with anything already in `operational_data`
  for this unit type (check the actual blueprint/operational_data structure first).

**POST THIS TO CHAT BEFORE PROCEEDING.**

---

## Problem Statement

**Current behavior**: After `advance_deployment_stages_from_effect` processes the
shell-print stage, `operational_data['deployment']` is updated, but nothing indicates
whether a shell physically exists on the tank or what condition it's in.

**Expected behavior**: After the shell-print stage completes, the tank's
`operational_data` includes a status/thickness representation of the shell that other
systems (future maintenance-robot logic) could read.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/task_execution_engine_v2.rb` | Effect handler | `#advance_deployment_stages_from_effect` (locate current line number) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/missions/tasks_v2/task_print_inflatable_tank_shells.json` | The effect definition that triggers this handler |
| Blueprint file for the relevant tank unit (locate via blueprint lookup) | Confirm `operational_data` structure before adding a new key |

### Migration (if needed)
- [x] No migration needed — `operational_data` is a JSON/jsonb column already used for
  this kind of flexible field.

---

## Implementation Steps

> ⚠️ Synthesis report must be posted and approved before any of this.

### Step 1 — Locate and confirm current handler state
Read the actual current `advance_deployment_stages_from_effect` method. Confirm it still
matches the shape described in Context above (it may have changed since this task was
written).

### Step 2 — Add shell status fields
When the stage transition reaches the shell-print-complete point, write something like:
```ruby
op_data['shell'] = {
  'status' => 'intact',
  'thickness_cm' => <reasonable default, e.g. 2.0>
}
```
Exact field names/values are your call within Gotcha 2's constraint — confirm naming
choice in your synthesis report before writing code.

### Step 3 — Verify

> CRITICAL EXECUTION MANDATE: Targeted spec only, per GUARDRAILS Rule 3. Do not run the
> full suite.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/task_execution_engine_v2_spec.rb 2>&1 | tail -30'
```
Also confirm via the `luna_mission:execute` rake that existing passing tasks are unaffected.

### Step 4 — Synthesis Report (before committing anything)
Standard template. Wait for explicit approval — do not commit (GUARDRAILS Rule 26).

---

## Acceptance Criteria
- [ ] Tank `operational_data` includes a shell status/thickness representation after the
      shell-print stage
- [ ] Existing `deployment` tracking fields unchanged in shape/behavior
- [ ] Targeted spec: 0 failures
- [ ] `luna_mission:execute` rake: no new failures vs. current baseline

---

## Stop Conditions — escalate to user immediately if:
- Any existing passing spec breaks
- The blueprint's `operational_data` structure doesn't match what's expected — escalate
  rather than guessing
- Gas-type logic seems necessary to complete this — it shouldn't be; stop and ask
- Any architectural decision is required beyond field naming

---

## Dependencies
**Blocked by**: none — the mechanical `advance_deployment_stages_from_effect` already exists.
**Blocks**: none currently known.
**Related tasks**: `2026-06-19-HIGH-VERIFICATION-LUNA-PRECURSOR-V2-SEQUENCE-RECONCILIATION.md` (gap 3c origin)

**Note on consumer gap**: there is currently no maintenance-robot task that would read this
field. CAR-300 deployment robots have no `task_affinity`/deploy task at all yet (per
`lunar_precursor_manifest_v2_DRAFT.json`'s explicit flag on this). This is not a blocker on
building the field now — but if you come back to this area later and the field appears
unused, that's expected until the CAR-300 deploy/repair gap is separately addressed. Not
in scope for this task; noted here so it isn't mistaken for dead code.

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
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY:
