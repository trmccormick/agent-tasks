# Research: Does Elevation Feed Into Biome/Temperature Classification?

**Date**: 2026-07-28  
**Agent**: Research Agent (Qwen)  
**Task**: 2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md  

---

## Findings Summary

**No — elevation does NOT currently influence biome classification.** Biome assignment is driven solely by latitude-derived temperature with no elevation-based temperature adjustment (no lapse rate applied).

---

## Detailed Findings

### 1. Elevation Influence: NO

Elevation data exists in the codebase (stored as `elevation` in tile/terrain data) but is **never used** in biome classification logic. There is no lapse rate, no altitude-based temperature correction, and no elevation-aware biome categories computed dynamically.

### 2. Variables Used for Biome Classification

Biome assignment flows through these variables:

- **Latitude** — primary driver, determines base temperature zone
- **Temperature** (latitude-derived) — used directly in biome classification thresholds
- **Precipitation** — checked in some biome classification logic (e.g., Köppen-style variants)
- **Material/terrain type** — derived from biome but not a classifier input

The classification chain is: `latitude → temperature → biome category → terrain family/type → tile key`

### 3. Lapse Rate Applied: NONE

No lapse rate (-6.5°C/km or any variant) is implemented anywhere in the terrain/biome pipeline. High-elevation tiles at tropical latitudes render with the same biome classification as low-elevation tiles at the same latitude.

### 4. Data Structures Carrying Elevation

**JavaScript (surface_view.js)**:
- Per-tile elevation data IS available in the tile dataset (stored in the JSON asset)
- `_selectTerrainFamily()` and `_selectTerrainType()` do NOT reference elevation
- `_biomeTileKey()` does NOT use elevation — it uses latitude-derived temperature only
- Elevation is present in the data but ignored for biome/terrain decisions

**Ruby (terra_sim services)**:
- Elevation exists as a field on tile/terrain models
- No Ruby service passes elevation to biome classification functions
- Climate/atmosphere models do not incorporate altitude

### 5. Existing Elevation-Based Biome Categories

None are computed dynamically. Any "alpine", "montane", or "highlands" biome labels in the codebase are:
- **Hardcoded** in tileset definitions (static entries)
- **Not derived** from elevation data at runtime
- Essentially placeholder categories that would only appear if manually assigned

---

## Files Examined

| File | What Was Checked | Key Finding |
|------|-----------------|-------------|
| `galaxy_game/app/assets/javascripts/surface_view.js` | `_selectTerrainFamily()`, `_selectTerrainType()`, `_biomeTileKey()` | No elevation references in any biome/terrain decision logic |
| `galaxy_game/app/assets/javascripts/biome_renderer.js` | BIOME_NAMES constant, biome selection | Biome names are static; no elevation-aware mapping |
| `galaxy_game/app/services/terra_sim/` (grep) | Elevation + biome/terrain/climate references | No Ruby service uses elevation for biome classification |
| Tile JSON asset (`galaxy_game/app/assets/javascripts/galaxy_game_tileset.json`) | Tile data structure | Elevation field exists but is not consumed by biome logic |

---

## Impact Assessment

### What This Means for Downstream Work

1. **Tropical high-elevation tiles render as rainforest** — without elevation adjustment, a tile at 0° latitude with 4000m elevation would still classify as tropical/rainforest biome, not alpine tundra.

2. **CanonicalMapService does NOT need elevation-aware aliases** — since no elevation logic exists, adding it would be a new feature, not a fix for missing functionality.

3. **Biome visual variety design (item 2)** — if elevation-based biome variants are desired, this requires implementing lapse rate logic in BOTH the Ruby terrain pipeline AND the JavaScript rendering layer.

4. **Canonical tile set expansion** — if elevation creates new biome categories (alpine tundra, montane forest, etc.), the tileset would need additional art variants for each latitude band × elevation zone combination.

### Recommended Next Steps (if elevation-aware biomes are desired)

1. Implement lapse rate in Ruby terrain pipeline: `adjusted_temp = base_temp - (0.0065 * elevation_m)`
2. Add elevation zone thresholds (e.g., 0-500m, 500-1500m, 1500-3000m, 3000-4500m, 4500m+)
3. Create biome classification matrix: latitude band × elevation zone → biome category
4. Update JavaScript rendering to use the new classification
5. Add art variants for newly created biome categories

---

## Follow-up Items Identified

- None — this is a standalone research task. Any follow-up work requires a new task file.
