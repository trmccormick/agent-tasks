---
status: backlog
priority: LOW
type: test-coverage
system_domain: FINANCIAL
mvp_alignment: TEST_RELIABILITY
local_worker_safe: true
created: 2026-08-16
---

# Task: Write Spec for `BaseOrganization#distribute_consortium_profits`

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase09-sol-expansion/2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase09-sol-expansion/2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md \
         projects/galaxy_game/tasks/active/2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.
```

---

## Background

`BaseOrganization#distribute_consortium_profits` (lines 114-130 of `base_organization.rb`) has been implemented but has zero spec coverage. This is a test-only task — no code changes.

The method:
- Returns early if the passed consortium is not a consortium type
- Returns early if net profit (revenue - costs) is ≤ 0
- Creates a `Financial::Transaction` for each active member with their ownership share
- Uses `transaction_type: :transfer` (not a dedicated `:profit_distribution` type)

## Prerequisites

### Factories Available
- `factory :consortium, factory: :corporation` — consortium is an organization_type enum value (2)
- `factory :consortium_membership` — has `ownership_percentage`, `voting_power`, `investment_amount`
- `factory :corporation` — used as member factory

### Model Structure
- `BaseOrganization` has `organization_type` enum: `development_corporation(0), corporation(1), consortium(2), government(3), tax_authority(4), insurance_corporation(5)`
- `consortium?` returns `organization_type == 'consortium'`
- `ConsortiumMembership` has `ownership_percentage` (float, default 10.0)
- `member_relationships.active` — active memberships on the consortium

### Key Code Under Test
```ruby
def distribute_consortium_profits(consortium)
  return unless consortium.consortium?
  revenue = consortium.calculate_revenue
  costs = consortium.calculate_costs
  net_profit = revenue - costs
  return if net_profit <= 0
  consortium.member_relationships.active.each do |membership|
    member_share = net_profit * (membership.ownership_percentage / 100.0)
    Financial::Transaction.create!(
      account: consortium.account,
      recipient: membership.member,
      amount: member_share,
      transaction_type: :transfer,
      description: "Consortium profit share for period",
      currency_id: consortium.account.currency_id
    )
  end
end
```

## Files Involved

### Target (read-only — do not edit)
| File | Purpose |
|------|---------|
| `galaxy_game/app/models/organizations/base_organization.rb` | Method under test (lines 114-130) |
| `galaxy_game/app/models/financial/transaction.rb` | Transaction model for verification |

### New file to create
| File | Purpose |
|------|---------|
| `galaxy_game/spec/models/organizations/base_organization_profit_spec.rb` | Spec for distribute_consortium_profits |

## Implementation Steps

### Step 1 — Create the spec file

Create `galaxy_game/spec/models/organizations/base_organization_profit_spec.rb` with tests covering:

**Early returns:**
- Returns nil/early when passed a non-consortium organization
- Returns early when net profit is 0
- Returns early when net profit is negative
- Does NOT create any Financial::Transaction in early-return cases

**Happy path — single member:**
- Consortium with revenue > costs creates one transaction for the sole member
- Transaction amount equals full net profit (100% ownership)
- Transaction uses `transaction_type: :transfer`
- Transaction description matches "Consortium profit share for period"

**Happy path — multiple members:**
- Multiple members each receive their proportional share
- Sum of all transaction amounts equals net profit (no rounding loss)
- Each transaction has correct recipient, amount, type, and currency_id

**Edge cases:**
- Empty member list — no transactions created, no error
- Zero ownership percentage member — receives 0.0, transaction still created
- Rounding: large profit with odd percentages — verify amounts are reasonable floats

### Step 2 — Run the spec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec galaxy_game/spec/models/organizations/base_organization_profit_spec.rb --format documentation 2>&1'
```

Expected: All examples pass, 0 failures.

### Step 3 — Verify no regressions

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec galaxy_game/spec/models/organizations/ --format progress 2>&1 | tail -5'
```

Expected: All organization specs pass.

## Acceptance Criteria
- [ ] `base_organization_profit_spec.rb` created with comprehensive coverage
- [ ] All spec examples pass (0 failures)
- [ ] Tests cover: early returns (non-consortium, zero profit, negative profit), single member, multiple members, edge cases
- [ ] No regressions in existing organization specs
- [ ] Transaction verification confirms correct type (`:transfer`), amount, recipient, description

## Stop Conditions — escalate to user immediately if:
- `ConsortiumMembership` factory cannot create valid records for testing
- `consortium.account` is nil in test context and cannot be stubbed
- `member_relationships.active` scope has unexpected behavior that blocks testing
- Any existing organization spec fails during this work (regression blocker)

## Commit Instructions

```bash
git add galaxy_game/spec/models/organizations/base_organization_profit_spec.rb
git commit -m 'test: add spec for BaseOrganization#distribute_consortium_profits'
```

**Do NOT push.** This is test-only — no shared code changes. Push after human review confirms no regressions in broader organization specs.

## Dependencies
**Blocked by**: none
**Blocks**: none (test coverage gap, not a blocker)
**Related to**: `2026-05-28-LOW-FEATURE-FINANCIAL-TRANSACTION-ENUM-AND-SPEC.md` (superseded — this task is the remaining actionable piece)
