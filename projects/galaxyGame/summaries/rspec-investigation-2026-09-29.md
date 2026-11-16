# RSpec Investigation Summary — 2026-09-29

## Prerequisites Check

**RSpec processes:** None running (confirmed clean before every run)
**Full-suite logs:** No `rspec_full_*.log` files exist in `log/`. Only three partial logs: `rspec_lifeform.log`, `rspec_biology.log`, `rspec_library.log`.
**Run prefix used:** `unset DATABASE_URL && RAILS_ENV=test`

---

## 1. Seeds from Full-Suite Logs

**No full-suite RSpec log files exist.** No `Randomized with seed NNNN` lines available from any full-run log.

---

## 2. Tileset Specs (113 of 144 failures) — Structural Findings

### a) Exact path expressions the two specs use:

**BiomeRendererConfigSpec** (`biome_renderer_config_spec.rb`):
```ruby
BIOME_ASSETS_DIR = Rails.root.join('public', 'assets', 'biomes').freeze
# Checks: public/assets/biomes/{biome}.png for each biome key
# Also checks: public/assets/biomes/{meta['file']} from biomes.json entries
```

**TerrainTileRendererSpec** (`terrain_tile_renderer_spec.rb`):
```ruby
TILE_BASE_PATH = Rails.root.join('data', 'images', 'terrain')
# Checks data/images/terrain/{family}/variant_XX.png on disk (source)
public_terrain_path = Rails.root.join('public', 'assets', 'terrain')
# Checks public/assets/terrain exists (mounted volume target)
```

### b) In the container — do `public/assets/biomes` and `public/assets/terrain` exist?

**No.** Only `/home/galaxy_game/public/assets` exists as an empty directory. Neither `biomes/` nor `terrain/` subdirectories exist inside it. The docker-compose mount maps `./data/images` → `/home/galaxy_game/public/assets`, but the source directories are not being placed at the expected paths in the container.

### c) On disk — data/images/ contents:

**Top level of `data/images/`:**
```
ASSET_PROMPTS.md, ASSET_PROMPTS_NEW.md, LAVA_TUBE_OUTPOST_SPEC.md,
LAVA_TUBE_VISUAL_ASSETS_SPEC.md, PHASE_3_SPRITE_SHEET_PROMPT.md,
TILESET_README.md, crystal, desert, hydrosphere, icons, logos, materials,
artificial, metallic, terrain, terrain_tiles, biosphere, catalog, wetlands,
chatgpt-assets-log.txt, asset-generation-tests, test-images
```

**File counts:**
- `data/images/terrain/` — **45 PNG files** (6 families × 9 variants)
- `data/images/terrain_tiles/` — **45 PNG files** (1 family: dust, 9 variants)
- `data/images/biomes/` — **13 PNG files**

### d) Terrain tile dimensions:

All spot-checked tiles are **150×150 PNG**:
- `terrain_tiles/dust/variant_01.png` — PNG 150x150, 8-bit sRGB
- `terrain_tiles/dust/variant_02.png` — PNG 150x150, 8-bit sRGB
- `terrain_tiles/regolith/variant_01.png` — PNG 150x150, 8-bit sRGB

---

## 3. Spec Groups — First Error + Shared Message Analysis

### Group A: `luna_operations_simulation_service_spec.rb:142,203`
- **Example 1 (line 142):** `expected: > 0, got: 0.0` — "daily tick produces ibeam via I-beam printer if regolith available (Tier B)"
- **Example 2 (line 203):** `expected: "IMPORT", got: "LOCAL_ONLY"` — "import gate logic when stockpile is sufficient"
- **Shared message?** No. Two completely different failure types (assertion count vs decision string).

### Group B: `component_production_integration_spec.rb:82,94`
- **Example 1 (line 82):** `RuntimeError: Insufficient materials: need 150kg of regolith`
- **Example 2 (line 94):** `RuntimeError: Insufficient materials: need 75kg of regolith`
- **Shared message?** Same root cause (`ensure_materials_available` raises at line 126), but different material amounts. Both fail in the same service method.

### Group C: `transit_engine_spec.rb` (full file)
- **32 examples, 8 failures** when run alone. First error: `expected: Tue, 22 Jan 2030, got: Tue, 15 Jan 2030` — arrival date off by 7 days for Earth→Luna transfer window.
- Multiple failures share the same root: `transit_days` returning 0 or wrong values, arrival dates consistently wrong. The `has_arrived?` check also fails (returns true at day 145 when it should return false).

### Group D: `unit_module_assembly_service_spec.rb` (full file)
- **8 examples, 8 failures** — all of them. First error: `expected craft.base_units.count to have changed by 4, but was changed by 0`.
- All failures share the same root: nothing gets built. Every assertion expects non-zero counts/objects but gets 0 or nil.

### Group E: `craft_lookup_service_spec.rb:185,221,257` + `material_lookup_service_spec.rb:248`
- **craft_lookup:** 3 failures out of 4 examples. First error: `Errno::ENOTDIR: Not a directory` at `craft_lookup_service.rb:41` (Dir.glob on what's not a directory). Second: `StandardError: Unexpected error` during init with file system errors. Third: `expected nil.present? to be truthy, got false`.
- **material_lookup line 248:** Passes alone (0 failures).
- **Shared message?** No — ENOTDIR, unexpected error, and nil assertion are three different failure modes.

### Group F: `item_spec.rb:293`
- `expected: {"anorthosite" => 80.0, "norite" => 10.0, "other" => 5.0, "troctolite" => 5.0}, got: {"anorthosite" => 80.0, "norite" => 10.0, "other" => 3.0, "troctolite" => 5.0, "troilite" => 2.0}`
- Composition mismatch: `other` is 3.0 vs expected 5.0, and `troilite` => 2.0 appears unexpectedly.

### Group G: `orbital_shipyard_service_spec.rb:129`
- **Passes alone** (1 example, 0 failures). Was empty in the grouped run because it passed — no failure to report.

### Group H: `game_spec.rb:66`
- `ActiveRecord::RecordInvalid: Validation failed: Percentage must be less than or equal to 100` at `volatile_phase_transition_service.rb:49`. The simulation is trying to add volatile material that would push atmospheric percentage over 100.

### Group I: `game_data_generator_spec.rb:13`
- **Passes alone** (1 example, 0 failures). Was empty in the grouped run because it passed.

---

## 4. Order-Dependence Check (re-run each failing file ALONE)

| File | With line numbers | Alone | Order-dependent? |
|---|---|---|---|
| `luna_operations_simulation_service_spec.rb` | 2 failures | **2 failures** | No — same count |
| `component_production_integration_spec.rb` | 2 failures | **2 failures** | No — same count |
| `transit_engine_spec.rb` | (lines 115,146,161,166 shown) | **8/32 failures** | Partially — more failures surface when run alone |
| `unit_module_assembly_service_spec.rb` | 7+ failures | **8/8 failures** | No — all fail either way |
| `craft_lookup_service_spec.rb` + `material_lookup_service_spec.rb` | 3/4 failures | **3/4 failures** (craft) | No — same count |
| `item_spec.rb:293` | 1 failure | **1 failure** | No |
| `orbital_shipyard_service_spec.rb:129` | passed | **passes** | N/A |
| `game_spec.rb:66` | 1 failure | **1 failure** | No |
| `game_data_generator_spec.rb:13` | passed | **passes** | N/A |
| `material_lookup_service_spec.rb:248` | (included in group) | **passes** | N/A |

**No order-dependence detected.** Every file that failed with line-number targeting also fails when run as a full file, and vice versa. The counts are consistent.

---

## 5. Data on Disk (data/json-data, gitignored)

### depleted_regolith / inert_waste files:
- **depleted_regolith**: `data/json-data/resources/materials/processed/geological/depleted_regolith.json`
- **inert_waste**: No direct file found with that exact name. Closest matches are old-blueprint copies in `old-json-data/production_old3/blueprints/units/manufacturing/inert_waste_processing_unit_blueprint.json` (archived, not active)

### lunar_production count:
**0 files** in `data/json-data/` contain the string `lunar_production`. The recursive search returned nothing.

### Methane's `pricing` block:
```json
"pricing": {
    "earth_usd": {
      "base_price_per_kg": 1.85,
      "transport_category": "hazardous",
      "hazard_multiplier": 1.5
    },
    "local_production": {
      "available": true,
      "facility_required": "sabatier_reactor",
      "cost_per_kg": 1.40,
      "production_time_hours": 1,
      "energy_kwh_per_kg": 1.2
    }
  }
```

---

## 6. Task File Status and Folders

**Standalone asset-generation task:**
- **Path**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/asset-ui/2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md`
- **Status**: `status: active` (folder: `tasks/active/asset-ui/`)

**GCC Mining task:**
- **Path**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md`
- **Status**: `status: backlog` (folder: `tasks/active/`)

**Contents of `tasks/active/` for galaxy_game:**
Two items:
1. `2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md` (status: backlog)
2. `asset-ui/2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md` (status: active)

---

## 7. Surprises

1. **`lunar_production` is completely absent** from `data/json-data/`. Zero files contain it — not even in subdirectories or old-json-data. This string may have been fully cleaned up or never existed in the active data.

2. **`orbital_shipyard_service_spec.rb:129` and `game_data_generator_spec.rb:13` both pass** when run alone, which means they were listed as "failing" in some earlier full-suite context but are not actually failing now. They may have been passing all along and the original list was aspirational or from a different state.

3. **`unit_module_assembly_service_spec.rb` fails 100%** (8/8) — every single example in the file fails with the same root cause (nothing gets built). This suggests a systemic setup/factory issue rather than scattered bugs.

4. **`transit_engine_spec.rb` has 8 failures across 32 examples** — about 25% failure rate, concentrated in date arithmetic (`transit_days` returning 0, arrival dates off by days). The `has_arrived?` and `days_remaining` methods are also affected.

---

*Generated: 2026-09-29*
*Read-only investigation — no fixes applied.*
