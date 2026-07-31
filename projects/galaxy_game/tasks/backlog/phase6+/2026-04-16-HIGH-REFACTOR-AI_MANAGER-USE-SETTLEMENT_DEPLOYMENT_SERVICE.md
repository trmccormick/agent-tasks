---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: TUTORIAL_ARC
local_worker_safe: true
created: 2026-06-21
last_updated: 2026-07-30
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/review/2026-04-16-HIGH-REFACTOR-AI_MANAGER-USE-SETTLEMENT_DEPLOYMENT_SERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/review/2026-04-16-HIGH-REFACTOR-AI_MANAGER-USE-SETTLEMENT_DEPLOYMENT_SERVICE.md \
         projects/galaxy_game/tasks/active/2026-04-16-HIGH-REFACTOR-AI_MANAGER-USE-SETTLEMENT_DEPLOYMENT_SERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-16-HIGH-REFACTOR-AI_MANAGER-USE-SETTLEMENT_DEPLOYMENT_SERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-04-16-REFACTOR-AI_MANAGER-SETTLEMENT_DEPLOYMENT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: AI Manager — Align with SettlementDeploymentService
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-06-21
**Last Updated**: 2026-07-30

## Problem Statement

`SettlementDeploymentService.establish_from_craft` is the canonical settlement-deployment primitive used by `BaseSettlement` and player-driven deployments. The AI Manager pipeline (via `AutonomousConstructionManager`, `TaskExecutionEngineV2`, and `SystemArchitect`) still creates settlements via `BaseSettlement.create!`, duplicating deployment logic.

**Current state**: 3 places in AI Manager use `BaseSettlement.create!` directly:
- `ai_manager/manager.rb:235` — initial settlement creation
- `ai_manager/task_execution_engine_v2.rb:107` — task-driven settlement creation
- `ai_manager/system_architect.rb:377` — habitat settlement creation

**Expected state**: All AI Manager settlement creation routes through `SettlementDeploymentService.establish_from_craft`.

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: `SettlementDeploymentService.establish_from_craft` requires a `craft` object and `manifest_name` parameter — AI Manager calls may not have these available.
- ❌ Wrong: Replace `BaseSettlement.create!` with `establish_from_craft` without verifying the craft/manifest context exists in each call site
- ✅ Right: Audit each of the 3 call sites to determine what craft/manifest data is available; design a wrapper or adapter if needed
- Why: The canonical service has specific preconditions that AI Manager's autonomous flow may not satisfy directly

⚠️ **GOTCHA 2**: `BaseSettlement.create!` may set fields that `establish_from_craft` does NOT set (or vice versa).
- ❌ Wrong: Blindly swap the method call — some settlement fields may become nil
- ✅ Right: Diff the field sets between `BaseSettlement.create!` and `establish_from_craft`; document any gaps
- Why: A blind swap could silently drop required data (e.g., initial inventory, surface storage setup)

⚠️ **GOTCHA 3**: AI_MANAGER_INTENT.md has forbidden patterns — no direct model manipulation.
- ❌ Wrong: Leave any `BaseSettlement.create!` calls in place without justification
- ✅ Right: Either route through the canonical service OR document why a call site must remain direct (with a TODO to fix)
- Why: This task enforces the AI_MANAGER_INTENT.md constraint against direct model manipulation

---

## REQUIRED Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: AI Manager — Align with SettlementDeploymentService
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| ai_manager/manager.rb:235 | Audit settlement creation call site | [not started / pending / done] |
| ai_manager/task_execution_engine_v2.rb:107 | Audit settlement creation call site | [not started / pending / done] |
| ai_manager/system_architect.rb:377 | Audit settlement creation call site | [not started / pending / done] |
| settlement_deployment_service.rb | Understand establish_from_craft preconditions | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Blindly swapping create! → establish_from_craft without field diff — instead ✅ Audit each call site's available context
- ❌ Leaving any BaseSettlement.create! calls without documented justification — instead ✅ Route through canonical service or document gap
- ❌ Dropping settlement fields during refactoring — instead ✅ Diff field sets between old and new paths

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Acceptance Criteria

- [ ] All 3 `BaseSettlement.create!` calls in AI Manager replaced or documented with justification
- [ ] Design shows how each call site integrates with `SettlementDeploymentService.establish_from_craft`
- [ ] Field set diff confirms no settlement data is lost during refactoring
- [ ] Architecture aligns with AI_MANAGER_INTENT.md forbidden patterns (no direct model manipulation)
- [ ] RSpec specs verify settlement creation works identically before and after refactor
- [ ] Recommendation includes tasks_v2 integration for consistent deployment logic

## Implementation Steps

1. **Audit**: Diff `BaseSettlement.create!` field sets vs `establish_from_craft` field sets at each of the 3 call sites
2. **Design**: Document how each call site should integrate with the canonical service (direct swap, wrapper, or adapter)
3. **Implement**: Replace `BaseSettlement.create!` calls with `SettlementDeploymentService.establish_from_craft` where possible
4. **Test**: Verify settlement creation produces identical results before and after refactor
5. **Document**: Note any call sites that must remain direct (with TODO to fix)

## Blockers
- AI Manager may not have craft/manifest context available at each call site
- `establish_from_craft` expects specific preconditions that autonomous flow may not satisfy

## Dependencies
- **Upstream**: SettlementDeploymentService.establish_from_craft must be stable and well-documented
- **Related**: AI_MANAGER_INTENT.md (forbidden patterns)

## Notes
- This is a HIGH-priority refactor — eliminates duplicated settlement creation logic across the codebase
- The problem was identified in 2026-04-16 but has persisted through multiple iterations
- All 3 call sites are in AI Manager sub-services, not in BaseSettlement itself

## Stop Conditions
- Stop if `establish_from_craft` preconditions cannot be met at any call site without major refactoring — report blocker before proceeding
- Stop if field set diff reveals critical data loss — document gap and halt refactor

## Completion Report

When done, provide:
1. **Files modified**: List all files changed with brief description
2. **Call site status**: For each of the 3 sites — replaced / documented justification / blocker
3. **Field set diff**: Summary of any fields that differ between old and new paths
4. **Test coverage**: Which RSpec specs were added/modified and what they verify
5. **Known limitations**: Any call sites that must remain direct with TODO

## Handoff Summary

**Task**: AI Manager — Align with SettlementDeploymentService
**Status**: backlog → active → completed
**Type**: architecture refactor (3 call sites to audit and fix)
**Key Risk**: establish_from_craft preconditions may not be met at all call sites — requires field set diff before blind swap
**Approach**: Audit each of 3 call sites → diff field sets → replace where possible → document gaps

