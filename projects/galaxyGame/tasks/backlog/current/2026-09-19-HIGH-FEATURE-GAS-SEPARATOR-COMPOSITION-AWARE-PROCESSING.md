---
status: backlog
priority: high
type: feature
category: isru
tags: [gas-separator, volatile-composition, material-processing]
created: 2026-09-19
updated: 2026-09-19
---

# Wire Gas Separator to Read Item.metadata Composition

## Problem Statement

The Gas Separator (distillation processing) currently uses a flat global default for volatile composition instead of reading the actual cargo being processed. When a skimmer delivers mixed volatiles with a modified composition (e.g., depleted CO2 from in-flight processing), the separator still uses the same baseline yields regardless of what's actually being separated.

**Current behavior**: `material_processing_service.rb:144` — when output is `'mixed_volatiles'`, it reads `crust_volatiles` (static geosphere data) and distributes into individual compounds. It never looks at the actual Item's metadata composition.

**Desired behavior**: Read the incoming volatile mixture's exact composition from `item.metadata['composition']` and calculate yields based on that real cargo, not a flat assumption.

## Design & Scope

### What Changes

**File**: `app/services/manufacturing/material_processing_service.rb` (around line 140–166)

**Before** (current code):
```ruby
when 'mixed_volatiles'
  crust_volatiles.each do |volatile, percent|
    produced = input_amount * (percent.to_f / 100.0) * geosphere_eff
    @settlement.inventory.add_item(volatile, produced, @settlement, {})
  end
```

**After** (reads composition metadata):
```ruby
when 'mixed_volatiles'
  composition = input_item.metadata['composition'] || {}
  total_mass_kg = input_item.metadata['total_mass_kg'] || input_amount
  
  # If composition exists, use it; otherwise fall back to crust_volatiles
  source_breakdown = composition.empty? ? crust_volatiles : composition
  
  source_breakdown.each do |compound_name, breakdown|
    # breakdown is { 'mass_kg' => N } (new format) or { percentage: N } (fallback)
    if breakdown.is_a?(Hash) && breakdown['mass_kg']
      # New format: mass_kg is stored directly
      base_mass = breakdown['mass_kg'].to_f
    else
      # Fallback: percentage-based (for legacy items without composition)
      percent = breakdown.is_a?(Hash) ? breakdown.dig(:percentage).to_f : breakdown.to_f
      base_mass = total_mass_kg * (percent / 100.0)
    end
    
    # Apply efficiency loss during separation
    produced = base_mass * geosphere_eff
    next if produced <= 0.001
    
    @settlement.inventory.add_item(compound_name, produced, @settlement, {})
  end
```

### Key Logic Changes

1. **Composition source priority**:
   - Check `input_item.metadata['composition']` first (actual cargo)
   - Fall back to `crust_volatiles` (for legacy items or direct regolith processing)
   - If neither, use flat default (safety net)

2. **Mass calculation**:
   - If composition hash exists, use stored `mass_kg` values directly
   - If fallback to percentages, calculate mass from `total_mass_kg` * percent
   - Never mix stored percentages with new mass-first approach

3. **Efficiency application**:
   - Apply `geosphere_eff` loss uniformly across all compounds
   - Produced amount = base_mass * geosphere_eff

### Why This Matters

**Before**: Separator yields are identical regardless of input composition
- Venus skimmer delivers modified mix (depleted CO2, added CO) → still uses default ratios
- TEU + Gas Separator always produces same O2/N2 split even though regolith composition varies per world
- Loss rates are decoupled from actual cargo quality

**After**: Separator yields match real cargo
- Venus mix with 30% CO2 (not 96%) → produces different O2 quantity
- Each world's regolith produces different volatile yields based on `stored_volatiles`
- Mass balance is preserved: input mass = output mass + loss

## Integration Points

- **Upstream**: removeRawGas task (provides composition metadata on Item)
- **Upstream**: In-flight CO2 cracking task (updates composition during transit)
- **Output**: Settlement inventory gains separated compounds (O2, CO2, H2O, N2, etc.)
- **No breaking changes**: Falls back to crust_volatiles for existing items/workflows

## Testing

**Unit Test Cases:**
1. **Item with composition metadata**
   - Create Mixed Volatiles item with { composition: { 'CO2' => { mass_kg: 100 } } }
   - Run separation → verify output includes O2 at proportional amount
   - Verify mass balance: sum of outputs (before efficiency loss) = input mass

2. **Item without composition metadata** (legacy fallback)
   - Create Mixed Volatiles item without metadata
   - Run separation → should use crust_volatiles
   - Verify output matches current behavior (no regression)

3. **Modified composition from Venus skimmer**
   - Simulate skimmer intake: create item with { CO2: 300kg, N2: 50kg, CO: 150kg }
   - Run separation → verify O2 yield reflects 300kg CO2 input (not 965kg default)

4. **Efficiency loss application**
   - Set geosphere_eff = 0.8
   - Input 100kg of specific compound → output should be 80kg

5. **Edge case: empty composition**
   - Create item with empty composition hash
   - Verify fallback to crust_volatiles works

## Files to Modify

1. `app/services/manufacturing/material_processing_service.rb` — Update `'mixed_volatiles'` case (lines ~140–166)
2. `spec/services/manufacturing/material_processing_service_spec.rb` — Add 5 test cases above
   - OR create new test file if not existing

## Acceptance Criteria

- [ ] Gas Separator reads Item.metadata['composition'] when available
- [ ] Falls back to crust_volatiles for legacy items (backward compatible)
- [ ] Mass balance verified: inputs − outputs = efficiency loss
- [ ] Spec tests passing (4/5 main cases + 1 fallback case)
- [ ] No regressions: existing material_processing_service workflows unchanged
- [ ] Code includes comments explaining fallback priority
- [ ] grep shows zero remaining TODOs in implementation

## Dependencies & Blockers

- ✅ No new external dependencies
- ⏳ Depends on removeRawGas task (provides proper composition structure)
- ✅ Can be implemented in parallel if using test fixtures for composition data

## Notes

- This is not a breaking change — code paths for items without composition still work
- The efficiency loss (`geosphere_eff`) stays the same; only the input composition changes
- Enables accurate accounting when skimmers deliver modified cargo from Venus or other worlds
