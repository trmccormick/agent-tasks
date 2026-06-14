---
status: active
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Re-implement Phase 4 Consumption-Aware Ordering in StrategySelector
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-14
**Last Updated**: 2026-06-14

---

## Agent Assignment

**Assigned To**: Local Ollama (Qwen3.5-27B)
**Why This Agent**: Surgical addition to a single service file with confirmed spec coverage
**Supervision Level**: Watched carefully

---

## Context

Phase 4 of the Luna MVP AI Manager implementation was committed as `01919af8` and
documented as complete in status.md. The Return Cargo session (`d458cf88`) overwrote
`strategy_selector.rb`, losing the Phase 4 logic. The specs remain in place and are
the source of truth for what must be re-implemented. No architectural decisions are
needed — the implementation is fully specified by the existing specs and status.md docs.

---

## Problem Statement

`app/services/ai_manager/strategy_selector.rb` is missing Phase 4 logic.
`execute_cost_reduction` currently passes raw deficit quantities to
`ImportRequestGenerator` with no transit buffer and no precursor phase gate.

Four specs fail as a result:
- `strategy_selector_spec.rb:737` — transit buffer not applied to Food order
- `strategy_selector_spec.rb:767` — life-support ordered when population is zero
- `strategy_selector_spec.rb:787` — transit buffer not applied to Water order
- `strategy_selector_spec.rb:802` — returns true instead of false when all
  life-support skipped in precursor phase

**Current behavior**: `execute_cost_reduction` passes `need: deficit` directly,
no population check, no buffer math.

**Expected behavior**: See Implementation Steps below.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/strategy_selector.rb` | AI Manager decision executor | `execute_cost_reduction` ~line 285, top of class for constants |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/services/ai_manager/strategy_selector_spec.rb` | Lines 726–814 are the Phase 4 specs — source of truth |
| `app/services/logistics/logistics_contract.rb` | May contain `EARTH_LUNA_TRANSIT_DAYS` constant |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Add constants at top of StrategySelector class

```ruby
LUNA_MARGIN_FACTOR = 1.25
LIFE_SUPPORT_MATERIALS = %w[Food Water Oxygen Energy].freeze
LUNA_TRANSIT_DAYS = 3
DAILY_CONSUMPTION_RATES = {
  'Food'   => 2.0,
  'Water'  => 1.0,
  'Oxygen' => 3.0,
  'Energy' => 3.0
}.freeze
```

### Step 2 — Add private helper methods

Add these to the private section:

```ruby
def precursor_phase?(settlement)
  settlement.current_population == 0
end

def life_support_material?(material)
  LIFE_SUPPORT_MATERIALS.include?(material)
end

def daily_consumption_rate_for(material, settlement)
  rate = DAILY_CONSUMPTION_RATES[material] || 0.0
  rate * settlement.current_population
end
```

### Step 3 — Update execute_cost_reduction shortage loop

Find the loop that processes `shortage_report[:survival_shortages]`. Currently it
calculates `deficit` and passes it directly. Replace the block inside the loop with:

```ruby
shortage_report[:survival_shortages].each do |shortage|
  deficit = [0, shortage[:need] - shortage[:have]].max
  next if deficit == 0

  material = shortage[:material]

  if life_support_material?(material)
    # Precursor phase: no humans present, skip life-support ordering entirely
    next if precursor_phase?(settlement)

    # Active settlement: add transit buffer for life-support materials
    daily_rate = daily_consumption_rate_for(material, settlement)
    transit_buffer = (LUNA_TRANSIT_DAYS * daily_rate * LUNA_MARGIN_FACTOR).ceil
    order_quantity = deficit + transit_buffer
  else
    order_quantity = deficit
  end

  begin
    import_req = Logistics::ImportRequestGenerator.generate_import_request(
      source: source,
      destination: settlement,
      shortage: { material: material, need: order_quantity }
    )
    success_count += 1 if import_req
    Rails.logger.info "[StrategySelector] Import request created for #{material} (#{order_quantity} units)"
  rescue Logistics::ImportRequestGenerator::ImportRequestError => e
    Rails.logger.warn "[StrategySelector] Failed to create import for #{material}: #{e.message}"
  end
end
```

### Step 4 — Verify targeted specs

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/strategy_selector_spec.rb 2>&1 | tail -20'
```

Expected: all Phase 4 examples pass, full selector spec count preserved with 0 new failures.

Then run the full ai_manager suite:

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -10'
```

Expected: 746 examples, 3 failures (pre-existing flaky only), 4 pending.

### Step 5 — Synthesis report format
SYNTHESIS REPORT
Specs fixed: [list lines]
Expected: > 10 / false
Got before fix: 10 / true
ROOT CAUSE
Phase 4 logic lost during d458cf88 Return Cargo session.
CHANGES

Added 4 constants to StrategySelector class
Added 3 private helpers: precursor_phase?, life_support_material?, daily_consumption_rate_for
Updated execute_cost_reduction loop with buffer math and precursor gate

TEST RESULT
strategy_selector_spec.rb: X examples, 0 failures
ai_manager suite: 746 examples, 3 failures (pre-existing), 4 pending
READY TO APPLY? — waiting for approval

---

## Acceptance Criteria
- [ ] `strategy_selector_spec.rb` Phase 4 examples all pass (lines 737, 767, 787, 802)
- [ ] No regressions in remaining strategy_selector specs
- [ ] ai_manager suite: 746 examples, 3 pre-existing failures only, 4 pending
- [ ] No changes to spec files, factories, or any file outside strategy_selector.rb

---

## Stop Conditions — escalate immediately if:
- Any previously passing spec in strategy_selector_spec.rb now fails
- ai_manager suite failure count exceeds 3
- `current_population` method does not exist on settlement — do not add it, escalate

---

## Commit Instructions
Host only, not inside Docker:
```bash
git add app/services/ai_manager/strategy_selector.rb
git commit -m "bug-fix: strategy_selector — re-implement Phase 4 consumption-aware ordering and precursor phase gate"
git push
```

---

## Dependencies
**Blocked by**: Task 1 (market_price_service_spec syntax fix) — run Task 1 first,
confirm import_request_generator_spec passes, then dispatch this task
**Blocks**: none
**Related tasks**: none

---

## Completion Report
*Filled in by implementing agent*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: strategy_selector.rb | Phase 4 constants + helpers + loop logic added | Run full suite to confirm baseline restored