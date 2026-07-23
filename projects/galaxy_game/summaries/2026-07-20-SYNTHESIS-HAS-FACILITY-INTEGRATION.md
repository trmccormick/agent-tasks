# 2026-07-20-SYNTHESIS-HAS-FACILITY-INTEGRATION

## ✅ COMPLETED

### Problem Statement
Two production services used a nonexistent `has_facility?` method guarded by `respond_to?` checks:
- `NpcPriceCalculator.can_produce_locally?` (line 213)
- `ProcurementService.can_produce_locally?` (hardcoded facility types)

Both were effectively disabled, meaning:
- Lunar production pricing always returned "not available"
- Local procurement always returned "can't produce locally"

### Root Cause
Initial task design assumed a "facility" entity that could be checked directly. The actual architecture is:
- **Facilities ARE units** with `output_resources` data
- **Resource names use chemical formulas** (H₂O, O₂, CO2, not "water", "oxygen")
- **Production capability is tied to celestial body location**, not individual units
- `PrecursorCapabilityService` correctly queries celestial body data (geosphere/atmosphere/hydrosphere) for what can be produced locally via ISRU

### Correct Solution
Both services now delegate to `PrecursorCapabilityService`:
1. Get `settlement.location.celestial_body`
2. Create `PrecursorCapabilityService.new(celestial_body)`
3. Call `can_produce_locally?(resource_name)` which queries sphere data
4. Returns true only if celestial body has that resource available for ISRU extraction

### Changes Made

**NpcPriceCalculator (line 201-214)**
- Before: `settlement.respond_to?(:has_facility?) && settlement.has_facility?(facility)`
- After: Delegates to `PrecursorCapabilityService.new(celestial_body).can_produce_locally?(resource_name)`

**ProcurementService (lines 24-35)**
- Before: Hardcoded case statement checking for `atmospheric_processor`, `isru_processor`, `cnt_fabricator`
- After: Delegates to `PrecursorCapabilityService.new(celestial_body).can_produce_locally?(resource_name)`
- Removed: `settlement_has_facility?` stub method (no longer needed)

**Test Updates (npc_price_calculator_spec.rb)**
- Before: Mocked `has_facility?` method on BaseSettlement
- After: Mocks `PrecursorCapabilityService` with correct service interface

### Test Results
✅ **19 examples, 0 failures** in NpcPriceCalculator spec suite

### Architecture Impact
- **Cleaner separation**: Location capabilities (PrecursorCapabilityService) separate from settlement resource management
- **Data-driven**: Capabilities now queried from actual sphere data, not hardcoded
- **Correct naming**: Uses chemical formulas internally (H2O, O2, CO2) as backend standard
- **Future-proof**: If celestial body data changes, capabilities update automatically

### Commit
`3fd0f226` - refactor: replace has_facility? guards with PrecursorCapabilityService delegation

