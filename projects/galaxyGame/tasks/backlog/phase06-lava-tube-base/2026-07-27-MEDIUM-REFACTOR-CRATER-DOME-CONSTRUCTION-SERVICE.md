---
status: backlog
priority: MEDIUM
type: refactor
system_domain: MANUFACTURING
mvp_alignment: LUNA_SETTLEMENT
phase: 6
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md \
         projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-27-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: CraterDomeConstructionService Implementation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Context

The `CraterDomeConstructionService` is currently a stub with only basic job creation and status updates. This service is responsible for orchestrating crater dome construction workflows, including material calculations, equipment sequencing, and construction execution.

See the AI Manager Service Inventory for detailed context: [ai_manager_service_inventory.md](../../services/ai_manager_service_inventory.md#known-incomplete-services)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Use DomeCalculator for material requirements**
- ❌ Wrong: Hard-code material quantities in the service
- ✅ Right: Delegate material calculation to `DomeCalculator` class
- Why: Calculator encapsulates dome-specific material formulas; changes in material requirements update only in one place

⚠️ **GOTCHA 2: Equipment must be available BEFORE creating construction jobs**
- ❌ Wrong: Create jobs first, check equipment availability later
- ✅ Right: Validate equipment via `EquipmentRegistry` before creating jobs
- Why: Jobs can fail if equipment isn't available, leaving the settlement in an inconsistent state

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Methods |
|---|---|---|
| `app/services/manufacturing/construction/crater_dome_construction_service.rb` | Crater dome-specific construction orchestration | `build_crater_dome`, `install_dome_panels` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/manufacturing/construction/dome_calculator.rb` | Material calculation logic for crater domes |
| `app/services/manufacturing/construction/station_construction_service.rb` | Pattern example for construction services |
| `spec/services/manufacturing/construction/` | RSpec test patterns |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md \
       projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md
```

Then edit and change `status: backlog` to `status: active`.

Verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md"
```

Expected: exactly one result at `tasks/active/`

### Step 1 — Read DomeCalculator

Understand how `DomeCalculator.calculate_materials` works and what it returns. This will drive the material requirements in construction jobs.

### Step 2 — Implement build_crater_dome

Replace TODO with:
1. Calculate materials using `DomeCalculator`
2. Validate settlement inventory has required materials
3. Create construction job with status `:in_progress`
4. Return job reference for tracking

### Step 3 — Implement install_dome_panels

Replace TODO with:
1. Retrieve panels from inventory
2. Sequence installation steps (foundation, rings, cap)
3. Update job status as steps complete
4. Handle equipment allocation/deallocation

### Step 4 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/construction/crater_dome_construction_service_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria

- [ ] `build_crater_dome` creates job with correct material requirements
- [ ] `install_dome_panels` sequences installation steps
- [ ] Materials validated before job creation
- [ ] Job status tracking works end-to-end
- [ ] RSpec isolated run: 0 failures

---

## Stop Conditions

- Root cause in shared construction concern
- Requires database migration
- Same failure persists after two attempts

---

## Commit Instructions

```bash
git add app/services/manufacturing/construction/crater_dome_construction_service.rb
git commit -m "refactor: CraterDomeConstructionService — implement dome construction workflow"
git push

# Task file closure
git mv projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-MEDIUM-REFACTOR-CRATER-DOME-CONSTRUCTION-SERVICE.md
git commit -m "chore: move CRATER-DOME-CONSTRUCTION-SERVICE to completed/"
git push
```

---

## Dependencies

**Blocked by**: ConstructionManager implementation
**Blocks**: Phase 6 worldhouse construction validation
**Related tasks**: StationConstructionService
