---
status: backlog
priority: MEDIUM
type: refactor
system_domain: MANUFACTURING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
phase: 6
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md \
         projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-27-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Manufacturing::ConstructionManager Implementation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Context

The `Manufacturing::ConstructionManager` service is currently a complete stub with both public methods (`orchestrate_construction` and `validate_construction_constraints`) containing only TODO placeholders. This service is responsible for orchestrating the overall construction workflow for different construction types (worldhouses, crater domes, stations).

See the AI Manager Service Inventory for detailed context: [ai_manager_service_inventory.md](../../services/ai_manager_service_inventory.md#known-incomplete-services)

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/architecture/manufacturing/` — manufacturing subsystem overview

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Construction types dispatch to specialized services**
- ❌ Wrong: Implement all construction logic directly in ConstructionManager
- ✅ Right: ConstructionManager orchestrates delegation to `StationConstructionService`, `CraterDomeConstructionService`, `SegmentCoveringService`, etc.
- Why: Each construction type (station, dome, covering) has unique material calculations and job sequencing

⚠️ **GOTCHA 2: Constraints validation must happen BEFORE job creation**
- ❌ Wrong: Create jobs first, validate afterward
- ✅ Right: Call `validate_construction_constraints` at start of `orchestrate_construction`, escalate if constraints violated
- Why: Early validation prevents orphaned jobs and failed material transactions

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Methods |
|---|---|---|
| `app/services/manufacturing/construction/construction_manager.rb` | Main orchestrator for construction workflows | `orchestrate_construction`, `validate_construction_constraints` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/manufacturing/construction/station_construction_service.rb` | Example of specialized construction service pattern |
| `app/services/manufacturing/construction/crater_dome_construction_service.rb` | Example of dome-specific construction logic |
| `spec/services/manufacturing/construction/` | RSpec test patterns for construction services |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md \
       projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md
```

Then edit the file and change `status: backlog` to `status: active`.

Then verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md"
```

Expected: exactly one result at `tasks/active/`

### Step 1 — Understand current stub structure

Read `app/services/manufacturing/construction/construction_manager.rb` to identify:
- What parameters `orchestrate_construction` accepts
- What `validate_construction_constraints` should check
- What exceptions/errors should be raised

### Step 2 — Implement orchestrate_construction

Replace the TODO with:
1. Call `validate_construction_constraints(construction_request)` first
2. Dispatch to appropriate service based on construction type (`:station`, `:crater_dome`, `:covering`, `:segment_covering`)
3. Capture result from delegated service
4. Return construction job with status tracking

### Step 3 — Implement validate_construction_constraints

Replace the TODO with validation logic that checks:
- Required parameters present
- Settlement/site valid
- Materials available (via inventory)
- Equipment available (via equipment registry)
- No conflicting construction jobs in progress

### Step 4 — Verify

Run specs for the service:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/construction/construction_manager_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria

- [ ] `orchestrate_construction` routes to correct specialized service
- [ ] `validate_construction_constraints` properly validates inputs
- [ ] Construction jobs created with correct status tracking
- [ ] No regressions in related construction service specs
- [ ] RSpec isolated run: 0 failures

---

## Stop Conditions — escalate to user immediately if:

- Fix requires changes to factory definitions beyond scope
- Same failure persists after two attempts
- Root cause is in a shared concern used across multiple construction types
- A database migration is needed

---

## Commit Instructions

```bash
git add app/services/manufacturing/construction/construction_manager.rb
git commit -m "refactor: Manufacturing::ConstructionManager — implement orchestration and constraint validation"
git push

# Task file closure
git mv projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION.md
git commit -m "chore: move MANUFACTURING-CONSTRUCTION-MANAGER-IMPLEMENTATION to completed/"
git push
```

---

## Dependencies

**Blocked by**: none
**Blocks**: Phase 6 worldhouse construction validation
**Related tasks**: StationConstructionService, CraterDomeConstructionService implementations

---

## Completion Report

*Filled in by implementing agent*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `app/services/manufacturing/construction/construction_manager.rb` — [specific changes]

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]
