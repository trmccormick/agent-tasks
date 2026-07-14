---
title: "Unit/Structure Layer Rendering & Sprite Integration"
date: 2026-07-13
priority: HIGH
type: FEATURE
status: backlog
phase: UI
assigned_to: qwen
depends_on: "2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION"
---

## Summary
Implement unit/structure rendering layer in surface views. Map 16 isometric structure sprites (biodomes, rovers, command centers) to Unit models in Ruby, populate unit_grid in terrain_data JSON, and add Layer 5 rendering to SurfaceView.js.

## Context
- **16 structure sprites** available (4×4 grid, isometric perspective)
  - Biodomes (habitats)
  - Rovers (mining/transport vehicles)
  - Command/control structures
  - Color-coded variants (blue, yellow, grey)
- **Unit data missing** from terrain_data JSON output
- **No unit rendering layer** in surface_view.js (currently only Layers 0–3: elevation, liquid, biomes, resources)

## Scope

### Phase 1: Backend Integration (Ruby)
1. **Unit Model Association**
   - Determine which Unit model types map to structure sprites
   - Create sprite lookup table (unit_type/role → sprite_index)
   - Example: BioDome(type=habitat) → sprite_03, Rover(type=mining) → sprite_11

2. **Terrain Data Export**
   - Add `unit_grid` to terrain_data JSON (same dimensions as elevation)
   - Store unit identifiers or null per tile (e.g., ["unit_1", null, "unit_3", ...])
   - Include unit metadata: {id, type, role, owner_faction} alongside grid

3. **Unit Positioning**
   - Decide: static grid placement (one unit per tile) or stack multiple units?
   - Define rendering order (small units on top of terrain/biomes, large structures)

### Phase 2: Frontend Rendering (JavaScript)
1. **Unit Layer in SurfaceView**
   - Add Layer 5: Units (rendered after Layer 4 would be resources overlay)
   - Load unit sprite images (16 sprites as canvas/image assets)
   - Implement unit sprite selection by type/role

2. **Unit Rendering Pass**
   - Loop through unit_grid during viewport-culled render
   - Draw unit sprite at correct screen position
   - Support unit highlighting/selection on mouseover

3. **Layer Toggle**
   - Add "Show Units" toggle button to layer controls
   - Allow toggling unit visibility separately from terrain/biomes

### Phase 3: Testing & Polish
1. **Unit Data Validation**
   - Ensure all unit_grid tiles have valid references
   - Test with mixed unit scenarios (empty tiles, stacked units)

2. **Sprite Rendering Verification**
   - Confirm all 16 sprites render correctly
   - Check sizing and alignment (should match 32px tile size like terrain)

3. **RSpec Coverage**
   - Unit-to-sprite mapping specs
   - terrain_data export specs (verify unit_grid structure)

## Acceptance Criteria
- [ ] Unit sprite lookup table implemented in Ruby
- [ ] unit_grid populated in terrain_data JSON with unit references
- [ ] Layer 5 rendering code added to SurfaceView.js
- [ ] All 16 structure sprites display correctly in surface view
- [ ] Units render on top of terrain/biomes layers
- [ ] "Show Units" toggle functional
- [ ] No broken image references
- [ ] RSpec specs added for unit mapping and data export
- [ ] Unit layer integrates cleanly with existing terrain/biome layers

## Blockers
- Depends on terrain sprite integration completion (Layer 0 needs to work first)
- Need to define unit model schema (which models have sprite representations)
- Need to know which unit types are placeable on surface (all? only specific roles?)

## Dependencies
- **Upstream**: 2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION (terrain tiles must load first)
- **Sprite Assets**: 16 isometric structure sprites (location: `/Users/tam0013/Documents/git/galaxyGame/data/images/test-images/ChatGPT Image Mar 4, 2026, 10_19_00 PM.png`)

## Notes
- Unit rendering is separate from terrain/biome rendering due to backend model mapping requirements
- Structure sprites already extracted and catalogued; extraction task not needed
- This is UI-layer work but has backend dependencies (unit model definitions)
- Consider isometric sprite orientation vs. orthographic map projection when sizing

## Next Steps
1. Research which Unit/Structure models should be visible on surface
2. Define sprite-to-type mapping
3. Implement unit_grid export in terrain_data builder
4. Add Layer 5 to surface_view.js render loop
5. Test with sample unit placements
