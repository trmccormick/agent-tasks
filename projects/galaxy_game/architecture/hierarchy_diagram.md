# Model Hierarchy Diagram

## Colony → Settlement → Structure Hierarchy

```
Colony
├── belongs_to :celestial_body (CelestialBodies::CelestialBody)
├── has_many :settlements (Settlement::BaseSettlement)
│   ├── has_one :marketplace (Market::Marketplace)
│   ├── has_one :location (Location::CelestialLocation)
│   ├── has_many :docked_crafts (Craft::BaseCraft)
│   ├── has_many :base_units / units (Units::BaseUnit)
│   ├── has_many :orbital_construction_projects
│   ├── has_many :jobs
│   ├── has_many :construction_jobs
│   ├── has_one :inventory → surface_storage
│   ├── has_one :account (Financial::Account)
│   │
│   ├── Settlement::BaseSettlement (enum: base, outpost, settlement, city, station)
│   ├── Settlement::OrbitalSettlement (STI — shares base_settlements table)
│   │   └── [replaced SpaceStation + OrbitalDepot on 2026-04-11]
│   │
│   └── has_many :structures (Structures::BaseStructure) ← via SettlementCore concern
│       ├── belongs_to :settlement (Settlement::BaseSettlement)
│       ├── belongs_to :owner (polymorphic)
│       ├── belongs_to :location (polymorphic, optional)
│       ├── belongs_to :container_structure (self-referential, optional)
│       ├── has_many :contained_structures (self-referential)
│       ├── has_many :modules (Modules::BaseModule)
│       ├── has_many :units (Units::BaseUnit)
│       ├── has_many :rigs (Rigs::BaseRig)
│       └── has_one :atmosphere
│       │
│       └── Structures::BaseStructure STI children:
│           ├── access_point.rb
│           ├── base_structure.rb (table_name = 'structures')
│           ├── converted_base.rb
│           ├── crater_dome.rb
│           ├── habitation_facility.rb
│           ├── hangar.rb
│           ├── manufacturing_facility.rb
│           ├── orbital_structure.rb
│           ├── power_station.rb
│           ├── segment_component.rb
│           ├── skylight.rb
│           ├── solar_array.rb
│           ├── storage_facility.rb
│           ├── worldhouse.rb
│           └── worldhouse_segment.rb
│
├── has_one :account (Financial::Account)
├── has_one :inventory
├── has_many :items (through inventory)
└── validates :must_have_multiple_settlements (min 2 settlements required)
```

### Key Insight: Two Parallel Hierarchies

Colony and Settlement share a **parallel** hierarchy, not a strict parent-child tree:

1. **Colony → Settlement**: A Colony owns multiple Settlements (each on its own celestial body)
2. **Settlement → Structure**: Each Settlement contains multiple Structures (physical buildings/hulls)
3. **Lateral link**: Colony ↔ Settlement are both `has_many`/`belongs_to` via `colony_id` foreign key, but Settlements also have their own independent identity (location, marketplace, etc.)

---

## Model Details

### Colony
- **Table**: `colonies`
- **Key fields**: `name`, `celestial_body_id`
- **Responsibilities**:
  - Owns a celestial body
  - Aggregates population across all settlements
  - Calculates resource requirements (food, water, oxygen)
  - Manages financial account and inventory at the colony level
  - Enforces minimum 2 settlements constraint
- **Associations**: `belongs_to :celestial_body`, `has_many :settlements`

### Settlement::BaseSettlement
- **Table**: `base_settlements` (jsonb column: `operational_data`)
- **Key fields**: `settlement_type` (enum: base, outpost, settlement, city, station), `colony_id`, `current_population`
- **Responsibilities**:
  - Life support management (food/water/energy per person)
  - Manufacturing efficiency and construction cost calculation
  - Unit/module storage and management
  - Energy management
  - Job/construction job tracking
  - Marketplace integration
- **Concerns included**: `SettlementCore`, `GameConstants`, `LifeSupport`, `CryptocurrencyMining`, `HasUnitStorage`, `EnergyManagement`
- **Associations**: `belongs_to :colony`, `has_many :structures`, `has_one :location`, `has_one :marketplace`

### Settlement::OrbitalSettlement (Active)
- **Table**: `base_settlements` (STI — shares table with BaseSettlement)
- **Responsibilities**:
  - Represents orbital settlements as a constellation of structures
  - Aggregates storage and population capacity across all deployed structures
  - Supports adding specialized structures via `add_specialized_structure!`
- **Key methods**: `location` (via first structure), `celestial_body`, `total_storage_capacity`, `population_capacity`
- **Status**: ✅ ACTIVE — this is the current class for all orbital settlements

### Structures::BaseStructure
- **Table**: `structures` (custom table_name, uses `structures_` prefix)
- **Key fields**: `name`, `structure_name`, `structure_type`, `current_population`, `operational_data`
- **Responsibilities**:
  - Physical building/hull management
  - Population capacity calculation from installed habitat units
  - Module/rig/unit attachment system
  - Resource input/output management
  - Inventory and atmosphere initialization
- **Concerns included**: `HasModules`, `HasRigs`, `RigAttachable`, `HasUnits`, `GameConstants`, `HasUnitStorage`, `HasExternalConnections`, `EnergyManagement`
- **Associations**: `belongs_to :settlement`, `belongs_to :owner` (polymorphic), `has_many :modules`, `has_many :units`, `has_many :rigs`

---

## Lateral Associations

### Non-Hierarchical Relationships

| Association | Type | Notes |
|---|---|---|
| Settlement → CelestialBody | via Location | Settlement.location → celestial_body (not direct Colony link) |
| Structure → CelestialLocation | via Location | Each structure has its own polymorphic location |
| Colony ↔ Settlement | via colony_id | Colony owns settlements, but settlements have independent identity |
| Structure ↔ ContainerStructure | self-referential | Structures can contain other structures (nested buildings) |
| Settlement → Marketplace | has_one | Each settlement has its own marketplace |
| Settlement → Jobs | has_many | Jobs are unified model linking settlement to work |

### Important: SettlementCore Concern

The `SettlementCore` concern (included in `BaseSettlement`) defines the core associations shared across all settlement types:
- `belongs_to :owner` (polymorphic)
- `belongs_to :colony` (optional)
- `has_many :structures` → **this is how Settlements own Structures**
- `has_many :missions`
- `has_one/many :accounts`

---

## Namespace History: OrbitalDepot → OrbitalSettlement

### Timeline

| Date | Event | Details |
|---|---|---|
| ~2025-12-16 | OrbitalDepot created (root-level) | First commit showing `orbital_depot.rb` at root level with PORO (Plain Old Ruby Object) implementation |
| ~2025-12-16 | Settlement::OrbitalDepot introduced | Namespaced version added alongside root-level class, inheriting from BaseSettlement |
| 2026-03-07 | Inheritance fix | `Settlement::OrbitalDepot` fixed to be a sibling of `SpaceStation` (both inherit directly from `BaseSettlement`), not a subclass. Includes `Structures::Shell` and `Docking` modules. |
| 2026-04-11 | Retirement | Both `Settlement::SpaceStation` and `Settlement::OrbitalDepot` were retired. All call sites rewired to `Settlement::OrbitalSettlement`. PORO and DepotWrapper logic removed. |
| 2026-07-31 | Stub files kept | Both retired files kept as empty class stubs for git history preservation only |

### Why the Change?

The namespace migration from `OrbitalDepot`/`SpaceStation` to `OrbitalSettlement` was driven by:

1. **Naming clarity**: "OrbitalDepot" conflated two concepts — orbital settlement (population/living) and depot (storage/gas). The term "OrbitalSettlement" is more accurate for the unified concept.
2. **Architecture consolidation**: Having both `SpaceStation` and `OrbitalDepot` as separate settlement types created duplication. The new `OrbitalSettlement` uses a constellation model — one settlement backed by multiple physical structures (station hulls, depots, etc.).
3. **Structure separation**: Physical storage/gas operations were moved to the market order system and structure layer (`Structures::OrbitalStructure`), separating concerns more cleanly.

### Migration Notes

- **Data migration**: All existing `SpaceStation` and `OrbitalDepot` records were migrated to `OrbitalSettlement` type
- **Model changes**: 
  - `Settlement::OrbitalSettlement` shares the `base_settlements` table (STI)
  - Physical hulls are now `Structures::OrbitalStructure` instances
  - Storage capacity is aggregated across all deployed structures
- **Breaking changes**: Any code referencing `Settlement::SpaceStation` or `Settlement::OrbitalDepot` directly must be updated to use `Settlement::OrbitalSettlement`

### Current State

- ✅ **Active class**: `Settlement::OrbitalSettlement` — use this for all orbital settlements
- ✅ **Physical hulls**: `Structures::OrbitalStructure` — represents the physical structure constellation
- ❌ **Retired**: `Settlement::SpaceStation` — empty stub file, kept for git history
- ❌ **Retired**: `Settlement::OrbitalDepot` — empty stub file, kept for git history
- ❌ **Retired**: Root-level `OrbitalDepot` — empty stub file, kept for git history

### All Code Should Reference

```ruby
# ✅ CORRECT
Settlement::OrbitalSettlement
Structures::OrbitalStructure

# ❌ WRONG (retired)
Settlement::SpaceStation    # empty stub
Settlement::OrbitalDepot    # empty stub
OrbitalDepot                # root-level, empty stub
```

---

## Evidence Sources

### Model Files Audited
| File | Verified |
|---|---|
| `app/models/colony.rb` | ✅ has_many :settlements, belongs_to :celestial_body |
| `app/models/settlement/base_settlement.rb` | ✅ includes SettlementCore, has_many :structures |
| `app/models/settlement/orbital_settlement.rb` | ✅ STI, shares base_settlements table |
| `app/models/settlement/orbital_depot.rb` | ❌ Retired 2026-04-11, empty stub |
| `app/models/settlement/space_station.rb` | ❌ Retired 2026-04-11, empty stub |
| `app/models/structures/base_structure.rb` | ✅ belongs_to :settlement, has_many :modules/units/rigs |
| `app/models/structures.rb` | ✅ table_prefix = "structures_" |
| `app/models/concerns/settlement/settlement_core.rb` | ✅ defines core associations for all settlements |

### Git History Verified
| Commit | Date | Significance |
|---|---|---|
| `7b327702` / `3b943004` | 2025-12-16 | First appearance of both root and namespaced OrbitalDepot |
| `fe85acd0` | 2026-03-07 | Fixed OrbitalDepot inheritance — sibling of SpaceStation |
| `bee0a625` | 2026-04-11 | Retired SpaceStation + OrbitalDepot, rewired to OrbitalSettlement |
| `08119b53` | 2026-07-31 | Latest commit touching orbital_depot.rb (stub file only) |
