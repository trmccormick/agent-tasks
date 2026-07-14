---
title: "Settlement Tiles & SimCity Entry Point Design"
date: 2026-07-13
priority: HIGH
type: DESIGN
status: backlog
phase: UI
assigned_to: qwen
depends_on: "2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS"
---

## Summary
Define how settlement tiles function as navigation points between Surface View (Civ4-style tactical map) and TerrainForge (SimCity-style detail view). A settlement tile is a special map location where structures/infrastructure are housed.

## Context
The three-layer architecture works as follows:
- **Surface View:** Tactical grid map showing entire settlement region (50×50 tiles typical)
- **Settlement Tile:** A single Surface View tile that contains buildings/infrastructure (marked with special overlay)
- **TerrainForge Detail View:** Interior view of ONE settlement tile's structures and layouts

Currently, the design conflated "buildings in the game" with "buildings visible across entire planet" — but actually:
- Structures are localized to **specific settlement tiles**
- Each settlement tile can contain multiple buildings (habitat domes, mining stations, power plants)
- Surface View shows settlement tiles with small sprite/icon indicating "settlement present here"
- Double-click settlement tile → enter TerrainForge to see and manage buildings on THAT tile

## Scope

### Phase 1: Settlement Tile Definition
1. **Settlement Tile Concept**
   - A settlement tile is a Surface View grid cell (32×32px on screen)
   - Special property: `has_settlement: true` in terrain_data
   - Contains array of buildings located on that tile
   - Can have multiple structures stacked (habitat dome + mining station + power plant all on same tile)
   - Size: Not larger than one surface tile (constraint: prevents mega-structures spanning multiple tiles in Phase 5)

2. **Settlement Tile Data Structure**
   ```json
   {
     "col": 50,
     "row": 42,
     "settlement_id": 1,
     "settlement_name": "Jezero Base",
     "faction": "Earth",
     "buildings": [
       {
         "id": 101,
         "type": "habitat",
         "capacity": 50,
         "power_input": 15,
         "status": "operational"
       },
       {
         "id": 102,
         "type": "mining_station",
         "material": "regolith",
         "power_input": 20,
         "production_rate": 100,
         "status": "idle"
       },
       {
         "id": 103,
         "type": "power_plant",
         "fuel_type": "solar",
         "power_output": 50,
         "status": "operational"
       }
     ]
   }
   ```

3. **Settlement Tile Rendering on Surface View**
   - Terrain/biome sprite rendered normally (Layer 0-3)
   - Settlement overlay icon placed on tile (Layer 4)
   - Icon indicates "settlement tile present here" (small dome/tower sprite)
   - Optional: Faction color border or glow
   - Optional: Population indicator (small number badge)

### Phase 2: Navigation Flow
1. **Surface View → TerrainForge Entry**
   - User clicks or double-clicks settlement tile in Surface View
   - Highlight settlement tile with glow/border to show selection
   - Display tooltip: "Habitat + Mining Station (2 buildings)"
   - Double-click → Transition to TerrainForge detail view

2. **TerrainForge Detail View**
   - Show interior layout of settlement tile
   - Display all buildings on this tile in 3D or detailed 2D view
   - Show power/material connections between buildings
   - User can click building to configure (set production rate, staffing, etc.)
   - Show building inventory/queue status
   - Bottom panel: "← Back to Surface View" button

3. **Return to Surface View**
   - Close TerrainForge or press ESC
   - Return to Surface View with same camera position
   - Reflect any parameter changes made to buildings

### Phase 3: Settlement Tile Markers & Overlays
1. **Visual Distinction**
   - Settlement tiles should be visually distinct from empty tiles
   - Options:
     - Colored background overlay (20% opacity faction color)
     - Small icon in corner (habitat dome, tower, etc.)
     - Glow effect around tile border
     - Text label showing settlement name (toggle-able)

2. **Information Display**
   - On hover: Show settlement name + building count
   - On click: Show detail panel (3-5 line text box):
     - Settlement name
     - Faction/owner
     - Population / capacity
     - Power status (produced vs. consumed)
     - Main production (mining regolith, generating power, etc.)

3. **Layer Toggle for Settlements**
   - Add "Show Settlements" layer toggle (can be disabled to see bare terrain)
   - Independent from unit/improvement layers

### Phase 4: Multiple Settlements on Region
1. **Handling Multiple Settlements**
   - Each Surface View region (50×50 tiles) might have 3-5 settlements
   - Each settlement tile independently accessible
   - Settlement overlays don't block neighboring tiles
   - UI makes it clear which tile is selected (glow/highlight)

2. **Settlement Navigation**
   - Double-click tile → enter SimCity for that tile
   - Right-click settlement → show context menu ("Manage", "Rename", "Delete", etc.)
   - Keyboard navigation: Tab to cycle through nearby settlements

### Phase 5: Data Export for TerrainForge
1. **Settlement Tile Data in terrain_data JSON**
   ```json
   {
     "settlements": [
       {
         "settlement_id": 1,
         "col": 50,
         "row": 42,
         "name": "Jezero Base",
         "faction_id": 1,
         "buildings": [ ...building array... ]
       }
     ]
   }
   ```
   Alternative: `settlement_grid` array parallel to `elevation_grid`

2. **Building Definition Schema**
   - Each building has: type, status, power_input/output, material (if applicable), capacity, queue
   - Store all needed state for TerrainForge to display and configure

## Acceptance Criteria
- [ ] Settlement tile concept clearly defined (single grid cell, contains array of buildings)
- [ ] Settlement tile data structure designed and documented
- [ ] Settlement tiles render as distinct overlay on Surface View (icon, color, or glow)
- [ ] Settlement tile hover/click shows info panel (name, building count, power status)
- [ ] Double-click settlement tile → transitions to TerrainForge view
- [ ] TerrainForge shows all buildings on that tile with configuration UI
- [ ] Back button returns to Surface View at same camera position
- [ ] Multiple settlements visible on same region without visual conflict
- [ ] Layer toggle for settlements works independent of other layers
- [ ] No performance impact from settlement overlays
- [ ] settlement_grid or settlements array properly exported in terrain_data JSON

## Blockers
- Depends on Surface View rendering foundation (Layers 0-3 working)
- Requires Building model definition in Ruby
- Requires TerrainForge detail view framework (separate layer, not this task's responsibility)

## Dependencies
- **Related**: 2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY (settlement tiles part of Civ4 UI)
- **Related**: 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING (buildings != units, different data)
- **Future**: TerrainForge detail view (consumes this data structure)

## Notes
- Settlement tiles are localized containers; prevents sprawling mega-bases across 10+ tiles
- Buildings within a settlement tile are connected internally (power/materials flow within same tile)
- Cross-tile connections (roads, pipelines between settlements) handled by Surface View improvements layer
- One settlement per tile or multiple buildings per tile? → Multiple buildings per tile (more interesting gameplay)
- TerrainForge responsibility: show building internals, configuration, connections between buildings on THIS tile only
- Surface View responsibility: show settlement tiles, show cross-tile connections (roads/pipelines)

## Next Steps
1. Finalize settlement tile data structure with Ruby developer
2. Design settlement tile visual treatment (which overlay style works best)
3. Implement settlement_grid or settlements array export in terrain_data builder
4. Add settlement overlay rendering to surface_view.js
5. Implement settlement tile click detection and info panel
6. Design TerrainForge entry/exit transition
7. Test with sample 3-settlement region
