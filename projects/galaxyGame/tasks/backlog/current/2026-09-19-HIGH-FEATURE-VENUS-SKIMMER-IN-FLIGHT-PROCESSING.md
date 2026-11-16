---
status: backlog
priority: high
type: feature
category: mission-execution
tags: [venus-skimmer, in-flight-processing, volatile-routing]
created: 2026-09-19
updated: 2026-09-19
---

# Implement In-Flight CO2 Cracking + Venting/Storage Decision Logic

## Problem Statement

The Venus skimmer is designed to actively process its payload mid-flight—cracking CO2 into LOX (O2) and CO (carbon monoxide). Depending on market conditions and tank configuration, the resulting CO can be:
1. **Vented** back into the Venusian atmosphere (sacrificing mass for storage space)
2. **Stored** in a dedicated CO tank (if equipped)
3. **Remixed** back into the main volatile tank (if no dedicated storage)

Currently, no in-flight processing logic exists. The skimmer simply hauls raw cargo without any transformation between scoop and arrival.

## Design & Scope

### Processing Flow

**Initial State** (after `removeRawGas` extraction):
```ruby
Mixed Volatiles Item
├─ metadata['composition'] = { CO2: 650kg, N2: 35kg, ... }
├─ metadata['total_mass_kg'] = 720kg
└─ dedicated LOX tank = empty
```

**Step 1: CO2 Cracking**
- Calculate crackable CO2 mass (e.g., 50% of available CO2)
- Chemical reaction: CO2 → 8/11 O2 + 3/11 CO (by mass)
- Example: 325 kg CO2 cracks to ~237 kg O2 + ~88 kg CO

**Step 2: LOX Tank Routing**
- Pure O2 (LOX) goes to dedicated header tank
- Update dedicated tank item with isolated oxygen inventory

**Step 3: CO Decision Node** (Market-Driven or Config-Based)
```
if craft.has_dedicated_co_tank?
  ├─ STORE: Add CO to dedicated CO tank inventory
  └─ Route: CO tank item metadata['composition'] = { CO: 88kg }
else if market_price(CO) > threshold?
  ├─ STORE: Mix CO back into main volatiles
  └─ Update: main item metadata['composition'] += CO mass
else
  ├─ VENT: Discard CO, reduce total_mass_kg
  └─ Update: Remove CO from composition, subtract mass
```

### New Service/Method Location

**Option A**: `TransitEngine.process_in_flight_chemistry(craft, item)` (called during has_arrived? → false interim state)
**Option B**: New service `AtmosphericProcessingService.crack_co2_in_flight(item, config)`
**Option C**: Direct method on Craft model

**Recommendation**: Option B (new service) — cleaner separation of concerns, testable independently, reusable by other craft types.

## Implementation Details

### Data Structure: In-Flight Processing Config

Each skimmer blueprint should include processing configuration:
```json
{
  "id": "venus_skimmer_v1",
  "operational_data": {
    "in_flight_processing": {
      "co2_cracking_fraction": 0.5,
      "co_routing": "vent_or_store",  // or "store_dedicated", "remix_always"
      "market_co_threshold_usd_per_kg": 50,
      "cracking_energy_per_kg_co2": 3.5  // kWh
    }
  }
}
```

### Service Method Signature

```ruby
class AtmosphericProcessingService
  def self.crack_co2_in_flight(item:, craft:, config:)
    # item: Mixed Volatiles inventory item with metadata['composition']
    # craft: The skimmer performing the processing
    # config: Processing parameters from blueprint operational_data
    
    # Returns: Hash with updated item state + side effects
    {
      updated_item_metadata: { composition: {...}, total_mass_kg: ... },
      lox_tank_contents: { O2: amount_kg },
      co_tank_contents: { CO: amount_kg } || nil,
      vented_mass_kg: amount_vented || 0,
      processing_energy_consumed_kwh: energy_used,
      status: :success
    }
  end
end
```

### Step-by-Step Logic

**1. Extract current state from item metadata**
```ruby
composition = item.metadata['composition'] || {}
total_mass_kg = item.metadata['total_mass_kg'] || 0
co2_available_kg = composition.dig('CO2', 'mass_kg') || 0
```

**2. Calculate cracking yield**
```ruby
co2_to_crack_kg = co2_available_kg * config[:co2_cracking_fraction]
# CO2 → 32/44 O2 + 12/44 CO (stoichiometry by mass)
o2_produced_kg = co2_to_crack_kg * (32.0 / 44.0)
co_produced_kg = co2_to_crack_kg * (12.0 / 44.0)
```

**3. Deduce CO2 in remaining mixture**
```ruby
co2_remaining_kg = co2_available_kg - co2_to_crack_kg
composition['CO2']['mass_kg'] = co2_remaining_kg
```

**4. Route LOX to dedicated tank**
```ruby
lox_tank = craft.tanks.find { |t| t.operational_data['fuel_type'] == 'lox' }
lox_tank.create_or_update_inventory_item(
  name: 'LOX',
  amount: o2_produced_kg,
  metadata: { source: 'in_flight_cracking', extracted_at: Time.current }
)
```

**5. CO decision node**
```ruby
if craft.has_dedicated_co_tank?
  # STORE in dedicated tank
  co_tank = craft.tanks.find { |t| t.operational_data['storage_type'] == 'co' }
  co_tank.create_or_update_inventory_item(
    name: 'CO',
    amount: co_produced_kg,
    metadata: { source: 'in_flight_cracking' }
  )
  co_vented_kg = 0
elsif should_store_co_given_market_conditions?(co_produced_kg, config)
  # REMIX into main volatiles
  composition['CO'] ||= { 'mass_kg' => 0 }
  composition['CO']['mass_kg'] += co_produced_kg
  co_vented_kg = 0
else
  # VENT back to atmosphere
  total_mass_kg -= co_produced_kg
  co_vented_kg = co_produced_kg
end
```

**6. Update main item metadata with final state**
```ruby
item.update!(
  metadata: item.metadata.merge(
    'composition' => composition,
    'total_mass_kg' => total_mass_kg,
    'processing_history' => {
      cracked_co2_kg: co2_to_crack_kg,
      produced_lox_kg: o2_produced_kg,
      produced_co_kg: co_produced_kg,
      vented_co_kg: co_vented_kg,
      processed_at: Time.current
    }
  )
)
```

## Integration Points

- **Called by**: TransitEngine or mission execution during transit (before has_arrived? is true)
- **Or**: Craft arrival handler before docking sequence
- **Upstream**: removeRawGas task (provides initial composition)
- **Upstream**: Craft blueprint operational_data config
- **Output**: 
  - Updated Mixed Volatiles item (changed composition/total_mass_kg)
  - Populated LOX tank item
  - Populated CO tank item (if equipped) OR CO remixed into composition
  - Vented CO returned to Venus atmosphere (optional side effect)

## Testing

**Unit Test Cases:**

1. **Basic CO2 cracking with vent decision**
   - Input: 650 kg CO2 in mixed volatiles
   - Config: crack 50%, no dedicated CO tank, market price below threshold
   - Expected: 
     - O2 → LOX tank (237 kg)
     - CO → vented (88 kg)
     - Remaining composition has 325 kg CO2
     - total_mass_kg reduced by 88 kg

2. **CO2 cracking with dedicated CO tank**
   - Input: same 650 kg CO2
   - Config: crack 50%, has dedicated CO tank
   - Expected:
     - O2 → LOX tank (237 kg)
     - CO → CO tank (88 kg)
     - Remaining composition has 325 kg CO2
     - total_mass_kg unchanged

3. **CO2 cracking with remix decision**
   - Input: same 650 kg CO2
   - Config: crack 50%, no dedicated CO tank, market price ABOVE threshold
   - Expected:
     - O2 → LOX tank (237 kg)
     - CO → remixed into composition['CO'] (88 kg)
     - total_mass_kg unchanged

4. **Mass balance verification**
   - Sum initial composition masses = total_mass_kg ✓
   - After processing: LOX mass + remaining composition + vented = initial total ✓
   - No mass disappears except venting

5. **Edge case: no CO2 in cargo**
   - Input: Mixed Volatiles with only N2/Ar (no CO2)
   - Expected: Method handles gracefully, returns unmodified item

6. **Energy consumption tracking**
   - Verify cracking_energy_kwh calculated: co2_to_crack_kg * config[:energy_per_kg]
   - Verify energy is deducted from craft's power systems

## Files to Create/Modify

1. `app/services/atmospheric_processing_service.rb` — NEW service class with `crack_co2_in_flight` method
2. `spec/services/atmospheric_processing_service_spec.rb` — NEW spec file with 6 test cases above
3. `galaxy_game/app/models/craft/base_craft.rb` — Add tank association helper (`has_dedicated_co_tank?`)
4. Blueprint data files:
   - Update `venus_skimmer_v1_operational_data.json` with `in_flight_processing` config
   - (Or verify if this file already has a placeholder)

## Acceptance Criteria

- [ ] AtmosphericProcessingService implemented with crack_co2_in_flight method
- [ ] All 6 test cases passing
- [ ] Mass balance verified: input mass = LOX + remaining composition + vented (for each test)
- [ ] Item.metadata updated with processing_history for traceability
- [ ] Vent/store/remix decision logic working based on config + market conditions
- [ ] Energy consumption tracked and validated
- [ ] No regressions in existing inventory or craft models
- [ ] Spec includes edge cases (no CO2, zero cracking fraction, etc.)
- [ ] Code includes comments on stoichiometric calculations
- [ ] grep shows zero remaining TODOs

## Dependencies & Blockers

- ✅ No external dependencies
- ⏳ Depends on removeRawGas task (provides composition structure)
- ⏳ Depends on Gas Separator rewrite task (consumes final composition)
- ✅ Can be implemented in parallel if using test fixtures

## Notes

- This method is called mid-transit, NOT at arrival
- Venus atmosphere depletion (venting CO back) is optional — could stub for now
- Market condition check (`should_store_co_given_market_conditions?`) can be a simple method that looks up current CO price vs. threshold
- Could be extended later to support other atmospheric compounds or craft types
- Energy cost is traceable via processing_history metadata
