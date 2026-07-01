---
status: active
priority: HIGH
type: refactor
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-06-29
last_updated: 2026-06-29
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-06-29-HIGH-REFACTOR-BASE-SETTLEMENT-GCC-ACCOUNT.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: Refactor Settlement GCC Account — Fix Duplicate Definition + Migrate Fragile Usage

**Status**: ACTIVE  
**Priority**: HIGH  
**Type**: refactor  
**Created**: 2026-06-29  
**Last Updated**: 2026-06-29  

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Guardrails**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
3. **Research Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-06-29-RESEARCH-GCC-ACCOUNT-REFACTOR.md`
4. **This Task File**: Everything below

---

## Context

The financial subsystem supports multiple currencies (GCC, USD, etc.) via `has_many :accounts` on settlements. However, 4 production services still use the fragile `settlement.account` (`has_one`) which returns an arbitrary account — not guaranteed to be GCC. Additionally, `gcc_account` is defined in TWO places with conflicting behavior: `SettlementCore` returns nil if missing, while `BaseSettlement` auto-creates. This task unifies the convenience method and migrates fragile usage.

**Relevant Architecture Docs**:
- `docs/agent/rules/GUARDRAILS.md` — Docker execution rules
- `galaxy_game/app/models/concerns/settlement/settlement_core.rb` — Settlement associations
- `galaxy_game/app/models/concerns/financial_management.rb` — Legacy single-currency concern
- `galaxy_game/app/models/financial/account.rb` — Multi-currency account model

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: Duplicate `gcc_account` with conflicting behavior**
- ❌ Wrong: Leave both definitions — `OrbitalSettlement` gets nil-returning version from concern, `BaseSettlement` gets auto-creating version from its own override
- ✅ Right: Single definition in `SettlementCore` concern using `find_or_create_by` (auto-create pattern). Remove the duplicate from `BaseSettlement`.
- Why: Inconsistent behavior across settlement types causes nil errors in OrbitalSettlement code paths.

⚠️ **GOTCHA 2: `has_one :account` returns arbitrary account, NOT guaranteed GCC**
- ❌ Wrong: Use `settlement.account.balance` — could return USD or any other currency account
- ✅ Right: Use `settlement.gcc_account` for GCC-specific operations
- Why: The `has_one` association returns the first DB match. With multi-currency accounts, this is non-deterministic and fragile.

⚠️ **GOTCHA 3: Do NOT remove `has_one :account` or `FinancialManagement` concern**
- ❌ Wrong: Delete `has_one :account` from concerns — it's used by legacy code and the `FinancialManagement` concern
- ✅ Right: Keep `has_one :account` for backward compatibility. Add `gcc_account` alongside it. Only migrate the 4 identified fragile services.
- Why: Many specs and legacy code depend on `settlement.account`. This is a targeted migration, not a full refactor of `FinancialManagement`.

⚠️ **GOTCHA 4: `extraction_service.rb` updates balance directly (no transaction)**
- ❌ Wrong: Change the direct `update!` pattern — it's intentional for this service
- ✅ Right: Only change `settlement.account` → `settlement.gcc_account`, keep the direct update pattern
- Why: This is a targeted account-reference fix, not a financial transaction refactor.

### Production Services to Migrate (4 files)

| File | Lines | Current Pattern | Target Pattern |
|------|-------|-----------------|----------------|
| `app/services/extraction_service.rb` | 17, 21 | `settlement.account&.balance` / `settlement.account.update!` | `settlement.gcc_account&.balance` / `settlement.gcc_account.update!` |
| `app/services/ai_manager/operational_manager.rb` | 811, 815 | `settlement.account&.balance` | `settlement.gcc_account&.balance` |
| `app/services/ai_manager/procurement_service.rb` | 90 | `settlement.account&.balance` | `settlement.gcc_account&.balance` |
| `app/services/crater_dome_construction_service.rb` | 9 | `@settlement&.account&.currency` | GCC currency lookup |

---

## Problem Statement

**Current behavior**: 
1. `gcc_account` is defined in two places (`SettlementCore` concern and `BaseSettlement` model) with different behavior (nil vs auto-create)
2. 4 production services use `settlement.account` which returns an arbitrary currency account, not guaranteed GCC
3. Specs break when multiple currency accounts exist because `settlement.account` is non-deterministic

**Expected behavior**:
1. Single `gcc_account` method in `SettlementCore` that auto-creates if missing
2. All 4 production services use `settlement.gcc_account` for GCC-specific operations
3. Related specs updated to match

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/app/models/concerns/settlement/settlement_core.rb` | Fix `gcc_account` to use `find_or_create_by` | Line ~42 |
| `galaxy_game/app/models/settlement/base_settlement.rb` | Remove duplicate `gcc_account` | Lines ~420-426 |
| `galaxy_game/app/services/extraction_service.rb` | Migrate to `gcc_account` | Lines 17, 21 |
| `galaxy_game/app/services/ai_manager/operational_manager.rb` | Migrate to `gcc_account` | Lines 811, 815 |
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | Migrate to `gcc_account` | Line 90 |
| `galaxy_game/app/services/crater_dome_construction_service.rb` | Fix currency derivation | Line 9 |

### Spec Files — update to match
| File | Purpose |
|---|---|
| `galaxy_game/spec/services/extraction_service_spec.rb` | Update mock from `account` to `gcc_account` |
| `galaxy_game/spec/models/settlement/base_settlement_spec.rb` | Add spec for unified `gcc_account` behavior |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/financial/account.rb` | Understand `find_or_create_for_entity_and_currency` pattern |
| `galaxy_game/app/models/concerns/financial_management.rb` | Understand legacy `has_one :account` (keep intact) |
| `galaxy_game/spec/factories/settlement/base_settlement.rb` | Factory creates GCC account via `create_account_and_inventory` |

### Migration
- [x] No migration needed — this is a code-only refactor

---

## Implementation Steps

### Step 1 — Fix duplicate `gcc_account` in SettlementCore concern

Change the existing `gcc_account` method in `SettlementCore` from `find_by` (returns nil) to `find_or_create_by` (auto-creates):

```ruby
# BEFORE (settlement_core.rb, line ~42)
def gcc_account
  accounts.find_by(
    currency: Financial::Currency.find_by(symbol: 'GCC')
  )
end

# AFTER
def gcc_account
  accounts.find_or_create_by(
    currency: Financial::Currency.find_by(symbol: 'GCC')
  )
end
```

This single change makes `gcc_account` consistent across ALL settlement types (BaseSettlement AND OrbitalSettlement).

### Step 2 — Remove duplicate `gcc_account` from BaseSettlement model

Remove the duplicate definition at the bottom of `base_settlement.rb` (lines ~420-426):

```ruby
# REMOVE THIS ENTIRE BLOCK from base_settlement.rb:
    # Returns the GCC account for this settlement, creating it if needed
    public
      def gcc_account
        accounts.find_or_create_by(currency: Financial::Currency.find_by(symbol: 'GCC'))
      end
    end
```

After removal, `BaseSettlement` will inherit `gcc_account` from `SettlementCore` (which now has the correct auto-create behavior).

### Step 3 — Migrate `extraction_service.rb` to use `gcc_account`

```ruby
# BEFORE (line 17)
    available_energy = settlement.account&.balance || 0

# AFTER
    available_energy = settlement.gcc_account&.balance || 0

# BEFORE (line 21)
    settlement.account.update!(balance: available_energy - total_energy_cost)

# AFTER
    settlement.gcc_account.update!(balance: available_energy - total_energy_cost)
```

### Step 4 — Migrate `operational_manager.rb` to use `gcc_account`

```ruby
# BEFORE (line 811)
      settlement.account&.balance&.negative? ? settlement.account.balance.abs : 0

# AFTER
      settlement.gcc_account&.balance&.negative? ? settlement.gcc_account.balance.abs : 0

# BEFORE (line 815)
      [settlement.account&.balance || 0, 0].max

# AFTER
      [settlement.gcc_account&.balance || 0, 0].max
```

### Step 5 — Migrate `procurement_service.rb` to use `gcc_account`

```ruby
# BEFORE (line 90)
      [settlement.account&.balance || 0, 0].max

# AFTER
      [settlement.gcc_account&.balance || 0, 0].max
```

### Step 6 — Fix `crater_dome_construction_service.rb` currency derivation

```ruby
# BEFORE (line 9)
    @currency = currency || (@settlement&.account&.currency)

# AFTER
    @currency = currency || Financial::Currency.find_by(symbol: 'GCC')
```

This is more explicit — when no currency is passed, default to GCC rather than deriving from an arbitrary account.

### Step 7 — Update `extraction_service_spec.rb` mock

```ruby
# BEFORE (line ~18)
    allow(settlement).to receive(:account).and_return(account)

# AFTER
    allow(settlement).to receive(:gcc_account).and_return(account)
```

### Step 8 — Add spec for `gcc_account` in base_settlement_spec.rb

Add a test that verifies `gcc_account` returns the GCC account and auto-creates if missing:

```ruby
describe '#gcc_account' do
  it 'returns the settlement GCC account' do
    gcc_currency = Financial::Currency.find_by!(symbol: 'GCC')
    expect(base_settlement.gcc_account.currency).to eq(gcc_currency)
  end

  it 'auto-creates GCC account if missing' do
    # Remove existing GCC account
    Financial::Account.where(accountable: base_settlement, currency_symbol: 'GCC').delete_all
    expect { base_settlement.gcc_account }.to change { base_settlement.accounts.count }.by(1)
  end
end
```

### Step 9 — Verify with targeted specs

Run targeted specs for the affected services:

```bash
✅ PRE-RSPEC EXECUTION CHECK:
- Command: docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/extraction_service_spec.rb'
- Scope: single file
- Targeted: YES
- File path: spec/services/extraction_service_spec.rb
- NOT a full suite: YES
- Ready to execute: YES
```

```bash
✅ PRE-RSPEC EXECUTION CHECK:
- Command: docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/settlement/base_settlement_spec.rb'
- Scope: single file
- Targeted: YES
- File path: spec/models/settlement/base_settlement_spec.rb
- NOT a full suite: YES
- Ready to execute: YES
```

### Step 10 — Synthesis Report (before committing)

Post completion summary to chat with exact diff stats. Wait for approval before commit.

---

## Acceptance Criteria

- [ ] `gcc_account` defined ONCE in `SettlementCore` concern with `find_or_create_by`
- [ ] Duplicate `gcc_account` removed from `BaseSettlement` model
- [ ] `extraction_service.rb` uses `settlement.gcc_account` (2 occurrences)
- [ ] `operational_manager.rb` uses `settlement.gcc_account` (2 occurrences)
- [ ] `procurement_service.rb` uses `settlement.gcc_account` (1 occurrence)
- [ ] `crater_dome_construction_service.rb` defaults to GCC currency explicitly
- [ ] `extraction_service_spec.rb` mock updated to `gcc_account`
- [ ] `base_settlement_spec.rb` has `gcc_account` tests
- [ ] All targeted specs pass (0 failures)
- [ ] `has_one :account` preserved for backward compatibility

---

## Commit Instructions

```bash
# Stage all changed files
git add galaxy_game/app/models/concerns/settlement/settlement_core.rb
git add galaxy_game/app/models/settlement/base_settlement.rb
git add galaxy_game/app/services/extraction_service.rb
git add galaxy_game/app/services/ai_manager/operational_manager.rb
git add galaxy_game/app/services/ai_manager/procurement_service.rb
git add galaxy_game/app/services/crater_dome_construction_service.rb
git add galaxy_game/spec/services/extraction_service_spec.rb
git add galaxy_game/spec/models/settlement/base_settlement_spec.rb

# Commit
git commit -m "refactor: unify gcc_account method and migrate fragile settlement.account usage

- Fix duplicate gcc_account: single definition in SettlementCore with find_or_create_by
- Remove redundant gcc_account from BaseSettlement (inherits from concern)
- Migrate extraction_service, operational_manager, procurement_service to gcc_account
- Fix crater_dome_construction_service currency derivation to default GCC explicitly
- Update extraction_service_spec mock and add gcc_account specs"
```

---

## Out of Scope (Phase 2 — separate task)

- Adding generic `account_for_currency(currency_symbol)` method
- Refactoring `FinancialManagement` concern for multi-currency awareness
- Migrating remaining `settlement.account` usage in test files beyond the 2 identified above
- Adding similar convenience methods to Player model
