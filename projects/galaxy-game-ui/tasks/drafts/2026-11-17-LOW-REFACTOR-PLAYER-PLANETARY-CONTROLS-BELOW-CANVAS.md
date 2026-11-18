---
status: draft
priority: LOW
type: refactor
system_domain: UI
---

# Player Planetary — move canvas controls beneath the map

## Objective

Correct the Player Planetary workspace structure in the Rails 8 prototype. The map and its controls belong in the left workspace pane; the contextual data panel occupies the full-height right pane.

**Authoritative design:** [Main GalaxyGame UI wiki — shared workspace layout](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md#shared-workspace-layout-pattern).

## Allowed files

In the `galaxy-game-ui` repository (the Rails app is nested inside its Git root):

- `galaxy-game-ui/app/views/player/planetary.html.erb`
- `galaxy-game-ui/app/assets/stylesheets/pages/player_planetary.css`

Do not edit JavaScript, controllers, routes, shared layout, data sources, or other files.

## Changes

- Move the layer buttons, Civ chips, zoom control, and Reset control directly beneath the map canvas, confined to the left pane.
- Make the map fill the available upper area of the left pane.
- Keep the right data panel full height alongside the map and controls; it contains data only, no canvas controls.
- Remove the duplicate title rendered inside the map. Keep the page-level planetary heading/body facts.
- Correct the pane sizing, flex, overflow, and scrolling as needed without fragile viewport-height calculations.
- Preserve the existing visual design and working map/layer/geographic-feature behavior.

## Important safeguards

- `planetary.js` is out of scope and must remain untouched. Preserve every existing DOM ID and JavaScript hook.
- The Civ chips have local, uncommitted work. Before editing, inspect `git status` and the relevant diff. Preserve those exact changes and incorporate the chips into the beneath-canvas control area; do not reset, stash, overwrite, or recreate them from guesswork.
- The older completed layout task describes a toolbar above the map. That arrangement is superseded by the 2026-11-17 established wiki decision. Do not follow it.
- Data-readout typography/spacing and progress-bar percentage formatting belong to a separate follow-up task.
- Do not change the shared navigation or add a MOCK DATA badge here.

## Verification and review gate

1. Inspect the working tree and relevant diffs before editing; report unexpected changes.
2. Make only the two allowed file changes.
3. Run the prototype using its documented Docker/container workflow.
4. Open Player Planetary in a browser and verify the map, controls, right data panel, scrolling, and responsive behavior. Capture a screenshot.
5. Report the exact diff and verification results to Tracy.

Stop after this task for visual review. Do not start the next Player Planetary task until Tracy has reviewed this result. Do not commit or push without Tracy's explicit approval, as required by GUARDRAILS Rule 26.

## Acceptance criteria

- [ ] Map occupies the upper portion of the left workspace pane.
- [ ] Layer buttons, Civ chips, zoom, and Reset sit directly beneath the map and remain in the left pane.
- [ ] Right data panel spans the workspace height and contains no canvas controls.
- [ ] Duplicate in-map title is removed; page heading/body facts remain.
- [ ] Existing IDs, JavaScript hooks, map behavior, and geographic-feature behavior are preserved.
- [ ] Only the two allowed files are changed; existing local Civ chips edits are preserved.
- [ ] Browser verification and screenshot are reported.
