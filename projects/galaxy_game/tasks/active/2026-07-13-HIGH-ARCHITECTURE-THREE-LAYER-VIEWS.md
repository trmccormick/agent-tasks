---
title: "Three-Layer View Architecture & Integration"
date: 2026-07-13
priority: HIGH
type: ARCHITECTURE
status: backlog
phase: UI
assigned_to: qwen
depends_on: null
---

## Summary
Define the complete rendering and interaction architecture for celestial bodies: Planetary View (SimEarth), Surface View (Civ4), and TerrainForge Detail View (SimCity). Establish data flow, zoom hierarchy, and layer separation rules.

## Context
Galaxy Game UI needs **three distinct operational levels** rather than monolithic surface map:

1. **Planetary View** — See entire world, macro weather, biome distribution
2. **Surface View** — Tactical settlement management (Civ4-style map)
   - Example: See Valles Marineris has a settlement tile (marker icon)
3. **TerrainForge Layer** — Same Surface View rendering, zoomed in on one settlement tile
   - Example: Click settlement on Valles Marineris → zoom in → see worldhouse structure, buildings inside/around it, roads, power infrastructure at readable scale
   - **NOT a separate rendering system** — same surface_view.js, just camera zoomed in

Current work focuses exclusively on **Surface View foundation**. This task defines how all three layers fit together and avoid scope creep into planetary concerns.

## Scope

### Phase 1: Architecture Definition
1. **Planetary View Specification**
   - Render planet as 3D globe or 2D flattened projection
   - Display atmospheric layers, cloud formations, weather systems
   - Show biome distribution heatmap
   - Allow rotation, zoom trigger to Surface View
   - Integration point: Click region → zoom to Surface View at that location
   - **Out of scope for Surface View work**

2. **Surface View Specification (PRIMARY FOCUS)**
   - Isometric or orthographic grid map (Civ4 standard)
   - Tactical-level settlement management
   - Resource/improvement display and interaction
   - Unit movement and orders
   - City overlay and yield management
   - Layer toggles for clarity
   - **IN SCOPE** — Current tasks define this layer

3. **TerrainForge Detail View Specification**
   - **Same rendering system as Surface View, but zoomed in on one settlement tile**
   - Zoom in 10-100x on a single settlement tile to show buildings at full scale
   - Shows all buildings/structures contained on that settlement tile (same sprites, larger)
   - Click building to configure operational parameters (production rate, staffing, inventory)
   - Drag-place new structures or rearrange on the tile
   - Connect pipelines/roads between buildings on this tile (same improvement system)
   - Uses same layer rendering (terrain, biomes, improvements, units) just zoomed
   - Double-click settlement tile in Surface View → TerrainForge at that location
   - Scroll out/zoom out → return to Surface View
   - **Not a separate rendering engine; just a camera zoom into Surface View**

### Phase 2: Data Flow & Zoom Hierarchy
1. **Zoom Navigation**
   ```
   Planetary View (overview)
     ├─ Rotation/pan on globe
     └─ Click region / "drill down" button
          ↓
     Surface View (settlement region)
        ├─ Pan/zoom within region
        ├─ Layer toggles (terrain, improvements, units, settlements)
        └─ Double-click SETTLEMENT TILE / "enter settlement" button
             ↓
          TerrainForge View (detail of that settlement tile)
             ├─ View all buildings on this tile
             ├─ Click building to configure
             └─ Close / "back to map" button
                  ↑
             Surface View (return)
   ```

2. **Context Preservation**
   - When zooming from Planetary → Surface, remember clicked location
   - Center Surface View camera on that region
   - When entering TerrainForge, pause Surface View time (or freeze camera)
   - When exiting TerrainForge, resume Surface View with same camera position

### Phase 3: Layer Separation Rules
1. **What Each Layer IS Responsible For**
   - **Planetary:** Global atmospheric effects, biome heatmap, weather visualization, macro events
   - **Surface/TerrainForge (same rendering):** Terrain rendering, improvements (roads/farms/mines), units/vehicles, settlement tiles, Civ4 gameplay
   - **Note:** TerrainForge IS Surface View zoomed in on one settlement tile — same rendering pipeline, different camera

2. **What Each Layer IS NOT Responsible For**
   - **Planetary:** Do NOT include settlement-level detail (tiles, improvements, units, settlement tiles)
   - **Surface/TerrainForge:** Do NOT include planetary atmosphere

3. **Data Ownership**
   - Planetary: planet_data, atmosphere_layers, weather_grid, biome_distribution
   - Surface + TerrainForge: **SAME terrain_data** (elevation, biomes, resources, improvements, units, settlement tiles)
   - **Difference: camera zoom and viewport focus only**
1. **Planetary → Surface Zoom**
   - User clicks region on planet
   - Determine which Surface View tile region that corresponds to
   - Initialize Surface View with camera centered on region
   - Smoothly transition (fade or zoom animation)

2. **Surface → TerrainForge (Enter Settlement Tile)**
   - User double-clicks settlement tile on Surface grid
   - Settlement tile is marked with special overlay (icon, glow, color)
   - Fetch buildings on this settlement tile from terrain_data
   - Initialize TerrainForge view showing all buildings on THIS tile
   - Pause Surface View time (optional: continue or freeze)

3. **TerrainForge → Surface Return (Exit Settlement Tile)**
   - User closes TerrainForge (back button or ESC)
   - Resume Surface View at same camera position
   - Reflect any parameter changes made to buildings (production rate, staffing, etc.)
   - Settlement tile still shows same overlay/marker

### Phase 5: Rendering Technology Considerations
1. **Planetary View Rendering**
   - Options: Three.js (3D globe), Babylon.js, or 2D canvas with projection
   - Atmosphere shader for visual effect
   - Cloud layer animation
   - Biome color overlay with blending

2. **Surface View Rendering**
   - 2D canvas (existing surface_view.js)
   - Orthographic or isometric projection
   - Sprite-based rendering (terrain, improvements, units)
   - Viewport culling for performance

3. **TerrainForge Rendering**
   - 2D or 3D depending on building visual complexity
   - Interactive connection editing (drag lines between ports)
   - Real-time visualization of power/material flow

## Acceptance Criteria
- [ ] Complete specification document for all three layers (architecture, data flow, responsibilities)
- [ ] Zoom hierarchy flow chart defined and documented
- [ ] Layer separation rules clearly stated (what each layer does/doesn't do)
- [ ] Integration points defined (zoom transitions, data synchronization)
- [ ] Data ownership clarified (which layer owns which terrain_data fields)
- [ ] Rendering technology recommendations documented
- [ ] Scope boundaries clearly marked (prevents Surface View tasks from creeping into planetary/terrain-forge)
- [ ] Surface View layer fully documented (current state + in-progress tasks)

## Blockers
- None — this is pure architecture/specification work

## Dependencies
- None — foundational document
- **Informs**: All current and future view layer work

## Notes
- Planetary View is out of scope for current surface_view.js improvements
- **TerrainForge IS NOT separate work** — it's surface_view.js with camera zoomed on one tile
- Example: Valles Marineris worldhouse
  - Surface View: See settlement marker on Valles Marineris tile
  - Click/zoom into tile: TerrainForge shows worldhouse structure, buildings, roads, power infrastructure at readable scale
  - Manage: Place structures, assign units, configure production
  - Zoom out: Return to Surface View
- This document serves as scope boundary enforcement
- Should be referenced in all UI-related task discussions

## Next Steps
1. Create detailed specification document for each layer
2. Define exact data structures for layer transitions
3. Document camera positioning/interpolation for smooth zooming
4. Create mockups/wireframes for each layer
5. Identify any shared systems (e.g., sprite cache used by multiple layers)
6. Plan rendering performance budget for each layer
