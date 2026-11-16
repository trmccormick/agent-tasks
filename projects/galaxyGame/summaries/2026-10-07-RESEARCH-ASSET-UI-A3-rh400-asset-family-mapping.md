# A3 — RH-400 Asset Family Mapping

**Task**: A3 (HIGH-RESEARCH-ASSET-UI-A3)  
**Status**: completed  
**Date**: 2026-10-07  
**Type**: research  

---

## Executive Summary

This research mapped each known RH-400 asset-family representation to its intended consumer, distinguishing catalog/presentation assets from surface-render assets. The canonical Visual Definition defines **six render profiles** and **four camera profiles**, but only **three actual image files** exist in the repository — none of which are named according to any established convention. The mapping reveals significant gaps between the planned architecture and current reality.

---

## 1. Canonical RH-400 Identity Chain

Three distinct canonical identifiers reference the same asset:

| Identifier Type | Value | Source |
|----------------|-------|--------|
| **Blueprint ID** | `regolith_harvester_rover` | `data/json-data/blueprints/crafts/ground/regolith_harvester_rover_bp.json` |
| **Visual Definition asset_id** | `VEHICLE_HARVESTER_ROVER_RH400` | `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` |
| **Blueprint name** | "RH-400 Regolith Harvester Rover" | Both files above |

### Canonical Relationships (from Visual Definition)
```json
{
  "asset_id": "VEHICLE_HARVESTER_ROVER_RH400",
  "asset_family": "vehicle",
  "component_class": "harvester",
  "blueprint_ref": "regolith_harvester_rover"
}
```

### Canonical Render Profiles (from Visual Definition)
The Visual Definition specifies **six** render profiles:
1. `inventory_icon`
2. `catalog_render`
3. `engineering_render`
4. `blueprint`
5. `exploded_view`
6. `sprite_sheet`

### Canonical Camera Profiles (from Visual Definition)
Four camera angles are specified:
1. `front_isometric`
2. `rear_isometric`
3. `side_profile`
4. `top_down`

---

## 2. RH-400 Asset Family Mapping Table

This table maps each **planned** representation to its **actual** repository state.

### OBSERVED FACTS (what exists in the repository)

| # | Representation | Intended Consumer | Actual File Path | Status | Evidence |
|---|---------------|-------------------|------------------|--------|----------|
| 1 | **Catalog render** (RGB, with background) | Catalog UI / documentation | `data/images/catalog/crafts/ground/rh400_concept.png` (1402×1122, RGB) | ✅ EXISTS | A2 file inspection |
| 2 | **Catalog render — alternate** (RGB, with background) | Catalog UI / documentation | `data/images/catalog/crafts/ground/rh400_regolith_harvesting_rover.png` (1536×1024, RGB) | ✅ EXISTS | A2 file inspection |
| 3 | **Surface sprite** (RGBA, transparent) | Surface renderer / gameplay | `data/images/catalog/crafts/ground/rh400_sprite_test.png` (1536×1024, RGBA) | ✅ EXISTS (test variant) | A2 file inspection |

### INFERRED MAPPINGS (based on canonical specs, not confirmed by files)

| # | Representation | Intended Consumer | Expected Path Pattern | Actual File | Status |
|---|---------------|-------------------|----------------------|-------------|--------|
| 4 | **Inventory icon** | Catalog card thumbnails | `data/images/catalog/crafts/ground/rh400_icon.png` (inferred) | ❌ MISSING | No file found |
| 5 | **Engineering render** | Engineering documentation | `data/images/catalog/crafts/ground/rh400_engineering.png` (inferred) | ❌ MISSING | No file found |
| 6 | **Blueprint** | Technical documentation | `data/images/catalog/crafts/ground/rh400_blueprint.png` (inferred) | ❌ MISSING | No file found |
| 7 | **Exploded view** | Assembly documentation | `data/images/catalog/crafts/ground/rh400_exploded.png` (inferred) | ❌ MISSING | No file found |
| 8 | **Sprite sheet** | Animation system | `data/images/catalog/crafts/ground/rh400_spritesheet.png` (inferred) | ❌ MISSING | No file found |

### ASSET-GENERATION TEST OUTPUTS (development artifacts, not production assets)

| # | Representation | Path | Status |
|---|---------------|------|--------|
| 9 | Run 03 ChatGPT control | `data/images/asset-generation-tests/rh400/control - run03/chatgpt/ChatGPT Image Aug 25, 2026, 10_39_33 PM.png` | Development artifact |
| 10 | Run 03 Gemini control | `data/images/asset-generation-tests/rh400/control - run03/gemini/Gemini_Generated_Image_4bk5gu4bk5gu4bk5.jpeg` | Development artifact |
| 11 | Run 04 ChatGPT | `data/images/asset-generation-tests/rh400/run04/chatgpt/ChatGPT Image Aug 26, 2026, 09_41_52 PM.png` | Development artifact |
| 12 | Run 04 Gemini | `data/images/asset-generation-tests/rh400/run04/gemini/Gemini_Generated_Image_nuhpofnuhpofnuhp.jpeg` | Development artifact |
| 13 | Run 05 ChatGPT | `data/images/asset-generation-tests/rh400/run05/chatgpt/ChatGPT Image Aug 26, 2026, 11_00_20 PM.png` | Development artifact |
| 14 | Run 05 Gemini | `data/images/asset-generation-tests/rh400/run05/gemini/Gemini_Generated_Image_bfxjb8bfxjb8bfxj.jpeg` | Development artifact |
| 15 | Run 06 ChatGPT | `data/images/asset-generation-tests/rh400/run06/chatgpt/ChatGPT Image Aug 27, 2026, 09_54_29 AM.png` | Development artifact |
| 16 | Run 06 Gemini | `data/images/asset-generation-tests/rh400/run06/gemini/Gemini_Generated_Image_vl5gmevl5gmevl5g.jpeg` | Development artifact |

---

## 3. Canonical Asset-Generation Pipeline (rh400-prompt-template.md)

The prompt template defines a **three-pass output structure** that clarifies the intended separation:

### Pass 1 — 3D/Orthographic Render (Transparent Background)
- **Purpose**: Feeds surface sprites, animation frames, and damage-state assets
- **Output**: Transparent-background images with multiple angles (front, rear, side, isometric)
- **Consumer**: Surface renderer, animation system, damage states

### Pass 2 — Blueprint/Dimensioned View (White Background)
- **Purpose**: Geometrically locked to Pass 1 proportions
- **Output**: Dimensioned blueprint views with gridlines and callouts
- **Consumer**: Technical documentation, engineering review

### Pass 3 — Catalog/UI Compositing (In-App)
- **Purpose**: Assembled in Rails/frontend from Pass 1 hero render + blueprint thumbnail + live sprites
- **Output**: NOT requested from image generator — assembled by application layer
- **Consumer**: Catalog UI cards

**Critical Rule from template**: "Sprite/animation/damage-state assets must always come from Pass 1 (transparent background), never cropped from a composite catalog sheet."

---

## 4. RH-400 as the Canonical Reference Case

### Visual Profile
RH-400 is the **canonical reference** for `VISUAL_PROFILE_precision_industrial_v1.md`. The Visual Definition explicitly states:
- "RH-400 is the canonical reference for this profile"
- Design philosophy: "Grounded engineering realism — every visible surface has a functional purpose"
- Feel: heavy, industrial, rugged, modular, repairable

### Technology Level
- `technology_level: 2` (Mk2) — "cleaner, welded, slight sheen" per Icon Bible Section 4
- `manufacturing_style: heavy_industrial` per Icon Bible Section 5

### Design Constraints (from Visual Definition)
```json
{
  "must_be_recognizable_at_32px": true,
  "must_share_family_appearance": true,
  "must_support_manufacturing_variants": true,
  "must_follow_location_agnostic_naming": true,
  "must_have_animation_profile": true
}
```

### Recognition Features (6 total — from Visual Definition)
1. Six-wheel independent suspension chassis
2. Forward regolith skimming scoop assembly
3. Mid-body cylindrical processing canister
4. Rear-mounted dust exhaust stack
5. Top-mounted sensor mast with rotating array
6. Exposed hydraulic actuator arms on scoop joints

### Animation Profile
- Value: `vehicles_status_lights` (from Icon Bible Section 9)
- Cycle: 0.5–1s for vehicle status lights

---

## 5. Naming Convention Analysis

### Observed File Names vs Canonical IDs

| File Name | Blueprint ID Match? | asset_id Match? | Pattern |
|-----------|---------------------|-----------------|---------|
| `rh400_concept.png` | ❌ No (`regolith_harvester_rover`) | ❌ No (`VEHICLE_HARVESTER_ROVER_RH400`) | Abbreviated prefix + descriptive suffix |
| `rh400_regolith_harvesting_rover.png` | Partial (contains blueprint name) | ❌ No | Abbreviated prefix + full blueprint name |
| `rh400_sprite_test.png` | ❌ No | ❌ No | Abbreviated prefix + purpose + `_test` suffix |

### Key Finding: No standardized naming convention exists
- All files use `rh400_` as a prefix (abbreviated, not matching either canonical ID)
- Suffixes are inconsistent: `concept`, `regolith_harvesting_rover`, `sprite_test`
- No file uses the full asset_id (`VEHICLE_HARVESTER_ROVER_RH400`) or blueprint ID (`regolith_harvester_rover`) as a prefix
- The `_test` suffix on the sprite file indicates it is an experimental variant, not a production asset

### Visual Definition Constraint
The Visual Definition states `must_follow_location_agnostic_naming: true` — but no naming convention has been established to satisfy this constraint.

---

## 6. CatalogService Path Resolution vs Actual RH-400 Files

### What CatalogService Would Derive
```ruby
# Blueprint path: app/data/blueprints/crafts/ground/regolith_harvester_rover_bp.json
relative = "blueprints/crafts/ground/regolith_harvester_rover_bp.json"
thumbnail_path = relative.gsub('.json', '.png')
# → "blueprints/crafts/ground/regolith_harvester_rover.png"

# Image lookup: app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png
# Result: ❌ FILE DOES NOT EXIST
```

### What Actually Exists
```
data/images/catalog/crafts/ground/rh400_concept.png
data/images/catalog/crafts/ground/rh400_regolith_harvesting_rover.png
data/images/catalog/crafts/ground/rh400_sprite_test.png
```

### The Mismatch (Reiterated from A2)
| Aspect | CatalogService Derives | Actual Location |
|--------|----------------------|-----------------|
| Root directory | `app/data/images/` | `data/images/catalog/` |
| Category dir | `blueprints/` | `catalog/crafts/ground/` |
| Filename | `regolith_harvester_rover.png` | `rh400_concept.png` (etc.) |

---

## 7. Missing Representations — Gap Analysis

The Visual Definition specifies **6 render profiles**. Only **3 files exist** (2 catalog renders + 1 sprite test). The following are **MISSING**:

| Missing Representation | Expected Consumer | Impact |
|----------------------|-------------------|--------|
| Inventory icon | Catalog card thumbnails | Catalog grid cards show no image for RH-400 |
| Engineering render | Engineering documentation | No engineering reference view available |
| Blueprint (dimensioned) | Technical documentation | No dimensioned technical drawing |
| Exploded view | Assembly documentation | No assembly visualization |
| Sprite sheet | Animation system | No animation frames available |

### Additional Missing Elements (from prompt template and Visual Definition)
| Missing Element | Expected Consumer | Impact |
|----------------|-------------------|--------|
| Multiple camera angles (front/rear/side/top) | All render profiles | Only single-angle renders exist |
| Animation frames (idle/moving/harvesting/damage) | Surface renderer / gameplay | No animation capability |
| Wrecked sprite variant | Damage states | No damage visualization |
| Thumbnail variant | Catalog grid | No optimized thumbnail |

---

## 8. Distinguishing Observed Facts from Inferred Relationships

### OBSERVED FACTS (confirmed by repository evidence)
1. RH-400 has exactly **3 image files** in `data/images/catalog/crafts/ground/`
2. Two are RGB catalog renders; one is RGBA sprite test
3. The Visual Definition defines 6 render profiles and 4 camera profiles
4. No file naming matches either canonical ID (blueprint or asset_id)
5. CatalogService derives non-existent paths from blueprint relative paths
6. Asset-generation tests produced 8 additional images across runs 03–06 (development artifacts)
7. The prompt template defines a three-pass output structure (Pass 1 = transparent, Pass 2 = white bg, Pass 3 = in-app compositing)
8. RH-400 is the canonical reference for `VISUAL_PROFILE_precision_industrial_v1.md`

### INFERRED RELATIONSHIPS (not confirmed by repository evidence)
1. **Inferred**: The two RGB files serve as "catalog render" — plausible but not explicitly labeled
2. **Inferred**: The RGBA file is intended as a surface sprite — it has `_test` suffix, suggesting experimental status
3. **Inferred**: Missing representations (icon, engineering, blueprint, exploded, sprite sheet) are needed for full pipeline — confirmed by Visual Definition render_profiles list
4. **Inferred**: The naming convention needs standardization — confirmed by `must_follow_location_agnostic_naming: true` constraint with no convention in place

---

## 9. Implications for B1 and B3

### For B1 (Map Asset Registry to Visual Definition)
- The asset registry must handle the **6-to-3 gap**: mapping 6 planned render profiles to only 3 existing files
- The registry must bridge three identifier systems: blueprint ID, asset_id, and file naming
- The registry must track which representations exist vs. which are missing for each asset
- File naming standardization is a prerequisite for reliable path resolution

### For B3 (Surface Asset Representation Contract)
- The sprite test file (`rh400_sprite_test.png`) is the only surface sprite candidate, but its `_test` suffix indicates it is not production-ready
- The prompt template's Pass 1 requirement (transparent background, multiple angles) is not met by any existing file
- Animation frames and damage states are entirely absent — B3 must define how these will be produced and referenced
- The separation between catalog renders (RGB with background) and surface sprites (RGBA transparent) is architecturally sound but only partially implemented

---

## 10. What A3 Unlocks

### B1 Can Now:
- Define the mapping between three identifier systems (blueprint ID, asset_id, file naming)
- Specify how the asset registry tracks existing vs. missing representations per asset
- Establish naming convention requirements that satisfy `must_follow_location_agnostic_naming`

### B3 Can Now:
- Define the surface asset representation contract grounded in the prompt template's Pass 1 specification
- Specify the gap between current sprite test and production-ready surface assets
- Define how animation frames and damage states will be produced and referenced

### B2 (Catalog Presentation Contract) Can Also Benefit:
- The catalog presentation layer must handle assets that exist as multiple representations (catalog render, icon, etc.)
- The path resolution mechanism must distinguish between RGB catalog renders and RGBA surface sprites

---

## 11. Files Referenced

| File | Purpose |
|------|---------|
| `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` | Canonical RH-400 Visual Definition (6 render profiles, 4 camera profiles) |
| `data/images/catalog/crafts/ground/rh400_concept.png` | RH-400 catalog render (RGB, 1402×1122) — OBSERVED |
| `data/images/catalog/crafts/ground/rh400_regolith_harvesting_rover.png` | RH-400 catalog render alternate (RGB, 1536×1024) — OBSERVED |
| `data/images/catalog/crafts/ground/rh400_sprite_test.png` | RH-400 sprite test (RGBA, 1536×1024) — OBSERVED, experimental |
| `docs/reference/asset-generation/rh400-prompt-template.md` | Three-pass output structure (Pass 1 transparent, Pass 2 white bg, Pass 3 in-app) |
| `docs/reference/asset-generation/rh400_controlled_generation_test_spec.md` | Controlled generation test spec — canonical source files and data |
| `data/json-data/blueprints/crafts/ground/regolith_harvester_rover_bp.json` | RH-400 blueprint (canonical game data) |
| `data/json-data/operational_data/crafts/ground/regolith_harvesting_rover_data.json` | RH-400 operational data |
| `galaxy_game/app/services/catalog_service.rb` | CatalogService path resolution logic |
| `docs/reference/asset-generation/ASSET_GENERATION_PIPELINE_VALIDATION.md` | RH-400 runs 03–06 results (feature prioritization, Profile Composition, safeguards) |
| `data/images/asset-generation-tests/rh400/` | Asset-generation test outputs (8 images across runs 03–06) |
| A2 synthesis report | Storage locations and path mismatch evidence |
