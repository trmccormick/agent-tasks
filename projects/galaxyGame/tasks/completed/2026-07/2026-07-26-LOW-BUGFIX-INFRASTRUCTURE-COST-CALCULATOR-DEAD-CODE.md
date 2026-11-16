---
status: active
priority: LOW
type: bug-fix
system_domain: ECONOMICS
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md \
         projects/galaxy_game/tasks/active/2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: InfrastructureCostCalculator has zero live callers and no spec coverage
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: LOW
**Type**: bug-fix
**Created**: 2026-07-26
**Last Updated**: 2026-07-28

Note (2026-07-28): a manual test script referencing this class exists at `galaxy_game/test_realistic_costs.rb` (single commit 90ba2a25, 2026-01-31, swept in under an unrelated 'diameter-based grid sizing for terrain generation' commit message — not touched since). Confirms the class was run/exercised once for cost-calibration purposes but is otherwise dormant; does not change the zero-live-callers-in-app/spec conclusion.

## Problem

`InfrastructureCostCalculator` (`galaxy_game/app/services/economics/infrastructure_cost_calculator.rb`) is a dead class:

1. **Zero live callers** — grep across `app/`, `lib/`, `config/` returns only the class definition itself. No service, controller, or model instantiates it.
2. **No spec coverage** — grep `spec/` for `InfrastructureCostCalculator` returns zero matches.
3. **Only exercised by a stray test script** — `galaxy_game/test_realistic_costs.rb` (outside the RSpec suite) is the sole consumer.
4. **Was broken** — line 152 called `PrecursorCapabilityService.can_produce?(destination, material.chemical_formula)` with wrong arity (the method doesn't exist; only `can_produce_locally?(resource)` does). This was fixed in this task but nothing caught it because there are no specs.

## Current State

- ✅ Signature fix applied: `can_produce?(destination, ...)` → `can_produce_locally?(material.chemical_formula)` (commit 4d68dafe)
- ✅ **Class removed**: `galaxy_game/app/services/economics/infrastructure_cost_calculator.rb` deleted (commit a288a60e)
- ✅ **Test script removed**: `galaxy_game/test_realistic_costs.rb` deleted (commit a288a60e)
- ✅ Decision made: REMOVE — zero live callers, no spec coverage, dead code removal commit applied

## Completion Report

**Decision**: REMOVE (already executed in commit a288a60e)

**Rationale:**
1. Zero live callers in the actual application — nothing depends on this class
2. No spec coverage — the signature bug went undetected because of this
3. The test script is a one-off from 2026-01-31, never integrated into RSpec
4. Keeping dead code creates future maintenance burden and false confidence
5. If needed later, git history preserves the class for reference

**Files removed (commit a288a60e):**
- `galaxy_game/app/services/economics/infrastructure_cost_calculator.rb` (203 lines)
- `galaxy_game/test_realistic_costs.rb` (79 lines)

**Acceptance Criteria:**
- [x] Decision made: remove InfrastructureCostCalculator
- [x] File deleted, no broken requires remain
- [x] Architecture doc updated to reflect current state (NEEDS_REVIEW.md entry RESOLVED)

## Notes
- This work was completed on 2026-08-04 (commit a288a60e) but the task file was never moved to completed/
- Synthesis report exists at: `docs/new_agent/projects/galaxy_game/summaries/2026-08-04-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md`
