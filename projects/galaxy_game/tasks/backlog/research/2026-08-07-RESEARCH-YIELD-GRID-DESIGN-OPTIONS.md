# Research: Yield Grid Design Options

**Date**: 2026-08-07
**Purpose**: Survey existing per-tile/per-area production patterns for CIV4-SURFACE-VIEW-GAMEPLAY yield_grid field
**Status**: Research only — no implementation

---

## Executive Summary

No per-tile yield system exists anywhere in the codebase. However, several **existing systems produce per-entity output values** that could serve as a pattern for designing a tile-level yield system. The closest conceptual match is the ECLSS loop architecture (per-module production rates) combined with LunaOperationsSimulationService's resource delta tracking.

---

## 1. Existing Per-Entity Production Patterns

### A. `LunaOperationsSimulationService` — Closest Pattern

**File**: `galaxy_game/app/services/luna_operations_simulation_service.rb`

This service tracks **per-resource production/consumption deltas** at the colony level:

```ruby
TRACKED_RESOURCES = %w[oxygen hydrogen water food regolith].freeze

# Per-tick, it computes:
production = {}   # resource => amount produced this tick (kg or L)
consumption = {}  # resource => amount consumed this tick (kg or L)
deltas[resource] = prod - cons  # net delta per tracked resource
```

**Tiered production model**:
- **Tier A**: Human crew life support (hard requirement, cannot be paused)
- **Tier B**: Blueprint production (blocked if materials unavailable)
- **Tier C**: Base maintenance (periodic infrastructure drain)

**Relevance to yield_grid**: The delta tracking pattern (`prod - cons` per resource) is exactly what a tile-level yield system would need. The difference is scale: this is colony-level, not tile-level.

### B. `ManufacturingService` — Blueprint-Based Production

**File**: `galaxy_game/app/services/manufacturing_service.rb`

Production is driven by blueprints with explicit input/output:

```ruby
# Input (from blueprint):
required_materials = blueprint_data['required_materials']  # { material_name => { amount: X } }

# Output (from blueprint):
output_type: blueprint_name,
completes_at: Time.current + production_time_hours.hours
```

**Relevance**: Blueprint `production_data` has `time_hours` and `manufacturing_time_hours`. A tile yield could similarly derive from the structures/units on that tile.

### C. `AIManager::ProductionManager` — Resource Management

**File**: `galaxy_game/app/services/ai_manager/production_manager.rb`

Tracks required vs available materials per construction plan:

```ruby
def manage_resources_for_construction(construction_plan)
  # Gathers required materials → checks inventory → prioritizes → acquires
end
```

**Relevance**: Shows how production needs are calculated from plans/blueprints. A yield system would invert this: calculate what a tile produces based on its contents.

### D. `Craft::BaseCraft#recalculate_stats` — Mining Rate Calculation

**File**: `galaxy_game/app/models/craft/base_craft.rb` (lines 373-388)

```ruby
def recalculate_stats
  base_mining_rate = operational_data.dig('operational_properties', 'base_mining_rate_gcc_per_hour') || 0
  
  # Sum mining boosts from computers
  computer_units = base_units.select { |u| u.unit_type.include?('computer') }
  computer_boost = computer_units.sum { |u| u.operational_data.dig('operational_properties', 'mining_boost_gcc_per_hour').to_f }
  
  gpu_rigs = rigs.select { |r| r.rig_type == 'gpu_coprocessor_rig' }
  gpu_boost = gpu_rigs.sum { |r| r.operational_data.dig('operational_properties', 'processing_boost_gcc_per_hour').to_f }
  
  total_mining_rate = base_mining_rate + computer_boost + rigged_computer_boost
  
  operational_data['operational_properties']['current_mining_rate_gcc_per_hour'] = total_mining_rate.round(2)
end
```

**Relevance**: This is the closest thing to a "per-entity yield" in the codebase. It:
1. Starts with a base rate from `operational_data`
2. Adds modifiers from attached units/modules
3. Stores the computed total back into `operational_data`
4. Uses `recalculate_stats` as the computation method name

**This pattern is directly reusable for tile yields**: base yield + structure/unit modifiers = final yield.

### E. `BaseStructure#input_resources` / `output_resources`

**File**: `galaxy_game/app/models/structures/base_structure.rb`

```ruby
def input_resources
  operational_data&.dig('resource_management', 'consumables') || {}
end

def output_resources
  operational_data&.dig('resource_management', 'generated') || {}
end
```

**Relevance**: Structures already track what they consume and produce. A tile yield grid could aggregate these across all structures on a tile.

### F. `BaseUnit#input_resources` / `output_resources`

**File**: `galaxy_game/app/models/units/base_unit.rb`

```ruby
def input_resources
  @unit_info['input_resources']
end

def output_resources
  @unit_info['output_resources']
end
```

**Relevance**: Same pattern as structures — units have resource I/O defined in their `@unit_info` JSON.

---

## 2. Design Docs with Yield-Related Concepts

### A. `docs/design/ECLSS_SYSTEM_ARCHITECTURE.md`

Contains **game parameters with explicit yield values**:

| Constant | Value | Meaning |
|---|---|---|
| `SABATIER_METHANE_YIELD_RATIO` | `0.25` | CH4 generated per unit CO2 processed |
| `HYDROPONIC_O2_YIELD_KG_DAY` | `0.8` | O2 produced per active farm tray daily |

**ECLSS loops** (6 functional life support loops):
1. Water Loop
2. Atmosphere Loop
3. Thermal Management Loop
4. **Food & Biomass Loop** (Agriculture) — "Hydroponic units consume purified water, fertilizer, and CO2 to generate rations and supplementary O2 production"
5. Waste Processing Loop
6. Power Distribution Loop

**Relevance**: The ECLSS architecture already defines per-module production rates. A tile yield grid could aggregate ECLSS outputs from all structures on a tile.

### B. `docs/design/ECLSS_PARAMETERS.md`

Contains **per-person consumption rates**:

| Parameter | Value | Source |
|---|---|---|
| Standard Crew Water Intake | 3.5 kg/person/day | — |
| ILENITE_O2_YIELD_PERCENT | 0.10 | USGS Lunar Geology Data |

**Relevance**: Per-person consumption rates are the inverse of per-tile yields. A tile with a settlement of population N would have food production needs = N × FOOD_PER_PERSON (already defined in GameConstants).

### C. `docs/design/ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md`

Resource flow table:
| Resource | Surface Source | Orbital Source | Settlement Use |
|---|---|---|---|
| Water (H2O) | Polar crater/glacier mining | Atmospheric Skimmer imports | ECLSS intake, Agriculture, Radiation Shielding |

**Relevance**: Confirms that resource flow (mining → processing → consumption) is a core game concept. Tile yields would represent the "surface source" side of this equation.

### D. `docs/architecture/economy/ISRU_PRICING_MODEL.md`

Describes **local production vs import cost** pricing:
- Market price = 95% of current import cost (EAP)
- Local production is always competitive with imports
- Titan N2: production_cost ≈ 0 (98.4% atmosphere harvest)

**Relevance**: Confirms that per-tile yield values would feed into the economic engine. A tile with a mine produces resources at ISRU cost; a tile without produces nothing.

---

## 3. Design Options for yield_grid

### Option A: Aggregate from Existing Structure/Unit I/O (Recommended)

**Approach**: Compute yields by aggregating `input_resources`/`output_resources` from all structures and units on each tile.

```ruby
# Pseudocode for terrain_data_builder.rb
def extract_yield_grid
  return nil unless has_structures_on_celestial_body?
  
  width, height = extract_width(terrain_map_data), extract_height(terrain_map_data)
  grid = Array.new(height) { Array.new(width, nil) }
  
  Structures::BaseStructure.where(celestial_body: @celestial_body).each do |structure|
    pos = get_grid_position(structure)
    next unless pos
    
    x, y = pos
    inputs = structure.input_resources   # { resource => rate }
    outputs = structure.output_resources # { resource => rate }
    
    grid[y][x] = {
      food: (outputs['food'] || 0),
      production: (outputs['production'] || 0),
      science: (outputs['science'] || 0),
      culture: (outputs['culture'] || 0)
    }
  end
  
  grid
end
```

**Pros**: 
- Uses existing data (no new models needed)
- Yields change dynamically as structures are added/removed
- Follows the `BaseStructure#output_resources` pattern already in use

**Cons**:
- No food/production/science/culture keys exist in any structure's output_resources yet — they'd need to be designed
- Mining rate exists on crafts (not structures) — would need to extend to units too

### Option B: GameConstants-Based Default Yields

**Approach**: Define default yields per biome/resource type in GameConstants, overrideable by structures.

```ruby
# In GameConstants:
TILE_YIELD_DEFAULTS = {
  'jungle' => { food: 3, production: 0, science: 0, culture: 0 },
  'desert' => { food: 0, production: 1, science: 0, culture: 0 },
  'regolith' => { food: 0, production: 2, science: 0, culture: 0 },
  'ice_cap' => { food: 0, production: 0, science: 1, culture: 0 }
}.freeze

RESOURCE_YIELD_MODIFIERS = {
  'water_deposit' => { food: 1 },
  'mineral_deposit' => { production: 2 },
  'rare_element_deposit' => { science: 2 }
}.freeze
```

**Pros**:
- Simple, deterministic, easy to balance
- No dependency on structure data (works for unimproved tiles)
- Follows the existing GameConstants pattern used throughout the codebase

**Cons**:
- Static — doesn't reflect actual structures on the tile
- Requires designing food/production/science/culture as game-balance concepts from scratch

### Option C: Hybrid (Default Yields + Structure Bonuses)

**Approach**: Start with biome-based defaults, add structure/unit bonuses.

```ruby
def extract_yield_grid
  width, height = extract_width(terrain_map_data), extract_height(terrain_map_data)
  biomes = extract_biomes(terrain_map_data)
  
  grid = Array.new(height) { Array.new(width, nil) }
  
  (0...height).each do |y|
    (0...width).each do |x|
      biome = biomes&.dig(y, x) || 'regolith'
      base_yield = GameConstants::TILE_YIELD_DEFAULTS[biome] || {}
      
      # Add structure/unit bonuses for this tile
      bonus = yield_bonus_for_tile(x, y)
      grid[y][x] = base_yield.merge(bonus)
    end
  end
  
  grid
end

def yield_bonus_for_tile(col, row)
  structures_on_tile(col, row).sum do |s|
    s.output_resources || {}
  end + units_on_tile(col, row).sum do |u|
    u.output_resources || {}
  end
end
```

**Pros**:
- Works for both improved and unimproved tiles
- Extensible — new structure types automatically contribute yields
- Follows the `recalculate_stats` pattern from Craft::BaseCraft

**Cons**:
- Most complex to implement
- Requires designing what food/production/science/culture mean in each context

---

## 4. Recommended Approach

**Option C (Hybrid)** is recommended because:

1. **Civ4-style games always have base terrain yields** — a desert tile produces something even without improvements
2. **Structures should modify yields** — a farm structure on a desert tile should increase food production
3. **The codebase already has the building blocks**: GameConstants for defaults, `output_resources` for structure bonuses, `recalculate_stats` pattern for computation

**Next steps before implementation**:
1. Design the food/production/science/culture game-balance concepts (what do they mean? how are they used?)
2. Define default yields per biome in GameConstants
3. Extend `output_resources` on structures to include these yield keys
4. Implement `extract_yield_grid` in TerrainDataBuilder using the hybrid approach

---

## 5. Files Referenced

| File | Relevance |
|---|---|
| `galaxy_game/app/services/luna_operations_simulation_service.rb` | Per-resource delta tracking pattern |
| `galaxy_game/app/services/manufacturing_service.rb` | Blueprint-based production model |
| `galaxy_game/app/services/ai_manager/production_manager.rb` | Resource management from plans |
| `galaxy_game/app/models/craft/base_craft.rb` (lines 373-388) | `recalculate_stats` mining rate pattern |
| `galaxy_game/app/models/structures/base_structure.rb` | `input_resources`/`output_resources` methods |
| `galaxy_game/app/models/units/base_unit.rb` | `input_resources`/`output_resources` methods |
| `docs/design/ECLSS_SYSTEM_ARCHITECTURE.md` | Per-module yield constants (O2, CH4) |
| `docs/design/ECLSS_PARAMETERS.md` | Per-person consumption rates |
| `docs/design/ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md` | Resource flow table |
| `docs/architecture/economy/ISRU_PRICING_MODEL.md` | Local production vs import pricing |
