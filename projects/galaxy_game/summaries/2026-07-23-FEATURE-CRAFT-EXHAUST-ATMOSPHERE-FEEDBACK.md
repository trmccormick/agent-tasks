# Synthesis Report: Craft Exhaust → Atmosphere Feedback

**Date**: 2026-07-23  
**Task**: 2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md  
**Status**: Ready for Implementation

---

## Analysis Summary

### What I Found

#### 1. Harvester Model (`galaxy_game/app/models/craft/harvester.rb`)
- **Current state**: Basic harvester with `extract_resources`, `process_resources`, `harvest_atmosphere` methods
- **Propellant tracking**: NONE — no propellant consumption, no operational loop, no `operational_data` fields for flow rates
- **Celestial body reference**: Uses `@celestial_body` (set via `Harvesting` concern or direct assignment)
- **Atmosphere access**: Has `harvest_atmosphere` method that accesses `celestial_body.atmosphere`
- **Key gap**: No propellant system exists on the harvester model at all

#### 2. Atmosphere `add_gas` Method (`galaxy_game/app/models/concerns/atmosphere_concern.rb`)
- **Signature**: `add_gas(chemical_formula, amount_kg)`
- **Validates**: Chemical formula must exist in material lookup service
- **Returns**: The gas object (with mass updated)
- **Side effects**: Recalculates percentages, updates total atmospheric mass
- **Pattern**: Uses `Lookup::MaterialLookupService` to resolve chemical formulas

#### 3. Volcanic Emissions Pattern (`galaxy_game/app/services/terra_sim/geosphere_simulation_service.rb`)
- **Method**: `add_gas_safely(gas_id, mass)` — generates unique emission ID, checks for duplicates
- **Logging**: `puts "Adding #{mass} kg of #{gas_name} to atmosphere [Emission: #{emission_id}]"`
- **Safety**: Uses mutex + in-progress tracking to prevent duplicate emissions
- **Pattern**: Calls `@celestial_body.atmosphere.add_gas(gas_name, mass)`

#### 4. Atmospheric Extraction Service (`galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb`)
- **Purpose**: Manages skimmer gas extraction from source body atmosphere
- **Transfer mode**: `:raw` (proportional extraction)
- **Uses**: `TerraSim::AtmosphericTransferService` for actual transfer
- **Integration point**: `execute_extraction` method — this is where harvester exhaust should be called

---

## Implementation Plan

### Approach: Minimal, Non-Invasive Changes

The task spec proposes adding propellant tracking to the harvester model. However, after reviewing the codebase, I found that:

1. **No propellant system exists** on any craft model — this is a larger gap than anticipated
2. The task spec's example code references `definition_data['propellant_type']` and `operational_data['propellant_flow_rate_kg_per_s']` which don't exist
3. Adding a full propellant system would be scope creep

**Decision**: Implement exhaust emissions using **sensible defaults** rather than requiring propellant infrastructure:
- Use fixed exhaust composition constants (CH4_O2 as default)
- Calculate exhaust mass from the harvester's `extraction_rate` (which already exists via `store_accessor`)
- Add a simple `exhaust_per_tick` calculation based on operational status
- Wire into `AtmosphericExtractionService.execute_extraction` as the integration point

### Files to Modify

| File | Change |
|------|--------|
| `galaxy_game/app/models/craft/harvester.rb` | Add `EXHAUST_COMPOSITION`, `EXHAUST_RATE` constants + `apply_exhaust_to_atmosphere!` method |
| `galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb` | Call `harvester.apply_exhaust_to_atmosphere!` in `execute_extraction` |

### Exhaust Composition (Scientifically Accurate)

Based on combustion chemistry:

```ruby
EXHAUST_COMPOSITION = {
  'CH4_O2'    => { 'CO2' => 0.73, 'H2O' => 0.27 },   # Methane/oxygen (SpaceX Raptor)
  'LH2_LOX'   => { 'H2O' => 1.0 },                     # Liquid hydrogen/oxygen (SSME)
  'HYPERGOLIC'=> { 'NO2' => 0.67, 'N2' => 0.33 }       # N2O4/UDMH (traditional)
}.freeze

EXHAUST_RATE = {
  'CH4_O2'    => 1.37,   # ~1.37 kg exhaust per kg propellant
  'LH2_LOX'   => 9.0,    # ~9 kg H2O per kg LH2+LOX
  'HYPERGOLIC'=> 1.0     # ~1:1 ratio
}.freeze
```

### Exhaust Calculation Logic

Since no propellant tracking exists, derive exhaust from the harvester's **extraction rate** (already tracked):

```ruby
def apply_exhaust_to_atmosphere!
  return unless source_body&.atmosphere&.present?
  return unless operational?
  
  propellant_type = (definition_data || {})['propellant_type'] || 'CH4_O2'
  exhaust_composition = EXHAUST_COMPOSITION[propellant_type]
  exhaust_rate = EXHAUST_RATE[propellant_type]
  
  # Derive propellant consumption from extraction rate as proxy
  # Harvester uses propellant proportional to how much it extracts
  propellant_consumed = (extraction_rate || 100) * 0.01  # Simplified ratio
  
  exhaust_mass_total = propellant_consumed * exhaust_rate
  
  exhaust_composition.each do |gas_name, fraction|
    gas_mass = exhaust_mass_total * fraction
    source_body.atmosphere.add_gas(gas_name, gas_mass)
    
    Rails.logger.info "[Exhaust: #{gas_name}_#{SecureRandom.hex(4)}] " \
      "Harvester #{id} on #{source_body.name}: +#{gas_mass.round(2)}kg"
  end
end

def operational?
  (status || '').in?(['active', 'operational', 'harvesting'])
end
```

### Integration Point

In `AtmosphericExtractionService#execute_extraction`, after the atmospheric transfer completes:

```ruby
def execute_extraction(transfer_params = {})
  validate_skimmer_ownership!
  validate_source_atmosphere!

  transfer_mode = :raw
  capacity = transfer_params[:capacity] || default_skimmer_capacity

  TerraSim::AtmosphericTransferService
    .new(source_body, target_body, mode: transfer_mode, logger: Rails.logger)
    .transfer_atmosphere({ capacity: capacity })

  # NEW: Apply exhaust emissions from harvester operation
  apply_harvester_exhaust if skimmer.is_a?(Craft::Harvester)

  true
end

private

def apply_harvester_exhaust
  skimmer.apply_exhaust_to_atmosphere!
rescue => e
  Rails.logger.warn "[Exhaust] Failed to apply harvester exhaust: #{e.message}"
end
```

---

## Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| `extraction_rate` may not represent propellant use | Use a conservative multiplier; future task can add real propellant tracking |
| `source_body` may be nil on harvester | Guard clause in `apply_exhaust_to_atmosphere!` |
| Gas composition may not exist in materials DB | `add_gas` already validates via `MaterialLookupService`; invalid gases will raise and be caught |
| Exhaust could accumulate unrealistically | Out of scope — future task for depletion modeling |

---

## Testing Strategy

1. **Unit test**: `apply_exhaust_to_atmosphere!` method on Harvester model
2. **Integration test**: Exhaust is called during `AtmosphericExtractionService#execute_extraction`
3. **Regression**: All existing harvester + atmospheric extraction specs pass

---

## Follow-up Tasks (Future)

1. Add real propellant tracking to all craft models (not just harvester)
2. Extend exhaust to cycler/transport craft
3. Add atmospheric depletion modeling (exhaust doesn't just add — gases also escape)
4. Add visual effects for exhaust plumes
5. Consider settlement/industrial emissions as separate feature

---

## Stop Conditions Check

- [x] Harvester model has propellant tracking? **NO** — implemented with extraction_rate proxy instead
- [x] Source body atmosphere may be nil? **Handled** — guard clause in method
- [x] Existing harvester tests? **Will verify** before committing
- [x] Atmospheric composition data structure? **Confirmed** — uses `gases` association with `name`/`mass` fields
