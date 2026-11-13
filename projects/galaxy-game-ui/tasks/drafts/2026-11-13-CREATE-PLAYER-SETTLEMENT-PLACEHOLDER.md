---
status: drafts
priority: normal
type: ui-prototype
project: galaxy-game-ui
---

# Create the Player Settlement View Placeholder

## Goal

Add a coherent visual placeholder for the Player Settlement context in the GalaxyGame UI prototype. Unlike Planetary, Surface, and Space, Settlement is a new view whose detailed design is still exploratory. The task is to establish a useful screen structure without inventing finalized gameplay.

## Read first

- Main game design authority: `galaxyGame/docs/wiki/ui/02_player_view_architecture.md`
- Settlement infrastructure context: `galaxyGame/docs/wiki/infrastructure/settlement_infrastructure.md`
- UI prototype direction: `galaxy-game-ui/docs/wiki/README.md`
- UI screen concepts: `galaxy-game-ui/docs/wiki/screen-concepts-and-wireframes.md`

## Work

1. Inspect the current prototype's Player navigation, page layout, reusable panels, and styling.
2. Add a Settlement destination in the Player context, using the existing navigation conventions.
3. Create a SimCity-like management workspace at a high level: a settlement overview/visual area, selected facility or district details, and compact summaries for relevant settlement conditions.
4. Use clearly identified sample/mock data only. Keep the page demonstrative; no game mechanics or backend operations are required.
5. Preserve the distinctions between a physical Settlement, a Colony (the governing entity), and player/corporation ownership.
6. Do not present early ideas about leasing NPC settlement connections, outside construction, service capacity, utility supply, or selling surplus power as established mechanics. If represented visually, label them as conceptual/placeholder only.
7. Keep the layout consistent with the prototype's dark charcoal/navy + cyan direction and existing typography/density. Reuse existing components rather than introducing a new design system.

The exact screen layout is open to visual judgment within these boundaries. Do not turn this task into a detailed gameplay design exercise.

## Acceptance criteria

- [ ] Settlement is reachable as one of the four Player contexts.
- [ ] The placeholder communicates the purpose of a settlement-management workspace.
- [ ] Sample data is clearly marked as mock/conceptual.
- [ ] Settlement, Colony, and ownership are not conflated.
- [ ] Exploratory mechanics are not presented as approved or implemented.
- [ ] The page uses existing prototype components and visual conventions.
- [ ] The result is checked in the running prototype at desktop size and obvious layout issues are reported.
- [ ] Report exact files changed and any design questions that need Tracy's decision.

## Out of scope

Planetary, Surface, Space, finalized Settlement mechanics, backend/API integration, simulation behavior, detailed construction/economy rules, and broad navigation redesign.

Do not modify the main game repository. Do not commit or push without Tracy's explicit approval.
