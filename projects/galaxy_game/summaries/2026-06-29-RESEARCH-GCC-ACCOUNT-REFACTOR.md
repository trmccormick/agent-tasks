# Research: GCC Account Convenience Method Refactor
**Date**: 2026-06-29  
**Agent**: Research Agent (Qwen3.6-27B)  
**Status**: COMPLETE — Awaiting human approval before implementation  

---

## STATUS SYNTHESIS REPORT

### 1. Current `settlement.account` Dependencies

#### The Association Conflict
The codebase has a **duplicate `has_one :account` definition** that creates ambiguity:

| Location | Definition | Purpose |
|----------|------------|---------|
| `SettlementCore` concern (line 18) | `has_one :account, as: :accountable, class_name: 'Financial::Account'` | Settlement-specific |
| `FinancialManagement` concern (line 5) | `has_one :account, as: :accountable, class_name: 'Financial::Account'` | Generic for ALL entities |

Both are included by `BaseSettlement`. The `has_one` returns the **first matching record** from the database, which is NOT guaranteed to be the GCC account when multiple currencies exist.

Additionally, `SettlementCore` defines `has_many :accounts` (line 23), which IS the correct multi-currency association.

#### Fragile `settlement.account` Usage (13 production + spec hits)

**Production Code (4 files, HIGH concern):**

| File | Line(s) | Usage | Currency Assumption |
|------|---------|-------|---------------------|
| `app/services/extraction_service.rb` | 17, 21 | Reads balance, updates balance directly | Implicitly GCC |
| `app/services/ai_manager/operational_manager.rb` | 811, 815 | Calculates debt and settlement funds | Implicitly GCC |
| `app/services/ai_manager/procurement_service.rb` | 90 | Checks settlement funds | Implicitly GCC |
| `app/services/crater_dome_construction_service.rb` | 9 | Derives currency from `settlement.account.currency` | Assumes account exists and is GCC |

**Test Code (5 files, MEDIUM concern):**

| File | Usage Pattern |
|------|---------------|
| `spec/services/crater_dome_construction_service_spec.rb:117` | `unless settlement.account` — creates account if nil |
| `spec/services/manufacturing/construction/dome_service_spec.rb:131` | Same pattern |
| `spec/services/manufacturing/assembly_service_spec.rb:107` | `settlement.account.update!(balance: 1000)` |
| `spec/models/settlement/base_settlement_spec.rb:99,167` | Tests account presence and balance updates |
| `spec/factories/settlement/base_settlement.rb:60` | Factory creates account if missing |

#### Correctly Currency-Aware Patterns (already in codebase)

Several services already use the correct multi-currency pattern:

| File | Pattern Used |
|------|--------------|
| `app/services/market/demand_service.rb:117` | `payer_settlement.accounts.find_by!(currency: gcc)` |
| `app/services/market/trade_execution_service.rb:57` | `Financial::Account.find_or_create_for_entity_and_currency(...)` |
| `app/services/manufacturing/assembly_service.rb:158,251` | `Financial::Account.find_or_create_for_entity_and_currency(...)` |
| `app/jobs/harvester_completion_job.rb:87` | `settlement.gcc_account` (uses convenience method!) |

### 2. Duplicate `gcc_account` Definition Bug

**CRITICAL FINDING**: `gcc_account` is defined in TWO places with different behavior:

| Location | Implementation | Behavior |
|----------|----------------|----------|
| `SettlementCore` concern (line 42) | `accounts.find_by(currency: ...)` | Returns **nil** if GCC account doesn't exist |
| `BaseSettlement` bottom (line 423) | `accounts.find_or_create_by(currency: ...)` | **Creates** GCC account if missing |

The `BaseSettlement` definition shadows the concern definition. This means `OrbitalSettlement` (which also includes `SettlementCore`) gets the nil-returning version, while `BaseSettlement` gets the auto-creating version. **Inconsistent behavior across settlement types.**

### 3. Multi-Currency Infrastructure (Already Exists)

The financial subsystem is FULLY prepared for multiple currencies:

- **`Financial::Currency` model**: Supports any currency (GCC, USD, etc.) with `is_system_currency` flag
- **`Financial::Account` model**: Required `belongs_to :currency`, polymorphic `accountable`
- **`find_or_create_for_entity_and_currency`** class method: The canonical lookup pattern
- **Integration tests**: Already test entities with BOTH GCC and USD accounts (`gcc_mining_sat.rb`, `exchange_rate_integration.rb`)

### 4. Discrepancy Analysis

| Aspect | Current Reality | Project Intent |
|--------|----------------|----------------|
| Account lookup | Fragile `has_one :account` (first match) | Currency-specific lookups |
| Convenience methods | Inconsistent `gcc_account` (2 definitions) | Single source of truth |
| Financial operations | Mixed — some services use `settlement.account`, others use currency-aware lookups | All currency-aware |
| Future expansion | Infrastructure ready, but usage is GCC-implicit | USD and other currencies planned |

**Root Cause**: The `FinancialManagement` concern was designed for a single-currency world. It defines `has_one :account` and delegates `balance`, `charge`, `credit` through that single association. When multi-currency support was added via `has_many :accounts`, the old convenience methods weren't updated.

---

## NEXT STEPS PROPOSAL

### Recommendation: Hybrid Approach — `gcc_account` NOW, `account_for_currency` SOON

I recommend a **two-phase approach** rather than choosing one or the other:

#### Phase 1: Immediate (this task)
1. **Fix duplicate `gcc_account`** — Move to `SettlementCore` concern with `find_or_create_by` behavior (the safer pattern). Remove the duplicate from `BaseSettlement`.
2. **Replace fragile `settlement.account` in production services** — Migrate the 4 production files to use `settlement.gcc_account`:
   - `extraction_service.rb`
   - `operational_manager.rb`
   - `procurement_service.rb`
   - `crater_dome_construction_service.rb`
3. **Update test code** — Migrate specs to use `settlement.gcc_account` where GCC is the intent

#### Phase 2: Near-term (separate task)
1. **Add `account_for_currency(currency_symbol)`** to `SettlementCore`:
   ```ruby
   def account_for_currency(currency_symbol)
     accounts.find_or_create_by(currency: Financial::Currency.find_by(symbol: currency_symbol))
   end
   ```
2. **Refactor `FinancialManagement` concern** — Replace `has_one :account` delegation with currency-aware methods, or deprecate it for settlement entities in favor of the `has_many :accounts` + convenience method pattern.

### Why This Order?

1. **Phase 1 fixes real bugs now** — The duplicate definition and fragile lookups are causing spec instability today
2. **`gcc_account` is the immediate need** — GCC is the primary currency; all current operations assume GCC
3. **Phase 2 enables expansion** — Once `gcc_account` usage is consistent, adding `account_for_currency` is a small incremental change
4. **Lower risk** — Smaller blast radius per commit, easier to validate

### Alternative: Skip to `account_for_currency` Directly

If the human prefers to go straight to the generic method, the implementation is straightforward and Phase 1 replacements would use `settlement.account_for_currency('GCC')` instead of `settlement.gcc_account`. The tradeoff is slightly more verbose call sites for what is currently 100% GCC operations.

---

## FILES REQUIRING CHANGES (Phase 1)

### Model/Concern Changes
- [ ] `app/models/concerns/settlement/settlement_core.rb` — Fix `gcc_account` to use `find_or_create_by`
- [ ] `app/models/settlement/base_settlement.rb` — Remove duplicate `gcc_account` (lines ~420-426)

### Production Service Changes
- [ ] `app/services/extraction_service.rb` — Replace `settlement.account` with `settlement.gcc_account`
- [ ] `app/services/ai_manager/operational_manager.rb` — Replace `settlement.account` with `settlement.gcc_account`
- [ ] `app/services/ai_manager/procurement_service.rb` — Replace `settlement.account` with `settlement.gcc_account`
- [ ] `app/services/crater_dome_construction_service.rb` — Replace `@settlement&.account&.currency` with GCC-aware lookup

### Test Changes
- [ ] `spec/services/extraction_service_spec.rb` — Update mock to use `gcc_account`
- [ ] `spec/services/manufacturing/assembly_service_spec.rb` — Use `gcc_account` where appropriate
- [ ] `spec/models/settlement/base_settlement_spec.rb` — Add spec for `gcc_account` method

---

## STOP AND WAIT

**No files have been modified.** Awaiting human approval on:
1. Recommendation (hybrid two-phase vs. generic-only)
2. Scope of Phase 1 changes listed above
3. Go-ahead to create implementation task file and begin work
