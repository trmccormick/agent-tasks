---
status: draft
priority: LOW
type: polish
system_domain: UI
---

# Player Planetary — compact the data readout

## Objective

Improve the readability and density of the right-hand Player Planetary data panel after the canvas/control layout task has been reviewed and accepted.

**Design reference:** [Main GalaxyGame UI wiki — shared workspace layout](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md#shared-workspace-layout-pattern). The right pane uses compact, readable section titles, data rows, and progress bars.

## Allowed files

- `galaxy-game-ui/app/assets/stylesheets/pages/player_planetary.css` (primary; make CSS changes first)
- `galaxy-game-ui/app/views/player/planetary.html.erb` (only if needed for the progress-bar percentage correction)

Do not edit JavaScript, controllers, routes, shared layout, data sources, or other files.

## Changes

- Reduce data-panel text sizing where needed while keeping labels and values comfortably readable.
- Apply consistent, compact spacing between data sections and rows.
- Keep each label/value pair aligned on one line where the available width permits; handle narrow widths without overlap.
- Correct the Biodiversity and Habitability progress-bar widths so percentage values are emitted as valid CSS percentages (for example, `42%`, not `42`).
- Preserve the existing section order, values, data source, colors, and overall visual style.

## Out of scope

- Do not change the workspace/pane architecture or move the canvas controls.
- Do not alter map sizing, canvas rendering, layer behavior, Civ chips, navigation, or the shared application layout.
- Do not change the underlying data or calculate new metrics.
- Do not reformat unrelated markup or perform broad CSS cleanup.

## Verification and review gate

1. Confirm the previous controls/layout task has been reviewed and accepted before starting.
2. Inspect the working tree and relevant diffs; preserve unrelated local changes.
3. Make only the allowed readout changes.
4. Run the prototype using its documented container workflow and inspect the page in a browser.
5. Verify label/value alignment, section rhythm, narrow-width behavior, and both progress bars. Capture a screenshot and report the exact diff and checks.

Stop for Tracy's visual review before proceeding to the next task. Do not commit or push without Tracy's explicit approval (GUARDRAILS Rule 26).

## Acceptance criteria

- [ ] Data-panel typography is compact but legible.
- [ ] Section and row spacing is consistent.
- [ ] Label/value pairs align on one line where space permits, without clipping or overlap.
- [ ] Biodiversity and Habitability bars render their intended percentage widths.
- [ ] Workspace structure and data behavior are unchanged.
- [ ] Only the allowed files are changed; browser verification and screenshot are reported.
