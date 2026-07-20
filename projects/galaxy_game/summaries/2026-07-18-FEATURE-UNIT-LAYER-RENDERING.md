# Synthesis: Unit/Structure Layer Rendering & Sprite Integration

**Date**: 2026-07-18
**Task**: 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING.md

---

## Research Findings

### Unit Model Architecture
- **Base class**: `Units::BaseUnit` (table: `base_units`) — polymorphic owner, has_one `celestial_location`
- **Subclasses**: Extractor, Habitat, Fabricator, Computer, Battery, Propulsion, Storage, Robot, PlanetaryUmbilicalHub
- **Craft models**: `Craft::BaseCraft` (separate table) with subclasses: Rover, Harvester, Ship, Spaceship, Satellite
- **CelestialBody associations**: `has_many :orbiting_craft`, `has_many :locations` — units attach to crafts/settlements via `attachable` polymorphic

### Terrain Data Flow
- Controller: `celestial_bodies_controller.rb#surface` sets `@terrain = @celestial_body.geosphere&.terrain_map`
- View: `surface.html.erb` injects terrain_data as JSON into a `<script type="application/json">` element
- Frontend: `SurfaceView.init()` reads from `#surface-data`, parses JSON, builds layers

### SurfaceView.js Architecture
- Layers object: `{ elevation, liquid, biomes, resources }` — all null initially, populated by `_buildLayers()`
- Render loop: RAF dirty-flag, viewport-culled, draws Layer 0→3 in order
- Layer toggles: `.layer-btn[data-layer="..."]` buttons toggle `visibleLayers` Set
- Toggle button: `#toggleTerrainBtn` for biome/sprite toggle (biome worlds only)

### Sprite Assets
- Source file: `/Users/tam0013/Documents/git/galaxyGame/data/images/test-images/ChatGPT Image Mar 4, 2026, 10_19_00 PM.png`
- Need to extract 16 sprites from the 4×4 grid

### Stop Condition Check
- ✅ Unit model exists with `celestial_location` (has grid_position via location)
- ✅ Sprite asset file exists at documented path
- ✅ terrain_data JSON schema is consistent (elevation, liquid, biomes, resource_grid)
- ⚠️ Upstream sprite tiles task dependency — need to verify completion status

---

## Implementation Plan

### Phase 1: Backend — Unit Grid Export

**File**: `galaxy_game/app/controllers/admin/celestial_bodies_controller.rb` (surface action)

The surface action already loads `@terrain`. We need to extend the terrain data with unit_grid. Two approaches:
1. **Service object** (`TerrainDataBuilder`) — clean separation, testable
2. **Direct in controller** — simpler, fewer files

Decision: Create a service object `TerrainDataBuilder` for clean architecture and testability.

**Unit-to-sprite mapping** (16 sprites):
```
Units::Extractor → sprite_00 (mining/drilling)
Units::Habitat → sprite_01 (habitation dome)
Units::Fabricator → sprite_02 (manufacturing)
Units::Computer → sprite_03 (command center)
Units::Battery → sprite_04 (power storage)
Units::Propulsion → sprite_05 (engine/thruster)
Units::Storage → sprite_06 (storage tank)
Units::Robot → sprite_07 (exploration robot)
Craft::Rover → sprite_08 (surface rover)
Craft::Harvester → sprite_09 (atmospheric harvester)
Craft::Ship → sprite_10 (orbital ship)
Craft::Spaceship → sprite_11 (deep space vessel)
Craft::Satellite → sprite_12 (orbital satellite)
Structures::PlanetaryUmbilicalHub → sprite_13 (planetary hub)
Generic structure → sprite_14 (generic building)
Generic vehicle → sprite_15 (generic vehicle)
```

### Phase 2: Frontend — Layer 5 Rendering

**File**: `galaxy_game/app/assets/javascripts/surface_view.js`

Changes needed:
1. Add `units: null` to layers object in `_buildLayers()`
2. Add `showUnits: true` boolean flag
3. Add `unitSprites: []` array for sprite images
4. Add `loadUnitSprites()` method (16 sprites)
5. Add `drawUnits()` render pass (after biomes, before resources)
6. Add "Show Units" toggle button to layer controls

### Phase 3: Sprite Asset Extraction

Extract 16 individual PNG sprites from the source image. Save to `galaxy_game/app/assets/images/unit_sprites/`.

### Phase 4: Testing

RSpec specs for:
- `TerrainDataBuilder` — unit_grid structure, sprite mapping
- SurfaceView.js — layer toggle behavior (if JS testing available)

---

## Risks & Assumptions

1. **Upstream dependency**: Sprite tiles integration task must be complete before this works end-to-end
2. **Unit positioning**: Units attach to crafts/settlements which have `celestial_location` — need to query via that association
3. **Sprite extraction**: The 4×4 grid sprite sheet needs to be split into 16 individual PNGs
4. **One unit per tile**: Simplified model — no stacking support

---

## Files to Create/Modify

### New files:
- `galaxy_game/app/services/terrain_data_builder.rb`
- `galaxy_game/app/assets/images/unit_sprites/sprite_00.png` through `sprite_15.png` (extracted from source)
- `spec/services/terrain_data_builder_spec.rb`

### Modified files:
- `galaxy_game/app/controllers/admin/celestial_bodies_controller.rb` — add unit_grid to surface action
- `galaxy_game/app/assets/javascripts/surface_view.js` — Layer 5 rendering
- `galaxy_game/app/views/admin/celestial_bodies/surface.html.erb` — "Show Units" toggle button
