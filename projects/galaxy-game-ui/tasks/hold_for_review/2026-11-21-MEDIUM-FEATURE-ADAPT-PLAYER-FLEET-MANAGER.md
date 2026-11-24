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

# Adapt the Player Fleet Manager for Distributed Operations

## Objective

Establish the Player Fleet Manager screen in the Rails 8.1 UI prototype by adapting the existing EVE Dashboard Fleet Manager layout and useful JavaScript. Preserve the recognizable fleet-management presentation while representing GalaxyGame fleets as coordinated operations whose craft may be distributed across locations and systems.

This is a presentation task. Reuse and adapt existing code and patterns; do not replace useful work with a wholesale rewrite. Clearly identified mock data and incomplete interactions are acceptable.

## Read first

- `agent-tasks/rules/GUARDRAILS.md`
- `agent-tasks/projects/galaxy-game-ui/README.md`
- `agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md`
- `galaxyGame/docs/wiki/ui/02_player_view_architecture.md` — “Space context”, “Individual craft tracking workspace and route views”, and “Fleet Manager and distributed fleet operations”
- `galaxyGame/docs/wiki/player/space_and_surface_operations.md` — authoritative gameplay distinction between spacecraft-based Space Operations and planetary Surface Operations
- Current Rails 8.1 prototype routes, Player navigation, existing Fleet Manager/EVE Dashboard views and JavaScript, shared layout, CSS, and craft-tracking work.

The main GalaxyGame application is Rails 7; the separate UI prototype is Rails 8.1. Keep the repositories and runtime assumptions distinct. Treat the main app and EVE Dashboard source as read-only references.

## Scope boundary: Space Operations only

This task is specifically the Player interface for spacecraft-based Space Operations. It includes asteroid/small-body harvesting (including EM), atmospheric gas harvesting by spacecraft/skimmers, orbital and asteroid construction/logistics, spacecraft transport, inter-system and in-system travel, and associated rendezvous/resupply.

Planetary Surface Operations are a separate activity model and interface. Surface mining, terrain-based work, surface vehicles/units, settlement/facility surface construction, and surface logistics are out of scope. Shared planning concepts do not make the two contexts one interface. Do not add surface-operation screens or surface-unit management to this Fleet Manager task; the separate Surface Operations design can be addressed independently.

## Intended behavior and presentation

Use the existing EVE Dashboard Fleet Manager view as the primary layout and interaction reference. EVE Dashboard is the friend's independently developed Python application connected to the EVE Online API; it is not EVE Online itself. Reuse its actual UI/JavaScript patterns, not EVE Online gameplay or backend assumptions. Inspect its actual view, scripts, styles, dependencies, and data assumptions before deciding what to reuse. Adapt its labels, content, and behavior to GalaxyGame.

A GalaxyGame fleet is a group of craft assigned to a shared objective or coordinated operation. Fleet members do not need to be physically together, in formation, or in the same system. They may perform different tasks at separate locations while contributing to the same operation.

Use an asteroid-station construction operation as the representative mock-data scenario:
- construction/assembly craft work at the asteroid;
- harvesting craft elsewhere collect electromagnetic material (EM);
- transport craft move harvested EM and supplies to the construction site;
- support craft provide fuel, maintenance, or other support.

The screen should make it possible to understand:
- which fleet/operation is selected and its objective/status/progress;
- which craft participate, their roles/tasks, locations, and operational states;
- how participating craft and work sites are distributed spatially;
- relevant planned movements, deliveries, resource flows, and task dependencies;
- delays, shortages, failures, or other issues needing attention.

Retain the useful EVE-inspired fleet list/selection, selected-fleet overview, participant/status presentation, map visualization, and detail-panel patterns where the source supports them. Do not force a specific pane arrangement if the existing view has a useful adaptable structure. The result should clearly communicate the relationship between the shared operation and distributed craft.

Provide a clear way to move from a participating craft to that craft's Individual Craft Tracking view. Fleet Manager and Individual Craft Tracking are separate screens with different organizing principles: fleet objective versus one spacecraft. Route authoring and saved-route management belong to the separate Player Route Planner; Fleet Manager may select or reference a saved route for an operation but does not become the route-authoring workspace.



### Asynchronous fleet activity planning

Fleet Manager must show that a fleet assignment is a planned operational itinerary, not simply one route followed by all craft together. The player can prepare an ordered plan and send it ahead; craft automation/AI Manager may continue authorized work while the player is away. The plan should support different craft assignments, different in-system routes, layovers and activities, dependencies, resource instructions, expected arrivals, rendezvous points, and planned wormhole departure windows.

Use a representative mock scenario that demonstrates:
- harvesters conducting light EM harvesting at an asteroid belt;
- a transport craft independently visiting a planet to sell materials and purchase specified fleet supplies while harvesting continues;
- another slower craft proceeding toward the next departure wormhole;
- craft returning or converging at a planned rendezvous, with expected arrival/readiness times and a planned next jump;
- the fleet continuing its inter-system wormhole route, with a separate local itinerary for each visited system.

The UI should distinguish the fleet's inter-system wormhole route from each system's in-system waypoints and activities. Craft can branch to different local destinations and later rendezvous; do not depict the fleet as a single marker or assume all members travel together. Show planned/estimated times separately from actual or last-reported status. Where data is delayed, expose last-known status/data age and avoid implying real-time player visibility or instant command delivery.

For this presentation task, activity sequences, market transactions, dependencies, ETAs, rendezvous readiness, and delayed status can be represented with internally consistent mock data and stub interactions. Do not implement the underlying logistics, market, simulation, scheduling, autonomy, communications, or command-execution systems.

## Reuse and adaptation

- Identify the exact EVE Dashboard view and JavaScript modules being reused or adapted; verify their paths and dependencies.
- Preserve useful map, selection, list, status, and panel behavior where practical. Adapt rather than copy EVE-specific assumptions.
- Reuse existing GalaxyGame prototype components and styling where they fit.
- Use mock fleet, craft, task, location, delivery, resource, and progress data where needed. Mark mock values clearly.
- Record source provenance and what was reused, adapted, stubbed, or deferred.

## Scope boundaries

- This is the Player Fleet Manager, not the individual-craft tracking workspace and not Admin/AI Manager oversight.
- Fleet membership represents shared operational purpose, not physical proximity, formation, or a group travelling together.
- Do not implement fleet persistence, backend APIs, real task orchestration, logistics/resource simulation, market transactions, authoritative route finding, route authoring/persistence, navigation simulation, scheduling/ETA calculation, autonomy, communications, or command execution.
- Do not add models, migrations, or production data integration.
- Do not replace existing Player Planetary work or conflate Planetary/Surface/Settlement with Space.
- Do not modify the main GalaxyGame or EVE source repositories.

## Acceptance criteria

- [ ] Fleet Manager is reachable through the existing Player navigation conventions.
- [ ] The screen retains and adapts the useful EVE Dashboard Fleet Manager presentation rather than becoming an unrelated new design.
- [ ] A player can select a fleet/operation and inspect its objective, status, and progress.
- [ ] The selected fleet shows participating craft, their roles/tasks, locations, and operational states.
- [ ] Mock data demonstrates one fleet with craft working at separate locations: asteroid construction, remote EM harvesting, and transport/delivery to the build site.
- [ ] The presentation communicates shared operational purpose without implying physical co-location or formation.
- [ ] Relevant resource flows, deliveries, task dependencies, and attention items are represented where practical.
- [ ] Mock assignments demonstrate craft following different local routes and performing different activities within one fleet operation.
- [ ] Mock activity planning demonstrates a transport craft visiting a planet to sell materials and buy fleet supplies while harvesters continue working.
- [ ] Mock timing demonstrates a craft proceeding toward a departure wormhole while other craft work, then coordinating at a planned rendezvous before the next jump.
- [ ] The view distinguishes the inter-system wormhole route from system-specific in-system waypoints/activities, and shows planned/estimated times separately from last-reported status and data age.
- [ ] A participating craft can be opened in or linked to Individual Craft Tracking, or any missing route is explicitly documented.
- [ ] Existing EVE/prototype code reuse and all mock/stub/deferred behavior are documented.
- [ ] The running prototype is visually inspected, console errors are checked, and screenshot evidence is captured.

## Verification and closeout

Inspect the actual route and screen in the running Rails 8.1 prototype. Verify fleet selection, distributed participant presentation, operation context, and navigation to individual craft tracking to the extent implemented. Capture a screenshot. Record exact files changed, source paths and provenance, mock data, reused/adapted/stubbed behavior, and remaining limitations.

Do not commit or push without independent review and Tracy's explicit authorization. Do not promote or dispatch this draft; it remains subject to the project's normal review and approval process.

## Dependencies

No backend, persistence, simulation, or authoritative route-finding dependency is required. Inspect the existing EVE Dashboard source and current prototype at implementation time. The individual-craft tracking task is a related separate view, not a prerequisite to establishing the Fleet Manager screen; document any cross-link that cannot yet be completed.
