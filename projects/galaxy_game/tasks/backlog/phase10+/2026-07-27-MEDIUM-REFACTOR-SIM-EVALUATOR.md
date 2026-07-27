---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: VENUS_EXPANSION
phase: 10
local_worker_safe: true
---
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md \
         projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-27-REFACTOR-SIM-EVALUATOR.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: SimEvaluator Implementation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Context

The `SimEvaluator` (System Integration Mission Evaluator) is currently a PARTIAL STUB. The Venus station deployment pattern has full step-by-step logic implemented, but it uses temporary `puts` logging and contains TODO placeholders where actual construction queuing should occur. This service evaluates complex orbital deployments using blueprint templates.

See the AI Manager Service Inventory for detailed context: [ai_manager_service_inventory.md](../../services/ai_manager_service_inventory.md#known-incomplete-services)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Venus pattern is reference implementation — keep the logic structure**
- ❌ Wrong: Refactor the Venus pattern implementation
- ✅ Right: Keep Venus pattern logic; replace `puts` with proper logging and replace TODO sections with actual construction job creation
- Why: Venus pattern validates the evaluator design; new work adds operational integration, not logic rewrite

⚠️ **GOTCHA 2: Construction queuing must respect settlement capacity**
- ❌ Wrong: Queue all construction jobs immediately
- ✅ Right: Check settlement construction queue capacity and staging capacity before queuing jobs
- Why: Prevents overload and ensures realistic construction scheduling

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Methods |
|---|---|---|
| `app/services/ai_manager/sim_evaluator.rb` | System Integration Mission evaluation for complex deployments | `evaluate_system_integration`, `deploy_venus_pattern` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/manufacturing/construction/construction_manager.rb` | Construction job creation pattern |
| `spec/services/ai_manager/sim_evaluator_spec.rb` | RSpec patterns for evaluator |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md \
       projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md
```

Edit and change `status: backlog` to `status: active`.

Verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md"
```

Expected: exactly one result at `tasks/active/`

### Step 1 — Audit Venus pattern implementation

Read `deploy_venus_pattern` to understand the step-by-step logic. Identify where `puts` statements occur and where TODOs appear.

### Step 2 — Replace puts logging

Replace temporary `puts` statements with proper Rails logging:
```ruby
Rails.logger.info("Venus Pattern: [descriptive message]")
```

### Step 3 — Implement construction queuing TODOs

In TODO sections, add actual construction job creation:
1. Check settlement construction queue capacity
2. Create ConstructionJob records for each deployment step
3. Queue jobs via `ConstructionManager` or appropriate service
4. Track job IDs for status monitoring
5. Return status summary

### Step 4 — Implement other deployment patterns (if applicable)

Check if there are other planetary deployment patterns beyond Venus. Implement them using Venus pattern as template.

### Step 5 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/sim_evaluator_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria

- [ ] Venus pattern logic preserved (no refactoring, only filling in TODOs)
- [ ] `puts` replaced with proper Rails logging
- [ ] Construction jobs created via ConstructionManager
- [ ] Queue capacity checked before job creation
- [ ] Status tracking implemented
- [ ] RSpec isolated run: 0 failures

---

## Stop Conditions

- Root cause in shared construction concern
- Same failure persists after two attempts
- Requires database migration

---

## Commit Instructions

```bash
git add app/services/ai_manager/sim_evaluator.rb
git commit -m "refactor: SimEvaluator — implement construction queuing for system integration missions"
git push

# Task file closure
git mv projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md
git commit -m "chore: move SIM-EVALUATOR to completed/"
git push
```

---

## Dependencies

**Blocked by**: ConstructionManager implementation
**Blocks**: Phase 10 Venus deployment validation
**Related tasks**: ConstructionManager, StationConstructionService
