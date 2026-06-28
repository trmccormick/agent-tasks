---
status: COMPLETED
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
codebase_status: "Research Required — Resource allocation logic currently lacks integration with TaskExecutionEngineV2."
---

# TASK: Research & Integrate Resource Allocation with EscalationHandler

## Context
The previous AI Manager task `2026-02-11-HIGH-AI-MANAGER-RESOURCE-ALLOCATION` is obsolete. It assumes manual, imperative allocation logic. Current architecture mandates that all high-level strategic decisions flow through `StrategySelector` and utilize the `EscalationHandler` for constraint resolution.

## Goal
Research and propose a "Task Contract" between the AI Manager's resource requirements and the `TaskExecutionEngineV2`.

## Research Requirements
1. **Integration Mapping**: Determine how `StrategySelector` can trigger resource requests (e.g., nitrogen, steel) without bypassing the existing `TaskExecutionEngineV2` workflow.
2. **Escalation Trigger**: Define when the AI Manager should hand off a resource shortage to the `EscalationHandler` (e.g., if local supply is 0 and market price exceeds threshold).
3. **Data Flow**: Identify the source of truth for current material stocks available to the AI Manager.

## Verification Steps
- [ ] Review `StrategySelector` to identify current hooks for `:resource_acquisition`.
- [ ] Audit `EscalationHandler` to ensure it can accept `ResourceAllocationTask` types.
- [ ] Determine if the AI Manager needs a new `AllocationAnalyzer` or if current `StateAnalyzer` data is sufficient.

## Completion Notes (Updated 2026-06-28)

### STATUS SYNTHESIS REPORT

**Agent**: Research/Architecture Agent (qwen3.6-27b-copilot-ryzen)
**Task**: Research & Integrate Resource Allocation with EscalationHandler
**Date**: 2026-06-28

---

#### 1. Current Architecture Baseline (Verified via Docker Inspection)

| Component | File | Status | Key Findings |
|---|---|---|---|
| **StrategySelector** | `strategy_selector.rb` | Active | Main decision loop. Has `execute_action` with cases for `:resource_acquisition`, `:cost_reduction`, etc. NO integration with TaskExecutionEngineV2 — they are parallel systems. |
| **TaskExecutionEngineV2** | `task_execution_engine_v2.rb` (619 lines) | Active | Data-driven engine using JSON task library. Effect system supports `deploy_unit`, `connect_units`, `transfer_resource`, `manufacture`. NOT called by StrategySelector. |
| **EscalationService** | `escalation_service.rb` | Active | Task file says "EscalationHandler" but actual class is `EscalationService`. Handles expired buy orders via 3 strategies: automated harvesting, manufacturing deployment, scheduled imports. Works with Order objects, NOT task types. Multiple stubs (`time_to_critical`, `time_to_next_resupply`). |
| **StateAnalyzer** | `state_analyzer.rb` | Active | Provides `inventory`, `surface_storage`, `power_available`, `cost_analysis`. Source of truth: `settlement.inventory` + `Market::Order` models. Sufficient for resource decisions — NO new `AllocationAnalyzer` needed. |
| **ServiceCoordinator** | `service_coordinator.rb` | Active | Bridge between StrategySelector and services. `acquire_resource()` calls `ResourceAcquisitionService.order_acquisition()` directly, bypassing TaskExecutionEngineV2 entirely. |

#### 2. Gap Analysis — What's Missing

**Critical Finding**: The task file references "EscalationHandler" but the actual class is `EscalationService`. This is a naming discrepancy that must be resolved.

**Gap 1: StrategySelector → TaskExecutionEngineV2 Integration (Missing)**
- StrategySelector calls `execute_action(action, settlement)` which routes to `service_coordinator.acquire_resource()` directly
- TaskExecutionEngineV2 exists but is only used by the old `TaskExecutionEngine` path in ServiceCoordinator (`start_mission`)
- **No bridge exists** between StrategySelector's decision loop and V2's effect-based execution

**Gap 2: EscalationService Cannot Accept "ResourceAllocationTask" Types (Confirmed)**
- Current `EscalationService.handle_expired_buy_orders()` only accepts Order objects
- No method to accept a task-type or strategy-selected action hash
- Would need new entry point: `handle_resource_shortage(action_hash, settlement)`

**Gap 3: Escalation Triggers Are Stubbed**
- `time_to_critical()` returns hardcoded 72 hours — no real consumption tracking
- `time_to_next_resupply()` returns hardcoded 7 days — no scheduled import querying
- Market price threshold check does NOT exist in current code

#### 3. Proposed "Task Contract" Design

```
StrategySelector.evaluate_next_action(settlement)
  │
  ├─ StateAnalyzer.analyze_state(settlement)
  │   └─ Returns: { inventory, cost_analysis, unfilled_buy_orders }
  │
  ├─ If :resource_acquisition action selected:
  │   │
  │   ├─ [NEW] Check local supply via ServiceCoordinator.check_resource_availability()
  │   │
  │   ├─ If local_supply == 0 AND market_price > threshold:
  │   │   └─ EscalationService.handle_resource_shortage({
  │   │         type: :resource_allocation,
  │   │         material: "Nitrogen",
  │   │         deficit: quantity,
  │   │         settlement: settlement
  │   │       })
  │   │
  │   ├─ If local_supply > 0 OR price acceptable:
  │   │   └─ TaskExecutionEngineV2.execute_resource_task({
  │   │         target_body: settlement.celestial_body.identifier,
  │   │         manifest: { phases: [{ phase_id: "resource_acquisition", ... }] }
  │   │       })
  │   │
  │   └─ [NEW] TaskExecutionEngineV2.add_effect("request_import", {...})
  │       # New effect action to support import requests via V2 pipeline
  │
  └─ If :cost_reduction action selected:
      └─ Existing execute_cost_reduction() path (ShortageDetector → ImportRequestGenerator)
```

#### 4. Required Changes (Phased)

**Phase A — Naming Alignment (Research Output)**
- [ ] Clarify: "EscalationHandler" in task file = `EscalationService` in code
- [ ] Document the mapping so future tasks use correct class name

**Phase B — EscalationService Extension (Implementation)**
- [ ] Add `handle_resource_shortage(action_hash, settlement)` class method
- [ ] Accept strategy-selected action hashes (not just Order objects)
- [ ] Implement market price threshold check (query `Market::Price` or equivalent)

**Phase C — StrategySelector → V2 Bridge (Implementation)**
- [ ] Add `execute_via_task_engine(action, settlement)` method to StrategySelector
- [ ] Route `:resource_acquisition` through TaskExecutionEngineV2 instead of direct ServiceCoordinator call
- [ ] Add `"request_import"` effect action to TaskExecutionEngineV2's `execute_effect()` case statement

**Phase D — Escalation Trigger Implementation (Future)**
- [ ] Replace `time_to_critical()` stub with real consumption rate from settlement.inventory
- [ ] Replace `time_to_next_resupply()` stub with ScheduledImport query
- [ ] Add market price threshold configuration

#### 5. Risk Assessment

| Risk | Severity | Mitigation |
|---|---|---|
| Breaking existing StrategySelector → ServiceCoordinator path | HIGH | Keep direct path as fallback; V2 route is opt-in via flag |
| TaskExecutionEngineV2 creates settlements (may duplicate) | MEDIUM | Add idempotency check; reuse existing settlement if found |
| EscalationService stubs return wrong values | LOW | Document as known limitation; Phase D fixes |

#### 6. Answering Task File Verification Steps

- [x] **StrategySelector hooks for `:resource_acquisition`**: Found. `execute_action()` routes to `execute_resource_acquisition()` which calls `service_coordinator.acquire_resource()`. NO hook for V2 pipeline.
- [x] **EscalationHandler (EscalationService) can accept ResourceAllocationTask types**: Currently NO. Only accepts Order objects. Requires new entry point method.
- [x] **New AllocationAnalyzer needed?**: NO. Current `StateAnalyzer` provides sufficient data (`inventory`, `cost_analysis`, `unfilled_buy_orders`).

---

**STOP POINT**: Awaiting human approval before proceeding to implementation phase.

## Completion Report
**Completed by**: qwen3.6-27b-copilot-ryzen
**Completion date**: 2026-06-28
**Outcome**: Research complete. Naming discrepancy (EscalationHandler → EscalationService) documented. Confirmed StrategySelector and TaskExecutionEngineV2 are parallel systems with no bridge. Confirmed StateAnalyzer is sufficient (no new AllocationAnalyzer needed). Two escalation stubs identified for future replacement.
**Follow-up tasks created**: split into two implementation task files per architecture review — 2026-06-28-HIGH-FEATURE-ESCALATION-SERVICE-RESOURCE-SHORTAGE-EXTENSION.md (ready to dispatch) and 2026-06-28-HIGH-ARCHITECTURE-STRATEGY-SELECTOR-V2-BRIDGE.md (blocked pending design decision).