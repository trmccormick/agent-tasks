# GalaxyGame UI — Draft Task Queue

**Updated:** 2026-11-13

This folder contains proposed work for the separate `galaxy-game-ui` visual prototype. These tasks are drafts for Tracy's review; creating them does not authorize dispatch. Qwen should receive **one task at a time**, in the order Tracy chooses.

## Current scope

The immediate work is to consolidate and port existing UI into the Rails 8.1 prototype, clean up presentation/CSS as needed, and add placeholders where a view does not yet exist.

- The main GalaxyGame backend is paused and is not part of this work.
- The prototype does not need a running simulation, production API, or live backend integration.
- Reuse existing views, JavaScript, renderers, assets, and useful data wherever practical.
- Imported JSON and clearly identified mock JSON may use the same presentation code.
- Record data needs and gaps as observations for future API discovery; do not define API contracts.
- The main GalaxyGame wiki owns game design intent. This UI wiki records prototype-specific presentation and integration guidance.
- The old backlog is reference material only. Do not revive unrelated tasks or expand scope to fix old backend issues.

## Current page/workspace drafts

| Suggested order | Draft task | Purpose |
|---:|---|---|
| 1 | `2026-11-13-PORT-PLAYER-PLANETARY-VIEW.md` | Port the existing Planetary view and renderer |
| 2 | `2026-11-13-PORT-PLAYER-SURFACE-VIEW.md` | Port the existing Surface view, renderers, and assets |
| 3 | `2026-11-13-PORT-PLAYER-SPACE-WORKSPACE.md` | Consolidate GalaxyGame Space work and useful EVE Dashboard modules |
| 4 | `2026-11-13-CREATE-PLAYER-SETTLEMENT-PLACEHOLDER.md` | Add the new Settlement context without inventing finalized mechanics |
| 5 | `2026-11-13-CONSOLIDATE-ADMIN-DASHBOARD-OVERVIEW.md` | Clean up the Admin entry point and workspace navigation |
| 6 | `2026-11-13-CONSOLIDATE-AI-MANAGER-WORKSPACE.md` | Consolidate useful AI Manager screens into an inspectable Admin workspace |

This is a practical starting order, not a rigid schedule. Tracy may choose a different task based on the current prototype state and evening availability. Do not dispatch several tasks in parallel.

## Authoritative design and prototype references

- Main-game Player view intent: `galaxyGame/docs/wiki/ui/02_player_view_architecture.md`
- Main-game Admin/AI Manager intent: `galaxyGame/docs/wiki/ui/03_admin_and_ai_manager.md`
- UI prototype direction: `galaxy-game-ui/docs/wiki/README.md`
- UI JavaScript reuse/porting: `galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md`
- UI data approach: `galaxy-game-ui/docs/wiki/data-integration-and-migration.md`
- Current AI Manager screen direction: `galaxy-game-ui/docs/wiki/future_work.md`
- Prototype-specific session/environment guidance: `projects/galaxy-game-ui/SESSION_GUIDANCE.md`

The Player interface has exactly four contexts: **Planetary, Surface, Space, and Settlement**. Admin is a separate interface; AI Manager is a workspace within Admin, not a fifth Player context.

## How to use these drafts

1. Review the next proposed task and adjust it if the current wiki or prototype has changed.
2. Dispatch only the approved task to Qwen.
3. Review the result before dispatching the next task.
4. Keep tasks focused on one page or bounded workspace.
5. Update the wiki only when a genuine design decision or clarification is needed; do not duplicate wiki material in task files.

## Older draft tasks

Older tasks in this folder were written for earlier prototype directions and are not automatically part of this queue. In particular, the November 4 waypoint/navigation task and earlier dashboard foundation tasks should be reviewed against the current wiki and prototype before anyone dispatches them. Do not delete or revive them as part of this reset without Tracy's direction.

## Task quality

Each current task should state:
- one page or bounded workspace to port/consolidate;
- the authoritative wiki references;
- existing source code/assets to inspect and reuse;
- the representative/mock-data boundary;
- what is explicitly out of scope;
- observable acceptance criteria and basic verification.

Keep task files short enough to execute. Do not add duplicate startup procedures, elaborate synthesis gates, or process checklists already covered by the shared guidance. Do not create extra reports or follow-on tasks unless the work genuinely requires them.

No task is dispatched or approved merely because it appears in this folder.
