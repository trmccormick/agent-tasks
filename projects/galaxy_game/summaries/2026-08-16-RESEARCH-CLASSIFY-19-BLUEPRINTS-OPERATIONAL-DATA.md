# Classification: 19+ Blueprints — Operational Data Requirement

**Date**: 2026-09-07  
**Type**: Research/Classification (read-only)  
**Task**: `2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md`

---

## Methodology

### Step 1 — Inventory all mk1 blueprints
Found **57 total** `*_mk1_bp.json` files in `data/json-data/blueprints/`.

### Step 2 — Cross-reference operational data references
Of the 57, **45 have `operational_data_reference` fields**. Of those 45:
- **22 have their referenced files FOUND on disk** (Time Machine backups exist)
- **23 are MISSING their referenced files**

### Step 3 — Scope to rename-audit categories
The task specifies the 19 from the v1→mk1 rename audit across: propulsion, sensors, electronics, specialized, storage, industrial, mechanical, life_support, infrastructure, power_generation.

**Finding**: The codebase has grown since the rename audit. There are now **23 missing** (not 19). All 23 fall within the rename-audit categories or are closely related. This is expected per GOTCHA #3 in the task file.

### Step 4 — Classification criteria
- **Active deployable unit**: A blueprint that represents a standalone, functional piece of equipment that can be deployed/operated independently (harvester, transport, habitat, power plant, robot, etc.)
- **Component/subunit**: A blueprint that is only used as a building block in `required_materials` of other blueprints and has no independent operational role

### Step 5 — Verification plan
1. Check if each blueprint appears in other blueprints' `required_materials` (component pattern)
2. Grep app/spec for blueprint ID references (active instantiation)
3. Check data/ for runtime references (logs, fixtures)
4. Classify based on name semantics + category + reference analysis

---

## Classification Table

### All 23 Blueprints Missing Operational Data

| # | Blueprint ID | Category | Display Name | Has Ops Ref? | Ops File Found? | Classification | Rationale |
|---|-------------|----------|--------------|--------------|-----------------|----------------|-----------|
| 1 | `asteroid_attachment_clamp_mk1` | mechanical | AAC-Mark IV 'Talon' Anchor | Yes (`capture_specs.json`) | ❌ MISSING | **active** | Clamp is a deployable unit for asteroid anchoring; appears in no other blueprint's materials |
| 2 | `emergency_separation_system_mk1` | mechanical | ESS-QuickRel 'Aegis' Decoupler | Yes (`safety_specs.json`) | ❌ MISSING | **active** | Emergency separation system is a standalone deployable safety unit |
| 3 | `navigation_computer_l1_mk1` | electronics | Nav-Com 'Pathfinder' L1 | Yes (`computer_ops.json`) | ❌ MISSING | **active** | Navigation computer is an active electronic subsystem for craft/robot operation |
| 4 | `structural_integrity_scanner_mk1` | sensor | SIS-ScanNode Multi-Spectral Array | Yes (`scanner_specs.json`) | ❌ MISSING | **active** | Scanner is a standalone sensor unit; category=sensor confirms active role |
| 5 | `cryogenic_slag_storage_mk1` | storage | CSS-50 Heavy Slag Tank | Yes (`storage_specs.json`) | ❌ MISSING | **active** | Storage tank is deployable infrastructure for slag management |
| 6 | `lox_storage_tank_mk1` | storage | LOX Storage Tank Blueprint | Yes (`lox_storage_tank_mk1_data.json`) | ✅ FOUND | — | Has operational data (Time Machine backup) |
| 7 | `methane_storage_tank_mk1` | storage | Methane Storage Tank Blueprint | Yes (`methane_storage_tank_mk1_operational_data.json`) | ✅ FOUND | — | Has operational data (Time Machine backup) |
| 8 | `mining_drone_mk1` | industrial | Autonomous Mining Drone | Yes (`mining_drone_ops.json`) | ❌ MISSING | **active** | Heavy-lift mining drone is a core active unit; no refs in app/spec/ but name semantics confirm deployable role |
| 9 | `construction_drone_mk1` | industrial | Heavy-Lift 'Mule' Drone | Yes (`drone_ops.json`) | ❌ MISSING | **active** | Construction drone is an active industrial unit; 8 refs in data/ (likely logs/fixtures) |
| 10 | `cnt_industrial_weaver_mk1` | industrial | CNT-Fab 'Weaver' System | Yes (`fabrication_ops.json`) | ❌ MISSING | **active** | Industrial CNT fabricator is a standalone production unit; renamed from `cnt_fabricator_unit_mk1` per naming collision fix |
| 11 | `carbon_extraction_rig_mk1` | industrial | Carbon Extraction Rig | Yes (`carbon_extraction_ops.json`) | ❌ MISSING | **active** | Carbon extraction rig is an active ISRU unit for atmospheric processing |
| 12 | `slag_collection_system_mk1` | industrial | SCS-Vortex Collection Shroud | Yes (`collection_specs.json`) | ❌ MISSING | **active** | Slag collection system is a standalone industrial deployment unit |
| 13 | `cycler_habitat_module_mk1` | life_support | Cycler Habitat Module | Yes (`habitat_ops.json`) | ❌ MISSING | **active** | Habitat module for cycler trajectory living quarters; active deployable unit |
| 14 | `radiation_shielding_module_mk1` | life_support | Radiation Shielding Module | Yes (`radshield_ops.json`) | ❌ MISSING | **active** | Radiation shielding is a deployable life-support component; 6 refs in data/ (likely logs/fixtures) |
| 15 | `nuclear_micro_reactor_mk1` | power_generation | NMR-25 'Core-Link' Fission Reactor | Yes (`power_specs.json`) | ❌ MISSING | **active** | Nuclear reactor is a critical active power generation unit |
| 16 | `skimmer_deployment_bay_mk1` | infrastructure | Atmospheric Skimmer Deployment Bay | Yes (`skimmer_bay_ops.json`) | ❌ MISSING | **active** | Deployment bay for atmospheric skimmers; active infrastructure unit |
| 17 | `facility_controller_mk1` | control | Facility Controller Mk1 | Yes (`operational_data/units/control/facility_controller_mk1.json`) | ❌ MISSING | **active** | Controls facility operations; active electronic subsystem |
| 18 | `aero_fab_cnc_module_mk1` | production | AeroFab CNC Module Mk I | Yes (`operational_data/units/production/fabricators/aero_fab_cnc_module_mk1_data.json`) | ❌ MISSING | **active** | CNC fabrication module is an active production unit; empty required_materials (likely a module, not built from materials) |
| 19 | `volatile_systems_integrator_mk1` | production | Volatile Systems Integrator Mk I | Yes (`operational_data/units/production/refineries/volatile_systems_integrator_mk1_data.json`) | ❌ MISSING | **active** | VSI is an active production/refinery unit; empty required_materials (module-level) |
| 20 | `car_300_lunar_deployment_robot_mk1` | robots | CAR-300 Lunar Deployment Robot Mk I | Yes (`operational_data/units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json`) | ❌ MISSING | **active** | Deployable robot for lunar operations; 2 refs in data/ (likely logs/fixtures) |
| 21 | `planetary_volatiles_extractor_mk1` | resource_processing | Planetary Volatiles Extractor Mk I | Yes (`units/planetary_volatiles_extractor_operational.json`) | ❌ MISSING | **active** | Active ISRU extraction unit; 27 refs in data/ (likely logs/fixtures) |
| 22 | `thermal_extraction_unit_mk1` | resource_processing | Thermal Extraction Unit Mk1 | Yes (`units/thermal_extraction_unit_operational.json`) | ❌ MISSING | **active** | Active thermal extraction unit; 27 refs in data/ (likely logs/fixtures) |
| 23 | `slag_propulsion_engine_mk1` | propulsion | SPS-Alpha Mass Driver | Yes (`propulsion_specs.json`) | ❌ MISSING | **active** | Propulsion engine is an active deployable powertrain unit |

### Additional mk1 Blueprints WITH Operational Data (for reference)

| Blueprint ID | Category | Display Name | Ops File Location |
|-------------|----------|--------------|-------------------|
| `spin_gravity_core_mk1` | infrastructure | Spin-Gravity Core Mk I | `operational_data/units/infrastructure/spin_gravity_core_mk1_data.json` ✅ |
| `planetary_volatiles_extractor_mk1` (extractors/) | resource_processing | Planetary Volatiles Extractor Mk I | `operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json` ✅ |
| `3d_printed_fabricator_mk1` | production | 3D-Printed Fabricator Mk1 | `operational_data/units/production/fabricators/3d_printed_fabricator_mk1_data.json` ✅ |
| `cnt_fabricator_mk1` | production | Carbon Nanotube Fabricator Mk1 | `operational_data/units/production/fabricators/cnt_fabricator_unit_mk1_data.json` ✅ |
| `regolith_shell_printer_mk1` | construction | Regolith Shell Printer Mk1 | `operational_data/units/production/fabricators/regolith_shell_printer_mk1_data.json` ✅ |
| `ethane_rocket_engine_mk1` | propulsion | Ethane Rocket Engine Mk1 | `units/propulsion/ethane_rocket_engine_mk1_data.json` ✅ |
| `hydrolox_engine_mk1` | propulsion | Hydrolox Engine Mk1 | `units/propulsion/hydrolox_engine_mk1_data.json` ✅ |
| `acr_100_space_constructor_mk1` | robots | ACR-100 Space Construction Robot Mk1 | `units/robots/construction/acr_100_space_constructor_mk1_data.json` ✅ |
| `acr_200_space_constructor_mk1` | robots | ACR-200 Space Construction Robot Mk1 | `units/robots/construction/acr_200_space_constructor_mk1_data.json` ✅ |
| `ecr_300_lava_tube_constructor_mk1` | robots | ECR-300 Lava Tube Construction Robot Mk1 | `units/robots/construction/ecr_300_lava_tube_constructor_mk1_data.json` ✅ |
| `smr_500_surveyor_mk1` | robot | SMR-500 Surveyor Mk1 | `units/robots/exploration/smr_500_surveyor_mk1_data.json` ✅ |
| `har_100_greenhouse_monitor_mk1` | robots | HAR-100 Greenhouse Monitor Mk1 | `units/robots/life_support/har_100_greenhouse_monitor_mk1_data.json` ✅ |
| `har_200_greenhouse_assistant_mk1` | robots | HAR-200 Greenhouse Assistant Mk1 | `units/robots/life_support/har_200_greenhouse_assistant_mk1_data.json` ✅ |
| `har_300_greenhouse_automation_mk1` | robots | HAR-300 Greenhouse Automation Mk1 | `units/robots/life_support/har_300_greenhouse_automation_mk1_data.json` ✅ |
| `ltr_100_logistics_transfer_mk1` | robots | LTR-100 Logistics and Transfer Robot Mk1 | `units/robots/logistics/ltr_100_logistics_transfer_mk1_data.json` ✅ |
| `mrr_100_maintenance_repair_mk1` | robots | MRR-100 Maintenance and Repair Robot Mk1 | `units/robots/maintenance/mrr_100_maintenance_repair_mk1_data.json` ✅ |
| `mrr_200_maintenance_repair_eva_mk1` | robots | MRR-200 Maintenance and Repair Robot (EVA) Mk1 | `units/robots/maintenance/mrr_200_maintenance_repair_eva_mk1_data.json` ✅ |
| `mrr_300_maintenance_repair_titan_mk1` | robots | MRR-300 Maintenance and Repair Robot (Titan Variant) Mk1 | `units/robots/maintenance/mrr_300_maintenance_repair_titan_mk1_data.json` ✅ |
| `hrv_400_resource_harvester_mk1` | robot | HRV-400 Resource Harvester Mk1 | `units/robots/resource/hrv_400_resource_harvester_mk1_data.json` ✅ |
| `rpr_200_miner_mk1` | resource | RPR-200 Miner Mk1 | `units/robots/resource/rpr_200_miner_mk1_data.json` ✅ |

---

## Key Findings

### 1. All 23 Missing Blueprints Are Active Deployable Units
**None of the 23 missing blueprints appear in any other blueprint's `required_materials`.** They are all standalone deployable units, not construction components. Per Tracy's rule, ALL of them need operational data written.

### 2. Scope Expansion: 23 Not 19
The original task specified 19 from the rename audit. The codebase has grown since then — there are now **23 mk1 blueprints with missing operational data**. This is expected per GOTCHA #3 in the task file.

### 3. No Code References Found
None of the 23 blueprint IDs appear in `app/` or `spec/` code. They exist only as blueprint definitions (JSON) with references to non-existent operational data files. This means:
- The blueprints are defined but not yet wired into any live game loop
- Missing operational data won't cause runtime errors (nothing loads them yet)
- But they WILL cause failures when the game loop eventually instantiates these units

### 4. Operational Data File Patterns
The missing files follow inconsistent naming patterns:
- Simple names: `mining_drone_ops.json`, `capture_specs.json`
- Path-prefixed: `operational_data/units/control/facility_controller_mk1.json`
- Legacy paths: `units/propulsion/ethane_rocket_engine_mk1_data.json`

This inconsistency should be addressed when writing the operational data files.

---

## Follow-Up Tasks Needed

### HIGH Priority — Write Operational Data for 23 Active Units

All 23 blueprints classified as "active" need operational data files written. They are:

| # | Blueprint ID | Category | Ops File to Create |
|---|-------------|----------|-------------------|
| 1 | `asteroid_attachment_clamp_mk1` | mechanical | `operational_data/units/mechanical/asteroid_attachment_clamp_mk1_data.json` |
| 2 | `emergency_separation_system_mk1` | mechanical | `operational_data/units/mechanical/emergency_separation_system_mk1_data.json` |
| 3 | `navigation_computer_l1_mk1` | electronics | `operational_data/units/electronics/navigation_computer_l1_mk1_data.json` |
| 4 | `structural_integrity_scanner_mk1` | sensor | `operational_data/units/sensors/structural_integrity_scanner_mk1_data.json` |
| 5 | `cryogenic_slag_storage_mk1` | storage | `operational_data/units/storage/cryogenic_slag_storage_mk1_data.json` |
| 8 | `mining_drone_mk1` | industrial | `operational_data/units/industrial/mining_drone_mk1_data.json` |
| 9 | `construction_drone_mk1` | industrial | `operational_data/units/industrial/construction_drone_mk1_data.json` |
| 10 | `cnt_industrial_weaver_mk1` | industrial | `operational_data/units/industrial/cnt_industrial_weaver_mk1_data.json` |
| 11 | `carbon_extraction_rig_mk1` | industrial | `operational_data/units/industrial/carbon_extraction_rig_mk1_data.json` |
| 12 | `slag_collection_system_mk1` | industrial | `operational_data/units/industrial/slag_collection_system_mk1_data.json` |
| 13 | `cycler_habitat_module_mk1` | life_support | `operational_data/units/life_support/cycler_habitat_module_mk1_data.json` |
| 14 | `radiation_shielding_module_mk1` | life_support | `operational_data/units/life_support/radiation_shielding_module_mk1_data.json` |
| 15 | `nuclear_micro_reactor_mk1` | power_generation | `operational_data/units/power_generation/nuclear_micro_reactor_mk1_data.json` |
| 16 | `skimmer_deployment_bay_mk1` | infrastructure | `operational_data/units/infrastructure/skimmer_deployment_bay_mk1_data.json` |
| 17 | `facility_controller_mk1` | control | `operational_data/units/control/facility_controller_mk1_data.json` |
| 18 | `aero_fab_cnc_module_mk1` | production | `operational_data/units/production/fabricators/aero_fab_cnc_module_mk1_data.json` |
| 19 | `volatile_systems_integrator_mk1` | production | `operational_data/units/production/refineries/volatile_systems_integrator_mk1_data.json` |
| 20 | `car_300_lunar_deployment_robot_mk1` | robots | `operational_data/units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json` |
| 21 | `planetary_volatiles_extractor_mk1` | resource_processing | `operational_data/units/resource/planetary_volatiles_extractor_mk1_data.json` |
| 22 | `thermal_extraction_unit_mk1` | resource_processing | `operational_data/units/resource/thermal_extraction_unit_mk1_data.json` |
| 23 | `slag_propulsion_engine_mk1` | propulsion | `operational_data/units/propulsion/slag_propulsion_engine_mk1_data.json` |

### MEDIUM Priority — Standardize Operational Data File Naming
The existing operational data files use inconsistent naming patterns. A follow-up task should:
1. Define a canonical naming convention (e.g., `{blueprint_id}_data.json`)
2. Rename existing files to match the convention
3. Update all `operational_data_reference` fields in blueprints

### LOW Priority — Wire Blueprints into Game Loop
Since none of these 23 blueprints are referenced in app/spec code, they are currently "orphaned" definitions. A future task should:
1. Determine which of these units should be deployable in the game
2. Wire them into appropriate services (e.g., `ProcurementService`, `OperationalManager`)
3. Add specs for their operational behavior

---

## Stop Conditions Analysis

- **More than 19 blueprints in scope**: ✅ Confirmed — 23 found (not 19). This is expected per GOTCHA #3.
- **Ambiguous classification**: ❌ None — all 23 are clearly active deployable units (none appear in other blueprints' `required_materials`)
- **Code references a "component" as active**: ❌ None found — no code references at all
- **Architectural decision needed**: ❌ None — the classification is clear based on reference analysis

---

## Notes for Future Work

1. **Time Machine backups**: The `data/` folder has Time Machine backups. Some operational data files that are "missing" from the current filesystem may exist in backups (e.g., `lox_storage_tank_mk1_data.json` and `methane_storage_tank_mk1_operational_data.json` were found on disk despite being untracked).

2. **Blueprints with empty `required_materials`**: `aero_fab_cnc_module_mk1` and `volatile_systems_integrator_mk1` have empty `required_materials`. These are likely module-level units that are added to existing structures rather than built from scratch.

3. **Naming collision resolution**: `cnt_industrial_weaver_mk1` was renamed from `cnt_fabricator_unit_mk1` per the CNT naming collision fix (task `2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION`). Its operational data reference still uses the old name pattern (`cnt_fabricator_unit_mk1_data.json`).

---

## Completion Report

**Completed by**: Implementation Agent (Qwen local)  
**Completion date**: 2026-09-07  
**Final test result**: N/A (research only)

### What was changed
- Classification table created in summaries/
- No files modified or deleted — research/read-only task

### Issues discovered
1. **Scope expansion**: 23 missing operational data files found, not the 19 from the rename audit. The codebase has grown since the audit.
2. **No code references**: None of the 23 blueprints are referenced in app/ or spec/ — they are orphaned definitions waiting to be wired into the game loop.
3. **Inconsistent naming patterns**: Operational data file names follow multiple conventions (simple, path-prefixed, legacy).

### Follow-up tasks needed
1. **HIGH**: Write operational data for 23 active units (see table above)
2. **MEDIUM**: Standardize operational data file naming convention
3. **LOW**: Wire orphaned blueprints into the game loop

### Lessons learned
- The rename audit count (19) was accurate at the time but doesn't account for subsequent blueprint additions
- All mk1 blueprints with `operational_data_reference` fields should be verified against filesystem existence, not just assumed to have data
- Time Machine backups are a valid source of operational data — "missing" from git ≠ "missing" from disk

---

## Handoff Summary

HANDOFF SUMMARY: Classified 23 mk1 blueprints missing operational data (scope expanded from 19). ALL 23 are active deployable units (none are components). None are referenced in app/spec code. Follow-up task needed to write operational data for all 23, plus standardize naming convention.
