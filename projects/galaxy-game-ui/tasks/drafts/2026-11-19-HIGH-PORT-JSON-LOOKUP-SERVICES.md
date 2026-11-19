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

Load the prototype's local copy of StarSim's minimal Sol JSON sample, resolve the requested body (initially Earth), and let the Rails controller return the values the Planetary UI needs to display.

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Main-app lookup reference: `app/services/lookup/star_system_lookup_service.rb` and `GalaxyGame::Paths`.
- Prototype integration point: `app/controllers/player_controller.rb`.

## Scope

- Copy the actual minimal `sol.json` sample used by the main application into `galaxy-game-ui/app/data/star_systems/sol.json`. Confirm the source through `GalaxyGame::Paths::STAR_SYSTEMS_PATH`; do not use `sol-complete.json` or create a replacement fixture.
- Implement only the lookup needed to load this Sol sample and find the requested body by name/identifier (initially Earth) in its nested celestial-body collection.
- Have the existing Rails controller request Earth and select/return the values required by the Planetary UI in a focused display payload.
- Keep the controller-to-frontend payload independent of the local JSON source so the lookup can later be replaced by an API request without changing the UI contract.

## Boundaries

- This is a data lookup for display only. Do not run StarSim, build the Sol system, run a simulation, or load the complete Sol dataset into the UI.
- Do not create a generic provider framework, hand-authored Earth fixture, or broad lookup-service port.
- Do not pass the entire source JSON to the frontend; the controller returns only the values the UI needs.
- No frontend layout/renderer redesign, production API endpoint, unrelated models, migrations, persistence, terrain loading, or simulation logic.
- Do not invent values for fields absent from the sample. Terrain and live simulation state are separate sources.

## Acceptance criteria

- [ ] The actual minimal `sol.json` sample is copied unchanged to the prototype's `app/data/star_systems/sol.json` path and tracked by Git.
- [ ] The prototype lookup can resolve the requested Earth entry from the nested Sol data; it does not need to expose or load the full system into the UI.
- [ ] The Rails controller selects and returns the Earth values required by the Planetary UI in a focused payload.
- [ ] Tests cover successful Earth lookup and missing or malformed source/body data.
- [ ] The frontend receives the display payload without knowing the JSON file path, source structure, or lookup implementation.
- [ ] The payload boundary is suitable for replacing local lookup with a future API request without changing the frontend contract.
- [ ] Relevant Rails checks and focused tests pass.

## Dependencies

**Blocked by:** none  
**Blocks:** `2026-11-19-MEDIUM-FEATURE-USE-LOADED-DATA-PLAYER-PLANETARY-VIEW.md`

## Dispatch note

This is the next task in the agreed order. It remains a draft until Tracy explicitly approves dispatch.