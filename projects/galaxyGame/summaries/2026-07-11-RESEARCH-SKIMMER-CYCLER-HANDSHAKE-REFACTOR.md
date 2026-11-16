# RESEARCH: SkimmerCyclerHandshakeService Refactor — Vessel Role Differentiation and Depot/Shipyard Logistics

**Date**: 2026-07-11
**Task**: `2026-07-10-HIGH-RESEARCH-SKIMMER-CYCLER-HANDSHAKE-REFACTOR.md`
**Researcher**: Research Agent (Qwen3.6-35b)
**Dependency**: `2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md` — Section "SkimmerCyclerHandshakeService — Conflicts with skimmer intent"

---

## Executive Summary

The current `SkimmerCyclerHandshakeService#process_cargo` method processes ALL cargo at 90% efficiency for ALL vessel types, contradicting the MVP design constraint that skimmers have **limited onboard processing** (only enough to refill their own fuel tanks). This research designs a complete refactoring with two distinct vessel profiles:

1. **Harvester Variant** (Local Circuit): Atmosphere ↔ Depot — limited onboard processing for fuel-tank refilling only
2. **Tanker Variant** (Cycler Circuit): Depot ↔ Cycler — bulk gas transport using 8x cryogenic storage units, refueling from Depot reserves

**Key Finding**: Blueprint data confirms capacity constraints exist but lack explicit fuel tank volumes. The design must derive fuel capacities from compatible unit blueprints (`lox_tank`, `methane_tank`) and use operational_data flags for variant identification.

---

## 1. Vessel Role Differentiation — Harvester vs Tanker

### Current Code (Wrong)

```ruby
# CURRENT (WRONG) — processes ALL cargo for ALL vessel types
def process_cargo(skimmer, cycler)
  required_energy = skimmer.raw_cargo.values.sum * 2
  return false if cycler.energy_reserve < required_energy
  cycler.energy_reserve -= required_energy
  skimmer.processed_cargo = skimmer.raw_cargo.transform_values { |v| v * 0.9 }
  skimmer.raw_cargo = {}
  skimmer.available = true
  true
end
```

**Problem**: No variant differentiation. All vessels get full cargo processing regardless of operational role.

### Harvester Variant (Local Circuit)

| Attribute | Value | Source |
|---|---|---|
| **Operational domain** | Atmosphere ↔ Depot (local circuit) | MVP design constraint |
| **Depart trigger** | `if cargo_tanks.full? then depart_for_depot` | MVP design constraint |
| **Onboard processing mode** | **Fuel-tank-only** during transit | MVP design constraint |
| **Blueprint** | `orbital_em_skimmer_mid_bp.json` | Blueprint data |
| **Cargo capacity** | 2,200 m³ (physical_properties) | Blueprint |
| **Fuel storage ports** | 4 (ports.fuel_storage_ports) | Blueprint |
| **Compatible fuel units** | `lox_tank`, `methane_tank` | Blueprint compatible_units |
| **Processing during docking** | Offload to Depot infrastructure (NOT onboard) | MVP design constraint |

**Logic**:
```ruby
# Harvester Variant — Limited Onboard Processing
def process_cargo_harvester(skimmer, cycler)
  # Step 1: Calculate fuel-tank refilling needs only
  fuel_needed = calculate_fuel_tank_refill(skimmer)
  
  # Step 2: Deduct from raw cargo (NOT all cargo)
  return false if skimmer.raw_cargo.values.sum < fuel_needed
  
  # Step 3: Process ONLY enough cargo to refill fuel tanks
  skimmer.fuel_tanks.refill(fuel_needed)
  skimmer.raw_cargo = reduce_raw_cargo_by(skimmer.raw_cargo, fuel_needed)
  
  # Step 4: Mark for Depot offload (NOT available for next dive)
  skimmer.available = false  # Must dock at Depot first
  true
end
```

### Tanker Variant (Cycler Circuit)

| Attribute | Value | Source |
|---|---|---|
| **Operational domain** | Depot ↔ Cycler (inter-depot circuit) | MVP design constraint |
| **Optimize for** | `logistics_priority` routing between orbital nodes | MVP design constraint |
| **Storage config** | 8x `cryogenic_storage_unit` | MVP design constraint |
| **Blueprint** | `base_cycler_bp.json` | Blueprint data |
| **Cryo unit capacity** | 10 m³ each (blueprint_data.output.volume_m3) | Blueprint |
| **Total cryo capacity** | 80 m³ (8 × 10 m³) | Derived |
| **Storage ports** | 16 (ports.storage_ports) | Blueprint |
| **Refuel logic** | Pull from Depot reserves upon docking | MVP design constraint |
| **Atmospheric extraction** | None — bulk gas transport only | MVP design constraint |

**Logic**:
```ruby
# Tanker Variant — Bulk Transport with Depot Refueling
def process_cargo_tanker(tanker, depot)
  # Step 1: Offload cargo to Depot cryo tanks
  offloaded = depot.receive_cargo(tanker.raw_cargo)
  
  # Step 2: Refuel from Depot reserves (NOT onboard processing)
  refueled = depot.refuel_vehicle(tanker, fuel_types: [:lox, :methane])
  
  # Step 3: Load new cargo for next leg
  tanker.load_cargo(depot.available_cargo, priority: tanker.logistics_priority)
  
  true
end
```

---

## 2. Station-Linked Operations Trigger

### Design: "Station-Detected" State Machine

```ruby
# Vessel operational state machine
class VesselStateTracker
  STATES = [:transit, :depot_detected, :no_depot, :docked]
  
  def detect_station(skimmer)
    # Proximity check via AI Manager routing system
    nearby_depots = ai_manager.find_nearby_depots(skimmer.current_position)
    
    if nearby_depots.any?
      skimmer.state = :depot_detected
      skimmer.trigger_rapid_shuttle_mode(nearby_depots.first)
    else
      skimmer.state = :no_depot
      skimmer.trigger_onboard_long_dwell_mode
    end
  end
  
  def trigger_rapid_shuttle_mode(depot)
    # Shift from onboard processing to rapid-shuttle logistics
    skimmer.offload_to_depot(depot)
    skimmer.refuel_from_depot(depot)
    skimmer.state = :docked
  end
  
  def trigger_onboard_long_dwell_mode
    # Limited processing for fuel-tank refilling only
    skimmer.process_fuel_tank_refill
    skimmer.state = :transit  # Continue transit to next target
  end
end
```

### Detection Mechanism

| Method | MVP Relevance | Notes |
|---|---|---|
| **Proximity check** | ✅ MVP | AI Manager routing system checks vessel position vs depot locations |
| **Beacon signal** | ❌ Phase 9+ | Requires new beacon infrastructure |
| **AI Manager routing** | ✅ MVP | Existing routing logic can be extended with depot-awareness |

### Fallback: Depot Unavailable Mid-Transit

```ruby
def handle_depot_unavailable(skimmer)
  case skimmer.variant
  when :harvester
    # Continue transit to Luna surface fallback (priority 2 destination)
    skimmer.departure_target = :luna_surface
    skimmer.state = :transit
  when :tanker
    # Wait in orbit, retry depot availability check every cycle
    skimmer.state = :waiting
    skimmer.retry_depot_check_after(300)  # 5-minute intervals
  end
end
```

---

## 3. Tanker Refuel-from-Depot-Reserves Logic

### Available Fuel Types from Depot Reserves

| Fuel Type | Source | Depot Availability |
|---|---|---|
| **LOX (Liquid Oxygen)** | Cryogenic tank modules (12x on orbital_depot_mk1) | ✅ High — primary depot output |
| **CH4 (Methane)** | Cryogenic tank modules + ISRU processing | ✅ Medium — requires CO2 feedstock |
| **H2 (Hydrogen)** | Byproduct of cryogenic processing | ⚠️ Low — vented at 0.05 kg/hr rate |

### Refuel Logic Design

```ruby
class DepotRefuelService
  def refuel_vehicle(depot, vehicle, fuel_types: [:lox, :methane])
    results = {}
    
    fuel_types.each do |fuel_type|
      available = depot.cryo_tanks[fuel_type] || 0
      needed = calculate_fuel_needed(vehicle, fuel_type)
      
      if available >= needed
        # Full refuel
        depot.cryo_tanks[fuel_type] -= needed
        vehicle.fuel_tanks[fuel_type] = needed
        results[fuel_type] = :full
      elsif available > 0
        # Partial refuel
        vehicle.fuel_tanks[fuel_type] = available
        depot.cryo_tanks[fuel_type] = 0
        results[fuel_type] = :partial
      else
        # No fuel available
        results[fuel_type] = :empty
      end
    end
    
    results[:success] = results.values.any? { |r| r != :empty }
    results
  end
  
  def calculate_fuel_needed(vehicle, fuel_type)
    case vehicle.variant
    when :tanker
      # Tanker needs full tank refill
      vehicle.fuel_tanks.capacity[fuel_type] - vehicle.fuel_tanks.current[fuel_type]
    when :harvester
      # Harvester only needs enough for next transit leg
      vehicle.transit_fuel_estimate(vehicle.next_target)
    end
  end
end
```

### Insufficient Reserves Handling

```ruby
def handle_insufficient_reserves(depot, vehicle)
  if depot.cryo_tanks.values.sum < vehicle.fuel_tanks.capacity.values.sum * 0.5
    # Depot reserves critically low — trigger resupply request
    ai_manager.request_resupply(depot, :cryogenic_fuel, priority: :high)
    
    # Vehicle must wait or find alternative depot
    vehicle.state = :waiting_for_resupply
    vehicle.notify_operator("Depot reserves critically low. Awaiting resupply.")
  else
    # Partial refuel is acceptable — proceed with what's available
    vehicle.refuel_partial(depot)
    vehicle.depart
  end
end
```

---

## 4. Vessel Variant Identification Verification

### How to Determine Vessel Type

| Method | MVP Relevance | Implementation |
|---|---|---|
| **Blueprint data** | ✅ MVP | Check `compatible_units` for `atmospheric_harvester_system` |
| **operational_data flags** | ✅ MVP | Add `vessel_variant` field to operational_data hash |
| **Module configuration** | ✅ MVP | Check for `cryogenic_storage_unit` count |
| **Explicit assignment** | ✅ MVP | AI Manager assigns variant at vessel creation |

### Identification Logic

```ruby
class VesselIdentifier
  def identify_vessel_variant(vessel)
    # Method 1: Check operational_data flag (preferred)
    return vessel.operational_data[:vessel_variant] if vessel.operational_data[:vessel_variant]
    
    # Method 2: Check blueprint compatible_units
    blueprint = lookup.find_blueprint(vessel.blueprint_id)
    if blueprint&.compatible_units&.include?('atmospheric_harvester_system')
      :harvester
    elsif blueprint&.compatible_units&.include?('cryogenic_storage_unit')
      :tanker
    else
      # Method 3: Check module configuration
      cryo_count = vessel.modules.count { |m| m.type == 'cryogenic_storage_unit' }
      if cryo_count >= 8
        :tanker
      elsif vessel.modules.any? { |m| m.type == 'atmospheric_harvester_system' }
        :harvester
      else
        # Default: assume harvester (most common variant)
        :harvester
      end
    end
  end
  
  def validate_variant(vessel, expected_variant)
    actual = identify_vessel_variant(vessel)
    if actual != expected_variant
      log_error("Vessel #{vessel.id} identified as #{actual}, expected #{expected_variant}")
      return false
    end
    true
  end
end
```

### Error Handling for Wrong Variant Execution

```ruby
def ensure_correct_variant(vessel, expected_variant)
  actual = identify_vessel_variant(vessel)
  unless actual == expected_variant
    raise ArgumentError, "Vessel #{vessel.id} is #{actual} variant, not #{expected_variant}"
  end
end

# Usage in handshake service:
ensure_correct_variant(skimmer, :harvester)
process_cargo_harvester(skimmer, cycler)
```

---

## 5. HLT Blueprint Capacity Constraints

### HLT (Heavy Lift Transport) — Confirmed from Blueprint

| Attribute | Value | Source File |
|---|---|---|
| **Blueprint ID** | `heavy_lift_transport` | `heavy_lift_transport_mk1_bp.json` |
| **Empty mass** | 163,000 kg | physical_properties.empty_mass_kg |
| **Max payload** | 150,000 kg | physical_properties.max_payload_kg |
| **Cargo capacity** | 1,000 m³ | cargo_capacity_m3 |
| **Fuel storage ports** | 2 (mk1) / 4 (orbital_em_skimmer_mid) | ports.fuel_storage_ports |
| **Compatible fuel units** | `lox_tank`, `methane_tank` | compatible_units |

### Orbital EM Skimmer (Mid-size) — Confirmed from Blueprint

| Attribute | Value | Source File |
|---|---|---|
| **Blueprint ID** | `orbital_em_skimmer_mid` | `orbital_em_skimmer_mid_bp.json` |
| **Empty mass** | 320,000 kg | physical_properties.empty_mass_kg |
| **Cargo capacity** | 2,200 m³ | cargo_capacity_m3 |
| **Fuel storage ports** | 4 | ports.fuel_storage_ports |
| **Compatible fuel units** | `lox_tank`, `methane_tank` | compatible_units |

### Base Cycler — Confirmed from Blueprint

| Attribute | Value | Source File |
|---|---|---|
| **Blueprint ID** | `base_cycler` | `base_cycler_bp.json` |
| **Empty mass** | 600,000 kg | physical_properties.empty_mass_kg |
| **Storage ports** | 16 | ports.storage_ports |
| **Compatible cryo units** | `cryogenic_storage_unit` | compatible_units |
| **Cryo unit capacity** | 10 m³ each | `cryogenic_storage_unit_bp.json` blueprint_data.output.volume_m3 |
| **8x cryo total** | 80 m³ | Derived (8 × 10 m³) |

### Orbital Depot MK1 — Confirmed from Blueprint

| Attribute | Value | Source File |
|---|---|---|
| **Blueprint ID** | `orbital_depot_mk1` | `orbital_depot_mk1_bp.json` |
| **Volume** | 5,000 m³ | physical_properties.volume |
| **Cryo tank modules** | 12 | container_capacity.module_slots |
| **Recommended cryo pumps** | 5x mk2 | recommended_units |
| **Fuel type** | Cryogenic (LOX/CH4) | connection_systems.refueling_ports |

### Critical Gap: No Explicit Fuel Tank Volumes in Blueprints

**Finding**: Blueprint data does NOT contain explicit fuel tank capacity values (liters/kg). The design must derive fuel capacities from:
1. Compatible unit blueprints (`lox_tank`, `methane_tank`) — not yet created in blueprint repo
2. Standard industry assumptions (LOX density: 1.141 kg/L, CH4 density: 0.423 kg/L at cryo temps)
3. Port count × assumed unit capacity per port

**Recommendation**: Create `lox_tank_bp.json` and `methane_tank_bp.json` blueprints with explicit volume/mass data for MVP accuracy.

---

## 6. Refactored Service Structure Proposal

### Proposed Service Split

```
AIManager::SkimmerCyclerHandshakeService (main orchestrator)
├── identify_vessel_variant(vessel) → :harvester | :tanker
├── dock_skimmer(skimmer, cycler) → bool
├── process_cargo(skimmer, cycler) → bool
│   ├── ensure_correct_variant(skimmer, :harvester)
│   └── process_cargo_harvester(skimmer, cycler)
├── process_cargo_harvester(skimmer, cycler) → bool
│   ├── calculate_fuel_tank_refill(skimmer)
│   ├── deduct_from_raw_cargo(amount)
│   ├── refill_fuel_tanks(amount)
│   └── mark_for_depot_offload
├── process_cargo_tanker(tanker, depot) → bool
│   ├── offload_to_depot(depot)
│   ├── refuel_from_depot(depot)
│   └── load_new_cargo(depot)
├── detect_nearby_depots(vessel) → [depot]
├── handle_depot_unavailable(vessel) → :transit | :waiting
└── compatible?(skimmer, cycler) → bool (existing)
```

### Refactored Code Structure

```ruby
module AIManager
  class SkimmerCyclerHandshakeService
    # Primary entry point — routes to variant-specific logic
    def process_cargo(vessel, target)
      variant = identify_vessel_variant(vessel)
      
      case variant
      when :harvester
        ensure_depot_detected_or_onboard_processing(vessel, target)
      when :tanker
        ensure_depot_docked(vessel, target)
      else
        log_error("Unknown vessel variant: #{variant}")
        false
      end
    end
    
    # Vessel identification (prerequisite step)
    def identify_vessel_variant(vessel)
      return vessel.operational_data[:vessel_variant] if vessel.respond_to?(:operational_data) && vessel.operational_data[:vessel_variant]
      
      blueprint = LookupService.find_blueprint(vessel.blueprint_id)
      if blueprint&.compatible_units&.include?('atmospheric_harvester_system')
        :harvester
      elsif blueprint&.compatible_units&.include?('cryogenic_storage_unit')
        :tanker
      else
        :harvester  # Default
      end
    end
    
    # Harvester: Limited onboard processing for fuel-tank refilling only
    def process_cargo_harvester(skimmer, cycler)
      return false unless cycler.docked_at == skimmer || cycler.docked_at&.id == skimmer.id
      
      # Calculate fuel needed for next transit leg
      fuel_needed = calculate_fuel_tank_refill(skimmer)
      
      # Process ONLY enough raw cargo to refill fuel tanks (NOT all cargo)
      return false if skimmer.raw_cargo.values.sum < fuel_needed
      
      # Deduct from raw cargo (preserving remaining cargo for Depot offload)
      processed_amount = deduct_from_raw_cargo(skimmer.raw_cargo, fuel_needed)
      
      # Refill fuel tanks
      skimmer.fuel_tanks.refill(processed_amount)
      
      # Mark for Depot offload (NOT available for next dive until docked)
      skimmer.available = false
      skimmer.next_target = :depot
      
      true
    end
    
    # Tanker: Bulk transport with Depot refueling
    def process_cargo_tanker(tanker, depot)
      return false unless tanker.docked_at == depot
      
      # Offload cargo to Depot cryo tanks
      offloaded = depot.receive_cargo(tanker.raw_cargo)
      tanker.raw_cargo = {}
      
      # Refuel from Depot reserves (NOT onboard processing)
      refueled = depot.refuel_vehicle(tanker, fuel_types: [:lox, :methane])
      
      # Load new cargo for next leg
      tanker.load_cargo(depot.available_cargo, priority: tanker.logistics_priority)
      
      true
    end
    
    # Depot detection trigger
    def detect_nearby_depots(vessel)
      ai_manager.find_nearby_depots(vessel.current_position)
    end
    
    # Ensure correct variant before processing
    def ensure_correct_variant(vessel, expected_variant)
      actual = identify_vessel_variant(vessel)
      unless actual == expected_variant
        log_error("Vessel #{vessel.id} is #{actual}, expected #{expected_variant}")
        return false
      end
      true
    end
    
    private
    
    def calculate_fuel_tank_refill(skimmer)
      # Derive from compatible unit blueprints or operational_data
      skimmer.fuel_tanks.capacity.values.sum - skimmer.fuel_tanks.current.values.sum
    end
    
    def deduct_from_raw_cargo(raw_cargo, amount)
      # Process proportionally across all gas types in raw cargo
      total = raw_cargo.values.sum
      return 0 if total == 0
      
      processed = {}
      remaining = amount
      raw_cargo.each do |gas, mass|
        portion = (mass / total * amount).round(2)
        processed[gas] = [portion, mass].min
        remaining -= portion
        break if remaining <= 0
      end
      
      # Update skimmer's raw cargo
      raw_cargo.each_key { |gas| raw_cargo[gas] -= (processed[gas] || 0) }
      
      processed.values.sum
    end
  end
end
```

---

## 7. MVP vs Phase 9+ Scope Boundary

### MVP Scope (Must Implement Before Skimmer Fuel Delivery Works)

| # | Feature | Priority | Notes |
|---|---|---|---|
| 1 | **Vessel variant differentiation** | 🔴 CRITICAL | `identify_vessel_variant` + variant-specific processing methods |
| 2 | **Harvester: fuel-tank-only processing** | 🔴 CRITICAL | Limit to refilling skimmer's own fuel tanks during transit |
| 3 | **Tanker: Depot refuel from reserves** | 🔴 CRITICAL | Pull LOX/CH4 from depot cryo tanks upon docking |
| 4 | **Depot detection trigger** | 🟡 HIGH | Proximity check via AI Manager routing system |
| 5 | **Rapid-shuttle logistics mode** | 🟡 HIGH | Offload to Depot infrastructure when detected |
| 6 | **Variant identification verification** | 🟡 HIGH | Ensure correct variant logic before processing |
| 7 | **Depot-unavailable fallback** | 🟡 HIGH | Luna surface fallback for harvester, wait mode for tanker |

### Phase 9+ Scope (Terraforming — Separate Task)

| # | Feature | Priority | Notes |
|---|---|---|---|
| 1 | **Full skimmer processing augmentation at L1 Depot** | 🟢 LOW | +15% throughput per docked skimmer |
| 2 | **Multi-depot logistics optimization** | 🟢 LOW | Route optimization across multiple Depots |
| 3 | **Beacon signal infrastructure** | 🟢 LOW | New beacon system for depot detection |
| 4 | **Inter-depot cycler routing AI** | 🟢 LOW | Autonomous route planning between Depots |
| 5 | **Dynamic depot capacity allocation** | 🟢 LOW | Real-time cargo prioritization at Depots |

---

## Gap Analysis: Blueprint Data vs Design Requirements

### Confirmed from Blueprints ✅

| Data Point | Value | Source |
|---|---|---|
| HLT max payload | 150,000 kg | `heavy_lift_transport_mk1_bp.json` |
| HLT cargo capacity | 1,000 m³ | `heavy_lift_transport_mk1_bp.json` |
| Skimmer cargo capacity | 2,200 m³ | `orbital_em_skimmer_mid_bp.json` |
| Cycler storage ports | 16 | `base_cycler_bp.json` |
| Cryo unit capacity | 10 m³ each | `cryogenic_storage_unit_bp.json` |
| Depot cryo tank modules | 12 | `orbital_depot_mk1_bp.json` |

### Missing from Blueprints ❌ (Must Create or Derive)

| Data Point | Required Action | Priority |
|---|---|---|
| **LOX tank capacity** | Create `lox_tank_bp.json` with explicit volume/mass | 🔴 CRITICAL |
| **Methane tank capacity** | Create `methane_tank_bp.json` with explicit volume/mass | 🔴 CRITICAL |
| **Fuel tank port volumes** | Derive from compatible unit blueprints | 🟡 HIGH |
| **Cryo tank fuel types** | Define in depot blueprint (LOX, CH4, H2) | 🟡 HIGH |
| **Refuel flow rates** | Define in depot blueprint (kg/hr per fuel type) | 🟢 LOW |

---

## Architecture Recommendations

1. **Add `vessel_variant` to operational_data** — Explicit flag for variant identification (preferred over blueprint inspection)

2. **Split `process_cargo` into variant-specific methods** — `process_cargo_harvester` + `process_cargo_tanker` with explicit variant routing

3. **Add `identify_vessel_variant` prerequisite step** — Called before any processing to ensure correct logic path

4. **Implement Depot refuel service** — New `DepotRefuelService` class handling fuel availability checks and partial refuels

5. **Create missing unit blueprints** — `lox_tank_bp.json` and `methane_tank_bp.json` with explicit capacity data for MVP accuracy

6. **Add proximity-based depot detection** — Extend AI Manager routing system to check vessel position vs depot locations

7. **Implement rapid-shuttle mode** — State machine transition from onboard processing to Depot offload when station detected

---

## Stop Conditions Assessment

### HLT Blueprint Specs — Fuel/Cargo Tank Capacity Data

| Constraint | Status | Notes |
|---|---|---|
| **Cargo tank capacity** | ✅ Available | 1,000 m³ (HLT), 2,200 m³ (skimmer) |
| **Max payload mass** | ✅ Available | 150,000 kg (HLT) |
| **Fuel tank capacity** | ❌ Missing | No explicit LOX/CH4 tank volumes in blueprints |
| **Fuel tank port count** | ✅ Available | 2 ports (HLT mk1), 4 ports (skimmer) |

**Assessment**: Blueprint data contains cargo capacity but NOT explicit fuel tank volumes. The design must derive fuel capacities from compatible unit blueprints (which don't exist yet). This is a **data gap**, not an architectural blocker — MVP can proceed with assumed values until proper blueprints are created.

### Architectural Decisions Required Before Research Can Continue

**None required.** All 7 research requirements have been addressed with design proposals based on existing blueprint data and MVP constraints. The missing fuel tank volume data is a known gap that can be worked around with assumptions.

---

## Files Referenced in This Research

| File | Purpose |
|---|---|
| `app/services/ai_manager/skimmer_cycler_handshake_service.rb` | Current (wrong) implementation |
| `data/json-data/blueprints/crafts/space/spacecraft/heavy_lift_transport_mk1_bp.json` | HLT capacity constraints |
| `data/json-data/blueprints/crafts/space/spacecraft/orbital_em_skimmer_mid_bp.json` | Skimmer capacity constraints |
| `data/json-data/blueprints/crafts/space/spacecraft/base_cycler_bp.json` | Cycler storage ports, cryo compatibility |
| `data/json-data/blueprints/units/storage/cryogenic_storage_unit_bp.json` | Cryo unit capacity (10 m³) |
| `data/json-data/blueprints/structures/space_stations/orbital_depot_mk1_bp.json` | Depot cryo tank modules, refueling ports |
| `data/json-data/blueprints/structures/space_stations/l1_shipyard_bp.json` | L1 Shipyard docking capacity |
| `docs/new_agent/projects/galaxy_game/summaries/2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md` | Dependency — previous skimmer audit |

---

## Next Steps for Implementation Agent

1. **Create missing unit blueprints** (`lox_tank_bp.json`, `methane_tank_bp.json`)
2. **Add `vessel_variant` to operational_data schema**
3. **Refactor `SkimmerCyclerHandshakeService`** with variant-specific methods
4. **Implement `DepotRefuelService`** for Tanker refueling logic
5. **Add proximity-based depot detection** to AI Manager routing
6. **Write RSpec tests** for both Harvester and Tanker variants

---

**Research Complete**: 2026-07-11
**Status**: Ready for implementation agent
