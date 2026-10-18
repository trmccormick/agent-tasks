---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: CONTROLLERS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-10-17
last_updated: 2026-10-17
---


## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/drafts/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-B-SYMLINK-CASES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/drafts/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-B-SYMLINK-CASES.md \
         projects/galaxy_game/tasks/active/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-B-SYMLINK-CASES.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-B-SYMLINK-CASES.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy_game/summaries/
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

# TASK: Symlink edge cases for catalog image containment
**Status**: BACKLOG (drafted for human review; NOT dispatched, NOT dispatch-ready until the checklist below is complete)
**Priority**: HIGH  |  **Type**: bug-fix  |  **Created**: 2026-10-17

---

## Prerequisites — READ FIRST

- Read `rules/GUARDRAILS.md` and `projects/galaxy_game/SESSION_GUIDANCE.md` before work.
- The facts marked **VERIFIED FROM PARENT** below are inherited from the supplied parent task and were not independently re-verified by this web agent.
- Approval to proceed is a human decision. This draft does not itself authorize dispatch or implementation.

## Context

The core containment fix is assigned to Task A, `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md`. This task adds focused symlink regression coverage and any narrowly necessary controller adjustment for those cases. The supplied PARENT reported that a symlink inside the catalog root pointing outside could pass the string-prefix guard; that report was not independently re-verified by the web agent.

**VERIFIED FROM PARENT:** `Admin::CatalogController#catalog_image` is at `galaxy_game/app/controllers/admin/catalog_controller.rb` line 39 and the `start_with?` check is at line 45, as read from origin on 2026-10-16. The existing traversal test `rejects path traversal attempts` is at `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` line 208 on origin and expects `have_http_status(:not_found)` (404). The existing spec hardcodes `/home/galaxy_game/app/data/images/catalog` and may create a zero-byte PNG there if missing; all new fixtures must be isolated in a temporary directory or stubbed root.

## Critical Information for This Task

1. **Depends on Task A:** Do not reimplement the core real-path fix. First verify Task A's approved/landed behavior in the current working tree and add the missing edge-case tests.
2. **Outside-root symlink:** Create a real symlink inside a temporary catalog root that points to a regular file outside that root; it must be rejected with 404.
3. **Dangling symlink:** A symlink whose target does not exist must be rejected with 404; path-resolution failure must not escape as an exception or 500.
4. **Symlinked catalog root:** If the configured catalog root path itself resolves through a symlink, a valid regular file beneath that resolved root must still be served. Containment is relative to the resolved root, not the spelling of the configured path.
5. **Test isolation:** Create all files, directories, and symlinks under a temporary directory/stubbed root. Never write into `/home/galaxy_game/app/data/images/catalog`.
6. **Out of scope:** No route, URL helper, response-shape, authentication, URL behavior, or canonical-render selection changes. Canonical-render selection remains a human decision.

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

Verify that the core real-path containment behavior handles symlink edge cases correctly: an in-root symlink to an outside target and a dangling symlink must be rejected as 404, while a valid regular file remains servable when the configured catalog root itself is reached through a symlink. Make only the narrowly necessary controller/spec changes for these cases; do not alter routes, URL behavior, authentication, response shape, or canonical-image selection.

## Files Involved

| File | Role | Evidence status |
|---|---|---|
| `galaxy_game/app/controllers/admin/catalog_controller.rb` | Adjust only symlink-related containment/rejection behavior if Task A's implementation does not satisfy these cases | **VERIFIED FROM PARENT:** origin read 2026-10-16; action line 39, guard line 45 |
| `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` | Add symlink regression tests | **VERIFIED FROM PARENT:** origin read 2026-10-16; existing traversal test line 208 |

No other source or test files may be changed. Do not add a helper or shared module. Any other changed file is a Stop Condition.

## Implementation Steps

- **Step 0:** Move the task file per the copied Agent Dispatch Interface; update status only after moving and verify only one copy exists.
- **Step 1:** Save the Status Synthesis Report template above as required by the project workflow before implementation work.
- **Step 2 (red first):** Add tests using temporary paths for (a) an in-root symlink to a regular file outside the root, (b) a dangling symlink, and (c) a valid regular file when the configured catalog root is reached through a symlink. Show which tests fail before the symlink-case change, where applicable.
- **Step 3:** If a test reveals a gap after Task A, make only the narrowly necessary change in `catalog_image` to satisfy the three cases while preserving the resolved-root boundary and 404 behavior. If the behavior already satisfies them, add tests only.
- **Step 4:** Run the controller spec in isolation using the **VERIFIED FROM PARENT** command below (container working directory is `/home/galaxy_game`; inspect the generated log with `tail -50`):

  ```bash
  docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/controllers/admin/catalog_controller_spec.rb > /home/galaxy_game/log/rspec_catalog_controller_$(date +%s).log 2>&1'
  ```

  Then run `docker exec web tail -50 /home/galaxy_game/log/rspec_catalog_controller_<timestamp>.log`, substituting the actual timestamp. Paste output; do not claim unrun specs passed.
- **Step 5:** Report exact changed paths and `git diff --stat`. Do not push or commit without explicit human approval.

## Acceptance Criteria

- [ ] **AC1 — Outside-root symlink:** An in-root symlink targeting a regular file outside the resolved catalog root is rejected with 404 and no unhandled exception.
- [ ] **AC2 — Dangling symlink:** A dangling symlink is rejected with 404; path resolution does not produce an unhandled exception or 500.
- [ ] **AC3 — Symlinked root remains usable:** A valid regular file beneath a catalog root whose configured path is reached through a symlink is served with an image content type.
- [ ] **AC4 — Test isolation and suite:** All symlink fixtures are created only in a temporary directory/stubbed root. `catalog_controller_spec.rb` isolation run has 0 failures; paste output.
- [ ] **AC5 — Scope:** Only `galaxy_game/app/controllers/admin/catalog_controller.rb` and `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` changed. No route, URL helper, response-shape, authentication, URL behavior, or canonical-render selection changes were made.

## Stop Conditions — escalate to user immediately if:

- Task A's core real-path containment behavior is not present or its state is unclear; stop and ask the human to resolve task ordering rather than reimplementing it.
- A symlink cannot be created in the test environment; report the limitation and do not silently omit the test.
- Valid-image behavior or the existing 404 rejection behavior changes unexpectedly.
- The same focused test failure persists after two repair attempts.
- Correct behavior appears to require changes outside the controller and its spec, or requires route, URL behavior, authentication, response-shape, or canonical-render changes.
- The configured root's intended semantics are unclear, or uncommitted changes not made by the agent are present in a file it needs to touch.

## Commit Instructions

Run git commands on **host only**. Stage explicit paths only; never use `git add .` or `git commit -a`. Report `git diff --cached --stat`. Per GUARDRAILS.md Rule 26, do not commit or push without explicit human approval in the current chat session; present the diff or proposed commit message and STOP for approval.

## Dependencies

- **Blocked by**: `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md`
- **Blocks**: none; this task is a focused follow-on to A and must not expand into HTTP-stack verification.
- **Related tasks**: `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`; canonical-render selection remains out of scope.

## Notes / Evidence to Preserve

The symlink escape was reported in the PARENT's context but was not independently re-verified. The parent-provided 404 expectation is `have_http_status(:not_found)` (404).

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
