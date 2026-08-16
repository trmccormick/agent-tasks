---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: false
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md \
         projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: HarvesterCompletionJob — Oxygen/Fixture Seeding Investigation
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-16
**Last Updated**: 2026-08-16

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS (structurally) — but see MVP Impact Note, Problem Statement is intentionally incomplete
- **Docker Wrapper Check**: N/A — no specs identified yet
- **MVP Alignment**: VALID (assumed — HarvesterCompletionJob implies ISRU/resource-production relevance) but unconfirmed
- **MVP Impact Note**: **This task file is a placeholder.** The originating chat summary did not include enough detail — what specifically fails, what the fixture currently seeds, what "oxygen issue" means concretely — for a real Problem Statement to be written at Claude's architecture tier. First step must be pulling real detail from the 2026-08-13 fixture-bundle task's synthesis report/completion notes before this can be scoped like a normal task.
- **Action Line**: NEEDS MANUAL REVIEW — do not dispatch for implementation until Problem Statement below is filled in with real detail.

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: TBD — not ready for dispatch
**Why This Agent**: N/A
**Local attempts before cloud**: N/A
**Supervision Level**: N/A — task not yet real enough to dispatch

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Prior context**: `summaries/2026-08-16-TEST-FIXTURE-BUNDLE.md` (or equivalent synthesis/completion report from the 2026-08-13 fixture-bundle task's close-out) — **read this before anything else on this task; it may contain the detail this file is currently missing.**

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work — but see Problem Statement below; a real synthesis may not be possible until prerequisite #4 is read.

---

## Context

While closing out the 2026-08-13 fixture-bundle task, the implementing agent flagged a new item (#9, not part of the original enumerated fixture list) involving `HarvesterCompletionJob` and an oxygen-related discrepancy, described only as needing "investigation of fixture seeding and job queue advancement." This was correctly stopped and escalated rather than fixed inline, since it was out of scope for a LOW-priority stale-fixture cleanup task.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials
N/A

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Don't skip straight to a fix.
- ❌ Wrong: attempting a code change based on the vague "oxygen issue" description alone.
- ✅ Right: first pass is pure diagnosis — read the fixture-bundle synthesis report, reproduce the actual failure, and write a real Problem Statement before touching any code.
- Why: this task file was drafted without enough detail to know what's actually broken; guessing at a fix risks solving the wrong problem.

⚠️ **GOTCHA 2**: Watch for overlap with recently-completed PVE oxygen-output work.
- ❌ Wrong: assuming this is fully isolated from other oxygen-producing code paths.
- ✅ Right: check whether this connects to the PVE Mk2/Mk3 output_resources fix (2026-08-15, commit `9568d152`) before proceeding — the project's ISRU chain (TEU/PVE) actively produces oxygen as a tracked output, and this may be the same resource-output path surfacing differently.
- Why: two independent fixes to the same underlying resource-output logic could conflict or duplicate work.

### Multi-Domain / Multi-Tenant Routing
N/A

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: HarvesterCompletionJob — Oxygen/Fixture Seeding Investigation
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
[State clearly: this first pass is diagnosis only. Describe how you will reproduce the actual failure and what you expect to find.]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `path/to/file` | [description] | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read prior fixture-bundle synthesis report for original context on item #9
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file

### Expected Outcomes
[This first pass: a real, specific Problem Statement — not a fix.]

### Critical Gotchas I Will Avoid
- ❌ Attempting a fix before diagnosis is complete — instead ✅ diagnose first, write up findings
- ❌ Assuming isolation from PVE oxygen-output work — instead ✅ explicitly check for overlap

---

**SYNTHESIS COMPLETE.** Ready to proceed with diagnosis only.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

**[FILL IN — not yet known.]** The only information available: `HarvesterCompletionJob` has an "oxygen issue" connected to fixture seeding and job queue advancement, per a one-line flag from the 2026-08-13 fixture-bundle task's close-out. What specifically fails (wrong output amount? missing resource? job not advancing at all?), what the fixture currently seeds, and what the expected-vs-actual behavior is are all unconfirmed. **The first implementation pass on this task must be diagnosis: reproduce the failure, write a real Problem Statement, and stop there for review before attempting any fix** (see Stop Conditions).

**Error output**: Not yet captured.
**Current behavior**: Unknown — needs reproduction.
**Expected behavior**: Unknown — needs to be determined once actual behavior is understood.

---

## Files Involved

**[FILL IN — Qwen/implementation agent to locate `HarvesterCompletionJob` and its associated spec/fixture files via terminal access, and confirm the actual failure mode before any fix is attempted. Claude has no filesystem access and cannot verify these directly.]**

### Migration
- [x] No migration needed (unconfirmed — revisit once root cause is known)

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
See Minimal Handoff block above for exact commands.

### Step 1 — Read prior context
Locate and read the 2026-08-13 fixture-bundle task's synthesis report / completion notes for whatever detail exists on item #9. If none exists beyond the one-line summary already captured here, note that explicitly.

### Step 2 — Reproduce the actual failure
Find `HarvesterCompletionJob` and its spec(s). Run the relevant specs and capture real failure output.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

### Step 3 — Write a real Problem Statement
Based on Steps 1-2, write up what's actually happening: current behavior, expected behavior, exact error output. **Stop here and report back — do not proceed to a fix in this same pass** (per Stop Conditions below).

### Step 4 — Synthesis Report (diagnosis findings, before any fix)

```
SYNTHESIS REPORT
Spec: [file:line]
Error: [exact message]
Expected: [value]
Got: [value]

ROOT CAUSE
[one paragraph — only if confidently identified; otherwise state what's still unknown]

PROPOSED FIX
[only if root cause is clear; otherwise leave for a follow-up task/session]

RISK
[note any connection to PVE oxygen-output work found during investigation]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] Real Problem Statement written (current vs. expected behavior, exact error output)
- [ ] Confirmed or ruled out: overlap with PVE Mk2/Mk3 oxygen-output fix (`9568d152`)
- [ ] Root cause identified, OR explicitly reported as still unknown with findings documented
- [ ] If a fix is confidently identified and approved: isolation run 0 failures, no regressions
- [ ] Full suite run completed and logged if a fix was applied (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- The first diagnosis pass does not produce a clear, specific Problem Statement — report findings and stop rather than guessing at a fix
- Any overlap with the PVE oxygen-output fix (`9568d152`) is found — flag and stop rather than fixing both paths independently
- Root cause touches a shared concern, base class, or factory used across many specs
- Fix requires changing more files than a narrowly-scoped follow-up would justify
- Any architectural decision is required

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: HarvesterCompletionJob oxygen fixture — [brief description]"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md
git commit -m "chore: move 2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md to completed/"
```

> If diagnosis-only and a real fix needs its own follow-up task instead: do NOT move to completed/. Report back to planning agent with findings so a properly-scoped follow-up task can be drafted.

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none identified
**Related tasks**: 2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md (completed/2026-08/ — this task is spun off from its item #9); possible overlap with PVE Mk2/Mk3 oxygen fix (`9568d152`, see Gotcha 2)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change, or "diagnosis only, no fix applied" if that's as far as this pass got]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
