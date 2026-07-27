---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md \
         projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-27-REFACTOR-SYSTEM-ARCHITECT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: SystemArchitect Implementation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Context

The `SystemArchitect` service is a PARTIAL STUB. Core ROI (Return on Investment) logic exists and is functional, but methods `trigger_mass_dump` and `retrieve_assets_to_sol_anchor` are TODOs. This service evaluates system profitability and manages mass-based economic strategies.

See the AI Manager Service Inventory for detailed context: [ai_manager_service_inventory.md](../../services/ai_manager_service_inventory.md#known-incomplete-services)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: ROI logic is verified working — only implement the two TODO methods**
- ❌ Wrong: Rewrite or refactor existing ROI calculation
- ✅ Right: Keep existing ROI logic; implement `trigger_mass_dump` and `retrieve_assets_to_sol_anchor` as new operations
- Why: ROI logic is proven; new work adds operational mechanics, not calculation rewrites

⚠️ **GOTCHA 2: Mass dump and asset retrieval are transactional operations**
- ❌ Wrong: Split the operations across multiple commits
- ✅ Right: Each operation (dump or retrieve) must be atomic — either complete or roll back
- Why: Prevents partial state where assets are moved but records inconsistent

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Methods |
|---|---|---|
| `app/services/ai_manager/system_architect.rb` | System profitability evaluation and mass-based economics | `trigger_mass_dump`, `retrieve_assets_to_sol_anchor` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/` | Other economic evaluation patterns |
| `spec/services/ai_manager/system_architect_spec.rb` | RSpec patterns for architect |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md \
       projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md
```

Edit and change `status: backlog` to `status: active`.

Verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md"
```

Expected: exactly one result at `tasks/active/`

### Step 1 — Audit existing ROI logic

Read the ROI calculation methods to understand what metrics drive profitability decisions. This context informs mass dump and asset retrieval logic.

### Step 2 — Implement trigger_mass_dump

Replace TODO with:
1. Evaluate whether mass dump is economically justified (ROI calculation)
2. Identify assets eligible for dump (location, profitability threshold)
3. Create mass dump operation record
4. Update asset status to `:dumped`
5. Track economic impact (savings/loss)
6. Return operation summary

### Step 3 — Implement retrieve_assets_to_sol_anchor

Replace TODO with:
1. Identify assets currently dumped
2. Evaluate whether retrieval is economically justified
3. Create retrieval operation (vehicle assignment, transit time, cost)
4. Update asset status from `:dumped` to `:in_transit` then to `:active`
5. Track economic impact and timing
6. Return operation summary

### Step 4 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/system_architect_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria

- [ ] ROI logic preserved (no regression)
- [ ] `trigger_mass_dump` is transactional
- [ ] `retrieve_assets_to_sol_anchor` is transactional
- [ ] Economic impact tracked correctly
- [ ] Asset status transitions valid
- [ ] RSpec isolated run: 0 failures

---

## Stop Conditions

- Root cause in shared economic concern
- Same failure persists after two attempts
- Requires database migration

---

## Commit Instructions

```bash
git add app/services/ai_manager/system_architect.rb
git commit -m "refactor: SystemArchitect — implement mass dump and asset retrieval operations"
git push

# Task file closure
git mv projects/galaxy_game/tasks/active/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md
git commit -m "chore: move SYSTEM-ARCHITECT to completed/"
git push
```

---

## Dependencies

**Blocked by**: Economic model implementation
**Blocks**: Phase 13 advanced economic decision-making
**Related tasks**: StateAnalyzer, StrategySelector
