---
status: active
type: documentation
priority: MEDIUM
resume: false
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
title: Clarify Review Gate Authorization Wording in TASK_TEMPLATE.md
created: 2026-10-26
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: agent-tasks
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-26-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-REVIEW-GATE-AUTHORIZATION-WORDING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-26-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-REVIEW-GATE-AUTHORIZATION-WORDING.md \
         projects/agent-tasks/tasks/active/2026-10-26-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-REVIEW-GATE-AUTHORIZATION-WORDING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/agent-tasks/tasks -name "2026-10-26-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-REVIEW-GATE-AUTHORIZATION-WORDING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/agent-tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# Task: Clarify Review Gate Authorization Wording in TASK_TEMPLATE.md

## Concern
The authorization wording in `TASK_TEMPLATE.md` needs to clearly separate commit and push authorizations, explicitly require human messages authorizing each action, remove references to "completion" in bundled phrasing, and state when confirming an agent's approval prompt counts as authorization.

## Out of Scope
- The four distinct meanings of "stop" in workflow documentation
- Draft-review checklist updates or additions
- Updating examples in `status.md`

## File to Edit
- `TASK_TEMPLATE.md`

## Implementation Steps

1. Locate item 2 in `TASK_TEMPLATE.md` and apply Edit 1.

Exact old text:
```markdown
2. **Human has authorized commit/push/completion** — even after reviewer GO, you may not proceed to commit or push without explicit human authorization.
```

Exact new text:
```markdown
2. **Human authorization** — Requires an explicit human message that authorizes commit (for example, 'commit it') and an explicit human message that authorizes push (for example, 'push it'). Authorization to commit does not imply authorization to push, and authorization to push does not imply authorization to commit.

Confirming a command in an agent's own approval prompt counts as authorization only after a reviewer GO is on record; before a GO, a click does not authorize commit or push.
```

2. Locate item 7 in `TASK_TEMPLATE.md` and apply Edit 2.

Exact old text:
```markdown
7. If **GO**: wait for explicit human authorization to commit/push/completion
```

Exact new text:
```markdown
7. If **GO**: wait for an explicit human message that authorizes commit. Push needs a separate, later human message that authorizes push.
```

3. Locate the checklist item in `TASK_TEMPLATE.md` and apply Edit 3.

Exact old text:
```markdown
> - [ ] Human has explicitly authorized commit/push/completion
```

Exact new text:
```markdown
> - [ ] Human has explicitly authorized the command I am about to run (commit or push) in a message that names it
```

4. Verify the changes using grep count checks:

```bash
grep -c "commit/push/completion" TASK_TEMPLATE.md
```
Expected output: 0

```bash
grep -c "HUMAN DECISION NEEDED:" TASK_TEMPLATE.md
```
Expected output: 0

```bash
grep -c "Option A" TASK_TEMPLATE.md
```
Expected output: 0

```bash
grep -c "Option B" TASK_TEMPLATE.md
```
Expected output: 0

```bash
grep -c "only after a reviewer GO is on record" TASK_TEMPLATE.md
```
Expected output: 1

5. Run the following commands to confirm expected diff stats and grep counts (4 edits total):

```bash
git diff --stat -- TASK_TEMPLATE.md
```
Expected: `5 insertions(+), 3 deletions(-)` (Edit 1 replaces one line with three; Edit 2 and Edit 3 replace one line each)

```bash
grep -c 'authorized the command I am about to run' TASK_TEMPLATE.md
```
Expected: `0` before edits, `1` after.

## Stop Points
The only allowed stop points during execution are waiting for human authorization before commit, waiting for human authorization before push, encountering an old text not found exactly once, and a check that still fails after one retry.

## Completion Line
Task complete: REVIEW GATE authorization wording updated in TASK_TEMPLATE.md.

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion]

### What was changed
-

### Issues discovered
- 