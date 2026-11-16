# A5 — Surface Sprite Consumption Research

**Task**: A5 (HIGH-RESEARCH-ASSET-UI-A5)  
**Status**: completed  
**Date**: 2026-10-07  
**Type**: research  

---

## Executive Summary

This research determined how the surface renderer currently consumes sprites and animation frames. The key finding is that **the surface rendering pipeline exists but has no functional sprite loading** — `KNOWN_ASSETS` is an empty Set, so all terrain tiles render as elevation colours with no sprites. BiomeRenderer has a working PNG fetch + fallback pattern for biome tiles (12 individual 142×142 PNGs), but this is for terrain biomes, not unit assets. No sprite sheets, animation frames, or damage states are modeled or consumed anywhere in the codebase. The RH-400 surface sprite (`rh400_sprite_test.png`) exists as a file but is never referenced by any renderer code path.

---

## 1. Surface Renderer Code Paths

### SurfaceView.js — Main Surface Renderer

**File**: `galaxy_game/app/assets/javascripts/surface_view.js`

#### Architecture
```javascript
window.SurfaceView = {
  TILE_SIZE: 32,  // Fixed 32px tile size (Civ4 feel)
  
  /* Terrain Pipeline (property-driven asset lookup) */
  VARIANT_COUNTS: {
    'regolith':  { plain: 1, rocky: 1, crater: 1 },
    'dust':      { plain: 1, dunes: 1, rocky: 1  },
    'frozen':    { plain: 1, mountains: 1        },
    'volcanic':  { plain: 1, lava: 1             },
    'temperate': { plain: 1, mountains: 1        },
    'tropical':  { plain: 1, hills: 1            },
    'barren':    { plain: 1, rocky: 1            },
  },
  
  KNOWN_ASSETS: new Set(),           // ← EMPTY! No assets registered.
  _terrainImageCache: null,          // Lazy-loaded Map
  
  /* Layers (4-layer pipeline) */
  layers: {
    elevation: null,   // Layer 0 — always shown
    liquid:    null,   // Layer 1 — hydrosphere bathtub
    biomes:    null,   // Layer 2 — biome colour map
    resources: null    // Layer 3 — optional overlay
  },
  
  /* Unit layer (Layer 5) */
  showUnits: false,
  layers.units: { grid, width, height }  // unit_grid from terrain_data
}
```

#### Terrain Tile Pipeline
```javascript
_terrainTileRef: function(planetData, biome, elev, col, row) {
  const family = this._selectTerrainFamily(planetData);
  const type   = this._selectTerrainType(family, biome, elev);
  const variant = this._selectVariant(family, type, col, row);
  const path   = this._buildTilePath(family, type, variant);
  return { family, type, variant, path };
}

_buildTilePath: function(family, type, variant) {
  const base = '/assets';
  const paths = [
    `${base}/${family}/${type}_${variant}.png`,
    `${base}/${family}/${type}_01.png`,
    `${base}/${family}/plain_01.png`,
  ];
  for (const p of paths) {
    if (this._assetExists(p)) return p;  // ← always false!
  }
  return null;  // ← always falls through to colour rendering
}

_assetExists: function(path) {
  return this.KNOWN_ASSETS.has(path);  // ← KNOWN_ASSETS is empty Set()
}
```

**Critical finding**: `KNOWN_ASSETS` is an empty `Set()`. The `_assetExists()` check always returns `false`. The fallback chain (`type_${variant}.png` → `type_01.png` → `plain_01.png`) never finds any known assets. All terrain tiles render as elevation colours, not sprites.

#### Biome Tile Key Resolution
```javascript
_biomeTileKey: function(elev, biome) {
  const b = (biome || '').toLowerCase().trim();
  
  // Single-character grid codes from automatic_terrain_generator.rb
  const charMap = {
    ' ': 'ocean', ':': 'ocean', '.': 'ocean',
    'a': 'tundra', 't': 'tundra',
    'f': 'forest', 'g': 'grasslands', 'p': 'plains',
    'd': 'hot_desert', 'j': 'jungle', 's': 'swamp'
  };
  if (b.length === 1) return charMap[b] || null;
  
  // Geological features — no sprite, use elevation colour
  if (b.includes('crater'))  return null;
  if (b.includes('regolith')) return null;
  if (b === 'maria' || b === 'mare') return null;
  // ... more geological terms
  
  // Full biome name matching
  const biomeMap = {
    'hot_desert': 'hot_desert',
    'cold_desert': 'cold_desert',
    'polar_desert': 'polar_desert',
    'forest': 'forest',
    'grasslands': 'grasslands',
    'jungle': 'jungle',
    'tropical_jungle': 'tropical_jungle',
    'savanna': 'savanna',
    'ocean': 'ocean',
    'plains': 'plains',
    'swamp': 'swamp',
    'tundra': 'tundra'
  };
  return biomeMap[b] || null;
}
```

**Key finding**: Biome tiles are resolved via a hardcoded string-to-string mapping that maps terrain grid values to BiomeRenderer PNG keys. This is for **terrain biomes**, not unit assets. No unit sprite lookup exists in SurfaceView.js.

#### Unit Layer Rendering
```javascript
// In _buildLayers():
if (td.unit_grid && Array.isArray(td.unit_grid) && td.unit_grid.length > 0) {
  this.layers.units = {
    grid: td.unit_grid,
    width: td.unit_grid[0]?.length || 0,
    height: td.unit_grid.length
  };
}
```

**Key finding**: The unit layer stores a grid of unit identifiers (strings/IDs) but **no sprite loading code exists**. The unit grid is stored as data but there is no code that resolves unit IDs to sprite assets or renders them on the surface.

---

## 2. BiomeRenderer — Sprite Loading Pattern (Terrain Only)

**File**: `galaxy_game/app/assets/javascripts/biome_renderer.js`

### How It Works
```javascript
class BiomeRenderer {
  static TILE_SIZE = 142;
  static CONFIG_PATH = '/tilesets/galaxy_game/biomes.json';
  
  async init() {
    // 1. Fetch biomes.json config
    const resp = await fetch(BiomeRenderer.CONFIG_PATH);
    this.config = await resp.json();
    
    const assetPath = this.config.asset_path || '/api/assets/biomes/';
    const biomeEntries = Object.entries(this.config.biomes);
    
    // 2. Seed fallback colours from config
    biomeEntries.forEach(([name, meta]) => {
      this.fallbacks.set(name, meta.color_fallback || '#1a1a2e');
    });
    
    // 3. Load + scale all PNGs concurrently
    await Promise.all(
      biomeEntries.map(([name, meta]) =>
        this._loadAndScale(name, `${assetPath}${meta.file}`)
      )
    );
  }
  
  draw(ctx, biomeName, x, y, rotation = 0) {
    const key = (biomeName || '').toLowerCase().trim();
    if (this.tiles.has(key)) {
      ctx.imageSmoothingEnabled = false;
      ctx.drawImage(this.tiles.get(key), x, y, size, size);
    } else {
      // Solid-colour fallback when PNG is missing
      ctx.fillStyle = this.fallbacks.get(key) || '#1a1a2e';
      ctx.fillRect(x, y, size, size);
    }
  }
}
```

### Tileset Configuration
**File**: `galaxy_game/public/tilesets/galaxy_game/biomes.json`
```json
{
  "version": "1.0.0",
  "tile_size": 142,
  "asset_path": "/assets/biomes/",
  "biomes": {
    "hot_desert": { "file": "hot_desert.png", "color_fallback": "#d4a017", ... },
    "cold_desert": { "file": "cold_desert.png", "color_fallback": "#8a7a6a", ... },
    // ... 12 biome entries total
  }
}
```

**Key finding**: BiomeRenderer loads 12 individual PNGs from `/assets/biomes/{biome_name}.png`. Each biome has a solid-colour fallback. This is the **only functional sprite loading pattern** in the surface rendering pipeline, and it's for terrain biomes only — not unit assets.

---

## 3. Sprite Asset Identification and Resolution

### Expected Filename/Path Conventions
From SurfaceView.js `VARIANT_COUNTS`:
```javascript
// Expected path pattern: /assets/{family}/{type}_{variant}.png
// e.g., /assets/regolith/plain_01.png, /assets/frozen/mountains_02.png
```

**No unit sprite path convention exists.** The terrain tile pipeline uses `{family}/{type}_{variant}.png` for terrain tiles only. There is no analogous convention for unit/surface sprites.

### RH-400 Surface Sprite — Actual File
| Property | Value |
|----------|-------|
| **Path** | `data/images/catalog/crafts/ground/rh400_sprite_test.png` |
| **Dimensions** | 1536 × 1024 |
| **Color** | RGBA (8-bit, transparent background) |
| **Role** | Sprite test (experimental) |
| **Referenced by any code?** | No |

### How the Renderer Would Resolve a Sprite Path
**Current state**: It doesn't. There is no sprite resolution code for unit assets in SurfaceView.js or BiomeRenderer. The terrain tile pipeline (`_buildTilePath`) only handles terrain families (regolith, dust, frozen, volcanic, temperate, tropical, barren) — not unit assets.

---

## 4. Animation Frames, Damage States, Sprite Sheets — Current State

### Animation Frames
**Status**: NOT MODELED ANYWHERE in the codebase.
- SurfaceView.js has no animation state machine
- BiomeRenderer has no frame sequencing
- No animation frame files exist for RH-400 or any other asset
- The Visual Definition specifies `animation_profile: "vehicles_status_lights"` but this is never consumed

### Damage States
**Status**: NOT MODELED ANYWHERE in the codebase.
- No damage state sprites exist
- No damage state switching logic exists
- The prompt template mentions "damage-state assets" as a Pass 1 output but no implementation exists

### Sprite Sheets
**Status**: NOT MODELED for unit assets.
- `chromakey_spritesheet.py` exists at `galaxy_game/chromakey_spritesheet.py` — this is for terrain tile extraction, not unit sprites
- No unit sprite sheet files exist
- The Visual Definition specifies `sprite_sheet` as one of 6 render profiles but no sprite sheet file exists

### What the Prompt Template Says (Pass 1)
From `rh400-prompt-template.md`:
> "Pass 1 — 3D/Orthographic Render (Transparent Background)"
> "Purpose: Feeds surface sprites, animation frames, and damage-state assets."
> "Request multiple angles: front elevation, rear elevation, side profile, isometric view."

**Gap**: The prompt template defines what Pass 1 SHOULD produce, but no code implements this. No sprite sheets, animation frames, or damage states have been generated or consumed.

---

## 5. Transparent-Background Asset Expectations

### What the Code Expects
- **BiomeRenderer**: Loads PNGs with transparent backgrounds (via fetch + Image loading). Falls back to solid colour if PNG is missing. Uses `ctx.imageSmoothingEnabled = false` for crisp pixel rendering.
- **SurfaceView.js terrain tiles**: Expects `/assets/{family}/{type}_{variant}.png` — but `KNOWN_ASSETS` is empty so no assets are ever loaded.

### What RH-400 Sprite Test Provides
- `rh400_sprite_test.png` is RGBA (transparent background) — format matches the expectation
- However, it's a single image at 1536×1024 — not a sprite sheet or individual frame
- No code path references this file

### Key Finding: Transparent Backgrounds Are Expected but Not Implemented
The surface rendering pipeline expects transparent-background assets (BiomeRenderer loads PNGs with alpha channels, SurfaceView.js composites layers). However, the unit asset loading path is entirely unimplemented. The terrain tile pipeline has a broken resolution mechanism (empty `KNOWN_ASSETS`), and there is no separate unit sprite loading path.

---

## 6. RH-400 as Evidence of Current Surface Representation Contract

### What Exists for RH-400
| Asset | Path | Format | Referenced by Renderer? |
|-------|------|--------|------------------------|
| Catalog render (concept) | `data/images/catalog/crafts/ground/rh400_concept.png` | RGB, 1402×1122 | No |
| Catalog render (alternate) | `data/images/catalog/crafts/ground/rh400_regolith_harvesting_rover.png` | RGB, 1536×1024 | No |
| Sprite test | `data/images/catalog/crafts/ground/rh400_sprite_test.png` | RGBA, 1536×1024 | No |

### What the Visual Definition Says RH-400 Needs
From `VEHICLE_HARVESTER_ROVER_RH400.json`:
- **Render profiles**: inventory_icon, catalog_render, engineering_render, blueprint, exploded_view, sprite_sheet (6 total)
- **Camera profiles**: front_isometric, rear_isometric, side_profile, top_down (4 total)
- **Animation profile**: vehicles_status_lights
- **Complexity levels**: 0–5

### The Gap
The Visual Definition specifies 6 render profiles and 4 camera angles. Only 3 files exist (2 catalog renders + 1 sprite test). None are referenced by any renderer code path. No animation frames, damage states, or sprite sheets exist.

---

## 7. How Current Consumption Compares with Pass 1 Surface-Output Structure

### Prompt Template Pass 1 Specification
```
[Context] Galaxy Game: industrial space settlement simulation...
[Asset Name] {{name}} ({{id}})
[Physical Specs] Length: {{length_m}}m | Width: {{width_m}}m | Height: {{height_m}}m | Mass: {{empty_mass_kg}}kg

Generate a transparent-background (alpha channel) image of this asset.
Request multiple angles: front elevation, rear elevation, side profile, isometric view.
Each angle must be fully framed with consistent padding — no cropping.
Lighting: cool, even, diffused studio lighting. No baked ground or environment shadows.
Style: hard sci-fi industrial, grounded engineering realism.
```

### Current Reality
| Requirement | Status | Evidence |
|------------|--------|----------|
| Transparent background | Partially met (sprite_test.png is RGBA) | File inspection |
| Multiple angles | NOT implemented | No multi-angle files exist |
| Consistent padding | Unknown (single file, no comparison possible) | N/A |
| Cool/even lighting | Unknown (no generation pipeline exists) | N/A |
| Hard sci-fi industrial style | Defined in Visual Definition but not enforced by code | docs/ only |
| Sprite sheet output | NOT implemented | No sprite sheet files exist |
| Animation frames | NOT implemented | No animation frame files exist |
| Damage states | NOT implemented | No damage state files exist |

### Key Finding: Pass 1 Is a Specification, Not an Implementation
The prompt template defines what Pass 1 SHOULD produce. The current codebase has no implementation of this specification. The surface rendering pipeline exists (SurfaceView.js + BiomeRenderer) but unit asset loading is entirely unimplemented.

---

## 8. Integration Gaps Identified

### Gap 1: No Unit Sprite Loading Path
- SurfaceView.js terrain tile pipeline only handles terrain families
- No code resolves unit IDs to sprite assets
- `KNOWN_ASSETS` is empty — no assets are registered for loading

### Gap 2: No Animation State Machine
- No animation frame sequencing in SurfaceView.js or BiomeRenderer
- No state-based sprite switching logic
- Visual Definition's `animation_profile: "vehicles_status_lights"` is never consumed

### Gap 3: No Damage State System
- No damage state sprites exist
- No damage state switching logic exists
- No code path for rendering damaged variants of assets

### Gap 4: Sprite Sheet Not Implemented
- Visual Definition specifies `sprite_sheet` as a render profile
- No sprite sheet files exist for RH-400 or any other asset
- `chromakey_spritesheet.py` is for terrain tiles, not unit sprites

### Gap 5: Path Resolution Mismatch (Reiterated from A2/A4)
- RH-400 sprite test exists at `data/images/catalog/crafts/ground/rh400_sprite_test.png`
- No code path references this file
- The terrain tile pipeline expects `/assets/{family}/{type}_{variant}.png` — a different convention entirely

### Gap 6: BiomeRenderer Pattern Is Reusable but Not Applied to Units
- BiomeRenderer's PNG fetch + solid-colour fallback pattern is functional for terrain biomes
- This pattern could be adapted for unit sprites, but no such adaptation exists
- The tileset JSON config pattern (biomes.json) could be extended for unit assets

---

## 9. What A5 Unlocks

### B3 (Surface Asset Representation Contract) Can Now:
- Define the surface asset representation contract grounded in the actual SurfaceView.js architecture
- Specify how sprite loading should work (adapting BiomeRenderer's pattern for unit assets)
- Define animation state handling requirements (currently entirely absent)
- Specify damage state representation requirements (currently entirely absent)
- Establish the gap between current terrain tile pipeline and required unit sprite pipeline

### C5 (Surface Asset Integration) Can Now:
- Know that RH-400 surface sprite exists as a test file but is not production-ready
- Know that no animation frames or damage states exist for any asset
- Know that the terrain tile pipeline convention (`/assets/{family}/{type}_{variant}.png`) differs from catalog conventions
- Design integration that bridges the gap between existing BiomeRenderer pattern and unit sprite requirements

---

## 10. Files Referenced

| File | Purpose |
|------|---------|
| `galaxy_game/app/assets/javascripts/surface_view.js` | SurfaceView.js — main surface renderer (KNOWN_ASSETS empty, broken tile resolution) |
| `galaxy_game/app/assets/javascripts/biome_renderer.js` | BiomeRenderer — PNG fetch + solid-colour fallback pattern (functional for terrain biomes only) |
| `galaxy_game/public/tilesets/galaxy_game/biomes.json` | Tileset config — 12 biome PNGs at `/assets/biomes/` |
| `data/images/catalog/crafts/ground/rh400_sprite_test.png` | RH-400 sprite test (RGBA, 1536×1024) — not referenced by any code |
| `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` | Visual Definition — animation_profile, render_profiles, camera_profiles |
| `docs/reference/asset-generation/rh400-prompt-template.md` | Three-pass output structure (Pass 1 = transparent surface outputs) |
| A2 synthesis report | Storage locations and path mismatch evidence |
| A3 synthesis report | RH-400 asset family mapping, 6-to-3 gap |
