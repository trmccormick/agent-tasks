---
status: backlog
resume: false
priority: HIGH
type: documentation
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
Task: <agent-tasks>/projects/galaxy_game/tasks/drafts/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-C-HTTP-VERIFICATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/drafts/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-C-HTTP-VERIFICATION.md \
         projects/galaxy_game/tasks/active/2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-C-HTTP-VERIFICATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-C-HTTP-VERIFICATION.md"
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

# TASK: HTTP verification for catalog image containment
**Status**: BACKLOG (drafted for human review; NOT dispatched, NOT dispatch-ready until the checklist below is complete)
**Priority**: HIGH  |  **Type**: documentation  |  **Created**: 2026-10-17

---

## Prerequisites — READ FIRST

- Read `rules/GUARDRAILS.md` and `projects/galaxy_game/SESSION_GUIDANCE.md` before work.
- The facts marked **VERIFIED FROM PARENT** below are inherited from the supplied parent task and were not independently re-verified by this web agent.
- Approval to proceed is a human decision. This draft does not itself authorize dispatch or implementation.

## Context

The PARENT's core finding is that the controller's current string-prefix guard can accept traversal and sibling-prefix paths. The human-run Ruby check on 2026-10-16 is **VERIFIED FROM PARENT** as path-building/guard behavior only, not as an end-to-end HTTP request:

| Requested `path` param | Built path | Guard passes |
|---|---|---|
| `../../etc/passwd` | `/etc/passwd` | false (blocked) |
| `images/catalog/../../../etc/passwd` | `/app/data/images/catalog/../../../etc/passwd` | **true (bypass)** |
| `images/catalog-private/x.png` | `/app/data/images/catalog-private/x.png` | **true (sibling-prefix bypass)** |
| `images/catalog/crafts/ground/rh400_concept.png` | normal | true (valid) |

**VERIFIED FROM PARENT:** `catalog_image` is defined at `galaxy_game/app/controllers/admin/catalog_controller.rb` line 39 and the `start_with?` check is at line 45, as read from origin on 2026-10-16. The existing traversal test is at `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` line 208 and expects 404. The PARENT explicitly says it is **not yet verified** whether routing or the web server normalizes `..` or `%2e%2e` before the controller, and whether `/admin` requires authentication.

## Critical Information for This Task

1. **No code changes:** This task's only deliverable is a Markdown report. Do not edit, stage, commit, or push source code, tests, routes, authentication, or configuration.
2. **Separate evidence types:** The PARENT's Ruby table establishes path-building/guard behavior only. Controller specs establish controller-level behavior only. Live HTTP requests are required for this task's conclusions about the HTTP stack.
3. **Percent encoding is unverified:** Send and record request targets containing percent-encoded traversal (`%2e%2e` and mixed-case encoding, such as `%2E%2e`, if practical). Report whether each target reaches the controller unchanged, is decoded or normalized before controller handling, or is rejected upstream. Do not infer the responsible layer without evidence.
4. **Authentication is unverified:** Observe whether `/admin` requires authentication before sending valid-image, bypass-path, or encoded-traversal requests. Use a request without supplied credentials or an existing authenticated session. If the evidence indicates that `/admin` does not require authentication, record the request/response evidence and stop immediately; do not perform further HTTP checks and do not change or add authentication.
5. **Scope boundaries:** Do not change route behavior, URL helper `admin_catalog_image_path(path:)`, JSON/response shape, authentication, or which image is selected for any asset. Canonical-render selection remains an unresolved human decision.
6. **Human approval:** Approval to proceed is a human decision; this draft does not itself authorize dispatch or execution.

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

Produce one Markdown report only, based on live HTTP observations, that records:
- whether `/admin` requires authentication, based on a request made without supplied credentials or an existing authenticated session;
- whether percent-encoded traversal (`%2e%2e`, including mixed-case encoding where practical) reaches the `catalog_image` controller unchanged, is decoded/normalized before reaching it, or is rejected upstream; and
- the exact request/response evidence for one valid image request and one bypass-path request.

Check application reachability first, then observe authentication before making any valid-image, bypass-path, or encoded-traversal requests. If `/admin` appears not to require authentication, record the evidence and stop before any further HTTP checks.

Do not change code, tests, routes, URL helpers, authentication, response shape, or image selection. This verification is separate from controller-level regression tests and does not replace them.

## Files Involved

| File/resource | Purpose | Access |
|---|---|---|
| `galaxy_game/app/controllers/admin/catalog_controller.rb` | Read only, to understand the action being exercised and identify what evidence can show controller reachability | **VERIFIED FROM PARENT:** origin read 2026-10-16; action line 39, guard line 45 |
| `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` | Read only, to distinguish controller-spec evidence from live HTTP evidence; existing traversal test at line 208 | **VERIFIED FROM PARENT:** origin read 2026-10-16 |
| `projects/galaxy_game/summaries/` | Save the single report-only deliverable in Markdown | Summary directory is named in the PARENT's synthesis-report requirement; exact output filename is to be chosen by the agent |
| Running application and request client | Check application reachability, observe `/admin` authentication, then make one valid-image request and one bypass request and observe encoded traversal handling if permitted by the authentication result | **VERIFIED:** `docker-compose.dev.yml` maps the web service to `3000:3000`, and the human runs that Compose file; base URL is `http://localhost:3000`. **VERIFIED:** route pattern is `/admin/catalog/image/*path`, with URL helper `admin_catalog_image_path(path:)` (route in `galaxy_game/config/routes.rb`, line 126, as supplied). |

No source, test, route, or configuration files may be changed. This is a report-only task.

## Implementation Steps

- **Step 0:** Follow the copied Agent Dispatch Interface's task-file lifecycle instructions only after human approval; update status only after moving and verify only one copy exists.
- **Step 1:** Save the Status Synthesis Report template referenced by the task's required workflow as an MD file before verification. Do not paste the synthesis report into chat.
- **Step 2:** Check reachability at the verified base URL `http://localhost:3000`. If the app is not reachable at http://localhost:3000, stop and report what you tried.
- **Step 3:** Before any valid-image, bypass-path, or encoded-traversal request, observe whether `/admin` requires authentication. Use a request without supplied credentials or an existing authenticated session. Record the exact request target, response status, relevant response evidence, and the basis for the conclusion. If `/admin` appears not to require authentication, record the evidence and stop immediately; do not perform further HTTP checks and do not change authentication.
- **Step 4:** Only if the authentication observation does not trigger the stop condition, make one valid-image request and one bypass-path request. Record each exact raw request target as sent, response status, relevant response headers (including content type for the valid image), and a concise response observation. Use a traversal form grounded in the PARENT's verified path-building table, such as `/admin/catalog/image/images/catalog/../../../etc/passwd`, adapting the request target only as necessary to exercise the intended path through the wildcard route; record the exact target actually sent.
- **Step 5:** Only if permitted by the authentication observation, send and record a request target containing `%2e%2e` and, if practical, a mixed-case form such as `%2E%2e`. Use request/controller evidence to determine whether the target reaches the controller unchanged, is decoded or normalized before controller handling, or is rejected upstream. Record the raw target and status for each attempt, and identify any inference separately from direct observation.
- **Step 6:** Save one Markdown report under `projects/galaxy_game/summaries/` containing the observations, exact request/response evidence, distinction between verified and unverified facts, and any limitations. If any live check cannot be performed, state that explicitly and leave the corresponding acceptance criterion unmet.

## Acceptance Criteria

- [ ] **AC1 — Authentication observation:** Before any valid-image, bypass-path, or encoded-traversal request, report whether `/admin` requires authentication. Include the exact request target, response status, and response evidence from a request made without supplied credentials or an existing authenticated session. If it appears not to require authentication, stop without making further HTTP requests or changing anything.
- [ ] **AC2 — Percent-encoded traversal:** If the authentication observation permits further checks, report how request targets containing `%2e%2e` (and a mixed-case variant if practical) are handled: controller receives them unchanged, they are decoded/normalized before controller handling, or they are rejected upstream. Include each exact raw request target and available evidence; distinguish direct observation from inference.
- [ ] **AC3 — Valid request evidence:** If the authentication observation permits further checks, record one actual valid-image request's exact raw target, response status, relevant headers including image content type, and response observation.
- [ ] **AC4 — Bypass request evidence:** If the authentication observation permits further checks, record one actual bypass-path request's exact raw target, response status, and response observation. State whether evidence shows it reached the controller or was stopped/rewritten upstream.
- [ ] **AC5 — Report-only boundary:** Save exactly one Markdown report in `projects/galaxy_game/summaries/`; no code, test, route, helper, authentication, response-shape, configuration, or canonical-render changes are made.
- [ ] **AC6 — Evidence integrity:** Do not claim an HTTP check passed if it was not run. If a request cannot be made or the app/environment cannot be identified, record the limitation and leave the affected criterion unmet.

## Stop Conditions — escalate to user immediately if:

- if the app is not reachable at http://localhost:3000, stop and report what you tried.
- `/admin` appears not to require authentication; record the observed evidence and stop. Do not fix or add authentication.
- A requested check would require changing a route, URL helper, authentication, response shape, app configuration, source code, tests, or canonical-image selection.
- The application is unavailable, a valid image cannot be identified safely, or the bypass request cannot be made; report the limitation without substituting assumptions for live evidence.
- Uncommitted changes or an environment issue would require changing files to proceed; do not make code changes.

## Commit Instructions

This is a report-only task with one deliverable: a Markdown report saved in the summaries folder. Do not edit, stage, commit, or push code or configuration. Report the exact report path and evidence gathered. If a task-file lifecycle action is required, follow the standard workflow only after the human approves proceeding.

## Dependencies

- **Blocked by**: `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md`
- **Blocks**: none; report-only verification follows A and does not modify implementation.
- **Related tasks**: `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`; canonical-render selection remains out of scope.

## Notes / Evidence to Preserve

The YAML `type` is `documentation`, a valid type listed in TASK_TEMPLATE.md, because this task's deliverable is a report and it authorizes no code changes. The required filename remains the user-specified `HIGH-BUG-FIX` name.

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
