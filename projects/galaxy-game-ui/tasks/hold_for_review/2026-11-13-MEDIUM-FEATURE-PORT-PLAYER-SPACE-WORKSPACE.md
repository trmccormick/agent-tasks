---
status: draft
resume: false
priority: MEDIUM
type: feature
system_domain: PLAYER SPACE
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-21
---

# Adapt the Player Space Individual Craft Tracking Workspace

## Objective

Establish the Player Space individual-craft tracking screen in the Rails 8.1 prototype by adapting useful existing EVE Dashboard layout and JavaScript behavior. The workspace must show one selected craft at a time, two simultaneously visible vertically stacked route maps, and the selected craft's status/details and planned route.

This is a presentation/prototype task. Existing JavaScript may be incomplete or require later refinement; preserve and adapt useful code rather than rewriting it wholesale.

## Read first

- `agent-tasks/rules/GUARDRAILS.md`
- `agent-tasks/projects/galaxy-game-ui/README.md`
- `agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md`
- `galaxyGame/docs/wiki/ui/02_player_view_architecture.md` — especially “Space context” and “Individual craft tracking workspace and route views”
- Current `galaxy-game-ui` routes, Player navigation, Space-related views, JavaScript, CSS, and existing dashboard patterns.

The main GalaxyGame application is Rails 7. The separate prototype is Rails 8.1. Keep the repositories and runtime assumptions distinct. The main app and EVE Dashboard source are read-only references.

Route creation and saved-route management are handled by the separate Player Route Planner. This workspace displays the route assigned to the selected craft; it is not the route-authoring interface.

## Intended full-screen structure

Retain the existing persistent GalaxyGame main-game navigation at the far left. Do not replace it with the craft list or duplicate it inside Space. The full screen therefore has four vertical areas: (1) persistent application navigation, followed by the three-pane Space workspace: (2) craft selection, (3) center maps, and (4) selected-craft details. The three workspace panes sit to the right of the persistent navigation.

Use the existing EVE Fleet Manager screenshot as a layout/interaction reference, not as a fleet-management specification.

- **Left workspace pane — craft selection:** selectable list of individual player spacecraft. Do not show two fleets or make fleet grouping the primary selection model.
- **Center workspace pane — two stacked maps, both visible simultaneously:**
  1. **Wormhole Network:** inter-system wormhole topology and the selected craft's planned route between systems, including system sequence and wormhole transitions.
  2. **In-System Route:** navigation within the relevant solar system, showing the selected craft, destination, intermediate stops/waypoints and local planned route.
- **Right workspace pane — selected craft details:** craft identity, operational status, current position, destination, assignment/objective, condition, relevant fuel/resources, cargo, and communication/data freshness where supported. Include the planned route as an ordered list of stops/transitions as well as map lines/markers.

Both maps and the right pane must reflect the same selected craft. Changing craft selection should update the three areas coherently. The maps are independent visualizations stacked within the center workspace pane, with their own map interactions; they are not full-height columns, tabs, or mutually exclusive options.

## Reuse and adaptation

- Inspect the existing EVE Dashboard JavaScript and identify the actual modules that implement useful craft selection, map rendering, pan/zoom, marker selection, route display, and selected-object details. Verify source paths and dependencies before adapting.
- Reuse/adapt the existing EVE code where it provides a useful starting point. Do not assume the EVE Fleet Manager already contains both GalaxyGame map scales; adapt suitable network/map behavior for the Wormhole Network and suitable local map behavior for the In-System Route.
- Inspect and reuse the existing GalaxyGame `system_renderer.js` baseline for the in-system view where useful.
- Preserve useful existing prototype JavaScript, styles, views, and layout. Adapt selectors, data shape, and presentation to GalaxyGame CSS and terminology. Do not rewrite whole modules simply because some behavior is incomplete.
- Use clearly labeled mock data where necessary. The two maps, route display, craft selection and right-side details need to be present for review even if some interactions or data connections are stubbed.
- Record source provenance and which behavior was reused, adapted, stubbed, or deferred.

## Scope boundaries

- This is an individual-craft workspace, not Fleet Management or the Route Planner. Fleet creation, fleet organization and multi-craft coordination are separate features; creating and saving preferred routes belongs to the Route Planner.
- GalaxyGame craft are autonomous assets. Do not introduce EVE pilots, characters, fleet commanders, combat, EVE terminology, or EVE backend/game rules.
- Do not implement route authoring, saved-route management, authoritative route finding, shortest-path logic, navigation simulation, command execution, production API integration, models, migrations, or persistence.
- Do not replace existing Player Planetary work or conflate Player Planetary/Surface/Settlement views with Space.
- Do not make a complete, working data loader or backend prerequisite for getting the view and layout in place.
- Do not modify the main GalaxyGame or EVE source repositories.

## Acceptance criteria

- [ ] Space is reachable through the existing Player navigation conventions.
- [ ] The workspace selects and displays one individual craft at a time; it is not laid out as two fleets or a fleet roster.
- [ ] The left craft list, both maps, and right-side details all reflect the same selected craft.
- [ ] Wormhole Network and In-System Route maps are displayed vertically stacked and simultaneously visible, not as tabs, toggles, or alternate views.
- [ ] The Wormhole Network map represents inter-system connections and the craft's planned inter-system route.
- [ ] The In-System Route map represents navigation within a solar system and the craft's planned local route.
- [ ] The selected craft's status/details and ordered planned route are visible in the right pane.
- [ ] Useful existing EVE Dashboard JavaScript and GalaxyGame renderer code are reused/adapted where appropriate; provenance and deferred behavior are recorded.
- [ ] Clearly labeled mock data or stubs are acceptable; no backend or simulation implementation is introduced.
- [ ] The running prototype is visually inspected and evidence is captured. Report incomplete interactions without expanding scope to fix unrelated functionality.

## Verification and closeout

Inspect the actual route and page in the running Rails 8.1 prototype. Verify layout, both stacked maps, craft selection behavior to the extent implemented, synchronized selected-craft information, and browser console errors. Capture a screenshot. Record exact files changed, EVE/GalaxyGame source paths and provenance, mock data, reused/adapted/stubbed behavior, and remaining limitations.

Do not commit or push without independent review and Tracy's explicit authorization. Do not promote or dispatch this draft; it remains subject to the project's normal review and approval process.

## Dependencies

No backend, JSON loader, or simulation dependency is required to establish the screen. Reuse and inspect available source code at implementation time. If a source module is unavailable, document that and use the closest existing GalaxyGame/prototype baseline rather than inventing an implementation.

