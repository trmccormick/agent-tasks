---
status: backlog
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
**Last Updated**: 2026-07-26

## Problem

`InfrastructureCostCalculator` (`galaxy_game/app/services/economics/infrastructure_cost_calculator.rb`) is a dead class:

1. **Zero live callers** — grep across `app/`, `lib/`, `config/` returns only the class definition itself. No service, controller, or model instantiates it.
2. **No spec coverage** — grep `spec/` for `InfrastructureCostCalculator` returns zero matches.
3. **Only exercised by a stray test script** — `galaxy_game/test_realistic_costs.rb` (outside the RSpec suite) is the sole consumer.
4. **Was broken** — line 152 called `PrecursorCapabilityService.can_produce?(destination, material.chemical_formula)` with wrong arity (the method doesn't exist; only `can_produce_locally?(resource)` does). This was fixed in this task but nothing caught it because there are no specs.

## Current State

- ✅ Signature fix applied: `can_produce?(destination, ...)` → `can_produce_locally?(material.chemical_formula)`
- ⏳ Still needs: decision on whether the class is wanted, and if so, spec coverage

## What Needs to Happen

1. **Decision**: Is `InfrastructureCostCalculator` still needed?
   - If yes → write specs covering `calculate_cost` and `calculate_local_production_discount`
   - If no → remove the file and any remaining references

2. **If keeping it**:
   - Create `spec/services/economics/infrastructure_cost_calculator_spec.rb`
   - Test `calculate_cost(infrastructure_type, location, options = {})` with realistic inputs
   - Test `calculate_local_production_discount(blueprint, destination)` including the ISRU capability check
   - Verify the class works end-to-end (no nil errors, correct discount calculations)

3. **If removing it**:
   - Delete `galaxy_game/app/services/economics/infrastructure_cost_calculator.rb`
   - Remove any remaining require/load statements
   - Update architecture docs if they reference this class

## Acceptance Criteria
- [ ] Decision made: keep or remove InfrastructureCostCalculator
- [ ] If kept: specs exist with >80% coverage of public methods
- [ ] If removed: file deleted, no broken requires remain
- [ ] Architecture doc updated to reflect current state

## Notes
- This is low priority because nothing depends on this class today
- The signature fix was applied as a correctness measure even though nothing exercises it
- Before trusting this class for any real cost calculation, it needs actual specs plus the keep/remove decision
