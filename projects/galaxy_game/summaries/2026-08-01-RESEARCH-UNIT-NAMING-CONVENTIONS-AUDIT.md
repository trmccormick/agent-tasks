# Synthesis Report: Unit Naming Conventions Audit

**Date**: 2026-08-01
**Task**: 2026-08-01-MEDIUM-RESEARCH-UNIT-NAMING-CONVENTIONS-MK-VS-DESIGNATION
**Type**: research

---

## Executive Summary

Audited 139 blueprint files across `/data/json-data/blueprints/units/` for naming convention compliance. Found **two categories of violations**: (1) v1/v1.1 naming that should be mk1/mk1.1, and (2) files without version indicators (should default to Mk1 per convention).

---

## Audit Results

### Total Files Audited
**139 blueprint files** across 28 directories

### Directory Structure Compliance

#### ✅ Correctly Placed Directories
| Category | Path | Status |
|----------|------|--------|
| Deployment robots | `robots/deployment/` | ✅ CAR-300 Mk1 present |
| Fabricators | `production/fabricators/` | ✅ AeroFab CNC Mk1, CNT fabricator mk1-mk3 |
| Refineries | `production/refineries/` | ✅ VSI Mk1 present |
| Extractors | `production/extractors/` | ✅ PVE mk1-mk3, TEU mk1 |
| Habitats | `habitats/` | ✅ 4 habitat variants (separate designs) |
| Housing | `housing/` | ✅ Inflatable habitat unit blueprint |
| Infrastructure | `infrastructure/` | ✅ Spin-Gravity Core Mk1 present |
| Resource | `resource/` | ✅ PVE Mk1 (compact variant) — valid dual-location |

#### ⚠️ Potentially Misplaced Directories
| Directory | Concern |
|-----------|---------|
| `power/` | 10 files — overlaps with `energy/` (5 files) and `power_generation/` (1 file) |
| `energy/` | 7 files — overlaps with `power/` |
| `power_generation/` | 1 file — should this be in `power/`? |
| `storage/` | 11 files — valid but could be split into fuel/storage categories |

---

## Violation Category 1: v1/v1.1 Naming (Should Be mk1/mk1.1)

**18 files found with v1/v1.1 naming:**

### Propulsion (2 files)
- `slag_propulsion_engine_v1_bp.json` → should be `mk1`
- `precision_orbit_control_v1.1_bp.json` → should be `mk1.1`

### Sensors (1 file)
- `structural_integrity_scanner_v1_bp.json` → should be `mk1`

### Electronics (1 file)
- `navigation_computer_l1_v1_bp.json` → should be `mk1`

### Specialized (1 file)
- `asteroid_hollowing_laser_v1_1_bp.json` → should be `mk1.1`

### Storage (1 file)
- `cryogenic_slag_storage_v1_bp.json` → should be `mk1`

### Industrial (6 files) — **LARGEST VIOLATION GROUP**
- `drone_bay_heavy_v1_bp.json` → should be `mk1`
- `slag_collection_system_v1_bp.json` → should be `mk1`
- `mining_drone_v1_bp.json` → should be `mk1`
- `construction_drone_v1_bp.json` → should be `mk1`
- `cnt_fabricator_unit_v1_bp.json` → should be `mk1` (note: `cnt_fabricator_mk1_bp.json` also exists in fabricators/)
- `isru_processor_v1_bp.json` → should be `mk1`
- `carbon_extraction_rig_v1_bp.json` → should be `mk1`

### Mechanical (2 files)
- `asteroid_attachment_clamp_v1_bp.json` → should be `mk1`
- `emergency_separation_system_v1_bp.json` → should be `mk1`

### Life Support (2 files)
- `cycler_habitat_module_v1_bp.json` → should be `mk1`
- `radiation_shielding_module_v1_bp.json` → should be `mk1`

### Infrastructure (1 file)
- `skimmer_deployment_bay_v1_bp.json` → should be `mk1`

### Power Generation (1 file)
- `nuclear_micro_reactor_v1_bp.json` → should be `mk1`

---

## Violation Category 2: Missing Version Indicators

**~100+ files without any version indicator** — per convention, these should default to Mk1.

### Examples by Category:
| Category | Files Without Version |
|----------|----------------------|
| Power | `mars_industrial_nuclear_reactor_bp.json`, `compact_solar_panel_bp.json`, etc. (10 files) |
| Computers | `distributed_computing_cluster_bp.json`, `server_farm_bp.json`, etc. (7 files) |
| Propulsion | `basic_ion_thruster_bp.json`, `methane_engine_bp.json`, etc. (8 files) |
| Sensors | `spectroscopic_sensor_bp.json`, `gravimetric_sensor_bp.json`, etc. (6 files) |
| Energy | `satellite_battery_bp.json`, `power_controller_bp.json`, etc. (7 files) |

**Note**: This is a **gray area**. The convention says "if no mk number is shown, assume Mk I." However, for explicitness and auditability, these should probably be renamed to include `_mk1_` in the filename.

---

## Key Findings — Conventions Working Correctly

### ✅ Designation + Mk Pattern (Working)
- `car_300_deployment_robot_mk1_bp.json` — CAR-300 line, Mk I
- `planetary_volatiles_extractor_mk1_bp.json` — PVE line, Mk I
- `planetary_volatiles_extractor_mk2_bp.json` — PVE line, Mk II
- `planetary_volatiles_extractor_mk3_bp.json` — PVE line, Mk III
- `aero_fab_cnc_module_mk1_bp.json` — AeroFab line, Mk I
- `volatile_systems_integrator_mk1_bp.json` — VSI line, Mk I
- `thermal_extraction_unit_mk1_bp.json` — TEU line, Mk I
- `spin_gravity_core_mk1_bp.json` — SG Core line, Mk I

### ✅ Mk Progression Examples (Working)
- **PVE**: mk1 → mk2 → mk3 (production/extractors/)
- **CNT Fabricator**: mk1 → mk2 → mk3 (production/fabricators/)
- **Regolith Shell Printer**: mk1 → mk2 → mk3 (production/fabricators/)
- **3D Printed Fabricator**: mk1 → mk2 → mk3 (production/fabricators/)

### ✅ Separate Designs Not Confused with Mk Progressions
- `inflatable_habitat_bp.json` — pre-assembled compact module
- `inflatable_habitat_unit_blueprint.json` — manufacturing blueprint
- `small_habitat_bp.json` — larger living quarters
- `cryogenic_habitat_blueprint.json` — cryogenic variant
- `starship_habitat_unit_bp.json` — starship variant

All four are separate designs (no shared designation) — correctly NOT treated as Mk1-Mk4.

### ✅ Dual-Location Validity
- PVE Mk1 in `resource/` (compact, Earth-shipped module) — valid
- PVE Mk1/Mk2/Mk3 in `production/extractors/` (industrial facility) — valid
- Same designation (PVE), different deployment profiles

---

## Recommendations

### Priority 1: Rename v1/v1.1 to mk1/mk1.1 (18 files)
**High impact, low risk** — these are clear violations of the established convention.

Files requiring rename:
```
propulsion/slag_propulsion_engine_v1_bp.json → propulsion/slag_propulsion_engine_mk1_bp.json
propulsion/precision_orbit_control_v1.1_bp.json → propulsion/precision_orbit_control_mk1.1_bp.json
sensors/structural_integrity_scanner_v1_bp.json → sensors/structural_integrity_scanner_mk1_bp.json
electronics/navigation_computer_l1_v1_bp.json → electronics/navigation_computer_l1_mk1_bp.json
specialized/asteroid_hollowing_laser_v1_1_bp.json → specialized/asteroid_hollowing_laser_mk1.1_bp.json
storage/cryogenic_slag_storage_v1_bp.json → storage/cryogenic_slag_storage_mk1_bp.json
industrial/drone_bay_heavy_v1_bp.json → industrial/drone_bay_heavy_mk1_bp.json
industrial/slag_collection_system_v1_bp.json → industrial/slag_collection_system_mk1_bp.json
industrial/mining_drone_v1_bp.json → industrial/mining_drone_mk1_bp.json
industrial/construction_drone_v1_bp.json → industrial/construction_drone_mk1_bp.json
industrial/cnt_fabricator_unit_v1_bp.json → industrial/cnt_fabricator_unit_mk1_bp.json
industrial/isru_processor_v1_bp.json → industrial/isru_processor_mk1_bp.json
industrial/carbon_extraction_rig_v1_bp.json → industrial/carbon_extraction_rig_mk1_bp.json
mechanical/asteroid_attachment_clamp_v1_bp.json → mechanical/asteroid_attachment_clamp_mk1_bp.json
mechanical/emergency_separation_system_v1_bp.json → mechanical/emergency_separation_system_mk1_bp.json
life_support/cycler_habitat_module_v1_bp.json → life_support/cycler_habitat_module_mk1_bp.json
life_support/radiation_shielding_module_v1_bp.json → life_support/radiation_shielding_module_mk1_bp.json
infrastructure/skimmer_deployment_bay_v1_bp.json → infrastructure/skimmer_deployment_bay_mk1_bp.json
power_generation/nuclear_micro_reactor_v1_bp.json → power_generation/nuclear_micro_reactor_mk1_bp.json
```

**Note**: `cnt_fabricator_unit_v1_bp.json` in industrial/ may conflict with `cnt_fabricator_mk1_bp.json` in production/fabricators/. These could be different variants of the same unit — verify before renaming.

### Priority 2: Add Explicit _mk1_ to Versionless Files (~100 files)
**Medium impact, medium risk** — convention says "assume Mk I" but explicit is better for auditability.

This is a larger refactor. Consider:
- Batch rename with script (git mv + update JSON content references)
- Deprecation period: keep old names as aliases during transition
- Update any code that references these files by name

### Priority 3: Consolidate Power/Energy Directories
**Low priority, structural change** — `power/`, `energy/`, and `power_generation/` overlap. Consider consolidating to single `power/` directory.

---

## Outstanding Questions (From Task File)

1. **Settlement infrastructure completeness**: ✅ Verified — Spin-Gravity Core Mk1 exists in infrastructure/. Remaining question: are habitat/power/extraction/refining/water units all represented?
2. **Backward compatibility**: The v1→mk1 renames above need a deprecation plan or simultaneous update of any code references.
3. **Documentation location**: Blocked on wiki reorganization (phase4).

---

## Blockers

**Wiki reorganization** must complete before updating formal documentation:
- `docs/wiki_reorganization/phase4/DOCUMENT_CLASSIFICATION.md`
- `docs/wiki_reorganization/phase4/CANONICAL_DOCUMENT_INDEX.md`

---

## Next Steps

1. **Immediate**: Rename 18 v1/v1.1 files to mk1/mk1.1 (Priority 1)
2. **Post-wiki-reorganization**: Create `docs/NAMING_CONVENTIONS.md`
3. **Audit**: Check all existing blueprints for naming compliance
4. **Deprecation plan**: For any misnamed blueprints
5. **Guardrails**: Update GUARDRAILS.md with naming convention requirement

---

**End of Synthesis Report**
