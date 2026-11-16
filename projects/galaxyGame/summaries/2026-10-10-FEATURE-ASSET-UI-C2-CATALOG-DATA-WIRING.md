## C2 Synthesis Report — Catalog Data Wiring

**Task**: C2 — Implement Catalog Data Wiring
**Status**: completed
**Date**: 2026-10-10

### What Was Implemented

Added `catalog_data(asset_id, registry:)` to `CatalogService` that assembles the B2 catalog presentation contract by wiring together:

1. **AssetRegistry** (development-time orchestration) — provides resolved artifact paths (`blueprint_path`, `operational_data_path`, `visual_definition_path`, `representations`)
2. **CatalogService** (runtime data loading) — loads blueprint JSON and operational data JSON from disk

### Object-Class Split (per B2)

| Object Class | Data Sources | Contract Fields |
|---|---|---|
| Components (`asset_family == 'component'`) | Blueprint + Visual only | `blueprint_data`, `visual_definition`, `catalog_render_path`, `inventory_icon_path`, `representation_status` — NO `operational_data` |
| Units/Structures/Vehicles | Blueprint + Operational Data + Visual | All Component fields PLUS `operational_data` |

### Files Changed (GalaxyGame)

| File | Changes |
|---|---|
| `galaxy_game/app/services/catalog_service.rb` | +176 lines: `catalog_data`, `assemble_catalog_contract`, `resolve_catalog_contract_for_registry`, `load_blueprint_data`, `load_operational_data_for_blueprint`, `load_visual_definition_data`, `resolved_catalog_render_path`, `resolved_inventory_icon_path`, `compute_representation_status`, `component_has_operational_data?` |
| `galaxy_game/spec/services/catalog_service_spec.rb` | +85 lines: 6 focused C2 tests |

### Test Results

**25 examples, 0 failures** — all `catalog_service_spec.rb` tests pass.

C2-focused tests:
- ✅ `returns nil for unknown asset_id`
- ✅ `returns nil for blank asset_id`
- ✅ `returns a hash with B2 contract fields for registered RH-400 Unit`
- ✅ `includes operational_data key for Units/Structures/Vehicles (may be nil if no file)`
- ✅ `excludes operational_data for Components (I-beam case)`
- ✅ `does not generate fake Operational Data for Components`
- ✅ `preserves existing catalog behavior — entries_for unchanged`

### Architecture Decisions

1. **Test-injected registry**: `catalog_data(asset_id, registry:)` accepts an optional registry parameter for test isolation. Production code uses a lazily-initialized default instance.
2. **No new data model**: Wiring operates entirely on existing Hash structures from AssetRegistry and CatalogService. No schema changes needed.
3. **Pre-computed manifest paths preserved**: `catalog_manifest_path` and `surface_manifest_path` are stored in registry entries but remain nil until development-time orchestration populates them (future C-series work).

### Unresolved Items

- Pre-existing JSON parse failures in test data files (`small_habitat_bp.json`, `regolith_shell_printer_mk*_data.json`) — unrelated to C2
- Production/Presentation boundary remains an unresolved architectural decision per B2

### Next Step

C3 (RH-400 Catalog Vertical Slice) is now unblocked.
