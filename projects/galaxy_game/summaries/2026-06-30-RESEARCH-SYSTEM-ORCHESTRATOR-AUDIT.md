# SystemOrchestrator Feasibility Audit — Research Report

**Date**: 2026-06-30  
**Agent**: Research Agent (Copilot)  
**Scope**: Audit of existing multi-settlement coordination capabilities vs. backlog requirements in `systemorchestrator_development.md`  
**Backlog Source**: `/docs/agent/archive/backlog_april_2026/systemorchestrator_development.md`

---

## Executive Summary

The SystemOrchestrator feature outlined in the April 2026 backlog is **already implemented at the class level**, but the implementation consists largely of structural scaffolding with stub methods and mock data. The architecture is sound — all four required components exist as classes under `AIManager` — but none are production-ready. Integration with `AIManager::Manager` is complete and tested, but the underlying coordination logic needs real data-driven implementations to replace mocks.

---

## 1. Existing Implementations

### Primary: `SystemOrchestrator` (`system_orchestrator.rb`)
**Status**: EXISTS — Full class structure present  
**Location**: `galaxy_game/app/services/ai_manager/system_orchestrator.rb`

The `SystemOrchestrator` class implements the complete multi-settlement coordination architecture described in the backlog:

| Backlog Requirement | Implementation Status | Details |
|---|---|---|
| Task 6.1: Multi-Settlement Resource Allocation | ✅ Class exists, ⚠️ stub methods | `allocate_system_resources()` delegates to `ResourceAllocator` and `PriorityArbitrator`. Private methods (`collect_system_resource_requests`, `execute_resource_allocations`) are present but rely on mock data from `SettlementManager.settlement_resources`. |
| Task 6.2: Inter-Body Logistics Coordination | ✅ Class exists, ⚠️ stub methods | `coordinate_logistics()` delegates to `LogisticsCoordinator`. Transfer optimization and scheduling logic is structurally complete but uses hardcoded capacity values (e.g., `mars_luna_capacity: 500`). |
| Task 6.3: System-Wide Priority Management | ✅ Class exists, ⚠️ stub methods | `PriorityArbitrator` handles conflict resolution with priority levels (`critical`/`high`/`medium`/`low`). Crisis escalation and conflict resolution mechanisms are present. |
| Task 6.4: Long-Term Strategic Planning | ⚠️ Partially implemented | `execute_strategic_planning()` method exists but delegates to private stubs (`evaluate_system_opportunities`, `coordinate_expansion_plans`, `balance_economic_development`). No multi-year forecasting or adaptive planning. |

### Supporting Infrastructure (All Present)

| Component | File | Purpose | Production-Ready? |
|---|---|---|---|
| `SettlementManager` | `settlement_manager.rb` | Per-settlement wrapper for the orchestrator | ❌ Mock data (`settlement_resources` returns hardcoded hash) |
| `SystemState` | `system_state.rb` | System-wide state tracking (resources, health, objectives, dependencies) | ⚠️ In-memory only, no DB persistence |
| `ResourceAllocator` | `resource_allocator.rb` | Cross-settlement resource allocation with fair-sharing algorithm | ⚠️ Dual constructor signatures (refactoring incomplete) |
| `PriorityArbitrator` | `priority_arbitrator.rb` | Conflict resolution for competing resource demands | ✅ Structurally complete, needs real data |
| `LogisticsCoordinator` | `logistics_coordinator.rb` | Inter-body transfer optimization and scheduling | ⚠️ Hardcoded transport capacities, no wormhole integration |
| `NetworkOptimizer` | `network_optimizer.rb` | Wormhole network gap analysis and development prioritization | ✅ Structurally complete |
| `ResourceFlowSimulator` | `resource_flow_simulator.rb` | Production chain modeling with dependency graphs | ✅ Luna-specific chains defined, generalizable pattern |
| `SharedContext` | `shared_context.rb` | Event notification system (pub/sub) for inter-service communication | ✅ Complete and functional |

### Integration Layer

**`AIManager::Manager`** (`manager.rb`) — The main entry point already accepts an optional `system_orchestrator`:
- Registers settlements on initialization
- Calls `orchestrate_system` during every `advance_time` tick
- Gracefully degrades when no orchestrator is provided (backward compatible)

**Test Coverage**: One integration spec exists:
- `spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb` — Tests registration, access, and orchestration invocation. Heavily stubbed; does NOT test actual multi-settlement coordination logic.

---

## 2. Naming Conflicts

### No Direct Naming Conflicts
The backlog document used the name "SystemOrchestrator" and the implementation uses exactly that name. However, there are related services with overlapping concerns:

| Service | Overlap With SystemOrchestrator? | Notes |
|---|---|---|
| `ServiceOrchestrator` | Adjacent concern | Manages AI *service* states (TaskExecutionEngine, ResourceAcquisitionService, ScoutLogic). Different abstraction level — coordinates services within a settlement, not settlements across the system. |
| `ServiceCoordinator` | Adjacent concern | Mission and resource coordination at single-settlement level. Called by `Manager.advance_time`. Complementary, not conflicting. |
| `OperationalManager` | Potential overlap | Single-settlement operations manager. Should be subordinated to SystemOrchestrator for cross-settlement decisions. |
| `ResourceFulfillmentService` | Partial overlap | Handles resource fulfillment at settlement level. SystemOrchestrator should delegate to this for execution. |
| `Logistics::TransportCostService` | Complementary | Lower-level logistics module (separate namespace). Should be used by `LogisticsCoordinator`. |
| `Logistics::ShortageDetector` | Complementary | Detects shortages at settlement level. SystemOrchestrator should aggregate across settlements. |
| `SafetyNetLogisticsJob` | Complementary | Background job for emergency logistics. Could be triggered by SystemOrchestrator crisis detection. |

**Verdict**: No naming conflicts. The hierarchy is:
```
SystemOrchestrator (system-wide)
  └── SettlementManager (per-settlement wrapper)
        ├── ServiceOrchestrator (service coordination within settlement)
        ├── ServiceCoordinator (mission/resource coordination)
        └── OperationalManager (settlement operations)
```

---

## 3. Architectural Gaps

### Critical Gaps (Blockers for Production Use)

1. **Mock Data Throughout** — `SettlementManager.settlement_resources` returns a hardcoded hash (`{ minerals: 100, energy: 80, ... }`). All coordination logic is built on top of mock data. Needs to query real `BaseSettlement.inventory` and `operational_data`.

2. **No Database Persistence for System State** — `SystemState` stores all state in Ruby instance variables. No persistence means system-wide state is lost between requests. Needs either a model or at minimum Redis-backed caching.

3. **Dual Constructor in ResourceAllocator** — The file contains TWO `ResourceAllocator` class definitions:
   - One with `initialize(settlement_size: 'small')` (old, single-settlement)
   - One with `initialize(shared_context)` (new, system-wide)
   This is a refactoring artifact that will cause the old version to shadow the new one.

4. **No Multi-Settlement Test Coverage** — The existing spec only tests registration and method invocation. No tests verify:
   - Resource allocation across 2+ settlements with competing demands
   - Priority arbitration actually resolves conflicts correctly
   - Logistics transfers between settlements on different celestial bodies
   - Strategic planning produces actionable expansion plans

5. **No Integration with Real Settlement Models** — `SettlementManager` doesn't query `BaseSettlement.inventory`, `account`, or `operational_data`. The orchestrator is an in-memory overlay disconnected from the actual data layer.

### Moderate Gaps (Impede Full Feature Completeness)

6. **Missing `StrategicPlanner` Class** — Backlog doc lists `strategic_planner.rb` as a file to create. Strategic planning logic is embedded in `SystemOrchestrator.execute_strategic_planning()` as private stubs. Should be extracted to its own class for testability.

7. **No Wormhole Route Integration** — `LogisticsCoordinator` uses hardcoded route capacities. Should integrate with actual wormhole network data from `WormholeManager` and `NetworkOptimizer`.

8. **No Financial Integration** — SystemOrchestrator has no connection to `Financial::Account`, `Financial::Transaction`, or settlement economic state. Cross-settlement resource transfers should have financial implications (cost tracking, inter-settlement accounting).

9. **No Economic Forecaster Integration** — `EconomicForecasterService` exists but is not wired into the orchestrator's strategic planning loop.

### Minor Gaps (Nice-to-Have)

10. **No Health Monitoring Dashboard** — System health metrics are calculated but not exposed via any API or UI endpoint.
11. **No Adaptive Planning** — Backlog asks for "adaptive planning based on changing conditions" — currently no feedback loop from execution results back to plan adjustment.

---

## 4. BaseSettlement & Financial Readiness

### BaseSettlement Architecture: ✅ READY
`BaseSettlement` has all the infrastructure needed to support system-level orchestration:
- `inventory` association (surface storage, resource tracking)
- `account` association (`Financial::Account`)
- `operational_data` JSONB column (config-driven behavior per DECISIONS.md)
- `location` → `CelestialLocation` → `celestial_body` (inter-body routing)
- `settlement_type` enum (base/outpost/settlement/city/station)
- Life support concerns (`LifeSupport`, `EnergyManagement`)
- Job associations for manufacturing and construction

### Financial Architecture: ✅ READY (Not Connected)
The `Financial` module is complete with `Account`, `Transaction`, `LedgerEntry`, `Currency`, `ExchangeRate`, and `Bond` models. Settlements have accounts via `SettlementCore` concern. The orchestrator just needs to wire into this existing infrastructure.

---

## 5. Actionability Verdict

### **NEEDS REFACTOR** — Architecture is sound, implementation is scaffolding

**Summary**: The SystemOrchestrator feature is architecturally complete but functionally incomplete. All four backlog tasks (6.1–6.4) have corresponding classes and method signatures, but the underlying logic relies on mock data and stub implementations. The architecture follows good patterns (delegation to specialized services, event-driven coordination via SharedContext, clean separation of concerns).

**Recommended Path Forward**:
1. **Fix dual constructor in `ResourceAllocator`** — Remove old single-settlement version
2. **Wire real data into `SettlementManager`** — Replace mock `settlement_resources` with actual inventory queries
3. **Add persistence to `SystemState`** — Redis cache or lightweight model for system-wide state
4. **Extract `StrategicPlanner`** — Move strategic planning stubs from SystemOrchestrator into its own class
5. **Write multi-settlement integration specs** — Test actual coordination across 2+ settlements with real factories
6. **Connect Financial tracking** — Wire orchestrator transfers to `Financial::Transaction` ledger entries

**Estimated Effort**: Medium (4-6 focused implementation tasks). The scaffolding means the hard architectural decisions are already made and coded. The remaining work is connecting real data sources and writing tests.

---

## Appendix: File Inventory

### SystemOrchestrator Core Files
| File | Lines | Purpose |
|---|---|---|
| `system_orchestrator.rb` | ~200+ | Main orchestrator class |
| `settlement_manager.rb` | ~150+ | Per-settlement wrapper |
| `system_state.rb` | ~150+ | System-wide state tracking |
| `resource_allocator.rb` | ~150+ | Resource allocation (dual definition) |
| `priority_arbitrator.rb` | ~120+ | Conflict resolution |
| `logistics_coordinator.rb` | ~150+ | Transfer optimization |

### Related AI Manager Services (Not Directly Part of Orchestrator)
| File | Purpose |
|---|---|
| `service_orchestrator.rb` | Service-level coordination (different abstraction) |
| `service_coordinator.rb` | Mission/resource coordination at settlement level |
| `operational_manager.rb` | Single-settlement operations |
| `network_optimizer.rb` | Wormhole network planning |
| `resource_flow_simulator.rb` | Production chain modeling |
| `economic_forecaster_service.rb` | Economic projections |
| `market_stabilization_service.rb` | Market balance |
| `shared_context.rb` | Event notification system |

### Test Files
| File | Coverage |
|---|---|
| `manager_system_orchestrator_integration_spec.rb` | Registration, access, method invocation (heavily stubbed) |
