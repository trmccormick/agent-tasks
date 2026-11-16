# SystemOrchestrator Proposed Tasks — Gap Analysis Review

**Date**: 2026-06-30
**Reviewer**: Research Agent (Copilot)
**Input**: 4 proposed tasks from Gemini + audit report (`2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`)
**Proposed Tasks Location**: `docs/new_agent/projects/galaxy_game/tasks/proposed new tasks/2026-06-30-TASK-UPDATE-1/`

---

## Overview

Cross-referenced the 4 proposed tasks against the 9 audit gaps and actual spec file state. Found **5 critical gaps** and **4 moderate gaps** not addressed by the current task set.

---

## Critical Gaps (Missing from Proposed Tasks)

### 1. No Multi-Settlement Integration Test Task
The audit flagged this as critical: *"No tests verify resource allocation across 2+ settlements with competing demands."* None of the 4 tasks address writing an integration test that validates actual coordination behavior. The existing `manager_system_orchestrator_integration_spec.rb` is heavily stubbed and only tests registration and method invocation. Without a multi-settlement integration test, there's no way to verify the orchestrator actually works end-to-end after all 4 tasks complete.

**Recommendation**: Add a HIGH-priority task — `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md` — as the final task in the pipeline, run after all 4 existing tasks complete.

### 2. Referenced Spec Files Don't Exist (3 of 4)
The verification commands reference specs that don't exist:
| Spec File | Exists? |
|---|---|
| `settlement_manager_spec.rb` | ❌ NO |
| `system_state_spec.rb` | ❌ NO |
| `strategic_planner_spec.rb` | ❌ NO (class doesn't exist yet) |
| `resource_allocator_spec.rb` | ✅ YES (but tests OLD constructor) |

Running these verification commands will fail immediately. Each task should include "create spec file" as step 1.

### 3. Existing ResourceAllocator Spec Tests the OLD Constructor
The current `resource_allocator_spec.rb` tests `described_class.new(settlement_size: 'small')` — the legacy single-settlement pattern. After Task 1 merges constructors, this spec will break. The task should explicitly include updating this existing spec to test the new `shared_context` constructor and system-wide allocation logic.

### 4. PriorityArbitrator Has Zero Test Coverage
`PriorityArbitrator` is a critical component (conflict resolution for competing resource demands) with no spec file at all. It's wired directly into `SystemOrchestrator.allocate_system_resources()` but none of the 4 tasks create tests for it. If arbitration logic has bugs, they'll silently produce wrong allocations.

### 5. LogisticsCoordinator Has Zero Test Coverage
Same issue — no spec exists and no task creates one. The coordinator handles inter-body transfers, which is literally Task 6.2 from the original backlog. Without tests, hardcoded capacity values can't be validated against real wormhole network data.

---

## Moderate Gaps

### 6. No Financial Integration Task
Cross-settlement resource transfers should create `Financial::Transaction` ledger entries. The `Financial` module is complete and ready (accounts exist on settlements via `SettlementCore`). At minimum a LOW priority item to wire transfer execution through the financial ledger.

### 7. No Wormhole Route Integration Task
`LogisticsCoordinator.initialize_transport_capacity()` returns hardcoded values (`mars_luna_capacity: 500`, etc.). Should integrate with `WormholeManager` and `NetworkOptimizer` for real route data. Not addressed by any task.

### 8. No EconomicForecasterService Wiring Task
`EconomicForecasterService` exists but isn't connected to the orchestrator's strategic planning loop. The StrategicPlanner extraction task should mention this, or a separate wiring task should exist.

### 9. Task Dependencies Not Explicit
The tasks are marked HIGH/MEDIUM but there's no dependency graph:
- **Task 2** (SettlementManager integration) depends on **Task 1** (ResourceAllocator fix) — SettlementManager calls into ResourceAllocator
- **Task 4** (StrategicPlanner extraction) should happen after **Task 3** (SystemState persistence) — the planner needs persistent state to work with
- **Task 3** (persistence) is a prerequisite for meaningful integration testing

The `TASK_REFACTOR_SUMMARY.md` table shows priority ordering but not execution ordering. An agent picking up Task 2 before Task 1 completes will hit broken imports.

**Recommendation**: Add explicit dependency ordering to `TASK_REFACTOR_SUMMARY.md`.

---

## Coverage Matrix

| Audit Gap | Covered By Proposed Task? | Notes |
|---|---|---|
| 1. Mock data throughout | ✅ Partially (Task 2) | Only SettlementManager, not LogisticsCoordinator |
| 2. No DB persistence for SystemState | ✅ Yes (Task 3) | |
| 3. Dual constructor in ResourceAllocator | ✅ Yes (Task 1) | But doesn't mention updating existing spec |
| 4. No multi-settlement test coverage | ❌ **NO** | Critical — no integration test task |
| 5. No real settlement model integration | ✅ Yes (Task 2) | |
| 6. Missing StrategicPlanner class | ✅ Yes (Task 4) | |
| 7. No wormhole route integration | ❌ **NO** | LogisticsCoordinator still hardcoded |
| 8. No financial integration | ❌ **NO** | Transfers have no ledger entries |
| 9. No economic forecaster wiring | ❌ **NO** | Forecaster exists but disconnected |
| PriorityArbitrator test coverage | ❌ **NO** | Zero specs, zero tasks |
| LogisticsCoordinator test coverage | ❌ **NO** | Zero specs, zero tasks |

---

## Recommendations Summary

1. **Add HIGH-priority integration test task** — Final pipeline step verifying coordination across 2+ settlements with real factories and competing demands
2. **Add spec creation to each task** — Step 1 should be "Create/update spec file for [component]" before implementation
3. **Update Task 1 to include spec migration** — Rewrite `resource_allocator_spec.rb` from old constructor pattern to new system-wide pattern
4. **Add LOW-priority financial wiring task** — Wire `LogisticsCoordinator.schedule_transfers()` through `Financial::Transaction` ledger entries
5. **Add explicit dependency ordering** to `TASK_REFACTOR_SUMMARY.md` — Show execution order, not just priority
6. **Defer wormhole integration and economic forecaster wiring** to a Phase 8b follow-up — Nice-to-have enhancements that don't block core orchestrator from functioning with real data
