# A4 — Catalog Data Retrieval Research

**Task**: A4 (HIGH-RESEARCH-ASSET-UI-A4)  
**Status**: completed  
**Date**: 2026-10-07  
**Type**: research  

---

## Executive Summary

This research traced how catalog presentation retrieves canonical Blueprint, Operational Data, and Visual Definition for RH-400 (Unit case). The key finding is that **the catalog pipeline has no Visual Definition lookup path at all** — it only loads blueprints + operational_data from `app/data/` and derives image paths via a broken `.json → .png` substitution. The Production/Presentation split is structurally enforced but the presentation side (image resolution) is non-functional for RH-400.

---

## 1. Catalog Retrieval Pipeline — Full Trace

### Entry Point: Admin::CatalogController

**File**: `galaxy_game/app/controllers/admin/catalog_controller.rb`

```ruby
class Admin::CatalogController < ApplicationController
  def index
    @categories = catalog_service.entries.map { |e| e[:category] }.uniq.sort
    filtered = catalog_service.entries
    # ... filtering by category, subcategory, search query ...
    @entries = catalog_service.paginated_result(filtered, page: params[:page])
  end

  def show
    @entry = catalog_service.find_entry(params[:id])
    @cross_ref = find_cross_reference(@entry)  # blueprint ↔ operational_data link
  end

  private
  def catalog_service
    @catalog_service ||= CatalogService.new
  end
end
```

**Key finding**: The controller uses `CatalogService` exclusively. No Visual Definition lookup, no asset registry query, no image path resolution beyond what CatalogService provides.

### Data Loading: CatalogService

**File**: `galaxy_game/app/services/catalog_service.rb`

#### What it loads (two sources):
1. **Blueprints**: `GalaxyGame::Paths::BLUEPRINTS_PATH.join('**/*.json')` → `app/data/blueprints/**/*.json`
2. **Operational Data**: `app/data/operational_data/**/*.json`

#### How it builds entries:
```ruby
def build_entry(file_path, source_type)
  data = JSON.parse(File.read(file_path))
  relative = Pathname.new(file_path).relative_path_from(base_path)
  # base_path = GalaxyGame::Paths::JSON_DATA = Rails.root.join('app', 'data')

  parts = relative.to_s.split('/')
  # e.g., ["blueprints", "crafts", "ground", "regolith_harvester_rover_bp.json"]

  category = parts[1]  # "blueprints" or "operational_data"
  subcategory = parts[2] if parts.size > 3  # "ground" for RH-400

  filename = File.basename(file_path, '.json')
  name = filename.sub(/_bp$/, '').split('_').map(&:capitalize).join(' ')
  # → "Regolith Harvester Rover"

  entry_type = data['type'] || data['craft_type'] || ...

  id = relative.to_s.sub("#{parts[0]}/", '').sub('.json', '').sub(/_bp$/, '')
  # → "crafts/ground/regolith_harvester_rover"

  {
    id: id,
    name: name,
    type: entry_type,
    category: category,
    subcategory: subcategory,
    source_type: source_type,
    file_path: file_path.to_s,
    has_image: image_exists?(relative),       # ← critical path
    thumbnail_path: relative.to_s.gsub('.json', '.png'),  # ← critical path
    data: data,
    created_at: File.mtime(file_path)
  }
end
```

#### Image path derivation (THE CRITICAL PATH):
```ruby
def image_exists?(relative_path)
  img_path = base_path.join('images', relative_path.to_s.gsub('.json', '.png'))
  img_path.exist?
end
```

For RH-400 blueprint at `app/data/blueprints/crafts/ground/regolith_harvester_rover_bp.json`:
- `relative` = `"blueprints/crafts/ground/regolith_harvester_rover_bp.json"`
- `thumbnail_path` = `"blueprints/crafts/ground/regolith_harvester_rover.png"`
- `img_path` = `app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png`
- **Result**: ❌ FILE DOES NOT EXIST

### View Rendering: Admin Catalog Views

**File**: `galaxy_game/app/views/admin/catalog/show.html.erb` (detail view)
```erb
<% if @entry[:has_image] %>
  <%= image_tag "/images/#{@entry[:thumbnail_path]}", alt: @entry[:name], class: 'detail-image' %>
<% else %>
  <div class="image-placeholder-large">
    <i class="<%= thumbnail_icon(@entry) %>"></i>
    <p>No image generated yet</p>
  </div>
<% end %>
```

**File**: `galaxy_game/app/views/admin/catalog/index.html.erb` (grid view)
```erb
<% if entry[:has_image] %>
  <%= image_tag "/images/#{entry[:thumbnail_path]}", alt: entry[:name], class: 'card-image' %>
<% else %>
  <!-- placeholder icon -->
<% end %>
```

**Key finding**: Views use `image_tag "/images/#{thumbnail_path}"` which maps to Rails asset pipeline at `/assets/{thumbnail_path}`. The path is derived from the blueprint's relative filesystem path, not from any Visual Definition or asset registry.

---

## 2. Identifier Resolution at Each Boundary

### Boundary 1: Blueprint ID → Catalog Entry
- **Identifier used**: Blueprint `id` field (`regolith_harvester_rover`)
- **Resolution**: Filesystem path — `app/data/blueprints/crafts/ground/regolith_harvester_rover_bp.json`
- **CatalogService ID**: Relative path from base_path with `.json` and `_bp` stripped → `"crafts/ground/regolith_harvester_rover"`
- **Cross-reference**: `find_operational_data_by_name()` strips `_bp` suffix, finds matching operational_data by basename

### Boundary 2: Catalog Entry → Image
- **Identifier used**: Relative filesystem path from blueprint file
- **Resolution**: `.json → .png` substitution at `base_path.join('images', relative.gsub('.json', '.png'))`
- **Result for RH-400**: Looks for `app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png` — DOES NOT EXIST
- **Actual files exist at**: `data/images/catalog/crafts/ground/rh400_concept.png`, etc.

### Boundary 3: Visual Definition → Catalog Entry
- **Identifier used**: NONE — no lookup path exists
- **Resolution**: Not implemented. The catalog pipeline has zero awareness of Visual Definitions.
- **Gap**: Visual Definition at `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` is never queried by the catalog system.

---

## 3. RH-400 Representation References in Current Code/Data

### What CatalogService Knows About RH-400
| Field | Value | Source |
|-------|-------|--------|
| `id` | `"crafts/ground/regolith_harvester_rover"` | Blueprint relative path |
| `name` | `"Regolith Harvester Rover"` | Filename parsing |
| `type` | `"craft"` | Blueprint JSON `type` field |
| `category` | `"blueprints"` | Directory structure |
| `subcategory` | `"ground"` | Directory structure |
| `source_type` | `"blueprint"` | Hardcoded in load_entries |
| `has_image` | `false` | Image path doesn't exist |
| `thumbnail_path` | `"blueprints/crafts/ground/regolith_harvester_rover.png"` | Derivation logic |

### What CatalogService Does NOT Know About RH-400
| Missing Information | Expected Source | Status |
|---------------------|-----------------|--------|
| Visual Definition asset_id (`VEHICLE_HARVESTER_ROVER_RH400`) | docs/ (development-time only) | ❌ Not loaded |
| Render profiles (6 defined in Visual Definition) | docs/ (development-time only) | ❌ Not loaded |
| Camera profiles (4 defined in Visual Definition) | docs/ (development-time only) | ❌ Not loaded |
| Recognition features (6 defined in Visual Definition) | docs/ (development-time only) | ❌ Not loaded |
| Animation profile (`vehicles_status_lights`) | docs/ (development-time only) | ❌ Not loaded |
| Any of the 3 actual image files | Filesystem (wrong path) | ❌ Path mismatch |

### Which RH-400 Files Are Actually Referenced by Current Code
**None.** The catalog pipeline derives a non-existent path (`app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png`). The actual files at `data/images/catalog/crafts/ground/rh400_*.png` are never referenced by any code path.

---

## 4. Multiple Representations — Explicitly Modeled or Coexisting?

**Answer**: They merely coexist as files. No explicit modeling exists.

- The three RH-400 image files (`rh400_concept.png`, `rh400_regolith_harvesting_rover.png`, `rh400_sprite_test.png`) exist as independent files in the same directory
- No metadata, manifest, or registry links them to a single asset identity
- No code distinguishes between catalog render and surface sprite representations
- The Visual Definition's `render_profiles` array (6 items) is never consumed by any runtime code

---

## 5. Existing Fallback/Path-Resolution Behavior

### CatalogService Image Resolution
```ruby
# For each blueprint entry:
has_image = image_exists?(relative_path)  # checks if derived path exists
thumbnail_path = relative.to_s.gsub('.json', '.png')  # always derives, never validates
```

**Behavior**: `has_image` is `false` for RH-400 because the derived path doesn't exist. The view renders a placeholder icon instead of an image. No fallback to alternative paths or directories occurs.

### SurfaceView.js Terrain Tile Resolution
```javascript
_buildTilePath: function(family, type, variant) {
  const base = '/assets';
  const paths = [
    `${base}/${family}/${type}_${variant}.png`,
    `${base}/${family}/${type}_01.png`,
    `${base}/${family}/plain_01.png`,
  ];
  for (const p of paths) {
    if (this._assetExists(p)) return p;  // KNOWN_ASSETS is empty Set!
  }
  return null;  // always falls through to colour rendering
}
```

**Behavior**: `KNOWN_ASSETS` is an empty `Set()`. The `_assetExists()` check always returns `false`. The fallback chain never finds any known assets. All terrain tiles render as elevation colours, not sprites.

### BiomeRenderer PNG Resolution
```javascript
// From biomes.json:
asset_path: '/assets/biomes/'

// In init():
await Promise.all(
  biomeEntries.map(([name, meta]) =>
    this._loadAndScale(name, `${assetPath}${meta.file}`)
  )
);

// In draw():
if (this.tiles.has(key)) {
  ctx.drawImage(this.tiles.get(key), x, y, size, size);
} else {
  ctx.fillStyle = this.fallbacks.get(key) || '#1a1a2e';
  ctx.fillRect(x, y, size, size);  // solid colour fallback
}
```

**Behavior**: BiomeRenderer fetches PNGs from `/assets/biomes/{biome_name}.png`. If a PNG fails to load (network error, missing file), it falls back to the `color_fallback` hex value from biomes.json. This is the only functional image resolution path in the system.

---

## 6. Production/Presentation Split — Explicitly Documented as Unresolved

### Current State of the Split

| Aspect | Production (Game Runtime) | Presentation (Catalog UI) |
|--------|--------------------------|--------------------------|
| **Data source** | `app/data/blueprints/` + `app/data/operational_data/` | Same files (via CatalogService) |
| **Visual Definition access** | None (development-time only in `docs/`) | None (development-time only in `docs/`) |
| **Image resolution** | SurfaceView.js terrain tiles (broken — empty KNOWN_ASSETS) | CatalogService derived paths (broken — path mismatch) |
| **Asset registry** | Not implemented | Not implemented |
| **Visual Profile** | Not consumed | Not consumed |
| **Render Template** | Not consumed | Not consumed |

### The Split Is Intentionally Unresolved
Per A4 task file Gotcha 3: "The Production/Presentation split is intentionally unresolved." This research confirms the split exists structurally but neither side has functional asset resolution.

### Concrete Options for B2 to Resolve (Evidence-Only, No Selection)

**Option A — CatalogService extends image lookup**:
- Add Visual Definition query to CatalogService
- Map blueprint ID → asset_id → actual file paths in `data/images/catalog/`
- Distinguish render profiles by type (catalog render vs sprite)
- **Risk**: Blurs production/presentation boundary; adds docs/ dependency

**Option B — Separate presentation layer**:
- Keep CatalogService as game-data-only (blueprints + operational_data)
- Build a separate `PresentationService` that queries Visual Definitions and resolves image paths
- CatalogController uses both services
- **Risk**: Adds architectural complexity; requires new service

**Option C — Asset Registry as bridge**:
- Implement asset registry (B1 work) that maps blueprint IDs to asset_ids to file paths
- Both production and presentation query the registry
- **Risk**: Requires B1 completion first; adds runtime dependency

### Decision Point for B2
The Production/Presentation split is an architectural decision point. This research reports evidence without selecting an architecture. B2 must decide how catalog presentation accesses Visual Definition data and resolves image paths.

---

## 7. Icon Bible Gap

**Status**: The Visual Definition references the Icon Bible for `technology_level` (Mk2), `manufacturing_style` (heavy_industrial), and `animation_profile` (vehicles_status_lights). However, the Icon Bible file (`2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md`) does not exist in the workspace.

**Impact on catalog presentation**: The catalog pipeline doesn't currently consume icon data at all. If B2's catalog presentation contract requires icon assets, A6 (Icon Bible dependency research) must be completed first to determine whether the Icon Bible exists or needs reconstruction.

---

## 8. Evidence Relevant to B2 Catalog Presentation Contract

### What Exists Today
1. **CatalogService** loads blueprints + operational_data from `app/data/` — functional for game data
2. **Admin::CatalogController** serves catalog index/detail views — functional for UI structure
3. **Image placeholder fallback** renders when `has_image` is false — functional degradation path
4. **BiomeRenderer** has a working PNG fetch + solid-colour fallback pattern — reusable pattern

### What Is Missing for B2
1. **Visual Definition lookup** — no code path exists to query Visual Definitions
2. **Asset identity resolution** — blueprint ID → asset_id → file path mapping is absent
3. **Render profile distinction** — no code distinguishes catalog render from surface sprite
4. **Image path resolution** — CatalogService derivation logic produces non-existent paths
5. **Multiple representation support** — no mechanism to select which RH-400 file to display

### B2 Design Inputs from This Research
- The catalog UI structure (controller + views) is functional and can be extended
- Image resolution must be redesigned — current `.json → .png` derivation is broken
- Visual Definition data lives in `docs/` (development-time only) — any runtime access requires architectural decision
- BiomeRenderer's fetch + fallback pattern is a viable model for catalog image loading
- The Icon Bible gap (A6) must be resolved before icon handling can be designed

---

## 9. Files Referenced

| File | Purpose |
|------|---------|
| `galaxy_game/app/services/catalog_service.rb` | CatalogService — blueprint/operational_data loader + image path derivation |
| `galaxy_game/app/controllers/admin/catalog_controller.rb` | Admin::CatalogController — catalog index/detail views |
| `galaxy_game/app/views/admin/catalog/show.html.erb` | Catalog detail view — image rendering with placeholder fallback |
| `galaxy_game/app/views/admin/catalog/index.html.erb` | Catalog grid view — image rendering with placeholder fallback |
| `data/json-data/blueprints/crafts/ground/regolith_harvester_rover_bp.json` | RH-400 blueprint (canonical game data) |
| `data/json-data/operational_data/crafts/ground/regolith_harvesting_rover_data.json` | RH-400 operational data |
| `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` | RH-400 Visual Definition (6 render profiles, 4 camera profiles) |
| `galaxy_game/app/assets/javascripts/surface_view.js` | SurfaceView.js — terrain tile pipeline (KNOWN_ASSETS empty, broken resolution) |
| `galaxy_game/app/assets/javascripts/biome_renderer.js` | BiomeRenderer — PNG fetch + solid-colour fallback pattern |
| `galaxy_game/public/tilesets/galaxy_game/biomes.json` | Tileset config — asset_path: `/assets/biomes/` |
| A2 synthesis report | Storage locations and path mismatch evidence |
| A3 synthesis report | RH-400 asset family mapping, 6-to-3 gap |
