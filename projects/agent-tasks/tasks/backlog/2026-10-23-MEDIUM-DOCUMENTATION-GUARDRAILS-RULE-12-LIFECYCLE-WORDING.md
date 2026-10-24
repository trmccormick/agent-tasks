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
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md \
         projects/agent-tasks/tasks/active/2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/agent-tasks/tasks -name "2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/agent-tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: GUARDRAILS Rule 12 — lifecycle wording (5 line edits, one file)
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-10-23

## Context

`rules/GUARDRAILS.md` Rule 12 still has wording that conflicts with decisions recorded in `projects/agent-tasks/NEEDS_REVIEW.md` (entry "Draft-gate commit 442a462", Status line, 2026-10-22). This task fixes only Rule 12 in `rules/GUARDRAILS.md`. Other files are separate tasks.

Decisions applied: (1) `backlog/` holds approved tasks only, grouped by phase or domain, no `drafts` subfolder, no date folders; `completed/` keeps its month folders. (2) A planner commits and pushes a draft only when the human tells it to. (3) A stray duplicate copy of a task file is reported to the human, not deleted.

## Gotchas

- Docs only. No Docker, no tests, no Ruby.
- `rules/GUARDRAILS.md` has a line of about 800 characters. Never rewrite the whole file. Use the Edit tool with the exact old text below, one edit per step.
- Do not touch any other line in the file, including line 269 (`# Paste find output before proceeding`). It is out of scope.
- Do not touch any other file.

## Steps

Only stop at: (a) an old text that is not found exactly once, (b) a check that still fails after one retry, (c) the review gate. At every other point continue to the next step without asking.

1. **Step 1 — completed folder in the lifecycle diagram (line 210).** Replace this line:
   old: `  → tasks/backlog/[SUBFOLDER]/ → tasks/active/ → tasks/completed/`
   new: `  → tasks/backlog/[SUBFOLDER]/ → tasks/active/ → tasks/completed/[YYYY-MM]/`

2. **Step 2 — no `drafts` in backlog subfolders (line 231).** Replace this line:
   old: `  - Organized by domain/phase: `current`, `design`, `deferred-cleanup`, `drafts`, `procedural_generation`, `research`, `superseded`, `ui`, `ai-manager`, etc.`
   new: `  - Approved tasks only. One subfolder per phase or domain (examples: `current`, `design`, `deferred-cleanup`, `procedural_generation`, `research`, `superseded`, `ui`, `ai-manager`). No `drafts` subfolder and no date folders.`

3. **Step 3 — planner commits when told (line 225).** Replace this line:
   old: `  - Committed to git for review access`
   new: `  - Committed and pushed when the human tells the planning agent to, so review agents can read it from git`

4. **Step 4 — planner commits updates when told (line 247).** Replace this line:
   old: `- If a planning agent needs to update a draft task after review feedback, it updates the file in-place in `tasks/drafts/` and commits the update; human still controls the move to backlog`
   new: `- If a planning agent needs to update a draft task after review feedback, it updates the file in-place in `tasks/drafts/`. It commits and pushes the update only when the human tells it to; human still controls the move to backlog`

5. **Step 5 — stray copies are reported, not removed (line 281).** Replace this line:
   old: `Only ONE result should exist. If two appear, a stale copy exists — remove it before committing.`
   new: `Only ONE result should exist. If two appear, stop and report both paths to the human. Do not delete, move or overwrite either copy.`

6. **Step 6 — checks.** Run from the repo root and compare to the expected output:
   ```bash
   grep -c 'tasks/completed/\[YYYY-MM\]/' rules/GUARDRAILS.md        # expect 1
   grep -c '`drafts`, `procedural_generation`' rules/GUARDRAILS.md   # expect 0
   grep -c 'No `drafts` subfolder and no date folders' rules/GUARDRAILS.md  # expect 1
   grep -c 'Committed to git for review access' rules/GUARDRAILS.md  # expect 0
   grep -c 'only when the human tells it to' rules/GUARDRAILS.md     # expect 1
   grep -c 'remove it before committing' rules/GUARDRAILS.md         # expect 0
   grep -c 'Do not delete, move or overwrite either copy' rules/GUARDRAILS.md  # expect 1
   git diff --stat                                                   # expect only rules/GUARDRAILS.md, 5 insertions(+), 5 deletions(-)
   ```
   If any count differs, fix that one step and rerun once. If it still differs, stop (condition b).

7. **Step 7 — print this completion line, then go to the review gate:**
   `EDITS DONE: GUARDRAILS Rule 12 lifecycle wording, 5 edits, checks pass. STOP: requesting reviewer assessment.`

## REVIEW GATE — hard stop

After Step 7, STOP. Paste the Step 6 output and `git diff` in chat. Do not commit, push, move the task file or mark anything complete. Wait for a reviewer disposition (GO / REVISE / STOP) and then a separate message from the human. Commit and push need separate authorization: commit only if the human's message authorizes commit; push only after a later message authorizes push. REVISE: fix the listed items, rerun Step 6, return to this gate. STOP: do nothing further and wait.

## After authorization

- Commit (only after commit is authorized): `git add rules/GUARDRAILS.md` then `git commit -m "docs: GUARDRAILS Rule 12 lifecycle wording (drafts, backlog folders, stray copies)"`. Never `git add .` or `git add -A`.
- Push (only after push is authorized): `git push`, then `git status -sb`.
- Then follow `SESSION_CLOSEOUT.md` only.

## Synthesis report (save as MD file, do not paste in chat)

```
## STATUS SYNTHESIS REPORT
Task: 2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING
Date: YYYY-MM-DD
About to do: five single-line edits in rules/GUARDRAILS.md Rule 12 (lines 210, 231, 225, 247, 281)
Files: rules/GUARDRAILS.md only
Done looks like: Step 6 counts match; git diff --stat shows 5 insertions, 5 deletions, one file
Gotchas: no whole-file rewrite; leave line 269 alone; stop only at (a), (b), (c)
SYNTHESIS COMPLETE.
```

## Acceptance Criteria
- [ ] Step 6 counts all match
- [ ] `git diff --stat` shows only `rules/GUARDRAILS.md`, 5 insertions, 5 deletions
- [ ] Reviewer GO and human authorization recorded before any commit

## Dependencies
**Blocked by**: none
**Blocks**: any later task that edits `rules/GUARDRAILS.md` (Rule 26 task)
**Related tasks**: separate small tasks for `PLANNING_AGENT_SESSION_START.md` (Rule 7, Step 2, `[handoff]` at line 43) and `TASK_TEMPLATE.md` (`{YYYY-MM}` at line 17, stale-copy wording at lines 150-152 and 421)

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion]

### What was changed
-

### Issues discovered
