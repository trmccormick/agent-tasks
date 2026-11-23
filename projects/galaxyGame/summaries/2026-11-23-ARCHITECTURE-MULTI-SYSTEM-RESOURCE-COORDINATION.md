# Architecture Research — Multi-System Resource Coordination

**Date:** 2026-11-23  
**Project:** GalaxyGame  
**Source branch reviewed:** trmccormick/galaxyGame:main  
**Original task:** [2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION](https://github.com/trmccormick/agent-tasks/blob/main/projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md)  
**Mode:** Read-only source/architecture investigation. No backend code changed and no tests were run.

## Executive finding

**Do not dispatch the original multi-system coordination task for implementation yet.** The code contains classes with the intended names, but the end-to-end capability is not present. The current SystemOrchestrator is a scaffold over mock settlement state; its resource allocator does not move or reserve inventory; its logistics coordinator cannot schedule a transfer; and there is no verified wormhole-aware delivery-cost input. Per-settlement acquisition is fragmented and several paths are disconnected or inconsistent with the current models.

This matches the newer [AI Manager module README](https://github.com/trmccormick/galaxyGame/blob/main/galaxy_game/app/services/ai_manager/README.md): cross-settlement and cross-system optimization is deferred until reliable local operations, acquisition, and transport-cost/connectivity inputs exist. The immediate Luna benchmark remains a prerequisite, not a result that can be assumed.

The enduring goal remains valid: coordinate resource surpluses and shortages across settlements and systems without creating a second acquisition system or bypassing logistics, economic, and wormhole rules. The original task should be replaced by smaller dependency-linked drafts after the foundations below are defined.

## Scope and evidence reviewed

Reviewed the original task and earlier coordination records, including the 2026-06-30 SystemOrchestrator audit, 2026-09-08 coordination summary, Foothold Planner completion record, and the 2026-09-16/17/24 acquisition and material-data-contract records.

Inspected the current AI Manager source and tests around:
- Manager, SystemOrchestrator, SettlementManager, SystemState, ResourceAllocator, PriorityArbitrator, and LogisticsCoordinator
- StateAnalyzer, StrategySelector, ServiceCoordinator, TaskExecutionEngineV2, EscalationService, ResourceAcquisitionService, ResourcePlanner, ResourceFulfillmentService, MaterialRequestService, and ProcurementService
- Inventory, Item, logistics providers/contracts/import requests, and financial ledger services
- Wormhole, WormholeNavigator, WormholeCoordinator, Mission::TransitEngine, and transport-cost helpers
- Relevant specs for the manager, selector, allocator, escalation, contracts, and wormhole coordination

Also compared current behavior with the authoritative AI Manager, economic-loop, logistics/hauling, EAP/escalation, and natural-wormhole wiki pages.

## Readiness against the original task

| Required foundation | Current assessment | Evidence-based conclusion |
|---|---|---|
| Single-system Luna operation proven | **Not established** | The AI Manager README still identifies the integrated Luna benchmark as the near-term proof target. The 2026-08-08 baseline report recorded 16/17 deployment tasks passing, but zero production and consumption over 50 ticks, population 0, and major economic criteria not tested. The 2026-09-01 Capture Luna Worked Example remains blocked on a stable loop. |
| Foothold Planner architecture | **Partially satisfied** | The architecture task is marked complete and FootholdPlanner exists and is used by MissionPlannerService. It is a bounded candidate scorer, not a resource logistics or multi-system coordination engine. |
| Per-settlement acquisition routing | **Not operationally established** | The old material-sourcing task was superseded by the pre-player decision-tree and material-data-contract work, but current source does not support the later completion claims that the tree is wired and facility-based. See the open NEEDS_REVIEW item. |
| Multiple settlements can trade and deliver goods | **Not established** | Contract/provider models and services exist, but no verified reservation → transit → inventory delivery → payment lifecycle was found. |
| Wormhole connectivity and delivery-cost inputs | **Not ready** | Existing route helpers are prototype, hardcoded, or incompatible with the current Wormhole model. The intra-system TransitEngine explicitly rejects cross-system transit. |
| Multi-settlement and multi-system acceptance scenario | **Not found** | Current tests cover service shape and optional manager invocation, not a real resource transfer between two settlements or across a wormhole. |

**Disposition:** the original feature is still deferred. Its original dependency list is incomplete: a working Luna loop, authoritative inventory availability/reservation, usable per-settlement acquisition, wormhole-aware route inputs, and executable logistics contracts are also prerequisites.

## Findings by subsystem

### 1. SystemOrchestrator and system state

The source contains the intended component names, but the behavior is not operational:

- Manager accepts an optional SystemOrchestrator and calls orchestrate_system when one is supplied. No production construction/injection of SystemOrchestrator was found in the tracked application; the references found were the integration spec and a manual test script.
- SettlementManager supplies mock resource quantities, mock health, and a default request for 50 units of energy when no action supplies a request. It is not an authoritative projection of settlement inventory and demand.
- SystemState is in-memory. Its economic stability and logistics efficiency values are placeholders, not calculated from actual financial accounts, contracts, or transport state.
- ResourceAllocator distributes the mock totals. execute_resource_allocations calls a settlement-manager method that only logs the allocation. It does not debit source inventory, credit destination inventory, or reserve stock.
- ResourceAllocator reopens the class and defines a second initialize(shared_context), overriding the earlier initialize(settlement_size:). The existing allocator spec still calls the old keyword initializer, so the current source and spec interface disagree. This was not executed; no test result is claimed.
- PriorityArbitrator uses hardcoded system resource limits. Its grant-check helper is not the path used by the main arbitration method.
- LogisticsCoordinator has hardcoded route capacities, durations, and cost estimates. find_settlement_manager returns nil, so schedule_transfer cannot reserve or schedule a real transfer. In addition, the combined/split transfer hashes contain resources and total_quantity, while the scheduler expects a single resource and quantity. Its reservation helper is a logging stub.

There is also a tick-ownership concern: each Manager#advance_time invokes the injected system orchestrator. If multiple settlement managers share one instance, the system pass could run once per settlement tick. A future implementation needs one explicit system-level scheduling boundary and idempotency, not accidental N-times-per-tick coordination.

### 2. Inventory availability and reservations

The authoritative model shape is a settlement's single Inventory record with associated Item rows. Item quantity is represented by amount; item identity/metadata and material type live on the item.

No authoritative inventory reservation model or reserve/release/consume API was found. SettlementManager#reserve_resources only logs and returns true. Contract creation checks current stock but does not reserve it, so two pending contracts can claim the same stock.

Several existing callers assume a different model:
- EscalationService.inventory_sufficient? treats settlement.inventory as a relation with where(material: ...), status, and quantity, which does not match the current Inventory/Item association.
- The escalation spec stubs inventory as a relation, masking the mismatch.
- ResourcePlanner calls inventory.available(resource) and rescues to zero; no corresponding Inventory method was found.

The original task's assumption that no new schema is required is therefore **unverified**. Decide the reservation contract before implementation: authoritative on-hand, reserved, committed, and available quantities; atomic reservation; release on cancellation/failure; consume on successful transfer; and rollback behavior. Do not choose a schema or hide reservations in metadata without a design review.

### 3. Per-settlement acquisition

There are multiple partially overlapping paths rather than one reliable acquisition contract:

- StateAnalyzer currently returns inventory, surface storage, power availability, and cost analysis. StrategySelector expects additional keys such as resource_needs, scouting_opportunities, and expansion_readiness. The StrategySelector specs stub these values, so they do not prove the real analyzer supplies them.
- ServiceCoordinator.process_resource_requests orders acquisition only when current availability is already greater than or equal to the requested quantity. A true shortage does not trigger the acquisition branch.
- TaskExecutionEngineV2 routes resource tasks through ServiceCoordinator, but its import request uses a fixed quantity of 100. ResourceAcquisitionService then uses a static local-resource list rather than a single facility/capability-aware sourcing contract.
- The local branch of ResourceAcquisitionService calls ContractCreationService.create_player_contract, which is currently a logging placeholder rather than a persisted, fulfilled local acquisition.
- The older external-import path creates an import contract and withdraws USD; the newer process_external_import_with_cost path checks affordability and creates an order but does not debit the account or establish physical delivery.
- EscalationService.pre_player_acquisition_tree exists, but it is not called by handle_resource_shortage or another code call site found. Its inventory check is incompatible with the real model. Its local-production branch creates/returns a robot but does not invoke the existing site-deployment helper or start a production job.
- can_harvest_locally? and can_manufacture_locally? check resource presence but do not establish that the required deployed facility and processing capability exist. The existing 2026-08-23 O2 capability NEEDS_REVIEW item remains relevant.
- ResourceFulfillmentService calls MaterialRequestService.request_materials; no such method exists in the current MaterialRequestService source.
- ProcurementService contains placeholder local-production and market-purchase methods. Its OperationalManager path is used by the Rake task, not a verified production composition root.
- The shortage/import-request path can create an ImportRequest and manifest, but no delivery executor was found. It is not a complete acquisition-and-fulfillment path.

The 2026-09-17 acquisition wiring task is stored in the completed folder but still has status: BACKLOG in its YAML. Its synthesis is an implementation plan, not proof of completed behavior. The 2026-09-24 summary claims the pre-player tree reads facility fields such as production.facility_type and production.input_materials; current source does not support that claim. This discrepancy was added to [GalaxyGame NEEDS_REVIEW](https://github.com/trmccormick/agent-tasks/blob/main/projects/galaxyGame/NEEDS_REVIEW.md). Do not mark the acquisition dependency complete until this history and current implementation are reconciled.

### 4. Wormhole connectivity, transit cost, and delivery

- The current Wormhole model uses solar_system_a_id/solar_system_b_id, stability, mass-limit data, and directional counters. It does not provide a transport price or delivery-time API.
- WormholeNavigator queries origin_system_id, destination_system_id, and status, which do not match the current model fields. No caller or dedicated spec was found.
- WormholeCoordinator does use the current system endpoint associations, but its traversal cost is a fixed 10,000 GCC base and its traversal time a fixed 24-hour base with multipliers. It uses the total mass_limit rather than the model's remaining directional capacity. Its route-wormhole extraction returns synthetic IDs, so network-utilization lookups cannot map those IDs back to real wormhole records. Resource-sharing and dependency-analysis helpers remain placeholders. The coordinator is used by expansion planning, not by LogisticsCoordinator.
- Mission::TransitEngine intentionally handles intra-system orbital transit and explicitly rejects cross-system routes. Preserve that topology boundary; do not make the existing engine silently accept wormhole travel.
- TransportCostService.calculate_transport_cost references a RouteFinder that was not found in the tracked tree. Its other cost path relies on RouteCostCalculator, which uses placeholder/hardcoded distances and has no wormhole graph input. TransitFeeService contains a fixed example fee, not a route-derived cost.
- The current [natural wormhole and EM wiki](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/technical/natural_wormholes_and_em.md) explicitly distinguishes implemented model pieces from open physical behavior. A coordination task must consume approved connectivity/capacity/cost inputs; it must not invent mass-limit recovery, instability, EM, or traversal equations.

A future route-input task should define a typed contract for connectivity, direction, stability/traversal eligibility, remaining capacity, estimated transit time, cost, and failure state. If the current physics cannot supply one of those values, return an explicit unavailable/unknown result and stop; do not substitute fabricated constants.

### 5. Commerce, logistics contracts, and finance

There are several overlapping contract paths:

- Logistics::ContractService can create a provider-backed internal contract after checking source inventory. It selects a provider by capability/reliability, not by a verified route or route cost. It does not reserve stock or settle provider payment.
- Its execute_pending_contracts path can move inventory through remove_item/add_item, but no production caller/job was found. The transfer is not wrapped as one atomic transaction, and the method does not model the route/arrival lifecycle.
- TradeService can call create_internal_transfer, but that creates a pending contract; it does not execute delivery.
- Logistics::InternalTransferService has a separate path that marks contracts in transit/delivered without moving the corresponding material between settlement inventories.
- ContractFulfillmentService has another fulfillment path using a transit-hangar representation and immediate delivery. No production caller was found.
- Logistics::Contract and top-level LogisticsContract are separate model abstractions over the same logistics-contract table, with different assumptions and workflows.
- ImportRequestGenerator creates request/manifest records; no end-to-end delivery path was found.
- Financial::VirtualLedgerService records ledger entries but does not itself transfer balances or goods. Financial::LedgerManager.settle_with_usd is empty. Real GCC account transfers exist elsewhere, but the provider-backed internal contract path is not connected to them.

The preferred logistics-company go-between remains a valid design direction, but the implementation must have one authoritative contract lifecycle: reserve source stock, select a feasible provider/route, calculate approved cost/ETA, dispatch, move cargo on arrival, settle GCC or the approved NPC/DC virtual ledger, and handle cancellation/failure/recovery.

## Design and documentation alignment

Use these distinctions when preparing successor tasks:

- The system-level coordinator sits above local settlement acquisition. It matches real surplus to real deficits and chooses a logistics route; it does not become a second acquisition engine.
- Preserve the current economic-loop direction: local production where capable; demand-driven market interaction; player-facing commerce uses real GCC; NPC/DC-only virtual ledger is not a substitute for player money; EAP is a ceiling/fallback rather than an excuse for unlimited imports. The current 80% buy / 90% sell bands are DIRECTION in the economic-loop wiki, not verified code.
- Do not inherit older market-maker/guaranteed-liquidity language from ai_manager_logic.md as settled policy. Keep the current non-competitive NPC market direction.
- Keep the pre-player acquisition decision tree distinct from the later player-facing market/contract path. Do not silently merge their rules.
- Do not change the intra-system TransitEngine boundary or add wormhole physics to this task.
- Prefer existing SystemOrchestrator and its components over introducing a parallel ResourceCoordinator service without a demonstrated need.

## Recommended successor task sequence

Create these as **drafts only**. They are not approved for backlog or dispatch.

1. **Inventory availability and reservation contract** — define and implement one authoritative available/reserved/committed quantity and atomic reserve/release/consume lifecycle on the existing inventory domain. Decide whether schema is required; remove mock/log-only reservation behavior.  
   **Unblocks:** acquisition and contract fulfillment.
2. **Unified per-settlement acquisition pipeline** — reconcile StateAnalyzer → StrategySelector → TaskExecutionEngineV2 → ServiceCoordinator → ResourceAcquisitionService with the pre-player tree and material-data contract. Make shortage detection, facility capability, local production, market/contract acquisition, EAP fallback, and delivery outcomes consistent with the era-specific policy. Do not create a second acquisition engine.  
   **Depends on:** 1 and the existing material-data contract; the Luna benchmark remains the proof gate.
3. **Wormhole route/cost input contract** — define a typed, source-grounded interface for route connectivity, direction, stability, remaining capacity, ETA, cost, and failure. Keep it separate from intra-system TransitEngine. No new physical equations or invented constants; stop where current physics leaves inputs unresolved.  
   **Depends on:** current natural-wormhole design decisions and source-model reconciliation.
4. **Logistics contract lifecycle** — select one canonical contract model/executor and connect provider selection, inventory reservation, route/cost inputs, cargo movement, delivery/failure, and GCC or NPC/DC ledger settlement. Retire or adapt duplicate status-only paths rather than adding another contract model.  
   **Depends on:** 1 and 3.
5. **SystemOrchestrator network coordination** — replace mock state with authoritative settlement snapshots; match surplus/deficit using local priorities and survival buffers; arbitrate against real availability; propose/dispatch through the canonical logistics lifecycle; persist or otherwise explicitly own system state; run once per system tick.  
   **Depends on:** 2, 3, 4, and a passing single-system Luna benchmark.
   
The final task should include an end-to-end acceptance scenario: at least two settlements in one system and two systems connected by a traversable wormhole; a real shortage is fulfilled from a surplus settlement; source stock is reserved; route cost/ETA is grounded; goods arrive and inventory changes; payment/ledger behavior follows policy; and failure/recovery does not duplicate or lose stock.

## Prior audit corrections

The 2026-06-30 SystemOrchestrator audit described integration as complete and the component architecture as structurally ready. Current main source does not support that conclusion: no production orchestrator construction was found, the settlement state is mocked, resource allocation only logs, and logistics scheduling cannot proceed. The earlier report's description of the two ResourceAllocator initializers is also imprecise: the later reopened-class initializer overrides the earlier one, leaving the old spec's keyword constructor incompatible.

The 2026-09-08 coordination summary identified multiple acquisition paths. This audit confirms that fragmentation remains and finds additional concrete mismatches: missing StateAnalyzer keys, an inverted resource-request condition, the unused pre-player tree, the nonexistent MaterialRequestService method, and conflicting completion metadata.

## Files updated during this review

- GalaxyGame wiki: [services/ai_manager.md](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/services/ai_manager.md) — clarified that source-class presence is not end-to-end verification and added the network-coordination maturity audit.
- agent-tasks: [GalaxyGame NEEDS_REVIEW](https://github.com/trmccormick/agent-tasks/blob/main/projects/galaxyGame/NEEDS_REVIEW.md) — added the acquisition-tree completion-claim conflict.
- agent-tasks: [GalaxyGame UI NEEDS_REVIEW](https://github.com/trmccormick/agent-tasks/blob/main/projects/galaxy-game-ui/NEEDS_REVIEW.md) — separately recorded possible Git-ignored local JSON duplicates in the UI prototype.

## Verification boundary

This report is based on static source and task-file inspection on GitHub main. No Docker commands, RSpec runs, database inspection, local checkout inspection, or runtime scenarios were performed. All statements about behavior are source-level findings, not claims of executed tests.
