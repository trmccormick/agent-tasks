---
status: draft
resume: false
priority: HIGH
type: feature
system_domain: DATA LOOKUP / SERVICES
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-19
---

# Load Sol JSON and return requested planetary data

## Objective

Have Rails load the prototype's local copy of StarSim's minimal Sol JSON sample, resolve the requested body (initially Earth), and return the data required by the current integration consumer. The browser must never load or parse the source JSON file itself.

Rails owns source-file access, source selection, record resolution and preparation of display payloads. JavaScript consumes data provided by Rails and handles rendering and transient interaction. Do not make the browser compensate for missing backend data by reading source files or reconstructing authoritative relationships.

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Main-app lookup reference: `app/services/lookup/star_system_lookup_service.rb` and `GalaxyGame::Paths`.
- Prototype integration point: `app/controllers/player_controller.rb`.
- UI prototype JavaScript reuse review: `docs/wiki/javascript-reuse-and-porting-plan.md` and `docs/wiki/interface-pattern-references.md`.
- Reviewed EVE reference files: `fleet_map.js`, `fleet_builder.js`, `fleet.js`, and `chain.js` (interaction patterns only; do not preserve EVE data assumptions or endpoints).

## Scope

- Copy the actual minimal `sol.json` sample used by the main application into `galaxy-game-ui/app/data/star_systems/sol.json`. Confirm the source through `GalaxyGame::Paths::STAR_SYSTEMS_PATH`; do not use `sol-complete.json` or create a replacement fixture.
- Implement only the lookup needed to load this Sol sample and find the requested body by name/identifier (initially Earth) in its nested celestial-body collection.
- Have the existing Rails controller/service read the local JSON source, request Earth, and select/return a focused payload for the current Planetary integration.
- Define the payload as a consumer-facing contract, not a copy of the source JSON schema. Include stable identifiers and the fields actually required by the integration; keep source paths, file layout, and nested-source traversal inside Rails.
- Keep the payload independent of the local JSON source so the lookup can later be replaced by an API request without changing the frontend contract.
- Use the real JavaScript integration work to discover and document additional data requirements. It is acceptable to mount reviewed EVE dashboard JavaScript in a GalaxyGame view and wire it to a Rails-provided payload even if some features initially do not work because expected GalaxyGame data or backend services are not yet available. Record those gaps explicitly rather than fabricating data, silently importing EVE assumptions, or expanding this loader into unrelated systems.
- The system-map adaptation may eventually need stable system IDs, names, map coordinates, genuine hierarchy/grouping metadata and actual connection/route data. Rails must resolve and supply these from GalaxyGame sources; JavaScript may render the map and handle pan/zoom, selection, filtering and grouping UI. Do not assume EVE's `sys`, `con`, `reg`, or `jmp` schema matches GalaxyGame.
- The craft-selection/operations adaptation may eventually need craft identifiers/names, current location, destination/assigned objective, operational state, communication/data freshness and optional task/operational-group membership. These are not assumed to exist in the Sol planetary JSON; obtain them from the appropriate Rails sources when that view is integrated. Craft are autonomous assets, not EVE characters or pilots, and permanent fleets are not assumed.
- `fleet.js` waypoint submission and `chain.js` wormhole/graph interactions are references for selective UI behavior only. Their EVE/Pixel Nexus endpoints, schemas and backend assumptions are not part of this loader contract.

## Planetary renderer clarification

The intended Planetary renderer is existing GalaxyGame application code, not the current prototype's adapted Planetary view. The prototype view may be sufficient as a temporary consumer for this initial data-loading slice, but it is not authoritative evidence of the original renderer's intent or complete data requirements.

Do not infer or redefine the original Planetary renderer's behavior from the adapted prototype. When the original GalaxyGame renderer is brought into the prototype, inspect that code and identify its actual data dependencies. For this task, provide the focused Earth data needed for the agreed initial integration and record any requirements that cannot yet be met.

## Four-system map scope

The previously discussed four-system map-data experiment is a separate consumer requirement from the initial Earth lookup. Before implementation, confirm whether it is being added to this loader task or remains a later extension. If included, specify its actual source fields and acceptance criteria; if deferred, do not imply that an Earth-only payload supports the system map. Either way, the same Rails-owned data boundary applies.

## Boundaries

- This is a data lookup for display only. Do not run StarSim, build the Sol system, run a simulation, or load the complete Sol dataset into the UI.
- Do not create a generic provider framework, hand-authored Earth fixture, or broad lookup-service port.
- Do not pass the entire source JSON to the frontend; the controller returns only the values the current consumer needs.
- Do not add browser fetches to local JSON files, JavaScript file readers, or frontend lookup services. File access, source selection, record resolution and authoritative relationship lookup belong in Rails.
- Do not port EVE data schemas, character/pilot/fleet rules, EVE endpoints, EVE stargate adjacency/pathfinding, or EVE security coloring.
- No frontend layout/renderer redesign, unrelated models, migrations, persistence, terrain loading, or simulation logic.
- Do not invent values for fields absent from the sample. Terrain and live simulation state are separate sources.

## Acceptance criteria

- [ ] The actual minimal `sol.json` sample is copied unchanged to the prototype's `app/data/star_systems/sol.json` path and tracked by Git.
- [ ] The prototype lookup can resolve the requested Earth entry from the nested Sol data.
- [ ] Rails selects and returns the Earth data required by the agreed initial integration in a focused, documented payload.
- [ ] The payload contract identifies its current consumer and distinguishes data available from this JSON source from requirements needing another source/service.
- [ ] The current adapted Planetary view is treated as a temporary consumer only, not as the specification for the original GalaxyGame Planetary renderer.
- [ ] Any missing data/services discovered while wiring reviewed JavaScript into a view are recorded as explicit gaps; no fake authoritative values or direct browser JSON reads are introduced.
- [ ] The four-system map experiment's inclusion in this task is explicitly resolved before implementation. If included, acceptance criteria cover real system IDs, names, coordinates, grouping metadata and actual connections supplied by Rails; if deferred, it remains outside this Earth-only implementation.
- [ ] Tests cover successful Earth lookup and missing or malformed source/body data.
- [ ] The frontend receives the display payload from Rails without knowing the JSON file path, source structure, or lookup implementation.
- [ ] Browser network/source inspection confirms no direct request for the source JSON file; any data request is to a Rails route/controller.
- [ ] The payload boundary is suitable for replacing local lookup with a future API request without changing the frontend contract.
- [ ] Relevant Rails checks and focused tests pass.

## Dependencies

**Blocked by:** none  
**Blocks:** `2026-11-19-MEDIUM-FEATURE-USE-LOADED-DATA-PLAYER-PLANETARY-VIEW.md`

## Dispatch note

This is the next task in the agreed order. It remains a draft until Tracy explicitly approves dispatch.
