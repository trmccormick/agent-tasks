# Manufacturing Chain Overview

## Overview

The manufacturing chain in galaxy_game is a complete ISRU (In-Situ Resource Utilization) production pipeline that transforms raw planetary resources into finished components and structures. This playable gameplay loop spans five distinct phases: raw material extraction from celestial bodies, material processing through specialized units, component production via PVE/TEU cycles, blueprint-gated validation, and assembly job execution. The system is designed to be a core economic engine where players establish settlements, extract resources, and produce goods for construction, trade, or further manufacturing.

The chain is orchestrated by `Manufacturing::ProductionService` at its core, with player-facing assembly workflows handled by `Manufacturing::AssemblyService`. Blueprint gating is enforced at the model level via Rails callbacks, ensuring no invalid production orders can be created. AI Manager services drive production planning but the actual manufacturing logic lives in the dedicated manufacturing services.

## Chain Phases

### 1. Raw Material Extraction

Raw materials are gathered from celestial bodies through extraction units (PVE and TEU types). Each celestial body has a geosphere with stored_volatiles that determine what resources are available.

**Mechanism**:
- Raw regolith is stored in `surface_storage.material_piles` as material piles with a `material_type` attribute
- Extraction units consume raw_regolith from surface piles via `consume_from_pile`
- Different celestial bodies provide different volatile compositions (water ice, mixed volatiles)
- The geosphere's `stored_volatiles` field determines extraction yields

**Key files**:
- `app/services/manufacturing/production_service.rb` — `consume_from_pile`, `produce_to_pile`
- `app/services/manufacturing/material_processing_service.rb` — Geosphere volatile reading
- `app/models/settlement.rb` — Surface storage and inventory management

**Celestial body mapping**: Each body's geosphere defines available volatiles. The `MaterialProcessingService.complete_job` reads `@settlement.celestial_body&.geosphere` to calculate extraction yields dynamically.

### 2. Material Processing

Raw materials are processed through unit cycles that transform them into intermediate products and byproducts. This phase uses operational data from unit JSON templates to determine input/output ratios.

**Mechanism**:
1. Unit operational data is looked up via `Lookup::UnitLookupService.find_unit(unit_type)`
2. Processing type determined from subcategory: `volatile_extraction` or `thermal_extraction`
3. Output resources calculated from `operational_data["output_resources"]`
4. **Dual-path output system**:
   - **Non-zero amounts**: Direct scaling (`input_amount * (out_amount / base_input) * geosphere_eff`)
   - **Zero amounts**: Geosphere volatile percentage calculation for dynamic extraction
5. Depleted regolith calculated as input minus all extracted volatiles

**Key files**:
- `app/services/manufacturing/material_processing_service.rb` — Main processing logic
- `app/services/manufacturing/processing.rb` — Processing abstraction
- `app/services/manufacturing/regolith_processing_service.rb` — Regolith-specific processing

**PVE_DATA ratios** (core ISRU chain):
| Input | Output | Ratio |
|---|---|---|
| 5.0kg processed regolith | 0.05kg gases | 1% |
| 5.0kg processed regolith | 0.10kg water | 2% |
| 5.0kg processed regolith | 4.85kg depleted regolith | 97% |

### 3. Component Production

Processed materials are assembled into components via the `ProductionService.manufacture_component` method, which orchestrates the entire upstream production chain atomically within a transaction.

**Mechanism**:
1. Calculate final material requirements from blueprint data
2. Determine upstream PVE cycles needed: `(inert_req_kg / PVE_DATA[:output_inert_waste_kg]).ceil`
3. Validate materials available in inventory (transactional check)
4. Consume raw regolith from surface pile
5. Run PVE cycles (producing water, gases, inert waste as byproducts)
6. Produce volatiles to settlement inventory via `produce_to_inventory`
7. Create production job record with status `in_progress`
8. Consume intermediate materials and produce final component
9. Update job status to `ready_to_claim`

**Key files**:
- `app/services/manufacturing/production_service.rb` — Core orchestrator
- `app/services/manufacturing/component_production_service.rb` — Component-specific production
- `app/services/manufacturing/byproduct_manufacturing_service.rb` — Byproduct handling

**Job record structure**:
```ruby
Job.create!(
  job_type: :component_production,
  settlement: @settlement,
  owner: @settlement.owner,
  status: 'in_progress',
  completes_at: Time.current + (production_time_hours || 2.0) * target_units.hours,
  operational_data: {
    component_blueprint_id: blueprint_data[:id],
    component_name: blueprint_data[:name],
    output_quantity: target_units,
    materials_consumed: {...},
    pve_cycles: ...,
    byproducts: [...]
  }
)
```

### 4. Blueprint Gating

Blueprint validation is enforced at the **model level**, not in service methods. This prevents invalid production orders from being created through any code path.

**Mechanism**:
1. **Model validations**: Presence of name, non-negative numerical values for efficiencies
2. **Licensed runs**: `licensed_runs_remaining` field gates production count; `nil` = unlimited (NPCs)
3. **can_manufacture?(quantity)**: Checks remaining licensed runs >= quantity
4. **consume_runs(quantity)**: Decrements licensed runs after manufacturing
5. **Efficiency calculations**: Research levels improve material/time efficiency via `calculate_efficiencies`

**Key files**:
- `app/models/blueprint.rb` — Blueprint model with licensing and efficiency logic
- `app/models/concerns/has_blueprint_ports.rb` — Port compatibility concern
- `data/json-data/blueprints/` — Blueprint JSON data files (10 categories)

**Validation flow**:
```
Player requests production
    → Blueprint.can_manufacture?(quantity)
        → licensed_runs_remaining.nil? → true (unlimited)
        → licensed_runs_remaining >= quantity → true/false
    → If false, raise error
    → If true, proceed with production
    → After completion, blueprint.consume_runs(quantity)
```

### 5. Assembly Jobs

Assembly jobs are created via `AssemblyService.start_assembly` which validates materials, calculates fees, and creates job records for player-facing workflows.

**Mechanism**:
1. **Input validation**: Blueprint and settlement type checks
2. **Material availability check**: Queries settlement inventory for owner-owned items
3. **FMV cost calculation**: Uses `Market::NpcPriceCalculator.calculate_ask` for missing materials
4. **Tenant fee**: `10 + build_time_hours + material_count` (base 10 GCC)
5. **Affordability check**: Financial account balance verification
6. **Buy missing materials** (optional): Transfers funds and adds items to inventory
7. **Job creation**: Creates `Job` or `ConstructionJob` record based on blueprint category
8. **Fee charging**: Transfers GCC from requester to settlement

**Key files**:
- `app/services/manufacturing/assembly_service.rb` — Main assembly service
- `app/services/manufacturing/unit_module_assembly.rb` — Unit/module assembly
- `app/services/manufacturing/unit_deployment.rb` — Unit deployment logic
- `app/services/manufacturing/equipment_request.rb` — Equipment request handling
- `app/services/manufacturing/material_request_system.rb` — Material request system

**Job types created**:
| Blueprint Category | Job Type | Record Model |
|---|---|---|
| `units`, `craft` | `:unit_assembly` | `Job` |
| `structures`, `facilities` | `:construction` | `ConstructionJob` |

## Key Services

| Service | Role in Chain | File Path |
|---|---|---|
| `ProductionService` | Core ISRU chain orchestrator | `manufacturing/production_service.rb` |
| `MaterialProcessingService` | Unit-level material processing | `manufacturing/material_processing_service.rb` |
| `AssemblyService` | Assembly job creation and validation | `manufacturing/assembly_service.rb` |
| `ComponentProductionService` | Component-specific production | `manufacturing/component_production_service.rb` |
| `ByproductManufacturingService` | Byproduct handling | `manufacturing/byproduct_manufacturing_service.rb` |
| `RegolithProcessingService` | Regolith-specific processing | `manufacturing/regolith_processing_service.rb` |
| `ShellPrintingService` | Shell/dome construction | `manufacturing/shell_printing_service.rb` |
| `CraftFactory` | Craft manufacturing | `manufacturing/craft_factory.rb` |
| `CostCalculator` | Production cost calculation | `manufacturing/cost_calculator.rb` |
| `UnitModuleAssembly` | Unit/module assembly | `manufacturing/unit_module_assembly.rb` |
| `UnitDeployment` | Unit deployment logic | `manufacturing/unit_deployment.rb` |
| `MaterialRequestSystem` | Material request handling | `manufacturing/material_request_system.rb` |
| `ConstructionManager` | Construction coordination | `manufacturing/construction/construction_manager.rb` |

## Blueprint System

### Blueprint Structure

Blueprints are defined in two places:
1. **Database**: `Blueprint` model with licensing, efficiency, and research data
2. **JSON files**: `data/json-data/blueprints/` with full specifications

**Database fields**:
- `name` — Blueprint identifier (required)
- `current_research_level` — Research progress (>= 0)
- `material_efficiency` — Material cost reduction (>= 0)
- `time_efficiency` — Production time reduction (>= 0)
- `licensed_runs_remaining` — Remaining production count (nil = unlimited for NPCs)

**JSON structure** (from sample: `regolith_harvester_rover_bp.json`):
```json
{
  "template": "base_craft",
  "id": "regolith_harvester_rover",
  "name": "RH-400 Regolith Harvester Rover",
  "description": "...",
  "type": "craft",
  "category": "harvester",
  "physical_properties": { length_m, width_m, height_m, empty_mass_kg, volume_m3, drag_coefficient },
  "mounting_points": [],
  "compatible_units": [],
  "compatible_modules": [],
  "ports": { internal_module_ports: 0, external_module_ports: 0, ... },
  "systems": { stabilizer_unit: {status}, propulsion: {status}, power: {status}, ... },
  "cost_data": {
    "purchase_cost": { currency: "GCC", amount: 45000 },
    "manufacturing_cost": { currency: "GCC", amount: 42750 },
    "maintenance": { time_to_repair_hours: 6, repair_cost_gcc: 1500, materials_needed_for_repair: [...] }
  },
  "blueprint_data": {
    "base_material_efficiency": 0.0,
    "base_time_efficiency": 0.0,
    "materials": [{ id: "aluminum", amount: 4200.0, unit: "kilogram" }, ...],
    "required_materials": { aluminum: { amount: 4200.0 }, electronics: { amount: 850.0 } },
    "production_data": { build_time_hours: 24, ... }
  }
}
```

### Validation Rules

| Rule | Enforced By | Description |
|---|---|---|
| Name presence | Rails validation | Blueprint must have a name |
| Non-negative efficiencies | Rails validation | material_efficiency >= 0, time_efficiency >= 0 |
| Licensed runs check | `can_manufacture?` method | licensed_runs_remaining >= quantity (nil = unlimited) |
| Run consumption | `consume_runs` method | Decrements licensed_runs_remaining after production |
| Material availability | `AssemblyService.check_material_availability` | Settlement inventory must have owner-owned items |
| Tenant fee affordability | `can_afford_fee?` method | Financial account balance >= fee |
| Blueprint category routing | `determine_job_type` | Units/crafts → Job; structures → ConstructionJob |

### Blueprint Categories

| Category | Location | Purpose |
|---|---|---|
| `crafts/` | `atmospheric/`, `ground/`, `space/` | Vehicles for different environments |
| `structures/` | `data/json-data/blueprints/structures/` | Buildings and facilities |
| `modules/` | `data/json-data/blueprints/modules/` | Spacecraft modules |
| `rigs/` | `data/json-data/blueprints/rigs/` | Equipment rigs |
| `units/` | `data/json-data/blueprints/units/` | Individual units |
| `components/` | `data/json-data/blueprints/components/` | Raw components |
| `items/` | `data/json-data/blueprints/items/` | Miscellaneous items |
| `materials/` | `data/json-data/blueprints/materials/` | Processed materials |
| `ports/` | `data/json-data/blueprints/ports/` | Port infrastructure |

## Data Flow Diagram

```
[Celestial Body]
       │ geosphere.stored_volatiles
       ▼
[Raw Regolith Pile] ──→ [Surface Storage]
       │
       ▼
[PVE/TEU Processing Units]
       │
       ├──→ Water (to_inventory)
       ├──→ Gases (to_inventory)
       └──→ Depleted Regolith (to_pile)
       │
       ▼
[MaterialProcessingService]
       │ operational_data["output_resources"]
       │ geosphere_efficiency
       ▼
[Processed Materials in Inventory]
       │
       ▼
[Blueprint.can_manufacture?] ← [Blueprint Model]
       │                    ├── licensed_runs_remaining
       │                    ├── material_efficiency
       │                    └── time_efficiency
       ▼
[ProductionService.manufacture_component]
       │
       ├── Calculate PVE cycles
       ├── Validate materials
       ├── Consume raw regolith
       ├── Run PVE cycles
       ├── Produce volatiles
       ├── Create production job
       └── Produce final component
       │
       ▼
[AssemblyService.start_assembly]
       │
       ├── Check material availability
       ├── Calculate tenant fee (10 + build_time + materials)
       ├── Buy missing materials (optional)
       └── Create Job/ConstructionJob
       │
       ▼
[Finished Component/Structure]
       │
       └──→ [Inventory → Player Claim]
```

## Playable Loop Summary

1. **Player establishes settlement** on a celestial body with raw material resources (geosphere volatiles)
2. **Extraction units** (PVE/TEU) process raw regolith from surface piles, producing water, gases, and depleted regolith
3. **Blueprints gate production** — player must have unlocked blueprints with sufficient licensed runs remaining
4. **Assembly requests** validate material availability in settlement inventory and charge tenant fees (10 GCC base + build time + materials)
5. **Production jobs** run asynchronously, producing components/structures over the specified build time
6. **Player claims finished products** from inventory for use in construction, crafting, or trade with NPC economy

The loop is designed to be self-sustaining: players extract raw materials, process them into components, use those components to build structures and vehicles, which enable further extraction and production cycles. The blueprint system ensures progression gating through research levels and licensed runs, while the tenant fee system provides economic balance.
