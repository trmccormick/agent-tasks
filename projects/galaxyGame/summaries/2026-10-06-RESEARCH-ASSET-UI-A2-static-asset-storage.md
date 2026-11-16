# A2 — Static Asset Storage and Presentation Research

**Task**: A2 (HIGH-RESEARCH-ASSET-UI-A2)  
**Status**: completed  
**Date**: 2026-10-06  
**Type**: research  

---

## Executive Summary

This research established the actual storage locations, naming conventions, and reference mechanisms for generated static assets in Galaxy Game. The primary finding is a **path resolution mismatch** between how CatalogService expects to find images and where they actually exist. RH-400 has three distinct representation files stored under `data/images/catalog/crafts/ground/`, but the catalog service would look for them at a completely different path derived from the blueprint file location.

---

## 1. Asset Storage Locations Found

### Primary Catalog Image Directory
**Path**: `data/images/catalog/`  
**Structure**: Flat category directories with subdirectories by type:

```
data/images/catalog/
├── components/          (3d_printed_ibeam_*.png)
├── crafts/              (rh400_*.png, heavy_lift_transport_*.png)
│   ├── ground/
│   └── space/spacecraft/
├── infrastructure/      (empty)
├── items/
├── materials/
├── modules/
├── ports/               (empty)
├── rigs/
├── structures/
└── units/               (production/fabricators/*.png, production/extractors/*.png)
```

### Rails App Asset Directory
**Path**: `galaxy_game/app/assets/images/`  
**Contents**: Only GalaxyGame branding images (GalaxyGame.png, GalaxyGame-Clean.png, etc.) and galaxy_surface.png. **No catalog unit images.**

### Lib Assets Directory
**Path**: `galaxy_game/lib/assets/`  
**Contents**: Empty (`.keep` file only).

### Public Directory
**Path**: `public/`  
**Contents**: Only `tilesets/` subdirectory. No catalog images.

---

## 2. RH-400 Presentation Assets — Exact Paths and Types

Three distinct files found at `data/images/catalog/crafts/ground/`:

| File | Dimensions | Color Type | Role |
|------|-----------|------------|------|
| `rh400_concept.png` | 1402 × 1122 | RGB (8-bit) | **Catalog render** — presentation asset with background |
| `rh400_regolith_harvesting_rover.png` | 1536 × 1024 | RGB (8-bit) | **Catalog render** — alternate/secondary presentation asset |
| `rh400_sprite_test.png` | 1536 × 1024 | RGBA (8-bit, transparent) | **Sprite test** — surface sprite candidate (transparent) |

### Naming Convention
- Catalog renders: `rh400_<description>.png` (lowercase prefix + underscore + descriptive suffix)
- Sprite variants: `rh400_<purpose>_test.png` (includes `_test` suffix)
- No standardized naming — "concept" vs "regolith_harvesting_rover" are both catalog renders with no clear hierarchy

### Key Finding: RH-400 is NOT a single image
Per A2 Gotcha 1, the three representations serve different consumers:
- **Catalog renders** (RGB): For UI/display with backgrounds
- **Sprite test** (RGBA): For surface rendering (transparent)
- These are separate files with separate technical contracts

---

## 3. Blueprint and Operational Data Locations

### Blueprint
**Path**: `data/json-data/blueprints/crafts/ground/regolith_harvester_rover_bp.json`  
**ID**: `regolith_harvester_rover`  
**Name**: "RH-400 Regolith Harvester Rover"  
**Type**: craft / harvester

### Operational Data
**Path**: `data/json-data/operational_data/crafts/ground/regolith_harvesting_rover_data.json`  
**ID**: `regolith_harvester_rover`  
**Name**: "RH-400 Regolith Harvester Rover"

### Visual Definition
**Path**: `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`  
**asset_id**: `VEHICLE_HARVESTER_ROVER_RH400`  
**blueprint_ref**: `regolith_harvester_rover`  
**Status**: active (created 2026-08-04)

### Identity Chain
```
Blueprint ID: regolith_harvester_rover
Visual Definition asset_id: VEHICLE_HARVESTER_ROVER_RH400
File naming: rh400_* (abbreviated, not matching either ID)
```

**Finding**: The file naming convention (`rh400_`) does NOT match the canonical blueprint ID (`regolith_harvester_rover`) or the Visual Definition asset_id (`VEHICLE_HARVESTER_ROVER_RH400`). This is a naming inconsistency that B1's mapping work must address.

---

## 4. CatalogService Path Resolution — THE CRITICAL MISMATCH

### How CatalogService Builds Image Paths

From `galaxy_game/app/services/catalog_service.rb`:

```ruby
# base_path = GalaxyGame::Paths::JSON_DATA = Rails.root.join('app', 'data')
relative = Pathname.new(file_path).relative_path_from(base_path)
# For blueprint at: app/data/blueprints/crafts/ground/regolith_harvester_rover_bp.json
# relative = "blueprints/crafts/ground/regolith_harvester_rover_bp.json"

thumbnail_path = relative.to_s.gsub('.json', '.png')
# thumbnail_path = "blueprints/crafts/ground/regolith_harvester_rover.png"

# image_exists? checks:
img_path = base_path.join('images', relative.to_s.gsub('.json', '.png'))
# = Rails.root.join('app', 'data', 'images', 'blueprints/crafts/ground/regolith_harvester_rover.png')
```

### Where CatalogService Expects Images
**Expected path**: `galaxy_game/app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png`  
(derived from blueprint relative path, .json → .png)

### Where RH-400 Images Actually Exist
**Actual path**: `data/images/catalog/crafts/ground/rh400_concept.png`  
(and `rh400_regolith_harvesting_rover.png`, `rh400_sprite_test.png`)

### The Mismatch
| Aspect | CatalogService Expects | Actual Location |
|--------|----------------------|-----------------|
| Root | `galaxy_game/app/data/images/` | `data/images/catalog/` |
| Category dir | `blueprints/` or `operational_data/` | `catalog/crafts/ground/` |
| Filename | `regolith_harvester_rover.png` (from blueprint) | `rh400_concept.png` (abbreviated) |
| **Match?** | ❌ NO — completely different path | |

### Verification
```bash
# Expected path does NOT exist:
ls galaxy_game/app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png
# → No such file or directory

# Actual path exists:
ls data/images/catalog/crafts/ground/rh400_concept.png
# → rh400_concept.png
```

---

## 5. Asset-Generation Test Outputs

**Path**: `data/images/asset-generation-tests/rh400/run06/`  
Contains generated images from ChatGPT and Gemini runs (Run 03 through Run 06):
- `run06/chatgpt/ChatGPT Image Aug 27, 2026, 09_54_29 AM.png`
- `run06/gemini/Gemini_Generated_Image_vl5gmevl5gmevl5g.jpeg`

These are **development-time generation artifacts** from the asset-generation pipeline experiments documented in `ASSET_GENERATION_PIPELINE_VALIDATION.md`. They are NOT catalog presentation assets.

---

## 6. Other Catalog Images Found

### Components
- `data/images/catalog/components/structural/3d_printed_ibeam_mk1.png` (and mk2, mk3, mk4)
- I-beam is the Component case referenced in B2's Object Class split

### Crafts — Space
- `data/images/catalog/crafts/space/spacecraft/heavy_lift_transport_mk1.png` (and variants)
- `data/images/catalog/crafts/space/spacecraft/cycler_concept.png`

### Units — Production
- `data/images/catalog/units/production/fabricators/3d_printed_fabricator_mk1_concept.png`
- `data/images/catalog/units/production/extractors/thermal_extraction_unit_mk1*.png` (multiple variants)

---

## 7. Storage Mechanism Summary

### How Assets Are Currently Stored
1. **Filesystem-based** — all images are PNG/JPEG files on disk under `data/images/`
2. **No database storage** — no ActiveStorage or similar attachment mechanism found
3. **No CDN or external hosting** — all assets are local files
4. **No standardized naming convention** — filenames vary by generation context (ChatGPT vs Gemini vs manual)

### How Assets Are Currently Referenced
1. **CatalogService** derives image paths from blueprint relative paths (`.json` → `.png`)
2. **Admin catalog views** use `image_tag "/images/#{thumbnail_path}"` to render images
3. **Visual Definition files** reference assets by `asset_id` but do NOT contain file paths
4. **PromptCompiler** consumes Visual Definitions as JSON inputs — no asset path resolution

### Critical Gap
The CatalogService path derivation logic assumes a 1:1 mapping between blueprint files and image files at `app/data/images/blueprints/...`. This assumption is **not met in the current repository state**. The actual images live under `data/images/catalog/` with different directory structure and naming.

---

## 8. Implications for B1 and B2

### For B1 (Map Asset Registry to Visual Definition)
- The asset registry concept must bridge the gap between:
  - Blueprint ID (`regolith_harvester_rover`)
  - Visual Definition asset_id (`VEHICLE_HARVESTER_ROVER_RH400`)
  - Actual file paths (`data/images/catalog/crafts/ground/rh400_concept.png`)
- The registry must handle **multiple representations per asset** (catalog render, sprite, etc.)
- File naming conventions are inconsistent and must be normalized or mapped

### For B2 (Catalog Presentation Contract)
- The catalog presentation layer needs a path resolution mechanism that:
  - Accepts an `asset_id` or blueprint ID as input
  - Resolves to the correct image file(s) in `data/images/catalog/`
  - Distinguishes between catalog render (RGB) and surface sprite (RGBA) representations
- The current CatalogService derivation logic (`relative_path_from(base_path).gsub('.json', '.png')`) is **not sufficient** — it produces paths that don't exist

### For A3 (RH-400 Asset Family Mapping)
- RH-400 has 3 distinct representations stored separately
- No single "canonical" image file exists for RH-400
- The naming convention (`rh400_`) is an abbreviation, not matching any canonical ID

---

## 9. What A2 Unlocks

### B1 Can Now:
- Define the mapping between blueprint IDs, asset_ids, and actual file paths
- Specify how the asset registry should store multiple representations per asset
- Establish naming convention requirements for future generated assets

### B2 Can Now:
- Design a path resolution mechanism that works with the actual `data/images/catalog/` structure
- Define the catalog presentation contract's image handling (catalog render vs sprite distinction)
- Specify how the admin catalog view should resolve images when the current derivation logic fails

### A3, A4, A5 Can Now:
- Build on established storage conventions rather than discovering them from scratch
- Reference concrete file paths and directory structures in their research

---

## 10. Files Referenced

| File | Purpose |
|------|---------|
| `data/images/catalog/` | Primary catalog image storage |
| `data/images/catalog/crafts/ground/rh400_concept.png` | RH-400 catalog render (RGB) |
| `data/images/catalog/crafts/ground/rh400_regolith_harvesting_rover.png` | RH-400 catalog render alternate (RGB) |
| `data/images/catalog/crafts/ground/rh400_sprite_test.png` | RH-400 sprite test (RGBA, transparent) |
| `galaxy_game/app/services/catalog_service.rb` | CatalogService path resolution logic |
| `galaxy_game/config/initializers/game_data_paths.rb` | GalaxyGame::Paths constants |
| `data/json-data/blueprints/crafts/ground/regolith_harvester_rover_bp.json` | RH-400 blueprint (canonical game data) |
| `data/json-data/operational_data/crafts/ground/regolith_harvesting_rover_data.json` | RH-400 operational data |
| `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` | RH-400 Visual Definition |
| `data/images/asset-generation-tests/rh400/run06/` | Asset-generation test outputs (development artifacts) |
| `galaxy_game/app/assets/images/` | Rails app assets (branding only, no catalog images) |
| `galaxy_game/app/views/admin/catalog/show.html.erb` | Catalog detail view (image rendering) |
| `galaxy_game/app/views/admin/catalog/index.html.erb` | Catalog index view (image rendering) |
