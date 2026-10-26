---
status: draft
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
  git mv projects/agent-tasks/tasks/drafts/2026-10-26-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-REVIEW-GATE-AUTHORIZATION-WORDING.md \
         projects/agent-tasks/tasks/backlog/2026-10-26-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-REVIEW-GATE-AUTHORIZATION-WORDING.md
  Then open the moved file and change: status: draft → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: draft → active → completed
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
The authorization wording in `TASK_TEMPLATE.md` needs to clearly separate commit and push authorizations, explicitly require human messages authorizing each action, remove references to "completion" in bundled phrasing, and provide decision options for handling approval prompts.

## Out of Scope
- The four distinct meanings of "stop" in workflow documentation
- Draft-review checklist updates or additions
- Updating examples in `status.md`

## File to Edit
- `TASK_TEMPLATE.md`

## Implementation Steps

1. Locate item 2 in `TASK_TEMPLATE.md` and make Edit 1 and Edit 4.

Exact old text:
```markdown
2. **Human has authorized commit/push/completion** — even after reviewer GO, you may not proceed to commit or push without explicit human authorization.
```

Exact new text:
```markdown
2. **Human authorization** — Requires an explicit human message that authorizes commit (for example, 'commit it') and an explicit human message that authorizes push (for example, 'push it'). Authorization to commit does not imply authorization to push, and authorization to push does not imply authorization to commit.

HUMAN DECISION NEEDED: keep exactly one option before this task is approved.
- Option A (existing decision, 2026-10-20): Confirming the command in an agent's own approval prompt counts as approval.
- Option B: Confirming the command in an agent's own approval prompt counts as approval only after a reviewer GO is on record.
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
Expected output: 1

```bash
grep -c "Option A" TASK_TEMPLATE.md
```
Expected output: 1

```bash
grep -c "Option B" TASK_TEMPLATE.md
```
Expected output: 1

5. Run the following commands to confirm expected diff stats and grep counts (4 edits total):

```bash
git diff --stat -- TASK_TEMPLATE.md
```
Expected: `TASK_TEMPLATE.md | 8 +4 -4` (4 insertions, 4 deletions across 4 separate edits — items 2a+2b each add one line for the HUMAN DECISION NEEDED block; item 7 adds one line; checklist adds one line)

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

**Completed by**: Qwen
**Completion date**: 2026-10-26
**Evidence basis:** review of pasted evidence (reviewer GO on file hash TBD, commit TBD)

### What was changed
- four single-line edits in TASK_TEMPLATE.md (items 2a+2b, item 7, checklist item)

### Issues discovered
- 