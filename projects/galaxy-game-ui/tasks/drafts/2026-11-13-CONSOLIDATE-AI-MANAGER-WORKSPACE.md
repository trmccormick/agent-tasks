---
status: drafts
priority: normal
type: ui-consolidation
project: galaxy-game-ui
---

# Consolidate the Admin AI Manager Workspace

## Goal

Bring the useful existing AI Manager screens into a coherent, inspectable workspace within the Admin Dashboard. The immediate prototype target is an activity and decision-inspection experience, not a functional AI control system.

## Read first

- AI Manager UI direction: `galaxy-game-ui/docs/wiki/future_work.md`
- UI prototype direction: `galaxy-game-ui/docs/wiki/README.md`
- Admin/AI Manager interface intent: `galaxyGame/docs/wiki/ui/03_admin_and_ai_manager.md`
- AI Manager domain intent: `galaxyGame/docs/wiki/logistics/ai_manager_logic.md`

## Inspect and reuse

- Existing AI Manager views, routes, styles, JavaScript, and mock data in the current UI prototype.
- Main-app starting references under `app/views/admin/ai_manager/` (including its existing subviews and testing views), plus their associated controllers and assets.
- Existing Admin navigation and reusable dashboard components.

Inspect before copying. The early main-app views are references, not a mature interface to reproduce wholesale.

## Work

1. Inventory the existing prototype AI Manager pages and the useful main-app starting views.
2. Consolidate the useful material into a coherent AI Manager workspace under Admin navigation.
3. Prioritize an activity feed/list and a selected activity/decision inspector.
4. Where useful, show objective, status, progress, related assets/location, constraints, rationale, and alternatives—but only when the available source or fixture actually supplies those fields.
5. Use clearly identified mock/representative data for the prototype. Do not fabricate detailed AI reasoning or imply mock records are live.
6. Make operational states and information density easy to scan. Refine typography and spacing; avoid oversized text and crowded/run-together content.
7. Preserve the distinction between observing/supervising the AI Manager and taking control away from it. Do not invent override controls.
8. Record what information the screen needs, what existing source data supplies it, and what remains unavailable. This is future API discovery, not an API contract.

## Acceptance criteria

- [ ] AI Manager is clearly an Admin workspace, not a Player context.
- [ ] Existing useful prototype/main-app views and components are reused where practical.
- [ ] An administrator can scan activity and inspect a selected decision/activity.
- [ ] Displayed rationale and operational fields are grounded in available source or clearly marked mock data.
- [ ] No live AI Manager, simulation, backend, or API is required.
- [ ] Typography, spacing, and information density are readable.
- [ ] The workspace is checked in the running prototype.
- [ ] Report source views reused, exact files changed, verification, and remaining data gaps.

## Out of scope

Building AI decision-making, mission execution, simulation controls, training pipelines, direct decision override, production APIs, or the rest of the Admin tool suite.

Do not modify the main game repository. Do not commit or push without Tracy's explicit approval.
