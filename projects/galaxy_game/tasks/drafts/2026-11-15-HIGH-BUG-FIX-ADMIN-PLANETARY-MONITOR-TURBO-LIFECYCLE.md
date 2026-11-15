---
status: draft
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — reconciled task-specific contract)

**Authority:** Use only after human approval, movement to backlog, YAML `status: backlog`, and completion of the backlog-only readiness checklist. This does not authorize dispatch while this file is in drafts.

**Reconciliation:** This dispatch section is placed immediately after YAML to follow the template's explicit validation requirement (the template's illustrative example shows a conflicting order). For an untracked task file, run `git add <source>` first, then `git mv <source> <destination>`. Never use plain `mv` or `cp` for task lifecycle moves. This resolves the generic template's contradictory untracked-file example and preserves Git history. Save synthesis in summaries before investigation; chat contains only its path and concise status, not report text or raw command output.

```text
You are **Implementation Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.
  1. Inspect:
       git status --short -- projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
       git ls-files --error-unmatch projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
     If untracked, stage first:
       git add projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
     Preserve pre-existing changes.
  2. Move:
       git mv projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md projects/galaxy_game/tasks/active/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
  3. Verify:
       git status --short
       git ls-files --error-unmatch projects/galaxy_game/tasks/active/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
       test ! -e projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md && test -f projects/galaxy_game/tasks/active/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
  4. Open active copy; change YAML status backlog → active and verify.
  5. Record Git inspection, optional staging, git mv, verification, and YAML update in synthesis.
  Do not investigate or implement until Step 0 and Stage 1 synthesis are complete.

If resume: true, read Progress / Resume, continue from Next step, and set resume: false when moving to active.

LIFECYCLE: backlog → active → completed
  - Tracked source: git mv. Untracked source: git add source first, then git mv.
  - Never use plain mv or cp for lifecycle moves. Never leave stale copies.
  - Verify: find projects/galaxy_game/tasks -name "2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md"
    Exactly ONE result; record it in evidence report.

READ FIRST after Step 0:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxy_game/README.md
  3. <agent-tasks>/projects/galaxy_game/SESSION_GUIDANCE.md, if present
  4. Active task file.

SYNTHESIS: Save Markdown to <agent-tasks>/projects/galaxy_game/summaries/YYYY-MM-DD-bug-fix-admin-planetary-monitor-turbo-lifecycle.md. Update after Step 0/before investigation, after investigation/before implementation, and after implementation/verification. Chat: report path and concise status only.

CLOSEOUT: Follow SESSION_CLOSEOUT.md and this task's Commit Instructions. Reviewer GO and operation-specific human authorization are mandatory; commit, push, and completion are separate authorizations.
```

**IMPORTANT:** Do not dispatch while in drafts. Replace only `<agent-tasks>` with the actual clone path. Backlog folder is `current/`; active destination is flat.

---


## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft.**

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/current/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. This is a dispatch-readiness gate, not a draft-review gate.**

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

# TASK: Restore reliable Admin Celestial Body View rendering across Turbo navigation

**Status**: DRAFT  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-11-15  
**Last Updated**: 2026-11-15  
**Author**: ChatGPT (draft for human review)

---

## Context

The original Rails 7 GalaxyGame application previously loaded and displayed planetary maps for Earth, Mars, Venus, and Titan. The user reports that these pages no longer behave reliably. The current lifecycle regression must be investigated and corrected in the original Rails 7 application before the useful behavior is ported into the separate Rails 8 UI prototype. This bug task is limited to lifecycle reliability; it is not the full Celestial Body View redesign.

**Relevant Wiki References** — read for product and UI context; these pages do not specify the JavaScript/Turbo lifecycle fix, which must be established from current code and runtime evidence:
- `docs/wiki/ui/03_admin_and_ai_manager.md` — establishes the Admin Dashboard's purpose, responsibilities, and monitoring/workspace context.
- `docs/wiki/ui/04_current_ui_state_and_known_issues.md` — records dated UI observations and the principle of preserving useful functionality while diagnosing defects.
- `docs/wiki/ui/05_admin_celestial_body_view.md` — records the intended Celestial Body View, distinguishes it from the legacy Monitor entry page and Planetary route, and documents current data flow, endpoints, polling behavior, Turbo lifecycle, test coverage, and unresolved regression context. Its dated code snapshot is background for investigation, not a prescribed fix.

Recent browser screenshots supplied by the user show inconsistent states:
- Some visits display a completely black/blank planetary canvas.
- Other visits display terrain after a reload or subsequent visit.
- In several blank-page screenshots, the sidebar contains values belonging to the body visited immediately before the current page. Examples observed in the screenshot sequence include Venus showing Titan-like atmosphere values, Earth showing Venus-like values, Eden II showing zeroed/Mars-like values, and Mars showing Earth-like geosphere values.
- The screenshots also show successful terrain renders for Earth, Mars, Venus, Titan, and Eden II on some visits. The issue is therefore not accurately described as every terrain dataset being absent or every renderer invocation failing.
- Venus has also appeared as a nearly uniform orange field in a rendered screenshot. This may be a separate terrain-data/normalization issue and is not assumed to have the same root cause as the blank/stale-page behavior.

Claude's screenshot review proposed a strong but unverified hypothesis: a prior page's `AdminMonitor` JavaScript state and periodic `sphere_data` polling may survive a Turbo visit, while initialization/rendering for the newly visited body is skipped or delayed. The sidebar showing the previous body's values is evidence consistent with stale page state, but the browser console/network behavior and actual runtime code still need direct verification.

A small Qwen patch was produced after Claude's initial investigation. The supplied patch file is `qwen-monitor-diff.txt`. It changes `galaxy_game/app/javascript/admin/monitor.js` by removing a one-time `turbo:load` listener around the delayed render and scheduling `scheduleRender` directly after `init()`:

```diff
-    // For Turbo: wait for page:render then page:after-render for full DOM stability
-    if (typeof Turbo !== 'undefined') {
-      document.addEventListener('turbo:load', function onFirstLoad() {
-        document.removeEventListener('turbo:load', onFirstLoad);
-        setTimeout(scheduleRender, 200);
-      });
-    } else {
-      setTimeout(scheduleRender, 100);
-    }
+    // init() already runs after the DOM is ready; just schedule render.
+    setTimeout(scheduleRender, 100);
```

This patch is a candidate change, not an established fix. Verify whether it is present in the current working tree, whether it is the code served to the browser, and whether it actually resolves the lifecycle issue under real Turbo navigation. Do not assume Claude's simulated results reproduce the application's actual Turbo behavior.

---

## Problem Statement

**Design/porting boundary:** The intended workspace is named **Admin Celestial Body View** because it must support planets, moons, asteroids, and other celestial bodies. In the current Rails 7 app, the catalog's **Monitor** route is only a navigation/status hub; the richer map-and-sphere-data page is currently labeled **Planetary View**. Those are legacy route/UI labels, not the future product name. The separate Rails 8 UI prototype should move toward the approved Celestial Body View design rather than copy legacy layout and naming unchanged. This task fixes only the existing Rails 7 lifecycle regression; it does not redesign or port the view.

**Exact description**: Blank terrain canvases and stale cross-body sidebar data occur when navigating the legacy Planetary route for the Admin Celestial Body View in the original Rails 7 application via Turbo navigation.

**Current behavior**: Navigating between the legacy Planetary pages for Earth, Mars, Venus, and Titan via Turbo produces inconsistent results — blank canvases, terrain rendered only after reload, or sidebar values from a previously visited body.

**Expected behavior**: Each Celestial Body View page should render the selected body's terrain correctly on both hard loads and Turbo visits, with sidebar data matching the current body.

---

## Local Worker Triage Report (Optional — for backlog review only)

**Disposition: NEEDS MANUAL REVIEW.** Earlier triage claims are unverified and are not approval, template-conformance evidence, or dispatch authorization.

- **Template Conformance**: NOT YET VERIFIED — revision in progress after Perplexity's REVISE disposition.
- **Docker/RSpec Assessment**: PENDING — inspect actual browser-capable test setup and project rules; JavaScript-related does not imply Docker/RSpec is irrelevant.
- **MVP Alignment**: Proposed alignment is original Rails 7 regression reliability before porting affected behavior to the separate Rails 8 prototype; human confirmation remains required.
- **Action Line**: NEEDS MANUAL REVIEW — draft only; not backlog-approved or dispatch-ready.

---

## Agent Assignment (Human-filled, not seen by agents)

**Proposed executor (human confirmation required)**: Qwen local via Copilot (primary)
**Why proposed**: Local worker has terminal/tool-use access to inspect the original app working tree and run targeted tests.
**Local attempts before cloud**: N/A — no implementation attempt has been authorized or made.
**Supervision Level**: Watched carefully — first dispatch of this task.
**Assignment status**: Proposal only; this draft does not assign or dispatch an agent.

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `<agent-tasks>/rules/GUARDRAILS.md` (EXECUTOR Role section)
2. **Project Guide**: `<agent-tasks>/projects/galaxy_game/README.md`
3. **Session Guidance**: `<agent-tasks>/projects/galaxy_game/SESSION_GUIDANCE.md` (if the project has one)
4. **This Task File**: Everything below

 > Agent MUST read in this order. Do not skip. Save the synthesis report as an MD file under `projects/galaxy_game/summaries/` before starting work; use chat for questions and concise status only.

---

## Files Involved

### Application-root convention and evidence status

Paths under `app/`, `config/`, `spec/`, and `docs/` are relative to the original Rails 7 GalaxyGame repository root, not `agent-tasks`. Paths under `projects/galaxy_game/` and `rules/` are relative to `agent-tasks`. All application/wiki paths are reported paths; verify existence and current contents locally before editing.

### Authorized application files — inspect; edit only as specified

| File | Purpose | Edit permission |
|---|---|---|
| `app/javascript/admin/monitor.js` | Admin Celestial Body View JS; initialization, Turbo events, polling | Editable for minimal lifecycle fix |
| `app/views/admin/celestial_bodies/planetary.html.erb` | Legacy Planetary route template | Inspect; edit only if verified fix requires narrowly scoped lifecycle markup change |
| `app/controllers/admin/celestial_bodies_controller.rb` | Legacy Planetary route controller | Inspect; edit only if verified cause requires narrowly scoped body-identity/data correction |

### Test files — conditional edit permission

| File | Purpose | Edit permission |
|---|---|---|
| `spec/features/admin/planetary_monitor_spec.rb` | Reported focused monitor coverage | Conditionally editable after verifying existence and suitability |
| Any additional test file | Browser/JavaScript lifecycle regression coverage | Inspect first; human approval for exact path required before editing |

### Reference files — read only

| File | Why |
|---|---|
| `app/javascript/application.js` | Module loading and Turbo integration |
| `config/routes.rb` | Legacy Monitor and Planetary routes |
| Related JS/layout/helper/test configuration | Trace lifecycle and test capabilities; record exact discovered paths |

### Authorized documentation update

| File | Purpose | Edit permission |
|---|---|---|
| `docs/wiki/ui/05_admin_celestial_body_view.md` | Existing authoritative wiki page and dated implementation snapshot | Authorized for focused closeout update only if lifecycle behavior changes; verify path/current content first and preserve dated history |

Any other application, test, data, terrain, or documentation path requires explicit human approval before editing.

---

## Critical Information for This Task

### Credentials and access applicability

No new credentials, accounts, or external service access are expected for this local Rails 7 lifecycle investigation. Use only the existing authorized local application and browser environment. Do not create users or credentials. If the local application/runtime is unavailable or requires access not already provided, stop and ask the human; do not invent access details.

### Repository and path convention

`app/`, `config/`, `spec/`, and `docs/` paths refer to the original Rails 7 application repository. `projects/galaxy_game/` and `rules/` paths refer to the separate `agent-tasks` repository. Verify paths in their respective repository roots before use.

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not assume the Qwen patch is correct without verifying it against current working tree and browser behavior.
- ❌ Wrong: Apply it blindly.
- ✅ Right: Verify whether the supplied diff is present, identify code actually served to the browser, test real Turbo navigation, and assess init → render → poll → navigate.
- Why: The diff changes delayed-render scheduling by removing a one-time `turbo:load` listener. Its relationship to initialization and later visits is unknown until traced.

⚠️ **GOTCHA 2**: Do not disable Turbo globally or use `data-turbo="false"` as a shortcut.
- ❌ Wrong: Disable Turbo across the application or all planetary links.
- ✅ Right: Investigate whether a narrowly scoped full-load fallback is consistent with existing patterns; any fallback changing accepted Turbo-navigation behavior requires explicit human scope approval.
- Why: Evidence suggests a lifecycle defect; global Turbo disabling would bypass rather than establish the cause.

⚠️ **GOTCHA 3**: Do not modify terrain datasets or terrain-generation/normalization code unless the investigation proves one is directly responsible.
- ❌ Wrong: Rewrite terrain loaders, GeoTIFF parsing, or normalization as part of this task.
- ✅ Right: If a distinct Venus data/normalization defect is confirmed, document it as a separate follow-up task.
- Why: This task is bounded to the Admin Celestial Body View page (legacy Planetary route) lifecycle and rendering behavior only.

---

## 🔴 REQUIRED: STATUS SYNTHESIS / EVIDENCE REPORT

Create one Markdown report under `projects/galaxy_game/summaries/` named `YYYY-MM-DD-bug-fix-admin-planetary-monitor-turbo-lifecycle.md`. Maintain it through three updates; never claim future evidence early.

### Stage 1 — after Step 0, before investigation
Record exact Git-state commands/outputs; tracked status; any `git add`; `git mv`; source/destination checks; single-copy `find`; YAML status change; objective/boundaries/unknowns; files to inspect and planned evidence. Mark investigation, reproduction, root cause, proposed fix, tests, and runtime results PENDING.

### Stage 2 — after investigation, before implementation
Record verified source paths/code locations; hard-load/Turbo lifecycle; initialization guards; event handlers; retained DOM/state; polling ownership and in-flight response handling; reproduction matrix; Qwen diff and served-code status; root cause or unresolved status; minimal proposed change, rejected alternatives, exact edit paths, and focused test plan. Request human decision if cause/scope/additional files/fallback is ambiguous. Do not implement until Stage 2 is complete and the human approves the proposed approach.

### Stage 3 — after implementation and verification
Append changed files, exact commands/test outputs, browser evidence, acceptance results, wiki update, remaining limitations, and follow-ups. Distinguish direct verification, supplied evidence, and reported claims.

### Copy-ready report scaffold

```markdown
# Status Synthesis and Evidence Report
**Task:** Restore reliable Admin Celestial Body View rendering across Turbo navigation
**Task file:** projects/galaxy_game/tasks/active/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
**Status:** [active / awaiting review / completed]
**Date:** YYYY-MM-DD
**Evidence basis:** [direct verification / supplied evidence / reported by agent / human assertion]

## Stage 1 — Initial understanding and Step 0
### Objective and boundaries
[Original Rails 7 lifecycle reliability only; no redesign or Rails 8 port.]
### Step 0 Git evidence
| Check | Exact command | Result |
|---|---|---|
| Source status/tracking | [command/output] | [result] |
| Staging, if needed | [command/output or N/A] | [result] |
| Git move | [command/output] | [result] |
| Destination tracking/existence | [command/output] | [result] |
| Source absence | [command/output] | [result] |
| Single-copy search | [command/output] | [result] |
| YAML status | [command/output] | [result] |
### Prerequisites completed
- [ ] Read `rules/GUARDRAILS.md` (EXECUTOR Role section).
- [ ] Read `projects/galaxy_game/README.md`.
- [ ] Read `projects/galaxy_game/SESSION_GUIDANCE.md`, if present.
- [ ] Read this active task file after Step 0.
- [ ] Confirm the repository-root convention and task-specific gotchas.
### Initial understanding and unknowns
[Separate supplied screenshot/diff evidence from hypotheses and unverified facts.]
### Planned evidence
[Commands/tools selected after environment inspection; do not invent.]

## Stage 2 — Investigation findings (before implementation)
### Source and lifecycle trace
[Verified paths/lines; module load/init; guards; Turbo events; DOM refs; polling start/stop; in-flight responses.]
### Reproduction matrix
| Body | Hard load | Turbo arrival | Repeat visit | Back/forward | Canvas identity | Sidebar identity | Polling/late response |
|---|---|---|---|---|---|---|---|
| Earth | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING |
| Mars | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING |
| Venus | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING |
| Titan | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING |
| Eden II (if available) | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING | PENDING |
### Qwen diff
[Working-tree status; served-code status; observed effect; conclusion.]
### Root cause and proposed correction
[Verified cause or unresolved; evidence; minimal fix; rejected alternatives; exact files; test plan.]
### Human approval before implementation
[Approval/scope or NOT APPROVED.]

## Stage 3 — Implementation and verification
### Changed files
| File | Change | Why authorized |
|---|---|---|
### Automated tests
| Exact command | Verified environment/container | Result | Evidence |
|---|---|---|---|
### Browser/runtime acceptance
[Repeat matrix with observations, repetition count, console/network evidence, delayed-response method.]
### Documentation
[Wiki snapshot update or reason none needed.]
### Acceptance criteria
[PASS / FAIL / UNVERIFIED for each, with evidence.]
### Remaining issues/follow-ups
[Only independently established findings.]
### Review and authorization
[Reviewer disposition; separate human authorization for commit, push, completion.]
### Handoff
[Changed files, tests, limitations, report path, next action.]
```

Do not use generic `[other files]`, unverified paths, or completed checkmarks for work not yet performed.

---

## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

**You MAY NOT commit, push, or mark any task step as "complete" until BOTH of the following conditions are met:**

1. **Reviewer has issued a GO disposition** — a reviewer has assessed your work and disposed it as one of: `GO`, `REVISE`, or `STOP`. See REVIEW_AGENT_GUIDE.md for reviewer authority definitions.
2. **Human authorization** — Requires an explicit human message that authorizes commit (for example, 'commit it') and an explicit human message that authorizes push (for example, 'push it'). Authorization to commit does not imply authorization to push, and authorization to push does not imply authorization to commit.

**Approval-prompt safeguard:** An agent's approval prompt is not an independent source of authority. It counts only after reviewer GO is recorded and the human explicitly authorizes that particular operation.

### What This Means In Practice

| You CAN do | You CANNOT do |
|---|---|
| Implement changes per task instructions | Commit/push/mark-complete without both conditions above |
| Run tests and verify your changes locally | Assume reviewer GO is sufficient to proceed to commit |
| Create synthesis and evidence artifacts | Proceed to the next task step if review disposition is REVISE or STOP |
| Report results and request review | Override a reviewer's REVISE or STOP disposition |

### Review-Gate Workflow

1. **Complete all implementation and verification steps** listed below this section
2. **Create evidence artifacts** (test output, synthesis reports)
3. **STOP** — save all evidence in the synthesis/evidence report, then provide the report path and a concise chat update stating: *"Implementation and verification are complete. Requesting reviewer assessment."* Do not paste the report or command output into chat.
4. **Wait for reviewer disposition** (GO / REVISE / STOP)
5. If **REVISE**: address the listed issues, re-test, return to step 3
6. If **STOP**: do not proceed; await human guidance
7. If **GO**: wait for an explicit human message that authorizes commit. Push needs a separate, later human message that authorizes push.
8. Proceed to Commit Instructions only after reviewer GO and explicit human authorization for that specific operation. Commit, push, and task completion are separate actions and require separate human authorization.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first: inspect Git state, stage the source with `git add` if it is untracked, move it using `git mv`, verify the move, and update the YAML status. Then save the STATUS SYNTHESIS REPORT to the summaries folder, including the Step 0 command outputs.
> Do not proceed to Priority 1 until both are complete. The synthesis report does not require separate chat approval.

All agents: follow these steps exactly in order.

### Priority 1 — Investigation

1. Inspect the current original-app working tree and Git state before changing anything. Identify whether the supplied Qwen patch is present, modified, committed, or absent. Preserve unrelated local work.
2. Trace the real lifecycle of `AdminMonitor`:
   - How and when the module is loaded and initialized.
   - The purpose and lifetime of any module-level `initialized` guard or retained state.
   - How initialization behaves on a hard page load versus Turbo visits and repeated visits.
   - Whether event listeners, intervals, timers, canvas references, cached DOM nodes, or prior body data survive page replacement.
   - How the five-second `sphere_data` polling cycle is started, stopped, and bound to the current celestial body.
3. Inspect which Turbo lifecycle events are actually used in this application (e.g., `turbo:before-cache`, `turbo:before-render`, `turbo:load`) and whether any handlers for these events exist in `monitor.js` or related modules. Do not assume all events are currently used or that adding handlers for all of them is necessary — only inspect what is present and assess its correctness.
4. Trace how the current page's body identity and `terrain_data` are supplied to the monitor and how the sidebar is updated. Explicitly verify whether an in-flight asynchronous `sphere_data` polling response from the previous body can arrive after navigation and update the new page. Checking interval cleanup alone is insufficient — trace the full request/response lifecycle including any pending Promises or XHR/fetch calls.
5. Reproduce and compare, where the local environment permits:
   - Direct hard load of each relevant Admin Celestial Body View page (legacy Planetary route).
   - Turbo navigation from one body to another.
   - Repeated navigation back to a previously visited body.
   - Browser back/forward navigation.
   Include at least Earth, Mars, Venus, and Titan; include Eden II if available. If a body or navigation scenario cannot be tested because of an environment limitation, report that limitation clearly rather than treating it as a successful test.
6. Inspect browser console and network activity, plus Rails/server logs as needed. Record actual evidence for initialization, rendering, polling requests, response body identity, and canvas state.

### Priority 2 — Fix

7. Determine whether the supplied Qwen patch is correct. Retain, revise, or reject it based on the verified root cause. Prefer a minimal lifecycle correction over disabling Turbo globally unless evidence shows that is the appropriate existing application pattern.
8. Implement the smallest justified fix in the original Rails 7 app only.

### Priority 3 — Verification

9. Inspect the existing browser/JavaScript-capable test setup and reported feature spec. Add/adjust focused coverage in the conditionally editable spec only if it meaningfully exercises behavior. If another test path is needed, obtain human approval for that exact path before editing.

10. Run meaningful targeted automated tests. If RSpec is used, run inside the verified active GalaxyGame web container, with `unset DATABASE_URL` immediately before RSpec, one process at a time. Verify actual container/service and command locally; do not invent them. GalaxyGame README requires targeted tests only and prohibits blanket full-suite runs, so executor must not run full suite. Record "not run by executor per project rule"; include a dated human-run result only if supplied.

11. Verify through the browser matrix; confirm current-body canvas/sidebar identity and prove a delayed prior-body polling response cannot overwrite the new page.

12. If browser/runtime verification is unavailable, mark affected criteria UNVERIFIED, record the blocker and automated evidence, and stop for human decision.

---

## Acceptance criteria

Mark every criterion PASS, FAIL, or UNVERIFIED in the final evidence report. UNVERIFIED is not a pass and prevents claiming full verification/completion without explicit human decision.

- [ ] The root cause of the blank/stale Admin Celestial Body View page (legacy Planetary route) behavior is identified from current source and runtime evidence, not inferred solely from the screenshots.
- [ ] The investigation establishes whether the module-level initialization guard, event timing, retained DOM/module state, polling lifecycle, or another mechanism causes the defect.
- [ ] The supplied Qwen patch is explicitly assessed against the current working tree and actual browser behavior.
- [ ] The smallest justified fix is implemented in the original Rails 7 app only.
- [ ] Browser matrix covers Earth, Mars, Venus, Titan, and Eden II if available. For each available body test hard load, Turbo arrival, repeated return visits, and back/forward. Record at least three consecutive Turbo transitions per body and one back/forward cycle; explain unavailable paths.
- [ ] Canvas and sidebar identify the selected body on each tested navigation; no previous-body data is displayed.
- [ ] Polling belongs to the current body. Test delayed/in-flight previous-body response with controllable delay/interception or focused automated equivalent; show it cannot overwrite the new page.
- [ ] Record browser console/network evidence and relevant server logs.
- [ ] Focused regression coverage is added/strengthened, or a concrete technical limitation is documented and assessed by reviewer; "where practical" is not a blanket waiver.
- [ ] Exact targeted test commands/results are recorded. Any RSpec uses verified Docker web container and `unset DATABASE_URL`; only one RSpec process at a time.
- [ ] Executor does not run full suite per GalaxyGame README; record "not run by executor per project rule". Cite dated human-run result only if supplied.
- [ ] If browser verification cannot run, affected criteria remain UNVERIFIED and task stops for human decision.
- [ ] No terrain datasets or unrelated terrain-generation/normalization behavior are changed.
- [ ] A synthesis report is saved under `projects/galaxy_game/summaries/` before implementation begins, and a concise completion handoff is provided.
- [ ] No commit, push, or task-completion move/status change occurs without reviewer GO and separate explicit human authorization for that operation.

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch.
- Same failure persists after two focused attempts.
- Root cause is in a shared concern, base class, or factory used across many modules.
- An unanticipated migration or architectural decision is required.
- A required edit is outside authorized application/test/wiki paths; additional paths require human approval before editing.
- A full-load fallback, global Turbo change, terrain-data/normalization change, or other scope expansion is required; obtain explicit human approval.
- Browser/runtime verification is unavailable or cannot demonstrate behavior; mark criteria UNVERIFIED and return for human decision.

---

## Documentation

- Existing Celestial Body View purpose, data flow, endpoints, polling, lifecycle, and regression context are reported as recorded in `docs/wiki/ui/05_admin_celestial_body_view.md`. Verify page and dated snapshot locally; do not duplicate content.
- If implementation changes lifecycle behavior, update that existing page's dated code snapshot during closeout. This focused update is authorized in Files Involved; preserve dated history and do not rewrite unrelated sections.
- If lifecycle behavior does not change, record why no snapshot update was needed.
- [ ] Verify wiki path/snapshot section before editing.
- [ ] Document lifecycle changes with date, source paths, and verified behavior.

---

## Deliverable

Maintain one three-stage synthesis report: (1) initial understanding and Step 0 evidence before investigation; (2) investigation/lifecycle/root-cause or unresolved status/Qwen patch/matrix/proposed fix/test plan before implementation; (3) actual changes, exact tests, browser matrix, wiki update, acceptance disposition, and remaining issues after implementation. Stage 2 and human approval of the proposed approach are required before implementation. Chat contains only report path and concise status. Closeout handoff identifies changed files, tests, issues, evidence basis, and any separately recommended Venus rendering follow-up.

---

## Dependencies

**Blocked by**:
- Human review/approval of this draft, then separate human authorization for backlog movement and dispatch.
- Availability of the original Rails 7 application and usable local browser/runtime.

**Blocks**: Porting the affected lifecycle behavior/fix into the separate Rails 8 prototype until Rails 7 behavior is investigated and stabilized. Unrelated Rails 8 prototype work is not blocked.

**Related tasks / overlap**: NOT VERIFIED. Before backlog approval, check current GalaxyGame `status.md` and active/backlog inventory; record date, paths/commands, and findings. Do not claim none until checked.

---

## Progress / Resume

- **Done so far:** Reviewed Perplexity's supplied REVISE report and compared it with the task, generic template, review guide, and GalaxyGame README. Revised the draft for structural, scope, evidence, testing, verification, and closeout gaps.
- **Next step:** Verify this revision against full template/project guidance, then submit the three-file package for external re-review. Human approval remains required before backlog movement or dispatch.
- **Last verified fact:** GitHub fetch of the prior task version returned blob SHA `7008dfe8e1f5c7f7b216f79fba25dbd9da3e516d`. Perplexity's report is supplied evidence; application code/runtime claims are not independently verified here.
- **Original-app Git state:** Not checked.
- **Application paths/runtime:** Reported from supplied context; not independently verified.
- **Task overlap inventory:** Not checked.
- **Owning session / window label:** Planning — Admin Celestial Body View regression.

---

## Commit Instructions

Run Git on the host from the correct repository root, never inside Docker. Never use `git add .`; stage only specifically authorized files.

Before each operation, both conditions are required:
1. Reviewer GO on the implementation evidence.
2. Explicit human authorization naming the operation about to be performed.

Record both in the evidence report. An agent approval prompt is not authority before reviewer GO. Commit, push, and task completion are separate actions; authorization for one does not authorize another.

### Commit
Only after reviewer GO and explicit human commit authorization:
```bash
git status --short
git add <each specifically authorized application/test/wiki/task/report path>
git diff --cached --check
git diff --cached --stat
git diff --cached
git commit -m "fix: restore Admin Celestial Body View Turbo lifecycle"
```
Run on host. Do not stage unrelated user changes.

### Push
Only after a separate, later human message explicitly authorizes push:
```bash
git push
```

### Active → completed task-file lifecycle
Implementation completion or reviewer GO alone does not complete the task. After separate human completion authorization, use `projects/galaxy_game/tasks/completed/YYYY-MM/` as the destination, move the tracked task with `git mv`, update YAML to `status: completed`, verify source absence and exactly one copy, and record outputs. Never use plain `mv` or `cp`. If unexpectedly untracked, stop and ask.

---

## Completion Report

*Implementation agent completes this after implementation/verification; leave pending in draft.*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final targeted test result**: [exact assertions/examples and failures, or N/A with reason]
**Full-suite disposition**: [not run by executor per GalaxyGame targeted-testing-only rule / human-run result with dated evidence]
**Browser/runtime result**: [PASS / FAIL / UNVERIFIED and matrix reference]
**Evidence basis**: [direct verification / supplied evidence / reported by agent / human assertion] — [source and scope]

### What was changed
- [file] — [verified description]

### Verification performed
- [exact command] — [result]
- [browser scenario/matrix] — [result]

### Documentation updated
- [wiki path and dated snapshot section, or reason no update was needed]

### Acceptance criteria disposition
- [criterion] — [PASS / FAIL / UNVERIFIED] — [evidence]

### Issues discovered
[Unexpected implementation issues.]

### Follow-up tasks needed
[New backlog items identified; do not create files.]

### Remaining limitations
[Blocked runtime, unavailable body, omitted coverage, or other limitation. Never label unverified criteria as passed.]

### Review and authorization record
- Reviewer disposition/date: [GO / REVISE / STOP; reviewer/date]
- Human commit authorization: [exact authorization/date or pending]
- Human push authorization: [exact authorization/date or pending]
- Human completion authorization: [exact authorization/date or pending]

### Lessons learned
[What worked, what did not, and future-task guidance.]

---

## Handoff Summary

HANDOFF SUMMARY: Draft created for original Rails 7 Admin Celestial Body View (legacy Planetary route) Turbo lifecycle regression | Qwen patch treated as candidate pending verification | Human review required before backlog approval or dispatch.
