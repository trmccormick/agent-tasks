# Task 1: MockDataHelper Enhancement (Blueprints, Items, Post-Snap Wormhole Network)

**Priority**: HIGH  
**Complexity**: Medium  
**Estimated Time**: 4-6 hours  
**Status**: Ready for Implementation  
**Date Created**: 2026-10-22  
**Revised**: 2026-10-22 — Post-Snap architecture (Sol + natural WH to procedural System B + artificial WH to Eden)

---

## Overview

Enhance MockDataHelper to provide:
1. Blueprint catalog data (with materials lookup)
2. Item catalog data (with material types and properties)
3. Post-Snap wormhole network (Sol → System B natural WH, Sol → Eden artificial WH)
4. Exotic Matter (EM) tracking and recapture mechanics
5. Asset mapping for images (crafts, materials, biomes, logos)

**Context**: Players enter post-Snap, inheriting a living Sol system with:
- **Natural wormhole to System B** (procedurally generated system with EM harvesting risk)
- **Artificial wormhole to Eden** (post-Snap construction using AWS at L3 Lagrange point for stable reconnection)

This foundation supports all subsequent UI tasks with realistic wormhole physics, EM dynamics, and post-crisis infrastructure.

---

## Current State

- MockDataHelper exists with 6 methods: system_status, bay_inventory, player_ships, npc_buy_orders, active_missions, market_quick_panel
- References undefined data in DashboardController
- No blueprint, item, or wormhole network data available

---

## Requirements

### 1. Wormhole Network (Post-Snap: 2 Connections)

**Architecture** (players inherit this state):
- **Sol**: Player home system (Milky Way, fully known data from JSON)
  - Identifier: "SOL"
  - System Mass: 500,000 tons (Jupiter anchor stabilization)
  - Anchored Artificial Wormhole Station (AWS) at L3 Lagrange point
- **System B**: Procedurally generated remote system (unknown type, discovered via natural WH)
  - Identifier: Random (e.g., "GJ-1061-B" or similar procedural name)
  - System Mass: ~250,000 tons (varies, procedurally set)
  - Natural Wormhole connection to Sol (primary EM source, unstable)
  - Traits: EM-rich environment, variable stability
- **Eden**: Initially isolated post-Snap, now reconnected via AWS
  - Identifier: "AOL-732356"
  - System Mass: 300,000 tons
  - Artificial Wormhole connection to Sol (stable, AWS-powered)
  - Connection Status: Reconstructed post-Snap (players know this as backstory)

**Wormhole Types**:
1. **Natural Wormhole (Sol ↔ System B)**
   - Unstable but free EM harvesting
   - Recapture satellites collecting EM from transiting mass
   - Risk: Snap event if mass exceeded (Snap Protocol possible via Hammer trigger)
   - Stability decay: Degrades over time without EM reinforcement

2. **Artificial Wormhole (Sol ↔ Eden)**
   - AWS provides dual-link stability (mass limit 2x multiplier)
   - Requires EM fuel to maintain (half the cost of natural WH infrastructure)
   - Built post-Snap for reconnaissance/reconnection
   - Safe for regular transit (no snap risk unless AWS damaged)

**Wormhole Physics Data Structure**:
```ruby
{
  id: 1,
  name: "Sol-System B Natural Link",
  system_a_id: 1,           # Sol
  system_b_id: 2,           # System B (procedural)
  system_a_mass: 500000,    # tons (Jupiter anchor baseline)
  system_b_mass: 250000,    # tons (procedurally varies)
  
  # Wormhole Type & Stability
  natural: true,            # Natural WH (not AWS-powered)
  wormhole_type: "traversable",
  
  # Core Physics
  stability_index: 0.78,    # 0.1-1.0 scale (decays naturally)
  stability_decay_per_day: 0.02,  # 2% daily decay
  em_potential: "high",     # Natural WH EM richness (high/medium/low)
  
  # EM Mechanics
  em_reserves: 8500,        # Current EM stored (recharge rate 150/day)
  em_harvest_rate: 150,     # EM per 1000 tons transited (recapture satellites)
  em_maintenance_cost: 250, # Daily cost to prevent collapse
  
  # Mass Limit Calculation
  system_mass: 500000,      # Using system A (Sol)
  em_surplus: 8500,         # Cached from em_reserves
  mass_limit: 37,           # (500000 * 0.78) / 8500 = ~37 tons safe per crossing
  
  # Transit History & Risk
  cumulative_transit_mass_a_to_b: 2500,  # Historical mass Sol→System B
  cumulative_transit_mass_b_to_a: 1800,  # Historical mass System B→Sol
  snap_risk_level: "caution",  # safe/caution/danger based on mass-limit ratio
  
  # Infrastructure
  recapture_satellites: 3,   # Active EM collection satellites
  stabilizer_count: 0,       # No active AWS (natural WH)
  counterbalance_quality: 0.0,  # No AWS dual-link
  
  # Traversal
  one_way: false,           # Bidirectional
  traversed: false,         # Player hasn't used yet
  
  # Hammer Protocol (admin interface)
  snap_eligible: true,      # Can trigger intentional Snap via Hammer Protocol
  snap_coordinates: [12.34, 56.78, 90.12],  # EM field residual if snapped
}

{
  id: 2,
  name: "Sol-Eden Artificial Link",
  system_a_id: 1,           # Sol
  system_b_id: 3,           # Eden
  system_a_mass: 500000,
  system_b_mass: 300000,
  
  natural: false,           # Artificial (AWS-powered)
  wormhole_type: "traversable",
  
  stability_index: 0.95,    # High stability (AWS maintained)
  em_potential: "none",     # Artificial link (no natural EM source)
  
  em_reserves: 15000,       # Larger buffer (AWS operational reserve)
  em_maintenance_cost: 400, # Higher cost (AWS upkeep)
  em_harvest_rate: 0,       # No natural harvesting (artificial only)
  
  # Mass Limit doubled by AWS
  mass_limit: 57,           # (500000 * 0.95) / 15000 * 2.0 (AWS multiplier) ≈ 57 tons
  
  cumulative_transit_mass_a_to_b: 1200,
  cumulative_transit_mass_b_to_a: 800,
  snap_risk_level: "safe",  # AWS provides inherent safety
  
  # AWS Infrastructure
  aws_operational: true,    # Artificial Wormhole Station active
  aws_location: "L3 Lagrange Point",
  recapture_satellites: 0,  # AWS doesn't use recapture (artificial only)
  stabilizer_count: 2,      # AWS dual-link stabilizers
  counterbalance_quality: 0.95,  # Excellent anchor point
  
  one_way: false,
  traversed: true,          # Players expect this is already accessible
  
  snap_eligible: false      # AWS links cannot Snap (intentional design feature)
}
```

**Method Signature**:
```ruby
def self.wormhole_network
  # Returns: 
  # {
  #   systems: [sol_hash, system_b_hash, eden_hash],
  #   wormholes: [sol_system_b_natural, sol_eden_aws],
  #   current_location: "SOL",
  #   current_timestamp: Time.now,
  #   post_snap_status: {
  #     snap_event_date: "2026-10-01T14:32:00Z",
  #     original_system_b_coordinates: [45.2, 123.8, 67.4],
  #     eden_orphaned_duration_hours: 18,
  #     aws_reconstruction_date: "2026-10-02T09:15:00Z"
  #   },
  #   ai_manager_status: { // See Task 7
  #     snap_awareness: true,
  #     next_action: "rebuild Eden infrastructure"
  #   }
  # }
end

def self.em_status
  # Returns global EM economy state
  # {
  #   total_em_reserves: 23500,  # Sol + System B + Eden combined
  #   daily_harvest_rate: 150,   # From System B natural WH
  #   daily_consumption: 650,    # Maintenance + travel + AWS upkeep
  #   net_daily_flow: -500,      # Deficit (sustainable if monitored)
  #   days_until_critical: 47,   # Before collapse
  #   recapture_satellites_active: 3,  # Harvesting infrastructure
  #   aws_efficiency: 0.95        # AWS operational health
  # }
end

def self.hammer_protocol_status
  # Returns snap mechanic state (admin only in UI)
  # {
  #   eligible_wormholes: ["sol-system-b-natural"],
  #   system_b_natural_wh: {
  #     current_mass_limit: 37,
  #     current_transit_mass: 12,
  #     snap_trigger_threshold: 35,  # 94% of limit
  #     snap_ready: false,
  #     projected_new_location: [78.3, 45.6, 12.1],
  #     orphan_risk_systems: ["eden"],  # Systems that would disconnect
  #     estimated_em_field_value: 2500
  #   }
  # }
end
```

### 2. Blueprint Catalog

**Source**: Use actual Galaxy Game blueprint JSON via BlueprintLookupService lookup

**Data Structure**:
```ruby
{
  id: 1,
  name: "Spaceship Blueprint",
  description: "Construct a basic spaceship",
  materials: [
    { item_name: "Steel Ingot", amount: 150 },
    { item_name: "Aluminum Ingot", amount: 75 },
    { item_name: "Copper Wire", amount: 50 }
  ],
  research_level: 2,
  time_efficiency: 0.95,
  material_efficiency: 0.92,
  licensed_runs_remaining: 5,
  production_time_hours: 48,
  output_quantity: 1,
  technology_level: "basic"
}
```

**Method Signature**:
```ruby
def self.blueprints(limit: 10)
  # Returns array of 10 blueprint hashes
  # Used for: Blueprints catalog, production queue preview
end

def self.blueprint_by_name(name)
  # Returns single blueprint hash by name
  # Used for: Detailed blueprint view, material requirements
end
```

### 3. Item Catalog

**Source**: Use actual Galaxy Game items via ItemLookupService lookup

**Data Structure**:
```ruby
{
  id: 1,
  name: "Steel Ingot",
  description: "Raw refined steel, suitable for construction",
  material_type: "processed_material",  # or: manufactured_goods, metal, component, gas, consumable, liquid, fuel, raw_material
  amount: 150,  # in inventory
  rarity: "common",
  source: "Mining/Extraction",
  is_container: false,
  durability: 100,
  unit_weight: 2.5,
  tags: ["metal", "construction", "refinable"],
  image_path: "materials/processed/steel_ingot.png"  # relative to /public/images
}
```

**Material Types** (9 categories):
- manufactured_goods
- metal
- component
- processed_material
- gas
- consumable
- liquid
- fuel
- raw_material

**Method Signature**:
```ruby
def self.items(limit: 20, filter_by_type: nil)
  # Returns array of item hashes
  # filter_by_type: "metal", "gas", "processed_material", etc.
  # Used for: Items catalog, inventory display
end

def self.item_by_name(name)
  # Returns single item hash by name
  # Used for: Item details, blueprint requirements lookup
end
```

### 4. Asset Mapping

**Directory Structure** (relative to rails public path):
```
/data/images/
├── catalog/
│   ├── crafts/
│   │   └── space/
│   │       ├── landers/
│   │       ├── probes/
│   │       ├── satellites/
│   │       └── spacecraft/
│   ├── materials/
│   │   └── processed/
│   │       └── polymers/
│   └── structures/
├── logos/  (20+ organization logos)
├── icons/  (tech icons)
└── biomes/ (13 biome thumbnails)
```

**Asset Mapping in MockData**:
```ruby
def self.asset_map
  {
    # Craft assets (10 available)
    "Spaceship" => "/data/images/catalog/crafts/space/spacecraft/spaceship.png",
    "Lander" => "/data/images/catalog/crafts/space/landers/lander.png",
    # ... more crafts
    
    # Logo assets (20+ available)
    "Wormhole Transit Consortium" => "/data/images/logos/Wormhole Transit Consortium.png",
    "AstroLift" => "/data/images/logos/AstroLift.png",
    # ... more orgs
    
    # Biome assets (13 available)
    "Forest" => "/data/images/biomes/forest.png",
    "Desert" => "/data/images/biomes/hot_desert.png",
    # ... more biomes
    
    # Missing assets (flagged for generation)
    "Steel Ingot" => nil,  # Will use placeholder, marked as "needed"
    "Reactor" => nil
  }
end
```

---

## Implementation Details

### File to Modify

**`galaxy-game-ui/app/services/mock_data_helper.rb`**

Add methods:
1. `self.wormhole_network` — Returns 3-system network with pathfinding data
2. `self.blueprints(limit, filter)` — Returns blueprint catalog
3. `self.blueprint_by_name(name)` — Returns single blueprint
4. `self.items(limit, filter_by_type)` — Returns item catalog
5. `self.item_by_name(name)` — Returns single item
6. `self.asset_map` — Returns craft/logo/biome/material image paths
7. `self.missing_assets` — Returns list of images needed for generation

### Data Sources

**Real Galaxy Game Lookup Services** (preferred):
```ruby
# For blueprints:
Lookup::BlueprintLookupService.new.find_blueprint(name)

# For items:
Lookup::ItemLookupService.new.find_item(name)

# For crafts:
Lookup::CraftLookupService.new.find_craft(name)
```

**Fallback** (if lookup services unavailable):
Use Faker + hardcoded base data for prototyping.

### Test Systems (JSON Data Exists)

**Sol System** (home, post-Snap):
```json
{
  "galaxy": {"name": "Milky Way", "identifier": "MILKYWAY-01"},
  "solar_system": {"name": "Sol", "identifier": "SOL-01"},
  "id": "Sol",
  "identifier": "SOL",
  "system_mass": 500000,
  "stars": [{"name": "Sol", "spectral_class": "G2V", "temperature": 5778}],
  "celestial_bodies": {...8 planets with full data...}
}
```

**System B** (procedurally generated, accessible via natural WH):
- Generate procedurally at runtime OR use a random Local Bubble system
- Example candidates: GJ-1002, Ross-128, Barnards-Star (use JSON data from `/data/json-data/star_systems/`)
- Characteristics: ~250K tons system mass, rich EM environment, partial celestial body data
- Marks as "Tier 2 (Hybrid)" in metadata (partial survey data from probe deployment)

**Eden System** (post-Snap reconnection):
```json
{
  "galaxy": {"name": "Milky Way", "identifier": "MILKYWAY-01"},
  "solar_system": {"name": "Eden", "identifier": "AOL-732356"},
  "id": "Eden",
  "identifier": "AOL-732356",
  "system_mass": 300000,
  "post_snap_status": {
    "was_orphaned": true,
    "orphan_duration_hours": 18,
    "reconnected_via": "artificial_wormhole_station",
    "aws_location": "L3 Lagrange Point"
  },
  "stars": [...],
  "celestial_bodies": [...]
}
```

---

## Acceptance Criteria

- ✅ `wormhole_network` returns post-Snap network: Sol ↔ System B (natural) + Sol ↔ Eden (artificial AWS)
- ✅ Natural wormhole (Sol-System B) has EM harvesting data (em_harvest_rate, recapture_satellites, stability_decay)
- ✅ Artificial wormhole (Sol-Eden) has AWS infrastructure (aws_operational, stabilizer_count, counterbalance_quality)
- ✅ Mass limits calculated correctly: (System_Mass × Stability_Index) / EM_Surplus
- ✅ Snap risk assessment returned for each wormhole (safe/caution/danger based on transit mass vs mass limit)
- ✅ `em_status` returns global EM economy state (reserves, harvest rate, consumption, days until critical)
- ✅ `hammer_protocol_status` returns snap trigger eligibility (admin interface data)
- ✅ Post-Snap context included (snap event date, Eden orphan duration, AWS reconstruction date)
- ✅ `blueprints` returns array of 10+ realistic blueprints with materials
- ✅ Blueprint materials reference actual items from catalog
- ✅ `items` returns array of 20+ items with 9 material types
- ✅ All items have descriptions and source information
- ✅ `asset_map` returns paths for all available images (crafts, logos, biomes)
- ✅ Missing assets flagged (for generation via ASSET_PROMPTS guide)
- ✅ System B procedurally generated or randomly selected from Local Bubble
- ✅ No errors when called from DashboardController / WormholesController / AdminController
- ✅ All methods return data in consistent hash format

---

## Code Example (Partial Implementation)

```ruby
module MockDataHelper
  class << self
    # ... existing methods ...

    def self.wormhole_network
      # Post-Snap network state: Sol is hub with 2 wormholes
      
      sol = {
        identifier: "SOL",
        name: "Sol System",
        system_mass: 500000,     # Jupiter anchor baseline
        stability_index_base: 0.95,
        galaxy: "Milky Way",
        known_data_level: "complete"  # Full JSON survey data
      }
      
      # System B: Procedurally generated or randomly selected Local Bubble system
      system_b = {
        identifier: "GJ-1061",    # or randomly selected from Local Bubble catalog
        name: "GJ 1061 System",   # Dynamic name based on procedural selection
        system_mass: 250000,      # Procedurally varies (200K-300K range)
        stability_index_base: 0.65,  # Natural WH less stable
        galaxy: "Milky Way",
        known_data_level: "partial"  # Tier 2 (Hybrid) - probe survey data only
      }
      
      eden = {
        identifier: "AOL-732356",
        name: "Eden",
        system_mass: 300000,
        stability_index_base: 0.88,
        galaxy: "Milky Way",
        known_data_level: "partial",
        post_snap: {
          was_orphaned: true,
          orphan_duration_hours: 18,
          reconnected_via: "artificial_wormhole_station"
        }
      }
      
      # Natural Wormhole: Sol ↔ System B
      wormhole_natural = {
        id: 1,
        name: "Sol-GJ 1061 Natural Link",
        system_a: sol[:identifier],
        system_b: system_b[:identifier],
        natural: true,
        wormhole_type: "traversable",
        
        # Physics
        stability_index: 0.78,
        stability_decay_per_day: 0.02,
        em_potential: "high",      # Natural WH is rich EM source
        
        # EM Economy
        em_reserves: 8500,
        em_harvest_rate: 150,      # EM per 1000 tons transited
        em_maintenance_cost: 250,  # Daily to prevent collapse
        
        # Mass Limit
        system_mass: sol[:system_mass],
        em_surplus: 8500,
        mass_limit: (sol[:system_mass] * 0.78 / 8500).round(1),  # ~37 tons
        
        # Transit History
        cumulative_transit_mass_a_to_b: 2500,
        cumulative_transit_mass_b_to_a: 1800,
        snap_risk_level: "caution",
        
        # Infrastructure
        recapture_satellites: 3,
        stabilizer_count: 0,       # No AWS
        counterbalance_quality: 0.0,
        
        traversal: { one_way: false, traversed: false },
        
        # Hammer Protocol
        snap_eligible: true,
        snap_coordinates: [12.34, 56.78, 90.12]  # Possible new exit location
      }
      
      # Artificial Wormhole: Sol ↔ Eden (AWS-powered, post-Snap)
      wormhole_artificial = {
        id: 2,
        name: "Sol-Eden AWS Link",
        system_a: sol[:identifier],
        system_b: eden[:identifier],
        natural: false,
        wormhole_type: "traversable",
        
        # Physics (AWS stabilized)
        stability_index: 0.95,
        em_potential: "none",      # Artificial (no natural source)
        
        # EM Economy (AWS operational)
        em_reserves: 15000,
        em_maintenance_cost: 400,  # Higher AWS upkeep
        em_harvest_rate: 0,        # Artificial only
        
        # Mass Limit (AWS doubles effective mass limit)
        system_mass: sol[:system_mass],
        em_surplus: 15000,
        mass_limit: ((sol[:system_mass] * 0.95 / 15000) * 2.0).round(1),  # ~63 tons (doubled by AWS)
        
        # Transit History
        cumulative_transit_mass_a_to_b: 1200,
        cumulative_transit_mass_b_to_a: 800,
        snap_risk_level: "safe",
        
        # AWS Infrastructure
        aws_operational: true,
        aws_location: "L3 Lagrange Point",
        recapture_satellites: 0,   # AWS doesn't recapture
        stabilizer_count: 2,       # Dual-link stabilizers
        counterbalance_quality: 0.95,
        
        traversal: { one_way: false, traversed: true },  # Already established
        
        # Hammer Protocol
        snap_eligible: false       # AWS links immune to Snap
      }
      
      {
        systems: [sol, system_b, eden],
        wormholes: [wormhole_natural, wormhole_artificial],
        current_location: "SOL",
        current_timestamp: Time.now,
        
        # Post-Snap context (players inherit this history)
        post_snap_status: {
          snap_event_date: "2026-10-01T14:32:00Z",
          snap_trigger: "Eden infrastructure exceeded natural WH mass limits",
          original_system_b_exit_location: [45.2, 123.8, 67.4],
          eden_orphaned_duration_hours: 18,
          aws_reconstruction_date: "2026-10-02T09:15:00Z",
          ai_manager_decision: "Build AWS to prevent future orphaning"
        },
        
        # Pathfinding
        can_reach: {
          "SOL" => ["GJ-1061", "AOL-732356"],
          "GJ-1061" => ["SOL"],  # One-way wormhole to Sol only
          "AOL-732356" => ["SOL"]
        }
      }
    end

    def self.em_status
      {
        total_em_reserves: 23500,
        daily_harvest_rate: 150,      # From natural WH recapture
        daily_consumption: 650,       # All systems
        net_daily_flow: -500,         # Deficit (needs monitoring)
        days_until_critical: 47,
        recapture_satellites_active: 3,
        aws_efficiency: 0.95,
        status: "operational_deficit"
      }
    end

    def self.hammer_protocol_status
      # Admin-only interface for snap mechanics
      {
        eligible_wormholes: ["sol-system-b-natural"],
        system_b_natural_wh: {
          current_mass_limit: 37,
          current_transit_mass: 12,
          snap_trigger_threshold: 35,   # 94% of limit triggers danger
          snap_ready: false,
          snap_projected_coordinates: [78.3, 45.6, 12.1],
          orphan_risk_systems: ["eden"],  # Would disconnect this system
          estimated_em_field_value: 2500
        }
      }
    end

    def self.blueprints(limit: 10)
      [
        {
          name: "Spaceship Blueprint",
          research_level: 2,
          materials: [
            { item: "Steel Ingot", amount: 150 },
            { item: "Aluminum Ingot", amount: 75 }
          ],
          time_efficiency: 0.95,
          material_efficiency: 0.92,
          production_time_hours: 48
        },
        # ... more blueprints
      ].first(limit)
    end

    def self.items(limit: 20, filter_by_type: nil)
      all_items = [
        {
          name: "Steel Ingot",
          description: "Refined steel for construction",
          material_type: "processed_material",
          amount: 150
        },
        # ... more items
      ]
      
      all_items = all_items.select { |i| i[:material_type] == filter_by_type } if filter_by_type
      all_items.first(limit)
    end

    def self.asset_map
      {
        "Spaceship" => "/data/images/catalog/crafts/space/spacecraft/spaceship.png",
        "Wormhole Transit Consortium" => "/data/images/logos/Wormhole Transit Consortium.png"
        # ... more assets
      }
    end
  end
end
```
        "Forest Biome" => "/data/images/biomes/forest.png"
      }
    end
  end
end
```

---

## References

- **Lookup Services**: `/galaxy_game/app/services/lookup/` (ItemLookupService, CraftLookupService, BlueprintLookupService)
- **Asset Generation Guide**: `/data/images/ASSET_PROMPTS.md`
- **Test Systems**: `/data/json-data/star_systems/` (sol, AOL-732356, AC-01)
- **Wormhole Physics**: `docs/storyline/05_physics_topology.md` (Mass Limit Formula)

---

## Next Steps

After completion:
- Task 2: Landing Page will use `system_status()` and `wormhole_network()` for system cards
- Task 3: Dashboard will use all 6 methods for panel data
- Tasks 4-5: Blueprints/Items UIs will use lookup services + asset_map
- Task 6: Wormhole Navigator will use full wormhole_network data

---

**Status**: ✅ Ready for implementation  
**Assigned To**: Implementation Agent  
**Review Gate**: Verify all 7 methods return expected data shapes, no missing keys
