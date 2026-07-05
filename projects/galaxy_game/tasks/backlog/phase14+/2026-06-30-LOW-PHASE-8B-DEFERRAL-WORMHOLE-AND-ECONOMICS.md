---
status: backlog
priority: LOW
type: documentation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Phase 8B Deferral — Wormhole Integration & Economic Forecaster Wiring (Post-MVP)

**Status**: BACKLOG
**Priority**: LOW
**Type**: documentation
**Created**: 2026-06-30
**Last Updated**: 2026-06-30

---

## Context

During the Phase 8 SystemOrchestrator audit and task decomposition, two moderate gaps were identified that improve the system but are not critical blockers for core functionality:

1. **Wormhole Route Integration** — `LogisticsCoordinator` currently uses hardcoded transport capacities (`mars_luna_capacity: 500`, `transfer_time_mars_luna: 2.hours`). Should integrate with `WormholeManager` and `NetworkOptimizer` for real route data.

2. **Economic Forecaster Wiring** — `EconomicForecasterService` exists but is disconnected from the orchestrator's strategic planning loop. Should integrate into `StrategicPlanner` for multi-year economic projections.

Both gaps represent valid enhancements but are **Phase 8B items** (post-MVP). Phase 8 focuses on making the orchestrator functional with real data. Phase 8B will optimize integration with wormhole networks and economic forecasting.

---

## Why This Is Deferred

- **Phase 8 Goal**: Validate that SystemOrchestrator can make correct resource allocation decisions using real settlement data
- **Phase 8B Goal**: Optimize orchestrator decisions using wormhole routing and economic projections
- **MVP Scope**: Phase 8 is sufficient to prove core orchestration works

Deferring these allows Phase 8 to ship on schedule without delaying critical integration work.

---

## Tasks to Create in Phase 8B

### 1. Wormhole Route Integration Task
**File**: `2026-0X-XX-MEDIUM-INTEGRATION-LOGISTICS-WORMHOLE-ROUTING.md`
**Work**: Wire `LogisticsCoordinator.initialize_transport_capacity` to query `WormholeManager` and `NetworkOptimizer` for real route data instead of hardcoded values
**Acceptance**: Transport costs and timings derive from actual wormhole network topology
**Dependencies**: Requires `WormholeManager` and `NetworkOptimizer` to be stable and queryable

### 2. Economic Forecaster Wiring Task
**File**: `2026-0X-XX-MEDIUM-INTEGRATION-STRATEGIC-PLANNER-ECONOMICS.md`
**Work**: Wire `StrategicPlanner.execute_strategic_planning` to call `EconomicForecasterService.project_forward(years: 5)` for multi-year projections
**Acceptance**: StrategicPlanner uses economic projections to set longer-term objectives
**Dependencies**: Requires `EconomicForecasterService` to be stable and return projections in expected format

### 3. Financial Transaction Ledger Wiring Task
**File**: `2026-0X-XX-LOW-INTEGRATION-FINANCIAL-LEDGER-TRANSFERS.md`
**Work**: Wire `LogisticsCoordinator.schedule_transfers` to create `Financial::Transaction` ledger entries for each transfer
**Acceptance**: Cross-settlement transfers create auditable financial records
**Dependencies**: Requires `Financial::Account` and `Financial::Transaction` models to be queryable from settlements

---

## Audit Gaps This Addresses

From `2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`:
- Gap #7: No wormhole route integration → Phase 8B Task 1
- Gap #8: No financial integration → Phase 8B Task 3
- Gap #9: No economic forecaster wiring → Phase 8B Task 2

---

## Phase 8 Completion Criteria (Unchanged)

Phase 8 is complete and ready to close when:
- ✅ ResourceAllocator Stability (Task 1)
- ✅ SettlementManager Real Data (Task 2)
- ✅ SystemState Persistence (Task 3)
- ✅ StrategicPlanner Extraction (Task 4)
- ✅ Multi-Settlement Integration Spec (Task 5)
- ✅ PriorityArbitrator Test Coverage (Task 6)
- ✅ LogisticsCoordinator Test Coverage (Task 7)

All 7 tasks pass with 0 failures and are committed.

---

## Phase 8B Kickoff Prerequisites

Before starting Phase 8B, verify:
- All Phase 8 tasks are committed and passing
- `WormholeManager` has queryable routes and capacities (not just test fixtures)
- `EconomicForecasterService` returns structured projections
- `Financial::Account` is properly associated with settlements

---

## Next Steps

1. **Close Phase 8** — Once all 7 Phase 8 tasks are complete, update `TASK_OVERVIEW.md` to mark Phase 8 as COMPLETE
2. **Schedule Phase 8B** — Create 3 Phase 8B tasks (listed above) in backlog folder, prioritized for next execution cycle
3. **Update Project Status** — Document in `projects/galaxy_game/status.md` that orchestrator is now live with real data wiring
