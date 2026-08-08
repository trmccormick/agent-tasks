# Orbital Gas Processing Pipeline — L1 Station Infrastructure

```yaml
id: 2026-06-08-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE
status: backlog
priority: HIGH
type: FEATURE
created: 2026-06-08
last_updated: 2026-06-08
system_domain: ISRU | ORBITAL_INFRASTRUCTURE | SUPPLY_CHAIN
mvp_alignment: PHASE_5_AUTONOMOUS_EXPANSION
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/2026-04-10-HIGH-ARCHITECTURE-ORBITAL-MARKET-SYSTEM.md` (gas processing gap identified during triage)

The supply chain architecture design intent confirms that gas harvesting and processing belongs at **orbital stations** (L1, Venus orbital depots), NOT on Luna surface. This is because:
- Gas liquefaction/compression requires continuous energy input
- Orbital stations have uninterrupted solar power access  
- Luna's 2-week night cycle makes energy-intensive processing non-viable

This task implements the gas → processed propellant pipeline at L1 Station, enabling skimmer overflow routing and cycler refueling operations. It aligns with Phase 5+ autonomous expansion by establishing the infrastructure needed for multi-system logistics (cyclers need fuel depots).

**Why Phase 5?** Luna Phases 1-3 establish Earth→Luna supply chain within Sol system. Gas processing at L1 becomes critical when:
- Skimmers harvest Venus atmosphere and need overflow routing destinations
- Cyclers require refueling stops on interplanetary routes  
- Multi-system expansion needs orbital depot infrastructure

---

## Problem Statement

**Current State:** 
- `Structures::OrbitalStructure` exists with docking, storage capacity, atmospheric processing capabilities
- Market order system fully implemented (orders, trade execution, GCC settlement)
- AI Manager monitors market orders and participates in economic decisions
- **Gap**: No gas-specific processing pipeline transforms raw harvested gases into processed propellants

**Expected Behavior:**
1. Skimmer craft docks at L1 Station with raw methane/CO2/H2 from Venus atmosphere harvests
2. Processing units consume raw gas inventory + energy input over time
3. Processed outputs (LOX, liquid CH4) enter station inventory and auto-list on market
4. AI Manager manages bid-ask spreads as depot owner
5. Cyclers dock to refuel with processed propellants for interplanetary transit

---

## Supply Chain Architecture Alignment

**Read First:** `docs/new_agent/projects/galaxy_game/status.md` — "Supply Chain Architecture (Design Intent)" section confirms:

```
- Earth launches → L1 Station (joint venture LDC + AstroLift, Zenith builds)
- Luna ISRU → L1 Station (regolith-derived metals, oxygen, structural materials)  
- L1 → Cyclers (mobile space stations on continuous orbital arcs) → Mars/Venus/Belt
- Core cycler route: L1 → Mars (Phobos/Deimos depot) → Venus (orbital processing) → L1
- Outer routes: Mars ↔ Saturn/Titan — flexible, AI Manager optimizes, Vector Hauling operates
- Cyclers + Tugs assembled at L1 — design intent, not yet implemented

Gas processing belongs at orbital stations (continuous solar power) NOT Luna surface
(Luna has 2-week night cycle — energy-intensive liquefaction/compression not viable)

Skimmer overflow routing: L1 primary, secondary orbital depot if L1 full,
Luna surface last resort for solid/regolith materials only
```

**Corporate Ownership:**
- **Zenith Orbital**: Builds and manages stations (station_construction org type)
- **AstroLift + LDC co-own L1 Station**: Combined control of Sol supply chains  
- **Vector Hauling**: Outer Sol bulk cargo, natural operator for Mars/Titan/Belt routes

---

## Current State Analysis

### Existing Infrastructure to Leverage (Read Before Starting)

| File | Purpose | Key Methods/Sections |
|------|---------|---------------------|
| `app/models/structures/orbital_structure.rb` | Orbital station model with docking/storage | `total_storage_capacity`, `needs_atmosphere?`, includes `AtmosphericProcessing` concern |
| `app/services/manufacturing/material_processing_service.rb` | Material transformation logic | Base processing pattern to extend for gases |
| `app/models/market/order.rb` + `marketplace.rb` | Order book system | Buy/sell orders, price-time priority matching |
| `app/services/market/trade_execution_service.rb` | Trade orchestration with GCC settlement | Tax collection, inventory updates, trade records |

### Reference Documentation — Read First
- Original source: `docs/agent/archive/backlog_april_2026/2026-04-10-HIGH-ARCHITECTURE-ORBITAL-MARKET-SYSTEM.md` (architecture design task)  
- Supply chain intent: `docs/new_agent/projects/galaxy_game/status.md#supply-chain-architecture-design-intent-not-yet-implemented`
- Related Phase 5+ tasks in same folder: WormholeNavigator upgrade, Foothold Establishment Service

---

## Implementation Scope

### In Scope (Phase 5+)

**Step 1 — Gas Processing Unit Blueprint & Model**
```ruby
# New blueprint type for orbital processing units
GasProcessingUnit < BaseBlueprint
- Input resources: Raw methane gas, CO2, H2 (from skimmer harvests)
- Energy requirement: Continuous solar power input  
- Output products: Liquid CH4 propellant, LOX (liquid oxygen), processed hydrogen
- Processing time: Tick-based or real-time duration model
```

**Step 2 — OrbitalStructure Gas Pipeline Integration**
```ruby
# Extend Structures::OrbitalStructure with gas-specific methods
def process_gas(input_resource, quantity) 
  # Consume raw inventory + energy over processing_duration
  # Output processed propellant to station inventory
end

def auto_list_processed_propellants_on_market(processed_resources)
  # Create sell orders for newly produced LOX/CH4 at calculated ask prices
end
```

**Step 3 — Skimmer Docking & Overflow Routing Logic**  
```ruby
# When skimmer docks with raw gas harvests:
def self.route_skimmer_overflow(skimmer, harvested_gases)
  # Priority routing: L1 Station → secondary orbital depot → Luna surface (solids only)
  # Check storage capacity before directing craft to dock
  
end

**Step 4 — AI Manager Depot Owner Strategy**  
```ruby
# As Zenith Orbital/LDC co-owner, manage bid-ask spreads
def self.manage_orbital_depot_pricing(settlement, gas_type)
  # Set buy prices for raw inputs (attract skimmer harvests)
  # Set sell prices for processed outputs (cycler refueling demand)
  # Adjust based on inventory levels and market conditions
  
end

### Out of Scope (Future Tasks or Already Implemented)

- **Already implemented**: Market order system, trade execution, GCC settlement ✅  
- **Already implemented**: OrbitalStructure model with docking/storage capabilities ✅  
- **Phase 6+**: TerraGen consortium formation detection for shared resource pools
- **Phase 5 separate task**: Foothold establishment service (multi-system colonization)
- **Not in scope**: Dynamic orbital position simulation (use static SpatialLocation distances)

---

## Technical Requirements

### Gas Processing Pipeline Design Questions to Answer During Implementation

**1. Energy Model Integration:**  
How does `EnergyManagement` concern on OrbitalStructure track continuous power consumption? Does it need extension for processing unit energy requirements over time?

```bash
grep -rn "include.*EnergyManagement" ~/Documents/git/galaxyGame/galaxy_game/app/models/ --include="*.rb" | head -10
cat app/models/concerns/energy_management.rb 2>/dev/null || echo "File not found"
```

**2. Processing Time Model:**  
Should gas processing be:
- **Tick-based**: Completes after N game ticks (deterministic, easier to test)
- **Real-time duration**: Starts at timestamp T, completes at T + processing_duration_seconds
- **Instant with cost penalty**: Immediate but consumes more energy/resources

**Recommendation:** Start with tick-based model for Luna Phase 5 implementation. Real-time can be added later when game loop simulation exists.

**3. Inventory Escrow Pattern:**  
When skimmer docks and sells raw gas:
```ruby
# Does inventory transfer happen immediately on trade execution?
# Or does it go into escrow until processing completes?
cat app/services/market/trade_execution_service.rb | grep -A 10 "remove_inventory"
```

**4. Auto-Listing Strategy:**  
When processed propellants enter station inventory:
```ruby
# Should Marketplace automatically create sell orders at calculated ask prices?
# Or does depot owner (AI Manager) manually place orders based on strategy?
cat app/models/market/marketplace.rb | grep -A 5 "def place_order"
```

---

## Key Files to Read Before Implementation

### Core Models & Services
- `app/models/structures/orbital_structure.rb` — existing capabilities, includes AtmosphericProcessing concern  
- `app/services/manufacturing/material_processing_service.rb` — processing pattern reference  
- `app/models/market/order.rb` + `marketplace.rb` — order book integration points  
- `app/services/market/trade_execution_service.rb` — inventory update patterns

### Energy & Storage
- `app/models/concerns/energy_management.rb` (if exists) or check OrbitalStructure includes  
- Check if storage capacity tracking uses BaseUnit operational_data pattern from OrbitalStructure#total_storage_capacity

### AI Manager Integration Points
- `app/services/ai_manager/resource_acquisition_service.rb` — how it currently handles market orders  
- `app/services/ai_manager/isru_optimizer.rb` — production planning patterns to extend for orbital processing

---

## Implementation Steps (Detailed)

### Phase 1: Gas Processing Unit Blueprint & Model Creation
**Files:** New blueprint schema + model extension

```ruby
# app/models/blueprints/gas_processing_unit.rb (or extend existing BaseBlueprint)
module Blueprints
  class GasProcessingUnit < BaseBlueprint
    
    # Input/output definitions for gas processing recipes
    RECIPES = {
      methane_liquefaction: {
        inputs: { 'raw_methane_gas' => 10, 'energy_units' => 5 },
        outputs: { 'liquid_ch4_propellant' => 9.5 }, # 5% loss in processing
        processing_ticks: 24 # ~1 game day at standard tick rate
      },
      oxygen_production_from_co2: {
        inputs: { 'raw_co2_gas' => 10, 'energy_units' => 8 },
        outputs: { 'lox_propellant' => 6.5, 'waste_carbon' => 3.5 },
        processing_ticks: 36 # ~1.5 game days
      }
    }.freeze
    
    def process_gas(recipe_name:, quantity:)
      # Validate sufficient raw inventory + energy availability
      # Start processing job (tick-based or duration-based)
      # Return processing_job_id for tracking completion
    end
    
    def on_processing_complete(job_id, recipe_name)
      # Remove consumed inputs from station inventory  
      # Add processed outputs to station inventory
      # Trigger auto-listing of propellants on market if configured
    end
  end
end
```

### Phase 2: OrbitalStructure Integration
**Files:** Extend `app/models/structures/orbital_structure.rb`

```ruby
# Add gas processing capabilities to orbital stations
module Structures
  class OrbitalStructure < BaseStructure
    
    # Gas-specific storage tracking (separate from general cargo capacity)
    def raw_gas_storage_capacity(gas_type = nil)
      base_units.where(unit_type: 'gas_processing_unit').sum do |unit|
        unit.operational_data&.dig('storage', gas_type || 'capacity')&.to_f || 0
      end
    end
    
    def current_raw_gas_inventory(gas_type)
      # Query inventory for raw harvested gases awaiting processing
    end
    
    def process_gases(recipe_name:, quantity:)
      # Delegate to GasProcessingUnit blueprint logic
      # Track energy consumption over processing duration
      # Update station inventory on completion
    end
    
    # Skimmer overflow routing helper (called when skimmer docks)
    def can_accept_skimmer_overflow?(harvested_resources, quantities)
      total_storage_capacity >= harvested_resources.sum { |res| quantities[res] }
    end
  end
end
```

### Phase 3: Market Integration & Auto-Listing  
**Files:** Extend `app/models/market/marketplace.rb` or create service layer

```ruby
# app/services/orbital/gas_processing_service.rb (new)
module Orbital
  class GasProcessingService
    
    def self.process_and_list(station, recipe_name:, quantity:)
      # Phase 1: Execute gas processing via station's units
      job_id = station.process_gases(recipe_name: recipe_name, quantity: quantity)
      
      # Wait for completion (or schedule callback on tick-based model)
      processed_outputs = wait_for_completion(job_id)
      
      # Phase 2: Auto-list propellants on market at calculated ask prices  
      marketplace = station.settlement.marketplace
      
      processed_outputs.each do |resource_name, output_quantity|
        ask_price = Market::NpcPriceCalculator.calculate_ask(
          station.settlement, 
          resource_name,
          supply: output_quantity # Higher inventory → lower price
        )
        
        marketplace.place_order({
          orderable: station.settlement.owner, # Zenith Orbital/LDC as seller
          resource: resource_name,
          quantity: output_quantity,
          order_type: :sell,
          price_per_unit: ask_price
        })
      end
      
      return processed_outputs
    end
    
  private

    def self.wait_for_completion(job_id)
      # Tick-based model: advance game time until processing complete  
      # Real-time model: sleep or schedule callback for completion timestamp
      # Return hash of { resource_name => output_quantity }
    end
  end
end
```

### Phase 4: AI Manager Depot Owner Strategy Integration
**Files:** Extend `app/services/ai_manager/resource_acquisition_service.rb` or create new service

```ruby
# app/services/ai_manager/orbital_depot_manager.rb (new)
module AIManager
  class OrbitalDepotManager
    
    # Called periodically to manage depot pricing strategy as owner
    def self.manage_pricing_strategy(settlement, gas_type)
      station = settlement.orbital_structures.first
      
      # Get current inventory levels for raw inputs and processed outputs  
      raw_inventory = station.current_raw_gas_inventory(gas_type)
      marketplace = settlement.marketplace
      
      # Adjust buy prices based on how much we want to attract from skimmers
      if raw_inventory < LOW_THRESHOLD
        increase_buy_prices(marketplace, gas_type, delta: 0.15) # +15% bid price
      elsif raw_inventory > HIGH_THRESHOLD  
        decrease_buy_prices(marketplace, gas_type, delta: 0.10) # -10% bid price
      
      end

      # Adjust sell prices for processed propellants based on cycler demand
      processed_outputs = ['liquid_ch4_propellant', 'lox_propellant']
      processed_outputs.each do |propellant|
        output_inventory = station.current_processed_output_inventory(propellant)
        
        if output_inventory > OPTIMAL_STOCK_LEVEL  
          decrease_sell_prices(marketplace, propellant, delta: 0.12) # Discount to move inventory
        elsif output_inventory < MINIMUM_STOCK_LEVEL
          increase_sell_prices(marketplace, propellant, delta: 0.20) # Premium pricing when scarce
      
      end

    end
    
    def self.handle_skimmer_arrival(skimmer, settlement)
      harvested_gases = skimmer.inventory.select { |item| item.type == 'raw_gas' }
      
      if settlement.can_accept_skimeter_overflow?(harvested_gases)
        # Route to this station for unloading + processing  
        direct_craft_to_dock(skimmer, settlement.primary_orbital_structure)
        
        # Place buy orders at competitive prices to attract skimmer sales
        harvested_gases.each do |gas|
          bid_price = Market::NpcPriceCalculator.calculate_bid(
            settlement, 
            gas.resource_name,
            demand: 1.25 # Slight premium over EAP to incentivize docking here
          )
          
        end

      else
        # Overflow routing logic — find alternative depot or return skimmer home  
        route_to_alternative_depot(skimmer, harvested_gases)
      
      end

    end
    
  private
  
    def self.increase_buy_prices(marketplace, resource, delta:)
      marketplace.adjust_bid_price(resource, multiplier: (1.0 + delta))
    end
    
    # ... similar for other price adjustment methods
  end
end
```

---

## Testing Requirements

### RSpec Coverage — Must Include

**Unit Tests:**
- GasProcessingUnit blueprint recipes validate input/output stoichiometry  
- Processing time model completes correctly (tick-based or duration-based)
- Energy consumption tracked accurately over processing duration

**Integration Tests:**
- OrbitalStructure#process_gases consumes inventory + energy, produces outputs  
- Auto-listing creates sell orders at correct ask prices on marketplace
- Skimmer overflow routing directs craft to available depot with capacity

**System Tests (AI Manager Integration):**
- `OrbitalDepotManager.manage_pricing_strategy` adjusts bid/ask based on inventory levels  
- AI Manager participates in market as depot owner, not just passive observer
- Processing pipeline integrates with existing trade execution service for GCC settlement

---

## Dependencies & Blockers

### Required Before Starting (Already Implemented ✅)
- Market order system + trade execution (`Market::Order`, `TradeExecutionService`) — **LIVE**  
- OrbitalStructure model with docking/storage capabilities — **LIVE**  
- Financial account transfer_funds for GCC settlement — **LIVE**  

### Phase 5+ Dependencies (May Block Full Implementation)
- WormholeNavigator upgrade task (`2026-06-07-MEDIUM-FEATURE-WORMHOLE-NAVIGATOR-UPGRADE.md`) — needed for multi-system cycler route planning  
- Foothold Establishment Service (`2026-06-07-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE.md`) — establishes orbital depots at new systems

### Not Blocking (Can Implement Independently)
- Multi-system resource coordination algorithms (blocked by both above tasks anyway)  
- Dynamic orbital position simulation (use static SpatialLocation distances for now)

---

## Success Criteria & Acceptance Tests

**Functional:**
1. ✅ Skimmer craft can dock at L1 Station and sell raw harvested gases via market orders  
2. ✅ Gas processing units consume inputs + energy over time, produce processed propellants  
3. ✅ Processed outputs auto-list on marketplace with calculated ask prices  
4. ✅ AI Manager adjusts bid/ask spreads based on inventory levels (depot owner strategy)
5. ✅ Skimmer overflow routing directs craft to available depot when L1 at capacity

**Non-Functional:**
6. ✅ Full RSpec coverage for gas processing pipeline (unit + integration tests)  
7. ✅ No breaking changes to existing market order system or trade execution service  
8. ✅ Clear documentation of energy model assumptions and tick-based vs real-time choices

---

## Notes & Caveats

**Energy Model Assumption:** Start with simplified continuous power consumption — no battery storage, always-on solar panels at orbital stations. Can add complexity later (battery buffers for eclipse periods if needed).

**Processing Time Choice:** Tick-based model recommended for Phase 5 implementation:
- Deterministic and easier to test  
- Aligns with existing game loop patterns in AI Manager services  
- Real-time duration can be added when full simulation exists  

**Market Integration Pattern:** Auto-listing creates sell orders immediately on processing completion. Alternative pattern (manual order placement by depot owner) available if human wants more control over pricing strategy — document this design decision clearly.

---

## Related Tasks & References

### Phase 5+ Task Files in Same Folder
- `2026-06-07-MEDIUM-FEATURE-WORMHOLE-NAVIGATOR-UPGRADE.md` — weighted pathfinding for cycler routes  
- `2026-06-07-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE.md` — multi-system depot establishment
- `2026-06-08-MEDIUM-FEATURE-RETURN-CARGO-PROFIT-OPTIMIZATION.md` — cycler cargo economics

### Archived Source Material  
- Original architecture task: `docs/agent/archive/backlog_april_2026/2026-04-10-HIGH-ARCHITECTURE-ORBITAL-MARKET-SYSTEM.md` (move to deprecated after this task created)
- Supply chain design intent: `docs/new_agent/projects/galaxy_game/status.md#supply-chain-architecture-design-intent-not-yet-implemented`

### Next Session Actions After This Task Created  
1. Archive original April 2026 orbital market system task file to `deprecated/` folder with header note explaining gas processing gap extracted as this new Phase 5+ task
2. Continue April backlog review — suggested next: verify if other architecture tasks superseded by Luna phases

---

## Task File Metadata (For Agent Workflow)

**Created By:** Qwen3.5-27B during April 2026 backlog triage session  
**Extracted From:** `docs/agent/archive/backlog_april_2026/2026-04-10-HIGH-ARCHITECTURE-ORBITAL-MARKET-SYSTEM.md` (gas processing gap identified during infrastructure audit)  
**Assigned To:** TBD — requires executor with experience in manufacturing services + market integration  
**Estimated Complexity:** HIGH — multi-file implementation across models, services, AI Manager integration  
**Supervision Level:** 🟡 Standard — clear scope but needs architectural decisions on energy model and time system
