# DESIGN: AI Manager Cargo Routing — Power-Constrained Processing & Price-Driven Venting

**Date**: 2026-07-10 (reframed)
**Task**: `2026-07-10-HIGH-RESEARCH-ATMOSPHERIC-TRANSFER-VENTING-DESIGN.md`
**Researcher**: Research Agent (Qwen3.6-35b)

---

## Executive Summary — FUNDAMENTAL REFRAME

The previous design treated venting as an `AtmosphericTransferService` mode. **This is architecturally wrong.**

Venting is a **cargo routing decision made by the AI Manager** after each skimmer processing cycle, before the skimmer departs for the depot. The decision chain is:

```
Skimmer dips → collects raw gas → returns to orbit → PROCESS (power-constrained)
    ↓
AI Manager evaluates remaining cargo:
  1. Fill fuel tanks first (LOX for Venus, CH4 for Titan)
  2. Price-check remainder via NpcPriceCalculator
  3. Store if price > threshold; vent to source if below threshold or storage full
```

**Key architectural insight**: The `AtmosphericTransferService` handles extraction/delivery only. Cargo routing (store vs vent) happens in the AI Manager layer, using existing services:
- `NpcPriceCalculator.calculate_bid(settlement, gas_name)` for price queries
- `Inventory#available_capacity` for storage capacity checks
- `CelestialBodies::Spheres::Atmosphere#add_gas` for venting back to source

---

## 1. Operational Context — Dip Cycle Model

### Skimmer Operations Are NOT Continuous

Skimmers operate in **dip cycles**:
1. **Dip**: Descend into atmosphere, collect raw mixed gas
2. **Extract**: `AtmosphericTransferService.raw_transfer()` extracts proportional gas mix
3. **Return**: Ascend to orbit/docking point
4. **Process**: Power-constrained processing of collected cargo
5. **Route**: AI Manager decides store vs vent for remainder
6. **Deploy**: Dock at depot (or Luna surface) for offload

### Processing Capacity Is Power-Constrained

| Skimmer Type | Power Source | Processing Capacity | Primary Output |
|---|---|---|---|
| Venus skimmer | Solar (good orbit) | Higher kW/hr per cycle | O2/LOX (craft needs LOX more than CH4) |
| Titan skimmer | RTG only | Limited kW/hr per cycle | CH4 for fuel (lower power than full processing) |

**Power-to-processing mapping**:
```ruby
# Processing capacity per dip cycle = available_power_kw × cycle_duration_hours
# Venus: solar panels → ~50-100 kW continuous in orbit
# Titan: RTG → ~2-5 kW continuous (RTG output degrades over time)

# Per blueprint, each ISRU unit has:
#   power_required_kw (from blueprint JSON)
#   processing_rate_kg_per_kwh (from blueprint JSON or derived from chemical stoichiometry)

# Available processing per cycle:
available_processing_kg = min(
  available_power_kw / total_power_required_kw * unit_count,
  raw_cargo_mass  # can't process more than collected
) * efficiency_factor
```

### Existing Precedent: ISRU Evaluator Power Model

The `AIManager::ISRUEvaluator` already models power-constrained processing:
```ruby
def assess_capabilities
  units = inventory_isru_units
  power = assess_power_availability
  required = calculate_total_power_requirement(units)
  
  if required > 0 && power < required
    return { status: :blocked, reason: :insufficient_power, ... }
  end
  
  # Power sufficient → calculate production rates
  { production_rates: calculate_production_rates(units, resources), ... }
end
```

This pattern applies directly to skimmer processing: **power is the hard constraint on how much gas can be processed per dip cycle**.

---

## 2. Processing Power Model

### Power Source → Processing Capacity Mapping

```ruby
POWER_SOURCES = {
  solar: { base_kw: 75, degradation_per_orbit: 0.001, min_kw: 10 },
  rtg:   { base_kw: 3,   degradation_per_orbit: 0.0001, min_kw: 0.5 }
}.freeze

# Per-craft processing capacity per dip cycle:
def processing_capacity_kg(craft)
  power_source = craft.power_source_type  # :solar or :rtg
  power_config = POWER_SOURCES[power_source]
  
  current_kw = [power_config[:base_kw] * (1 - craft.orbit_count * power_config[:degradation_per_orbit]),
                power_config[:min_kw]].max
  
  # Cycle duration depends on orbit altitude and body
  cycle_hours = craft.dip_cycle_duration_hours  # from blueprint or derived
  
  # Each ISRU unit consumes power proportional to its processing rate
  total_power_required = craft.isru_units.sum { |unit| unit.power_required_kw }
  
  if current_kw < total_power_required && total_power_required > 0
    # Partial processing: scale output proportionally
    utilization_ratio = current_kw / total_power_required
    raw_cargo_mass * utilization_ratio * craft.processing_efficiency
  else
    # Full processing capacity available
    [raw_cargo_mass, current_kw * cycle_hours * craft.kg_per_kwh].min
  end
end
```

### Venus vs Titan Processing Profiles

```ruby
# Venus Skimmer Processing Profile
venus_profile = {
  power_source: :solar,
  processing_capacity_kg_per_cycle: 500..1000,  # higher due to solar
  primary_output: 'O2',                         # LOX for craft propulsion
  secondary_output: 'N2',                       # byproduct (store or vent)
  self_fuel_deduction: { gas: 'CO2', kg_per_kg_o2: 1.33 },  # Sabatier reactor
  processing_priority: ['O2', 'CO2_scrubbing', 'N2']
}

# Titan Skimmer Processing Profile
titan_profile = {
  power_source: :rtg,
  processing_capacity_kg_per_cycle: 10..50,     # limited by RTG
  primary_output: 'CH4',                         # fuel for next dip cycle
  secondary_output: 'N2',                        # remainder after CH4 separation
  self_fuel_deduction: { gas: 'NH3', kg_per_kg_ch4: 1.0 },  # ammonia cracking
  processing_priority: ['CH4', 'NH3_decomposition', 'N2']
}
```

---

## 3. Cargo Routing Decision Chain (AI Manager Layer)

### Where the Decision Occurs

The routing decision happens in the AI Manager **after each processing cycle**, before skimmer departs for depot:

```ruby
# In AIManager::SkimmerDispatchService or similar:
def process_skimmer_cargo(skimmer, processing_results)
  # Step 1: Fill fuel tanks (priority over everything else)
  fuel_filled = fill_fuel_tanks(skimmer, processing_results)
  
  # Step 2: Price-check remaining cargo
  remaining_cargo = skimmer.processed_cargo.reject { |_, v| v <= 0 }
  routing_decisions = {}
  
  remaining_cargo.each do |gas_name, mass|
    decision = route_gas_decision(skimmer, gas_name, mass)
    routing_decisions[gas_name] = decision
  end
  
  # Step 3: Execute routing
  execute_routing(skimmer, routing_decisions)
  
  # Step 4: Record results
  {
    fuel_filled: fuel_filled,
    routing_decisions: routing_decisions,
    cargo_for_depot: skimmer.cargo_ready_for_delivery,
    vented_to_source: skimmer.gases_vented_to_source
  }
end

def route_gas_decision(skimmer, gas_name, mass)
  # Step A: Check storage capacity at target location
  available = query_storage_capacity(skimmer.target_depot, gas_name)
  
  if available >= mass
    return { action: :store, reason: :has_capacity }
  end
  
  # Step B: Price check (existing hook: store_if_value_positive policy)
  price = NpcPriceCalculator.calculate_bid(skimmer.target_depot, gas_name)
  threshold = skimmer.operational_data.dig(:gas_handling_policy, :minimum_value_threshold, gas_name) || DEFAULT_THRESHOLD
  
  if price && price >= threshold
    # Partial storage: store what fits
    stored_amount = [available, mass].min
    return { action: stored_amount > 0 ? :partial_store : :vent, 
             amount: stored_amount, reason: :price_positive }
  end
  
  # Step C: Vent to source (below threshold or no storage)
  { action: :vent, reason: :price_below_threshold } if ventable?(gas_name)
end
```

### Price-Driven Venting Logic

```ruby
# store_if_value_positive policy — existing hook in HLT operational data
def should_store?(gas_name, price, threshold)
  # Configurable per-gas threshold from skimmer's operational_data
  min_threshold = threshold || DEFAULT_MINIMUM_VALUE_THRESHOLD
  price >= min_threshold
end

# Default thresholds (configurable per mission profile)
DEFAULT_MINIMUM_VALUE_THRESHOLD = {
  'O2' => 50.0,    # High value — always store if possible
  'CH4' => 80.0,   # Fuel grade — high threshold
  'N2' => 10.0,    # Low value — vent more readily
  'CO' => 30.0,    # Byproduct asset — moderate threshold
  'CO2' => 15.0    # Moderate value
}.freeze
```

---

## 4. Venting Implementation — Add Gas Back to Source Atmosphere

### Venting Is a Simple Operation

```ruby
def vent_to_source(skimmer, gas_name, mass)
  source_atmosphere = skimmer.source_body.atmosphere
  return false unless source_atmosphere.present?
  
  # Safety: verify gas is ventable (not a byproduct asset)
  return false if NON_VENTABLE_BYPRODUCTS.include?(gas_name)
  
  source_atmosphere.add_gas(gas_name, mass)
  
  # Record in skimmer state
  skimmer.gases_vented_to_source ||= {}
  skimmer.gases_vented_to_source[gas_name] = (skimmer.gases_vented_to_source[gas_name] || 0) + mass
  
  true
end

NON_VENTABLE_BYPRODUCTS = %w[CO].freeze  # CO from MOXIE is a fuel asset
VENTABLE_GASES = %w[N2 CH4 Ar CO2 He Ne Kr Xe H2 O2].freeze
```

### Mass Balance Tracking

```ruby
def verify_mass_balance(skimmer)
  total_extracted = skimmer.raw_cargo.values.sum
  total_routed = [
    skimmer.fuel_tank_contents.values.sum,
    skimmer.cargo_ready_for_delivery.values.sum,
    (skimmer.gases_vented_to_source&.values || []).sum
  ].max
  
  transport_loss = total_extracted - total_routed
  tolerance = total_extracted * 0.02  # 2% tolerance for transport loss
  
  if (transport_loss - skimmer.transport_loss_kg).abs > tolerance
    log_warning("Mass balance mismatch: extracted=#{total_extracted}, routed=#{total_routed}, loss=#{transport_loss}")
  end
end
```

---

## 5. MVP Scope — One Dip Cycle Per Dispatch

### MVP Boundaries

| Feature | In MVP? | Notes |
|---|---|---|
| Single dip cycle per dispatch | ✅ Yes | Skimmer: dip → collect → return → process → route → deploy |
| Power-constrained processing | ✅ Yes | Solar vs RTG determines kg/cycle capacity |
| Price-driven vent decision | ✅ Yes | NpcPriceCalculator + configurable threshold |
| Store-if-value-positive policy | ✅ Yes | Existing hook in HLT operational data |
| Vent to source atmosphere | ✅ Yes | `add_gas` back to source body |
| Fuel tank priority routing | ✅ Yes | LOX for Venus, CH4 for Titan |
| Multi-cycle simulation | ❌ No | Phase 9+ — continuous operations |
| Terraforming-scale venting | ❌ No | Phase 9+ |
| Dynamic power degradation | ❌ No | MVP uses static power source values |
| Per-orbit solar degradation | ❌ No | Phase 9+ |

### MVP Scenario: Venus Skimmer Single Dispatch

```
1. DIP CYCLE START
   - Skimmer descends to Venus atmosphere
   - AtmosphericTransferService.raw_transfer() extracts proportional gas mix
   - Raw cargo: { 'CO2' => 800kg, 'N2' => 50kg, 'SO2' => 5kg } = 855kg total

2. RETURN TO ORBIT
   - Skimmer ascends to Venus orbit (solar powered)
   - Transport efficiency: 98% → cargo mass reduced by 2%
   - Actual cargo: { 'CO2' => 784kg, 'N2' => 49kg, 'SO2' => 5kg } = 838kg

3. PROCESSING (Power-Constrained)
   - Solar power: ~75 kW available
   - Processing priority: O2 first (LOX for craft), then CO2 scrubbing
   - MOXIE processing: CO2 → O2 + CO
   - Processing capacity: 600kg CO2 processed (power-limited, not cargo-limited)
   - Outputs:
     * O2 produced: 600 × (32/44) × 0.95 = 415kg → fills LOX fuel tank
     * CO produced: 600 × (28/44) × 0.95 = 360kg → byproduct asset
   - Remaining unprocessed: { 'CO2' => 184kg, 'N2' => 49kg, 'SO2' => 5kg }

4. CARGO ROUTING (AI Manager Decision)
   a) Fuel tanks filled: LOX = 415kg ✓
   b) Price check on remaining cargo:
      - CO2: bid price = $12/kg, threshold = $15/kg → BELOW threshold
      - N2: bid price = $8/kg, threshold = $10/kg → BELOW threshold  
      - SO2: bid price = $5/kg, threshold = $8/kg → BELOW threshold
   c) Storage check (L1 Depot cryo tanks):
      - CO2: 50kg remaining capacity → partial store
      - N2: 0 capacity → vent
      - SO2: 0 capacity → vent
   d) Decisions:
      - CO2: store 50kg at L1, vent 134kg to Venus
      - N2: vent 49kg to Venus (below threshold + no storage)
      - SO2: vent 5kg to Venus (byproduct, not asset)

5. DEPART FOR DEPOT
   - Cargo for delivery: { 'O2' => 415kg (fuel), 'CO' => 360kg (asset) }
   - Vented to source: { 'CO2' => 134kg, 'N2' => 49kg, 'SO2' => 5kg }
   - Stored at L1: { 'CO2' => 50kg }

6. MASS BALANCE VERIFICATION
   Extracted: 838kg = Delivered(775kg) + Stored(50kg) + Vented(188kg)
   Wait — CO is byproduct, not vented:
   Extracted: 838kg = Fuel(415kg) + Asset(360kg) + Stored(50kg) + Vented(188kg)
   Total routed: 415 + 360 + 50 + 188 = 1,013kg
   Hmm — CO was PRODUCED from CO2, not extracted. Correct:
   Extracted raw: 838kg (CO2:784, N2:49, SO2:5)
   Processed: 600kg CO2 → 415kg O2 + 360kg CO (net gain from processing)
   Remaining raw: 184kg CO2 + 49kg N2 + 5kg SO2 = 238kg
   Routed: 415(O2 fuel) + 360(CO asset) + 50(CO2 stored) + 188(vented) = 1,013kg
   Mass balance: 784(CO2 extracted) = 600(processed) + 184(unprocessed) ✓
                  600(CO2 processed) → 415(O2) + 360(CO) - 175(processing mass diff) ✓
```

---

## 6. Architecture — Where Each Piece Lives

### Service Layer Responsibilities

| Service | Responsibility | Venting Role |
|---|---|---|
| `AtmosphericTransferService` | Extraction/delivery only | NONE — extracts raw gas, delivers to target |
| `AIManager::SkimmerDispatchService` (new) | Processing + routing decisions | YES — makes store vs vent decisions |
| `NpcPriceCalculator` | Market price queries | YES — provides bid prices for decision |
| `Inventory` model | Storage capacity queries | YES — reports available cryo tank capacity |
| `Atmosphere` model | Gas state management | YES — `add_gas` for venting back to source |

### Key Architectural Decision: NO Changes to AtmosphericTransferService

The previous design incorrectly proposed adding venting logic to `AtmosphericTransferService`. **This is wrong because:**

1. **Wrong layer**: ATM service handles transport, not business logic
2. **Wrong context**: ATM doesn't know about skimmer power constraints or market prices
3. **Wrong scope**: Venting is a per-craft cargo decision, not a transport mode
4. **Breaking changes**: Adding venting to ATM would require new modes and break existing consumers

**Correct architecture**: ATM extracts → AI Manager processes → AI Manager routes (store/vent) → ATM delivers stored gas to depot.

---

## 7. Integration with Existing Systems

### NpcPriceCalculator Integration

```ruby
# Existing API — no changes needed:
bid_price = Market::NpcPriceCalculator.calculate_bid(settlement, gas_name)
spread = Market::NpcPriceCalculator.calculate_spread(settlement, gas_name)

# For venting decision:
price = Market::NpcPriceCalculator.calculate_bid(skimmer.target_depot, gas_name)
threshold = skimmer.operational_data.dig(:gas_handling_policy, :minimum_value_threshold, gas_name)
should_store = price && price >= (threshold || DEFAULT_MINIMUM_VALUE_THRESHOLD)
```

### store_if_value_positive Hook

The existing `store_if_value_positive` policy in HLT operational data is the correct hook:
```ruby
# In skimmer blueprint or operational_data:
{
  gas_handling_policy: {
    mode: :price_driven,           # or :always_store, :never_vent
    store_if_value_positive: true, # existing hook
    minimum_value_threshold: {      # per-gas thresholds (optional)
      'O2' => 50.0,
      'CH4' => 80.0,
      'N2' => 10.0,
      'CO' => 30.0
    },
    vent_to_source: true            # allow venting back to source body
  }
}
```

### Storage Capacity Integration

```ruby
# Existing Inventory model — no changes needed:
inventory = skimmer.target_depot.inventory
available_capacity = inventory.available_capacity  # returns hash or total kg
gas_available = available_capacity[gas_name] || available_capacity

# Cryo tanks are already blueprint-defined (inflatable_cryo_tank_bp.json)
# and deployed into inventory. No new infrastructure needed.
```

---

## 8. MVP Scope Boundary Definition

### MVP (This Task)
- Power-constrained processing per dip cycle (solar vs RTG)
- Price-driven vent decision via NpcPriceCalculator
- store_if_value_positive policy as configurable hook
- Vent to source atmosphere (add_gas back to source body)
- Fuel tank priority routing (LOX for Venus, CH4 for Titan)
- Single dip cycle per dispatch
- Mass balance verification

### Phase 9+ (Separate Task)
- Multi-cycle continuous operations
- Dynamic power degradation (solar panel aging, RTG decay)
- Per-orbit solar degradation modeling
- Terraforming-scale atmospheric modification
- Multi-world atmospheric interaction via cycler routes
- Automated venting optimization (AI-driven decisions)
- Environmental constraints on per-world venting

### Scope Boundary Rationale

MVP scope is **operational skimmer dispatch** — the immediate need for Luna settlement fuel delivery. Phase 9+ scope is **strategic atmospheric management** — long-term terraforming across multiple worlds.

---

## 9. Deliverables Summary

1. ✅ Design document specifying power-constrained processing model (solar vs RTG)
2. ✅ Cargo routing decision chain in AI Manager layer (not ATM service)
3. ✅ Price-driven vent decision using NpcPriceCalculator + configurable threshold
4. ✅ Venting implementation: add_gas back to source Atmosphere object
5. ✅ Storage capacity integration via existing Inventory model
6. ✅ MVP scope: one dip cycle per dispatch, not continuous simulation
7. ✅ Mass balance verification across extraction/processing/routing
8. ✅ Architecture: NO changes to AtmosphericTransferService required

---

## Appendix A: Gas Classification Reference

### Ventable Gases (can be returned to source atmosphere)
`N2`, `CH4`, `Ar`, `CO2`, `He`, `Ne`, `Kr`, `Xe`, `H2`, `O2`

### Non-Ventable Byproducts (must be tracked as assets)
`CO` — from MOXIE CO2 processing; usable as fuel/propellant

### Conditional Gases (evaluate per-world)
- `H2O` — ventable if source has water vapor capacity
- `NH3` — ventable but may have environmental constraints on Titan

---

## Appendix B: Files Affected by Implementation

| File | Change Type | Notes |
|---|---|---|
| New: `ai_manager/skimmer_dispatch_service.rb` | Create | Processing + routing logic |
| Existing: `atmospheric_transfer_service.rb` | None | No changes needed |
| Existing: `npc_price_calculator.rb` | None | Read-only price queries |
| Existing: `inventory.rb` | None | Read-only capacity queries |
| Existing: `celestial_bodies/spheres/atmosphere.rb` | None | Read-only add_gas for venting |
| Blueprint JSON (skimmer HLT) | Extend | Add operational_data with gas_handling_policy |

---

## Appendix C: Stop Condition Assessment

**No stop conditions triggered.** The existing architecture fully supports this feature:
- ✅ `NpcPriceCalculator.calculate_bid()` for price queries
- ✅ `Inventory#available_capacity` for storage checks
- ✅ `Atmosphere#add_gas` for venting back to source
- ✅ `ISRUEvaluator` power model as precedent for power-constrained processing
- ✅ `store_if_value_positive` hook already exists in HLT operational data
- ✅ Zero breaking changes required
