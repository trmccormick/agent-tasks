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

# Adapt the EVE Chain Map into the Player Route Planner

## Objective

Port/adapt the existing EVE Dashboard Chain Map view and useful JavaScript into the Rails 8.1 GalaxyGame UI prototype as the initial Player Route Planner. Preserve the useful node-and-connection workspace, map interactions, route presentation, and contextual detail layout, then adapt its terminology, data assumptions, and purpose to GalaxyGame.

The Route Planner is a distinct Player Space capability for creating, saving, and reusing preferred routes at both inter-system/wormhole and in-system scales. This is an initial UI adaptation, not implementation of GalaxyGame's authoritative navigation or route-finding systems.

## Read first

- `agent-tasks/rules/GUARDRAILS.md`
- `agent-tasks/projects/galaxy-game-ui/README.md`
- `agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md`
- `galaxyGame/docs/wiki/ui/02_player_view_architecture.md` — “Space context”, “Individual craft tracking workspace and route views”, “Fleet Manager and distributed fleet operations”, and “Player Route Planner”
- Current Rails 8.1 prototype routes, Player navigation, existing Chain Map source/view/JavaScript/styles, map components, and any route-related code.

The main GalaxyGame application is Rails 7; the separate UI prototype is Rails 8.1. Keep repositories and runtime assumptions distinct. Treat the main app and EVE Dashboard source as read-only references.

## Intended presentation and prototype behavior

Use the supplied EVE Dashboard Chain Map screenshot and the actual existing Chain Map implementation as the primary visual and interaction reference. Inspect the source before deciding what to port. Adapt its node-and-connection canvas, pan/zoom, node and connection selection, route highlighting/editing patterns, toolbar, and contextual detail panel where the source supports them.

The Route Planner must represent two related routing scales:
- **Inter-system / wormhole routing:** systems and their available/potential connections, with a route expressed as an ordered sequence of systems and wormhole transitions.
- **In-system routing:** locations within one solar system, with ordered waypoints between relevant destinations such as orbital infrastructure, celestial bodies, asteroids, settlements, and harvesting sites.

A planned journey may include an inter-system leg and then an in-system leg to its final destination. The UI should make the transition between scales understandable. Do not assume the EVE Chain Map source already supports both scales; determine what it actually does and document reusable/adaptable portions. Use existing GalaxyGame rendering/components where they provide a better basis for in-system spatial context, without abandoning the Chain Map visual direction.

The player should be able to work with preferred routes: create or edit a route, give it a name, save it in the prototype's mock/client-side presentation state, and select a previously saved route. Show the ordered route/waypoints and relevant connection transitions. Provide a conceptual assignment path for assigning a saved route to an individual craft or using it in a fleet operation. Show that the route can provide inter-system legs and system-specific waypoint paths, while craft-specific activities, market stops, logistics, timing, and rendezvous are planned separately in Fleet Manager. If actual assignment interaction is not practical in this presentation pass, provide a clear mock/stub interaction and document the limitation.

Use clearly identified mock systems, connections, wormholes, in-system locations, routes, saved-route entries, and assignment targets as needed. Keep examples internally consistent across both routing scales. The purpose is to establish the route-planning screen and let its presentation be reviewed, not to build a complete navigation simulator.



A saved route is a navigation plan, not a complete fleet work schedule. Fleet Manager combines selected route segments with craft-specific activities, local layovers, logistics, dependencies, timing, and rendezvous into an operational assignment. A route may include the inter-system wormhole sequence and associated system-specific waypoint paths, but the actual fleet itinerary can branch: different craft may take different local routes and later rendezvous. Do not expand the Route Planner task into market, harvesting, scheduling, or fleet activity orchestration; those belong to Fleet Manager.

## Scope boundaries

- This is the Player Route Planner, not Fleet Manager and not Individual Craft Tracking. Fleet Manager coordinates distributed operations and may consume saved routes; Craft Tracking displays one craft's assigned route and progress.
- Do not implement authoritative route finding, shortest/safest path algorithms, route feasibility, live wormhole availability, navigation simulation, fuel calculations, travel-time simulation, communications behavior, command execution, backend APIs, database persistence, models, or migrations.
- Do not imply that a saved preferred route guarantees availability or overrides current conditions or craft capabilities.
- Do not carry over EVE pilots, characters, kill intelligence, EVE security mechanics, manually maintained wormhole assumptions, or EVE backend/game rules.
- Do not modify the main GalaxyGame or EVE source repositories.
- Do not replace existing Player Planetary work or alter the separate Fleet Manager and Craft Tracking layouts as part of this task.

## Acceptance criteria

- [ ] A Player Route Planner screen is reachable through the existing Player navigation conventions.
- [ ] The screen visibly adapts the EVE Chain Map presentation and useful existing JavaScript rather than becoming an unrelated new design.
- [ ] Source inspection identifies the exact Chain Map view/scripts/styles and their dependencies; reuse/adaptation and any unavailable source are documented.
- [ ] The interface represents inter-system/wormhole routes and in-system routes, and communicates the distinction between them.
- [ ] A route can be represented as an ordered sequence of systems/connections and/or in-system locations/waypoints.
- [ ] The UI demonstrates a journey combining an inter-system leg with an in-system destination leg.
- [ ] The player can create/edit and name a preferred route, save it in prototype state, and select a saved route, or any stubbed behavior is clearly identified.
- [ ] The UI demonstrates how a saved route could be assigned to an individual craft or used by a fleet operation; authoritative execution is not required.
- [ ] Mock data, reused/adapted/stubbed behavior, and deferred functionality are clearly documented.
- [ ] The running prototype is visually inspected, browser console errors are checked, and screenshot evidence is captured.

## Verification and closeout

Inspect the actual route and page in the running Rails 8.1 prototype. Verify screen navigation, map interactions available in the port, route selection/editing presentation, saved-route selection, and the conceptual assignment path to craft/fleet targets. Capture a screenshot. Record exact files changed, EVE source paths and dependencies, GalaxyGame components reused, mock data, stubbed interactions, and remaining limitations.

Do not commit or push without independent review and Tracy's explicit authorization. Do not promote or dispatch this draft; it remains subject to the project's normal review and approval process.

## Dependencies

No backend, persistence, authoritative route finding, or simulation dependency is required to establish this screen. Inspect the actual Chain Map source and current prototype at implementation time. Fleet Manager and Individual Craft Tracking are related consumers of saved routes, not prerequisites to creating the Route Planner screen; document any cross-link that cannot yet be completed.
