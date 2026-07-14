---
status: not-started
priority: HIGH
type: feature
mvp_alignment: POST_SNAP (admin catalog needed for asset management pre-launch)
local_worker_safe: true
created: 2026-07-12
last-updated: 2026-07-12
phase: 15+
---

# Admin Catalog View — Blueprint + Operational Data + Images

## Context
The admin interface needs a unified catalog view that displays all blueprints and operational data entries with their associated images. Currently there is no single view that lets admins browse, filter, and inspect the full asset pipeline (JSON data → generated images).

**Data sources**:
- Blueprints: `data/json-data/blueprints/` — unit/craft/module/structure/blueprint JSON files
- Operational data: `data/json-data/operational_data/` — runtime parameters for units/modules/rigs/structures
- Images: `data/json-data/images/` — generated asset images (mirrors blueprints + operational_data structure)

**Target deployment**: Images will be mapped to `galaxy_game/public/` in the container later. The catalog view needs to reference images by their path within `images/`.

## Problem Statement
Without a unified catalog, admins cannot:
- Browse all assets (blueprints + operational data) in one view
- See which assets have generated images vs missing images
- Filter by category (units, modules, rigs, structures, crafts, components)
- Search by name or type
- Preview images before deploying to public folder

## Scope
- Create admin catalog index page listing all blueprints + operational data entries
- Display thumbnail previews for assets with generated images
- Show missing-image indicators for assets without images
- Filter by category (units/modules/rigs/structures/crafts/components/materials/items)
- Filter by subcategory (life_support, propulsion, energy, etc.)
- Search by name/type
- Detail view showing full JSON data + image preview side-by-side
- Link from blueprint entry to its operational data counterpart (where applicable)

## Target Files
- `galaxy_game/app/controllers/admin/catalog_controller.rb` (NEW)
- `galaxy_game/app/views/admin/catalog/index.html.erb` (NEW — grid/list view with thumbnails)
- `galaxy_game/app/views/admin/catalog/show.html.erb` (NEW — detail view with JSON + image)
- `galaxy_game/app/assets/stylesheets/admin/catalog.scss` (NEW — grid layout, thumbnail sizing)
- `galaxy_game/app/javascript/admin/catalog.js` (NEW — filter/search/debounce)

## Acceptance Criteria
- [ ] Catalog index lists all blueprints and operational data entries with thumbnails
- [ ] Assets with images show thumbnail; assets without show placeholder icon
- [ ] Filter by category works (units, modules, rigs, structures, crafts, components, materials, items)
- [ ] Filter by subcategory works (life_support, propulsion, energy, etc.)
- [ ] Search by name/type returns matching results
- [ ] Detail view shows full JSON data in collapsible panel + image preview
- [ ] Blueprint → operational data cross-links work where applicable
- [ ] Performance acceptable for 200+ entries (lazy-load thumbnails)

## Implementation Steps
1. Create `CatalogController#index` that loads all blueprint and operational data files from `data/json-data/`
2. Build a unified index mapping each entry to its image path (if exists) within `images/`
3. Create grid/list view with thumbnail previews and category/subcategory filters
4. Implement search with debounce for name/type filtering
5. Create detail view (`show`) with JSON data panel + image preview
6. Add cross-reference links between blueprints and operational data where applicable
7. Style catalog with admin theme (consistent with existing admin views)

## Dependencies
- Requires: `data/json-data/blueprints/` populated with blueprint files
- Requires: `data/json-data/operational_data/` populated with operational data files
- Requires: `data/json-data/images/` structure created (done 2026-07-12)
- No other task dependencies

## Notes
- Image paths should use relative paths within `images/` — the public mapping happens later in deployment
- Thumbnail generation can be deferred — show placeholder icons for now, swap to thumbnails when images exist
- Consider pagination or virtual scrolling if entry count exceeds ~100
- The catalog view is admin-only — no authentication changes needed (uses existing admin auth)

---

## Handoff

### STEP 0 — Lifecycle Rules (MANDATORY)
1. **Move task from backlog/phase15+/ → backlog/current/ BEFORE starting work**
2. Update YAML header: `status: not-started` → `status: active`
3. Commit the move to agent-tasks repo before writing any code
4. Do not skip this step — it signals work has begun

### Output Destination
- Synthesis report → `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/2026-07-12-ADMIN-CATALOG-VIEW.md`
- Save synthesis report to agent-tasks repo after implementation

### Files to Review (context only — not for editing)
- `galaxy_game/app/views/admin/organizations/index.html.erb` — existing admin view pattern reference
- `galaxy_game/app/views/admin/ai_manager/index.html.erb` — existing admin layout reference
- `data/json-data/blueprints/units/life_support/co2_oxygen_production_bp.json` — sample blueprint data
- `data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json` — sample operational data

### Scope Clarification
This task creates an **admin-only catalog view** for browsing and inspecting all game assets (blueprints + operational data + images). It does NOT modify the JSON data files themselves, nor does it handle image generation. The public deployment mapping of `images/` to `public/` is a separate concern handled in deployment configuration.
