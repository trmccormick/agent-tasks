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

# Load Sol JSON and resolve planetary data

## Objective

Give the Rails 8.1 prototype a replaceable local data provider that loads the main game's minimal Sol test system and resolves Earth for the Player Planetary view.

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Main-app lookup reference: `galaxy_game/app/services/lookup/star_system_lookup_service.rb`, its base/dependencies, and `GalaxyGame::Paths`.
- Prototype integration point: `galaxy-game-ui/app/controllers/player_controller.rb`.

## Scope

- Copy the main application's minimal `sol.json` test system into the prototype's matching app data location: `galaxy-game-ui/app/data/star_systems/sol.json`. Confirm the main-app source through `GalaxyGame::Paths::STAR_SYSTEMS_PATH`; use `sol.json`, not `sol-complete.json`.
- Adapt only the lookup and path behavior needed to load Sol and resolve an individual body such as Earth. Reuse the main-app implementation where appropriate; do not port every lookup service.
- Establish a source-independent provider boundary and make the resolved body data available to the existing Rails controller as a consistent `@planetary_data` payload.
- Keep the provider replaceable by a future main-app API implementation without changing the frontend contract.

## Boundaries

- No hand-authored replacement Earth/system fixture and no generic mock-data framework.
- No frontend layout/renderer redesign in this task; the Planetary View task consumes the controller payload.
- No API endpoint, unrelated models, migrations, persistence, simulation logic, or broad backend expansion.
- Do not treat `sol.json` as a terrain-map or live simulation-state source. Do not invent missing values.

## Acceptance criteria

- [ ] The minimal source `sol.json` is copied unchanged to the prototype's corresponding `app/data/star_systems/sol.json` path and is tracked by Git.
- [ ] The prototype can load Sol and resolve Earth from the actual nested system/body data.
- [ ] The existing Rails controller can obtain Earth through the provider and expose it as `@planetary_data`.
- [ ] Provider/controller tests cover successful Sol/Earth resolution and missing or malformed source data.
- [ ] The frontend-facing payload does not expose file paths or depend on the local JSON provider.
- [ ] No parallel static planetary definitions, invented data, terrain data, or unrelated backend infrastructure are added.
- [ ] Relevant Rails checks and focused tests pass.

## Dependencies

**Blocked by:** none  
**Blocks:** `2026-11-19-MEDIUM-FEATURE-REVIEW-PLAYER-PLANETARY-VIEW.md`

## Dispatch note

This is the next task in the agreed order. It remains a draft until Tracy explicitly approves dispatch.
