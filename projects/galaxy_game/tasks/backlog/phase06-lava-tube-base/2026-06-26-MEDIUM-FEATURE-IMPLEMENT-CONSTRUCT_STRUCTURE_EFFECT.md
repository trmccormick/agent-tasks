---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER | TASK_EXECUTION
mvp_alignment: LUNA_LAVA_TUBE_CONSTRUCTION
local_worker_safe: true
created: 2026-06-26
updated: 2026-06-26
---

# TASK: Implement construct_structure_from_effect for Lava Tube Construction

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-26

---

## Context

`TaskExecutionEngineV2.construct_structure_from_effect` (line 467) is a stub that logs "not yet implemented" and returns true unconditionally. This handler is needed for Mission 2+ lava tube construction tasks (airlock import, I-beam printing, floor prep, sealing/pressurization).

The current 3 phases (power_comms, isru_deployment, gas_processing) do NOT use `construct_structure` effects — they only use `deploy_unit`, `connect_units`, and `set_unit_state`. This is intentionally deferred to Mission 2+.

## Problem Statement

**Current behavior**: Stub returns true without creating any structure records.
**Expected behavior**: Create a structure record (e.g., `Units::Structure`) at the settlement, link it to the blueprint, and return success.

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Engine with stub handler | `construct_structure_from_effect` line ~467 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/blueprints/structures/*` | Blueprint structure for I-beams, airlocks, etc. |
| `galaxy_game/app/models/units/structure.rb` | Structure model (if exists) |

## Implementation Steps

### Step 1 — Verify no current usage
```bash
grep -r "construct_structure" /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions/tasks_v2/*.json
```
Expected: No results from the current 3 phases. Confirm it's only needed for future missions.

### Step 2 — Design and implement
- Check if `Units::Structure` model exists; if not, check what structure records use
- Implement real logic: create structure record at settlement, link to blueprint
- Raise `InfrastructureSequenceError` if blueprint not found or settlement doesn't exist
- Follow existing error pattern (not logging + returning true)

### Step 3 — Verify
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission:execute 2>&1 | tail -5'
```
Expected: Still 13/13 passing (no regression from stub removal).

## Acceptance Criteria
- [ ] `construct_structure_from_effect` creates a structure record at the settlement
- [ ] Raises `InfrastructureSequenceError` if blueprint not found
- [ ] No regression in existing 13/13 Luna V2 rake suite
- [ ] Follows existing error pattern (no silent returns)

## Stop Conditions — escalate to user immediately if:
- No structure model exists and you're unsure what to create
- Implementation requires changes to more than the one handler method
- Existing rake tests fail after implementation

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb
git commit -m "feat: implement construct_structure_from_effect for lava tube construction"
git push
```

## Dependencies
**Blocked by**: none
**Blocks**: Mission 2 scoping (lava tube floor prep, airlock import, I-beam printing)
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
