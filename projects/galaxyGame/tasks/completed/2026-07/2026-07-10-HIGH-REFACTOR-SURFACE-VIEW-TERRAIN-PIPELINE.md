---
status: active
priority: HIGH
type: refactor
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-10-HIGH-REFACTOR-SURFACE-VIEW-TERRAIN-PIPELINE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-10-HIGH-REFACTOR-SURFACE-VIEW-TERRAIN-PIPELINE.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-07-10-HIGH-REFACTOR-SURFACE-VIEW-TERRAIN-PIPELINE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-07-10-HIGH-REFACTOR-SURFACE-VIEW-TERRAIN-PIPELINE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Refactor Surface View Terrain Rendering Pipeline — Property-Driven Asset Lookup
**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-07-10

## Prerequisites

- Read: `galaxy_game/app/assets/javascripts/surface_view.js` — current renderer (actual path, NOT `public/javascripts/`)
- Read: `docs/architecture/terrain/generation_and_rendering.md` — terrain arch
- Read: `galaxy_game/public/assets/` — existing asset directory structure
- Understand: Physical properties drive rendering, never planet name
- Understand: Five distinct layers — geology, hydrosphere, biomes, infrastructure, units
- Understand: biomes/ contains LIVING layer tiles only (Earth-class worlds)
  terrain family folders (regolith/, dust/, frozen/ etc.) are geological tiles

## Problem Statement

`surface_view.js` currently looks up terrain tiles by biome name string
against a flat `public/assets/biomes/` folder. This is Earth-centric and
breaks for procedurally generated worlds. The terrain family directories
(`regolith/`, `dust/`, `frozen/`, `volcanic/` etc.) already exist under
`public/assets/` but are not connected to the renderer.

The renderer needs to select terrain tiles based on physical properties
of the celestial body, not by planet name or biome string, so that any
procedurally generated world can be rendered correctly from the same
asset library.

## Acceptance Criteria

- [ ] `_biomeTileKey` replaced with `_terrainTileRef(planetData, biome, elev)`
      that returns `{family, type, variant}` based on physical properties
- [ ] Renderer builds asset path: `assets/{family}/{type}_{variant}.png`
- [ ] Variant selected deterministically using world seed (not random)
- [ ] Fallback chain implemented:
        1. `assets/{family}/{type}_{variant}.png`
        2. `assets/{family}/{type}_01.png`
        3. `assets/{family}/plain_01.png`
        4. elevation colour (existing behaviour)
- [ ] `biomes/` folder lookup preserved as separate path for living layer
      (only used when `hasBiomes === true`)
- [ ] No planet names anywhere in tile selection logic
- [ ] Existing Earth/biome worlds render correctly after refactor
- [ ] Luna renders using `regolith/` family tiles when assets present
- [ ] Console logs family/type/variant selection for debugging

## Implementation Notes

### Family Selection Logic

Map physical properties → terrain family. Use these rules in order:

```javascript
_selectTerrainFamily(planetData) {
  const temp     = planetData.surface_temperature;
  const pressure = planetData.atmosphere_pressure || 0;
  const ironOx   = planetData.crust_iron_oxide_percentage || 0;
  const geoAct   = planetData.geological_activity || 0;
  const hasLife  = planetData.has_biosphere || false;
  const liquid   = planetData.hydrosphere_liquid_type || null;

  // Order matters — check most specific first
  if (geoAct > 80 && temp > 700)        return 'volcanic';
  if (temp < -100 && liquid === 'CH4')  return 'frozen';   // Titan-type
  if (temp < -50)                        return 'frozen';
  if (ironOx > 10 && pressure < 0.1)   return 'dust';     // Mars-type
  if (pressure < 0.001 || !pressure)   return 'regolith'; // Airless
  if (hasLife && temp > -10 && temp < 50) return 'temperate';
  if (temp > 30 && pressure > 0.5)     return 'tropical';
  return 'barren'; // default fallback
}
```

### Type Selection Logic

Within a family, select type from biome string or elevation:

```javascript
_selectTerrainType(family, biome, elev) {
  // Biome string takes precedence when present
  const typeMap = {
    'mountains': 'mountains', 'hills': 'hills',
    'crater': 'crater', 'plains': 'plain',
    'rocky': 'rocky', 'dunes': 'dunes',
    'canyon': 'canyon', 'glacier': 'glacier',
    'lava': 'lava', 'ash': 'ash'
  };
  if (biome && typeMap[biome]) return typeMap[biome];

  // Fall back to elevation hints
  if (elev > 3500) return 'mountains';
  if (elev > 1500) return 'hills';
  if (elev < 0)    return null; // hydrosphere layer handles this
  return 'plain';
}
```

### Variant Selection

Use world seed for deterministic variant selection:

```javascript
_selectVariant(family, type, col, row) {
  const seed    = window.WORLD_SEED || 42;
  const maxVars = this._countVariants(family, type) || 1;
  // Simple deterministic hash from seed + position
  const hash = (seed * 31 + col * 17 + row * 13) % maxVars;
  return String(hash + 1).padStart(2, '0'); // '01', '02' etc.
}
```

### Variant Count

Track available variants per family/type. Start with a static map,
replace with dynamic discovery later:

```javascript
VARIANT_COUNTS: {
  'regolith': { plain: 1, rocky: 1, crater: 1 },
  'dust':     { plain: 1, dunes: 1, rocky: 1  },
  'frozen':   { plain: 1, mountains: 1        },
  'volcanic': { plain: 1, lava: 1             },
  'temperate':{ plain: 1, mountains: 1        },
  // expand as assets are added — no code change needed
}
```

### Fallback Chain

```javascript
_buildTilePath(family, type, variant) {
  const base = '/assets';
  const paths = [
    `${base}/${family}/${type}_${variant}.png`,
    `${base}/${family}/${type}_01.png`,
    `${base}/${family}/plain_01.png`,
  ];
  // Return first path that has a known asset, else null (use colour)
  for (const p of paths) {
    if (this._assetExists(p)) return p;
  }
  return null;
}
```

Note: `_assetExists` can use a preloaded asset manifest or try/catch on
Image load. Start with a static known-assets set, expand later.

### Preserve biomes/ Path

When `hasBiomes === true`, the existing BiomeRenderer PNG lookup against
`biomes/` folder continues unchanged. The new terrain pipeline only
applies to the geological base layer (Layer 0), not the biome overlay
(Layer 2).

```javascript
// Layer 0 — geological terrain (always)
tilePath = this._buildTilePath(family, type, variant);

// Layer 2 — biomes overlay (Earth-class only, unchanged)
if (hasBiomes) {
  tileKey = this._biomeTileKey(elev, biome); // existing method, keep as-is
  this.renderer.drawTile(ctx, tileKey, x, y, tileSize);
}
```

## Files to Modify

| File | Change |
|---|---|
| `galaxy_game/public/javascripts/surface_view.js` | Add `_selectTerrainFamily`, `_selectTerrainType`, `_selectVariant`, `_buildTilePath`. Refactor `_drawTile` to use new pipeline for Layer 0. Keep `_biomeTileKey` for Layer 2. |

## Files NOT to Modify

- Ruby backend — no changes needed
- Asset directories — no changes needed (empty folders are fine)
- `biomes/` folder contents — preserve as-is

## Verification

1. Open Surface View for Luna — should attempt `regolith/plain_01.png`,
   fall back to elevation colour if file missing. Console should show:
   `[TerrainPipeline] Luna: family=regolith type=plain variant=01 (fallback: colour)`

2. Open Surface View for Earth — biome layer still renders from `biomes/`
   folder as before. No regression.

3. Add a test tile: copy any existing `biomes/plains.png` to
   `regolith/plain_01.png`. Reload Luna — tile should render.

4. Verify variant determinism: reload page multiple times — same tiles
   appear in same positions.

## Stop Conditions — escalate to Tracy if:

- `surface_view.js` architecture requires Ruby backend changes
- BiomeRenderer integration is more complex than expected
- World seed is not available in JS context (check window.WORLD_SEED)

## Notes

- VARIANT_COUNTS static map is intentional — update it as new assets
  are added to terrain family folders. No code change needed for new
  variants, only count update.
- This task does NOT require any terrain assets to exist — fallback
  to elevation colour means the refactor is safe to ship before
  Terrain Kit 001 assets are generated.
- Terrain Kit 001 asset generation is a separate task in backlog/ui/
- components/ folder (catalog assets) is a separate pipeline entirely —
  do not touch in this task.
