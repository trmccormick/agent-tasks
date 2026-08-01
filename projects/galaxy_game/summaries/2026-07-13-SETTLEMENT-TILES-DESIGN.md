# Settlement Tiles & SimCity Entry Point — Design Document

**Task**: 2026-07-13-HIGH-DESIGN-SETTLEMENT-TILES-ENTRY-POINT.md
**Status**: Complete
**Date**: 2026-07-30
**Type**: Design/Schema (NOT implementation)

---

## Executive Summary

This document defines the **data structure**, **visual treatment rules**, and **navigation flow** for settlement tiles within the existing three-layer architecture. Settlement tiles are Surface View grid cells marked with `has_settlement: true` that contain arrays of buildings. TerrainForge is NOT a separate rendering layer — it's surface_view.js zoomed 10-100x on one settlement tile.

---

## 1. Settlement Tile Data Structure

### 1.1 Integration Point: terrain_data JSON

Settlement data integrates into the **existing terrain_data JSON** exported by `TerrainDataBuilder`. No separate export or API endpoint needed.

**Current terrain_data structure** (from `TerrainDataBuilder#build`):
```ruby
{
  elevation: [[...]],           # 2D array of elevation values
  biomes: [[...]],              # 2D array of biome strings
  resources: [[...]],           # 2D array of resource types
  width: 100,
  height: 100,
  quality_score: 85,
  generation_method: 'nasa_mola',
  unit_grid: [[{sprite_index, entity_id, ...}, nil, ...], ...]  # 2D grid
}
```

**New field added to terrain_data**:
```ruby
# In TerrainDataBuilder#build, add:
settlements: extract_settlements(terrain_map_data)
```

### 1.2 Settlement Tile Schema (JSON)

```json
{
  "settlements": [
    {
      "settlement_id": 1,
      "name": "Jezero Base",
      "faction_id": 1,
      "col": 50,
      "row": 42,
      "status": "active",
      "population": 127,
      "capacity": 200,
      "power_balance": {
        "produced": 85,
        "consumed": 62
      },
      "buildings": [
        {
          "id": 101,
          "type": "habitat",
          "status": "operational",
          "capacity": 50,
          "occupancy": 48,
          "power_input": 15,
          "production_rate": null,
          "queue": []
        },
        {
          "id": 102,
          "type": "mining_station",
          "material": "regolith",
          "status": "idle",
          "power_input": 20,
          "production_rate": 100,
          "queue": [
            { "item": "refined_iron", "quantity": 500, "eta_turns": 12 }
          ]
        },
        {
          "id": 103,
          "type": "power_plant",
          "fuel_type": "solar",
          "status": "operational",
          "power_output": 50,
          "power_input": 0,
          "queue": []
        }
      ]
    }
  ]
}
```

### 1.3 Terrain Map Integration

The `geospheres.terrain_map` JSONb column already stores terrain data. Settlement data is added as a top-level key:

```ruby
# In Geosphere model or TerrainDataBuilder:
terrain_map['settlements'] = settlements_array
```

**No migration needed** — terrain_map is JSONb, so new keys are schemaless.

### 1.4 Ruby Model (Suggested)

```ruby
# app/models/settlement.rb (NEW)
class Settlement < ApplicationRecord
  belongs_to :celestial_body, class_name: 'CelestialBodies::CelestialBody'
  has_many :buildings, dependent: :destroy
  has_many :structures, as: :structureable, class_name: 'Structure', dependent: :destroy

  validates :col, :row, presence: true
  validate :col_row_within_bounds

  def to_terrain_data_entry
    {
      settlement_id: id,
      name: name,
      faction_id: faction_id,
      col: col,
      row: row,
      status: status,
      population: population,
      capacity: capacity,
      power_balance: {
        produced: power_produced,
        consumed: power_consumed
      },
      buildings: buildings.map(&:to_terrain_data_entry)
    }
  end

  private
  def col_row_within_bounds
    return unless celestial_body&.geosphere&.terrain_map
    terrain = celestial_body.geosphere.terrain_map
    w = terrain['width'] || 100
    h = terrain['height'] || 100
    errors.add(:col, 'out of bounds') if col < 0 || col >= w
    errors.add(:row, 'out of bounds') if row < 0 || row >= h
  end
end

# app/models/building.rb (NEW)
class Building < ApplicationRecord
  belongs_to :settlement, class_name: 'Settlement'

  # type enum: habitat, mining_station, power_plant, fabricator, storage,
  #             laboratory, agriculture, refinery, warehouse, command_center
  validates :type, presence: true, inclusion: { in: %w[habitat mining_station power_plant fabricator storage laboratory agriculture refinery warehouse command_center] }

  def to_terrain_data_entry
    {
      id: id,
      type: type,
      status: status,
      capacity: capacity,
      occupancy: occupancy,
      power_input: power_input,
      power_output: power_output,
      material: material,
      production_rate: production_rate,
      queue: queue || []
    }
  end
end
```

---

## 2. Visual Treatment Rules for Surface View (Layer 4)

### 2.1 Settlement Tile Rendering Order

Settlement overlay renders **on top of** Layers 0-3 (terrain/liquid/biomes/resources) but **below** Layer 5 (units). This is the planned Layer 4 slot.

```
Render order:
  Layer 0: Elevation (base terrain color/sprite)
  Layer 1: Liquid (water overlay)
  Layer 2: Biomes (biome color/sprite)
  Layer 3: Resources (yellow tint)
  ── SETTLEMENT OVERLAY (NEW LAYER 4) ──
  Layer 4: Settlement markers/icons
  ── UNITS ──
  Layer 5: Units/Structures (on top of everything)
```

### 2.2 Visual Distinction Rules

**Rule 1**: Settlement tiles render with a **faction-colored border glow** on the tile edge.

```javascript
// Pseudocode — NOT implementation, just design spec
function drawSettlementOverlay(col, row, settlement, scale) {
  const x = col * TILE_SIZE * scale + offsetX;
  const y = row * TILE_SIZE * scale + offsetY;
  const tileSize = TILE_SIZE * scale;

  // Faction color border (2px glow)
  ctx.strokeStyle = factionColor(settlement.faction_id);
  ctx.lineWidth = 2;
  ctx.shadowColor = factionColor(settlement.faction_id);
  ctx.shadowBlur = 8;
  ctx.strokeRect(x + 1, y + 1, tileSize - 2, tileSize - 2);
  ctx.shadowBlur = 0; // reset

  // Small dome icon in corner (top-right)
  drawDomeIcon(x + tileSize - 8, y + 4, scale);

  // Optional: population badge (bottom-left) if scale > 1.5
  if (scale > 1.5 && settlement.population > 0) {
    drawPopulationBadge(x + 2, y + tileSize - 10, settlement.population, scale);
  }
}
```

**Rule 2**: Settlement icon is a **small dome/tower sprite** (8x8px at scale=1), rendered in the corner of the tile.

**Rule 3**: Faction color determines border glow:

| Faction | Color | Hex |
|---|---|---|
| Earth | Blue | #4A90D9 |
| Mars | Red | #D94A4A |
| Luna | Gray | #8B8B8B |
| Ceres | Orange | #D9A04A |
| Independent | Green | #4AD94A |

**Rule 4**: Settlement name label is **toggle-able** (default off to reduce clutter). When enabled, renders in small white text at tile center with dark background.

### 2.3 Hover/Click Behavior

**Hover**: Show tooltip with settlement name + building count:
```
Jezero Base
Habitat + Mining Station (2 buildings)
Pop: 127/200 | Power: 85/62 ✓
```

**Click**: Highlight tile with brighter glow, show detail panel (3-5 line text box):
```
Jezero Base [Earth]
Population: 127 / 200
Power: 85 produced, 62 consumed (+23 surplus)
Main: Mining regolith @ 100 units/hr
[Enter TerrainForge] [Manage]
```

### 2.4 Layer Toggle

Add "Show Settlements" toggle to the existing layer toggle system:

```javascript
// In surface_view.js visibleLayers Set (add 'settlements'):
window.SurfaceView.visibleLayers = new Set(['terrain', 'liquid', 'biomes', 'settlements']);

// Default: settlements ON (same as terrain/liquid/biomes)
// Toggle independent from unit layer
```

---

## 3. Navigation Flow: Surface View ↔ TerrainForge

### 3.1 Camera State Transition Design

**Critical**: TerrainForge uses the **same canvas, same rendering pipeline, same sprites** as Surface View. Only camera state changes.

```
Surface View (scale=1.0)
  │
  ├─ User double-clicks settlement tile at (col=50, row=42)
  │
  ▼
Camera transition (smooth animation, ~300ms)
  scale: 1.0 → 50.0
  offsetX: current → canvas.width/2 - (50*32)*50/2
  offsetY: current → canvas.height/2 - (42*32)*50/2
  │
  ▼
TerrainForge Detail View (scale=50.0)
  Single tile fills ~90% of viewport
  Buildings rendered at full scale
```

### 3.2 Zoom Level Specifications

| Zoom State | Scale | Tile Size on Screen | Use Case |
|---|---|---|---|
| Planetary transition | ~0.3x | ~10px | See entire surface region |
| Default Surface View | 1.0x | 32px | Standard tactical view |
| Mid zoom | 2-5x | 64-160px | Closer terrain inspection |
| Settlement approach | 8-15x | 256-480px | Approaching settlement tile |
| TerrainForge entry | 10-100x | 320-3200px | Single tile detail view |
| Building close-up | 100-200x | 3200-6400px | Individual building details |

### 3.3 Navigation Entry Points

**Entry to TerrainForge**:
1. Double-click settlement tile in Surface View
2. Click "Enter TerrainForge" button in settlement info panel
3. Right-click settlement → context menu → "Manage Settlement"

**Exit from TerrainForge**:
1. Press ESC key
2. Click "Back to Map" button
3. Zoom out below threshold (scale < 8x)

### 3.4 Context Preservation Rules

| Transition | Camera Tracking | Time Behavior | Data Sync |
|---|---|---|---|
| Surface → TerrainForge | Freeze at settlement tile center | Pause (or continue in background) | Buildings loaded from terrain_data.settlements[N].buildings |
| TerrainForge → Surface | Resume at frozen position | Resume | Building parameter changes persisted to terrain_data |

---

## 4. Multiple Settlement Handling

### 4.1 Visual Conflict Prevention

**Rule**: Each settlement tile has its own independent faction-colored border glow. When settlements are adjacent (neighboring tiles), borders do not overlap — each renders within its own tile bounds.

**Rule**: If two settlements exist on the same celestial body, their icons and labels are positioned independently. No visual conflict occurs because:
- Each settlement is confined to one grid cell
- Faction colors differentiate visually
- Labels are toggle-able (can be disabled)

### 4.2 Settlement Count per Region

Expected range: **3-5 settlements per Surface View region** (50×50 tiles). This is a design constraint, not a hard limit — the system supports any number.

---

## 5. Building Definition Schema

### 5.1 Building Types and Required Fields

| Building Type | Required Fields | Optional Fields |
|---|---|---|
| `habitat` | capacity, occupancy | power_input, status |
| `mining_station` | material, production_rate | power_input, queue, status |
| `power_plant` | fuel_type, power_output | power_input, status |
| `fabricator` | input_material, output_item | power_input, production_rate, queue |
| `storage` | capacity, current_content | power_input |
| `laboratory` | research_focus | power_input, status |
| `agriculture` | crop_type, yield_rate | power_input, water_input |
| `refinery` | input_material, output_material | power_input, production_rate |
| `warehouse` | capacity, current_content | power_input |
| `command_center` | command_radius | power_input, status |

### 5.2 Building Status Values

```
operational — functioning normally
idle — powered off, no active tasks
maintenance — under repair/maintenance
damaged — partially non-functional
destroyed — no longer usable
```

### 5.3 Building Queue Schema

```json
{
  "queue": [
    {
      "item": "refined_iron",
      "quantity": 500,
      "eta_turns": 12,
      "priority": "normal"
    }
  ]
}
```

---

## 6. Integration Points with Existing Code

### 6.1 TerrainDataBuilder Changes (Design Only)

In `TerrainDataBuilder#build`, add:
```ruby
settlements: extract_settlements(terrain_map_data)
```

In `TerrainDataBuilder#extract_settlements` (NEW method):
```ruby
def extract_settlements(terrain_map_data)
  return [] unless celestial_body.settlements.any?

  celestial_body.settlements.map do |settlement|
    settlement.to_terrain_data_entry
  end
end
```

### 6.2 surface_view.js Changes (Design Only)

**New property**:
```javascript
visibleLayers: new Set(['terrain', 'liquid', 'biomes', 'settlements']),
showSettlements: true,
```

**New layer building** (in `_buildLayers`):
```javascript
// ── Settlements (Layer 4) ───────────────────────────────
if (td.settlements && Array.isArray(td.settlements)) {
  this.layers.settlements = td.settlements;
}
```

**New render step** (in `renderGrid`, after Layer 3, before Layer 5):
```javascript
// ── Layer 4: Settlement overlays ────────────────────────
if (this.visibleLayers.has('settlements') && this.layers.settlements) {
  this.drawSettlementOverlays();
}
```

**New method**:
```javascript
drawSettlementOverlays: function() {
  // For each settlement in this.layers.settlements:
  //   - Check if tile is visible in viewport
  //   - Draw faction-colored border glow
  //   - Draw dome icon in corner
  //   - Draw population badge if scale > 1.5
}
```

### 6.3 Geosphere Model Changes (Design Only)

No migration needed — `terrain_map` is JSONb. Settlement data stored as:
```ruby
geosphere.terrain_map['settlements'] = settlements_array
```

---

## 7. Stop Conditions

The following conditions would invalidate this design and require escalation:

1. **Existing terrain_data already has settlement tile data** — verified it does NOT (no `settlements` key in current TerrainDataBuilder output)
2. **surface_view.js rendering pipeline changed significantly** — verified the existing layer system (Layers 0-5) with RAF loop and viewport culling is still the active architecture
3. **Three-layer views architecture superseded** — verified three_layer_views.md (last updated 2026-07-27) is current

---

## 8. What This Design Does NOT Cover (Future Tasks)

- ❌ Ruby model implementation (Settlement, Building models)
- ❌ Database migrations for settlements table
- ❌ surface_view.js rendering code (Layer 4 implementation)
- ❌ Camera zoom animation code
- ❌ TerrainForge building configuration UI
- ❌ Drag-and-drop building placement
- ❌ Pipeline/road connection system in TerrainForge
- ❌ Right-click context menu for settlements
- ❌ Keyboard navigation between settlements

---

## 9. Success Criteria

This design task is complete when:

- [x] Settlement tile data structure defined and documented (Section 1)
- [x] Integration with existing terrain_data JSON specified (Section 1.1, 6.1)
- [x] Visual treatment rules for Surface View overlay defined (Section 2)
- [x] Navigation flow between Surface View and TerrainForge specified (Section 3)
- [x] Building definition schema documented (Section 5)
- [x] Multiple settlement handling strategy defined (Section 4)
- [x] Integration points with existing code identified (Section 6)
- [x] Stop conditions verified (Section 7)

---

## Appendix A: Comparison — Current terrain_data vs. Proposed

| Field | Current | Proposed Addition |
|---|---|---|
| `elevation` | ✅ 2D array | Unchanged |
| `biomes` | ✅ 2D array | Unchanged |
| `resources` | ✅ 2D array | Unchanged |
| `width`/`height` | ✅ integers | Unchanged |
| `unit_grid` | ✅ 2D array | Unchanged |
| **`settlements`** | **❌ absent** | **✅ NEW: array of settlement objects** |
| `quality_score` | ✅ integer | Unchanged |
| `generation_method` | ✅ string | Unchanged |

## Appendix B: File Locations Referenced

| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/terrain_data_builder.rb` | Current terrain_data export | Verified — no settlements key |
| `galaxy_game/app/assets/javascripts/surface_view.js` | Existing rendering pipeline | Verified — Layers 0-5, RAF loop, viewport culling |
| `galaxy_game/app/models/celestial_bodies/spheres/geosphere.rb` | terrain_map storage (JSONb) | Verified — schemaless, no migration needed |
| `docs/architecture/three_layer_views.md` | Three-layer architecture spec | Verified — current as of 2026-07-27 |
| `galaxy_game/db/migrate/...add_terrain_map_to_geospheres.rb` | terrain_map column definition | Verified — jsonb type |

---

**DESIGN COMPLETE.** Ready for implementation task that consumes this document.
