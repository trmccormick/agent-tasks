# A2 Verification Summary — B1/B2 Completion Claims vs Current Code

**Reviewer**: Planning Agent (Qwen)  
**Date**: 2026-10-13  
**Assignment**: Verify A2 synthesis claims against B1/B2 implementation evidence  

---

## 1. Task Files, Syntheses, and Commits

### B1 — Map Asset Registry to Visual Definition
| Item | Location |
|------|----------|
| Task file | `tasks/completed/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md` (status: completed) |
| Synthesis | `summaries/2026-10-07-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md` |
| Commit | `4361fbc3` — "feat: implement Asset Registry/orchestration per B1 design" |
| Code | `galaxy_game/app/services/asset_registry.rb` (class `AssetRegistry`) |

### B2 — Catalog Presentation Contract
| Item | Location |
|------|----------|
| Task file | `tasks/completed/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md` (status: completed) |
| Synthesis | `summaries/2026-10-07-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md` |
| Commit | `d614e4fa` — "feat: implement catalog data wiring per B2 contract (C2)" |
| Code | `catalog_data(asset_id, registry:)` method in `CatalogService` |

### C2 — Catalog Data Wiring (implementation)
| Item | Location |
|------|----------|
| Original spec | `tasks/backlog/asset-ui/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md` (status: backlog) |
| Synthesis | `summaries/2026-10-10-FEATURE-ASSET-UI-C2-CATALOG-DATA-WIRING.md` (status: completed) |
| Commit | `d614e4fa` (same as B2 — wiring was the implementation of B2's design) |

### C3 — RH-400 Catalog Vertical Slice
| Item | Location |
|------|----------|
| Synthesis | `summaries/2026-10-10-FEATURE-ASSET-UI-C3-RH400-CATALOG-VERTICAL-SLICE.md` |
| Commit | `d7894ca3` — "fix(C3): Add RH-400 catalog image serving fallback when AssetRegistry fails" |

---

## 2. Path Resolution Status: PARTIALLY RESOLVED

### What Was Resolved (via C2/C3 code)

The **new path resolution path** for `catalog_data(asset_id)` works as follows:

```
catalog_data(asset_id)
  → AssetRegistry.resolve(asset_id)
    → register_asset() or discover_from_filesystem()
      → representations manifest with file paths
  → resolved_catalog_render_path(entry) — reads from manifest['catalog_render']['files']
  → resolved_inventory_icon_path(entry) — reads from manifest['inventory_icon']['files']
```

For RH-400 specifically, when `AssetRegistry.resolve()` returns nil (visual definitions not mounted), the **C3 fallback** fires:

```ruby
# catalog_service.rb line ~108
if entry.nil? && asset_id == AssetRegistry::RH400_ASSET_ID
  entry = assemble_rh400_entry  # hardcoded RH-400 data
end
```

The C3 controller (`catalog_image` action) serves images from `app/data/images/catalog/...` via:
```ruby
file_path = GalaxyGame::Paths::JSON_DATA.join(params[:path])
catalog_dir = GalaxyGame::Paths::JSON_DATA.join('images', 'catalog')
if file_path.to_s.start_with?(catalog_dir.to_s) && File.exist?(file_path)
  send_file file_path, disposition: 'inline'
end
```

### What Was NOT Resolved (still in code)

The **old broken path derivation** still exists in `CatalogService`:

1. **`image_exists?` method** (line ~245): Still uses `base_path.join('images', relative_path.to_s.gsub('.json', '.png'))` — this checks `app/data/images/blueprints/...` which does NOT exist. This method is used by the legacy `build_entry()` path for the catalog index listing, NOT by `catalog_data()`.

2. **`thumbnail_path` in build_entry** (line ~238): Still derives `blueprints/crafts/ground/regolith_harvester_rover.png` from blueprint relative path. This is used for the catalog index view's image_tag, which means the **index page still shows broken images** for assets that don't have a matching file at the derived path.

3. **No standardized naming convention**: A2 flagged this; no follow-up task was filed or executed.

### Summary: Partial Resolution

| Aspect | Status | Evidence |
|--------|--------|----------|
| `catalog_data(asset_id)` for detail view | ✅ Resolved — uses AssetRegistry manifest or C3 fallback | `catalog_service.rb` lines ~108-145, `resolved_catalog_render_path()` |
| RH-400 catalog image display | ✅ Resolved — C3 fallback + controller serving | Commit `d7894ca3`, `catalog_image` action |
| Catalog index page images | ❌ Unresolved — still uses old `.json → .png` derivation | `build_entry()` line ~238, `image_exists?()` line ~245 |
| Naming convention standardization | ❌ Unresolved — no task filed or executed | A2 finding #2; no follow-up |

---

## 3. RH-400 Representation Selection in Code

### How representations are distinguished:

**In AssetRegistry**: The `representations` manifest stores each render profile as a key (`'catalog_render'`, `'inventory_icon'`, etc.) with status and files array. This is populated by `register_asset()` or `discover_from_filesystem()`.

**In CatalogService**: `resolved_catalog_render_path(entry)` reads `manifest['catalog_render']['files'].first`. `resolved_inventory_icon_path(entry)` reads `manifest['inventory_icon']['files'].first`.

**For RH-400 fallback** (`assemble_rh400_entry`): Hardcoded representations:
```ruby
'representations' => {
  'catalog_render' => { status: 'exists', files: find_rh400_catalog_renders() },
  'inventory_icon' => { status: 'missing', files: [] }
}
```

**`find_rh400_catalog_renders()`**: Globs `rh400_*concept*.png` and `rh400_*regolith*.png` under `data/images/catalog/`. Returns first match as catalog render.

**Key finding**: The code distinguishes representations by **render profile key** (catalog_render vs inventory_icon vs sprite_sheet, etc.), NOT by file extension or color type (RGB vs RGBA). A2's finding that RH-400 has 3 distinct files (2 RGB catalog renders + 1 RGBA sprite test) is acknowledged in the manifest structure but there is no code that selects based on consumer context (UI display vs surface rendering).

---

## 4. C2 Record Reconciliation

### Completed record: `summaries/2026-10-10-FEATURE-ASSET-UI-C2-CATALOG-DATA-WIRING.md`
- Claims: `catalog_data(asset_id, registry:)` implemented, object-class split per B2, 25 tests passing
- **Supported by code**: ✅ The method exists at `catalog_service.rb` line ~108 with all described fields (blueprint_data, visual_definition, catalog_render_path, inventory_icon_path, representation_status, operational_data)
- **Supported by commit**: ✅ `d614e4fa` shows +241 lines across catalog_service.rb and catalog_service_spec.rb

### Original spec: `tasks/backlog/asset-ui/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md`
- Status: `backlog` (never moved to active/completed)
- Represents the **planned** C2 task before execution
- The implementation was executed and committed but the original spec file was never moved — it remains in backlog as a historical artifact

### Conclusion on C2 reconciliation
The completed record accurately describes what was implemented. The original spec file's `backlog` status is stale — the work it described was done under commit `d614e4fa`. This is a **task file hygiene issue**, not an implementation gap.

---

## 5. Recommended Next Bounded Task

**Task**: Fix catalog index page image resolution for assets that don't have files at the derived `.json → .png` path.

**Scope**: 
- Modify `build_entry()` in `CatalogService` to use AssetRegistry manifest paths (when available) as the primary image source, falling back to the old derivation only when registry is unavailable
- OR: Add a secondary lookup that checks `data/images/catalog/...` when the primary derived path doesn't exist
- Target: catalog index view (`admin/catalog/index.html.erb`) image display

**Why this first**: The detail view (RH-400 vertical slice) works. The index page is what users see first and currently shows broken images for most assets. This is the highest-impact gap remaining from the A2/B1/B2/C2 chain.

---

*Verification complete. No tasks created, moved, or dispatched per constraints.*
