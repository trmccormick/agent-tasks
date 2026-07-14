---
title: "Civ4-Style Surface View Gameplay Layer"
date: 2026-07-13
priority: HIGH
type: FEATURE
status: backlog
phase: UI
assigned_to: qwen
depends_on: "2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION"
---

## Summary
Add Civ4-style interactive gameplay to surface view: city overlays, terrain improvements, tile selection, yield displays, and building/unit placement UI.

## Context
Current surface_view.js is **pure rendering** (read-only visualization). Civ4 gameplay requires **interactive mechanics**:
- Click tiles to inspect elevation/biome/resources/yields
- See city control radius and citizen allocation
- Toggle terrain improvement layers (roads, farms, mines)
- Drag-select units for movement
- Place buildings/infrastructure with visual feedback
- Display production/yield information per tile

## Scope

### Phase 1: City Overlay Layer
1. **Settlement Radius Visualization**
   - Draw semi-transparent overlay showing city control area (typically 2-3 tiles radius)
   - Different colors per faction/settlement type
   - Toggle on/off with layer button

2. **Tile Yield Display**
   - Show food/production/science/culture per tile (from yield_data JSON)
   - Overlay small numbers or icons on each tile
   - Highlight high-yield tiles during city planning

3. **Citizen Allocation Indicator**
   - Visual marker showing which tiles are being worked by settlement
   - Different icon/color for allocated vs. available tiles

### Phase 2: Terrain Improvements & Infrastructure
1. **Improvement Sprites Layer**
   - Generic road/rail/pipeline icons (small overlays, don't obscure terrain)
   - Farm/irrigation markers
   - Mine indicators
   - Power generation markers (solar, geothermal, wind)
   - Position determined by Building model in terrain_data.improvements array (NEW)

2. **Improvement Visibility Toggle**
   - Separate layer control: "Show Improvements"
   - Can be toggled independent of units/biomes

### Phase 3: Tile Interaction & UI
1. **Click-to-Inspect**
   - Click tile → show detail panel: elevation, biome, resources, yields, current occupant
   - Display panel location: side panel or hover tooltip
   - Show resource amounts (water, minerals, rare elements)

2. **Tile Selection State**
   - Visual highlight for selected tile (border, subtle glow)
   - Persist selection until clicking another tile or ESC

3. **Building Placement Preview**
   - Drag-select building type from UI
   - Preview placement on hover (ghost sprite with transparency)
   - Click to confirm placement (if allowed)
   - Show validation feedback (OK/not allowed due to terrain/conflicts)

### Phase 4: Unit Movement & Orders
1. **Unit Selection & Movement Path**
   - Click unit → show movement range overlay (pathfinding via shortest-path algorithm)
   - Highlight tiles unit can reach with current fuel/movement
   - Drag to move or click destination (Civ4-style)

2. **Order Indicators**
   - Show current unit orders as overlay text/icons
   - "Mining" / "Moving" / "Idle" states visible on map

### Phase 5: Resource Node Interactivity
1. **Resource Detail on Hover**
   - Show resource type/amount when hovering over resource node
   - Click to filter map view (show only water, only minerals, etc.)

2. **Harvestable Resource Queue**
   - Visual indicator when a resource is queued for extraction
   - Countdown or progress bar overlay

## Data Requirements

**New terrain_data fields:**
```json
{
  "city_overlays": [
    {
      "settlement_id": 1,
      "center_col": 50,
      "center_row": 40,
      "radius": 3,
      "faction": "Earth",
      "worked_tiles": [[50,40], [51,40], [50,41]]
    }
  ],
  "improvements": [
    {
      "col": 51,
      "row": 40,
      "type": "road",
      "direction": "horizontal"
    },
    {
      "col": 52,
      "row": 41,
      "type": "mine",
      "material": "regolith"
    }
  ],
  "yield_grid": [[{food: 2, prod: 1}, {food: 1, prod: 3}]],  // or null per tile
  "unit_orders": [
    {
      "unit_id": 5,
      "order": "mining",
      "target_col": 55,
      "target_row": 42,
      "time_remaining": 120
    }
  ]
}
```

## Acceptance Criteria
- [ ] City overlay renders with faction-specific colors
- [ ] Tile yields (food/prod/science) display when improvement layer enabled
- [ ] Click tile → detail panel shows elevation, biome, resources, yields
- [ ] Building/improvement icons render without obscuring terrain
- [ ] Improvements toggle on/off via layer control
- [ ] Unit movement range visible on unit selection
- [ ] Resource nodes show type/amount on hover
- [ ] Tile selection state persists and visually highlights
- [ ] Building placement preview (ghost sprite) works on hover
- [ ] Unit order status ("Mining", "Moving") visible as overlay
- [ ] No performance regression from additional overlays
- [ ] RSpec specs cover city overlay, improvement rendering, selection logic

## Blockers
- Depends on terrain + unit sprite rendering working first
- Requires yield_data builder in Ruby (may not exist yet)
- Unit/Building data must be exported in terrain_data JSON
- Requires pathfinding algorithm for movement range calculation

## Dependencies
- **Upstream**: 2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION
- **Upstream**: 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING
- **Related**: Civ4 UI reference (see docs/reference/civ4_gameplay_mechanics.md if it exists)

## Notes
- Civ4 uses isometric view; confirm tile selection logic works with current orthographic map
- Resource node harvesting may require separate Civ4-style "right-click → queue action" workflow
- Consider accessibility: ensure all overlays can be toggled for clarity
- This is the "gameplay feel" layer — transforms surface_view from static map to interactive strategic interface

## Next Steps
1. Design city overlay data structure (settlement radius, faction colors, worked tiles)
2. Implement yield_grid builder in terrain_data generator
3. Add improvements array to terrain_data export
4. Implement city overlay rendering pass
5. Add tile click detection and detail panel
6. Test with sample settlement + improvements
