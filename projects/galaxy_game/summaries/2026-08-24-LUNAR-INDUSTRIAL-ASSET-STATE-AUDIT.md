# Lunar Industrial / Construction Asset State Audit

**Date:** 2026-08-24  
**Type:** READ-ONLY discovery audit — no modifications made  
**Scope:** Existing Lunar industrial/construction ecosystem assets in the repository  

---

## 1. Scope

### Directories Searched

| Directory | Status |
|---|---|
| `data/json-data/blueprints/` | Searched recursively (all subdirectories) |
| `data/json-data/operational_data/` | Searched recursively (all subdirectories) |
| `data/json-data/missions/luna_base_establishment/` | Searched recursively |
| `data/json-data/missions/npc-base-deploy/` | Searched recursively |
| `data/json-data/missions_v2/tasks/` | All files read |
| `data/json-data/items/components/structural/` | All files listed |
| `data/json-data/items/equipment/industrial/` | All files listed |
| `data/json-data/items/consumable/` | Subdirectories listed |
| `data/json-data/templates/` | All template files listed |
| `data/images/catalog/components/structural/` | All files listed |
| `data/images/catalog/units/production/fabricators/` | All files listed |
| `data/images/catalog/units/production/extractors/` | All files listed |
| `data/images/catalog/crafts/ground/` | All files listed |
| `data/images/catalog/` (all other subdirs) | Listed — most empty |

### Source Files Read in Full

- `blueprints/components/structural/3d_printed_ibeam_mk1_bp.json` (v1.3)
- `blueprints/components/structural/3d_printed_ibeam_mk5_bp.json` (v1.4)
- `blueprints/components/structural/3d_printed_regolith_panel_mk1_bp.json` (v1.3)
- `blueprints/components/structural/3d_printed_regolith_panel_mk5_bp.json` (v1.4)
- `blueprints/units/production/extractors/thermal_extraction_unit_mk1_bp.json` (unit_blueprint v1.x)
- `blueprints/units/resource/mining_harvester_bp.json` (unit_blueprint)
- `blueprints/units/resource/planetary_volatiles_extractor_mk1_bp.json` (v1.3)
- `blueprints/units/construction/surface_prep_unit_lspu_bp.json` (unit_blueprint_v1.3)
- `blueprints/units/construction/planetary_i_beam_printing_unit_bp.json` (listed, not fully read)
- `blueprints/units/production/fabricators/regolith_shell_printer_mk1_bp.json` (v1.3)
- `blueprints/modules/energy/solar_array_bp.json` (base_module_blueprint)
- `blueprints/modules/energy/power_distribution_hub_bp.json` (module_blueprint)
- `blueprints/modules/energy/basic_efficiency_module_bp.json` (base_module_blueprint)
- `blueprints/modules/energy/emergency_power_backup_bp.json` (module_blueprint)
- `blueprints/modules/life_support/co2_scrubber.json` (base_module_blueprint)
- `blueprints/crafts/ground/regolith_harvesting_rover_bp.json` (base_craft)
- `missions/luna_base_establishment/luna_settlement_profile_v1.json` (mission_profile)
- `missions/luna_base_establishment/luna_base_establishment_manifest_v2.json` (manifest)
- `missions/luna_base_establishment/phases/phase_1_power_comms.json`
- `missions/luna_base_establishment/phases/phase_2_isru_deployment.json`
- `missions/luna_base_establishment/phases/phase_3_gas_processing.json`
- `missions/luna_base_establishment/phases/phase_4_robot_logistics.json`
- `missions/npc-base-deploy/npc_base_deploy_manifest_v3.json` (mission_manifest v1.2)
- `missions/npc-base-deploy/npc_base_deploy_profile_v1.1.json` (mission_profile)
- `missions_v2/tasks/task_deploy_thermal_extraction_unit_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_pve_unit_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_regolith_harvester_rover_v2.json` (generic_task)
- `missions_v2/tasks/task_deploy_comms_equipment_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_solar_rig_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_puh_and_ppmu_v2.json` (task_v2.1)
- `missions_v2/tasks/task_site_prep_foundation_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_gas_separator_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_volatiles_storage_v2.json` (task_v2.1)
- `missions_v2/tasks/task_inflatable_tank_deployment_v2.json` (task_v2.1)
- `missions_v2/tasks/task_print_inflatable_tank_shells_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_robot_charging_port_v2.json` (task_v2.1)
- `missions_v2/tasks/task_deploy_lspu_v2.json` (task_v2.1)
- `missions_v2/tasks/task_surface_preparation_unit_operations_v2.json` (task_v2.1)
- `operational_data/units/production/extractors/thermal_extraction_unit_mk1_data.json`
- `operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json`
- `operational_data/units/production/fabricators/regolith_shell_printer_mk1_data.json`
- `operational_data/units/power/solar_panel_array.json` (unit_operational_data)
- `operational_data/units/communication/communication_module.json` (unit_operational_data)
- `operational_data/units/energy/radioisotope_thermoelectric_generator_data.json`
- `operational_data/units/energy/solar_panel_data.json`
- `operational_data/units/infrastructure/planetary_umbilical_hub_mk1_data.json`
- `operational_data/units/infrastructure/planetary_power_management_unit_mk1_data.json`
- `operational_data/units/infrastructure/comms_equipment_data.json`
- `operational_data/units/habitats/inflatable_habitat_data.json`
- `operational_data/units/specialized/retractable_landing_legs_data.json`
- `operational_data/units/computers/navigation_computer_l1_v1_data.json`
- `items/components/structural/3d_printed_ibeam_mk1.json` (component_item)
- `items/components/structural/3d_printed_regolith_panel_mk1.json` (component_item)

### Directories Listed but NOT Fully Read

All blueprint subdirectories, operational data subdirectories, items subdirectories, and image catalog directories were listed to determine file existence. Only the files above were read in full.

---

## 2. Existing Lunar Startup / Construction Workflow

### 2.1 Primary Mission Profile: Luna Base Establishment

**Source:** `data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json`

```
Mission ID: luna_base_establishment
Profile template: mission_profile
Target body: LUNA-01
Pattern class: airless_rocky_isru
Day/night cycle: 708 hours
```

**Phases (4 total):**

| Phase | Name | Task List File |
|---|---|---|
| power_comms | Initial Power and Comms | `phases/phase_1_power_comms.json` |
| isru_deployment | ISRU Unit Deployment | `phases/phase_2_isru_deployment.json` |
| gas_processing | Gas Separation and Cryo Storage | `phases/phase_3_gas_processing.json` |
| robot_logistics_site_hardening | Robot Logistics & Site Hardening | `phases/phase_4_robot_logistics.json` |

**Success conditions:** All 4 phases complete, ISRU producing, construction zone leveled.

### 2.2 Phase 1: Power and Comms (5 tasks)

**Source:** `data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json`

| Task Ref | Resolved Task ID |
|---|---|
| `tasks_v2/task_deploy_regolith_harvester_rover.json` | deploy_regolith_harvester_rover |
| `tasks_v2/task_deploy_lspu.json` | deploy_lspu |
| `tasks_v2/task_site_prep_foundation.json` | site_prep_foundation |
| `tasks_v2/task_deploy_comms_equipment.json` | deploy_comms_equipment |
| `tasks_v2/task_deploy_puh_and_ppmu.json` | deploy_puh_and_ppmu |
| `tasks_v2/task_deploy_solar_rig.json` | deploy_solar_rig |

### 2.3 Phase 2: ISRU Unit Deployment (4 tasks)

**Source:** `data/json-data/missions/luna_base_establishment/phases/phase_2_isru_deployment.json`

| Task Ref | Resolved Task ID |
|---|---|
| `tasks_v2/task_deploy_thermal_extraction_unit.json` | deploy_thermal_extraction_unit |
| `tasks_v2/task_deploy_pve_unit.json` | deploy_pve_unit |
| `tasks_v2/task_inflatable_tank_deployment.json` | inflatable_tank_deployment |
| `tasks_v2/task_print_inflatable_tank_shells.json` | print_inflatable_tank_shells |

### 2.4 Phase 3: Gas Processing (3 tasks)

**Source:** `data/json-data/missions/luna_base_establishment/phases/phase_3_gas_processing.json`

| Task Ref | Resolved Task ID |
|---|---|
| `tasks_v2/task_deploy_volatiles_storage.json` | deploy_volatiles_storage |
| `tasks_v2/task_deploy_gas_separator.json` | deploy_gas_separator |
| `tasks_v2/task_surface_preparation_unit_operations.json` | surface_preparation_unit_operations |

### 2.5 Phase 4: Robot Logistics & Site Hardening (4 tasks)

**Source:** `data/json-data/missions/luna_base_establishment/phases/phase_4_robot_logistics.json`

| Task Ref | Resolved Task ID |
|---|---|
| `tasks_v2/task_deploy_robot_charging_port.json` | deploy_robot_charging_port |
| `tasks_v2/task_car_300_charge_cycle.json` | car_300_charge_cycle |
| `tasks_v2/task_isru_stockpile_initiation.json` | isru_stockpile_initiation |
| `tasks_v2/task_construction_zone_leveling.json` | construction_zone_leveling |

### 2.6 NPC Base Deployment (Alternative/Complementary Manifest)

**Source:** `data/json-data/missions/npc-base-deploy/npc_base_deploy_profile_v1.1.json`

```
Mission ID: npc_base_deploy
Manifest: npc_base_deploy_manifest_v1.json (profile references v1, but v3 exists)
Phases: initial_setup → regolith_processing → infrastructure_construction
```

**NPC Base Deploy Manifest v3 Inventory** (`npc_base_deploy_manifest_v3.json`):

The Starship cargo variant carries the following units:

| Unit | Count | Classification |
|---|---|---|
| Raptor Engine | 6 | propulsion |
| LOX Tank | 1 | fuel_storage |
| Methane Tank | 1 | fuel_storage |
| Multi-Purpose Cryo Storage (Methane) | 1 | storage |
| Multi-Purpose Cryo Storage (Nitrogen) | 1 | storage |
| Robotic Docking Unit | 1 | deployment |
| Storage Unit | 4 | storage |
| Retractable Landing Legs | 2 | specialized |
| Navigation Module | 1 | computer |
| Landing Radar | 1 | sensor |
| Power Distribution Hub | 1 | power |
| Autonomous Control Unit | 1 | computer |

**Deployed Units (on arrival):**

| Unit | Count | Provenance |
|---|---|---|
| CAR-300 | 2 | Earth-imported robot |
| Planetary Umbilical Hub | 1 | Earth-imported infrastructure |
| Planetary Power Management Unit | 1 | Earth-imported infrastructure |
| Radioisotope Thermoelectric Generator | 1 | Earth-imported power |
| Comms Equipment | 1 | Earth-imported comms |
| SMR-500 | 1 | Earth-imported robot |
| HRV-400 | 1 | Earth-imported robot |
| Solar Expansion Rig | 1 | Earth-imported power |
| Compact Solar Panel | 10 | Earth-imported power |
| Inflatable Pressure Tank | 1 | Earth-imported storage |
| Inflatable Cryogenic Tank | 3 | Earth-imported storage |
| Inflatable Habitat Unit | 2 | Earth-imported habitat |
| Inflatable Greenhouse Unit | 2 | Earth-imported agriculture |
| General-Purpose Inflatable Unit | 3 | Earth-imported multi-use |
| 3D Regolith Shell Printer | 1 | Earth-imported manufacturing |
| Planetary Volatiles Extractor Mk1 | 1 | Earth-imported ISRU |
| Planetary I-Beam Printing Unit | 1 | Earth-imported manufacturing |
| Thermal Extraction Unit | 1 | Earth-imported ISRU |
| Mining Harvester | 1 | Earth-imported extraction |
| Surface Preparation Unit | 1 | Earth-imported construction |
| Robot Charging Port | 2 | Earth-imported support |

### 2.7 Workflow Reconstruction from Evidence

```
PHASE 0: Earth Delivery (Starship cargo variant)
  ├─ Raptor Engines (6x) — transport
  ├─ All robots (CAR-300, SMR-500, HRV-400)
  ├─ All inflatable structures (habitats, greenhouses, tanks)
  ├─ ISRU equipment (TEU, PVE, Shell Printer, I-Beam Printer)
  ├─ Power infrastructure (RTG, solar panels, power distribution hub)
  ├─ Comms equipment
  └─ Support (robot charging ports, storage units)

PHASE 1: Power and Comms
  ├─ Deploy regolith harvester rover (RPR-200 class)
  ├─ Deploy Surface Prep Unit LSPU
  ├─ Site prep — sinter foundation slab
  ├─ Deploy comms equipment
  ├─ Deploy PUH + PPMU (planetary umbilical hub + power management)
  └─ Deploy solar rig → connect to PPMU

PHASE 2: ISRU Deployment
  ├─ Deploy Thermal Extraction Unit (TEU)
  ├─ Deploy Planetary Volatiles Extractor (PVE)
  ├─ Deploy inflatable pressure/cryogenic tanks
  └─ Print regolith shells around inflatables (via Shell Printer)

PHASE 3: Gas Processing
  ├─ Deploy volatiles storage
  ├─ Deploy gas separator → connect to PUH cryo grid
  └─ Surface preparation unit operations (landing pad ready)

PHASE 4: Robot Logistics & Site Hardening
  ├─ Deploy robot charging ports
  ├─ CAR-300 charge cycle validation
  ├─ ISRU stockpile initiation
  └─ Construction zone leveling
```

---

## 3. Existing Hardware Inventory

### 3.1 Units / Equipment (Active)

| ID | Name | Classification | Provenance | Data Path | Operational Data | Status |
|---|---|---|---|---|---|---|
| `thermal_extraction_unit_mk1` | Thermal Extraction Unit Mk1 | unit/equipment | Earth-imported precursor | `blueprints/units/production/extractors/thermal_extraction_unit_mk1_bp.json` | `operational_data/units/production/extractors/thermal_extraction_unit_mk1_data.json` | EXISTS — BOTH |
| `planetary_volatiles_extractor_mk1` | Planetary Volatiles Extractor Mk I | unit/equipment | Earth-imported precursor | `blueprints/units/resource/planetary_volatiles_extractor_mk1_bp.json` | `operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json` | EXISTS — BOTH |
| `planetary_volatiles_extractor_mk2` | Planetary Volatiles Extractor Mk II | unit/equipment | unknown | N/A (blueprint not found) | `operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json` | EXISTS — IMAGE (ops data only) |
| `planetary_volatiles_extractor_mk3` | Planetary Volatiles Extractor Mk III | unit/equipment | unknown | N/A (blueprint not found) | `operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json` | EXISTS — IMAGE (ops data only) |
| `mining_harvester` | Mining Harvester | unit/equipment | Earth-imported precursor | `blueprints/units/resource/mining_harvester_bp.json` | N/A | EXISTS — DATA |
| `regolith_shell_printer_mk1` | Regolith Shell Printer Mk1 | unit/equipment | Earth-imported precursor | `blueprints/units/production/fabricators/regolith_shell_printer_mk1_bp.json` | `operational_data/units/production/fabricators/regolith_shell_printer_mk1_data.json` | EXISTS — BOTH |
| `regolith_shell_printer_mk2` | Regolith Shell Printer Mk2 | unit/equipment | Luna (upgrade) | `blueprints/units/production/fabricators/regolith_shell_printer_mk2_bp.json` | `operational_data/units/production/fabricators/regolith_shell_printer_mk2_data.json` | EXISTS — BOTH |
| `regolith_shell_printer_mk3` | Regolith Shell Printer Mk3 | unit/equipment | Luna (upgrade) | `blueprints/units/production/fabricators/regolith_shell_printer_mk3_bp.json` | `operational_data/units/production/fabricators/regolith_shell_printer_mk3_data.json` | EXISTS — BOTH |
| `surface_prep_unit_lspu` | Surface Preparation Unit (LSPU) | unit/equipment | Earth-imported precursor | `blueprints/units/construction/surface_prep_unit_lspu_bp.json` | N/A | EXISTS — DATA |
| `planetary_i_beam_printing_unit` | Planetary I-Beam Printing Unit | unit/equipment | Earth-imported precursor | `blueprints/units/construction/planetary_i_beam_printing_unit_bp.json` | N/A | EXISTS — DATA (blueprint only) |

### 3.2 Robots

| ID | Name | Classification | Provenance | Data Path | Operational Data | Status |
|---|---|---|---|---|---|---|
| `car_300_deployment_robot_mk1` | CAR-300 Deployment Robot Mk1 | unit/robot | Earth-imported | `blueprints/units/robots/deployment/car_300_deployment_robot_mk1_bp.json` | N/A | EXISTS — DATA |
| `hrv_400_resource_harvester_mk1` | HRV-400 Resource Harvester Mk1 | unit/robot | Earth-imported | `blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` | N/A | EXISTS — DATA |
| `rpr_200_miner_mk1` | RPR-200 Miner Mk1 | unit/robot | Earth-imported | `blueprints/units/robots/resource/rpr_200_miner_mk1_bp.json` | N/A | EXISTS — DATA |
| `acr_100_space_constructor_mk1` | ACR-100 Space Constructor Mk1 | unit/robot | unknown | `blueprints/units/robots/construction/acr_100_space_constructor_mk1_bp.json` | N/A | EXISTS — DATA |
| `acr_200_space_constructor_mk1` | ACR-200 Space Constructor Mk1 | unit/robot | unknown | `blueprints/units/robots/construction/acr_200_space_constructor_mk1_bp.json` | N/A | EXISTS — DATA |
| `ecr_300_lava_tube_constructor_mk1` | ECR-300 Lava Tube Constructor Mk1 | unit/robot | unknown | `blueprints/units/robots/construction/ecr_300_lava_tube_constructor_mk1_bp.json` | N/A | EXISTS — DATA |

### 3.3 Industrial Units (Non-Robot)

| ID | Name | Classification | Provenance | Data Path | Operational Data | Status |
|---|---|---|---|---|---|---|
| `carbon_extraction_rig_mk1` | Carbon Extraction Rig Mk1 | unit/equipment | unknown | `blueprints/units/industrial/carbon_extraction_rig_mk1_bp.json` | N/A | EXISTS — DATA |
| `cnt_fabricator_unit_mk1` | CNT Fabricator Unit Mk1 | unit/equipment | Luna (upgrade) | `blueprints/units/industrial/cnt_fabricator_unit_mk1_bp.json` | `operational_data/units/production/fabricators/cnt_fabricator_unit_mk1_data.json` | EXISTS — BOTH |
| `construction_drone_mk1` | Construction Drone Mk1 | unit/equipment | unknown | `blueprints/units/industrial/construction_drone_mk1_bp.json` | N/A | EXISTS — DATA |
| `drone_bay_heavy_mk1` | Heavy Drone Bay Mk1 | unit/equipment | unknown | `blueprints/units/industrial/drone_bay_heavy_mk1_bp.json` | N/A | EXISTS — DATA |
| `isru_processor_mk1` | ISRU Processor Mk1 | unit/equipment | Luna (ISRU) | `blueprints/units/industrial/isru_processor_mk1_bp.json` | N/A | EXISTS — DATA |
| `mining_drone_mk1` | Mining Drone Mk1 | unit/equipment | unknown | `blueprints/units/industrial/mining_drone_mk1_bp.json` | N/A | EXISTS — DATA |
| `slag_collection_system_mk1` | Slag Collection System Mk1 | unit/equipment | unknown | `blueprints/units/industrial/slag_collection_system_mk1_bp.json` | N/A | EXISTS — DATA |

### 3.4 Power Generation Units

| ID | Name | Classification | Provenance | Data Path | Operational Data | Status |
|---|---|---|---|---|---|---|
| `nuclear_micro_reactor_mk1` | Nuclear Micro Reactor Mk1 | unit/equipment | Earth-imported | `blueprints/units/power_generation/nuclear_micro_reactor_mk1_bp.json` | N/A | EXISTS — DATA |
| `compact_nuclear_reactor` | Compact Nuclear Reactor | unit/equipment | unknown | `blueprints/units/power/compact_nuclear_reactor_bp.json` | N/A | EXISTS — DATA |
| `mars_industrial_nuclear_reactor` | Mars Industrial Nuclear Reactor | unit/equipment | Mars-specific | `blueprints/units/power/mars_industrial_nuclear_reactor_bp.json` | N/A | EXISTS — DATA |
| `rtg_power_unit` | RTG Power Unit | unit/equipment | Earth-imported precursor | `blueprints/units/power/rtg_power_unit_bp.json` | `operational_data/units/energy/radioisotope_thermoelectric_generator_data.json` | EXISTS — BOTH |
| `solar_panel_array` | Solar Panel Array | unit/equipment | Earth-imported precursor | `blueprints/units/power/solar_panel_array_bp.json` | `operational_data/units/power/solar_panel_array.json` | EXISTS — BOTH |
| `solar_panel` | Solar Panel | unit/equipment | Earth-imported precursor | `blueprints/units/power/solar_panel_bp.json` | `operational_data/units/energy/solar_panel_data.json` | EXISTS — BOTH |
| `compact_solar_panel` | Compact Solar Panel | unit/equipment | Earth-imported precursor | `blueprints/units/power/compact_solar_panel_bp.json` | `operational_data/units/energy/compact_solar_panel_data.json` | EXISTS — BOTH |

### 3.5 Storage Units

| ID | Name | Classification | Provenance | Data Path | Operational Data | Status |
|---|---|---|---|---|---|---|
| `inflatable_pressure_tank` | Inflatable Pressure Tank | structure/storage | Earth-imported precursor | `blueprints/units/storage/inflatable_pressure_tank_bp.json` | N/A | EXISTS — DATA |
| `inflatable_cryo_tank` | Inflatable Cryogenic Tank | structure/storage | Earth-imported precursor | `blueprints/units/storage/inflatable_cryo_tank_bp.json` | N/A | EXISTS — DATA |
| `inflatable_gas_storage` | Inflatable Gas Storage | structure/storage | Earth-imported precursor | `blueprints/units/storage/inflatable_gas_storage_bp.json` | N/A | EXISTS — DATA |
| `lox_storage_tank_mk1` | LOX Storage Tank Mk1 | structure/storage | Luna (upgrade) | `blueprints/units/storage/lox_storage_tank_mk1_bp.json` | N/A | EXISTS — DATA |
| `lox_storage_tank_mk2` | LOX Storage Tank Mk2 | structure/storage | Luna (upgrade) | `blueprints/units/storage/lox_storage_tank_mk2_bp.json` | N/A | EXISTS — DATA |
| `lox_storage_tank_mk3` | LOX Storage Tank Mk3 | structure/storage | Luna (upgrade) | `blueprints/units/storage/lox_storage_tank_mk3_bp.json` | N/A | EXISTS — DATA |
| `methane_storage_tank_mk1` | Methane Storage Tank Mk1 | structure/storage | Luna (upgrade) | `blueprints/units/storage/methane_storage_tank_mk1_bp.json` | N/A | EXISTS — DATA |
| `methane_storage_tank_mk2` | Methane Storage Tank Mk2 | structure/storage | Luna (upgrade) | `blueprints/units/storage/methane_storage_tank_mk2_bp.json` | N/A | EXISTS — DATA |
| `methane_storage_tank_mk3` | Methane Storage Tank Mk3 | structure/storage | Luna (upgrade) | `blueprints/units/storage/methane_storage_tank_mk3_bp.json` | N/A | EXISTS — DATA |
| `multi_purpose_cryo_storage` | Multi-Purpose Cryo Storage | structure/storage | Earth-imported precursor | `blueprints/units/storage/multi_purpose_cryo_storage_bp.json` | N/A | EXISTS — DATA |
| `multi_purpose_cryo_storage_tank_mk2` | Multi-Purpose Cryo Storage Tank Mk2 | structure/storage | Luna (upgrade) | `blueprints/units/storage/multi_purpose_cryo_storage_tank_mk2_bp.json` | N/A | EXISTS — DATA |
| `multi_purpose_cryo_storage_tank_mk3` | Multi-Purpose Cryo Storage Tank Mk3 | structure/storage | Luna (upgrade) | `blueprints/units/storage/multi_purpose_cryo_storage_tank_mk3_bp.json` | N/A | EXISTS — DATA |
| `cryogenic_methane_tank` | Cryogenic Methane Tank | structure/storage | Luna (upgrade) | `blueprints/units/storage/cryogenic_methane_tank_bp.json` | N/A | EXISTS — DATA |
| `cryogenic_slag_storage_mk1` | Cryogenic Slag Storage Mk1 | structure/storage | Luna (upgrade) | `blueprints/units/storage/cryogenic_slag_storage_mk1_bp.json` | N/A | EXISTS — DATA |
| `cryogenic_storage_unit` | Cryogenic Storage Unit | structure/storage | unknown | `blueprints/units/storage/cryogenic_storage_unit_bp.json` | N/A | EXISTS — DATA |
| `fuel_tank_s` | Fuel Tank S | structure/storage | unknown | `blueprints/units/storage/fuel_tank_s_bp.json` | N/A | EXISTS — DATA |
| `nuclear_fuel_tank` | Nuclear Fuel Tank | structure/storage | unknown | `blueprints/units/storage/nuclear_fuel_tank_bp.json` | N/A | EXISTS — DATA |
| `storage_unit` | Storage Unit | structure/storage | Earth-imported precursor | `blueprints/units/storage/storage_unit_bp.json` | N/A | EXISTS — DATA |

---

## 4. Existing Component Inventory

### 4.1 I-Beams (Mk1–Mk5)

| ID | Name | Mk | Blueprint Path | Item Path | Visual Asset | Status |
|---|---|---|---|---|---|---|
| `3d_printed_ibeam_mk1` | 3D-Printed I-Beam Mk1 | Mk1 | `blueprints/components/structural/3d_printed_ibeam_mk1_bp.json` (v1.3) | `items/components/structural/3d_printed_ibeam_mk1.json` (component_item) | `images/catalog/components/structural/3d_printed_ibeam_mk1.png`, `3d_printed_ibeam_mk1_test.png` | EXISTS — BOTH |
| `3d_printed_ibeam_mk2` | 3D-Printed I-Beam Mk2 | Mk2 | `blueprints/components/structural/3d_printed_ibeam_mk2_bp.json` | `items/components/structural/3d_printed_ibeam_mk2.json` | N/A | EXISTS — DATA |
| `3d_printed_ibeam_mk3` | 3D-Printed I-Beam Mk3 | Mk3 | `blueprints/components/structural/3d_printed_ibeam_mk3_bp.json` | `items/components/structural/3d_printed_ibeam_mk3.json` | `images/catalog/components/structural/3d_printed_ibeam_mk3.png`, `3d_printed_ibeam_mk3_test.png` | EXISTS — BOTH |
| `3d_printed_ibeam_mk4` | 3D-Printed I-Beam Mk4 | Mk4 | `blueprints/components/structural/3d_printed_ibeam_mk4_bp.json` | `items/components/structural/3d_printed_ibeam_mk4.json` | N/A | EXISTS — DATA |
| `3d_printed_ibeam_mk5` | 3D-Printed I-Beam Mk5 | Mk5 | `blueprints/components/structural/3d_printed_ibeam_mk5_bp.json` (v1.4) | `items/components/structural/3d_printed_ibeam_mk5.json` | N/A | EXISTS — DATA |

**Mk progression notes:**
- Mk1: Pure regolith sintering, no binder, no external materials
- Mk5: Regolith + aluminum alloy + binding agent + nano-additives + CNT (powder or fiber)
- All are reusable construction hardware, NOT active units

### 4.2 Regolith Panels (Mk1–Mk5)

| ID | Name | Mk | Blueprint Path | Item Path | Visual Asset | Status |
|---|---|---|---|---|---|---|
| `3d_printed_regolith_panel_mk1` | 3D-Printed Regolith Panel Mk1 | Mk1 | `blueprints/components/structural/3d_printed_regolith_panel_mk1_bp.json` (v1.3) | `items/components/structural/3d_printed_regolith_panel_mk1.json` (component_item) | N/A | EXISTS — DATA |
| `3d_printed_regolith_panel_mk2` | 3D-Printed Regolith Panel Mk2 | Mk2 | `blueprints/components/structural/3d_printed_regolith_panel_mk2_bp.json` | `items/components/structural/3d_printed_regolith_panel_mk2.json` | N/A | EXISTS — DATA |
| `3d_printed_regolith_panel_mk3` | 3D-Printed Regolith Panel Mk3 | Mk3 | `blueprints/components/structural/3d_printed_regolith_panel_mk3_bp.json` | `items/components/structural/3d_printed_regolith_panel_mk3.json` | N/A | EXISTS — DATA |
| `3d_printed_regolith_panel_mk4` | 3D-Printed Regolith Panel Mk4 | Mk4 | `blueprints/components/structural/3d_printed_regolith_panel_mk4_bp.json` | `items/components/structural/3d_printed_regolith_panel_mk4.json` | N/A | EXISTS — DATA |
| `3d_printed_regolith_panel_mk5` | 3D-Printed Regolith Panel Mk5 | Mk5 | `blueprints/components/structural/3d_printed_regolith_panel_mk5_bp.json` (v1.4) | `items/components/structural/3d_printed_regolith_panel_mk5.json` | N/A | EXISTS — DATA |

**Mk progression notes:**
- Mk1: Pure regolith sintering, no binder
- Mk5: Regolith + aluminum alloy + binding agent + nano-additives + CNT (powder or fiber)
- All are reusable construction hardware, NOT active units

### 4.3 Other Structural Components

| ID | Name | Classification | Blueprint Path | Status |
|---|---|---|---|---|
| `3d_printed_regolith_reinforcement_panel` | 3D-Printed Regolith Reinforcement Panel | component | `blueprints/components/structural/3d_printed_regolith_reinforcement_panel_bp.json` | EXISTS — DATA |
| `advanced_composites` | Advanced Composites | component | `blueprints/components/structural/advanced_composites_bp.json` | EXISTS — DATA |
| `basic_crater_tube_cover_array` | Basic Crater Tube Cover Array | component | `blueprints/components/structural/basic_crater_tube_cover_array_bp.json` | EXISTS — DATA |
| `basic_structural_components` | Basic Structural Components | component | `blueprints/components/structural/basic_structural_components_bp.json` | EXISTS — DATA |
| `basic_transparent_crater_tube_cover_array` | Basic Transparent Crater Tube Cover Array | component | `blueprints/components/structural/basic_transparent_crater_tube_cover_array_bp.json` | EXISTS — DATA |
| `high_strength_steel` | High Strength Steel | component | `blueprints/components/structural/high_strength_steel_bp.json` | EXISTS — DATA |
| `modular_structural_panel` | Modular Structural Panel | component | `blueprints/components/structural/modular_structural_panel_bp.json` | EXISTS — DATA |
| `radiation_shielding` | Radiation Shielding | component | `blueprints/components/structural/radiation_shielding_bp.json` | EXISTS — DATA |
| `radiation_shielding_cover_panel` | Radiation Shielding Cover Panel | component | `blueprints/components/structural/radiation_shielding_cover_panel_bp.json` | EXISTS — DATA |
| `sealed_lava_tube_cover` | Sealed Lava Tube Cover | component | `blueprints/components/structural/sealed_lava_tube_cover_bp.json` | EXISTS — DATA |
| `solar_cover_panel` | Solar Cover Panel | component | `blueprints/components/structural/solar_cover_panel_bp.json` | EXISTS — DATA |
| `structural_cover_panel` | Structural Cover Panel | component | `blueprints/components/structural/structural_cover_panel_bp.json` | EXISTS — DATA |
| `structural_reinforcement_materials` | Structural Reinforcement Materials | component | `blueprints/components/structural/structural_reinforcement_materials_bp.json` | EXISTS — DATA |
| `thermal_insulation_cover_panel` | Thermal Insulation Cover Panel | component | `blueprints/components/structural/thermal_insulation_cover_panel_bp.json` | EXISTS — DATA |
| `transparent_cover_panel` | Transparent Cover Panel | component | `blueprints/components/structural/transparent_cover_panel_bp.json` | EXISTS — DATA |

### 4.4 Shell / Foundation Components

The repository references shells and pads through:
- `regolith_shell_printer_mk1` unit (produces shell components)
- `task_print_inflatable_tank_shells_v2.json` task (prints protective regolith shells around inflatables)
- No standalone "shell component" blueprint found — shells appear to be produced as a byproduct of the Shell Printer unit operation

### 4.5 Solar Mounting Hardware

No dedicated solar mounting hardware components found in the repository. The `solar_array_bp.json` module exists but has no explicit mounting hardware definition.

---

## 5. Existing Earth-Built Equipment

All precursor equipment is documented as arriving on the Starship cargo variant per `npc_base_deploy_manifest_v3.json`. These are Earth-imported items that establish the initial Luna settlement:

### Power Infrastructure
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| RTG Power Unit | `blueprints/units/power/rtg_power_unit_bp.json` | `operational_data/units/energy/radioisotope_thermoelectric_generator_data.json` | EXISTS — BOTH |
| Solar Panel Array | `blueprints/units/power/solar_panel_array_bp.json` | `operational_data/units/power/solar_panel_array.json` | EXISTS — BOTH |
| Solar Panel (individual) | `blueprints/units/power/solar_panel_bp.json` | `operational_data/units/energy/solar_panel_data.json` | EXISTS — BOTH |
| Compact Solar Panel | `blueprints/units/power/compact_solar_panel_bp.json` | `operational_data/units/energy/compact_solar_panel_data.json` | EXISTS — BOTH |
| Power Distribution Hub | `blueprints/modules/energy/power_distribution_hub_bp.json` | N/A | EXISTS — DATA |
| Emergency Power Backup | `blueprints/modules/energy/emergency_power_backup_bp.json` | N/A | EXISTS — DATA |

### Comms / Computing
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| Comms Equipment | Referenced in manifest v3 | `operational_data/units/infrastructure/comms_equipment_data.json` | EXISTS — IMAGE (ops data only) |
| Navigation Computer L1 | N/A (L1-specific) | `operational_data/units/computers/navigation_computer_l1_v1_data.json` | EXISTS — IMAGE (ops data only) |

### ISRU / Processing
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| Thermal Extraction Unit Mk1 | `blueprints/units/production/extractors/thermal_extraction_unit_mk1_bp.json` | `operational_data/units/production/extractors/thermal_extraction_unit_mk1_data.json` | EXISTS — BOTH |
| Planetary Volatiles Extractor Mk1 | `blueprints/units/resource/planetary_volatiles_extractor_mk1_bp.json` | `operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json` | EXISTS — BOTH |
| Mining Harvester | `blueprints/units/resource/mining_harvester_bp.json` | N/A | EXISTS — DATA |
| 3D Regolith Shell Printer | `blueprints/units/production/fabricators/regolith_shell_printer_mk1_bp.json` | `operational_data/units/production/fabricators/regolith_shell_printer_mk1_data.json` | EXISTS — BOTH |
| Planetary I-Beam Printing Unit | `blueprints/units/construction/planetary_i_beam_printing_unit_bp.json` | N/A | EXISTS — DATA (blueprint only) |

### Infrastructure
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| Planetary Umbilical Hub Mk1 | Referenced in manifest v3 | `operational_data/units/infrastructure/planetary_umbilical_hub_mk1_data.json` | EXISTS — IMAGE (ops data only) |
| Planetary Power Management Unit Mk1 | Referenced in manifest v3 | `operational_data/units/infrastructure/planetary_power_management_unit_mk1_data.json` | EXISTS — IMAGE (ops data only) |

### Habitat / Life Support
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| Inflatable Habitat Unit | Referenced in manifest v3 | `operational_data/units/habitats/inflatable_habitat_data.json` | EXISTS — IMAGE (ops data only) |
| Inflatable Greenhouse Unit | Referenced in manifest v3 | `operational_data/units/life_support/inflatable_greenhouse_data.json` | EXISTS — IMAGE (ops data only) |
| General-Purpose Inflatable Unit | Referenced in manifest v3 | N/A | EXISTS — DATA (manifest only) |

### Storage
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| Inflatable Pressure Tank | `blueprints/units/storage/inflatable_pressure_tank_bp.json` | N/A | EXISTS — DATA |
| Inflatable Cryogenic Tank | `blueprints/units/storage/inflatable_cryo_tank_bp.json` | N/A | EXISTS — DATA |
| Inflatable Gas Storage | `blueprints/units/storage/inflatable_gas_storage_bp.json` | N/A | EXISTS — DATA |
| Multi-Purpose Cryo Storage (Methane) | Referenced in manifest v3 | N/A | EXISTS — DATA (manifest only) |
| Multi-Purpose Cryo Storage (Nitrogen) | Referenced in manifest v3 | N/A | EXISTS — DATA (manifest only) |

### Robots
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| CAR-300 Deployment Robot Mk1 | `blueprints/units/robots/deployment/car_300_deployment_robot_mk1_bp.json` | N/A | EXISTS — DATA |
| HRV-400 Resource Harvester Mk1 | `blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` | N/A | EXISTS — DATA |
| SMR-500 | Referenced in manifest v3 | N/A | EXISTS — DATA (manifest only) |

### Support Equipment
| Item | Blueprint Path | Ops Data Path | Status |
|---|---|---|---|
| Surface Prep Unit LSPU | `blueprints/units/construction/surface_prep_unit_lspu_bp.json` | N/A | EXISTS — DATA |
| Robot Charging Port | Referenced in manifest v3 | N/A | EXISTS — DATA (manifest only) |
| Retractable Landing Legs | `blueprints/units/specialized/retractable_landing_legs_bp.json` | `operational_data/units/specialized/retractable_landing_legs_data.json` | EXISTS — BOTH |

---

## 6. Existing Lunar Tasks / Mission Definitions

### 6.1 Luna Base Establishment Tasks (via mission profile)

All resolved from `missions_v2/tasks/`:

| Task ID | Name | Source File | Priority |
|---|---|---|---|
| `deploy_regolith_harvester_rover` | Deploy Regolith Harvester Rover | `missions_v2/tasks/task_deploy_regolith_harvester_rover_v2.json` | 2 |
| `deploy_lspu` | Deploy Surface Prep Unit LSPU | `missions_v2/tasks/task_deploy_lspu_v2.json` | 2 |
| `site_prep_foundation` | Site Prep — Sintered Foundation Slab | `missions_v2/tasks/task_site_prep_foundation_v2.json` | 1 |
| `deploy_comms_equipment` | Deploy Comms Equipment | `missions_v2/tasks/task_deploy_comms_equipment_v2.json` | 2 |
| `deploy_puh_and_ppmu` | Deploy PUH and PPMU | `missions_v2/tasks/task_deploy_puh_and_ppmu_v2.json` | 3 |
| `deploy_solar_rig` | Deploy Solar Expansion Rig | `missions_v2/tasks/task_deploy_solar_rig_v2.json` | 4 |
| `deploy_thermal_extraction_unit` | Deploy Thermal Extraction Unit | `missions_v2/tasks/task_deploy_thermal_extraction_unit_v2.json` | 6 |
| `deploy_pve_unit` | Deploy PVE Mk1 | `missions_v2/tasks/task_deploy_pve_unit_v2.json` | 7 |
| `inflatable_tank_deployment` | Inflatable Tank Deployment | `missions_v2/tasks/task_inflatable_tank_deployment_v2.json` | 5 |
| `print_inflatable_tank_shells` | Print Inflatable Tank Shells | `missions_v2/tasks/task_print_inflatable_tank_shells_v2.json` | 5.7 |
| `deploy_volatiles_storage` | Deploy Volatiles Storage | `missions_v2/tasks/task_deploy_volatiles_storage_v2.json` | 8 |
| `deploy_gas_separator` | Deploy Gas Separator | `missions_v2/tasks/task_deploy_gas_separator_v2.json` | 8 |
| `surface_preparation_unit_operations` | Surface Preparation Unit Operations | `missions_v2/tasks/task_surface_preparation_unit_operations_v2.json` | 6 |
| `deploy_robot_charging_port` | Deploy Robot Charging Port | `missions_v2/tasks/task_deploy_robot_charging_port_v2.json` | 5 |
| `car_300_charge_cycle` | CAR-300 Charge Cycle | `missions_v2/tasks/task_car_300_charge_cycle_v2.json` | N/A |
| `isru_stockpile_initiation` | ISRU Stockpile Initiation | `missions_v2/tasks/task_isru_stockpile_initiation_v2.json` | N/A |
| `construction_zone_leveling` | Construction Zone Leveling | `missions_v2/tasks/task_construction_zone_leveling_v2.json` | N/A |
| `deploy_car_robots` | Deploy CAR Robots | `missions_v2/tasks/task_deploy_car_robots_v2.json` | N/A |

### 6.2 NPC Base Deployment Task Files (legacy/parallel)

All in `missions/npc-base-deploy/`:

| File | Description |
|---|---|
| `initial_setup_phase_1_v1.1.json` | Initial power & comms setup |
| `initial_regolith_processing_phase_2_v1.4.json` | Regolith harvest & processing |
| `infrastructure_construction_phase_3_v1.3.json` | Infrastructure construction |
| `npc_base_deploy_manifest_v3.json` | Full Starship manifest (v1.2) |
| `npc_base_deploy_profile_v1.1.json` | Mission profile |
| `npc_regolith_processing_phases_v1.json` | Regolith processing phases |
| `npc_base_deploy_industrial_expansion_v1.json` | Industrial expansion |

### 6.3 Additional Mission Profiles Referencing Luna

| File | Description |
|---|---|
| `missions/npc-base-deploy/npc_base_deploy_manifest_v1.json` | Legacy manifest v1 |
| `missions/npc-base-deploy/npc_base_deploy_manifest_v1.1.json` | Legacy manifest v1.1 |
| `missions/npc-base-deploy/npc_base_deploy_manifest_v2.json` | Legacy manifest v2 |
| `missions/luna_base_establishment/luna_base_establishment_manifest_v2.json` | Asset manifest (v2.0) |

---

## 7. Existing Visual Assets

### 7.1 Production / Catalog Assets

| ID / Concept | Image Path | Mk | Type | Notes |
|---|---|---|---|---|
| `3d_printed_ibeam_mk1` | `images/catalog/components/structural/3d_printed_ibeam_mk1.png` | Mk1 | production | Catalog asset |
| `3d_printed_ibeam_mk1` (test) | `images/catalog/components/structural/3d_printed_ibeam_mk1_test.png` | Mk1 | test | Test variant |
| `3d_printed_ibeam_mk2` | `images/catalog/components/structural/3d_printed_ibeam_mk2.png` | Mk2 | production | Catalog asset |
| `3d_printed_ibeam_mk3` | `images/catalog/components/structural/3d_printed_ibeam_mk3.png` | Mk3 | production | Catalog asset |
| `3d_printed_ibeam_mk3` (test) | `images/catalog/components/structural/3d_printed_ibeam_mk3_test.png` | Mk3 | test | Test variant |
| `3d_printed_ibeam_mk4` | `images/catalog/components/structural/3d_printed_ibeam_mk4.png` | Mk4 | production | Catalog asset |
| `primary_structural_spine` | `images/catalog/components/structural/primary_structural_spine.png` | N/A | production | Generic structural spine |
| `thermal_extraction_unit_mk1` | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1.png` | Mk1 | production | Catalog asset |
| `thermal_extraction_unit_mk1` (concept) | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1_concept_art.png` | Mk1 | concept | Concept art |
| `thermal_extraction_unit_mk1` (concept 2) | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1_concept_art_2.png` | Mk1 | concept | Concept art variant |
| `thermal_extraction_unit_mk1` (sample) | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1_sample.png` | Mk1 | test | Sample image |
| `thermal_extraction_unit_mk2` | `images/catalog/units/production/extractors/thermal_extraction_unit_mk2.png` | Mk2 | production | Catalog asset |
| `3d_printed_fabricator_mk1` | `images/catalog/units/production/fabricators/3d_printed_fabricator_mk1_concept.png` | Mk1 | concept | Concept art |
| `regolith_harvesting_rover` | `images/catalog/crafts/ground/regolith_harvesting_rover.png` | N/A | production | Catalog asset (craft) |

### 7.2 ChatGPT-Generated Images (Jul 12, 2026)

| Image Path | Notes |
|---|---|
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_19_39 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_19_52 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_19_55 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_19_59 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_20_02 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_20_06 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_20_09 PM.png` | Concept/test — ChatGPT generated |
| `images/catalog/components/structural/ChatGPT Image Jul 12, 2026, 02_20_13 PM.png` | Concept/test — ChatGPT generated |

### 7.3 Empty Catalog Directories (No Visual Assets)

The following catalog directories exist but contain NO files:

| Directory | Expected Content |
|---|---|
| `images/catalog/components/electronics/` | Electronics components |
| `images/catalog/components/materials/` | Material components |
| `images/catalog/components/mechanical/` | Mechanical components |
| `images/catalog/components/production/` | Production components |
| `images/catalog/components/specialized/` | Specialized components |
| `images/catalog/structures/habitation/` | Habitat structures |
| `images/catalog/structures/landing_infrastructure/` | Landing infrastructure |
| `images/catalog/structures/life_support/` | Life support structures |
| `images/catalog/structures/manufacturing/` | Manufacturing structures |
| `images/catalog/structures/mega_structures/` | Mega structures |
| `images/catalog/structures/power_generation/` | Power generation structures |
| `images/catalog/structures/resource_extraction/` | Resource extraction structures |
| `images/catalog/structures/resource_processing/` | Resource processing structures |
| `images/catalog/structures/science_research/` | Science/research structures |
| `images/catalog/structures/space_stations/` | Space station structures |
| `images/catalog/structures/storage/` | Storage structures |
| `images/catalog/structures/transportation/` | Transportation structures |
| `images/catalog/modules/*` (all subdirs) | All module categories |
| `images/catalog/units/construction/` | Construction units |
| `images/catalog/units/control/` | Control units |
| `images/catalog/units/electronics/` | Electronics units |
| `images/catalog/units/em_processing/` | EM processing units |
| `images/catalog/units/fuel/` | Fuel units |
| `images/catalog/units/gravitational_control/` | Gravitational control units |
| `images/catalog/units/habitats/` | Habitat units |
| `images/catalog/units/housing/` | Housing units |
| `images/catalog/units/infrastructure/` | Infrastructure units |
| `images/catalog/units/mechanical/` | Mechanical units |
| `images/catalog/units/power_generation/` | Power generation units (separate from extractors) |
| `images/catalog/units/propulsion/` | Propulsion units |
| `images/catalog/units/resource/` | Resource units |
| `images/catalog/units/sensors/` | Sensor units |
| `images/catalog/units/specialized/` | Specialized units |
| `images/catalog/units/storage/` | Storage units |
| `images/catalog/units/vehicles/` | Vehicle units |
| `images/catalog/crafts/atmospheric/` | Atmospheric crafts |
| `images/catalog/crafts/space/` (subdirs) | Space crafts |

---

## 8. Blueprint / Template Relationships

### 8.1 Templates Used by Discovered Definitions

| Template File | Used By |
|---|---|
| `component_blueprint_v1.json` through `v1.4` | I-beam Mk1–Mk5, regolith panel Mk1–Mk5, all other structural components |
| `component_item.json` / `component_item_v1.0.json` | Item definitions for I-beams and panels in `items/components/structural/` |
| `unit_blueprint` / `unit_blueprint_v1.x` | All unit/equipment blueprints (TEU, PVE, mining harvester, shell printer, etc.) |
| `unit_operational_data` / `unit_operational_data_v1.x` | All operational data files for units |
| `base_module_blueprint` | Module definitions (solar array, CO2 scrubber, basic efficiency module) |
| `module_blueprint` | Power distribution hub, emergency power backup |
| `craft_blueprint_v1.x` | Craft definitions |
| `base_craft` | Regolith harvesting rover craft |
| `mission_manifest` | All mission manifest files |
| `mission_profile` | Luna settlement profile, NPC base deploy profile |
| `task_v2.1` / `generic_task` | All task definition files in missions_v2/tasks/ |

### 8.2 Template Files That Exist but Were NOT Referenced by Any Lunar Asset

| Template File | Notes |
|---|---|
| `alien_world_templates_v1.json` / `v1.1` | Non-Luna templates |
| `celestial_body.json` | Celestial body template (not a Lunar-specific asset) |
| `chemical.json` | Chemical resource template |
| `component_blueprint_v1.1.json` through `v1.4` | Versioned templates (current is v1.4 for components) |
| `craft_blueprint_v1.1.json` through `v1.7` | Versioned craft templates |
| `fuel.json` | Fuel template |
| `geological_material_template.json` | Geological material template |
| `initial_settlement_plan_template.json` | Settlement plan template |
| `material_v1.x` series | Material templates |
| `ore_material_template.json` | Ore material template |
| `rig_blueprint_v1.x` | Rig templates |
| `robot_unit_operational_data_v1.x` | Robot unit ops data template |
| `structure_blueprint_v1.json` | Structure blueprint template |
| `structure_operational_data_v1.json` | Structure ops data template |
| `technology_category.json` | Technology category template |
| `tool_item.json` | Tool item template |
| `consumable_item.json` | Consumable item template |
| `container_item.json` | Container item template |
| `equipment_item.json` | Equipment item template |
| `furniture_item.json` | Furniture item template |

---

## 9. Gaps

### 9.1 Missing Data (Blueprints / Definitions)

| Item | Gap Type | Evidence |
|---|---|---|
| PVE Mk2 blueprint | MISSING — DATA | Ops data exists at `operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json` but no corresponding blueprint found in `blueprints/` |
| PVE Mk3 blueprint | MISSING — DATA | Same as Mk2 — ops data exists, blueprint missing |
| SMR-500 robot blueprint | MISSING — DATA | Referenced in manifest v3 inventory but no blueprint file found |
| Robot Charging Port blueprint | MISSING — DATA | Referenced in manifest v3 and task definitions but no blueprint file found |
| Gas Separator blueprint | MISSING — DATA | Referenced in `task_deploy_gas_separator_v2.json` but no blueprint file found |
| Planetary I-Beam Printing Unit operational data | MISSING — IMAGE (ops data) | Blueprint exists at `blueprints/units/construction/planetary_i_beam_printing_unit_bp.json` but no operational data file found |
| Surface Prep Unit LSPU operational data | MISSING — IMAGE (ops data) | Blueprint exists but no operational data file found |
| Mining Harvester operational data | MISSING — IMAGE (ops data) | Blueprint exists but no operational data file found |
| CAR-300 operational data | MISSING — IMAGE (ops data) | Blueprint exists but no operational data file found |
| HRV-400 operational data | MISSING — IMAGE (ops data) | Blueprint exists but no operational data file found |
| Shell component definition | UNCERTAIN | Shells are produced by Shell Printer unit operation but no standalone shell component blueprint found |
| Pad/foundation component definition | UNCERTAIN | Landing pads referenced in tasks but no standalone pad component blueprint found |
| Solar mounting hardware | MISSING — DATA | No dedicated solar mounting hardware components found |

### 9.2 Missing Visual Assets (for items that have data)

| Item | Expected Image Path | Status |
|---|---|---|
| `3d_printed_ibeam_mk2` visual | `images/catalog/components/structural/3d_printed_ibeam_mk2.png` | EXISTS — DATA only (no image) |
| `3d_printed_ibeam_mk4` visual | `images/catalog/components/structural/3d_printed_ibeam_mk4.png` | EXISTS — DATA only (no image) |
| `3d_printed_ibeam_mk5` visual | `images/catalog/components/structural/3d_printed_ibeam_mk5.png` | MISSING — IMAGE |
| `3d_printed_regolith_panel_mk1` visual | `images/catalog/components/structural/3d_printed_regolith_panel_mk1.png` | MISSING — IMAGE |
| `3d_printed_regolith_panel_mk2` visual | `images/catalog/components/structural/3d_printed_regolith_panel_mk2.png` | MISSING — IMAGE |
| `3d_printed_regolith_panel_mk3` visual | `images/catalog/components/structural/3d_printed_regolith_panel_mk3.png` | MISSING — IMAGE |
| `3d_printed_regolith_panel_mk4` visual | `images/catalog/components/structural/3d_printed_regolith_panel_mk4.png` | MISSING — IMAGE |
| `3d_printed_regolith_panel_mk5` visual | `images/catalog/components/structural/3d_printed_regolith_panel_mk5.png` | MISSING — IMAGE |
| `thermal_extraction_unit_mk1` (production) | Already exists at extractors/ | EXISTS — BOTH |
| `planetary_volatiles_extractor_mk1` visual | `images/catalog/units/production/extractors/planetary_volatiles_extractor_mk1.png` | MISSING — IMAGE |
| `mining_harvester` visual | `images/catalog/units/resource/mining_harvester.png` | MISSING — IMAGE |
| `regolith_shell_printer_mk1` visual | `images/catalog/units/production/fabricators/regolith_shell_printer_mk1.png` | MISSING — IMAGE (only concept exists) |
| `surface_prep_unit_lspu` visual | `images/catalog/units/construction/surface_prep_unit_lspu.png` | MISSING — IMAGE |
| `planetary_i_beam_printing_unit` visual | `images/catalog/units/construction/planetary_i_beam_printing_unit.png` | MISSING — IMAGE |
| RTG visual | `images/catalog/units/power/rtg_power_unit.png` | MISSING — IMAGE |
| Solar Panel Array visual | `images/catalog/units/power/solar_panel_array.png` | MISSING — IMAGE |
| Comms Equipment visual | `images/catalog/units/infrastructure/comms_equipment.png` | MISSING — IMAGE |
| PUH visual | `images/catalog/units/infrastructure/planetary_umbilical_hub.png` | MISSING — IMAGE |
| PPMU visual | `images/catalog/units/infrastructure/planetary_power_management_unit.png` | MISSING — IMAGE |
| Inflatable Habitat visual | `images/catalog/modules/harvester/inflatable_habitat.png` or similar | MISSING — IMAGE |
| Inflatable Greenhouse visual | `images/catalog/modules/life_support/inflatable_greenhouse.png` | MISSING — IMAGE |
| CAR-300 visual | `images/catalog/units/robots/deployment/car_300.png` | MISSING — IMAGE |
| HRV-400 visual | `images/catalog/units/robots/resource/hrv_400.png` | MISSING — IMAGE |
| SMR-500 visual | `images/catalog/units/robots/SMR-500.png` | MISSING — IMAGE |
| Retractable Landing Legs visual | `images/catalog/units/specialized/retractable_landing_legs.png` | MISSING — IMAGE |

### 9.3 Uncertain / Needs Human Decision

| Item | Uncertainty | Evidence |
|---|---|---|
| Shell component vs. byproduct | Are shells a standalone component or just a unit operation output? | Task `print_inflatable_tank_shells_v2.json` references printing shells but no shell component blueprint exists |
| Pad/foundation component | Is there a pad component, or is it just a settlement state flag? | Task `site_prep_foundation_v2.json` sets `foundation_sintered: true` — no component definition found |
| Gas Separator | Unit type unclear — structure, unit, or module? | Referenced in task but no blueprint exists |
| Robot Charging Port | Unit or structure? | Referenced in manifest and tasks but no blueprint exists |
| Mk2/Mk3 PVE blueprints | Should they exist if only ops data exists? | Ops data files exist for Mk2/Mk3 but no corresponding blueprints |
| ChatGPT images (Jul 12, 2026) | Are these production assets or concept/test? | 8 ChatGPT-generated images in components/structural/ — classification uncertain |

---

## 10. Existing Assets That Should NOT Be Regenerated

Based on the repository's existing documentation standards and production catalog patterns:

### Production-Ready Visual Assets (catalog standard)
| Asset | Path | Reason |
|---|---|---|
| `3d_printed_ibeam_mk1.png` | `images/catalog/components/structural/3d_printed_ibeam_mk1.png` | Catalog asset, not test/concept |
| `3d_printed_ibeam_mk2.png` | `images/catalog/components/structural/3d_printed_ibeam_mk2.png` | Catalog asset |
| `3d_printed_ibeam_mk3.png` | `images/catalog/components/structural/3d_printed_ibeam_mk3.png` | Catalog asset |
| `3d_printed_ibeam_mk4.png` | `images/catalog/components/structural/3d_printed_ibeam_mk4.png` | Catalog asset |
| `thermal_extraction_unit_mk1.png` | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1.png` | Catalog asset |
| `thermal_extraction_unit_mk2.png` | `images/catalog/units/production/extractors/thermal_extraction_unit_mk2.png` | Catalog asset |
| `regolith_harvesting_rover.png` | `images/catalog/crafts/ground/regolith_harvesting_rover.png` | Catalog asset (craft) |
| `primary_structural_spine.png` | `images/catalog/components/structural/primary_structural_spine.png` | Catalog asset |

### Production-Ready Data Definitions
| Asset | Path | Reason |
|---|---|---|
| I-beam Mk1 blueprint | `blueprints/components/structural/3d_printed_ibeam_mk1_bp.json` (v1.3) | Complete, versioned, compliant |
| I-beam Mk5 blueprint | `blueprints/components/structural/3d_printed_ibeam_mk5_bp.json` (v1.4) | Complete, latest version |
| Panel Mk1 blueprint | `blueprints/components/structural/3d_printed_regolith_panel_mk1_bp.json` (v1.3) | Complete, versioned, compliant |
| Panel Mk5 blueprint | `blueprints/components/structural/3d_printed_regolith_panel_mk5_bp.json` (v1.4) | Complete, latest version |
| TEU blueprint + ops data | Both in extractors/ | Complete pair |
| PVE Mk1 blueprint + ops data | Both found | Complete pair |
| Shell Printer Mk1–M3 blueprints + ops data | All found | Complete pairs for all 3 versions |
| Luna settlement profile | `missions/luna_base_establishment/luna_settlement_profile_v1.json` | Authoritative mission profile |
| NPC base deploy manifest v3 | `missions/npc-base-deploy/npc_base_deploy_manifest_v3.json` (v1.2) | Complete manifest with full inventory |

---

## 11. Recommended Next Review

### Items Needing Human Decision (Smallest Set)

1. **Shell component classification** — Is the regolith shell a standalone component type, or is it purely a unit operation output of the Shell Printer? This affects whether a new blueprint file is needed.

2. **Pad/foundation component classification** — The `site_prep_foundation` task sets a settlement state flag (`foundation_sintered: true`). Is there a physical pad component that should have its own definition, or is it purely a terrain/state concept?

3. **Gas Separator unit type** — Referenced in task definitions but no blueprint exists. Should this be a unit, structure, or module? What are its inputs/outputs relative to the TEU/PVE thermal cascade?

4. **Robot Charging Port classification** — Unit, structure, or module? Blueprint needed?

5. **PVE Mk2/Mk3 blueprints** — Ops data exists for both but no blueprints. Should blueprints be created, or are these ops-data-only definitions?

6. **ChatGPT image classification** — 8 ChatGPT-generated images from Jul 12, 2026 in `images/catalog/components/structural/`. Are these production assets, concept art, or test images? Their placement in the catalog directory suggests production intent but their origin is uncertain.

7. **SMR-500 robot** — Referenced in manifest v3 inventory but no blueprint exists. Is this a gap that needs filling, or was it intentionally omitted?

8. **Planetary I-Beam Printing Unit operational data** — Blueprint exists but no ops data. Should ops data be created to match the pattern of other deployed units?

---

## Appendix A: Complete File Inventory Summary

### Blueprints (Lunar-Relevant)
| Category | Count | Path Pattern |
|---|---|---|
| Components (structural) | 25 files | `blueprints/components/structural/*.json` |
| Units — construction | 3 files | `blueprints/units/construction/*.json` |
| Units — robots | 6 files | `blueprints/units/robots/{construction,deployment,resource}/*.json` |
| Units — industrial | 7 files | `blueprints/units/industrial/*.json` |
| Units — power generation | 1 file | `blueprints/units/power_generation/*.json` |
| Units — power | 8 files | `blueprints/units/power/*.json` |
| Units — production (extractors) | 2 files | `blueprints/units/production/extractors/*.json` |
| Units — production (fabricators) | 10 files | `blueprints/units/production/fabricators/*.json` |
| Units — resource | 2 files | `blueprints/units/resource/*.json` |
| Units — storage | 18 files | `blueprints/units/storage/*.json` |
| Units — specialized | 5 files | `blueprints/units/specialized/*.json` |
| Modules — energy | 5 files | `blueprints/modules/energy/*.json` |
| Modules — power | 2 files | `blueprints/modules/power/*.json` |
| Modules — life_support | 2 files | `blueprints/modules/life_support/*.json` |
| Modules — infrastructure | 1 file | `blueprints/modules/infrastructure/*.json` |
| Modules — utility | 1 file | `blueprints/modules/utility/*.json` |
| Modules — computer | 5 files | `blueprints/modules/computer/*.json` |
| Modules — sensor | 2 files | `blueprints/modules/sensor/*.json` |
| Modules — stabilization_core | 1 file | `blueprints/modules/stabilization_core/*.json` |
| Crafts — ground | 1 file | `blueprints/crafts/ground/*.json` |
| Rigs | 1 file | `blueprints/infrastructure/*.json` |
| Ports | 1 file | `blueprints/ports/*.json` |

### Operational Data (Lunar-Relevant)
| Category | Count | Path Pattern |
|---|---|---|
| Units — production/extractors | 6 files | `operational_data/units/production/extractors/*_data.json` |
| Units — production/fabricators | 9 files | `operational_data/units/production/fabricators/*_data.json` |
| Units — power | 7 files | `operational_data/units/power/*_data.json` |
| Units — energy | 7 files | `operational_data/units/energy/*_data.json` |
| Units — infrastructure | 5 files | `operational_data/units/infrastructure/*_data.json` |
| Units — communication | 1 file | `operational_data/units/communication/*_data.json` |
| Units — computers | 9 files | `operational_data/units/computers/*_data.json` |
| Units — habitats | 4 files | `operational_data/units/habitats/*_data.json` |
| Units — life_support | 9 files | `operational_data/units/life_support/*_data.json` |
| Units — sensors | 6 files | `operational_data/units/sensors/*_data.json` |
| Units — propulsion | 18 files | `operational_data/units/propulsion/*_data.json` |
| Units — specialized | 2 files | `operational_data/units/specialized/*_data.json` |
| Units — mechanical | 2 files | `operational_data/units/mechanical/*_data.json` |
| Units — em_processing | 1 file | `operational_data/units/em_processing/*_data.json` |
| Units — gravitational_control | 1 file | `operational_data/units/gravitational_control/*_data.json` |

### Items (Lunar-Relevant)
| Category | Count | Path Pattern |
|---|---|---|
| Components — structural | 15 files | `items/components/structural/*.json` |
| Components — production | 1 file | `items/components/production/*.json` |
| Equipment — industrial | 1 file | `items/equipment/industrial/*.json` |
| Consumable — life_support | subdirectory exists | `items/consumable/life_support/` |
| Consumable — power | 1 file | `items/consumable/power/battery_pack.json` |

### Tasks (Lunar-Relevant)
| Count | Path Pattern |
|---|---|
| 18 task files | `missions_v2/tasks/task_*_v2.json` |

### Mission Profiles / Manifests (Lunar-Relevant)
| Count | Path Pattern |
|---|---|
| 1 luna_base_establishment profile | `missions/luna_base_establishment/luna_settlement_profile_v1.json` |
| 1 luna_base_establishment manifest | `missions/luna_base_establishment/luna_base_establishment_manifest_v2.json` |
| 4 phase files | `missions/luna_base_establishment/phases/phase_*.json` |
| 7 npc_base_deploy files | `missions/npc-base-deploy/*.json` |

### Visual Assets (Lunar-Relevant)
| Count | Path Pattern |
|---|---|
| 4 I-beam production images | `images/catalog/components/structural/3d_printed_ibeam_mk*.png` |
| 2 I-beam test images | `images/catalog/components/structural/3d_printed_ibeam_mk*_test.png` |
| 8 ChatGPT concept images | `images/catalog/components/structural/ChatGPT Image*.png` |
| 1 structural spine image | `images/catalog/components/structural/primary_structural_spine.png` |
| 1 TEU production image | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1.png` |
| 2 TEU concept images | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1_concept_art*.png` |
| 1 TEU sample image | `images/catalog/units/production/extractors/thermal_extraction_unit_mk1_sample.png` |
| 1 TEU Mk2 image | `images/catalog/units/production/extractors/thermal_extraction_unit_mk2.png` |
| 1 fabricator concept image | `images/catalog/units/production/fabricators/3d_printed_fabricator_mk1_concept.png` |
| 1 rover image | `images/catalog/crafts/ground/regolith_harvesting_rover.png` |

---

**End of Audit Report**
