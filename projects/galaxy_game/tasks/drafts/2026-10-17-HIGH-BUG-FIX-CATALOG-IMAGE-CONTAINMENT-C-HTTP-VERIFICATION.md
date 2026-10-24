---
status: draft
resume: false
priority: HIGH
type: documentation
system_domain: CONTROLLERS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-10-17
last_updated: 2026-10-23
---

## 🟡 DRAFT STATUS NOTE

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

---

## 🟡 Draft-Specific Guidance (not sent to agents)

This section is for human/planning-agent use only. It is NOT part of the agent dispatch contract.

- This task remains in `tasks/drafts/` until a review agent returns GO and the human approves the move to backlog.
- Do not send this task to an implementation agent while it is in drafts.
- The Agent Dispatch Interface below (after this section) is the startup contract that WILL be sent to an agent — but only AFTER the task has been moved to `tasks/backlog/` and approved.

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**NOT YET APPLICABLE.** This task is in `tasks/drafts/` with `status: draft`. It has NOT been reviewed or approved for implementation. Do not use this interface until the task is moved to `tasks/backlog/` with `status: backlog` and a review agent returns GO.

When this task IS ready for dispatch (after human approval), the Agent Dispatch Interface will be filled out per TASK_TEMPLATE.md and PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow". It will reference the backlog path, not the drafts path.

---

## TASK: HTTP verification for catalog image containment
**Status**: DRAFT (awaiting review; NOT dispatched, NOT dispatch-ready until reviewed and approved)
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
7. **Sensitive diagnostic output:** Never paste or save unredacted verbose HTTP output or diagnostic logs that may contain credentials. Before retaining or sharing captured output, redact the values of the entire `Cookie:` and `Set-Cookie:` headers. Never write the session cookie value to chat, the report, or any file. For bypass and encoded-traversal requests, record only the response status, content type, and byte length; never record or reproduce a non-image response body.

## Status Synthesis Report (for implementation session only)

**NOT YET APPLICABLE.** This section will be included in the Agent Dispatch Interface when the task is moved to `tasks/backlog/` and approved. It is not required for review of this draft.

When this task IS ready for dispatch, the synthesis report template will be part of the Agent Dispatch Interface (sent to the implementation agent). The implementation agent will create and post it before starting any work.

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

*These steps apply only when this task is dispatched to an implementation agent (after it has been moved to `tasks/backlog/` and approved).*

- **Step 0:** Follow the Agent Dispatch Interface's task-file lifecycle instructions; update status only after moving and verify only one copy exists.
- **Step 1:** Save the Status Synthesis Report template referenced by the task's required workflow as an MD file before verification. Do not paste the synthesis report into chat.
- **Step 2:** Check reachability at the verified base URL `http://localhost:3000`. If the app is not reachable at http://localhost:3000, stop and report what you tried.
- **Step 3:** Before any valid-image, bypass-path, or encoded-traversal request, observe whether `/admin` requires authentication. First use a request without supplied credentials or an existing authenticated session. Record the exact request target, response status, relevant response evidence, and the basis for the conclusion. If `/admin` appears not to require authentication, record the evidence and stop immediately; do not perform further HTTP checks and do not change authentication. If `/admin` requires authentication, the human must supply a session cookie through an environment variable (for example, `GG_ADMIN_COOKIE`) set in the shell before the run. Never print, paste, log, or otherwise write the cookie value to chat, the report, or any file. If no session is supplied, record that authenticated checks could not be made, leave AC2–AC4 unmet, and stop.
- **Step 4:** Only if the authentication observation permits further checks and a required session has been supplied, make one valid-image request and one bypass-path request using a client that does not normalize dot segments. For `curl`, use `--path-as-is`. Record the exact request line as sent, including the raw request target, response status, and relevant response headers. For the valid-image request, include the image content type and response observation. For the bypass-path request, record only the response status, content type, and byte length; never record or reproduce the response body of a non-image file. Use a traversal form grounded in the PARENT's verified path-building table, such as `/admin/catalog/image/images/catalog/../../../etc/passwd`, adapting the request target only as necessary to exercise the intended path through the wildcard route; record the exact request line actually sent.
- **Step 5:** Only if permitted by the authentication observation and a required session has been supplied, send and record a request target containing `%2e%2e` and, if practical, a mixed-case form such as `%2E%2e`, using a client that does not normalize dot segments. For `curl`, use `--path-as-is`. Record the exact request line as sent, including each raw request target, and the response status, content type, and byte length only. Never record or reproduce the response body of a non-image file. Use request/controller evidence to determine whether the target reaches the controller unchanged, is decoded or normalized before controller handling, or is rejected upstream. Identify any inference separately from direct observation.
- **Step 6:** Save one Markdown report under `projects/galaxy_game/summaries/` containing the observations, exact request/response evidence, distinction between verified and unverified facts, and any limitations. If any live check cannot be performed, state that explicitly and leave the corresponding acceptance criterion unmet.

## Acceptance Criteria

- [ ] **AC1 — Authentication observation:** Before any valid-image, bypass-path, or encoded-traversal request, report whether `/admin` requires authentication. Include the exact request target, response status, and response evidence from a request made without supplied credentials or an existing authenticated session. If it appears not to require authentication, stop without making further HTTP requests or changing anything. If authentication is required, the human must supply a session cookie through an environment variable such as `GG_ADMIN_COOKIE`, set in the shell before the run. Never write the cookie value to chat, the report, or any file. If no session is supplied, record that authenticated checks could not be made, leave AC2–AC4 unmet, and stop.
- [ ] **AC2 — Percent-encoded traversal:** If the authentication observation permits further checks and any required session has been supplied, report how request targets containing `%2e%2e` (and a mixed-case variant if practical) are handled: controller receives them unchanged, they are decoded/normalized before controller handling, or they are rejected upstream. Use a client that does not normalize dot segments (`curl --path-as-is` when using curl). Include the exact request line as sent, each raw request target, status, content type, byte length, and available evidence; distinguish direct observation from inference. Never record or reproduce a non-image response body.
- [ ] **AC3 — Valid request evidence:** If the authentication observation permits further checks, record one actual valid-image request's exact raw target, response status, relevant headers including image content type, and response observation.
- [ ] **AC4 — Bypass request evidence:** If the authentication observation permits further checks, record one actual bypass-path request's exact raw target, response status, content type, and byte length only. State whether available evidence shows it reached the controller or was stopped/rewritten upstream, distinguishing observation from inference. Never record or reproduce the response body of a non-image file.
- [ ] **AC5 — Report-only boundary:** Save exactly one Markdown report in `projects/galaxy_game/summaries/`; no code, test, route, helper, authentication, response-shape, configuration, or canonical-render changes are made.
- [ ] **AC6 — Evidence integrity:** Do not claim an HTTP check passed if it was not run. If a request cannot be made or the app/environment cannot be identified, record the limitation and leave the affected criterion unmet.

## Stop Conditions — escalate to user immediately if:

- if the app is not reachable at http://localhost:3000, stop and report what you tried.
- `/admin` appears not to require authentication; record the observed evidence and stop. Do not fix or add authentication.
- `/admin` requires authentication but the human has not supplied a session cookie through a shell environment variable (for example, `GG_ADMIN_COOKIE`). Record that authenticated checks could not be made, leave AC2–AC4 unmet, and stop. Never write the cookie value to chat, the report, or any file.
- A requested check would require changing a route, URL helper, authentication, response shape, app configuration, source code, tests, or canonical-image selection.
- The application is unavailable, a valid image cannot be identified safely, or the bypass request cannot be made; report the limitation without substituting assumptions for live evidence.
- The client normalizes dot segments and cannot be configured to preserve the intended raw request target; do not treat the resulting request as valid evidence for the intended target.
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

*NOT YET APPLICABLE.* This section will be filled in by the implementing agent after the task is dispatched and completed. It is not applicable while this task remains in drafts.

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
