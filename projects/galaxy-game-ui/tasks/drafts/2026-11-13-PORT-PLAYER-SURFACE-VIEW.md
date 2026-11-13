---
status: drafts
priority: normal
type: ui-port
project: galaxy-game-ui
---

# Port the Player Surface View

## Goal

Bring the existing GalaxyGame Surface view into the separate Rails 8.1 UI prototype by reusing its renderer, terrain/biome pipeline, and existing assets. This is a visual/interaction prototype; do not build or repair the live simulation.

## Read first

- Main game design authority: `galaxyGame/docs/wiki/ui/02_player_view_architecture.md`
- Current main-game observations: `galaxyGame/docs/wiki/ui/04_current_ui_state_and_known_issues.md`
- UI prototype direction: `galaxy-game-ui/docs/wiki/README.md`
- UI porting guidance: `galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md`
- UI data approach: `galaxy-game-ui/docs/wiki/data-integration-and-migration.md`

## Inspect and reuse

In the main Rails 7 application, begin with:

- `app/views/admin/celestial_bodies/surface.html.erb`
- `app/assets/javascripts/surface_view.js`
- `app/assets/javascripts/biome_renderer.js`
- `app/assets/javascripts/terrain_tile_renderer.js`
- `app/assets/javascripts/simple_tileset_loader.js`

Also inspect the embedded `#surface-data` payload, tileset manifest, biome `asset_path` values, and terrain image paths. Verify exact current paths and dependencies before copying. Inspect the target prototype first and reuse any work already present.

## Work

1. Port the Surface view and the existing rendering pipeline into the prototype.
2. Reuse the existing biome/terrain renderers and tileset assets; do not replace them with a new map implementation by default.
3. Adapt asset paths and initialization for the prototype's Rails 8.1/Propshaft/importmap/Turbo setup as needed.
4. Use existing Surface JSON/data where practical and clearly identified mock JSON for gaps. Keep the same presentation code for imported and mock data.
5. Make the page render without main-app routes, APIs, polling, or simulation services.
6. Fit the view into the prototype's existing Player context and visual shell. Make focused CSS adjustments for legibility and information density.
7. Record which data/assets were reused, any missing or incompatible assets, and what data the screen appears to need. Do not define an API contract.

Do not silently diagnose or redesign the underlying map/data pipeline based only on old screenshots. If an existing source asset or renderer is broken, identify the specific failure and reuse what still works; ask before expanding into a separate repair project.

## Acceptance criteria

- [ ] Surface is reachable through the prototype's Player navigation/context.
- [ ] The map/terrain view initializes and renders representative terrain/biome content.
- [ ] Existing renderer and asset pipeline are reused where practical.
- [ ] Required tileset/terrain assets load from prototype-compatible paths.
- [ ] No main-app backend, API, or live simulation is required.
- [ ] Mock and imported data are distinguishable.
- [ ] The result is checked in the running prototype; report visual limitations and any unresolved asset/data issues.
- [ ] Report exact files reused/changed and verification performed.

## Out of scope

Planetary, Space, Settlement, Admin, AI Manager, new terrain datasets, large-scale geospatial pipeline changes, production APIs, simulation behavior, unrelated bug fixing, and broad redesign.

Do not modify the main game repository. Do not commit or push without Tracy's explicit approval.
