---
status: backlog
resume: false
priority: HIGH
type: documentation
system_domain: WORKFLOW
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-10-18
last_updated: 2026-10-18
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: agent-tasks
Task: <agent-tasks>/projects/agent-tasks/tasks/drafts/2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/drafts/2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md \
         projects/agent-tasks/tasks/active/2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/agent-tasks/tasks -name "2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/agent-tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.


## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

# TASK: Fix four internal contradictions in TASK_TEMPLATE.md (mechanical consistency pass)
**Status**: BACKLOG (drafted by a web review session; NOT dispatched, NOT dispatch-ready until the checklist above is complete)
**Priority**: HIGH  |  **Type**: documentation  |  **Created**: 2026-10-18

---

## Prerequisites — READ FIRST

- Read `rules/GUARDRAILS.md` and `TASK_TEMPLATE.md` (agent-tasks repo root).
- Read `projects/agent-tasks/summaries/2026-10-07-GOVERNANCE-CONFLICT-REGISTER.md`. It lists other `TASK_TEMPLATE.md` conflicts (synthesis gate required vs optional, Qwen-first routing line, Dispatch Interface for every task, backlog date folders). **Those are policy decisions and are out of scope here.** This task is the mechanical pass and goes first, so the two efforts do not edit the same file at the same time.
- Approval to proceed is a human decision. This draft does not authorize dispatch or implementation.

## Context

Four contradictions in `TASK_TEMPLATE.md` were found while reviewing the catalog_image containment drafts. Every task drafted from the template inherits them. Line numbers below were VERIFIED against origin on 2026-10-17; re-check them before editing.

1. **Synthesis destination.** Lines 123 and 243 say save the synthesis as an MD file and never paste it in chat. Lines 241 and 280 say post it in chat ("POST THIS TO CHAT BEFORE PROCEEDING").
2. **Synthesis timing.** Line 241 says the synthesis comes before "running any commands or modifying any files". Lines 102 and 107 say Step 0 (`git mv` and the YAML status change) comes first and synthesis must not start until it is done.
3. **Working directory.** Line 115's `find` command uses `agent-tasks/projects/...` (relative to the clone's parent), while the `git mv` commands use `projects/...` (relative to the clone root).
4. **Section order.** The header comments (lines 4 and 46) say the Agent Dispatch Interface goes immediately after the YAML. The body lists the Readiness Checklist (line 72) before the Dispatch Interface (line 89), and the checklist item at line 76 says "Agent Dispatch Interface section below".

## Critical Information for This Task

Recommended resolutions (pre-filled so no further decision is needed; Tracy approves them at review):

1. **File only.** The synthesis is saved as an MD file in the summaries folder named in the Dispatch Interface. Chat is for questions. Remove the "post in chat" wording at the lines in item 1 above. Chat may carry only the file path.
2. **Step 0 first.** Reword the synthesis timing sentence to: after Step 0, and before any other command, test run, or file change.
3. **Everything relative to the clone root.** Make the `find` command repo-relative (`find projects/[project]/tasks -name "[FILENAME].md"`) and say once that all commands run from the clone root.
4. **Dispatch Interface first, checklist after.** Move the body to match the header comments, and change the checklist item to "section above".

Do not change: the policy questions in the conflict register, any individual task file, `GUARDRAILS.md`, or any other document. Re-substituting the verbatim blocks in existing tasks (for example the catalog_image drafts A, B, C) is a separate follow-up after this task is committed.

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: [name from filename]
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `path/to/file` | [description] | [not started / pending / done] |

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
- ❌ [wrong approach] — instead ✅ [right approach]
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

## Problem Statement

`TASK_TEMPLATE.md` cannot be followed as written because of the four contradictions above. Fix them in the template only, using the recommended resolutions, without introducing new ones and without touching policy.

## Files Involved

| File | Role | Evidence status |
|---|---|---|
| `TASK_TEMPLATE.md` | The only file to change | line numbers VERIFIED from origin 2026-10-17; re-check |

Any other changed file is a Stop Condition.

## Implementation Steps

- **Step 0:** Move the task file per the copied Agent Dispatch Interface; update status only after moving; verify one copy.
- **Step 1:** Save the Status Synthesis Report as the task requires.
- **Step 2:** Re-check each line reference in Context against the current template and record any that moved.
- **Step 3:** Draft the four edits and show them as one diff. Do not commit.
- **Step 4:** Verify with commands, pasting each result: `grep -n -i "POST THIS TO CHAT\|post a .*synthesis.*in chat" TASK_TEMPLATE.md`; `grep -n "find agent-tasks" TASK_TEMPLATE.md`; confirm the Dispatch Interface section comes before the Readiness Checklist; `git diff --stat`.
- **Step 5:** Present the diff and the proposed commit message and STOP for approval.

## Acceptance Criteria

- [ ] **AC1:** The first grep in Step 4 returns no lines (no instruction to post the synthesis in chat remains).
- [ ] **AC2:** `find agent-tasks` no longer appears; the `find` command is repo-relative and matches the `git mv` convention.
- [ ] **AC3:** The synthesis timing text states that Step 0 comes first.
- [ ] **AC4:** The Dispatch Interface section precedes the Readiness Checklist, and the checklist item says "above".
- [ ] **AC5:** Only `TASK_TEMPLATE.md` changed (`git diff --stat` pasted).
- [ ] **AC6:** No new contradiction: the template read end to end has one synthesis destination, one step order, one path convention, and one section order.

## Stop Conditions — escalate to user immediately if:

- Fixing any item requires a policy decision from the conflict register (whether synthesis is mandatory, routing, date folders).
- Any file other than `TASK_TEMPLATE.md` would need to change.
- The line references have moved enough that a contradiction can no longer be located unambiguously.
- Uncommitted changes not made by the agent are present in `TASK_TEMPLATE.md`.
- The same edit fails to apply after two attempts.

## Commit Instructions

Run git commands from the clone root, on the host. Stage explicit paths only; never `git add .` or `git commit -a`. Per GUARDRAILS.md Rule 26, an agent may `git add` but may not commit or push without an explicit human approval message in the current session; present the diff or proposed commit message and STOP for approval.

## Dependencies

- **Blocked by**: none.
- **Sequenced before**: the governance alignment work listed in `projects/agent-tasks/summaries/2026-10-07-GOVERNANCE-CONFLICT-REGISTER.md` (it also edits `TASK_TEMPLATE.md`).
- **Blocks**: re-substitution of the verbatim blocks in existing drafts, including the catalog_image tasks A, B and C in `projects/galaxy_game/tasks/drafts/`.
- **Related**: `projects/agent-tasks/needs_review.md` entry dated 2026-10-18 (mark RESOLVED when this task is committed).

## Notes / Evidence to Preserve

The four contradictions and their line numbers were read directly from `TASK_TEMPLATE.md` on origin on 2026-10-17 and are logged in `projects/agent-tasks/needs_review.md`.

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**


## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was changed
- `[file]` — [description of change]

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
