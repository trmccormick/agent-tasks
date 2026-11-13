---
status: drafts
priority: normal
type: ui-consolidation
project: galaxy-game-ui
---

# Consolidate the Admin Dashboard Overview

## Goal

Consolidate the existing Admin Dashboard overview in the GalaxyGame UI prototype so it serves as a clear entry point to administrative workspaces. Reuse existing prototype screens and components; do not build a complete administration system.

## Read first

- UI prototype direction and interface responsibilities: `galaxy-game-ui/docs/wiki/README.md`
- UI workflow guidance: `galaxy-game-ui/docs/wiki/interface-and-workflows.md`
- Admin/AI Manager screen concepts: `galaxy-game-ui/docs/wiki/screen-concepts-and-wireframes.md`
- Admin and AI Manager domain intent: `galaxyGame/docs/wiki/ui/03_admin_and_ai_manager.md`

## Inspect

- Existing Admin Dashboard routes, controllers, views, layout, navigation, styles, and mock data in the current prototype.
- Existing main-app Admin Dashboard views/components where they offer useful presentation or workflow patterns.
- Existing screenshot/reference evidence in the UI wiki.

Do not assume old task files or old status documents describe the current prototype accurately. Inspect the active branch first.

## Work

1. Identify the current Admin Dashboard overview and reusable administrative components.
2. Consolidate the overview into a readable landing page for Admin workspaces, preserving useful existing content and interactions.
3. Make the distinction between Admin and Player interfaces clear.
4. Provide clear entry points to existing Admin areas, including AI Manager where already represented.
5. Use representative mock data and label it as mock. Do not imply that status indicators or actions are connected to live services.
6. Refine typography, spacing, density, and panel hierarchy in line with the current dark charcoal/navy + cyan visual direction.
7. Keep navigation shallow and task-oriented; do not turn the game-domain hierarchy into a deeply nested sidebar.

If the current overview is already adequate, make only the small adjustments needed and report what was retained rather than redesigning it.

## Acceptance criteria

- [ ] Admin Dashboard has a clear, distinct entry point in the prototype.
- [ ] Existing useful views/components are reused.
- [ ] Admin and Player navigation/workflows are visibly distinct.
- [ ] Workspace links lead to existing prototype destinations or clearly marked placeholders.
- [ ] Mock indicators/data are not represented as live operational state.
- [ ] Typography and information density are readable and consistent.
- [ ] The page is checked in the running prototype.
- [ ] Report exact files reused/changed and any remaining missing destinations.

## Out of scope

Implementing the individual Admin tools, AI Manager internals, Map Studio, simulation controls, production services/APIs, permissions, or backend functionality.

Do not modify the main game repository. Do not commit or push without Tracy's explicit approval.
