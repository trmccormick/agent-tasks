# Research: Unit Order Tracking Options

**Date**: 2026-08-07
**Purpose**: Survey existing unit/craft command state patterns for CIV4-SURFACE-VIEW-GAMEPLAY unit_orders field
**Status**: Research only — no implementation

---

## Executive Summary

No explicit "unit order" or "unit command" system exists in the codebase. However, **two existing systems track work assignments** that can serve as the foundation:

1. **`Job` model** (generic production jobs) — tracks what a settlement is producing
2. **`ConstructionJob` model** (construction-specific jobs) — tracks construction tasks with status enums

Neither is unit-bound (they're settlement-bound), but they provide the enum/status pattern that would be needed for unit orders.

---

## 1. Existing Work Assignment Patterns

### A. `Job` Model — Generic Production Jobs

**File**: `galaxy_game/app/models/job.rb`

```ruby
class Job < ApplicationRecord
  belongs_to :owner, polymorphic: true
  belongs_to :settlement, class_name: 'Settlement::BaseSettlement'
  belongs_to :blueprint, optional: true

  enum job_type: {
    material_processing: 0,
    component_production: 1,
    smelting: 2,
    unit_assembly: 3,
    resource_processing: 4,
    environment_processing: 5
  }

  enum status: {
    in_progress: 0,
    ready_to_claim: 1,
    claimed: 2,
    failed: 3,
    cancelled: 4,
    pending: 5
  }

  validates :completes_at, presence: true, unless: -> { pending? }
end
```

**How it's created** (from services):
```ruby
# ManufacturingService.manufacture():
job = Job.create!(
  job_type: :unit_assembly,
  status: :in_progress,
  owner: owner,
  settlement: settlement,
  output_type: blueprint_name,
  completes_at: Time.current + production_time_hours.hours
)

# MaterialRequestService:
pressurization_job = Job.create!(
  job_type: :environment_processing,
  ...
)

# Manufacturing::MaterialProcessingService:
Job.create!(
  job_type: job_type.to_sym,
  ...
)
```

**Relevance**: The `job_type` + `status` enum pattern is exactly what unit orders would need. The gap is that jobs are settlement-bound, not unit-bound.

### B. `ConstructionJob` Model — Construction-Specific Jobs

**File**: `galaxy_game/app/models/construction_job.rb`

```ruby
class ConstructionJob < ApplicationRecord
  belongs_to :target_unit, class_name: 'Units::BaseUnit', optional: true, foreign_key: 'inflatable_id'
  belongs_to :regolith_source_settlement, class_name: 'Settlement::BaseSettlement', optional: true
  belongs_to :jobable, polymorphic: true
  belongs_to :blueprint, optional: true
  belongs_to :settlement, class_name: 'Settlement::BaseSettlement', foreign_key: 'settlement_id'

  enum job_type: {
    crater_dome_construction: 0,
    skylight_cover: 1,
    access_point_conversion: 2,
    habitat_expansion: 3,
    structure_upgrade: 4,
    shell_printing: 5,
    seal_printing: 6
  }

  enum status: {
    pending: 0,
    scheduled: 1,
    materials_pending: 2,
    equipment_pending: 3,
    workers_pending: 4,
    in_progress: 5,
    completed: 6,
    failed: 7,
    canceled: 8
  }
end
```

**Relevance**: 
- Has `target_unit` association (the closest thing to a unit-bound work assignment)
- Rich status enum with detailed stages (materials_pending → equipment_pending → workers_pending → in_progress → completed)
- **This is the best existing pattern to extend for unit orders** — it already connects jobs to units

### C. `Craft::BaseCraft#recalculate_stats` — Mining Rate as Implicit Order

**File**: `galaxy_game/app/models/craft/base_craft.rb` (lines 373-388)

```ruby
def recalculate_stats
  base_mining_rate = operational_data.dig('operational_properties', 'base_mining_rate_gcc_per_hour') || 0
  
  computer_units = base_units.select { |u| u.unit_type.include?('computer') }
  computer_boost = computer_units.sum { |u| u.operational_data.dig('operational_properties', 'mining_boost_gcc_per_hour').to_f }
  
  gpu_rigs = rigs.select { |r| r.rig_type == 'gpu_coprocessor_rig' }
  gpu_boost = gpu_rigs.sum { |r| r.operational_data.dig('operational_properties', 'processing_boost_gcc_per_hour').to_f }
  
  total_mining_rate = base_mining_rate + computer_boost + rigged_computer_boost
  
  operational_data['operational_properties']['current_mining_rate_gcc_per_hour'] = total_mining_rate.round(2)
end
```

**Relevance**: Mining rate stored in `operational_data` is an **implicit order indicator**. If a craft has `base_mining_rate > 0`, it's implicitly "mining." This could be made explicit by adding a `current_order` field.

### D. Unit/Structure Type as Implicit Role

**File**: `galaxy_game/app/models/units/base_unit.rb` and `galaxy_game/app/models/craft/base_craft.rb`

Units have `unit_type` (e.g., 'mining_laser', 'drill_unit', 'excavator', 'computer', 'habitat'). The type implies the role:
- Mining units → mining
- Computer units → boosting other units
- Habitat units → housing
- Storage units → storing

**Relevance**: Unit type already encodes some "order" information. A `mining_laser` unit is implicitly assigned to mining. This could be the default order, overridable by an explicit field.

### E. `BaseUnit#job_types` — Supported Job Types

**File**: `galaxy_game/app/models/units/base_unit.rb`

```ruby
def job_types
  operational_data&.dig('job_types', 'supported') || []
end

def max_concurrent_jobs
  operational_data&.dig('job_types', 'max_concurrent') || 1
end

def supports_job_type?(job_type)
  job_types.include?(job_type.to_s)
end
```

**Relevance**: Units already declare what job types they support and how many concurrent jobs they can handle. This is the **capability layer** that unit orders would build on.

---

## 2. Closest Existing Patterns to Extend

### Pattern 1: Extend `ConstructionJob` to Support Unit Orders

**Approach**: Generalize `ConstructionJob` beyond construction to cover all unit work assignments.

```ruby
# Option A: Rename/generalize ConstructionJob
class WorkAssignment < ApplicationRecord
  belongs_to :unit, polymorphic: true
  belongs_to :settlement, class_name: 'Settlement::BaseSettlement', optional: true
  
  enum order_type: {
    mining: 0,
    moving: 1,
    idle: 2,
    constructing: 3,
    harvesting: 4,
    exploring: 5,
    defending: 6
  }
  
  enum status: {
    pending: 0,
    in_progress: 1,
    completed: 2,
    failed: 3,
    canceled: 4
  }
  
  # Target location for movement/mining/harvesting
  attr_accessor :target_col, :target_row
  
  # Time remaining (seconds)
  attr_accessor :time_remaining
end
```

**Pros**:
- Follows the existing `ConstructionJob` pattern exactly
- Already has `target_unit` association
- Rich status enum with detailed stages
- Settlement-bound for AI Manager coordination

**Cons**:
- ConstructionJob is heavily tied to construction (shell_printing, seal_printing, etc.)
- Would need to decide: extend ConstructionJob or create new model?

### Pattern 2: Add `current_order` to Unit/Craft Models

**Approach**: Add a simple order field directly on units/crafts.

```ruby
# On Units::BaseUnit and Craft::BaseCraft:
enum current_order: {
  idle: 0,
  mining: 1,
  moving: 2,
  constructing: 3,
  harvesting: 4,
  exploring: 5,
  defending: 6
}

# Or as JSONB for more complex orders:
# operational_data['current_order'] = {
#   type: 'mining',
#   target_col: 55,
#   target_row: 42,
#   time_remaining: 120
# }
```

**Pros**:
- Simplest approach — no new model needed
- Directly answers "what is this unit doing right now?"
- Easy to query: `Unit.where(current_order: 'mining')`

**Cons**:
- Loses the detailed status tracking that ConstructionJob provides
- No history of past orders (can't show "was mining, now moving")
- Doesn't support queued orders (Civ4-style action queues)

### Pattern 3: Hybrid — `current_order` + WorkAssignment Model

**Approach**: Simple current state on unit, detailed assignment in a separate model.

```ruby
# On Units::BaseUnit:
enum current_order: { idle: 0, mining: 1, moving: 2, constructing: 3, harvesting: 4, exploring: 5, defending: 6 }

# WorkAssignment model (extends ConstructionJob pattern):
class WorkAssignment < ApplicationRecord
  belongs_to :unit, polymorphic: true
  enum order_type: { mining: 0, moving: 1, idle: 2, constructing: 3, harvesting: 4, exploring: 5, defending: 6 }
  enum status: { pending: 0, in_progress: 1, completed: 2, failed: 3, canceled: 4 }
  
  # Target info
  attr_accessor :target_col, :target_row, :time_remaining
  
  # Blueprint/plan reference
  belongs_to :blueprint, optional: true
end
```

**Pros**:
- Best of both worlds: simple current state + detailed assignment history
- Follows existing patterns from both Job and ConstructionJob
- Supports queued orders (multiple WorkAssignments per unit)

**Cons**:
- Most complex to implement
- Requires deciding how units get assigned work (AI Manager? Player input?)

---

## 3. Recommended Approach for terrain_data_builder.rb Export

For the **immediate export need** (CIV4-SURFACE-VIEW-GAMEPLAY), **Pattern 2 (simple `current_order` on unit)** is recommended because:

1. It's the simplest to implement now
2. The export just needs to show "what is this unit doing" — idle/mining/moving/etc.
3. Can be extended later with WorkAssignment model for queued orders and history

**Export pseudocode**:
```ruby
def extract_unit_orders
  craft = celestial_body.orbiting_craft.to_a
  locations = celestial_body.locations.to_a
  
  (craft + locations).map do |entity|
    pos = get_grid_position(entity)
    next unless pos
    
    {
      unit_id: entity.id,
      order: entity.current_order || 'idle',  # or derive from unit_type if no current_order field
      target_col: pos[0],
      target_row: pos[1],
      time_remaining: nil  # stub — needs WorkAssignment model for real values
    }
  end.compact
end
```

---

## 4. Files Referenced

| File | Relevance |
|---|---|
| `galaxy_game/app/models/job.rb` | Generic production job enum/status pattern |
| `galaxy_game/app/models/construction_job.rb` | Unit-bound work assignment with detailed status stages |
| `galaxy_game/app/models/units/base_unit.rb` | `job_types`, `max_concurrent_jobs`, `supports_job_type?` capability methods |
| `galaxy_game/app/models/craft/base_craft.rb` (lines 373-388) | `recalculate_stats` mining rate as implicit order indicator |
| `galaxy_game/app/services/manufacturing_service.rb` | Job creation pattern (`job_type`, `status`, `completes_at`) |
| `galaxy_game/app/services/material_request_service.rb` | Job creation for environment processing |
| `galaxy_game/app/services/manufacturing/material_processing_service.rb` | Job creation for material processing |
| `galaxy_game/app/services/manufacturing/production_service.rb` | Job creation for component production |
| `galaxy_game/app/services/crater_dome_construction_service.rb` | ConstructionJob creation pattern |
