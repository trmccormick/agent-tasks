---
status: backlog
priority: high
type: architecture
category: isru
tags: [atmospheric-harvesting, venus-skimmer, volatile-composition]
created: 2026-09-19
updated: 2026-09-19
---

# Proportional Atmospheric Harvesting (`removeRawGas` Service Method)

## Problem Statement

Skimmers harvesting from planetary atmospheres don't cherry-pick individual gas molecules. They bulk-scoop atmosphere, pulling the exact elemental composition as it currently exists at the intake point. The intake needs to extract proportionally based on the world's gas mass ratios, not pull arbitrary amounts.

**Current gap**: No method exists to perform this proportional extraction. Code assumes flat constants or requires per-gas specification rather than a unified bulk intake.

## Design & Scope

### New Service Method: `CelestialBody#removeRawGas(target_mass_kg)`

**Input:**
- `target_mass_kg` (Float): Total atmospheric mass to extract (e.g., 1000 kg)

**Output:**
- Hash containing composition metadata ready to populate `Item.metadata`:
  ```ruby
  {
    'source' => 'venus',
    'composition' => {
      'O2'   => { 'mass_kg' => 2.25 },
      'CO2'  => { 'mass_kg' => 1.50 },
      'H2O'  => { 'mass_kg' => 0.75 },
      'N2'   => { 'mass_kg' => 0.50 }
    },
    'total_mass_kg' => 5.0,
    'extracted_at' => Time.current
  }
  ```

### Implementation Pattern

1. **Calculate mass ratios** from current `body.gases` records
   - Query all gases where `body_id == self.id`
   - Sum total atmospheric mass across all gas.mass values
   
2. **Proportional extraction loop**
   - For each gas record: `extracted_mass = target_mass_kg * (gas.mass / total_atm_mass)`
   - Build composition hash with each compound's exact extracted mass_kg
   - Track cumulative total_mass_kg (should equal sum of components)

3. **Decrement world's atmosphere** using existing depletion pattern
   - Call existing `remove_gas(gas_name, extracted_mass)` for each compound
   - This ensures atmospheric consistency (same pattern as manual gas removal)
   - Recalculates percentages automatically via existing `recalculate_gas_percentages`

4. **Return metadata structure**
   - Ready to pass directly into `item.metadata = returned_hash`
   - No further transformation needed

### Key Constraints

- **Mass-first approach**: Store extracted_mass in kg, never percentages (mirrors Gas model)
- **Reuse existing patterns**: Call `remove_gas` to maintain depletion tracking and recalculation
- **No new columns**: Metadata jsonb handles composition structure entirely
- **Atomic operation**: Calculate all extractions in one pass, commit all removals together (transaction if needed)

## Integration Points

- **Called by**: Venus skimmer intake handler, regolith TEU extraction routines
- **Output fed to**: `Item.create!(name: 'Mixed Volatiles', amount: total_mass_kg, metadata: returned_hash)`
- **Upstream**: Existing `CelestialBody.gases` records with accurate mass values
- **Downstream**: Gas Separator (reads composition metadata), in-flight processing (updates metadata)

## Testing

**Unit Test Cases:**
1. **Venus atmosphere extraction** (965% CO2, 3.5% N2, etc.)
   - Extract 1000 kg → verify ~965 kg CO2, ~35 kg N2 in result
   - Verify body.total_atmospheric_mass decreased by 1000 kg

2. **Small extraction** (below threshold)
   - Extract 0.0001 kg → verify behavior with sub-0.001 rounding

3. **Depletion tracking**
   - Extract 1000 kg twice → second extraction should yield different ratios (atmosphere changed)
   - Verify gases with mass <= 0.001 are destroyed (cleaned up)

4. **Mass balance**
   - Sum all composition['X']['mass_kg'] values = returned['total_mass_kg'] ✓
   - Sum of extractions for all gases = target_mass_kg ✓

## Dependencies & Blockers

- ✅ No new external dependencies
- ✅ Uses existing `remove_gas` and `recalculate_gas_percentages` (in AtmosphereConcern)
- ✅ Assumes CelestialBody has populated `gases` records (seed data responsibility)

## Files to Modify

1. `app/models/celestial_body.rb` — Add `removeRawGas` method
2. `spec/models/celestial_body_spec.rb` — Add 4 test cases above

## Acceptance Criteria

- [ ] Method implemented and tested (4 unit test cases passing)
- [ ] Returns metadata hash with exact mass breakdown per gas
- [ ] Decrements world's atmosphere via existing `remove_gas` calls
- [ ] Rounding errors handled (sub-0.001 kg = destroy gas record)
- [ ] Spec validates mass balance: sum of extracted masses = total_mass_kg
- [ ] grep shows zero remaining TODOs or FIXME in implementation

## Notes

- This is the foundation for Venus skimmer intake and TEU regolith processing
- Must be complete before in-flight CO2 cracking or Gas Separator rewiring (dependent tasks)
- Mirrors existing `add_gas` pattern (mass primary, percentage derived)
