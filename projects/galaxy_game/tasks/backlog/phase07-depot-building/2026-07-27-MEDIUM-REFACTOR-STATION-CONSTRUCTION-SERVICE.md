---
status: backlog
priority: MEDIUM
type: refactor
system_domain: MANUFACTURING
mvp_alignment: ORBITAL_INFRASTRUCTURE
phase: 7
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md \
         projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-27-REFACTOR-STATION-CONSTRUCTION-SERVICE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: StationConstructionService Implementation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Context

The `StationConstructionService` is currently a stub with only placeholder `puts` statements. This service orchestrates orbital station construction, including material calculations via `StationCalculator`, equipment allocation, and job sequencing for airlocks, docking ports, and utility connections.

See the AI Manager Service Inventory for detailed context: [ai_manager_service_inventory.md](../../services/ai_manager_service_inventory.md#known-incomplete-services)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: StationCalculator provides material requirements**
- ❌ Wrong: Implement material calculations directly in this service
- ✅ Right: Call `StationCalculator.calculate_materials(radius, length, type)` to get materials
- Why: Station design formulas centralized in calculator; easy to adjust specs

⚠️ **GOTCHA 2: Job creation and equipment requests are separate operations**
- ❌ Wrong: Create one big job with all equipment
- ✅ Right: Create construction job, then create separate equipment requests for each major subsystem (airlocks, docking ports, utilities)
- Why: Allows staged equipment allocation and better progress tracking

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Methods |
|---|---|---|
| `app/services/manufacturing/construction/station_construction_service.rb` | Orbital station construction orchestration | `build_station_shell`, `install_airlock`, `install_docking_port` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/manufacturing/construction/station_calculator.rb` | Material/time calculations for orbital stations |
| `app/services/manufacturing/construction/covering_service.rb` | Pattern example for construction service workflow |
| `spec/services/manufacturing/construction/` | RSpec test patterns |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md \
       projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md
```

Edit and change `status: backlog` to `status: active`.

Verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md"
```

Expected: exactly one result at `tasks/active/`

### Step 1 — Read StationCalculator

Understand what parameters it expects and what it returns (materials, time estimate, complexity factors).

### Step 2 — Implement build_station_shell

Replace placeholder `puts` with:
1. Call `StationCalculator.calculate_materials` with station dimensions
2. Validate settlement inventory has required materials
3. Create construction job for shell assembly
4. Create equipment request for each material type
5. Return job reference

### Step 3 — Implement install_airlock

Replace placeholder `puts` with:
1. Retrieve/allocate airlock equipment
2. Create sub-job for airlock installation
3. Update parent job status
4. Track completion

### Step 4 — Implement install_docking_port

Same pattern as airlock: retrieve equipment, create sub-job, update status.

### Step 5 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/construction/station_construction_service_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria

- [ ] `build_station_shell` creates job with correct materials from StationCalculator
- [ ] `install_airlock` and `install_docking_port` create and track sub-jobs
- [ ] Equipment requests created for each major subsystem
- [ ] Inventory validation prevents job creation without materials
- [ ] RSpec isolated run: 0 failures

---

## Stop Conditions

- Root cause in shared construction concern
- Same failure persists after two attempts
- Requires database migration

---

## Commit Instructions

```bash
git add app/services/manufacturing/construction/station_construction_service.rb
git commit -m "refactor: StationConstructionService — implement orbital station construction workflow"
git push

# Task file closure
git mv projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-MEDIUM-REFACTOR-STATION-CONSTRUCTION-SERVICE.md
git commit -m "chore: move STATION-CONSTRUCTION-SERVICE to completed/"
git push
```

---

## Dependencies

**Blocked by**: ConstructionManager implementation
**Blocks**: Phase 7 orbital infrastructure validation
**Related tasks**: CraterDomeConstructionService
