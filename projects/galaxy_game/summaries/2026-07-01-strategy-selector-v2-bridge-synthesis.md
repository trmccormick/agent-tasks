# STATUS SYNTHESIS REPORT: StrategySelector → TaskExecutionEngineV2 Bridge (V2)

**Task**: `2026-06-28-HIGH-ARCHITECTURE-STRATEGY-SELECTOR-V2-BRIDGE.md`  
**Date**: 2026-07-01  
**Agent**: Implementation Agent

---

## Pre-Check Results

### Pre-check #1: `execute_resource_task` does NOT exist — CONFIRMED ✅
Grep for `execute_resource_task` returned **zero code matches**. Only doc references in handoffs/status.md. This method must be built fresh on `TaskExecutionEngineV2`.

### Pre-check #2: Callers of `ServiceCoordinator.acquire_resource()` — ⚠️ BLOCKER FOUND

**Multiple callers exist beyond StrategySelector.** Decommissioning (deleting) the method is NOT safe without scoping down. Here are all callers found in production code:

| Caller | File | Line | Context |
|--------|------|------|---------|
| `StrategySelector#execute_resource_acquisition` | `strategy_selector.rb` | 238 | **Target of this task** — will be routed away |
| `ServiceOrchestrator#execute_mission` | `service_orchestrator.rb` | 140 | Uses it for resource acquisition during missions |
| `ServiceOrchestrator#execute_mission_with_resource_support` | `service_orchestrator.rb` | 165 | Pre-acquires resources before mission start |
| `ServiceCoordinator#process_resource_requests` | `service_coordinator.rb` | 196 | Self-call for pending resource requests |
| `Manager#acquire_resource` | `manager.rb` | 75-76 | Thin wrapper delegating to service_coordinator |

**STOP CONDITION TRIGGERED**: Pre-check #2 finds other callers. I cannot delete `ServiceCoordinator.acquire_resource()`. The scope must be scoped down to **"stop calling it from StrategySelector only"** — the method remains alive for ServiceOrchestrator, Manager, and internal coordinator use.

### Pre-check #3: `Market::NpcPriceCalculator` — EXISTS ✅

**Two implementations found:**
1. `galaxy_game/app/models/market/npc_price_calculator.rb` (line 3) — **Has `calculate_bid(settlement, resource, demand:)`** — this is the one to use.
2. `galaxy_game/app/services/market/npc_price_calculator.rb` (line 10) — duplicate path, needs investigation.

The model version (`Market::NpcPriceCalculator.calculate_bid`) returns a Float bid price in GCC. It takes:
- `settlement` (BaseSettlement)
- `resource` (String)  
- `demand:` keyword arg (Integer, default 1)

**This is usable for the `request_import` price check.** No class needs to be invented.

### Pre-check #4: Price Threshold — NOT DEFINED IN CODE ⚠️

Grep for `price_threshold|eap|economic_action_point|break_even_price` found **zero live code constants**. Only old doc references to "EAP-COGS" (Earth-Imported Cost of Goods Sold) in archived task files.

**No existing threshold exists.** I propose:
- **Default**: Import only if `NpcPriceCalculator.calculate_bid(settlement, resource)` < **50.0 GCC per unit**. This means local production cost is cheaper than Earth import for resources with base price ≥ 50 GCC.
- **Rationale**: Resources with base_price ≥ 50 (solar_panel=500, fuel_cell=400, battery=300, carbon_fiber=150, ibeam=100) would be produced locally. Only cheap commodities (steel=30, aluminum_alloy=50) might be imported.
- **Flag for Tracy's approval**: This number is arbitrary and should be reviewed against game economy design.

### Pre-check #5: `request_import` is net-new — CONFIRMED ✅

No existing `request_import` case in `TaskExecutionEngineV2#execute_effect`. The current case statement handles: `deploy_unit`, `connect_units`, `construct_structure`, `set_unit_state`, `set_settlement_state`, `check_unit_state`, `transfer_resource`, `manufacture`, `advance_deployment_stages`. A new `"request_import"` case must be added.

---

## Implementation Plan (Adjusted for Pre-check #2 Blocker)

### Step 1: Add `execute_via_task_engine(action, settlement)` to `StrategySelector`
- New public method on `strategy_selector.rb`
- Accepts an action hash and settlement
- Delegates to `TaskExecutionEngineV2.execute_resource_task(...)`
- Replaces the current `execute_resource_acquisition` path

### Step 2: Build `execute_resource_task(settlement, action)` on `TaskExecutionEngineV2`
- New method on `task_execution_engine_v2.rb` (line ~9 class)
- Maps StrategySelector intents to V2 effect actions
- Uses the manifest/task_plan system already in place

### Step 3: Add `"request_import"` case to `execute_effect`
- In `TaskExecutionEngineV2#execute_effect` (line ~243)
- Bridges to logistics/import path
- Uses `Market::NpcPriceCalculator.calculate_bid(settlement, resource)` for price check
- Applies proposed threshold of 50.0 GCC (for approval)

### Step 4: Route StrategySelector → TaskEngineV2 (NO deletion of ServiceCoordinator.acquire_resource)
- **Scope change**: Only stop calling `acquire_resource` from `StrategySelector`
- `ServiceCoordinator.acquire_resource()` remains alive for ServiceOrchestrator, Manager, internal use
- StrategySelector's `execute_resource_acquisition` is replaced by `execute_via_task_engine`

### Files to Touch
1. `galaxy_game/app/services/ai_manager/strategy_selector.rb` — add `execute_via_task_engine`, update routing
2. `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` — add `execute_resource_task`, add `"request_import"` case to `execute_effect`

### Files NOT Touching (per stop condition)
- `service_coordinator.rb` — method stays
- `service_orchestrator.rb` — no changes needed
- `manager.rb` — no changes needed

---

## Risks & Open Questions

1. **Threshold value (50.0 GCC)**: Arbitrary proposal. Needs Tracy's approval before treating as final.
2. **`request_import` logistics path**: No existing central market controller to wire into. Will need to create a fresh bridge — possibly using `ServiceCoordinator.detect_and_request_imports` which already exists (line ~173 of service_coordinator.rb).
3. **Two NpcPriceCalculator files**: `app/models/market/` vs `app/services/market/` — the services one may be a duplicate or variant. Should verify which is the active one before wiring.

---

## Awaiting Approval

**This synthesis gates implementation.** Please confirm:
1. Scope change accepted? (Only stop calling from StrategySelector, don't delete ServiceCoordinator.acquire_resource)
2. Price threshold of 50.0 GCC acceptable as default? Or propose alternative?
3. Any concerns with the two NpcPriceCalculator files?
