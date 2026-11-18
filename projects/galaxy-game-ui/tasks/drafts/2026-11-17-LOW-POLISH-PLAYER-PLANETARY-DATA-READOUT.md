---
status: draft
priority: LOW
type: polish
system_domain: UI
---

# Player Planetary — compact the data readout

## Objective

Improve the readability and density of the right-hand Player Planetary data panel. This is a focused readout polish task; it does not depend on a particular map-renderer implementation. Because it shares the Planetary stylesheet with the canvas layout task, do not run both tasks concurrently or overwrite changes from the other task.

**Design reference:** [Main GalaxyGame UI wiki — shared workspace layout](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md#shared-workspace-layout-pattern). The right pane uses compact, readable section titles, data rows, and progress bars.

## Allowed files

- `galaxy-game-ui/app/assets/stylesheets/pages/player_planetary.css` (primary; make CSS changes first)
- `galaxy-game-ui/app/views/player/planetary.html.erb` (only if needed for the progress-bar percentage correction)

Do not edit JavaScript, controllers, routes, shared layout, data sources, or other files.

## Changes

- Use the existing extra-small typography token (currently 0.75rem) for data-row labels and values. Keep section headings on the existing extra-small heading token; do not globally shrink page headings or map controls.
- Keep the existing small row-text token (currently 0.875rem) for any readout content that demonstrably needs the additional emphasis for legibility; explain any exception in the report rather than applying broad font-size changes.
- Set compact, consistent data-row vertical padding to approximately 4px and section-to-section spacing to approximately 10px, adjusting only where existing component structure requires it.
- Keep each label/value pair on one line when it fits. When a value is too long, allow the value to wrap within its own value area or truncate it with a clear CSS overflow treatment; do not let it overlap the label, escape the panel, or force horizontal scrolling. Prefer wrapping where it preserves the full value.
- Preserve the existing section order, values, data source, colors, and overall visual style.
- Correct the Biodiversity and Habitability progress-bar widths so percentage values are emitted as valid CSS percentages (for example, `42%`, not `42`). The current mock ERB values need the percent suffix; do not alter JavaScript data behavior.

## JavaScript-generated readout content

The existing JavaScript rewrites portions of the panel at load, including composition rows and their inline styles. Do not edit or depend on ERB text content for formatting that JavaScript replaces. Apply typography, spacing, alignment, and overflow rules through stable CSS classes/selectors that apply to the generated markup as well as the initial markup. Do not edit JavaScript or add brittle selectors tied to generated text values.

## Out of scope

- Do not change the workspace/pane architecture, map sizing, canvas rendering, or move the canvas controls.
- Do not alter layer behavior, Civ chips, navigation, shared application layout, data sources, or calculate new metrics.
- Do not reformat unrelated markup or perform broad CSS cleanup.

## Verification and review gate

1. This task is independent of the navigation task. It should be performed separately from the canvas layout task because both may touch the same stylesheet; do not run them concurrently or overwrite the other task's changes.
2. Inspect the working tree and relevant diffs; stop if unrelated or uncommitted changes make the intended edit unsafe.
3. Make only the allowed readout changes.
4. Run static checks available in the repository, including ERB/markup sanity checks if the view is edited. Do not claim browser verification.
5. Verify the CSS selectors cover both initial and JavaScript-generated rows by inspecting the markup-generation code, without editing that JavaScript.
6. Write the task report to `~/scratch/player-planetary-data-readout-report.md` using shell redirection. Include exact diff summary, selectors/components affected, checks run/results, and anything not verified. Keep this report for Tracy's review; remove only temporary files created during the task.
7. Stop for Tracy to inspect the page in a browser, including a narrow viewport, and review the result. Do not commit or push without Tracy's explicit approval (GUARDRAILS Rule 26).

## Acceptance criteria

- [ ] Data-row labels and values use the specified compact typography while remaining legible.
- [ ] Row padding is approximately 4px and section gaps approximately 10px, with any justified deviations reported.
- [ ] Label/value pairs remain aligned without overlap; long values wrap or truncate within the value area.
- [ ] CSS formatting applies to both initial and JavaScript-generated readout rows.
- [ ] Biodiversity and Habitability bars render their intended percentage widths.
- [ ] Workspace structure, map behavior, and data values are unchanged.
- [ ] Only the allowed files are changed; static verification and report file are provided.
- [ ] Browser review is left to Tracy; no browser verification is claimed by Qwen.
