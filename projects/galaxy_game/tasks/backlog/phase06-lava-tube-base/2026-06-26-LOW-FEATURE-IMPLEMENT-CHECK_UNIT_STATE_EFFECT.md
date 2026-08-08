---
status: backlog
priority: LOW
type: feature
system_domain: AI_MANAGER | TASK_EXECUTION
mvp_alignment: LUNA_PRECURSOR_MISSION_1
local_worker_safe: true
created: 2026-06-26
updated: 2026-06-26
---

# TASK: Implement check_unit_state_from_effect for precondition verification

**Status**: BACKLOG
**Priority**: LOW
**Type**: feature
**Created**: 2026-06-26

---

## Context

`TaskExecutionEngineV2.check_unit_state_from_effect` (line 529) is a stub that logs "Verified" and returns true without actually checking anything. One task file (`task_verify_processing_chain_ready.json`) uses `"action": "check_unit_state"` but it's not wired into any of the current 3 phases.

This handler is needed for precondition verification — tasks that need to verify another unit is in a specific state before proceeding (e.g., "verify TEU is processing" before starting gas separator deployment).

## Problem Statement

**Current behavior**: Stub logs and returns true regardless of actual unit state.
**Expected behavior**: Load the named unit, check its operational_data['state'] matches expected_state, raise error if mismatch.

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Engine with stub handler | `check_unit_state_from_effect` line ~529 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/missions/tasks_v2/task_verify_processing_chain_ready.json` | Example task using check_unit_state |
| `galaxy_game/app/models/units/base_unit.rb` | Unit model with operational_data |

## Implementation Steps

### Step 1 — Verify current usage
```bash
grep -r "check_unit_state" /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions/tasks_v2/*.json
```
Expected: Only `task_verify_processing_chain_ready.json` uses it, not wired into any phase.

### Step 2 — Implement real verification
- Load unit by name from settlement.units
- Check operational_data['state'] matches expected_state
- Raise `InfrastructureSequenceError` if unit not found or state mismatch
- Return true only if state matches

### Step 3 — Verify no regression
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission:execute 2>&1 | tail -5'
```
Expected: Still 13/13 passing.

## Acceptance Criteria
- [ ] `check_unit_state_from_effect` actually verifies unit state matches expected
- [ ] Raises `InfrastructureSequenceError` if unit not found or state mismatch
- [ ] No regression in existing 13/13 Luna V2 rake suite
- [ ] Follows existing error pattern

## Stop Conditions — escalate to user immediately if:
- Unit model doesn't have a 'state' field in operational_data
- Implementation requires changes to more than the one handler method

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb
git commit -m "feat: implement check_unit_state_from_effect for precondition verification"
git push
```

## Dependencies
**Blocked by**: none
**Blocks**: None — this is a standalone improvement
**Related tasks**: None currently in backlog

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
