# GalaxyGame UI — Draft Task Queue

**Updated:** 2026-11-13

This folder contains proposed work for the separate galaxy-game-ui visual prototype. These tasks are drafts for Tracy's review; creating them does not authorize dispatch. Tracy selects the agent and dispatches one task at a time.

## Current scope

The immediate work is to consolidate and port existing UI into the Rails 8.1 prototype, clean up presentation/CSS as needed, and add placeholders where a view does not yet exist.

- The main GalaxyGame backend is paused and is not part of this work.
- The prototype is a visual/design prototype; it does not need a running simulation, production API, or live backend integration.
- Reuse existing views, JavaScript, renderers, assets, and useful data wherever practical.
- Imported JSON and clearly identified mock JSON may use the same presentation code. Follow the current UI data wiki and inspect the live prototype before choosing a data location or adding infrastructure.
- Record data needs and gaps as observations for future API discovery; do not define API contracts.
- The main GalaxyGame wiki owns game design intent. This UI wiki records prototype-specific presentation and integration guidance.
- The old backlog is reference material only. Do not revive unrelated tasks or expand scope to fix old backend issues.

## Current page/workspace drafts

| Suggested order | Draft task | Purpose |
|---:|---|---|
| 1 | 2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-PLANETARY-VIEW.md | Port the existing Planetary view and renderer; first slice is Earth |
| 2 | 2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SURFACE-VIEW.md | Port the Surface page and reusable presentation code; asset exploration is paused |
| 3 | 2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SPACE-WORKSPACE.md | Create a bounded Sol Space view, using only the relevant fleet-map interaction pattern |
| 4 | 2026-11-13-MEDIUM-FEATURE-CREATE-PLAYER-SETTLEMENT-PLACEHOLDER.md | Add Settlement without inventing finalized mechanics |
| 5 | 2026-11-13-MEDIUM-FEATURE-CREATE-ADMIN-SHELL-AND-OVERVIEW.md | Create a distinct Admin shell and overview entry point |
| 6 | 2026-11-13-MEDIUM-FEATURE-CREATE-AI-MANAGER-INSPECTION-WORKSPACE.md | Create a focused mission/decision inspection workspace within Admin |

This is a practical starting order, not a rigid schedule. Tracy may choose a different task based on the current prototype state and availability. Do not dispatch several tasks in parallel.

## Surface imagery and tileset status

Terrain/biome/tileset asset exploration is paused and is not part of the current Surface port.

- Trident was an early experiment, not an approved or default tileset.
- Earlier exploration of FreeCiv and other existing free tilesets was abandoned.
- Generated samples and individual image files from earlier experiments are exploratory material, not an approved or complete asset set.
- Do not source, generate, convert, stage or commit new terrain/biome imagery as part of these tasks.
- A later, separate exploration will determine proper image sources and visual direction.

The Surface task should port and adapt the existing tile-based rendering setup as the initial prototype approach where practical, using existing assets and bounded compatibility fixes. This is provisional UI work, not a final terrain/rendering architecture decision. The pause applies to new asset sourcing/generation, replacement-tileset exploration and broad pipeline repair; missing or broken dependencies should be reported rather than triggering that larger work.

## Authoritative design and prototype references

- Main-game Player view intent: galaxyGame/docs/wiki/ui/02_player_view_architecture.md
- Main-game Admin/AI Manager intent: galaxyGame/docs/wiki/ui/03_admin_and_ai_manager.md
- UI prototype direction: galaxy-game-ui/docs/wiki/README.md
- UI JavaScript reuse/porting: galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md
- UI data approach: galaxy-game-ui/docs/wiki/data-integration-and-migration.md
- Current AI Manager screen direction: galaxy-game-ui/docs/wiki/future_work.md
- Prototype-specific session/environment guidance: projects/galaxy-game-ui/SESSION_GUIDANCE.md

The Player interface has four contexts: Planetary, Surface, Space and Settlement. Admin is a separate interface; AI Manager is a workspace within Admin, not a fifth Player context.

## How to use these drafts

1. Review the next proposed task against the current wiki and prototype.
2. Obtain an independent review and Tracy's approval before moving a draft to backlog.
3. Dispatch only the approved task selected by Tracy.
4. Review the result before dispatching the next task.
5. Keep tasks focused on one page or bounded workspace.
6. Update the wiki only when a genuine design decision or clarification is needed; do not duplicate wiki material in task files.

## Older draft tasks

Older tasks are not automatically part of this queue. Review them only if they become relevant to a current work area. Do not delete or revive them as part of this reset without Tracy's direction.

## Task quality

Each task should state one bounded objective, authoritative wiki references, source code/assets to inspect and reuse, the mock-data boundary, exclusions, observable acceptance criteria and basic verification.

Keep task files focused. Do not add new data frameworks, duplicate design specifications, or extra reports/follow-on tasks unless genuinely required. Shared startup, synthesis, review and lifecycle requirements remain governed by the task template and rules/GUARDRAILS.md.

No task is approved or dispatched merely because it appears in this folder.
