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
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/drafts/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md \
         projects/galaxy_game/tasks/active/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md"
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

# TASK: Core real-path containment for `catalog_image`
**Status**: BACKLOG (drafted for human review; NOT dispatched, NOT dispatch-ready until the checklist below is complete)
**Priority**: HIGH  |  **Type**: bug-fix  |  **Created**: 2026-10-17

---

## Prerequisites — READ FIRST

- Read `rules/GUARDRAILS.md` and `projects/galaxy_game/SESSION_GUIDANCE.md` before work.
- The facts marked **VERIFIED FROM PARENT** below are inherited from the supplied parent task and were not independently re-verified by this web agent.
- Approval to proceed is a human decision. This draft does not itself authorize dispatch or implementation.

## Context

`Admin::CatalogController#catalog_image` serves files from the catalog image directory. The supplied parent task records a string-prefix containment check on an unnormalized path and a human-run Ruby path-building check dated 2026-10-16. The evidence below is **VERIFIED FROM PARENT** as path-building/guard behavior only, not as an end-to-end HTTP result.

| Requested `path` param | Built path | Guard passes |
|---|---|---|
| `../../etc/passwd` | `/etc/passwd` | false (blocked) |
| `images/catalog/../../../etc/passwd` | `/app/data/images/catalog/../../../etc/passwd` | **true (bypass)** |
| `images/catalog-private/x.png` | `/app/data/images/catalog-private/x.png` | **true (sibling-prefix bypass)** |
| `images/catalog/crafts/ground/rh400_concept.png` | normal | true (valid) |

**VERIFIED FROM PARENT:** `Admin::CatalogController#catalog_image` is at `galaxy_game/app/controllers/admin/catalog_controller.rb` line 39 and the `start_with?` check is at line 45, as read from origin on 2026-10-16. The existing traversal test, `rejects path traversal attempts`, is at `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` line 208 on origin and expects `have_http_status(:not_found)` (404). The existing spec hardcodes `/home/galaxy_game/app/data/images/catalog` and may create a zero-byte PNG there if missing; new tests must not write to that real data directory.

## Critical Information for This Task

1. **Real-path boundary:** A string-prefix check is insufficient. Resolve both configured root and candidate target, and enforce a separator-aware strict-descendant relationship. The catalog root itself is not a valid image target.
2. **Regular files only:** Reject directories and other filesystem objects. A missing target or a failure during real-path resolution must produce 404, not an unhandled exception or 500.
3. **Test isolation:** Use a temporary directory or stubbed root. Do not create test fixtures in `/home/galaxy_game/app/data/images/catalog`. Keep scratch files/logs in `temp-files/`, never the repository root.
4. **Out of scope:** Do not change routes, `admin/catalog_image_path(path:)`, JSON/response shape, authentication, URL behavior, or canonical image selection. Canonical-render selection remains an unresolved human decision.
5. **Verified from parent:** The path-building Ruby table above is not an end-to-end request. Whether the HTTP stack normalizes traversal is not established by controller tests.

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

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

Fix only the `catalog_image` containment decision so it serves a requested target if and only if the target resolves to a regular file strictly beneath the resolved catalog root. Resolution failures and rejected targets must return the existing 404 behavior, without unhandled exceptions. This task covers core real-path containment and non-symlink cases only; symlink-specific regression coverage is assigned to Task B, and HTTP-stack verification is assigned to Task C.

## Files Involved

| File | Role | Evidence status |
|---|---|---|
| `galaxy_game/app/controllers/admin/catalog_controller.rb` | Change only the `catalog_image` containment/rejection logic | **VERIFIED FROM PARENT:** origin read 2026-10-16; action line 39, guard line 45 |
| `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` | Add isolated regression tests; existing traversal test at line 208 | **VERIFIED FROM PARENT:** origin read 2026-10-16 |

No other source or test files may be changed. Do not add a helper or shared module. Any other changed file is a Stop Condition.

## Implementation Steps

- **Step 0:** Move the task file per the copied Agent Dispatch Interface; update `status: backlog` to `status: active` only after the move, and verify only one copy exists.
- **Step 1:** Save the Status Synthesis Report template above as required by the project workflow before implementation work.
- **Step 2 (red first):** Add isolated controller-spec regression tests for (a) embedded `..` traversal using `images/catalog/../../../etc/passwd`, (b) sibling-prefix `images/catalog-private/x.png`, (c) an absolute-path parameter, (d) a nonexistent target, (e) a directory target, and (f) a valid regular image file. Rejected targets must return 404 without raising. Run the applicable tests before changing production code and record red output for cases that demonstrate the current guard's weakness.
- **Step 3:** Change only the containment decision in `catalog_image`: resolve the configured root and candidate to real paths; treat resolution errors as rejection; enforce a separator-aware strict-descendant boundary; require a regular file before serving.
- **Step 4:** Run the controller spec in isolation using the **VERIFIED FROM PARENT** command below (container working directory is `/home/galaxy_game`; use the log-redirect form and inspect the log with `tail -50`):

  ```bash
  docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/controllers/admin/catalog_controller_spec.rb > /home/galaxy_game/log/rspec_catalog_controller_$(date +%s).log 2>&1'
  ```

  Then run `docker exec web tail -50 /home/galaxy_game/log/rspec_catalog_controller_<timestamp>.log`, substituting the actual timestamp. Paste the relevant output. Run related specs only if project guidance identifies them; do not broaden scope.
- **Step 5:** Report exact changed paths and `git diff --stat`. Do not push. Do not commit without explicit human approval.

## Acceptance Criteria

- [ ] **AC1 — Traversal and prefix boundary:** Regression tests reject embedded `..` traversal and the sibling-prefix path with 404; tests demonstrate the relevant current-guard weakness before the fix where the test reaches the controller.
- [ ] **AC2 — Absolute parameter:** An absolute-path parameter outside the resolved catalog root is rejected with 404 and no unhandled exception.
- [ ] **AC3 — Missing and non-regular target:** A nonexistent target and a directory target return 404; real-path resolution failure does not become a 500.
- [ ] **AC4 — Valid file:** A valid regular image beneath the catalog root is still served with an image content type.
- [ ] **AC5 — Test result:** `catalog_controller_spec.rb` isolation run has 0 failures; paste output and report any related specs identified by project guidance without claiming unrun specs passed.
- [ ] **AC6 — Scope and isolation:** Only the controller and its spec changed; `git diff --stat` is reported; fixtures exist only in a temp directory/stubbed root, and no scratch files are left in the repository root.
- [ ] **AC7 — Preserved boundaries:** Existing 404 rejection behavior is preserved. No route, URL helper, response-shape, authentication, or canonical-render selection changes were made.

## Stop Conditions — escalate to user immediately if:

- The fix appears to require a route, URL helper, view, service, helper, or source/test file beyond the two listed.
- Valid-image behavior changes in an existing spec, or the 404 rejection behavior cannot be preserved.
- The same focused test failure persists after two repair attempts.
- A decision about which image is canonical comes up.
- Uncommitted changes not made by the agent are present in a file it needs to touch.
- Correct containment appears to require changes to route behavior, URL encoding/decoding, authentication, or another out-of-scope surface.
- The configured catalog root cannot be resolved safely or its intended semantics are unclear; stop rather than guessing.
- The agent cannot establish red/green evidence for a case because the test layer rewrites it; record the limitation and do not claim the case was exercised.

## Commit Instructions

Run git commands on **host only**. Stage explicit paths only; never use `git add .` or `git commit -a`. Report `git diff --cached --stat`. Per GUARDRAILS.md Rule 26, do not commit or push without explicit human approval in the current chat session; present the diff or proposed commit message and STOP for approval.

## Dependencies

- **Blocked by**: none
- **Blocks**: `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-B-SYMLINK-CASES.md` and `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-C-HTTP-VERIFICATION.md` are follow-on tasks; they must not be folded into this task.
- **Related tasks**: `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`; canonical-render selection remains out of scope.

## Notes / Evidence to Preserve

The PARENT's reported path-building table and line references are retained above as parent-supplied verified evidence, not independently re-verified here.

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
