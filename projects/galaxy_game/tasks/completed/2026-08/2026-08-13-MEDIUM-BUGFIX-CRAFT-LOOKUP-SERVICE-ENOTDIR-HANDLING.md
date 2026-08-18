---
status: active
priority: MEDIUM
type: bug-fix
system_domain: LOGISTICS
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-13
last_updated: 2026-08-14
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md \
         projects/galaxy_game/tasks/active/2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-14-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: CraftLookupService — Handle Errno::ENOTDIR Gracefully
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-13
**Last Updated**: 2026-08-14

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: Original task file was missing most required sections — reformatted 2026-08-14 to match TASK_TEMPLATE.md; content/scope unchanged.
- **Docker Wrapper Check**: [Qwen to confirm — spec exists at spec/services/lookup/craft_lookup_service_spec.rb:186]
- **MVP Alignment**: OTHER — defensive error handling, not blocking any MVP-critical path
- **MVP Impact Note**: Low risk, narrow scope — single rescue clause addition
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Small, well-scoped bug fix — single rescue clause, no design judgment needed
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
`CraftLookupService#find_craft` does not properly handle `Errno::ENOTDIR` errors that can occur when `Dir.glob` encounters a path component that is a file rather than a directory (e.g. a stray file sitting where a directory is expected in the lookup path). The spec at `spec/services/lookup/craft_lookup_service_spec.rb:186` already mocks this scenario and expects `find_craft` to return `nil` gracefully rather than raising.

The service already handles `Errno::ENOENT` and has some `File.directory?` checks — this is a narrow gap in the same error-handling family, not a new pattern.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Confirm the exact rescue scope.
- ❌ Wrong: Add a bare `rescue => e` that swallows all errors — this would mask genuinely unexpected failures, not just the ENOTDIR case.
- ✅ Right: Rescue `Errno::ENOTDIR` specifically, alongside (not replacing) the existing `Errno::ENOENT` rescue.
- Why: Overly broad rescues hide real bugs; this task is about one specific, well-understood failure mode.

⚠️ **GOTCHA 2**: Check whether the existing spec mock should be removed or kept.
- The task's acceptance criteria note the spec at line 186 should pass "without mocking (or mock is removed if service handles it natively)" — confirm which is cleaner given the actual code structure once you're in the file, and note the decision in your completion report.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, create and post a **synthesis report** in chat. This demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/lookup/craft_lookup_service.rb` | Add ENOTDIR rescue | not started |
| `spec/services/lookup/craft_lookup_service_spec.rb` | Existing spec at line 186 | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Bare rescue that swallows all errors — instead ✅ rescue Errno::ENOTDIR specifically

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
`CraftLookupService#find_craft` does not rescue `Errno::ENOTDIR`, which can occur when `Dir.glob` encounters a path component that is a file rather than a directory. The existing spec at line 186 mocks this and expects a graceful `nil` return, but the service currently has no handling for it — it will raise instead.

**Current behavior**: `Dir.glob` raising `Errno::ENOTDIR` propagates unhandled, crashing the lookup instead of returning `nil`.
**Expected behavior**: `find_craft` catches `Errno::ENOTDIR` (alongside the existing `Errno::ENOENT` handling) and returns `nil`.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/lookup/craft_lookup_service.rb` | Craft lookup by Dir.glob | `#find_craft` — add ENOTDIR rescue |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/services/lookup/craft_lookup_service_spec.rb` (line 186) | Existing test for this exact scenario — confirm it passes after the fix |

### Migration (if needed)
- [ ] None needed — pure error-handling addition, no schema/data changes

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
[Standard — see Minimal Handoff above]

### Step 1 — Locate the current error handling in find_craft
Read the method, confirm exactly how `Errno::ENOENT` is currently rescued and where `Dir.glob` is called, so the new rescue matches the existing style/structure.

### Step 2 — Add Errno::ENOTDIR handling
Add a rescue for `Errno::ENOTDIR` alongside the existing `Errno::ENOENT` rescue (same begin/rescue block, additional exception class — do not duplicate the whole rescue structure). Return `nil` on this error, consistent with the existing pattern.

### Step 3 — Reconcile the spec
Check spec/services/lookup/craft_lookup_service_spec.rb:186. Decide whether the existing mock is still needed (it may now just confirm real behavior rather than simulate it) — per GOTCHA 2, note your decision and reasoning in the completion report.

### Step 4 — Verify
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/services/lookup/craft_lookup_service_spec.rb --format progress 2>&1 | tail -30
```
Expected result: all examples passing, 0 failures, including the ENOTDIR case.

### Step 5 — Synthesis Report (before committing anything)
```
SYNTHESIS REPORT
File: [file:line]

ROOT CAUSE
[one paragraph]

PROPOSED FIX
[exact code change]

RISK
[low — scoped to one method's error handling]

READY TO APPLY? — waiting for approval
```
Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] `find_craft` rescues `Errno::ENOTDIR` and returns `nil`
- [ ] Existing spec at line 186 passes (mocked or native — document which)
- [ ] No regression in other error handling paths (`Errno::ENOENT`, `File.directory?` checks)
- [ ] Full craft_lookup_service_spec.rb suite: 0 failures

---

## Stop Conditions — escalate to user immediately if:
- The fix requires touching more than the one method's error handling
- `Errno::ENOTDIR` handling reveals a deeper structural issue with how `Dir.glob` is used across the service (not just this one method)
- Any other spec in the suite regresses

---

## Commit Instructions
```bash
git add galaxy_game/app/services/lookup/craft_lookup_service.rb spec/services/lookup/craft_lookup_service_spec.rb
git commit -m "bug-fix: CraftLookupService — rescue Errno::ENOTDIR in find_craft"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md
git commit -m "chore: move 2026-08-13-MEDIUM-BUGFIX-CRAFT-LOOKUP-SERVICE-ENOTDIR-HANDLING.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `craft_lookup_service.rb` — [description]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
