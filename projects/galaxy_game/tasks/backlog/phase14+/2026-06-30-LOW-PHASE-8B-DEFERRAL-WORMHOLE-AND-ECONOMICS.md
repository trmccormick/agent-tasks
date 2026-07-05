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

---

## Context

During the Phase 8 SystemOrchestrator audit and task decomposition, two moderate gaps were identified that improve the system but are not critical blockers for core functionality:

1. **Wormhole Route Integration** — `LogisticsCoordinator` currently uses hardcoded transport capacities. Should integrate with `WormholeManager` and `NetworkOptimizer` for real route data.

2. **Economic Forecaster Wiring** — `EconomicForecasterService` exists but is disconnected from the orchestrator's strategic planning loop. Should integrate into `StrategicPlanner`.

Both gaps represent valid enhancements but are **Phase 8B items** (post-MVP).

---

## Why This Is Deferred

- **Phase 8 Goal**: Validate that SystemOrchestrator can make correct resource allocation decisions using real settlement data
- **Phase 8B Goal**: Optimize orchestrator decisions using wormhole routing and economic projections
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

## Next Steps

1. **Close Phase 8** — Once all 7 Phase 8 tasks are complete, mark as COMPLETE
2. **Schedule Phase 8B** — Create 2-3 Phase 8B tasks for wormhole routing and economic forecaster integration
3. **Update Project Status** — Document that orchestrator is now live with real data wiring
