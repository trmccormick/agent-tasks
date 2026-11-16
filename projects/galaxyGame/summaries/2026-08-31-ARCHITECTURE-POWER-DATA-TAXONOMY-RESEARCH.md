# STATUS SYNTHESIS REPORT

**Task**: 2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH
**Status**: backlog → active
**Date**: 2026-09-14

### What I'm About to Do

Inventory the actual blueprint and operational-data filesystem, parse the relevant
JSON files, inspect code and loader references, and compare the energy/power/
power_generation taxonomy. The investigation will remain read-only and will
produce evidence for a later migration task.

### Files I'll Reference

| File or path | Purpose | Status |
|---|---|---|
| `/home/galaxy_game/app/data/blueprints/` | Blueprint catalog inventory | pending |
| `/home/galaxy_game/app/data/operational_data/` | Operational-data catalog inventory | pending |
| `/home/galaxy_game/app/data/blueprints/structures/power_generation/` | Structure-level power-generation records | pending |
| `/home/galaxy_game/app/data/blueprints/units/energy/` | Historical unit-level energy path | pending |
| `/home/galaxy_game/app/data/blueprints/units/power/` | Current unit-level power path | pending |
| `/home/galaxy_game/app/data/blueprints/units/power_generation/` | Possible duplicate unit-level path | pending |
| `compact_nuclear_reactor_bp.json` | Compact reactor comparison target | pending |
| `nuclear_micro_reactor_mk1_bp.json` | Micro-reactor comparison target | pending |
| `mars_industrial_nuclear_reactor_bp.json` | Possible Mars reactor overlap | pending |
| `compact_fusion_reactor_l1_bp.json` | Blueprint/operational-data mismatch target | pending |
| `compact_fusion_reactor_l1_data-2.json` | Operational-data comparison target | pending |
| `docs/new_agent/rules/DECISIONS.md` | Locked architecture decisions | confirmed |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution guardrails | confirmed |
| Lookup and loader source files found by search | Path and lookup behavior | pending |
| Related specs and fixtures found by search | Compatibility and regression references | pending |

### Prerequisites Completed

- ✅ Step 0: Task file moved to `active/` with `git mv`.
- ✅ Step 0: YAML status updated from `backlog` to `active`.
- ✅ Step 0: Exactly one task-file path verified with `find`.
- ✅ Read the agent-tasks README EXECUTOR section.
- ✅ Read the Galaxy Game project guide.
- ✅ Read this task file.
- ✅ Reviewed architecture gotchas.
- ✅ Confirmed this is a read-only investigation.
- ✅ Confirmed no credentials or multi-tenant domain are required.
- ✅ Docker container `web` confirmed running (Up 4 days).
- ✅ Data paths `/home/galaxy_game/app/data/blueprints/` and `/home/galaxy_game/app/data/operational_data/` confirmed accessible.

### Expected Outcomes

The report will contain an evidence-based inventory of the actual directories and
JSON files, duplicate and near-duplicate candidates, filename/payload mismatches,
loader references, slot semantics, schema differences, and a recommended taxonomy.
No application data or source catalog file will be changed.

### Critical Gotchas I Will Avoid

- ❌ Moving or renaming files during inventory — instead, record proposed moves only.
- ❌ Treating `energy`, `power`, and `power_generation` as equivalent — instead, compare their slot and loader semantics.
- ❌ Treating filename suffixes as authoritative — instead, validate payload metadata.
- ❌ Comparing reactor output values without units — instead, classify thermal, electrical, gross, and net output.
- ❌ Assuming structures can only host units — instead, inspect unit, rig, and module slot support.

---

**SYNTHESIS COMPLETE.** Ready to proceed with PRIORITY 1: filesystem and JSON inventory.

---

# POWER, ENERGY, AND GENERATION DATA TAXONOMY — RESEARCH REPORT

**Task**: 2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH
**Status**: active
**Date**: 2026-09-14
**Researcher**: Implementation Agent (Qwen)
**Scope**: Read-only investigation of `/home/galaxy_game/app/data/blueprints/` and `/home/galaxy_game/app/data/operational_data/`

---

## 1. Executive Findings

### Key Discovery: Three Distinct Power-Related Categories Exist

The data catalog contains **three separate power-related categories** that are loaded independently by the lookup service:

| Category | Blueprint Path | Operational Data Path | Loader Key | File Count (BP) |
|---|---|---|---|---|
| `energy` | `/blueprints/units/energy/` | `/operational_data/units/energy/` | `energy` | 7 |
| `power` | `/blueprints/units/power/` | `/operational_data/units/power/` | `power` | 10 |
| `power_generation` | `/blueprints/units/power_generation/` | `/operational_data/units/power_generation/` | NOT DEFINED | 1 |

**Critical Finding**: The loader (`UnitLookupService`) has explicit entries for both `energy` and `power` paths but **no entry for `power_generation`**. This means files in `blueprints/units/power_generation/` are **not loaded by the unit lookup service** unless another mechanism references them.

### Slot Semantics Are Category-Specific

The factory structure (`metal_smelter_facility_bp`) reveals a clear pattern:
- **Unit slots use `type: "energy"`** (count: 3) — units are categorized as `energy` type
- **Module slots use `type: "power"`** (count: 2) — modules are categorized as `power` type

This suggests **`energy` is the unit-level category and `power` is the module-level category**, not interchangeable synonyms.

### Duplicate IDs Are a Critical Risk

Eight duplicate ID pairs were found across blueprints, including two CRITICAL duplicates that span power/energy directories:
- `power_controller` exists in 3 locations (modules/energy, units/energy, units/power)
- `solar_panel` exists in 2 locations (units/energy, units/power)

These duplicates would cause loader collisions if both directories are loaded into the same namespace.

### Reactor Blueprints Lack Specs

All three nuclear reactor blueprints (`compact_nuclear_reactor`, `nuclear_micro_reactor_mk1`, `mars_industrial_nuclear_reactor`) have **no specs section** — no thermal output, electrical output, mass, volume, or construction data. They are metadata shells only.

---

## 2. Actual Directory Tree (Power-Related Paths)

### Blueprints Structure
```
blueprints/
├── modules/energy/                    [7 files]
├── rigs/energy/                       [exists]
├── rigs/power/                        [exists]
├── structures/power_generation/       [0 files — empty directory]
├── units/energy/                      [7 files]
├── units/power/                       [10 files]
└── units/power_generation/            [1 file]
```

### Operational Data Structure
```
operational_data/
├── modules/energy/                    [7 files]
├── rigs/energy/                       [exists]
├── rigs/power/                        [1 file: solar_expansion_rig_data.json]
├── structures/power_generation/       [exists — no files listed]
├── units/energy/                      [7 files]
├── units/power/                       [2 files]
└── units/power_generation/            [1 file: nuclear_micro_reactor_v1_data.json]
```

### Directory Existence Table

| Path | Exists | Files |
|---|---|---|
| `blueprints/units/energy` | ✅ YES | 7 |
| `blueprints/units/power` | ✅ YES | 10 |
| `blueprints/units/power_generation` | ✅ YES | 1 |
| `blueprints/structures/power_generation` | ✅ YES | 0 (empty) |
| `operational_data/units/energy` | ✅ YES | 7 |
| `operational_data/units/power` | ✅ YES | 2 |
| `operational_data/units/power_generation` | ✅ YES | 1 |

**All six paths from the task requirements exist.** The `energy` directory was NOT renamed or removed — it coexists with `power` and `power_generation`.

---

## 3. Complete Affected JSON Inventory

### Blueprints — units/energy (7 files)
| File | ID | Template | Metadata.type | Category |
|---|---|---|---|---|
| biogas_generator_engine_bp.json | biogas_generator_engine | unit_blueprint | blueprint | energy |
| nuclear_reactor_fusion_bp.json | nuclear_reactor_fusion | unit_blueprint | blueprint | energy |
| planetary_power_management_unit_bp.json | planetary_power_management_unit_mk1 | unit_blueprint | blueprint | power_generation |
| power_controller_bp.json | power_controller | unit_blueprint | blueprint | power_generation |
| radioisotope_thermoelectric_generator_bp.json | radioisotope_thermoelectric_generator | unit_blueprint | blueprint | energy |
| satellite_battery_bp.json | satellite_battery | unit_blueprint | blueprint | energy |
| solar_panel_bp.json | solar_panel | unit_blueprint | blueprint | energy |

### Blueprints — units/power (10 files)
| File | ID | Template | Metadata.type | Category |
|---|---|---|---|---|
| compact_nuclear_reactor_bp.json | compact_nuclear_reactor | component_blueprint | blueprint | power_generation |
| compact_solar_panel_bp.json | compact_solar_panel | unit_blueprint | blueprint | power |
| energy_storage_unit_bp.json | energy_storage_unit | unit_blueprint | blueprint | power |
| mars_industrial_nuclear_reactor_bp.json | mars_industrial_nuclear_reactor | component_blueprint | blueprint | power_generation |
| power_controller_bp.json | power_controller | unit_blueprint | blueprint | power_generation |
| power_distribution_unit_bp.json | power_distribution_unit | unit_blueprint | blueprint | power |
| rtg_power_unit_bp.json | rtg_power_unit | unit_blueprint | blueprint | power |
| solar_panel_array_bp.json | solar_panel_array | unit_blueprint | blueprint | power |
| solar_panel_bp.json | solar_panel | unit_blueprint | blueprint | power |
| solar_shade_power_collector_bp.json | solar_shade_power_collector | unit_blueprint | blueprint | power |

### Blueprints — units/power_generation (1 file)
| File | ID | Template | Metadata.type | Category |
|---|---|---|---|---|
| nuclear_micro_reactor_mk1_bp.json | nuclear_micro_reactor_mk1 | unit_blueprint | blueprint | power_generation |

### Modules — energy (7 files)
All modules in `blueprints/modules/energy/` have `category: "energy"` and are loaded via `ModuleLookupService` using the single `energy` path. No `modules/power/` directory exists.

---

## 4. Duplicate and Near-Duplicate Candidates

### CRITICAL Duplicates (Same ID, Different Categories)

| ID | Location 1 | Location 2 | Severity |
|---|---|---|---|
| `power_controller` | `blueprints/modules/energy/power_controller_bp.json` | `blueprints/units/energy/power_controller_bp.json` | CRITICAL |
| `power_controller` | `blueprints/modules/energy/power_controller_bp.json` | `blueprints/units/power/power_controller_bp.json` | CRITICAL |
| `power_controller` | `blueprints/units/energy/power_controller_bp.json` | `blueprints/units/power/power_controller_bp.json` | CRITICAL |
| `solar_panel` | `blueprints/units/energy/solar_panel_bp.json` | `blueprints/units/power/solar_panel_bp.json` | CRITICAL |

### HIGH Duplicates (Same ID, Same Category Level)

| ID | Location 1 | Location 2 | Severity |
|---|---|---|---|
| `planetary_power_management_unit_mk1` | `blueprints/units/energy/planetary_power_management_unit_bp.json` | `blueprints/units/infrastructure/planetary_power_management_unit_mk1_bp.json` | HIGH |
| `comms_equipment` | `blueprints/units/electronics/comms_equipment_bp.json` | `blueprints/units/infrastructure/comms_equipment_bp.json` | HIGH |
| `3d_printed_ibeam_mk1` | `blueprints/components/structural/3d_printed_ibeam_mk1_bp.json` | `blueprints/components/structural/3d_printed_ibeam_mk1_bp_v1.4.json` | HIGH |
| `smart_composites_bp` | `blueprints/components/materials/smart_composites_bp.json` | `blueprints/items/smart_composites_bp.json` | HIGH |
| `planetary_volatiles_extractor_mk1` | `blueprints/units/production/extractors/...` | `blueprints/units/resource/...` | HIGH |

### Near-Duplicates (Similar Names, Different IDs)

| ID 1 | ID 2 | Relationship |
|---|---|---|
| `compact_nuclear_reactor` | `mars_industrial_nuclear_reactor` | Both are nuclear reactors in different directories |
| `nuclear_micro_reactor_mk1` | `compact_nuclear_reactor` | Both are micro/compact nuclear reactors |
| `solar_panel_bp.json` (units/energy) | `solar_panel_array_bp.json` (units/power) | Similar solar generation products |
| `compact_solar_panel_bp.json` (units/power) | `solar_panel_bp.json` (units/energy) | Both are compact/small solar panels |

---

## 5. Reactor Comparison

### Compact Nuclear Reactor (`compact_nuclear_reactor`)
- **Path**: `blueprints/units/power/compact_nuclear_reactor_bp.json`
- **Template**: `component_blueprint` (NOT `unit_blueprint`)
- **Metadata.type**: `blueprint`
- **Category**: `power_generation`
- **Subcategory**: `nuclear`
- **Specs present**: ❌ NONE (no thermal_output, electrical_output, mass, volume)
- **Unit/module/rig slots**: ❌ NONE

### Nuclear Micro Reactor Mk1 (`nuclear_micro_reactor_mk1`)
- **Path**: `blueprints/units/power_generation/nuclear_micro_reactor_mk1_bp.json`
- **Template**: `unit_blueprint`
- **Metadata.type**: `blueprint`
- **Category**: `power_generation`
- **Subcategory**: `nuclear_reactor`
- **Specs present**: ❌ NONE
- **Unit/module/rig slots**: ❌ NONE

### Mars Industrial Nuclear Reactor (`mars_industrial_nuclear_reactor`)
- **Path**: `blueprints/units/power/mars_industrial_nuclear_reactor_bp.json`
- **Template**: `component_blueprint` (NOT `unit_blueprint`)
- **Metadata.type**: `blueprint`
- **Category**: `power_generation`
- **Subcategory**: `nuclear`
- **Specs present**: ❌ NONE
- **Unit/module/rig slots**: ❌ NONE

### Compact Fusion Reactor L1 (`compact_fusion_reactor_l1`)
- **Status**: ❌ FILE NOT FOUND
- **Expected location**: `blueprints/units/power_generation/compact_fusion_reactor_l1_bp.json`
- **Operational data variant**: `compact_fusion_reactor_l1_data-2.json` also NOT FOUND

### Comparison Summary

| Field | compact_nuclear_reactor | nuclear_micro_reactor_mk1 | mars_industrial_nuclear_reactor |
|---|---|---|---|
| Template | component_blueprint | unit_blueprint | component_blueprint |
| Category | power_generation | power_generation | power_generation |
| Subcategory | nuclear | nuclear_reactor | nuclear |
| Specs | ❌ none | ❌ none | ❌ none |
| Directory | units/power | units/power_generation | units/power |

**Finding**: All three reactors are **metadata shells** with no operational specs. The `compact_nuclear_reactor` and `mars_industrial_nuclear_reactor` share the same template (`component_blueprint`) and subcategory (`nuclear`), suggesting they may be variants of the same product line. The `nuclear_micro_reactor_mk1` uses `unit_blueprint` template, which is inconsistent with the other two.

---

## 6. Blueprint vs Operational-Data Mismatches

### Filename/Payload Type Mismatches

| File | Filename Suffix | Payload metadata.type | Mismatch? |
|---|---|---|---|
| `compact_fusion_reactor_l1_bp.json` (expected) | `_bp` = blueprint | ??? (file not found) | ❓ Cannot verify |
| `compact_nuclear_reactor_bp.json` | `_bp` = blueprint | `blueprint` | ✅ Consistent |
| `mars_industrial_nuclear_reactor_bp.json` | `_bp` = blueprint | `blueprint` | ✅ Consistent |

### Missing Operational Data Files

The task expected `compact_fusion_reactor_l1_data-2.json` to exist in operational data. **This file does not exist** in the current data tree. Neither does any `compact_fusion_reactor*` file anywhere under `/home/galaxy_game/app/data/`.

### Directory vs Payload Category Mismatches

| File | Directory Category | Payload Category | Match? |
|---|---|---|---|
| `blueprints/units/power/compact_nuclear_reactor_bp.json` | power | power_generation | ❌ MISMATCH |
| `blueprints/units/power/mars_industrial_nuclear_reactor_bp.json` | power | power_generation | ❌ MISMATCH |
| `blueprints/units/energy/planetary_power_management_unit_bp.json` | energy | power_generation | ❌ MISMATCH |
| `blueprints/units/energy/power_controller_bp.json` | energy | power_generation | ❌ MISMATCH |

**Finding**: Four files are stored in directories that do not match their payload category. This suggests the directory structure was created by a later convention than the file metadata, or files were moved without updating metadata.

---

## 7. Energy vs Power Evidence

### Loader Behavior

The `UnitLookupService` loads units from two independent paths:

```ruby
# app/services/lookup/unit_lookup_service.rb
UNIT_PATHS = {
  energy: {
    path: -> { GalaxyGame::Paths::ENERGY_UNITS_PATH },  # /blueprints/units/energy
    recursive_scan: true
  },
  power: {
    path: -> { GalaxyGame::Paths::POWER_UNITS_PATH },   # /blueprints/units/power
    recursive_scan: true
  },
  # ... other categories
}
```

**Key Finding**: Both `energy` and `power` are loaded as **separate unit categories**. They are not aliases or synonyms — they are distinct loader keys. Files in `units/energy/` are indexed under the `energy` key, and files in `units/power/` are indexed under the `power` key.

### Module Lookup Service

The `ModuleLookupService` uses a single path:
```ruby
energy: {
  path: -> { base_modules_path.join("energy") },  # /blueprints/modules/energy
}
```

**No `modules/power/` directory exists.** Modules use only the `energy` category.

### Slot Type Evidence

From `metal_smelter_facility_bp`:
- **Unit slots**: `type: "energy"` (count: 3) — structures accept units of type `energy`
- **Module slots**: `type: "power"` (count: 2) — structures accept modules of type `power`

**Conclusion**: The slot system uses `energy` as the unit compatibility category and `power` as the module compatibility category. These are **intentionally different** in the composition model, not interchangeable.

### Directory Generation Script Discrepancy

The original directory-generation script defined:
```
blueprints/units/energy/        ✅ EXISTS (matches)
blueprints/modules/energy/      ✅ EXISTS (matches)
blueprints/rigs/energy/         ✅ EXISTS (matches)
blueprints/rigs/power/          ✅ EXISTS (extra — not in original script)
blueprints/units/power/         ❌ NOT in original script (added later)
blueprints/units/power_generation/  ❌ NOT in original script (added later)
```

**Finding**: `units/power/` and `units/power_generation/` were added after the original directory generation script, suggesting a taxonomy evolution or drift.

---

## 8. Structure/Unit/Rig/Module Composition Evidence

### Factory Structure (`metal_smelter_facility_bp`) Slot Model

```json
{
  "unit_slots": [
    {"type": "production/smelters", "count": 8},
    {"type": "energy", "count": 3},
    {"type": "computers", "count": 2},
    {"type": "life_support", "count": 4},
    {"type": "storage", "count": 3}
  ],
  "module_slots": [
    {"type": "power", "count": 2},
    {"type": "computer", "count": 1},
    {"type": "safety", "count": 2}
  ]
}
```

### Compatible Units/Modules Examples

**Compatible units include**: `{"id"=>"backup_generator", "type"=>"energy"}`
**Compatible modules include**: `{"id"=>"efficiency_optimizer", "type"=>"power"}`, `{"id"=>"output_booster", "type"=>"power"}`

### Key Composition Findings

1. **Structures host units** — via `unit_slots` with type-based compatibility
2. **Structures host modules** — via `module_slots` with type-based compatibility
3. **Rig slots exist on some structures** — e.g., `planetary_umbilical_hub_data.json` has `rig_slots`
4. **Units may host modules** — e.g., `distributed_computing_cluster_bp.json` has `"module_slots": 6`
5. **Slot `type` values are category names** — they match the directory subdirectory names (e.g., `type: "energy"` matches `/units/energy/`)
6. **`energy` and `power` are intentionally different** — units use `energy`, modules use `power`

### Template Evidence

Structure blueprints use `structure_blueprint_v1` template with top-level `unit_slots`, `module_slots`, `rig_slots` arrays.
Unit blueprints use `unit_blueprint` template with nested `container_capacity.module_slots` (integer counts).

---

## 9. Loader and Code References

### Unit Lookup Service (`app/services/lookup/unit_lookup_service.rb`)

- Loads from `ENERGY_UNITS_PATH` and `POWER_UNITS_PATH` independently
- Both use `recursive_scan: true`
- No `POWER_GENERATION_UNITS_PATH` constant defined
- Files in `units/power_generation/` are **not loaded** by this service

### Module Lookup Service (`app/services/lookup/module_lookup_service.rb`)

- Loads from `base_modules_path.join("energy")` only
- No `power` module path defined
- All modules use `energy` category

### Structure Lookup Service (`app/services/lookup/structure_lookup_service.rb`)

- Explicitly references `'power_generation'` for structures:
  ```ruby
  'power_generation' => GalaxyGame::Paths::STRUCTURES_PATH.join('power_generation')
  ```

### Path Constants (`config/initializers/game_data_paths.rb`)

```ruby
ENERGY_UNITS_PATH = UNITS_PATH.join('energy').freeze
POWER_UNITS_PATH = UNITS_PATH.join('power').freeze
# No POWER_GENERATION_UNITS_PATH defined
```

### Energy Management Concern (`app/models/concerns/energy_management.rb`)

- Checks `operational_data.dig('category') == 'energy'` to identify power-generating units
- Sums `power_generation_kw` from operational data
- References `resource_management.generated.energy_kwh.rate` for generation rates

### Unit Deployment Service (`app/services/manufacturing/unit_deployment.rb`)

- Reads `operational_data.dig('power_generation', 'output_kw')` and `fuel_type`
- Uses `power_generation` as an **operational data key**, not a directory category

---

## 10. JSON Syntax Validation Results

### Errors Found (9 total)

| File | Error |
|---|---|
| `blueprints/units/habitats/small_habitat_bp.json` | expected ',' or '}' after object value |
| `generated_star_systems/djew-716790.json` | expected ',' or '}' after object value |
| `generated_star_systems/fr-488530.json` | expected object key |
| `items/components/structural/3d_printed_ibeam_mk1.json` | unexpected token at end of stream |
| `items/components/structural/3d_printed_ibeam_mk2.json` | unexpected token at end of stream |
| `items/components/structural/3d_printed_ibeam_mk3.json` | unexpected token at end of stream |
| `missions/puck_conversion_hub/puck_conversion_hub_profile.json` | expected ',' or '}' after object value |
| `missions/tasks_v2/ai_decision_framework_and_manifest_optimization.json` | got '>' character |
| `missions/venus_settlement/phases/04_industrial_integration.json` | expected object key |

**None of the JSON errors are in power/energy/power_generation files.** The affected files are in habitats, star systems, items, and missions directories.

---

## 11. Recommended Canonical Taxonomy

### Proposed Category Hierarchy

```
power_data/
├── units/
│   ├── energy/           # Power-generating units (solar, nuclear, fusion, RTG)
│   ├── power/            # Power-distribution/storage units (panels, batteries, controllers)
│   └── power_generation/ # [DEPRECATED — migrate to units/energy or units/power]
├── modules/
│   └── energy/           # Power-related modules (efficiency, emergency backup)
│       # NOTE: No modules/power/ directory exists; all modules use energy category
├── rigs/
│   ├── energy/           # Power-related rigs
│   └── power/            # Power-related rigs (solar expansion, etc.)
└── structures/
    └── power_generation/ # Power-generating structures (empty — no files)
```

### Rationale

1. **`energy` = generation category** — Units that generate power (solar panels, reactors, RTGs) belong in `units/energy/`
2. **`power` = distribution/storage category** — Units that store or distribute power (batteries, controllers, arrays) belong in `units/power/`
3. **`modules` use only `energy`** — No module-level `power` directory exists; all power modules are in `modules/energy/`
4. **`power_generation` is a payload category, not a directory** — Files have `category: "power_generation"` in their metadata regardless of which directory they're stored in
5. **Slot types match directory categories** — `type: "energy"` for units, `type: "power"` for modules

### Migration Priority

| Issue | Priority | Action |
|---|---|---|
| `power_controller` duplicate (3 locations) | CRITICAL | Consolidate to single ID or namespace |
| `solar_panel` duplicate (2 locations) | CRITICAL | Consolidate to single ID or namespace |
| Files with directory/category mismatch | HIGH | Update metadata or move files |
| `units/power_generation/` not loaded by lookup | MEDIUM | Add loader entry or migrate files |
| Empty `structures/power_generation/` directory | LOW | Remove if intentional, populate if not |

---

## 12. Proposed Migration Plan (Advisory Only — Do Not Apply)

### Phase 1: Resolve Duplicate IDs (CRITICAL)

1. **`power_controller`** — Keep one instance (recommend `units/power/power_controller_bp.json` as canonical), remove or rename others
2. **`solar_panel`** — Keep one instance (recommend `units/energy/solar_panel_bp.json` as canonical for generation), rename the other to `solar_panel_array` or similar

### Phase 2: Fix Directory/Category Mismatches (HIGH)

Move files where directory does not match payload category:
- `compact_nuclear_reactor_bp.json` — currently in `units/power/`, category is `power_generation` → move to `units/energy/` (generation) or update category to `power`
- `mars_industrial_nuclear_reactor_bp.json` — same issue as above
- `planetary_power_management_unit_bp.json` — currently in `units/energy/`, category is `power_generation` → evaluate if it should be in `units/power/` (management/distribution)
- `power_controller_bp.json` (units/energy/) — same evaluation needed

### Phase 3: Loader Alignment (MEDIUM)

1. Add `power_generation` as a loader key in `UnitLookupService` if files in `units/power_generation/` need to be loaded
2. OR migrate `nuclear_micro_reactor_mk1_bp.json` from `units/power_generation/` to `units/energy/` (it's a reactor = generation)
3. Document the distinction between `energy` (generation) and `power` (distribution/storage) in architecture docs

### Phase 4: Populate or Remove Empty Directories (LOW)

1. If `structures/power_generation/` is intentional, add structure blueprints for power-generating mega-structures
2. If not, remove the empty directory to reduce confusion

---

## 13. Files Requiring Human Decisions

| File/Issue | Decision Needed |
|---|---|
| `power_controller` (3 copies) | Which copy is canonical? Merge or rename? |
| `solar_panel` (2 copies) | Which copy is canonical? Are they different products? |
| `compact_nuclear_reactor` vs `mars_industrial_nuclear_reactor` | Are these separate products or variants? Both use `component_blueprint` template |
| `nuclear_micro_reactor_mk1` template | Should it use `unit_blueprint` (current) or `component_blueprint` (like other reactors)? |
| `units/power_generation/` loader gap | Add loader entry, migrate files, or remove directory? |
| Reactor specs missing | All three reactors lack thermal/electrical output, mass, volume — who provides gameplay values? |

---

## 14. Documentation Gaps

| Gap | Location | Impact |
|---|---|---|
| No taxonomy documentation explaining energy vs power distinction | None found | High — causes directory drift |
| `power_generation` not in loader constants | `config/initializers/game_data_paths.rb` | Medium — files in this directory are invisible to lookup |
| Reactor specs missing from all three reactor blueprints | `blueprints/units/power/`, `blueprints/units/power_generation/` | High — reactors cannot be deployed without specs |
| No record of why `units/power/` and `units/power_generation/` were added | Git history only | Low — for future reference |
| Directory-generation script does not match current structure | Original script vs actual filesystem | Medium — new files may follow incorrect conventions |

---

## 15. Stop Conditions Assessment

| Condition | Status |
|---|---|
| Loader semantics contradict folder taxonomy | ⚠️ PARTIAL — `power_generation` category exists in metadata but has no loader entry |
| Duplicate IDs found | ✅ YES — 8 duplicate pairs identified, 2 CRITICAL |
| Reactor output units cannot be interpreted | ✅ YES — all three reactors have NO specs (no thermal/electrical output values) |
| Source or data files modified accidentally | ✅ NO — read-only investigation confirmed |
| Docker container or data path unavailable | ✅ NO — container running, paths accessible |

---

## Recommendations Summary

1. **Do NOT merge `energy` and `power`** — they serve different roles in the composition model (units vs modules)
2. **Resolve duplicate IDs before any migration** — loader collisions will occur if both copies are loaded
3. **Add specs to reactor blueprints** — all three are metadata shells with no operational data
4. **Align `units/power_generation/` with loader** — either add a loader entry or migrate files to `units/energy/`
5. **Document the taxonomy** — create architecture docs explaining energy (generation) vs power (distribution/storage) distinction
6. **Fix directory/category mismatches** — update metadata or move files to match their payload category

---

**RESEARCH COMPLETE.** Human review required before any migration actions.

