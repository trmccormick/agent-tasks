---
status: draft
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## Agent Dispatch Interface

**Status**: DRAFT — NOT READY FOR DISPATCH. Human review and approval are required first.

This draft does not authorize implementation. The human must review and approve the task, move it through the prescribed lifecycle, and complete dispatch details before any agent begins work.

## Draft status

This is a review-only draft for the original Rails 7 GalaxyGame application. It is not approved for backlog or implementation dispatch.

The separate Rails 8 `galaxy-game-ui` prototype is paused. Do not work in that repository as part of this task.

## TASK: Restore reliable Admin Planetary monitor rendering across Turbo navigation

**Status**: DRAFT  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-11-15  
**Author**: ChatGPT (draft for human review)

## Context

The original Rails 7 GalaxyGame application previously loaded and displayed planetary maps for Earth, Mars, Venus, and Titan. The user reports that these pages no longer behave reliably. The current issue must be investigated and corrected in the original application before related monitor functionality is ported to the separate Rails 8 UI prototype.

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

## Purpose

Establish the actual cause of blank planetary canvases and stale cross-body sidebar data when navigating among Admin Planetary pages in the original Rails 7 application, then make the smallest correct fix and verify it.

The task is deliberately bounded to the Admin Planetary page lifecycle and rendering behavior. It is not a general terrain-loader rewrite or a port to the Rails 8 prototype.

## Scope

1. Inspect the current original-app working tree and Git state before changing anything. Identify whether the supplied Qwen patch is present, modified, committed, or absent. Preserve unrelated local work.
2. Trace the real lifecycle of `AdminMonitor`:
   - How and when the module is loaded and initialized.
   - The purpose and lifetime of any module-level `initialized` guard or retained state.
   - How initialization behaves on a hard page load versus Turbo visits and repeated visits.
   - Whether event listeners, intervals, timers, canvas references, cached DOM nodes, or prior body data survive page replacement.
   - How the five-second `sphere_data` polling cycle is started, stopped, and bound to the current celestial body.
3. Trace how the current page's body identity and `terrain_data` are supplied to the monitor and how the sidebar is updated. Confirm whether a prior page's response can update the newly visited page.
4. Reproduce and compare, where the local environment permits:
   - Direct hard load of each relevant Admin Planetary page.
   - Turbo navigation from one body to another.
   - Repeated navigation back to a previously visited body.
   - Browser back/forward navigation.
   Include at least Earth, Mars, Venus, and Titan; include Eden II if it is available in the current local data.
5. Inspect browser console and network activity, plus Rails/server logs as needed. Record actual evidence for initialization, rendering, polling requests, response body identity, and canvas state. Do not rely only on screenshots or simulated lifecycle tests.
6. Determine whether the supplied Qwen patch is correct. Retain, revise, or reject it based on the verified root cause. Prefer a minimal lifecycle correction over disabling Turbo globally unless evidence shows that is the appropriate existing application pattern.
7. Add or adjust focused regression coverage for the lifecycle behavior where the current test setup can meaningfully exercise it. Do not add broad or brittle tests that merely assert implementation details.
8. Verify the fix through the same navigation paths that reproduced the problem. Confirm that the current body's canvas and sidebar remain associated with the current body and that old polling cannot overwrite the new page.

## Explicit boundaries

- Work only in the original Rails 7 application repository: `trmccormick/galaxyGame`.
- Do not modify the separate Rails 8 `galaxy-game-ui` prototype.
- Do not regenerate, convert, replace, or rewrite any planetary source or processed terrain datasets.
- Do not change terrain source-file priorities, GeoTIFF/ASC parsing, elevation values, sea-level logic, or terrain normalization as part of this task unless the lifecycle investigation proves one is directly responsible for the blank/stale-page defect. If a distinct Venus data/normalization defect is confirmed, document it as a separate follow-up rather than broadening this task.
- Do not use `data-turbo="false"` globally or disable Turbo across the application as a shortcut. A narrowly scoped full-load fallback may be proposed only if the investigation demonstrates that it is necessary and consistent with existing app behavior.
- Do not refactor the monitor or redesign the planetary UI.
- Do not change unrelated files or clean up pre-existing modifications.
- Do not run the full RSpec suite. Follow the project targeted-test rules, including `unset DATABASE_URL`, one RSpec process at a time, and log-file redirection.
- No commits or pushes without explicit human approval.

## Required investigation evidence

The synthesis report must distinguish:
- **Verified**: directly observed in the current source/runtime or reproduced with commands.
- **Historical**: supported by Git history, prior reports, or user-provided screenshots.
- **Hypothesis**: plausible explanation not yet reproduced or confirmed.
- **Unresolved**: unavailable evidence or environment limitation.

At minimum, record:
- Current Git branch and working-tree status before edits.
- Whether the supplied Qwen diff is present and its exact current disposition.
- Relevant source paths and the lifecycle call sequence.
- Hard-load versus Turbo-navigation results.
- Whether stale polling responses are observed and how they are prevented after the fix.
- Targeted test commands and exact results, or why a test could not be run.

## Acceptance criteria

- [ ] The root cause of the blank/stale Admin Planetary page behavior is identified from current source and runtime evidence, not inferred solely from the screenshots.
- [ ] The investigation establishes whether the module-level initialization guard, event timing, retained DOM/module state, polling lifecycle, or another mechanism causes the defect.
- [ ] The supplied Qwen patch is explicitly assessed against the current working tree and actual browser behavior.
- [ ] The smallest justified fix is implemented in the original Rails 7 app only.
- [ ] Navigating between Earth, Mars, Venus, and Titan using both hard loads and Turbo visits no longer produces a blank canvas or previous-body sidebar/polling data.
- [ ] Regression coverage is added or existing focused coverage is strengthened where practical.
- [ ] Relevant targeted tests pass; any pre-existing failures or environment limitations are reported without being misrepresented.
- [ ] No terrain datasets or unrelated terrain-generation/normalization behavior are changed.
- [ ] A synthesis report is saved under `projects/galaxy_game/summaries/` before implementation begins, and a concise completion handoff is provided.
- [ ] No commit or push is made without explicit human approval.

## Deliverable

Before implementation, save a synthesis report in `projects/galaxy_game/summaries/` with a date-prefixed filename. It must contain the investigation evidence, lifecycle trace, root-cause determination (or explicit unresolved status), Qwen patch assessment, reproduction matrix, proposed minimal correction, and focused test plan.

After implementation, update that report with the actual changes and verification results. At closeout, provide a concise handoff identifying changed files, tests run, remaining issues, and any separate follow-up task recommended for Venus's uniform-orange rendering.

## Dependencies

**Blocked by**: Human review and approval of this draft; availability of the original Rails 7 application and its local runtime for reproduction.  
**Blocks**: Any port of the Admin Planetary monitor lifecycle behavior to the separate Rails 8 UI prototype.  
**Related tasks**: None identified at draft creation.

## Progress / Resume

- **Done so far:** Reviewed Claude's screenshot-based findings and the supplied Qwen diff; inspected the GalaxyGame task template, project guidance, and current drafts location.
- **Next step:** Human review of scope and dispatch readiness; do not dispatch while this file remains in `tasks/drafts/`.
- **Last verified fact (with command):** The supplied `qwen-monitor-diff.txt` contains the single `monitor.js` scheduling change reproduced above. Source: user-provided attachment.
- **Local-only commits:** Not checked; implementation agent must inspect the original app's Git state before work.
- **Owning session / window label:** Planning — Admin Planetary monitor regression.

## Completion Report

Not applicable while this task remains a draft.

## Handoff Summary

HANDOFF SUMMARY: Draft created for original Rails 7 Admin Planetary Turbo lifecycle regression | Qwen patch treated as candidate pending verification | Human review required before backlog approval or dispatch.
