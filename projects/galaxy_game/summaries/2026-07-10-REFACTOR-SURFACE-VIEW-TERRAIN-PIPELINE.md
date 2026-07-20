# 2026-07-10-REFACTOR-SURFACE-VIEW-TERRAIN-PIPELINE — Synthesis

**Date**: 2026-07-10
**Type**: refactor
**Status**: in-progress

## Summary

Refactor `surface_view.js` to use property-driven terrain tile lookup instead of biome-name-only lookup. The new pipeline maps physical properties (temperature, pressure, iron oxide, geological activity, biosphere) → terrain family → type → variant, building asset paths like `/assets/regolith/plain_01.png`.

## Key Findings

### Current Architecture
- `surface_view.js` lives at `galaxy_game/app/assets/javascripts/surface_view.js` (NOT `public/javascripts/`)
- Layer 0 (elevation): currently uses `_getElevationColor()` — pure color, no tile lookup
- Layer 1 (liquid): water depth coloring via `_getWaterColor()`
- Layer 2 (biomes): sprite overlay via `_biomeTileKey()` → BiomeRenderer PNG lookup
- The render loop draws Layer 0 as color, then optionally overlays sprites for biome tiles

### Asset State
- All 7 terrain family folders exist under `galaxy_game/public/assets/`: regolith, dust, frozen, volcanic, barren, temperate, tropical
- **All are EMPTY** — fallback to elevation colour is the default path (safe per task notes)
- Biomes folder has 10 PNG sprites for Earth-class worlds

### Implementation Plan
1. Add `VARIANT_COUNTS` static map as a property on SurfaceView
2. Add `_selectTerrainFamily(planetData)` — maps physical properties → family string
3. Add `_selectTerrainType(family, biome, elev)` — maps within-family type
4. Add `_selectVariant(family, type, col, row)` — deterministic from seed + position
5. Add `_countVariants(family, type)` — looks up VARIANT_COUNTS
6. Add `_buildTilePath(family, type, variant)` — fallback chain (3 paths → colour)
7. Add `_assetExists(path)` — static known-assets set (empty initially)
8. Refactor render loop: Layer 0 attempts tile path first, falls back to elevation color

### Design Decisions
- **No Ruby backend changes** — all logic in JS, planetData already available via `this.planetData`
- **biomes/ preserved** — `_biomeTileKey()` stays for Layer 2 sprite overlay (Earth-class only)
- **Empty VARIANT_COUNTS is fine** — defaults to 1 variant per type; update as assets are added
- **_assetExists returns false initially** — all tiles fall back to colour until assets exist
- **Console logging** — `[TerrainPipeline]` prefix for debug visibility

## Files to Modify
- `galaxy_game/app/assets/javascripts/surface_view.js` — add 6 new methods + refactor render loop

## Verification Steps (to test after implementation)
1. Open Surface View for Luna → console shows `[TerrainPipeline] Luna: family=regolith type=plain variant=01 (fallback: colour)`
2. Open Surface View for Earth → biome layer renders from biomes/ as before, no regression
3. Copy a tile to `regolith/plain_01.png` → Luna should render the tile
4. Reload page multiple times → same tiles in same positions (deterministic)
