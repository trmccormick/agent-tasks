# Phase C: Request Import Synthesis Report

**Date**: 2026-07-01  
**Role**: Implementation Agent  
**Task**: Implement `request_import` in TaskExecutionEngineV2 using real pricing data  
**Status**: Awaiting approval before implementation

---

## 1. Current State of `request_import` Stub

**File**: `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` (lines 240–320)

The current implementation has **two critical problems**:

### Problem A — Magic threshold blocks real imports:
```ruby
IMPORT_PRICE_THRESHOLD = 50.0  # Line 245 — hardcoded GCC value, no basis
```
Both `execute_resource_task` (line 268) and `request_import_from_effect` (line 307) check `bid_price > IMPORT_PRICE_THRESHOLD`. This means any resource with a bid price above 50 GCC gets silently skipped. The model-based `NpcPriceCalculator` has titanium at base 200.0 — it would never be acquired.

### Problem B — `request_import` ignores the resource being requested:
```ruby
def request_import(settlement, resource_name)
  # ...logs resource_name...
  result = AIManager::ServiceCoordinator.detect_and_request_imports(settlement)
  # ^^^ Does NOT pass resource_name or quantity — just detects generic shortages
end
```
The method receives `resource_name` but never uses it. It delegates to `detect_and_request_imports` which does a blanket shortage scan, not a targeted import request.

### Current test failure (line 113 of `strategy_selector_spec.rb`):
- **Test expects**: `service_coordinator.acquire_resource('steel', 100, settlement)` × 2
- **Actual path**: `execute_action` → `execute_resource_task` → `NpcPriceCalculator.calculate_bid` → threshold check → `request_import` (which doesn't call `acquire_resource`)
- **Result**: `false` (test fails because the stubbed expectation is never met)

---

## 2. Which NpcPriceCalculator File Is Active?

**Two files exist with the same class name `Market::NpcPriceCalculator`:**

| File | Status | Characteristics |
|------|--------|-----------------|
| `app/services/market/npc_price_calculator.rb` | **Full implementation** (~340 lines) | Cost-based + market-based pricing, uses `Tier1PriceModeler`, calls `load_material_data` via `MaterialGeneratorService.generate_material`, has `calculate_import_cost`, `can_produce_locally?`, etc. |
| `app/models/market/npc_price_calculator.rb` | **Stub** (~60 lines) | Hardcoded base prices (steel=30, titanium=200, ibeam=100, etc.), simple demand/supply multipliers |

**Rails/Zeitwerk behavior**: Both define `Market::NpcPriceCalculator`. The model file is loaded first (traditional location), but the service file may override it depending on autoload order. **This is a conflict that should be resolved** — likely the model stub is dead code or the service file is the intended implementation. For this task, I'll assume the **service-based version** is authoritative since it has the full pricing infrastructure.

---

## 3. Implementation Plan for Pricing Chain

### Step 1: Remove `IMPORT_PRICE_THRESHOLD` entirely
Remove all 5 references across lines 245, 268-269, 307-308.

### Step 2: Implement real pricing chain in `request_import`:
```
Primary EAP = material.cost_data.purchase_cost.amount (USD)
            + (mass_per_unit_kg × GameConstants::INITIAL_TRANSPORTATION_COST_PER_KG)

Fallback 1 = unit.base_cost_eap (from operational_data, 75 files populated)

Fallback 2 = empty_mass_kg × GameConstants::INITIAL_TRANSPORTATION_COST_PER_KG
```

### Step 3: Add location awareness comment:
```ruby
# PriceHistory location awareness deferred — requires settlement_id migration on market_price_histories
```

### Step 4: Update `request_import_from_effect` similarly
Remove threshold, use pricing chain.

### Step 5: Update `execute_resource_task`
Remove the threshold check entirely; let `request_import` handle price validation with real data.

---

## 4. Files I Will Touch

| File | Action |
|------|--------|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Remove `IMPORT_PRICE_THRESHOLD`, implement pricing chain in `request_import` + `request_import_from_effect`, update `execute_resource_task` |
| `galaxy_game/spec/services/ai_manager/strategy_selector_spec.rb` (line 113) | Replace steel/titanium with realistic Luna import resources, stub `NpcPriceCalculator.calculate_bid` appropriately |
| `galaxy_game/spec/services/ai_manager/task_execution_engine_v2_spec.rb` | **Create new** — targeted tests for `execute_resource_task` and `request_import` |

---

## Key Design Decisions

1. **No more magic 50 GCC threshold** — pricing is now data-driven from material blueprints
2. **`request_import` becomes the single source of truth** for import pricing, not `execute_resource_task`
3. **Material lookup uses existing infrastructure** — `load_material_data` → `MaterialGeneratorService.generate_material` → JSON data with `cost_data.purchase_cost.amount`
4. **Model-based NpcPriceCalculator is likely dead code** — the service-based version has the real implementation. Leave both files as-is for now (not in scope) but note this for future cleanup.

---

## Prerequisites Confirmed by Research

- Material JSON files have `cost_data.purchase_cost.amount` (USD) — 95% populated
- Blueprint JSON files have `empty_mass_kg` — 90% populated  
- Unit operational_data files have `base_cost_eap` — 75 files populated
- `GameConstants::INITIAL_TRANSPORTATION_COST_PER_KG = 1320.00` exists in config
- `Market::PriceHistory` has NO location reference — do not use it yet

---

**Awaiting approval to proceed with implementation.**
