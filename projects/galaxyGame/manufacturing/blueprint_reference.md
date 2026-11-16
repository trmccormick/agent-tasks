# Blueprint Reference

## Overview

Blueprints in galaxy_game define what can be manufactured, assembled, or constructed. They exist in two complementary forms:

1. **Database records** (`Blueprint` model) — Licensing, research progress, efficiency tracking
2. **JSON data files** (`data/json-data/blueprints/`) — Full specifications, costs, materials, physical properties

Both must align for production to succeed. The database record gates *whether* something can be produced; the JSON file defines *what* is produced and *how much* it costs.

---

## Blueprint Model (Database)

### Table: `blueprints`

| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | integer | primary key | Auto-incrementing ID |
| `name` | string | NOT NULL | Blueprint identifier (used for lookups) |
| `player_id` | integer | NOT NULL, FK → players | Owner of the blueprint |
| `current_research_level` | integer | >= 0 | Research progress (improves efficiency) |
| `material_efficiency` | decimal | >= 0 | Material cost reduction factor |
| `time_efficiency` | decimal | >= 0 | Production time reduction factor |
| `licensed_runs_remaining` | integer \| null | >= 0 or NULL | Remaining production count; NULL = unlimited (NPCs) |
| `created_at` | datetime | — | Record creation timestamp |
| `updated_at` | datetime | — | Last update timestamp |

### Model Methods

| Method | Return Type | Description |
|---|---|---|
| `materials` | Hash `{name => amount}` | Returns required materials from JSON lookup |
| `can_manufacture?(quantity = 1)` | Boolean | Checks licensed runs remaining >= quantity (nil = true) |
| `consume_runs(quantity = 1)` | nil | Decrements licensed_runs_remaining after production |
| `calculate_efficiencies` | nil | Updates material_efficiency and time_efficiency from JSON research_effects |

### Validations

```ruby
validates :name, presence: true
validates :current_research_level, numericality: { greater_than_or_equal_to: 0 }
validates :material_efficiency, numericality: { greater_than_or_equal_to: 0 }
validates :time_efficiency, numericality: { greater_than_or_equal_to: 0 }
validates :licensed_runs_remaining, numericality: { greater_than_or_equal_to: 0 }, allow_nil: true
```

---

## Blueprint JSON Structure

### Base Schema

All blueprints share a common structure with category-specific fields. The schema is defined by the `template` field which determines which base template applies.

### Common Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `template` | string | Yes | Base template name (e.g., "base_craft", "base_structure") |
| `id` | string | Yes | Unique blueprint identifier (snake_case) |
| `name` | string | Yes | Display name |
| `description` | string | Yes | Human-readable description |
| `type` | string | Yes | Top-level type (craft, structure, module, etc.) |
| `category` | string | Yes | Sub-category for routing and organization |

### Category-Specific Field Sets

#### Crafts (`template: base_craft`)

```json
{
  "template": "base_craft",
  "id": "regolith_harvester_rover",
  "name": "RH-400 Regolith Harvester Rover",
  "description": "...",
  "type": "craft",
  "category": "harvester",

  "physical_properties": {
    "length_m": 6.80,
    "width_m": 3.30,
    "height_m": 2.65,
    "empty_mass_kg": 22800.0,
    "volume_m3": 59.5,
    "drag_coefficient": 0.9
  },

  "mounting_points": [],
  "compatible_units": [],
  "compatible_modules": [],
  "crew_capacity": 0,

  "ports": {
    "internal_module_ports": 0,
    "external_module_ports": 0,
    "internal_fuel_storage_ports": 0,
    "external_fuel_storage_ports": 0,
    "internal_unit_ports": 1,
    "external_unit_ports": 0,
    "propulsion_ports": 0,
    "storage_ports": 0,
    "internal_rig_ports": 0,
    "external_rig_ports": 0,
    "umbilical_ports": 0
  },

  "systems": {
    "stabilizer_unit": { "status": "offline" },
    "propulsion": { "status": "offline" },
    "power": { "status": "offline" },
    "life_support": { "status": "offline" },
    "navigation": { "status": "offline" }
  },

  "cost_data": {
    "purchase_cost": { "currency": "GCC", "amount": 45000 },
    "manufacturing_cost": { "currency": "GCC", "amount": 42750 },
    "maintenance": {
      "time_to_repair_hours": 6,
      "repair_cost_gcc": 1500,
      "materials_needed_for_repair": [
        { "id": "steel", "amount": 450.0, "unit": "kilogram" },
        { "id": "electronics", "amount": 100.0, "unit": "kilogram" }
      ]
    }
  },

  "blueprint_data": {
    "base_material_efficiency": 0.0,
    "base_time_efficiency": 0.0,
    "materials": [
      { "id": "aluminum", "amount": 4200.0, "unit": "kilogram" },
      { "id": "electronics", "amount": 850.0, "unit": "kilogram" }
    ],
    "required_materials": {
      "aluminum": { "amount": 4200.0 },
      "electronics": { "amount": 850.0 }
    },
    "production_data": {
      "build_time_hours": 24,
      "production_time_hours": 2.0
    },
    "research_effects": {
      "material_efficiency": {
        "start_value": 0.0,
        "improvement_percentage_per_research_level": 5.0
      },
      "time_efficiency": {
        "start_value": 0.0,
        "improvement_percentage_per_research_level": 3.0
      }
    }
  }
}
```

#### Structures (`template: base_structure`)

```json
{
  "template": "base_structure",
  "id": "habitation_module_a",
  "name": "Habitation Module A",
  "description": "...",
  "type": "structure",
  "category": "habitation",

  "physical_properties": {
    "dimensions_m": [10.0, 10.0, 5.0],
    "mass_kg": 15000.0,
    "volume_m3": 500.0
  },

  "ports": {
    "internal_module_ports": 2,
    "external_module_ports": 4,
    "umbilical_ports": 6
  },

  "systems": {
    "life_support": { "capacity": 10, "status": "online" },
    "power": { "generation_kw": 50.0, "storage_kwh": 200.0 },
    "structural_integrity": { "rating": 100 }
  },

  "cost_data": {
    "purchase_cost": { "currency": "GCC", "amount": 120000 },
    "manufacturing_cost": { "currency": "GCC", "amount": 95000 },
    "maintenance": {
      "time_to_repair_hours": 12,
      "repair_cost_gcc": 5000,
      "materials_needed_for_repair": [
        { "id": "steel", "amount": 2000.0, "unit": "kilogram" },
        { "id": "electronics", "amount": 500.0, "unit": "kilogram" }
      ]
    }
  },

  "blueprint_data": {
    "base_material_efficiency": 0.0,
    "base_time_efficiency": 0.0,
    "materials": [
      { "id": "steel", "amount": 8000.0, "unit": "kilogram" },
      { "id": "electronics", "amount": 2000.0, "unit": "kilogram" }
    ],
    "required_materials": {
      "steel": { "amount": 8000.0 },
      "electronics": { "amount": 2000.0 }
    },
    "production_data": {
      "build_time_hours": 48,
      "construction_required": true
    },
    "research_effects": {
      "material_efficiency": {
        "start_value": 0.0,
        "improvement_percentage_per_research_level": 4.0
      },
      "time_efficiency": {
        "start_value": 0.0,
        "improvement_percentage_per_research_level": 2.5
      }
    }
  }
}
```

#### Components/Materials (`template: base_component`)

```json
{
  "template": "base_component",
  "id": "steel_alloy",
  "name": "Steel Alloy",
  "description": "Processed steel alloy for construction and manufacturing.",
  "type": "component",
  "category": "materials",

  "cost_data": {
    "manufacturing_cost": { "currency": "GCC", "amount": 150.0 },
    "maintenance": {
      "time_to_repair_hours": 0,
      "repair_cost_gcc": 0
    }
  },

  "blueprint_data": {
    "base_material_efficiency": 0.0,
    "base_time_efficiency": 0.0,
    "materials": [
      { "id": "iron_ore", "amount": 100.0, "unit": "kilogram" },
      { "id": "carbon", "amount": 10.0, "unit": "kilogram" }
    ],
    "required_materials": {
      "iron_ore": { "amount": 100.0 },
      "carbon": { "amount": 10.0 }
    },
    "production_data": {
      "build_time_hours": 4,
      "output_amount": 90.0
    },
    "research_effects": {
      "material_efficiency": {
        "start_value": 0.0,
        "improvement_percentage_per_research_level": 6.0
      },
      "time_efficiency": {
        "start_value": 0.0,
        "improvement_percentage_per_research_level": 4.0
      }
    }
  }
}
```

---

## Blueprint Categories and Purposes

### Crafts (Vehicles)

| Subcategory | Location | Purpose |
|---|---|---|
| `atmospheric` | `blueprints/crafts/atmospheric/` | Vehicles for atmospheric flight |
| `ground` | `blueprints/crafts/ground/` | Surface rovers and harvesters |
| `space` | `blueprints/crafts/space/` | Orbital and interplanetary craft |

**Key fields**: `physical_properties`, `ports`, `systems`, `compatible_units`, `compatible_modules`, `crew_capacity`

### Structures (Buildings)

| Subcategory | Location | Purpose |
|---|---|---|
| `habitation` | `blueprints/structures/habitation/` | Living quarters for personnel |
| `landing_infrastructure` | `blueprints/structures/landing_infrastructure/` | Landing pads and docking stations |
| `life_support` | `blueprints/structures/life_support/` | Environmental control systems |
| `manufacturing` | `blueprints/structures/manufacturing/` | Production facilities |
| `mega_structures` | `blueprints/structures/mega_structures/` | Large-scale installations |

**Key fields**: `physical_properties`, `ports`, `systems`, `construction_required`

### Modules (Spacecraft Components)

| Location | Purpose |
|---|---|
| `blueprints/modules/` | Interchangeable spacecraft modules |

**Key fields**: `ports`, `compatible_units`, `crew_capacity`, `systems`

### Rigs (Equipment Mounts)

| Location | Purpose |
|---|---|
| `blueprints/rigs/` | Equipment rigs for mounting on vehicles/structures |

**Key fields**: `mounting_points`, `compatible_units`, `ports`

### Units (Individual Entities)

| Location | Purpose |
|---|---|
| `blueprints/units/` | Individual production/extraction units |

**Key fields**: `operational_properties`, `power_requirements`, `output_resources`

### Components (Raw Materials)

| Subcategory | Location | Purpose |
|---|---|---|
| `electronics` | `blueprints/components/electronics/` | Electronic components |
| `materials` | `blueprints/components/materials/` | Raw material compounds |
| `mechanical` | `blueprints/components/mechanical/` | Mechanical parts |
| `production` | `blueprints/components/production/` | Production-specific components |
| `specialized` | `blueprints/components/specialized/` | Specialized components |

**Key fields**: `required_materials`, `production_data.output_amount`

### Items (Miscellaneous)

| Subcategory | Location | Purpose |
|---|---|---|
| `components` | `blueprints/items/components/` | Item-level components |
| `consumables` | `blueprints/items/consumables/` | Single-use items |

**Key fields**: `required_materials`, `production_data`

### Materials (Processed Resources)

| Location | Purpose |
|---|---|
| `blueprints/materials/` | Processed resource definitions |

**Key fields**: `required_materials`, `production_data.output_amount`

### Ports (Infrastructure)

| Location | Purpose |
|---|---|
| `blueprints/ports/` | Port and docking infrastructure |

**Key fields**: `ports`, `compatible_units`

---

## Validation Rules Per Category

### Crafts

| Rule | Description |
|---|---|
| Port compatibility | `ports` must be non-negative integers |
| System status | All systems must have a valid status ("online"/"offline") |
| Physical properties | Must include mass, volume, and dimensional data |
| Cost data | Must have both purchase_cost and manufacturing_cost |
| Materials | `blueprint_data.materials` must specify id, amount, unit |

### Structures

| Rule | Description |
|---|---|
| Construction flag | `production_data.construction_required` should be true |
| Systems capacity | Life support must have capacity; power must have generation/storage |
| Structural integrity | Must include structural_integrity rating |
| Build time | `build_time_hours` typically >= 24 for structures |

### Components/Materials

| Rule | Description |
|---|---|
| Output amount | `production_data.output_amount` must be > 0 |
| Input validation | Total input mass >= output mass (no free matter) |
| Research effects | Must define improvement percentages per research level |

### All Categories

| Rule | Description |
|---|---|
| Template match | JSON `template` field must match expected base template |
| ID uniqueness | Blueprint `id` must be unique within its category |
| Required materials | `required_materials` must align with `materials` array |
| Research effects | Must define both material_efficiency and time_efficiency improvements |

---

## How Blueprints Gate Production Orders

### Gating Flow

```
Player requests production of "regolith_harvester_rover"
    │
    ▼
1. Lookup Blueprint model by name
    → Blueprint.find_by(name: "regolith_harvester_rover")
    → If nil, production fails (blueprint not unlocked)
    │
    ▼
2. Check can_manufacture?(quantity)
    → licensed_runs_remaining.nil? → true (unlimited for NPCs)
    → licensed_runs_remaining >= quantity → true/false
    → If false, raise "Insufficient licensed runs"
    │
    ▼
3. Lookup Blueprint JSON data
    → Lookup::BlueprintLookupService.find_blueprint("regolith_harvester_rover")
    → If nil, production fails (JSON data missing)
    │
    ▼
4. Check material availability in settlement inventory
    → AssemblyService.check_material_availability(blueprint_data, settlement, requester)
    → For each required_material:
        available = settlement.inventory.items.where(name: mat_name, owner: requester).sum(:amount)
        if available < required_amount → add to missing list
    │
    ▼
5. Calculate tenant fee
    → 10 + build_time_hours + material_count
    │
    ▼
6. Check affordability
    → Financial::Account.balance >= fee
    → If false, raise "Cannot afford tenant fee"
    │
    ▼
7. (Optional) Buy missing materials from NPC stock
    → Transfer funds, add items to settlement inventory
    │
    ▼
8. Create production/assembly job
    → Job.create!(job_type: :unit_assembly, ...) or ConstructionJob.create!(...)
    │
    ▼
9. Consume licensed runs (if applicable)
    → blueprint.consume_runs(quantity)
    │
    ▼
10. Production completes at completes_at time
    → Job status changes to ready_to_claim
    → Player claims finished product from inventory
```

### Gating Points Summary

| Gate | Enforced By | What it checks |
|---|---|---|
| Blueprint unlocked | `Blueprint.find_by(name:)` | Player owns the blueprint record |
| Licensed runs | `can_manufacture?` | Remaining runs >= quantity (or nil = unlimited) |
| Material availability | `check_material_availability` | Settlement inventory has owner-owned items |
| Tenant fee | `can_afford_fee?` | Financial account balance >= fee |
| JSON data exists | `BlueprintLookupService.find_blueprint` | Blueprint JSON file exists and is valid |
| Category routing | `determine_job_type` | Blueprint category determines Job vs ConstructionJob |

---

## Example Blueprints from Data Files

### Example 1: Ground Craft (Regolith Harvester)

**File**: `data/json-data/blueprints/crafts/ground/regolith_harvesting_rover_bp.json`

```json
{
  "template": "base_craft",
  "id": "regolith_harvester_rover",
  "name": "RH-400 Regolith Harvester Rover",
  "type": "craft",
  "category": "harvester",
  "physical_properties": {
    "length_m": 6.80,
    "width_m": 3.30,
    "height_m": 2.65,
    "empty_mass_kg": 22800.0,
    "volume_m3": 59.5,
    "drag_coefficient": 0.9
  },
  "ports": {
    "internal_unit_ports": 1,
    "storage_ports": 0,
    "external_unit_ports": 0
  },
  "cost_data": {
    "purchase_cost": { "currency": "GCC", "amount": 45000 },
    "manufacturing_cost": { "currency": "GCC", "amount": 42750 }
  },
  "blueprint_data": {
    "materials": [
      { "id": "aluminum", "amount": 4200.0, "unit": "kilogram" },
      { "id": "electronics", "amount": 850.0, "unit": "kilogram" }
    ],
    "required_materials": {
      "aluminum": { "amount": 4200.0 },
      "electronics": { "amount": 850.0 }
    },
    "production_data": {
      "build_time_hours": 24
    }
  }
}
```

### Example 2: Component (Steel Alloy)

**File**: `data/json-data/blueprints/components/materials/` (steel alloy definition)

```json
{
  "template": "base_component",
  "id": "steel_alloy",
  "name": "Steel Alloy",
  "type": "component",
  "category": "materials",
  "blueprint_data": {
    "materials": [
      { "id": "iron_ore", "amount": 100.0, "unit": "kilogram" },
      { "id": "carbon", "amount": 10.0, "unit": "kilogram" }
    ],
    "required_materials": {
      "iron_ore": { "amount": 100.0 },
      "carbon": { "amount": 10.0 }
    },
    "production_data": {
      "build_time_hours": 4,
      "output_amount": 90.0
    }
  }
}
```

---

## Blueprint JSON Directory Structure

```
data/json-data/blueprints/
├── components/          # Raw component definitions
│   ├── electronics/
│   ├── materials/
│   ├── mechanical/
│   ├── production/
│   └── specialized/
├── crafts/              # Vehicle blueprints
│   ├── atmospheric/
│   ├── ground/
│   └── space/
├── infrastructure/      # Infrastructure blueprints
├── items/               # Miscellaneous items
│   ├── components/
│   └── consumables/
├── materials/           # Processed resource definitions
├── modules/             # Spacecraft modules
├── ports/               # Port/docking infrastructure
├── rigs/                # Equipment rigs
├── structures/          # Building/facility blueprints
│   ├── habitation/
│   ├── landing_infrastructure/
│   ├── life_support/
│   ├── manufacturing/
│   └── mega_structures/
└── units/               # Individual unit definitions
```

---

## Lookup Service

Blueprints are accessed via `Lookup::BlueprintLookupService`:

```ruby
service = Lookup::BlueprintLookupService.new
data = service.find_blueprint("regolith_harvester_rover")
# Returns the JSON hash for the blueprint, or nil if not found
```

The lookup service reads from `data/json-data/blueprints/` and returns the full JSON specification including all category-specific fields.

---

## Research Effects

All blueprints include research effects that improve production efficiency as the player advances:

| Field | Description |
|---|---|
| `base_material_efficiency` | Starting material cost reduction (0.0 = no reduction) |
| `base_time_efficiency` | Starting time cost reduction (0.0 = no reduction) |
| `research_effects.material_efficiency.start_value` | Base material efficiency at research level 0 |
| `research_effects.material_efficiency.improvement_percentage_per_research_level` | How much material efficiency improves per research level |
| `research_effects.time_efficiency.start_value` | Base time efficiency at research level 0 |
| `research_effects.time_efficiency.improvement_percentage_per_research_level` | How much time efficiency improves per research level |

**Calculation** (via `Blueprint#calculate_efficiencies`):
```ruby
self.material_efficiency = start_value + (current_research_level * improvement_per_level)
self.time_efficiency = start_value + (current_research_level * improvement_per_level)
```

---

## Tenant Fee Calculation

Assembly jobs charge a tenant fee based on blueprint complexity:

```ruby
tenant_fee = 10 + build_time_hours + material_count
```

| Component | Source | Example |
|---|---|---|
| Base fee | Fixed | 10 GCC |
| Build time | `blueprint_data.production_data.build_time_hours` | 24 hours → 24 GCC |
| Material count | `blueprint_data.required_materials.size` | 3 materials → 3 GCC |
| **Total** | | **37 GCC** |

---

## Stop Conditions for Contributors

If you encounter any of the following while working with blueprints:

1. **Blueprint JSON missing**: The file doesn't exist in `data/json-data/blueprints/` — this is a data gap, not a code issue
2. **ID mismatch**: Blueprint model name doesn't match any JSON `id` field — check for typos or stale references
3. **Invalid template**: JSON `template` field doesn't match expected base template — verify against schema
4. **Negative licensed runs**: Should be prevented by validation, but if encountered, check for race conditions in `consume_runs`
