---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md \
         projects/galaxy_game/tasks/active/2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-15-MEDIUM-REFACTOR-SUPERMARS*"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Remove SuperMarsSettlementService — Dead Code Cleanup
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context

`SuperMarsSettlementService` is a thought exercise that was written to explore "what if Mars had no moons" scenarios. It was never wired into the codebase — it's a standalone class with zero references anywhere else. This task removes it as dead code.

**Verification**: `grep -r SuperMarsSettlementService galaxy_game/` returns only 1 match (the class definition itself). No controllers, services, specs, or other files reference it.

---

## Problem Statement

**Current state**: `SuperMarsSettlementService` exists as an isolated class with no callers, no tests, and no integration points. It's dead code that adds maintenance burden.

**Expected state**: Service removed; any related thought-exercise documentation archived or deleted.

---

## Architecture Gotchas

⚠️ **GOTCHA 1: Verify zero references before deleting**
- ❌ Wrong: Delete the file without confirming no callers exist
- ✅ Right: Run `grep -r SuperMarsSettlementService galaxy_game/` and confirm only 1 match (the class definition)
- Why: If a caller exists, deletion would cause a runtime error

⚠️ **GOTCHA 2: Check for related specs**
- ❌ Wrong: Only check app/ directory
- ✅ Right: Also check spec/ for any test files referencing this service
- Why: Specs may exist even if app code doesn't reference it

---

## Files Involved

### Primary Files — you will delete these
| File | Purpose | Action |
|---|---|---|
| `galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb` | Dead code — thought exercise only | DELETE |

### Reference Files — check but do not edit
| File | Why You Need It |
|---|---|
| Run `grep -r SuperMarsSettlementService galaxy_game/` | Verify zero references before deletion |
| Run `grep -r super_mars_settlement galaxy_game/spec/` | Check for any spec files referencing it |

### Migration
- [x] No migration needed — this is a service file removal only

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md \
       projects/galaxy_game/tasks/active/2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Verify zero references

Run these commands and paste output in your synthesis report:

```bash
grep -r SuperMarsSettlementService galaxy_game/ 2>&1
grep -r super_mars_settlement galaxy_game/spec/ 2>&1
```

**Expected result**: First command returns exactly 1 match (the class definition). Second command returns no matches.

### Step 2 — Delete the dead code file

```bash
rm galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb
```

### Step 3 — Verify no breakage

Run any related specs to confirm nothing breaks:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/services/ai_manager/ \
  --exclude-pattern "**/super_mars*" 2>&1 | tail -20
```

Expected: No new failures.

### Step 4 — Synthesis Report (before committing)

```
SYNTHESIS REPORT
Files deleted:
  - galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb

Verification results:
  [paste grep output — should show 1 match for class definition only]
  
Specs run:
  [paste test output — should show no new failures]

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `grep -r SuperMarsSettlementService galaxy_game/` confirms only 1 match (class definition) before deletion
- [ ] No spec files reference this service
- [ ] File is deleted from the codebase
- [ ] No new RSpec failures introduced by removal
- [ ] Git diff shows only one file deleted

---

## Stop Conditions — escalate to user immediately if:
- `grep` finds more than 1 reference to SuperMarsSettlementService (a caller exists)
- Any spec references this service
- Same failure persists after two attempts

---

## Commit Instructions

Run git commands on **host**, not inside container:

```bash
git rm galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb
git commit -m "refactor: remove SuperMarsSettlementService — dead code, thought exercise only"
git push
```

---

## Documentation
- [ ] No additional doc changes needed

---

## Dependencies
**Blocked by**: none
**Blocks**: None (cleanup task)
**Related tasks**: 
- `2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md` — was wired into SuperMars in original Task C, but should be removed from that task instead

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- Deleted `galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb` (dead code)

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: SuperMarsSettlementService removed as dead code | Zero references confirmed | No regression