# ProcurementService.can_produce_locally? — Delegation Fix

## Summary
`ProcurementService.can_produce_locally?` is a broken stub that always returns `true`. It hardcodes 3 facility types (`atmospheric_processor`, `isru_processor`, `cnt_fabricator`) that don't exist in the codebase. The fix is to delegate to `PrecursorCapabilityService`, which already queries celestial body sphere data correctly.

## Root Cause
- `settlement_has_facility?` is a stub returning `true` unconditionally
- Facility types referenced don't exist anywhere in the codebase
- All procurement decisions default to "produce locally" regardless of actual capability

## Fix Strategy
Replace the broken stub with delegation to `PrecursorCapabilityService`:
1. Resolve `settlement → celestial_body` via `settlement.location&.celestial_body`
2. Instantiate `PrecursorCapabilityService` with the celestial body
3. Call `can_produce_locally?(resource)` — resource uses chemical formulas (`'O2'`, `'H2O'`, `'CO2'`)
4. Remove the broken `settlement_has_facility?` stub entirely

## Files to Edit
- `galaxy_game/app/services/ai_manager/procurement_service.rb` — fix `can_produce_locally?`, remove `settlement_has_facility?`

## Reference Files (read-only)
- `galaxy_game/app/services/ai_manager/precursor_capability_service.rb` — delegation target
- `galaxy_game/app/services/ai_manager/mission_planner_service.rb` — call pattern reference
- `galaxy_game/spec/services/ai_manager/procurement_service_spec.rb` — existing specs

## Verification
- Run procurement_service_spec.rb RSpec
- Run precursor_capability_service_spec.rb RSpec (confirm no regressions)

## Risks
- Resource naming: must use chemical formulas, not display names
- Nil location handling: settlement.location&.celestial_body may be nil
- Spec expectations: existing specs may expect the stub behavior
