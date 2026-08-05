# 2026-08-04 BUGFIX InfrastructureCostCalculator Dead Code Removal

## Synthesis

### Current State
- `InfrastructureCostCalculator` at `galaxy_game/app/services/economics/infrastructure_cost_calculator.rb` has **zero live callers** in app/lib/config
- Only consumer is `galaxy_game/test_realistic_costs.rb` (standalone test script, not RSpec)
- Signature fix was applied previously: `can_produce?(destination, ...)` → `can_produce_locally?(material.chemical_formula)`
- NEEDS_REVIEW.md entry is RESOLVED
- DOCUMENT_INVENTORY.md lists it in the inventory table

### Decision: REMOVE

**Rationale:**
1. Zero live callers in the actual application — nothing depends on this class
2. No spec coverage — the signature bug went undetected because of this
3. The test script is a one-off from 2026-01-31, never integrated into RSpec
4. Keeping dead code creates future maintenance burden and false confidence
5. If needed later, git history preserves the class for reference

### References
- Task: `2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md`
- Class: `galaxy_game/app/services/economics/infrastructure_cost_calculator.rb`
- Test script: `galaxy_game/test_realistic_costs.rb` (also remove — no longer exercises anything live)
- NEEDS_REVIEW.md entry: RESOLVED (signature fix confirmed)
- DOCUMENT_INVENTORY.md: will be updated to remove the entry

### Files to Remove
1. `galaxy_game/app/services/economics/infrastructure_cost_calculator.rb`
2. `galaxy_game/test_realistic_costs.rb` (only consumer of the class, no other value)

### Files to Update
1. `docs/wiki_reorganization/inventory/DOCUMENT_INVENTORY.md` — remove InfrastructureCostCalculator entry from Economics Services table
