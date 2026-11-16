# Phase C: Implementation Notes — Request Import with Real Pricing

**Date**: 2026-07-01  
**Status**: ✅ Complete — All tests passing (58 examples, 0 failures)

---

## What Was Implemented

### 1. Removed `IMPORT_PRICE_THRESHOLD` Constant
- Deleted the magic `50.0 GCC` threshold that was blocking all real imports
- Removed all 5 references across `execute_resource_task`, `request_import`, and `request_import_from_effect`
- Replaced with nil/zero price validation: `if bid_price.nil? || bid_price.zero?`

### 2. Implemented Real Pricing Chain in `request_import`
```ruby
# request_import now:
1. Calls Market::NpcPriceCalculator.calculate_bid(settlement, resource_name)
   → This delegates to the service-based NpcPriceCalculator which:
     a. Checks can_produce_locally? (material JSON pricing.lunar_production.available)
     b. If local production available → calculate_local_production_cost
     c. Otherwise → calculate_earth_import_cost via Tier1PriceModeler
        → material.cost_data.purchase_cost.amount + transport cost
2. Validates price is non-nil and non-zero
3. Logs the import price for observability
4. Delegates to ServiceCoordinator.acquire_resource(resource, quantity, settlement)
```

### 3. Updated `request_import_from_effect` Similarly
- Same pricing chain as `request_import`
- Uses effect's `quantity` field (default 100) instead of hardcoded value
- Also delegates to `ServiceCoordinator.acquire_resource`

### 4. Made `request_import` Public
- Added `public` keyword before the method definition
- Was previously private, causing `NoMethodError` when called from `execute_resource_task`

### 5. Fixed ServiceCoordinator Delegation
- Changed from broken `AIManager::ServiceCoordinator.detect_and_request_imports(settlement)` (class method that doesn't exist)
- To proper instance creation: `coordinator = AIManager::ServiceCoordinator.new(@shared_context); coordinator.acquire_resource(...)`

---

## Files Modified

| File | Changes |
|------|---------|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Removed IMPORT_PRICE_THRESHOLD, implemented real pricing chain in request_import + request_import_from_effect, made request_import public, fixed ServiceCoordinator delegation |
| `galaxy_game/spec/services/ai_manager/strategy_selector_spec.rb` (line 108-125) | Changed resources from steel/titanium to electronics/nitrogen, added NpcPriceCalculator stubs, added ServiceCoordinator.new stub |
| `galaxy_game/spec/services/ai_manager/task_execution_engine_v2_spec.rb` | **Created new** — 11 targeted tests for execute_resource_task and request_import/request_import_from_effect |

---

## Test Results

### strategy_selector_spec.rb: 47 examples, 0 failures ✅
### task_execution_engine_v2_spec.rb: 11 examples, 0 failures ✅
### Combined: 58 examples, 0 failures ✅

---

## Key Design Decisions

1. **No magic threshold** — pricing is entirely data-driven from material blueprints via `NpcPriceCalculator`
2. **`request_import` is the single source of truth** for import pricing validation
3. **ServiceCoordinator.new stub in tests** was necessary because the engine creates its own coordinator internally (with nil @shared_context), not the one injected into StrategySelector
4. **Model-based NpcPriceCalculator (`app/models/market/npc_price_calculator.rb`) is likely dead code** — the service-based version (`app/services/market/npc_price_calculator.rb`) is authoritative with full pricing infrastructure

---

## Deferred Work (Noted in Code)

```ruby
# PriceHistory location awareness deferred — requires settlement_id migration on market_price_histories
```

The `Market::PriceHistory` model has no `settlement_id` column, so location-aware pricing cannot be implemented yet. This is a future migration task.

---

## Awaiting APPROVAL before git commit
