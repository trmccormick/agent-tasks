# Synthesis Report: Retroactive Review of Commit 7db7566c (market-fee-hold)

**Commit:** `7db7566c` — feat: per-location market fee management for AI Manager  
**Branch:** `market-fee-hold` (unpushed)  
**Review date:** 2026-08-16  
**Type:** Retroactive Synthesis Report (required before push approval — touches shared code)

---

## What the Commit Changed

### Files Modified (6 files, 3 additions + 3 modifications):

| File | Type | Change |
|------|------|--------|
| `galaxy_game/app/models/concerns/settlement_fees.rb` | **NEW** | Concern with broker_fee, transaction_fee, order_duration accessors; fees stored in `operational_data['fees']` jsonb |
| `galaxy_game/app/models/settlement/base_settlement.rb` | Modified | Added `include SettlementFees` |
| `galaxy_game/app/models/settlement/orbital_settlement.rb` | Modified | Added `include SettlementFees` (in this commit) |
| `galaxy_game/app/services/ai_manager/logistics_coordinator.rb` | Modified | Added `set_location_fees`, `get_location_fees`, `apply_default_fees` methods |
| `galaxy_game/app/services/ai_manager/universal_docking_service.rb` | Modified | Added `calculate_docking_fees`, `process_docking_fees` methods |
| `spec/services/ai_manager/per_location_fees_spec.rb` | **NEW** | 30 tests, 0 failures |

### Additional files changed (since this commit, NOT part of it):
The earlier diff comparing 7db7566c→HEAD showed 22 files — those are changes made AFTER the market-fee commit, not part of it. Only the 6 files above were touched by 7db7566c itself.

---

## Shared Code Risk Assessment

### CRITICAL: OrbitalSettlement SettlementFees Reverted

**Finding:** `orbital_settlement.rb` had `include SettlementFees` added in commit 7db7566c, but a later commit removed it. Current state:

```ruby
# galaxy_game/app/models/settlement/orbital_settlement.rb (current)
class OrbitalSettlement < BaseSettlement
  has_many :orbital_construction_projects, foreign_key: 'station_id'
  self.table_name = 'base_settlements'
  include SettlementCore
  # NOTE: SettlementFees is NOT included here
```

**Impact:** Any code path that calls fee methods on an OrbitalSettlement will raise NoMethodError:
- `LogisticsCoordinator#set_location_fees` → `settlement.broker_fee_type = ...` — **will crash**
- `LogisticsCoordinator#get_location_fees` → `settlement.broker_fee_type` — **will crash**  
- `LogisticsCoordinator#apply_default_fees` → `settlement.apply_default_fees!` — **will crash**
- `UniversalDockingService#calculate_docking_fees` → `destination.calculate_broker_fee` — guarded by `respond_to?`, will return `{broker_fee: 0.0, ...}` silently
- `UniversalDockingService#process_docking_fees` → same guard, silent zero

**Risk level:** HIGH — this is a silent failure path for orbital settlements in the AI Manager fee system.

### BaseSettlement SettlementFees — Still Included ✓

```ruby
# galaxy_game/app/models/settlement/base_settlement.rb (current)
include SettlementFees  # still present, not reverted
```

**Impact:** All non-orbital settlements (colony, outpost, etc.) continue to have fee methods. This is correct.

### SettlementFees Concern — Intact ✓

The concern itself (`settlement_fees.rb`) was added in the commit and has NOT been modified since. It provides:
- `broker_fee_type`, `broker_fee_value` accessors
- `transaction_fee_type`, `transaction_fee_value` accessors  
- `order_duration_min`, `order_duration_max` accessors
- `calculate_broker_fee(amount)`, `calculate_transaction_fee(amount)` methods
- `apply_default_fees!` method

### LogisticsCoordinator Fee Methods — Intact ✓

Three new methods added in the commit, not modified since:
- `set_location_fees(settlement, fee_config)` — validates types, applies to settlement
- `get_location_fees(settlement)` — returns current config hash
- `apply_default_fees(settlement)` — calls `settlement.apply_default_fees!`

**Note:** All three assume the settlement responds to fee methods. No guard against OrbitalSettlement.

### UniversalDockingService Fee Methods — Intact ✓

Two new methods added in the commit, not modified since:
- `calculate_docking_fees(transaction_amount, destination)` — guarded by `respond_to?`
- `process_docking_fees(transaction_amount, destination, sender)` — uses calculated fees

**Note:** The `respond_to?` guard prevents crashes on OrbitalSettlement but silently returns zero fees. This is a silent degradation, not a crash.

---

## Cross-Task Dependency Analysis

### Affected by has_storage.rb change (post-market-fee):
The post-commit change to `has_storage.rb` (`find_storage_unit_for` now handles `nil` storage_type) does NOT interact with the fee system. No dependency risk.

### Affected by BaseUnit#storage_type change (post-market-fee):
The post-commit change to `base_unit.rb` (`operational_data['subcategory']` instead of nested path) does NOT interact with the fee system. No dependency risk.

### SettlementFees concern is a NEW shared concern:
It was introduced in this commit and has no prior task assumptions built on top of it. The only assumption is that all settlements include it — which OrbitalSettlement currently violates.

---

## Recommendations

### Must Fix Before Push:
1. **Restore `include SettlementFees` to OrbitalSettlement** OR explicitly document why orbital settlements should NOT have fee methods and add guards in LogisticsCoordinator/UniversalDockingService.
   - If restoring: any code that removed it was incorrect — needs investigation of why it was removed.
   - If documenting: add explicit `respond_to?` guards or raise a clear error rather than silent zero.

### Should Address:
2. **LogisticsCoordinator fee methods should validate settlement type** before calling fee methods, or rescue NoMethodError with a clear log message.
3. **Consider whether orbital settlements should have fees at all** — they're a constellation, not a physical location. If the answer is "no," then the current design (adding SettlementFees to BaseSettlement and removing from OrbitalSettlement) is correct, but the fee methods in LogisticsCoordinator/UniversalDockingService need explicit guards.

### Optional:
4. **Add spec coverage for OrbitalSettlement fee behavior** — currently only 30 tests on per_location_fees_spec.rb, likely all using BaseSettlement subclasses. Need at least one test confirming orbital settlements do or don't have fees.

---

## Sign-off

**Reviewer:** Planning Agent (Qwen)  
**Date:** 2026-08-16  
**Verdict:** APPROVED with conditions — fix OrbitalSettlement SettlementFees issue before push. The core design is sound; the shared concern inclusion gap is a bug that needs resolution, not a design flaw.

---

## Files Changed Summary (7db7566c only)

```
 galaxy_game/app/models/concerns/settlement_fees.rb              |  new file
 galaxy_game/app/models/settlement/base_settlement.rb            | +4
 galaxy_game/app/models/settlement/orbital_settlement.rb         | +1 (later reverted)
 galaxy_game/app/services/ai_manager/logistics_coordinator.rb    | +40
 galaxy_game/app/services/ai_manager/universal_docking_service.rb| +37
 galaxy_game/spec/services/ai_manager/per_location_fees_spec.rb  | new file (245 lines)
```
