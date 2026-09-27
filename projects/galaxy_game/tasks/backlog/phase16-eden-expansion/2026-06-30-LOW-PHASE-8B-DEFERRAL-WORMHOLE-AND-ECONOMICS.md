---
status: backlog
priority: LOW
type: documentation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Phase 14+ Deferral — Wormhole Integration & Economic Forecaster Wiring (Post-MVP)

**Status**: BACKLOG
**Priority**: LOW
**Type**: documentation
**Created**: 2026-06-30
**Phase**: phase14+

---

## Context

During the Phase 8 SystemOrchestrator audit and task decomposition, two moderate gaps were identified that improve the system but are not critical blockers for core functionality:

1. **Wormhole Route Integration** — `LogisticsCoordinator` currently uses hardcoded transport capacities. Should integrate with `WormholeManager` and `NetworkOptimizer` for real route data.

2. **Economic Forecaster Wiring** — `EconomicForecasterService` exists but is disconnected from the orchestrator's strategic planning loop. Should integrate into `StrategicPlanner`.

Both gaps represent valid enhancements but are **post-MVP items** (deferred to Phase 14+).

---

## Why This Is Deferred

- **Phase 8 Goal**: Validate that SystemOrchestrator can make correct resource allocation decisions using real settlement data
- **Phase 14+ Goal**: Optimize orchestrator decisions using wormhole routing and economic projections (post-MVP)
- **MVP Scope**: Phase 8 is sufficient to prove core orchestration works

---

## Phase 8 Completion Criteria

Phase 8 is complete when:
- ✅ ResourceAllocator Stability
- ✅ SettlementManager Real Data
- ✅ SystemState Persistence
- ✅ StrategicPlanner Extraction
- ✅ Multi-Settlement Integration Spec
- ✅ PriorityArbitrator Test Coverage
- ✅ LogisticsCoordinator Test Coverage

All 7 tasks pass with 0 failures and are committed.

---

## Dependencies

**Blocked by**: 
- `2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md` (ResourceAllocator Stability)
- `2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md` (SettlementManager Real Data)
- `2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md` (SystemState Persistence)
- `2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md` (StrategicPlanner Extraction)
- `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md` (Multi-Settlement Integration Spec)
- `2026-06-30-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md` (PriorityArbitrator Test Coverage)
- `2026-06-30-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md` (LogisticsCoordinator Test Coverage)

**Blocks**: Phase 14+ wormhole integration and economic forecaster tasks (when created)
**Related tasks**: None yet — Phase 8B tasks to be created when Phase 8 completes

---

## Next Steps

1. **Close Phase 8** — Once all 7 Phase 8 tasks are complete, mark as COMPLETE
2. **Schedule Phase 14+** — Create 2-3 Phase 14+ tasks for wormhole routing and economic forecaster integration (post-MVP)
3. **Update Project Status** — Document that orchestrator is now live with real data wiring

---

## Handoff Summary

HANDOFF SUMMARY: Deferral task — Phase 8 complete when all 7 listed tasks pass with 0 failures | Next action: create Phase 14+ tasks for wormhole routing and economic forecaster integration
