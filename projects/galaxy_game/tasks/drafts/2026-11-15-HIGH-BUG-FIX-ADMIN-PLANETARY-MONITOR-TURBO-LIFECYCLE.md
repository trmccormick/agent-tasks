---
status: draft
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/current/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

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

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md

STEP 0 — STAGE IF NECESSARY, THEN MOVE THE TASK WITH GIT MV (no exceptions):
  Source:
    projects/galaxy_game/tasks/backlog/current/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md
  Destination:
    projects/galaxy_game/tasks/active/2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md

  1. Inspect the task file's Git state before moving it:
       git status --short -- <source-path>
       git ls-files --error-unmatch <source-path>
     Do not assume that a file in backlog is already committed or tracked.
  2. If the source file is not yet tracked by Git (for example, it is newly created or untracked), stage it first:
       git add <source-path>
     This ensures the file is in Git's index before the move.
  3. Move the task using Git in every case:
       git mv <source-path> <destination-path>
     Do not use plain mv or cp as the task-file move operation. If the file was already tracked, run git mv directly; if it was not tracked, git add it first and then run git mv.
  4. Verify the move:
       git status --short
       git ls-files --error-unmatch <destination-path>
       test ! -e <source-path> && test -f <destination-path>
     Confirm that the source no longer exists, the destination exists, and Git tracks the destination. Preserve any pre-existing task changes; do not discard or overwrite them.
  5. Open the destination file and change its YAML status from `backlog` to `active`. Verify the updated status.
  6. Record the Git-state inspection, any staging command, the `git mv`, and verification outputs in the synthesis report. Create the synthesis report only after Steps 1–5 are complete, and before beginning investigation or implementation.

  In chat, provide only a concise status update and the report path; do not paste the report or command output.
  Do NOT start investigation or implementation until the move, status update, and synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-11-15-HIGH-BUG-FIX-ADMIN-PLANETARY-MONITOR-TURBO-LIFECYCLE.md"
    Only ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxy_game/README.md
  3. <agent-tasks>/projects/galaxy_game/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold the standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

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
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS — all required template sections present
- **Docker Wrapper Check**: N/A — JavaScript lifecycle defect, no Docker/RSpec involved
- **MVP Alignment**: VALID — task addresses a regression in the original Rails 7 app before any port to Rails 8 UI prototype
- **MVP Impact Note**: Resolving this defect ensures the Admin Celestial Body View works reliably in the production application before any migration work begins.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Local worker has terminal/tool-use access to inspect the original app working tree and run targeted tests.
**Local attempts before cloud**: N/A — first dispatch of this task.
**Supervision Level**: Watched carefully — all agents on first dispatch of a task.

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

### Primary Files — you will inspect/edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/javascript/admin/monitor.js` | Admin Celestial Body View JS module | Initialization, Turbo event handling, polling lifecycle |
| `app/views/admin/celestial_bodies/planetary.html.erb` | Admin Celestial Body View page (legacy Planetary route) template | DOM structure, data attributes, Turbo links |
| `app/controllers/admin/celestial_bodies_controller.rb` | Controller serving legacy Celestial Body View / Planetary route | `terrain_data` response, body identity |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/javascript/application.js` | Module loading and Turbo integration |
| `config/routes.rb` | Legacy Monitor and Planetary route definitions for celestial bodies |
| `spec/features/admin/planetary_monitor_spec.rb` | Existing focused coverage for monitor behavior |

### Migration (if needed)
- [x] No migration needed — this is a JavaScript lifecycle defect.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not assume the Qwen patch is correct without verifying it against the current working tree and actual browser behavior.
- ❌ Wrong: Apply the Qwen patch blindly because it looks like a reasonable simplification.
- ✅ Right: Verify whether the patch is present in the working tree, test it under real Turbo navigation, and assess its effect on the full lifecycle (init → render → poll → navigate).
- Why: The patch removes a `turbo:load` listener that may be essential for re-initialization on subsequent visits. Disabling it without evidence could worsen the defect.

⚠️ **GOTCHA 2**: Do not disable Turbo globally or use `data-turbo="false"` as a shortcut.
- ❌ Wrong: Add `data-turbo="false"` to all planetary links or disable Turbo across the application.
- ✅ Right: Investigate whether a narrowly scoped full-load fallback is consistent with existing app patterns, and only propose it if the investigation demonstrates necessity.
- Why: The defect is in the JavaScript lifecycle, not Turbo itself. Global Turbo disabling would break other functionality and mask the root cause.

⚠️ **GOTCHA 3**: Do not modify terrain datasets or terrain-generation/normalization code unless the investigation proves one is directly responsible.
- ❌ Wrong: Rewrite terrain loaders, GeoTIFF parsing, or normalization as part of this task.
- ✅ Right: If a distinct Venus data/normalization defect is confirmed, document it as a separate follow-up task.
- Why: This task is bounded to the Admin Celestial Body View page (legacy Planetary route) lifecycle and rendering behavior only.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

After completing Step 0 (including checking/staging the task file as needed, moving it with `git mv`, verifying the destination, and updating the YAML status), but before beginning investigation or implementation, you MUST create a **synthesis report** as an MD file under `projects/galaxy_game/summaries/`. Record the Step 0 Git-state, staging (if needed), move, and verification command outputs in that report. This report demonstrates you understand the task before executing the investigation.

**Synthesis Report Template** (save as MD file to `projects/galaxy_game/summaries/YYYY-MM-DD-bug-fix-admin-planetary-monitor-turbo-lifecycle.md`):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Restore reliable Admin Celestial Body View rendering across Turbo navigation
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/javascript/admin/monitor.js` | Monitor JS lifecycle | pending |
| [other files] | [description] | [pending / done] |

### Prerequisites Completed
- ✅ Step 0: Inspected whether the task file was tracked/committed; staged the source with `git add` first if it was untracked
- ✅ Step 0: Moved the task file to active/ using `git mv` (command output recorded in this report)
- ✅ Step 0: Verified source removal, destination existence, and Git tracking
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Blindly applying the Qwen patch — instead ✅ verify against working tree and browser behavior
- ❌ Disabling Turbo globally — instead ✅ investigate minimal lifecycle correction
- ❌ Modifying terrain datasets — instead ✅ document as separate follow-up if needed

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

Save this report before beginning investigation or implementation. The report is a required file artifact, not a chat approval gate. In chat, provide its path and a concise status update only; do not paste the report.

---

## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

**You MAY NOT commit, push, or mark any task step as "complete" until BOTH of the following conditions are met:**

1. **Reviewer has issued a GO disposition** — a reviewer has assessed your work and disposed it as one of: `GO`, `REVISE`, or `STOP`. See REVIEW_AGENT_GUIDE.md for reviewer authority definitions.
2. **Human authorization** — Requires an explicit human message that authorizes commit (for example, 'commit it') and an explicit human message that authorizes push (for example, 'push it'). Authorization to commit does not imply authorization to push, and authorization to push does not imply authorization to commit.

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
3. **STOP** — paste your evidence in chat and state: *"All implementation and verification complete. Requesting reviewer assessment."*
4. **Wait for reviewer disposition** (GO / REVISE / STOP)
5. If **REVISE**: address the listed issues, re-test, return to step 3
6. If **STOP**: do not proceed; await human guidance
7. If **GO**: wait for an explicit human message that authorizes commit. Push needs a separate, later human message that authorizes push.
8. Once **both** conditions are satisfied: proceed to Commit Instructions below

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

6. Determine whether the supplied Qwen patch is correct. Retain, revise, or reject it based on the verified root cause. Prefer a minimal lifecycle correction over disabling Turbo globally unless evidence shows that is the appropriate existing application pattern.
7. Implement the smallest justified fix in the original Rails 7 app only.

### Priority 3 — Verification

8. Add or adjust focused regression coverage for the lifecycle behavior where the current test setup can meaningfully exercise it. Do not add broad or brittle tests that merely assert implementation details.
9. Verify the fix through the same navigation paths that reproduced the problem. Confirm that the current body's canvas and sidebar remain associated with the current body and that old polling cannot overwrite the new page.

---

## Acceptance criteria

- [ ] The root cause of the blank/stale Admin Celestial Body View page (legacy Planetary route) behavior is identified from current source and runtime evidence, not inferred solely from the screenshots.
- [ ] The investigation establishes whether the module-level initialization guard, event timing, retained DOM/module state, polling lifecycle, or another mechanism causes the defect.
- [ ] The supplied Qwen patch is explicitly assessed against the current working tree and actual browser behavior.
- [ ] The smallest justified fix is implemented in the original Rails 7 app only.
- [ ] Navigating between Earth, Mars, Venus, and Titan using both hard loads and Turbo visits no longer produces a blank canvas or previous-body sidebar/polling data.
- [ ] Regression coverage is added or existing focused coverage is strengthened where practical.
- [ ] Relevant targeted tests pass; any pre-existing failures or environment limitations are reported without being misrepresented.
- [ ] No terrain datasets or unrelated terrain-generation/normalization behavior are changed.
- [ ] A synthesis report is saved under `projects/galaxy_game/summaries/` before implementation begins, and a concise completion handoff is provided.
- [ ] No commit or push is made without explicit human approval.

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many modules
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Documentation
- [x] The existing Admin Celestial Body View purpose, data flow, endpoints, polling behavior, current Turbo lifecycle implementation, and known regression context are recorded in `docs/wiki/ui/05_admin_celestial_body_view.md`. Do not duplicate that material in this task. If implementation changes the lifecycle behavior, update the wiki's dated code snapshot during closeout.

---

## Deliverable

Before implementation, save a synthesis report in `projects/galaxy_game/summaries/` with a date-prefixed filename. It must contain the investigation evidence, lifecycle trace, root-cause determination (or explicit unresolved status), Qwen patch assessment, reproduction matrix, proposed minimal correction, and focused test plan.

After implementation, update that report with the actual changes and verification results. At closeout, provide a concise handoff identifying changed files, tests run, remaining issues, and any separate follow-up task recommended for Venus's uniform-orange rendering.

---

## Dependencies

**Blocked by**: Human review and approval of this draft; availability of the original Rails 7 application and its local runtime for reproduction.  
**Blocks**: Any port of the Admin Celestial Body View lifecycle behavior to the separate Rails 8 UI prototype.  
**Related tasks**: None identified at draft creation.

---

## Progress / Resume

- **Done so far:** Reviewed Claude's screenshot-based findings and the supplied Qwen diff; inspected the GalaxyGame task template, project guidance, and current drafts location.
- **Next step:** Human review of scope and dispatch readiness; do not dispatch while this file remains in `tasks/drafts/`.
- **Last verified fact (with command):** The supplied `qwen-monitor-diff.txt` contains the single `monitor.js` scheduling change reproduced above. Source: user-provided attachment.
- **Local-only commits:** Not checked; implementation agent must inspect the original app's Git state before work.
- **Owning session / window label:** Planning — Admin Celestial Body View regression.

---

## Completion Report

Not applicable while this task remains a draft.

---

## Handoff Summary

HANDOFF SUMMARY: Draft created for original Rails 7 Admin Celestial Body View (legacy Planetary route) Turbo lifecycle regression | Qwen patch treated as candidate pending verification | Human review required before backlog approval or dispatch.
