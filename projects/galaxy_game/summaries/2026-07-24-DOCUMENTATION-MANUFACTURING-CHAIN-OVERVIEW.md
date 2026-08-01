# Manufacturing Chain Synthesis Report

**Date**: 2026-07-29
**Task**: 2026-07-24-HIGH-DOCUMENTATION-MANUFACTURING-CHAIN-OVERVIEW.md
**Status**: Active — Synthesis Complete, Ready for Documentation Creation

---

## Executive Summary

The manufacturing chain in galaxy_game is a multi-phase ISRU (In-Situ Resource Utilization) production pipeline that transforms raw regolith into finished components and structures. The system spans three architectural domains:

1. **Production Services** (`app/services/manufacturing/`) — Core manufacturing logic
2. **Blueprint System** (`app/models/blueprint.rb` + `data/json-data/blueprints/`) — Gating and validation
3. **AI Manager** (`app/services/ai_manager/`) — Production planning (not directly in scope but drives the chain)

---

## Chain Phase Analysis

### Phase 1: Raw Material Extraction

**How it works**: Raw regolith is gathered from celestial bodies via extraction units (PVE/TEU types). The `ProductionService` handles this by consuming raw_regolith from a surface pile.

**Key files**:
- `app/services/manufacturing/production_service.rb` — Core ISRU chain orchestrator
- `app/services/manufacturing/material_processing_service.rb` — Unit-level processing logic
- `app/services/manufacturing/regolith_processing_service.rb` — Regolith-specific processing

**Mechanism**:
- Raw regolith is stored in `surface_storage.material_piles` (material piles with material_type)
- PVE units process raw_regolith → water + gases + depleted_regolith (byproducts)
- PVE_DATA ratios: input 5.0kg processed → output 0.05kg gases, 0.10kg water, 4.85kg inert waste

**Celestial body mapping**: Different bodies provide different raw material compositions (geosphere stored_volatiles). The `MaterialProcessingService.complete_job` reads `@settlement.celestial_body&.geosphere` for volatile extraction efficiency.

### Phase 2: Material Processing

**How it works**: Raw materials are processed through unit cycles that transform them into intermediate products and final components.

**Key files**:
- `app/services/manufacturing/material_processing_service.rb` — Main processing service
- `app/services/manufacturing/processing.rb` — Processing abstraction
- `app/services/manufacturing/material_processing.rb` — Material processing module

**Mechanism**:
1. Unit operational data is looked up via `Lookup::UnitLookupService.find_unit(unit_type)`
2. Processing type determined from subcategory: `volatile_extraction` or `thermal_extraction`
3. Output resources calculated from `operational_data["output_resources"]`
4. Zero-amount outputs use geosphere volatile percentages for dynamic extraction
5. Non-zero outputs scale by input amount and geosphere efficiency

**Critical detail**: The processing uses a dual-path output system:
- **Non-zero amounts**: Direct scaling (`input_amount * (out_amount / base_input) * geosphere_eff`)
- **Zero amounts**: Geosphere volatile percentage calculation for dynamic resource extraction

### Phase 3: Component Production

**How it works**: Processed materials are assembled into components via the `ProductionService.manufacture_component` method.

**Key files**:
- `app/services/manufacturing/production_service.rb` — Core production orchestrator
- `app/services/manufacturing/component_production_service.rb` — Component-specific production
- `app/services/manufacturing/byproduct_manufacturing_service.rb` — Byproduct handling

**Mechanism**:
1. Calculate final material requirements from blueprint data
2. Determine upstream PVE cycles needed
3. Validate materials available in inventory
4. Consume raw regolith from surface pile
5. Run PVE cycles (producing water, gases, inert waste as byproducts)
6. Produce to_inventory for volatiles
7. Create production job record
8. Consume intermediate materials and produce final component
9. Update job status to `ready_to_claim`

**Key data structure**: `PVE_DATA` ratios drive the entire upstream calculation chain.

### Phase 4: Blueprint Gating

**How it works**: Blueprint validation is enforced at the **model level** via Rails callbacks and validations, NOT in service methods.

**Key files**:
- `app/models/blueprint.rb` — Blueprint model with licensing and efficiency logic
- `app/models/concerns/has_blueprint_ports.rb` — Port compatibility concern
- `data/json-data/blueprints/` — Blueprint JSON data files (10 categories)

**Mechanism**:
1. **Blueprint model validations**: presence of name, non-negative numerical values for efficiencies
2. **Licensed runs**: `licensed_runs_remaining` field gates production count; nil = unlimited (NPCs)
3. **can_manufacture?**: Checks remaining licensed runs >= quantity
4. **consume_runs**: Decrements licensed runs after manufacturing
5. **Efficiency calculations**: Research levels improve material/time efficiency via `calculate_efficiencies`

**Blueprint categories** (from JSON data):
- `crafts/` — atmospheric, ground, space vehicles
- `structures/` — buildings and facilities
- `modules/` — spacecraft modules
- `rigs/` — equipment rigs
- `units/` — individual units
- `components/` — raw components
- `items/` — miscellaneous items
- `materials/` — processed materials
- `ports/` — port infrastructure

**JSON structure** (from regolith_harvester_rover_bp.json):
```json
{
  "template": "base_craft",
  "id": "regolith_harvester_rover",
  "name": "RH-400 Regolith Harvester Rover",
  "type": "craft",
  "category": "harvester",
  "physical_properties": { ... },
  "ports": { internal_module_ports: 0, ... },
  "systems": { stabilizer_unit: {...}, propulsion: {...}, ... },
  "cost_data": { purchase_cost: {...}, manufacturing_cost: {...}, maintenance: {...} },
  "blueprint_data": { base_material_efficiency: 0.0, materials: [{id, amount, unit}], ... }
}
```

### Phase 5: Assembly Jobs

**How it works**: Assembly jobs are created via `AssemblyService.start_assembly` which validates materials, calculates fees, and creates job records.

**Key files**:
- `app/services/manufacturing/assembly_service.rb` — Main assembly service
- `app/services/manufacturing/unit_module_assembly.rb` — Unit/module assembly
- `app/services/manufacturing/unit_deployment.rb` — Unit deployment logic
- `app/services/manufacturing/equipment_request.rb` — Equipment request handling
- `app/services/manufacturing/material_request_system.rb` — Material request system

**Mechanism**:
1. **Validation**: Blueprint and settlement type checks
2. **Material availability check**: Queries settlement inventory for owner-owned items
3. **FMV cost calculation**: Uses `Market::NpcPriceCalculator.calculate_ask`
4. **Tenant fee**: 10 GCC base + build_time_hours + material_count
5. **Affordability check**: Financial account balance verification
6. **Buy missing materials**: Optional — transfers funds and adds items to inventory
7. **Job creation**: Creates `Job` or `ConstructionJob` record based on blueprint category
8. **Fee charging**: Transfers GCC from requester to settlement

**Job types created**:
- `:unit_assembly` — For units/crafts (creates Job record)
- `:construction` — For structures/facilities (creates ConstructionJob record)

---

## Key Services Summary

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
| `ConstructionManager` | Construction coordination | `manufacturing/construction/` |

---

## Data Flow Diagram

```
[Celestial Body]
       │
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
[Material Processing Service]
       │
       ▼
[Processed Materials in Inventory]
       │
       ▼
[Blueprint Validation] ← [Blueprint Model]
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

---

## Playable Loop Summary

1. **Player establishes settlement** on a celestial body with raw material resources
2. **Extraction units** (PVE/TEU) process raw regolith from surface piles
3. **Byproducts** (water, gases) are stored in inventory; depleted regolith returned to surface
4. **Blueprints gate production** — player must have unlocked blueprints with sufficient licensed runs
5. **Assembly requests** validate material availability and charge tenant fees
6. **Production jobs** run asynchronously, producing components/structures over time
7. **Player claims finished products** from inventory for use in construction, crafting, or trade

---

## Architecture Gotchas Confirmed

✅ **GOTCHA 1**: Manufacturing services span multiple directories — confirmed (production_service.rb is the core orchestrator, but assembly_service.rb handles the player-facing workflow)

✅ **GOTCHA 2**: Blueprint gating is model-level — confirmed (Blueprint model has `can_manufacture?`, `consume_runs`, validations; NOT in service methods)

⚠️ **Additional Gotcha**: The Job model is used for both production jobs and assembly jobs, with different job_type values. ConstructionJob is a separate model for structure upgrades.

---

## Next Steps

1. Create `docs/new_agent/projects/galaxy_game/manufacturing/manufacturing_chain_overview.md`
2. Create `docs/new_agent/projects/galaxy_game/manufacturing/blueprint_reference.md`
3. Update task file status.md
4. Commit documentation
