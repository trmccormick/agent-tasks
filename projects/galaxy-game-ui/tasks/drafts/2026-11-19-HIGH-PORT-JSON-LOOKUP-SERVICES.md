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

Have Rails load the prototype's local copy of StarSim's minimal Sol JSON sample, resolve the requested body (initially Earth), and return the values the Planetary UI needs to display. The browser must never load or parse the source JSON file itself.

The loader and its Rails-to-frontend payload boundary must also be designed with the reviewed JavaScript reuse in mind: Rails supplies the structured, source-resolved data those interactions need; JavaScript consumes that payload and handles presentation and transient interaction. Do not make the browser compensate for missing backend data by reading source files or reconstructing authoritative relationships.

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Main-app lookup reference: `app/services/lookup/star_system_lookup_service.rb` and `GalaxyGame::Paths`.
- Prototype integration point: `app/controllers/player_controller.rb`.
- UI prototype JavaScript reuse review: `docs/wiki/javascript-reuse-and-porting-plan.md` and `docs/wiki/interface-pattern-references.md`.
- Reviewed EVE reference files: `fleet_map.js`, `fleet_builder.js`, `fleet.js`, and `chain.js` (interaction patterns only; do not preserve EVE data assumptions or endpoints).

## Scope

- Copy the actual minimal `sol.json` sample used by the main application into `galaxy-game-ui/app/data/star_systems/sol.json`. Confirm the source through `GalaxyGame::Paths::STAR_SYSTEMS_PATH`; do not use `sol-complete.json` or create a replacement fixture.
- Implement only the lookup needed to load this Sol sample and find the requested body by name/identifier (initially Earth) in its nested celestial-body collection.
- Have the existing Rails controller/service read the local JSON source, request Earth, and select/return the values required by the Planetary UI in a focused display payload.
- Define the payload as a consumer-facing contract, not a copy of the source JSON schema. Include stable identifiers and only the requested display fields; keep source paths, file layout, and nested-source traversal inside Rails.
- Review the planned JavaScript consumers when defining the contract. The system-map adaptation may need stable system IDs, names, map coordinates, hierarchy/grouping labels (such as constellation/region where GalaxyGame data actually defines them), and actual system-connection data. Rails must resolve and supply these values and relationships; JavaScript may render the map, group/filter/select systems, pan/zoom, and display supplied routes, but must not derive fictional connections or import EVE stargate/pathfinding semantics.
- The craft-selection/operations adaptation may later need craft identifiers/names, current system or location, destination/assigned objective, operational state, communication/data freshness, and optional task/operational-group membership. These are not assumed to exist in the Sol planetary JSON; obtain them from the appropriate Rails models/services when that later feature is implemented. Do not fabricate craft records or expand this Earth lookup into a craft-management implementation.
- Deliver the payload through Rails-rendered page data or a Rails endpoint. The frontend consumes the prepared payload only; it must not fetch the JSON file, know its path, or perform source-structure lookup.
- Keep the controller-to-frontend payload independent of the local JSON source so the lookup can later be replaced by an API request without changing the UI contract.

## Data requirements from the JavaScript review

These requirements guide the Rails payload design and future extensions; they do not mean every dataset belongs in this first Earth lookup.

- **Planetary view:** requested body's stable identity and the supported planetary values/layer data needed by its existing renderer. Return only fields actually present in the source or supplied by an explicitly identified source; do not invent terrain or live simulation values.
- **System map (`fleet_map.js` patterns):** systems with stable IDs, names and usable map coordinates; any real parent grouping metadata available in GalaxyGame; and actual connection/route relationships supplied by Rails. EVE's `sys`, `con`, `reg`, and `jmp` shapes are not GalaxyGame's schema and must not be copied as assumptions. Natural wormholes and other GalaxyGame connections must retain their actual meaning.
- **Craft operations (`fleet_builder.js` patterns):** craft identity, current location, destination/objective, operational status and optional operational grouping when the relevant Rails data source is available. Craft are autonomous assets, not EVE characters or pilots, and permanent fleets are not assumed.
- **Other reviewed files:** `fleet.js` waypoint submission and `chain.js` wormhole/graph interactions are references for selective UI behavior only. Their EVE/Pixel Nexus endpoints, schemas, and backend assumptions are not part of this loader contract.

Before implementation, reconcile the current Earth-only first-slice scope with the previously discussed four-system map-data experiment. If the four-system experiment remains part of this loader task, explicitly add its actual source fields and acceptance criteria; otherwise keep it as a later consumer of the same Rails-owned lookup/payload boundary. Do not silently claim that an Earth-only payload satisfies the map's system/connection requirements.

## Boundaries

- This is a data lookup for display only. Do not run StarSim, build the Sol system, run a simulation, or load the complete Sol dataset into the UI.
- Do not create a generic provider framework, hand-authored Earth fixture, or broad lookup-service port.
- Do not pass the entire source JSON to the frontend; the controller returns only the values the UI needs.
- Do not add browser fetches to local JSON files, JavaScript file readers, or frontend lookup services. File access, source selection, record resolution, and authoritative relationship lookup belong in Rails.
- Do not port EVE data schemas, character/pilot/fleet rules, EVE endpoints, EVE stargate adjacency/pathfinding, or EVE security coloring.
- No frontend layout/renderer redesign, unrelated models, migrations, persistence, terrain loading, or simulation logic.
- Do not invent values for fields absent from the sample. Terrain and live simulation state are separate sources.

## Acceptance criteria

- [ ] The actual minimal `sol.json` sample is copied unchanged to the prototype's `app/data/star_systems/sol.json` path and tracked by Git.
- [ ] The prototype lookup can resolve the requested Earth entry from the nested Sol data; it does not need to expose or load the full system into the UI.
- [ ] The Rails controller selects and returns the Earth values required by the Planetary UI in a focused, documented payload.
- [ ] The payload contract records which planned JavaScript consumer needs each field and distinguishes data available from this JSON source from data requiring another Rails source.
- [ ] The four-system map experiment's inclusion in this task is explicitly resolved before implementation; if included, acceptance criteria must cover real system IDs, names, coordinates, grouping metadata and actual connections supplied by Rails. If deferred, it remains clearly outside this task's Earth-only implementation.
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