---
title: "Generic Terrain Improvements & Infrastructure Sprites"
date: 2026-07-13
priority: HIGH
type: DESIGN
status: backlog
phase: UI
assigned_to: qwen
depends_on: "2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY"
---

## Summary
Define generic terrain improvement sprites (roads, farms, mines, power plants, docks) and infrastructure design language. These assets are **not planet-specific** and must render cleanly over any terrain type.

## Context
Civ4 gameplay requires visual indicators for terrain improvements (farms, mines, roads, irrigation). Current spritesheet has building section but lacks **generic improvement overlays** that work across all planet types.

Current Challenge:
- ❌ "Martian Mine" only makes sense on Mars
- ✅ Generic "Mine" works on any world (sprite is a pickaxe or mining symbol, not terrain-specific)
- Infrastructure must be small overlays that don't obscure terrain/biome sprites below

## Scope

### Phase 1: Design Language & Reference
1. **Improvement Types** (Civ4 and Galaxy Game standard set)
   - **Production:**
     - Farm (food)
     - Mine (production from regolith/minerals)
     - Irrigation (food boost)
     - Logging Camp (forest clearance)
   - **Resources:**
     - Resource Extraction Node (generic, specialized per material)
     - Harvester (mobile resource collector)
   - **Movement/Trade:**
     - Road (basic movement)
     - Rail (fast transport)
     - Pipeline (fluid transport — O2, H2O, propellant)
     - Harbor/Dock (water/liquid handling)
   - **Power:**
     - Solar Panel Field
     - Geothermal Vent (Titan-style)
     - Wind Farm (atmospheric worlds)
     - Reactor (nuclear)
   - **Command/Control:**
     - Outpost (visibility/control)
     - Communication Relay (signal)
     - Defense Tower (military)

2. **Visual Hierarchy Rules**
   - Improvement icons should occupy ~25% of tile space (not dominate)
   - Neutral colors (greys, industrial blues) that work over any biome
   - Icons must be recognizable at 32×32px tile size
   - Use symbolic rather than photorealistic style to match terrain sprite aesthetic

3. **Placement Rules**
   - Improvements render ABOVE biome/terrain sprites but BELOW units
   - Can stack up to 2-3 improvements per tile (e.g., road + farm, rail + mine)
   - Priority stacking: base improvement (farm) + modifier (irrigation) + connection (road)

### Phase 2: Asset Definitions
1. **Per-Improvement Asset Definition**
   - Icon sprite(s): base (small, 16×16px) + variants
   - Rotation variants if applicable (roads: N-S, E-W, corner, T-junction)
   - Color scheme: grayscale base + faction coloring overlay option
   - Canvas: transparent PNG, same 8-bit sRGB as terrain tiles

2. **Improvement Sprite Directory Structure**
   ```
   /data/images/improvements/
     farm/
       farm_01.png (field icon)
       farm_irrigated_01.png (wet field)
     mine/
       mine_01.png (pickaxe / mining symbol)
       mine_regolith_01.png
     road/
       road_straight.png (horizontal segment)
       road_vert.png (vertical segment)
       road_corner.png
       road_tjunction.png
       road_cross.png
     rail/
       rail_straight.png
       rail_vert.png
       rail_corner.png
     pipeline/
       pipeline_liquid.png (water/methane)
       pipeline_gas.png (oxygen/nitrogen)
       pipeline_energy.png (power conduit)
     power/
       solar_panel.png
       geothermal.png
       windmill.png
       reactor.png
     extraction/
       harvester_generic.png
       resource_node_marker.png
     dock/
       harbor_liquid.png
     command/
       outpost.png
       relay.png
       defense_tower.png
   ```

### Phase 3: Integration Rules
1. **Rendering Layer**
   - New Layer 3.5 (between biomes/resources and units)
   - Called during main render loop AFTER resource grid, BEFORE unit grid
   - Viewport-culled like terrain (no rendering off-screen)

2. **Data Structure**
   ```ruby
   # terrain_data.improvements array
   [
     {
       col: 42,
       row: 50,
       type: "farm",
       variant: "01",
       rotated: false
     },
     {
       col: 43,
       row: 50,
       type: "road",
       variant: "straight_horizontal",
       rotated: false
     }
   ]
   ```

3. **Asset Lookup Path**
   `/assets/improvements/{type}/{variant}.png`
   Fallback: `/assets/improvements/{type}/01.png`

### Phase 4: Faction Coloring (Optional Enhancement)
1. **Color Overlay System**
   - Base sprites: neutral grey/blue
   - Apply faction color tint in JS (blue for Earth, red for Mars faction, etc.)
   - Allows single sprite asset + runtime coloring

## Acceptance Criteria
- [ ] 10+ improvement types defined with design language rules
- [ ] All improvement sprites extracted/created and organized
- [ ] Sprites render cleanly over terrain without obscuring biome
- [ ] Up to 3 improvements stack per tile without visual conflict
- [ ] Rotation variants work for road/rail/pipeline (N-S, E-W, corners)
- [ ] Generic naming — no planet-specific suffixes
- [ ] Asset directory matches defined structure
- [ ] Render layer integration in surface_view.js working
- [ ] Performance: no measurable FPS impact from improvement rendering
- [ ] Faction coloring tint system implemented (if used)

## Blockers
- Depends on having final ChatGPT-derived or custom infrastructure sprites
- May need designer review for visual consistency

## Dependencies
- **Related**: 2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY (uses these sprites)
- **Related**: Unit/Building model definitions (determines which improvements are renderable)

## Notes
- Improvements are **visual indicators only** at this phase — actual mechanics (farm producing food, mine extracting resources) handled elsewhere
- Consider directional rendering for roads/rails (no diagonal movement in Civ4-style grids)
- Each improvement type should have 3-5 visual variants to reduce repetitiveness
- Fence/border rendering optional — may add too much visual clutter

## Next Steps
1. Review ChatGPT buildings section and extract reusable icons
2. Design generic improvement icon library
3. Extract/create sprites matching directory structure
4. Verify compatibility with 32×32 tile rendering
5. Integrate into surface_view.js improvement layer
