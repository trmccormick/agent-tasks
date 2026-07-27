---
status: backlog
priority: MEDIUM
type: refactor
system_domain: MANUFACTURING
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md \
         projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-27-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Manufacturing::ProductionService Implementation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Context

The `Manufacturing::ProductionService` is currently a PARTIAL STUB. The PVE (Power Voltage Efficiency) metrics calculation works, but all logistics and consumption steps are TODOs. This service orchestrates ISRU chain for final component production with PVE/TEU (Thermal Energy Unit) cycle calculations.

See the AI Manager Service Inventory for detailed context: [ai_manager_service_inventory.md](../../services/ai_manager_service_inventory.md#known-incomplete-services)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: PVE metrics already implemented — only complete logistics/consumption**
- ❌ Wrong: Rewrite PVE calculation logic
- ✅ Right: Keep existing PVE logic; implement the TODO placeholders for material consumption and logistics tracking
- Why: PVE calculation is verified working; new work adds material accounting and job status updates

⚠️ **GOTCHA 2: Consumption must be transactional**
- ❌ Wrong: Consume materials after production
- ✅ Right: Validate materials available, consume them atomically at start of production cycle
- Why: Prevents double-consumption and partial production states

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Methods |
|---|---|---|
| `app/services/manufacturing/production_service.rb` | ISRU chain orchestration for final component production | `manufacture_component`, `run_unit_cycle` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/manufacturing/material_request_system.rb` | Material request workflow pattern |
| `app/services/manufacturing/processing.rb` | Transaction pattern for material consumption |
| `spec/services/manufacturing/` | RSpec test patterns |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md \
       projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md
```

Edit and change `status: backlog` to `status: active`.

Verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md"
```

Expected: exactly one result at `tasks/active/`

### Step 1 — Audit existing PVE logic

Read the `run_unit_cycle` method to understand what's already implemented (PVE metrics). Keep this logic; only add logistics around it.

### Step 2 — Implement material consumption

In the TODO sections, add:
1. Material requirement lookup (what materials needed for this production cycle)
2. Inventory validation (do we have the materials)
3. Atomic consumption (deduct materials from settlement inventory)
4. Error handling (escalate if materials insufficient)

### Step 3 — Implement logistics tracking

Add:
1. Job creation for each production step
2. Status updates as cycle progresses
3. Byproduct handling (any waste products)
4. Completion tracking and reporting

### Step 4 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/production_service_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria

- [ ] PVE metrics calculation still works (no regression)
- [ ] Material consumption is transactional
- [ ] Logistics tracked via Job records
- [ ] Byproducts handled correctly
- [ ] RSpec isolated run: 0 failures

---

## Stop Conditions

- Root cause in shared ISRU concern
- Same failure persists after two attempts
- Requires database migration

---

## Commit Instructions

```bash
git add app/services/manufacturing/production_service.rb
git commit -m "refactor: Manufacturing::ProductionService — implement material consumption and logistics tracking"
git push

# Task file closure
git mv projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-MEDIUM-REFACTOR-MANUFACTURING-PRODUCTION-SERVICE.md
git commit -m "chore: move MANUFACTURING-PRODUCTION-SERVICE to completed/"
git push
```

---

## Dependencies

**Blocked by**: Material request system implementation
**Blocks**: Phase 5 Luna ISRU validation
**Related tasks**: Processing, MaterialRequestSystem
