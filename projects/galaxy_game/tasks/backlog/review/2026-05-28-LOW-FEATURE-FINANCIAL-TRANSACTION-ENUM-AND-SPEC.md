---
status: backlog
priority: LOW
type: feature
system_domain: CONTROLLERS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

# TASK: Extend Financial::Transaction Enum and Create Consortium Profit Spec

**Status**: BACKLOG
**Priority**: LOW
**Type**: feature
**Created**: 2026-05-28
**Last Updated**: 2026-05-28

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*

- **Template Conformance**: PASS — follows task template structure
- **Docker Wrapper Check**: PASS — RSpec commands properly formatted for container execution
- **MVP Alignment**: VALID — improves spec health and financial transaction architecture
- **MVP Impact Note**: Ensures consortium profit distribution (GCC routing between organizations) is fully tested before integration
- **Action Line**: READY FOR CLOUD HANDOFF — straightforward enum extension + spec writing

---

## Agent Assignment

**Assigned To**: Claude Sonnet 1x
**Why This Agent**: Moderate complexity — needs to understand existing Financial::Transaction model structure, write comprehensive RSpec, and potentially document architecture
**Supervision Level**: Autonomous OK — read-only research phase, then write code once model structure is confirmed

---

## Context

`Financial::Transaction` model exists and is used by `BaseOrganization#distribute_consortium_profits` for recording GCC movements between organizations (consortium profit sharing). However, the model's transaction type enum is incomplete and lacks the specific types needed for consortium operations. Additionally, the profit distribution method itself has no spec, creating a gap in test coverage for a core financial feature.

**Relevant Architecture Docs** — read before starting:
- `galaxy_game/app/models/financial/transaction.rb` — existing model with current enum
- `galaxy_game/app/models/organizations/base_organization.rb` — distribute_consortium_profits method (line ~110)
- `docs/wormhole_expansion/wh-expansion.md` — Consortium governance and GCC routing (if it exists)

---

## Problem Statement

**Current state**:
- `Financial::Transaction` enum only has: `deposit, withdraw, transfer, tax_collection`
- Missing required types: `profit_distribution, transit_fee, maintenance_levy, import_payment, debt_repayment`
- No spec exists for `BaseOrganization#distribute_consortium_profits` method

**Expected behavior**:
- All five transaction types available for consortium operations
- Comprehensive spec for profit distribution covering: revenue calculation, cost calculation, profit sharing per member, transaction creation

**Why it matters**: Consortium profit routing is a core feature in the Wormhole Expansion system. Without complete transaction types and test coverage, the feature is incomplete.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/models/financial/transaction.rb` | Extend enum with 5 new transaction types | `enum transaction_type:` line ~15 |
| `galaxy_game/spec/models/organizations/base_organization_profit_spec.rb` | NEW — comprehensive spec for distribute_consortium_profits | profit distribution tests |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/financial/account.rb` | Account structure for mocking |
| `galaxy_game/spec/factories/organizations.rb` | Factory patterns for orgs |
| `galaxy_game/app/models/consortium.rb` | Consortium model if it exists |

### Migration
- [ ] No migration needed — enum changes do not require DB changes (text column)

---

## Implementation Steps

### Step 1 — Research: Verify current enum structure

```bash
docker exec -it web bash -c 'sed -n "10,30p" galaxy_game/app/models/financial/transaction.rb'
```

Expected: See the current enum with deposit, withdraw, transfer, tax_collection

### Step 2 — Extend Financial::Transaction enum

Current enum:
```ruby
enum transaction_type: { 
  deposit: 'deposit', 
  withdraw: 'withdraw', 
  transfer: 'transfer', 
  tax_collection: 'tax_collection' 
}
```

Add five new types for consortium operations:
```ruby
enum transaction_type: { 
  deposit: 'deposit', 
  withdraw: 'withdraw', 
  transfer: 'transfer', 
  tax_collection: 'tax_collection',
  profit_distribution: 'profit_distribution',
  transit_fee: 'transit_fee',
  maintenance_levy: 'maintenance_levy',
  import_payment: 'import_payment',
  debt_repayment: 'debt_repayment'
}
```

**File**: `galaxy_game/app/models/financial/transaction.rb`

### Step 3 — Create base_organization_profit_spec.rb

Create `galaxy_game/spec/models/organizations/base_organization_profit_spec.rb` with tests for:

**1. Method existence**
- `distribute_consortium_profits` method exists on BaseOrganization

**2. Calculate revenue**
- Method returns 0 when no sales
- Method correctly sums revenue from sales

**3. Calculate costs**
- Method returns 0 when no costs
- Method correctly sums costs

**4. Profit sharing**
- Returns early if net profit is 0 or negative
- Creates Financial::Transaction for each member with correct amount
- Uses correct transaction_type (should be `:profit_distribution`)
- Shares profit according to membership ownership percentage

**5. Edge cases**
- Handles empty member list
- Handles single member (receives all profit)
- Handles multiple members with different percentages

**Example test structure**:
```ruby
RSpec.describe 'BaseOrganization Profit Distribution' do
  let(:consortium) { create(:consortium) }
  let(:org) { create(:base_organization) }
  
  describe '#distribute_consortium_profits' do
    it 'returns early if consortium is not a consortium' do
      non_consortium = create(:base_organization)
      expect(Financial::Transaction).not_to receive(:create!)
      org.distribute_consortium_profits(non_consortium)
    end
    
    it 'returns early if net profit is negative' do
      allow(consortium).to receive(:calculate_revenue).and_return(100)
      allow(consortium).to receive(:calculate_costs).and_return(200)
      expect(Financial::Transaction).not_to receive(:create!)
      org.distribute_consortium_profits(consortium)
    end
    
    it 'creates profit distribution transactions for each member' do
      # setup
      allow(consortium).to receive(:calculate_revenue).and_return(1000)
      allow(consortium).to receive(:calculate_costs).and_return(200)
      net_profit = 800
      
      member1 = create(:base_organization)
      member2 = create(:base_organization)
      
      create(:consortium_membership, consortium: consortium, member: member1, ownership_percentage: 60)
      create(:consortium_membership, consortium: consortium, member: member2, ownership_percentage: 40)
      
      # action
      org.distribute_consortium_profits(consortium)
      
      # verify
      expect(Financial::Transaction.where(transaction_type: :profit_distribution).count).to eq(2)
      # member1 should receive 480 (800 * 0.60)
      # member2 should receive 320 (800 * 0.40)
    end
  end
end
```

### Step 4 — Run profit spec

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec galaxy_game/spec/models/organizations/base_organization_profit_spec.rb -v 2>&1 | tail -30'
```

Expected: X examples, 0 failures

### Step 5 — Verify no regressions

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec galaxy_game/spec/models/financial/transaction_spec.rb galaxy_game/spec/models/organizations/ 2>&1 | tail -20'
```

Expected: All tests pass

---

## Acceptance Criteria
- [ ] `Financial::Transaction` enum includes all 9 transaction types (4 original + 5 new)
- [ ] `base_organization_profit_spec.rb` created with comprehensive coverage
- [ ] All profit distribution tests pass
- [ ] No regressions in Financial::Transaction specs
- [ ] No regressions in Organizations specs
- [ ] `distribute_consortium_profits` method fully tested

---

## Stop Conditions — escalate to user immediately if:
- Consortium model structure is different than expected (no calculate_revenue, calculate_costs, member_relationships)
- Test requires changes to `distribute_consortium_profits` method signature
- Enum extension breaks existing transaction lookups
- Factory structure for consortium/membership is unavailable

---

## Commit Instructions

```bash
git add galaxy_game/app/models/financial/transaction.rb
git add galaxy_game/spec/models/organizations/base_organization_profit_spec.rb
git commit -m 'feature: extend financial transaction enum and add profit distribution spec'
git push
```

---

## Documentation
- [ ] Consider creating `docs/architecture/financial_transaction_system.md` after this task completes — would document:
  - Transaction types and their purposes
  - Consortium profit distribution flow
  - GCC routing between organizations
  - For now: flag as future task, do not create during this task

---

## Dependencies
**Blocked by**: none
**Blocks**: none (improves coverage but not blocking)
**Related tasks**: 
- `2026-03-27-MEDIUM-FEATURE-FINANCIAL-TRANSACTION-MODEL.md` — predecessor (now superseded)
- Consortium dividend implementation (future)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: 
**Completion date**: 
**Final test result**: 

### What was changed
- 

### Issues discovered
- 

### Follow-up tasks needed
- 

### Lessons learned
- 

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [coverage added] | [next action]
