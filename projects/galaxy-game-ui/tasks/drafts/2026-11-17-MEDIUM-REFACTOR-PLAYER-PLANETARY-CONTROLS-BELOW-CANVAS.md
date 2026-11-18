---
status: draft
priority: MEDIUM
type: refactor
system_domain: UI
---

# Player Planetary — responsive canvas workspace and controls below map

## Objective

Correct the Player Planetary workspace structure in the Rails 8 prototype. The persistent left navigation and right contextual data panel should remain mostly stable in width while the center visualization pane uses the remaining available space. The existing Planetary renderer remains the prototype visualization; this task makes its workspace and display sizing responsive without declaring one map geometry suitable for every celestial body.

**Authoritative design:** [Main GalaxyGame UI wiki — shared workspace layout](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md#shared-workspace-layout-pattern).

**Long-term architecture boundary:** [Main GalaxyGame UI wiki — irregular and repurposed body visualization](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md#irregular-and-repurposed-body-visualization). The current Planetary view is appropriate for this prototype's present scope, but it is not the universal long-term visualization for every celestial body. Do not implement alternate asteroid/moon renderers in this task.

## Allowed files

In the `galaxy-game-ui` repository (the Rails app is nested inside its Git root):

- `galaxy-game-ui/app/views/player/planetary.html.erb`
- `galaxy-game-ui/app/assets/stylesheets/pages/player_planetary.css`
- `galaxy-game-ui/app/javascript/player/planetary.js` — only the existing Planetary map sizing/resize code that must change to support the responsive center pane. Confirm this path and the relevant functions before editing; do not create a second renderer or duplicate sizing logic.

Do not edit controllers, routes, shared layout, data sources, other JavaScript, or other files. If the necessary sizing behavior cannot be isolated to these files, stop and report before expanding scope.

## Changes

- Keep the persistent left navigation and right contextual data panel mostly constant in width; make the center workspace pane flexible so it consumes the available horizontal space.
- Place the map canvas in the upper portion of the center/left workspace pane. Move the layer buttons, Civ chips, zoom control, and Reset control directly beneath the map, all confined to that pane.
- The shared dashboard layout currently constrains its main content with `max-w-6xl mx-auto`. Work within that existing constraint; do not edit the shared layout as part of this task. If this maximum-width constraint prevents the center pane from using the available space as intended, stop and report the limitation rather than expanding scope.
- Keep the right data panel full height alongside the map and controls. It contains contextual data only, no canvas controls.
- Make the displayed map use the available center-pane area at different viewport sizes. Recalculate its display sizing when the viewport or center-pane dimensions change; do not rely on one-time initialization dimensions.
- Inspect how the selected body's map dimensions and geometry are currently supplied. Preserve the actual per-body map dimensions, aspect ratio, and data bounds; do not force every celestial body into one universal 800×400 canvas, tile count, or aspect ratio. Do not destructively resize source terrain or map data.
- Scale/fit the current prototype map to the available display area without stretching or distorting its geometry, cropping away map content, or leaving avoidable black gutters caused by fixed wrapper/scroller dimensions. If the body's actual geometry/aspect ratio cannot occupy every pixel without distortion, preserve the geometry and use the existing appropriate fit/zoom/background behavior rather than falsifying the body shape.
- Remove the duplicate title text rendered inside the map while retaining the `#mapTitle` element/overlay container structure required by the current JavaScript sizing code. The JS currently references it; remove or hide only the duplicate visible title text without removing or renaming the required hook. Keep the page-level planetary heading and body facts.
- Correct pane sizing, flex, overflow, and scrolling as needed. Avoid fragile hard-coded viewport-height calculations.

## Important safeguards

- A small, focused edit to the existing `galaxy-game-ui/app/javascript/player/planetary.js` map sizing path is explicitly allowed because the current implementation initializes fixed overlay/scroller dimensions once and does not adequately respond to container resizing. Locate and inspect `setupScrollableMap` and its related one-time rectangle/dimension sizing before editing; treat that as the primary investigation/edit surface. Do not refactor unrelated map behavior or renderer architecture.
- Preserve all existing DOM IDs, classes, data attributes, event hooks, and layer/geographic-feature behavior. In particular, layer controls/chips must retain the `.layer-btn` and `data-layer` hooks used by the existing JavaScript.
- **Civ chips precondition:** the local Civ chips work must be committed separately before this task begins; do not include that work in this task. At task start, inspect the committed working-tree state and confirm that the expected Civ chips and their DOM hooks are actually present in the current view/markup. If the chips or expected hooks are absent, stop and report; do not invent or implement them. Also require a clean working tree. If it is not clean, stop without editing. Stop if any unexpected change touches files outside this task's allowed list. Do not reset, stash, overwrite, or recreate prior work.
- The older completed layout task describes a toolbar above the map. That arrangement is superseded by the 2026-11-17 established wiki decision. Do not follow it.
- Data-readout typography/spacing and progress-bar percentage formatting belong to a separate task.
- Do not change shared navigation or add a MOCK DATA badge.
- The wiki's long-term multiple-visualization requirement is context only. Do not add asteroid visualization, new body classification logic, a new renderer, Digital Twin redesign, or new map data contracts here.

## Verification and review gate

1. Inspect `git status`, the current implementation, and the relevant diffs before editing. Confirm the working tree is clean, the separately committed Civ chips and their DOM hooks are present, and only expected task files will be changed; otherwise stop and report. Confirm any earlier task changes are already committed and present in the current branch before editing shared files.
2. Identify the actual per-body map dimensions/geometry path and the fixed sizing assumptions in the existing renderer before modifying them. Inspect `setupScrollableMap` and related one-time sizing logic. Check whether the shared `max-w-6xl mx-auto` content constraint limits the intended center-pane behavior; if it does, stop and report rather than editing the shared layout.
3. Make only the allowed file changes. Afterward, report `git diff --stat`, the complete changed-file list, and confirm no other files changed.
4. Run static checks available in the repository: JavaScript syntax/lint checks applicable to the edited file, ERB/markup sanity checks, and any relevant existing tests. Do not claim browser verification.
5. Write the task report to `~/scratch/player-planetary-controls-below-canvas-report.md` using shell redirection. Include findings, exact diff summary, checks run/results, and anything not verified. Keep this report for Tracy's review; remove only temporary files created during the task.
6. Stop for Tracy to run the app, inspect the browser at more than one viewport size and capture screenshots. Do not start the readout task until Tracy has reviewed this layout result.
7. Do not commit or push without Tracy's explicit approval, as required by GUARDRAILS Rule 26.

## Acceptance criteria

- [ ] Persistent left navigation and right data panel remain mostly stable in width while the center pane flexes with available space.
- [ ] Map display resizes with the center pane and responds to viewport/container resizing.
- [ ] Map uses the selected body's actual dimensions/aspect ratio/data bounds; no universal fixed map geometry is imposed.
- [ ] Map geometry is not distorted or destructively changed, and avoidable black gutters from fixed sizing are eliminated.
- [ ] Layer buttons, Civ chips, zoom, and Reset sit directly beneath the map and remain in the center/left pane.
- [ ] Right data panel spans the workspace height and contains no canvas controls.
- [ ] Duplicate in-map title text is removed without breaking the `#mapTitle` element/overlay structure still referenced by JavaScript; page heading/body facts remain.
- [ ] Existing layer toggles, geographic features, IDs, and JavaScript hooks continue to work.
- [ ] Only the allowed files are changed; static verification and report file are provided.
- [ ] Browser review and screenshots are left to Tracy; no browser verification is claimed by Qwen.
