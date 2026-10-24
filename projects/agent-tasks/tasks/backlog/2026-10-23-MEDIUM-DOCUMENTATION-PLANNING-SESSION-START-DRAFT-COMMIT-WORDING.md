---
status: backlog
resume: false
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: agent-tasks
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING.md \
         projects/agent-tasks/tasks/active/2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/agent-tasks/tasks -name "2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/agent-tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: PLANNING_AGENT_SESSION_START.md — draft commit wording (6 line edits, one file)
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-10-23

## Context

`PLANNING_AGENT_SESSION_START.md` still lets a planning agent commit and push a draft on its own, and its lifecycle diagram still has a `[handoff]` step. Both conflict with decisions in `projects/agent-tasks/NEEDS_REVIEW.md` (entry "Draft-gate commit 442a462", Status line, 2026-10-22): (a) a planner commits and pushes a draft only when the human tells it to, so web review agents can read the repo, and the draft stays in `drafts/` until the human approves; (d) `[handoff]` is retired as a lifecycle step, and `completed/` keeps its month folders.

This task fixes only `PLANNING_AGENT_SESSION_START.md`. Rule 12 in `rules/GUARDRAILS.md` and `TASK_TEMPLATE.md` are separate tasks.

## Gotchas

- Docs only. No Docker, no tests, no Ruby.
- Use the Edit tool with the exact old text below, one edit per step. Do not rewrite the whole file.
- Match the old text without the trailing spaces some lines end with; leave those spaces alone.
- Do not touch any other line, even ones that mention committing or pushing (for example lines 30, 166 and 239). They are out of scope.
- Do not touch any other file.

## Steps

Only stop at: (a) an old text that is not found exactly once, (b) a check that still fails after one retry, (c) the review gate. At every other point continue to the next step without asking.

1. **Step 1 — session rule 7, heading (line 15).** Replace this text:
   old: `7. **Planning agents commit draft tasks; humans control approval gates** (GUARDRAILS Rule 12).`
   new: `7. **Planning agents commit and push draft tasks only when the human tells them to; humans control approval gates** (GUARDRAILS Rule 12).`

2. **Step 2 — session rule 7, first bullet (line 16).** Replace this line:
   old: `   - You may commit draft tasks to git (makes them reviewable)`
   new: `   - When the human tells you to, commit and push a draft task so web review agents can read it from git; until then leave it uncommitted and say so`

3. **Step 3 — session rule 7, last bullet (line 19).** Replace this line:
   old: `   - Show what you've committed and wait for human approval before proceeding`
   new: `   - Show what you committed and pushed, and wait for human approval before proceeding`

4. **Step 4 — lifecycle diagram, drop `[handoff]` (line 43).** Replace this line:
   old: `  → tasks/backlog/[SUBFOLDER]/ → [handoff] → tasks/active/ → tasks/completed/`
   new: `  → tasks/backlog/[SUBFOLDER]/ → tasks/active/ → tasks/completed/[YYYY-MM]/`

5. **Step 5 — procedure step 2 (line 61).** Replace this line:
   old: `2. **Commit to git** — make task reviewable from public repo`
   new: `2. **Commit and push, only when the human tells you to** — this makes the task reviewable by web review agents. Until then leave the draft uncommitted and say so.`

6. **Step 6 — key rule summary (line 103).** Replace this line:
   old: `- Create draft task → commit to git → stop and wait`
   new: `- Create draft task → commit and push when the human tells you to → stop and wait`

7. **Step 7 — checks.** Run from the repo root and compare to the expected output:
   ```bash
   grep -c 'commit and push draft tasks only when the human tells them to' PLANNING_AGENT_SESSION_START.md  # expect 1
   grep -c 'Planning agents commit draft tasks;' PLANNING_AGENT_SESSION_START.md                           # expect 0
   grep -c 'You may commit draft tasks to git' PLANNING_AGENT_SESSION_START.md                             # expect 0
   grep -c 'Show what you committed and pushed' PLANNING_AGENT_SESSION_START.md                            # expect 1
   grep -c '\[handoff\]' PLANNING_AGENT_SESSION_START.md                                                   # expect 0
   grep -c 'tasks/completed/\[YYYY-MM\]/' PLANNING_AGENT_SESSION_START.md                                  # expect 1
   grep -c 'Commit and push, only when the human tells you to' PLANNING_AGENT_SESSION_START.md             # expect 1
   grep -c '2. \*\*Commit to git\*\*' PLANNING_AGENT_SESSION_START.md                                      # expect 0
   grep -c 'commit and push when the human tells you to → stop and wait' PLANNING_AGENT_SESSION_START.md   # expect 1
   grep -c 'Create draft task → commit to git' PLANNING_AGENT_SESSION_START.md                             # expect 0
   git diff --stat   # expect only PLANNING_AGENT_SESSION_START.md, 6 insertions(+), 6 deletions(-)
   ```
   If any count differs, fix that one step and rerun once. If it still differs, stop (condition b).

8. **Step 8 — print this completion line, then go to the review gate:**
   `EDITS DONE: PLANNING_AGENT_SESSION_START.md draft commit wording, 6 edits, checks pass. STOP: requesting reviewer assessment.`

## REVIEW GATE — hard stop

After Step 8, STOP. Paste the Step 7 output and `git diff` in chat. Do not commit, push, move the task file or mark anything complete. Wait for a reviewer disposition (GO / REVISE / STOP) and then a separate message from the human. Commit and push need separate authorization: commit only if the human's message authorizes commit; push only after a later message authorizes push. REVISE: fix the listed items, rerun Step 7, return to this gate. STOP: do nothing further and wait.

## After authorization

- Commit (only after commit is authorized): `git add PLANNING_AGENT_SESSION_START.md` then `git commit -m "docs: planning session start, drafts committed only when told; drop [handoff] step"`. Never `git add .` or `git add -A`.
- Push (only after push is authorized): `git push`, then `git status -sb`.
- Then follow `SESSION_CLOSEOUT.md` only.

## Synthesis report (save as MD file, do not paste in chat)

```
## STATUS SYNTHESIS REPORT
Task: 2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING
Date: YYYY-MM-DD
About to do: six single-line edits in PLANNING_AGENT_SESSION_START.md (lines 15, 16, 19, 43, 61, 103)
Files: PLANNING_AGENT_SESSION_START.md only
Done looks like: Step 7 counts match; git diff --stat shows 6 insertions, 6 deletions, one file
Gotchas: no whole-file rewrite; leave other commit/push lines alone; stop only at (a), (b), (c)
SYNTHESIS COMPLETE.
```

## Acceptance Criteria
- [ ] Step 7 counts all match
- [ ] `git diff --stat` shows only `PLANNING_AGENT_SESSION_START.md`, 6 insertions, 6 deletions
- [ ] Reviewer GO and human authorization recorded before any commit

## Dependencies
**Blocked by**: none
**Blocks**: any later task that edits `PLANNING_AGENT_SESSION_START.md` (planner boundary pointer, guides task)
**Related tasks**: `2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md` (1a); a separate small task for `TASK_TEMPLATE.md`
**Out of scope, for a later task**: line 30 ("commit/push your own changes"), line 166 and line 239 (review sequence ends in "commit") use older wording and may need the same "when told" qualifier.

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion]

### What was changed
-

### Issues discovered
