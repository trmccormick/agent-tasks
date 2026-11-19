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

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Main-app lookup reference: `app/services/lookup/star_system_lookup_service.rb` and `GalaxyGame::Paths`.
- Prototype integration point: `app/controllers/player_controller.rb`.

## Scope

- Copy the actual minimal `sol.json` sample used by the main application into `galaxy-game-ui/app/data/star_systems/sol.json`. Confirm the source through `GalaxyGame::Paths::STAR_SYSTEMS_PATH`; do not use `sol-complete.json` or create a replacement fixture.
- Implement only the lookup needed to load this Sol sample and find the requested body by name/identifier (initially Earth) in its nested celestial-body collection.
- Have the existing Rails controller/service read the local JSON source, request Earth, and select/return the values required by the Planetary UI in a focused display payload.
- Deliver that payload through Rails-rendered page data or a Rails endpoint. The frontend consumes the prepared payload only; it must not fetch the JSON file, know its path, or perform source-structure lookup.
- Keep the controller-to-frontend payload independent of the local JSON source so the lookup can later be replaced by an API request without changing the UI contract.

## Boundaries

- This is a data lookup for display only. Do not run StarSim, build the Sol system, run a simulation, or load the complete Sol dataset into the UI.
- Do not create a generic provider framework, hand-authored Earth fixture, or broad lookup-service port.
- Do not pass the entire source JSON to the frontend; the controller returns only the values the UI needs.
- Do not add browser fetches to local JSON files, JavaScript file readers, or frontend lookup services. File access and body resolution belong in Rails.
- No frontend layout/renderer redesign, production API endpoint, unrelated models, migrations, persistence, terrain loading, or simulation logic.
- Do not invent values for fields absent from the sample. Terrain and live simulation state are separate sources.

## Acceptance criteria

- [ ] The actual minimal `sol.json` sample is copied unchanged to the prototype's `app/data/star_systems/sol.json` path and tracked by Git.
- [ ] The prototype lookup can resolve the requested Earth entry from the nested Sol data; it does not need to expose or load the full system into the UI.
- [ ] The Rails controller selects and returns the Earth values required by the Planetary UI in a focused payload.
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