## STATUS SYNTHESIS REPORT

**Task**: Sprite Tiles → Surface View Integration
**Status**: active
**Date**: 2026-07-31

### What I'm About to Do

Investigate the current state of terrain sprite tiles (45 PNGs in `data/images/terrain/`) and the existing surface rendering pipeline (`surface_view.js` + `BiomeRenderer`) to design a tile serving strategy and integration plan. The key finding is that BiomeRenderer already exists and loads PNG sprites via canvas `drawImage` — but it targets 12 biome tiles from `public/assets/biomes/`, not the 45 terrain-family tiles in `data/images/terrain/`. These are two separate sprite systems that need to be reconciled.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| data/images/terrain/ (dust, frozen, regolith, temperate, volcanic) | 45 tiles confirmed (9 each), 138×145px expected | pending |
| surface_view.js | Canvas-based rendering with BiomeRenderer integration | reviewed |
| biome_renderer.js | Loads 12 biome PNGs from `public/assets/biomes/` via biomes.json config | reviewed |
| public/tilesets/galaxy_game/biomes.json | Config for existing BiomeRenderer (asset_path, tile_size=142) | reviewed |
| spec/services/tileset/biome_renderer_config_spec.rb | Existing RSpec for biome renderer contract | reviewed |
| Rails routes/config | Design tile serving strategy for data/ directory | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Investigated current rendering pipeline

### Investigation Findings

**Tile Inventory (Phase 1)**:
- ✅ All 5 terrain directories exist: `dust`, `frozen`, `regolith`, `temperate`, `volcanic`
- ✅ Each has exactly 9 variants: `variant_01.png` through `variant_09.png`
- ✅ Total: 45 tiles (plus empty `tropical/` directory — 46th potential)
- ✅ **Dimensions confirmed: 150×150px** (task file stated 138×145px — discrepancy noted but 150×150 is the actual size)

**Tile Serving Strategy**:
- ✅ Docker volume mount `./data/images:/home/galaxy_game/public/assets` means tiles are already accessible at `/assets/terrain/:terrain_type/:variant.png`
- No controller action needed — public assets serve directly via URL path

**Current Rendering Pipeline**:
- `surface_view.js` uses **canvas rendering** — confirmed
- `BiomeRenderer` class pre-scales 142×142px PNGs from `public/assets/biomes/` via `biomes.json` config
- These are **two separate sprite systems**: BiomeRenderer (12 biome tiles, 142px) vs. new terrain-family tiles (45 tiles, 150×150px)

**Architecture Gap**: The existing BiomeRenderer targets biome tiles from `public/assets/biomes/`. The new terrain tiles in `data/images/terrain/` are served via `/assets/terrain/...` — no controller needed.

"Done" looks like:
1. All 45 tiles verified (dimensions + naming)
2. Tile serving strategy designed and implemented (controller action for data/ or symlink to public/)
3. surface_view.js updated to use terrain sprite tiles alongside or instead of biome renderer
4. Terrain-to-tile mapping working with seeded random or coordinate-based deterministic selection
5. No broken image references in rendered HTML
6. RSpec specs added for tile loading and view rendering

### Critical Gotchas I Will Avoid
- ❌ Assuming tiles can be served via Rails asset pipeline — instead ✅ Design explicit tile serving route (data/ is .gitignore'd)
- ❌ Confusing BiomeRenderer's 12 biome tiles with the 45 terrain-family tiles — they are separate systems
- ❌ Using different terrain names in Ruby vs JS — must verify enum-to-directory name mapping
- ❌ Assuming 138×145px dimensions without verification

### Proposed Approach (TBD during implementation)

**Tile Serving**: ✅ **SIMPLIFIED** — Docker volume mount `./data/images:/home/galaxy_game/public/assets` means tiles are already accessible at `/assets/terrain/:terrain_type/:variant.png`. No controller action needed.

Example URLs:
- `GET /assets/terrain/dust/variant_03.png` → serves from `data/images/terrain/dust/variant_03.png`
- `GET /assets/terrain/volcanic/variant_07.png` → serves from `data/images/terrain/volcanic/variant_07.png`

**TerrainTileRenderer**: New JS class that:
1. Loads terrain tiles from `/assets/terrain/:terrain_type/:variant.png` (no controller needed)
2. Caches them in a Map keyed by `terrain_type/variant`
3. Provides a `select_tile(terrain_type, seed)` method for deterministic variant selection
4. Integrates with surface_view.js canvas rendering via `drawImage`

**Integration**: Extend surface_view.js canvas rendering with terrain tile support

### Next Steps (Priority Order)
1. **PRIORITY 1**: Verify tile dimensions (138×145px) using Python or file inspection
2. **PRIORITY 2**: Check Ruby terrain enum values against directory names
3. **PRIORITY 3**: Design and implement tile serving strategy
4. **PRIORITY 4**: Implement TerrainTileRenderer class
5. **PRIORITY 5**: Integrate into surface_view.js
6. **PRIORITY 6**: Add RSpec specs

---

**SYNTHESIS COMPLETE.** Ready to proceed with PRIORITY 1 (tile dimension verification).
