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
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING.md \
         projects/agent-tasks/tasks/active/2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/agent-tasks/tasks -name "2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/agent-tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: TASK_TEMPLATE.md — backlog path and stale-copy wording (3 line edits, one file)
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-10-23

## Context

`TASK_TEMPLATE.md` still has three lines that conflict with decisions in `projects/agent-tasks/NEEDS_REVIEW.md` (entry "Draft-gate commit 442a462", Status line, 2026-10-22, and the 2026-10-20 lifecycle entry's Status line):

- Line 17 sends approved tasks to `tasks/backlog/{YYYY-MM}/`. Backlog has no date folders; it holds approved tasks only, grouped by phase or domain (decision d).
- Line 130 lists the allowed dispatch-block substitutions but does not say what to do for a project whose backlog is flat, like `agent-tasks`.
- Line 421 says a stale copy "must be removed first". Stray copies are reported and escalated, and removed only with the human's approval (2026-10-20 decision 4).

This task fixes only those three lines in `TASK_TEMPLATE.md`. Review-gate wording, the synthesis report and the 2026-10-18 contradictions list are separate tasks.

## Gotchas

- Docs only. No Docker, no tests, no Ruby.
- Use the Edit tool with the exact old text below, one edit per step. Do not rewrite the whole file.
- Do NOT edit anything inside the dispatch block code fence (lines 132-162). Step 2 edits a sentence outside it, above the fence.
- Do not touch any other line, including the `[YYYY-MM]` in square brackets in the completed/ paths. Only the curly-brace `{YYYY-MM}` on line 17 changes.
- Do not touch any other file.

## Steps

Only stop at: (a) an old text that is not found exactly once, (b) a check that still fails after one retry, (c) the review gate. At every other point continue to the next step without asking.

1. **Step 1 — approved-task path (line 17).** Replace this line:
   old: `# For APPROVED TASKS: Place in tasks/backlog/{YYYY-MM}/ or tasks/active/ as directed by human after review approval.`
   new: `# For APPROVED TASKS: Place in tasks/backlog/[SUBFOLDER]/ (grouped by phase or domain; no date folders; flat if the project has no subfolders) or tasks/active/ as directed by human after review approval.`

2. **Step 2 — flat backlog substitution (line 130).** Replace this text, which is the end of the sentence on that line:
   old: `Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.`
   new: `Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual; if the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.`

3. **Step 3 — stale copies are reported, not removed (line 421).** Replace this line:
   old: `> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.`
   new: `> ❌ Do NOT proceed if two results appear — stop and report both paths to the human. Do not delete, move or overwrite either copy.`

4. **Step 4 — checks.** Run from the repo root and compare to the expected output:
   ```bash
   grep -c '{YYYY-MM}' TASK_TEMPLATE.md                                                 # expect 0
   grep -c 'no date folders; flat if the project has no subfolders' TASK_TEMPLATE.md    # expect 1
   grep -c 'omit the `\[SUBFOLDER\]/` segment from the paths' TASK_TEMPLATE.md          # expect 1
   grep -c 'must be removed first' TASK_TEMPLATE.md                                     # expect 0
   grep -c 'Do not delete, move or overwrite either copy' TASK_TEMPLATE.md              # expect 1
   git diff --stat -- TASK_TEMPLATE.md   # expect 1 file changed, 3 insertions(+), 3 deletions(-)
   ```
   If any count differs, fix that one step and rerun once. If it still differs, stop (condition b). A plain `git diff --stat` also lists this task file's own status edit from Step 0; ignore that line.

5. **Step 5 — print this completion line, then go to the review gate:**
   `EDITS DONE: TASK_TEMPLATE.md backlog path and stale-copy wording, 3 edits, checks pass. STOP: requesting reviewer assessment.`

## REVIEW GATE — hard stop

After Step 5, STOP. Paste the Step 4 output and `git diff` in chat. Do not commit, push, move the task file or mark anything complete. Wait for a reviewer disposition (GO / REVISE / STOP) and then a separate message from the human. Commit and push need separate authorization: commit only if the human's message authorizes commit; push only after a later message authorizes push. REVISE: fix the listed items, rerun Step 4, return to this gate. STOP: do nothing further and wait.

## After authorization

- Commit (only after commit is authorized): `git commit -m "docs: TASK_TEMPLATE backlog path, flat-backlog substitution, stale copies reported" -- TASK_TEMPLATE.md` (path-limited, so a staged move of this task file is not swept into the commit). Never `git add .` or `git add -A`.
- Push (only after push is authorized): `git push`, then `git status -sb`.
- Then follow `SESSION_CLOSEOUT.md` only.

## Synthesis report (save as MD file, do not paste in chat)

```
## STATUS SYNTHESIS REPORT
Task: 2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING
Date: YYYY-MM-DD
About to do: three single-line edits in TASK_TEMPLATE.md (lines 17, 130, 421)
Files: TASK_TEMPLATE.md only
Done looks like: Step 4 counts match; `git diff --stat -- TASK_TEMPLATE.md` shows 3 insertions, 3 deletions, one file
Gotchas: no whole-file rewrite; nothing inside the dispatch code fence; stop only at (a), (b), (c)
SYNTHESIS COMPLETE.
```

## Acceptance Criteria
- [ ] Step 4 counts all match
- [ ] `git diff --stat -- TASK_TEMPLATE.md` shows 3 insertions, 3 deletions
- [ ] Reviewer GO and human authorization recorded before any commit

## Dependencies
**Blocked by**: none
**Blocks**: any later task that edits `TASK_TEMPLATE.md` (stop wording and review-gate authorization block; synthesis report optional; 2026-10-18 contradictions list). Those run in order after this one.
**Related tasks**: `2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md` (1a), `2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING.md` (1b)
**Out of scope, for a later task**: lines 150-152 inside the dispatch block ("Never leave stale copies in the source folder"; "Only ONE result should exist. Paste this output before committing.") do not conflict with the new Step 3 wording but may need rewording with the stop-wording task.

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion]

### What was changed
-

### Issues discovered
