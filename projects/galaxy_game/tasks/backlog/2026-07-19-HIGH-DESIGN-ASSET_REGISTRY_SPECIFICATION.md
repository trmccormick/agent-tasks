---
date_created: 2026-07-19
date_modified: 2026-07-19
status: backlog
priority: HIGH
type: DESIGN
component: Asset System Architecture
phase: Phase 2 - Asset Foundation
relates_to:
  - 2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md (companion document - HOW assets look)
  - UNIFIED_ASSET_CATALOG_ARCHITECTURE.md (architectural principle)
---

# GalaxyGame Asset Registry Specification

## Executive Summary

The Asset Registry is the **single source of truth for all game content**. While the Icon Bible answers **"How should everything look?"**, the Registry answers **"What assets exist?"**

This document catalogs every resource, manufactured item, unit, structure, vehicle, technology, organization, and UI element in GalaxyGame. It provides:
1. **Canonical asset list** (what actually exists)
2. **Asset metadata** (ID, category, variants, tech levels)
3. **Manufacturing recipes** (what's crafted vs. raw)
4. **Location availability** (where resources appear)
5. **JSON integration** (how systems reference assets)

**This document grows continuously** — new resources, units, and technologies are added throughout development. It is linked to the Icon Bible (stable) and referenced by all JSON mission data, code, and asset generation prompts.

---

## Purpose: Single Source of Truth

### Before Asset Registry
- Art team designs icons independently
- Code team creates JSON with different naming conventions
- Wiki documents a third set of names
- Mission designers use a fourth naming scheme
- Simulation adds yet another layer
- **Result**: Confusion, duplicates, missing connections

### With Asset Registry
- Registry defines canonical name (`RES_GAS_OXYGEN`)
- All systems reference this ID:
  - JSON: `resource_id: "RES_GAS_OXYGEN"`
  - Code: `Resource.find("RES_GAS_OXYGEN")`
  - Art: `icon_RES_GAS_OXYGEN_256.svg`
  - Wiki: Links to Registry entry
  - Prompts: "Generate icon for RES_GAS_OXYGEN"
- **Result**: Single definition, consistent everywhere

---

## Structure: Asset Categories

### 1. RESOURCES (Raw Materials)

**Format**: `RES_[TYPE]_[NAME]_[VARIANT]`

#### Atmospheric Gases
```
RES_GAS_OXYGEN
RES_GAS_NITROGEN
RES_GAS_CARBON_DIOXIDE
RES_GAS_HYDROGEN
RES_GAS_METHANE
RES_GAS_AMMONIA
RES_GAS_HELIUM
RES_GAS_ARGON
RES_GAS_NEON
RES_GAS_KRYPTON
RES_GAS_XENON
RES_GAS_CARBON_MONOXIDE
RES_GAS_OZONE
RES_GAS_SULFUR_DIOXIDE
RES_GAS_HYDROGEN_SULFIDE
```

#### Liquids
```
RES_LIQUID_WATER
RES_LIQUID_HEAVY_WATER
RES_LIQUID_BRINE
RES_LIQUID_SULFURIC_ACID
RES_LIQUID_HYDROGEN
RES_LIQUID_OXYGEN
RES_LIQUID_METHANE
RES_LIQUID_AMMONIA
RES_LIQUID_METHANOL
RES_LIQUID_ETHANOL
```

#### Ices
```
RES_ICE_WATER
RES_ICE_CARBON_DIOXIDE
RES_ICE_METHANE
RES_ICE_NITROGEN
RES_ICE_AMMONIA
```

#### Elements (Metals & Non-metals)
```
RES_ELEMENT_IRON
RES_ELEMENT_ALUMINUM
RES_ELEMENT_TITANIUM
RES_ELEMENT_SILICON
RES_ELEMENT_CARBON
RES_ELEMENT_NICKEL
RES_ELEMENT_CHROMIUM
RES_ELEMENT_MAGNESIUM
RES_ELEMENT_COPPER
RES_ELEMENT_LITHIUM
RES_ELEMENT_SODIUM
RES_ELEMENT_POTASSIUM
RES_ELEMENT_CALCIUM
RES_ELEMENT_SULFUR
RES_ELEMENT_PHOSPHORUS
```

#### Minerals & Ores
```
RES_MINERAL_REGOLITH (generic planetary dust)
RES_MINERAL_BASALT
RES_MINERAL_GRANITE
RES_MINERAL_QUARTZ
RES_MINERAL_ILMENITE
RES_MINERAL_OLIVINE
RES_MINERAL_FELDSPAR
RES_MINERAL_HEMATITE
RES_MINERAL_MAGNETITE
RES_MINERAL_PYRITE
RES_MINERAL_OBSIDIAN
RES_MINERAL_CLAY
RES_MINERAL_LIMESTONE
RES_MINERAL_GYPSUM
```

### 2. MANUFACTURED ITEMS (Crafted/Processed Products)

**Format**: `ITEM_[CATEGORY]_[NAME]_[TECH_LEVEL]`

#### Materials (Processed from Resources)
```
ITEM_MATERIAL_STEEL_MK1
ITEM_MATERIAL_ALUMINUM_ALLOY_MK2
ITEM_MATERIAL_TITANIUM_ALLOY_MK2
ITEM_MATERIAL_CARBON_FIBER_MK3
ITEM_MATERIAL_COMPOSITE_PANEL_MK2
ITEM_MATERIAL_GLASS_MK1
ITEM_MATERIAL_CERAMIC_MK2
ITEM_MATERIAL_KEVLAR_MK3
ITEM_MATERIAL_POLYMER_MK1
ITEM_MATERIAL_CONCRETE_MK1
```

#### Structural Components
```
ITEM_COMPONENT_BEAM_MK1
ITEM_COMPONENT_FRAME_MK2
ITEM_COMPONENT_STRUT_MK1
ITEM_COMPONENT_JOINT_MK2
ITEM_COMPONENT_WELD_ASSEMBLY_MK3
ITEM_COMPONENT_PRESSURE_VESSEL_MK2
ITEM_COMPONENT_TANK_FUEL_MK1
ITEM_COMPONENT_TANK_WATER_MK1
ITEM_COMPONENT_TANK_GAS_MK1
```

#### Mechanical Components
```
ITEM_COMPONENT_AIRLOCK_MK1
ITEM_COMPONENT_AIRLOCK_MK2
ITEM_COMPONENT_AIRLOCK_MK3
ITEM_COMPONENT_DOCKING_PORT_MK1
ITEM_COMPONENT_THRUSTER_MK1
ITEM_COMPONENT_THRUSTER_MK2
ITEM_COMPONENT_RADIATOR_PANEL_MK1
ITEM_COMPONENT_RADIATOR_PANEL_MK2
ITEM_COMPONENT_SOLAR_PANEL_MK1
ITEM_COMPONENT_SOLAR_PANEL_MK2
ITEM_COMPONENT_SOLAR_PANEL_MK3
```

#### Electronics
```
ITEM_ELECTRONICS_CIRCUIT_BOARD_MK1
ITEM_ELECTRONICS_PROCESSOR_MK1
ITEM_ELECTRONICS_PROCESSOR_MK2
ITEM_ELECTRONICS_MEMORY_MK1
ITEM_ELECTRONICS_SENSOR_MK1
ITEM_ELECTRONICS_OPTICAL_FIBER_MK2
ITEM_ELECTRONICS_POWER_CELL_MK1
ITEM_ELECTRONICS_BATTERY_MK1
ITEM_ELECTRONICS_BATTERY_MK2
ITEM_ELECTRONICS_SUPERCAPACITOR_MK3
```

#### Biological Products
```
ITEM_BIOLOGICAL_SEEDS
ITEM_BIOLOGICAL_ALGAE_CULTURE
ITEM_BIOLOGICAL_PROTEIN_PASTE
ITEM_BIOLOGICAL_STORED_MEAL_MK1
ITEM_BIOLOGICAL_FERTILIZER
```

### 3. UNITS (Mobile Equipment)

**Format**: `UNIT_[TYPE]_[VARIANT]_[TECH_LEVEL]`

```
UNIT_EXTRACTOR
UNIT_HABITAT_INFLATABLE_MK1
UNIT_HABITAT_RIGID_MK2
UNIT_FABRICATOR_MK1
UNIT_FABRICATOR_MK2
UNIT_COMPUTER_DATA_CENTER_MK1
UNIT_BATTERY_ENERGY_STORAGE
UNIT_PROPULSION_THRUSTER
UNIT_STORAGE_DEPOT
UNIT_ROBOT_AUTONOMOUS_MK1
UNIT_ROBOT_AUTONOMOUS_MK2
```

### 4. STRUCTURES (Stationary Installations)

**Format**: `STRUCT_[TYPE]_[SUBTYPE]_[TECH_LEVEL]`

```
STRUCT_POWER_SOLAR_MK1
STRUCT_POWER_SOLAR_MK2
STRUCT_POWER_THERMAL_MK1
STRUCT_POWER_NUCLEAR_MK3
STRUCT_LAB_RESEARCH_MK1
STRUCT_LAB_RESEARCH_MK2
STRUCT_HABITAT_HUB_INFLATABLE_MK1
STRUCT_HABITAT_HUB_RIGID_MK2
STRUCT_HABITAT_UMBILICAL_MK1
STRUCT_MINING_OPERATION_MK1
STRUCT_PROCESSING_FACILITY_MK1
STRUCT_PROCESSING_FACILITY_MK2
STRUCT_STORAGE_FACILITY_MK1
STRUCT_DOCK_LANDING_PAD
STRUCT_DOCK_ORBITAL_MK1
STRUCT_AIRLOCK_EXTERNAL
STRUCT_GREENHOUSE_AGRICULTURAL_MK1
```

### 5. VEHICLES (Craftable Mobile Units)

**Format**: `VEHICLE_[TYPE]_[VARIANT]_[TECH_LEVEL]`

```
VEHICLE_ROVER_WHEELED_MK1
VEHICLE_ROVER_WHEELED_MK2
VEHICLE_HARVESTER_INDUSTRIAL_MK1
VEHICLE_SHIP_SHUTTLE_MK1
VEHICLE_SHIP_CARGO_MK2
VEHICLE_SPACESHIP_LARGE_MK3
VEHICLE_SATELLITE_ORBITAL_MK1
VEHICLE_TUG_ORBITAL_MK1
```

### 6. TECHNOLOGY/RESEARCH

**Format**: `TECH_[DOMAIN]_[TECH_LEVEL]`

```
TECH_MINING_MK1
TECH_MINING_MK2
TECH_HABITAT_MK1
TECH_HABITAT_MK2
TECH_HABITAT_MK3
TECH_MANUFACTURING_MK1
TECH_MANUFACTURING_MK2
TECH_AGRICULTURE_MK1
TECH_AGRICULTURE_MK2
TECH_ENERGY_SOLAR_MK1
TECH_ENERGY_THERMAL_MK2
TECH_ENERGY_NUCLEAR_MK3
TECH_PROPULSION_CHEMICAL_MK1
TECH_PROPULSION_ION_MK3
TECH_SENSORS_MK1
TECH_SENSORS_MK2
TECH_MEDICAL_MK1
TECH_MEDICAL_MK2
TECH_AUTOMATION_MK1
TECH_AUTOMATION_MK2
TECH_AI_CORE_MK4
TECH_TERRAFORMING_MK1
TECH_TERRAFORMING_MK2
```

### 7. ORGANIZATIONS (Faction/Corporate)

**Format**: `ORG_[TYPE]_[NAME]`

```
ORG_CORPORATION_ICC (Interplanetary Colonization Consortium)
ORG_CORPORATION_MCC (Martian Colonial Company)
ORG_CORPORATION_VENUSIAN_TRUST
ORG_CORPORATION_BELT_MINERS_COOP
ORG_FACTION_GOVERNMENT_EARTH
ORG_FACTION_GOVERNMENT_MARS
ORG_FACTION_ROGUE_COLLECTIVE
```

### 8. UI/MAP SYMBOLS

**Format**: `UI_[TYPE]_[NAME]` or `MAP_[TYPE]_[NAME]`

```
UI_ICON_RESOURCE_GENERIC
UI_ICON_UNIT_GENERIC
UI_ICON_STRUCTURE_GENERIC
UI_ICON_RESEARCH
UI_ICON_BUILD
UI_ICON_HARVEST
UI_ICON_TRANSPORT

UI_STATUS_DAMAGED
UI_STATUS_LOCKED
UI_STATUS_LOW_STOCK
UI_STATUS_RESEARCH_INCOMPLETE
UI_STATUS_POWERED_OFF
UI_STATUS_MALFUNCTION

MAP_SYMBOL_SETTLEMENT
MAP_SYMBOL_MINING_SITE
MAP_SYMBOL_TRADE_ROUTE
MAP_SYMBOL_RESOURCE_DEPOSIT
MAP_SYMBOL_HAZARD
```

---

## Asset Record Structure (JSON Template)

Every registry entry follows this structure:

```json
{
  "asset_id": "RES_GAS_OXYGEN",
  "category": "resource",
  "type": "gas",
  "canonical_name": "Oxygen",
  "display_name": "Oxygen (O₂)",
  "aliases": ["O2", "oxygen gas", "atmospheric oxygen"],
  
  "visual": {
    "shape": "circle",
    "color_family": "blue",
    "icon_id": "icon_RES_GAS_OXYGEN",
    "icon_sizes": ["24px", "32px", "64px", "256px"],
    "illustration": true,
    "blueprint": false,
    "3d_render": false,
    "material": "none"
  },
  
  "tech_level": 1,
  "is_crafted": false,
  
  "location_prevalence": {
    "Luna": { "abundance": "abundant", "extraction_difficulty": 0.5 },
    "Mars": { "abundance": "abundant", "extraction_difficulty": 0.6 },
    "Venus": { "abundance": "rare", "extraction_difficulty": 1.2 },
    "Earth": { "abundance": "abundant", "extraction_difficulty": 0.3 }
  },
  
  "manufacturing_origins": null,
  
  "simulation_effects": {
    "thermal_properties": 0.8,
    "reactivity": 0.9,
    "density_kg_m3": 1.43
  },
  
  "crafting_recipe": null,
  "source_method": "atmospheric_extraction",
  
  "json_reference_id": "oxygen",
  "code_constant": "RESOURCE_OXYGEN",
  
  "created_date": "2026-07-19",
  "last_updated": "2026-07-19",
  "version": 1
}
```

---

## Integration Points

### JSON Mission Data
```json
{
  "mission_id": "luna_phase1_oxy_extraction",
  "resources_required": [
    { "asset_id": "RES_GAS_OXYGEN", "quantity_kg": 50000 }
  ],
  "deliverables": [
    { "asset_id": "ITEM_ELECTRONICS_CIRCUIT_BOARD_MK1", "quantity": 10 }
  ]
}
```

### Code References
```ruby
# services/resource_service.rb
asset = AssetRegistry.find("RES_GAS_OXYGEN")
puts asset.canonical_name  # "Oxygen"
puts asset.color_family    # "blue"
```

### Icon Generation Prompts
```
Generate icon for: RES_GAS_OXYGEN
Style: Icon Bible (Section 2)
Shape: Circle (gas)
Color Family: Blue
Lighting: 45° top-left
Materials: None (pure substance)
Size: Generate all (24px, 32px, 64px, 256px)
```

### Wiki/Documentation
```markdown
# Oxygen (RES_GAS_OXYGEN)

**Asset ID**: RES_GAS_OXYGEN
**Type**: Atmospheric Gas
**Shape**: Circle (gas category)
**Color**: Blue (atmosphere family)
**Status**: Production

See Asset Registry entry: [Link to registry entry]
```

---

## Catalog Maintenance Rules

1. **No Location-Specific Duplicates**
   - ❌ RES_MINERAL_LUNAR_REGOLITH
   - ✅ RES_MINERAL_REGOLITH (with location_prevalence field)

2. **Manufacturing Origin Noted, Not Duplicated**
   - ✅ ITEM_MATERIAL_STEEL_MK1 (can be Earth/Lunar/Orbital manufactured)
   - ❌ ITEM_MATERIAL_LUNAR_STEEL vs ITEM_MATERIAL_EARTH_STEEL

3. **Tech Levels Explicit**
   - ✅ TECH_ENERGY_SOLAR_MK1, TECH_ENERGY_SOLAR_MK2, TECH_ENERGY_SOLAR_MK3
   - ❌ TECH_ENERGY_SOLAR (ambiguous level)

4. **Variants Explicit**
   - ✅ UNIT_HABITAT_INFLATABLE_MK1, UNIT_HABITAT_RIGID_MK2
   - ❌ UNIT_HABITAT_LUNAR, UNIT_HABITAT_MARS

5. **IDs Never Change**
   - Once assigned, an asset ID is permanent
   - If asset renamed/redesigned: create NEW ID, mark old one deprecated

---

## Acceptance Criteria

**Asset Registry Specification** (this document):
- [ ] Complete catalog structure defined (8 categories)
- [ ] Asset ID format established (category_type_name_variant)
- [ ] Asset record JSON template defined
- [ ] Integration points documented (JSON, code, art, wiki)
- [ ] Maintenance rules enforced (no location-specific duplicates, explicit tech levels)
- [ ] Companion Icon Bible reference linked

**Quality Bar**:
- Every asset in the game has a single entry in this registry
- All systems (JSON, code, art, wiki, missions) reference same ID
- New assets added by following template structure
- No ambiguity about what "RES_GAS_OXYGEN" is

---

## Related Documentation

- **2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md**: HOW assets look (stable, rarely changes)
- **UNIFIED_ASSET_CATALOG_ARCHITECTURE.md**: WHY location-agnostic naming (architectural principle)
- **2026-07-19-MEDIUM-FEATURE-UNIT-SPRITE-ASSET-GENERATION.md**: First sprites generated using this registry

---

## Implementation Timeline

**Phase 1** (Initial): 
- Define asset categories (done in this document)
- Create initial resource list (~100 entries)
- Test integration with existing JSON missions

**Phase 2** (Expansion):
- Add unit/structure/vehicle definitions
- Build technology tree catalog
- Integrate with crafting system JSON

**Phase 3** (Ongoing):
- Add new resources as discovered/needed
- Expand tech tree as research unlocks
- Update manufacturing origins as production methods evolve
- Maintain version history for long-term reference

---

## Notes

This Registry grows continuously throughout development. It is the **single source of truth** that ties together:
- Simulation (where resources exist, how they work)
- Visuals (how they look, Icon Bible rules)
- Code (internal references, service layer)
- Missions (JSON definitions, deliverables)
- Players (wiki, in-game encyclopedia)

Every system references this Registry by asset ID. No ambiguity, no duplication, no location-specific bloat.
