# AI Manager Service Inventory

> **Last Updated**: 2026-07-25
> **Total Services**: 89 files in `app/services/ai_manager/` (flat directory)
> **Related Domains**: Manufacturing (14 services), Terraforming (imported service)

## Overview

The AI Manager layer is the largest subsystem in galaxy_game, containing **89 Ruby files** all within a single flat directory (`app/services/ai_manager/`). Despite the task documentation suggesting hierarchical subdirectories (e.g., `super_mars/`, `luna/`), all services live at one depth level.

The AI Manager is responsible for autonomous decision-making across the game world: settlement management, mission planning, expansion, terraforming, wormhole infrastructure, market stabilization, and resource logistics. It orchestrates NPC behavior, procedural events, and complex multi-system coordination.

The entry point is `app/services/ai_manager.rb`, which acts as a require-bundler loading ~35 core files. Many other services exist independently and are loaded on-demand.

## Core Services (MVP-relevant)

### Settlement & Colony Management

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **OperationalManager** | `operational_manager.rb` | Main decision loop for settlements; orchestrates all other AI services | `make_decision`, `check_critical_priorities`, `assess_operational_state` | MVP |
| **SettlementManager** | `settlement_manager.rb` | Settlement strategy selection and resource coordination | `initialize_capabilities`, `collect_resource_requests` | MVP |
| **ColonyManager** | `colony_manager.rb` | NPC colony management + player colony auto-management | `manage_colonies`, `manage_npc_colonies`, `handle_player_trade` | MVP |
| **SuperMarsSettlementService** | `super_mars_settlement_service.rb` | Super Mars settlement patterns (moonless planet, large moon, depot building) | `moonless_planet_pattern`, `large_moon_pattern`, `choose_pattern` | MVP |

### Mission & Expansion

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **MissionPlannerService** | `mission_planner_service.rb` | Mission simulation, timeline, cost calculation, sourcing strategy | `simulate`, `calculate_timeline`, `calculate_resource_requirements` | MVP |
| **ExpansionService** | `expansion_service.rb` | Multi-phase expansion with probe deployment and wormhole coordination | `expand_with_pattern`, `expand_with_intelligence`, `expand_with_network_awareness` | MVP |
| **ExpansionDecisionService** | `expansion_decision_service.rb` | Expansion decision logic | (see file) | MVP |

### Economy & Market

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **EconomicForecasterService** | `economic_forecaster_service.rb` | Resource demand forecasting and scenario comparison | `analyze`, `forecast_resource_demand`, `compare_scenarios` | MVP |
| **MarketStabilizationService** | `market_stabilization_service.rb` | NPC buyer/producer/importer of last resort for market liquidity | `stabilize_market`, `ensure_new_player_essentials`, `handle_unsold_goods` | MVP |
| **FinancialService** | `financial_service.rb` | Financial calculations for settlements | (see file) | MVP |
| **ConsortiumManager** | `consortium_manager.rb` | Wormhole network health, orphaned system handling, AWS construction | `handle_orphaned_prize_system`, `cold_start_external_ignition` | MVP |

### Construction & Production

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **ConstructionService** | `construction_service.rb` | Facility building with resource checks | `build_facility`, `can_build?`, `prepare_shell_for_cycler_arrival` | MVP |
| **ProductionManager** | `production_manager.rb` | Resource management for construction plans | `manage_resources_for_construction`, `calculate_required_materials` | MVP |
| **ProcurementService** | `procurement_service.rb` | Local production vs market purchase decisions | `procure_resource`, `can_produce_locally?` | MVP |
| **ResourceAcquisitionService** | `resource_acquisition_service.rb` | Low-level resource ordering (local vs external import) | `order_acquisition`, `check_expired_orders`, `is_local_resource?` | MVP |
| **ResourceFulfillmentService** | `resource_fulfillment_service.rb` | Supply need fulfillment via MaterialRequestService | `fulfill_supply_need` | MVP |

### Terraforming

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **TerraformingManager** | `terraforming_manager.rb` | Planetary terraforming phase determination and gas calculations | `determine_terraforming_phase`, `calculate_gas_needs`, `has_magnetosphere_protection?` | MVP |
| **AtmosphericExtractionService** | `atmospheric_extraction_service.rb` | Atmospheric skimmer extraction with raw transfer mode | `execute_extraction`, `dock_and_transfer_to_cycler` | Phase 2 |
| **AtmosphericHarvesterService** | `atmospheric_harvester_service.rb` | Harvester-specific atmospheric processing | (see file) | Phase 2 |

### Wormhole Infrastructure

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **WormholeManager** | `wormhole_manager.rb` | Wormhole mass monitoring, shift discharge, EM bloom harvesting | `monitor_and_trigger_shift`, `execute_shift_discharge`, `harvest_em_bloom` | Phase 2 |
| **WormholePlacementService** | `wormhole_placement_service.rb` | Gravitational Lagrange point placement calculations | `calculate_optimal_placement`, `calculate_gravitational_potential` | Phase 2 |
| **WormholeScoutingService** | `wormhole_scouting_service.rb` | Evaluate scouting opportunities, create artificial wormholes | `evaluate_scouting_opportunities`, `create_scouting_wormhole` | Phase 2 |
| **TransitFeeService** | `transit_fee_service.rb` | GCC transit fees and dividend distribution | `charge_fee`, `calculate_fee`, `distribute_dividends` | Phase 2 |
| **UniversalDockingService** | `universal_docking_service.rb` | Universal docking between any entities (craft, station, base) | `dock`, `compatible_ports?`, `transfer_payload` | Phase 2 |
| **SkimmerCyclerHandshakeService** | `skimmer_cycler_handshake_service.rb` | Skimmer-cycler cargo transfer protocols | `dock_skimmer`, `process_cargo`, `execute_atmospheric_extraction` | Phase 2 |

### Probe & Exploration

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **ProbeDeploymentService** | `probe_deployment_service.rb` | Scout probe deployment for system analysis | `deploy_scout_probes`, `deploy_probe` | Phase 2 |
| **SystemDiscoveryService** | `system_discovery_service.rb` | Discover and analyze available systems | `discover_available_systems`, `discover_systems_in_range` | Phase 2 |
| **SystemIntelligenceService** | `system_intelligence_service.rb` | System status reporting, operational ratios, sustainability | `system_status`, `narrative_status`, `licensing_runway` | Phase 2 |

### Pattern Learning & Knowledge

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **PrecursorCapabilityService** | `precursor_capability_service.rb` | Precursor technology capability assessment | (see file) | Phase 2 |
| **PrecursorLearningService** | `precursor_learning_service.rb` | Learned pattern application for precursor tech | (see file) | Phase 2 |
| **WorldKnowledgeService** | `world_knowledge_service.rb` | ISRU technology catalog and celestial body assessment | `ISRU_TECHNOLOGIES`, `celestial_body=` | Phase 2 |
| **PatternValidationService** | `pattern_validation_service.rb` | Pattern validation logic | (see file) | Phase 2 |
| **PatternValidator** | `pattern_validator.rb` | Pattern validation algorithms | (see file) | Phase 2 |
| **PatternLoader** | `pattern_loader.rb` | Load trained patterns from data store | (see file) | Phase 2 |
| **PatternTargetMapper** | `pattern_target_mapper.rb` | Map pattern names to target locations | (see file) | Phase 2 |

### Escalation & Emergency Response

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **EscalationService** | `escalation_service.rb` | Resource shortage handling, expired order escalation | `handle_resource_shortage`, `handle_expired_buy_orders` | MVP |
| **EmergencyMissionService** | `emergency_mission_service.rb` | Emergency mission creation and execution | (see file) | MVP |
| **ContractCreationService** | `contract_creation_service.rb` | AI contract generation | (see file) | Phase 2 |

### Logistics & Coordination

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **LogisticsCoordinator** | `logistics_coordinator.rb` | Multi-system logistics coordination | (see file) | Phase 2 |
| **NetworkOptimizer** | `network_optimizer.rb` | Network-aware optimization | (see file) | Phase 2 |
| **MultiWormholeEventHandler** | `multi_wormhole_event_handler.rb` | Multi-wormhole event handling | (see file) | Phase 2 |

### Decision Support & Analytics

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **MissionProfileAnalyzer** | `mission_profile_analyzer.rb` | Mission profile analysis | (see file) | Phase 2 |
| **MissionScorer** | `mission_scorer.rb` | Mission scoring and evaluation | (see file) | Phase 2 |
| **PriorityArbitrator** | `priority_arbitrator.rb` | Priority conflict resolution | (see file) | Phase 2 |
| **PerformanceTracker** | `performance_tracker.rb` | Settlement performance tracking and adaptation | `get_adapted_decision_recommendation` | Phase 2 |
| **ISREvaluator** | `isru_evaluator.rb` | ISRU evaluation | (see file) | Phase 2 |
| **ISROptimizer** | `isru_optimizer.rb` | ISRU optimization | (see file) | Phase 2 |

### Map Generation

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **PlanetaryMapGenerator** | `planetary_map_generator.rb` | Planetary map generation | (see file) | Phase 2 |
| **EarthMapGenerator** | `earth_map_generator.rb` | Earth-specific map generation | (see file) | Phase 2 |
| **ResourcePositioningService** | `resource_positioning_service.rb` | AI-powered resource placement using terrain analysis | `place_resources_on_map` | Phase 2 |

### Bootstrap & Planning Utilities

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **BootstrapResourceAllocator** | `bootstrap_resource_allocator.rb` | Bootstrap resource requirements calculation | `calculate_bootstrap_requirements` | Phase 2 |
| **LLMPlannerService** | `llm_planner_service.rb` | LLM-based planning integration | (see file) | Phase 2 |
| **EMMissionCoordinatorService** | `em_mission_coordinator_service.rb` | EM mission coordination | (see file) | Phase 2 |

## Supporting Services (Non-Class Files)

These files are loaded by `ai_manager.rb` but are utilities, modules, or configuration rather than standalone services:

| File | Type | Purpose |
|---|---|---|
| `ai_priority_system.rb` | Module | AI priority system for decision weighting |
| `builder.rb` | Utility | Builder pattern utilities |
| `construction.rb` | Module | Construction module |
| `corporate_roles.rb` | Config | Corporate role definitions |
| `decision_tree.rb` | Algorithm | Decision tree algorithm |
| `errors.rb` | Config | Error class definitions |
| `hammer_protocol.rb` | Protocol | Hammer protocol implementation |
| `priority_heuristic.rb` | Algorithm | Priority heuristic algorithms |
| `scout_logic.rb` | Module | Scout logic module |
| `resource_planner.rb` | Utility | Resource planning utilities |
| `settlement_plan_generator.rb` | Utility | Settlement plan generation |
| `sim_evaluator.rb` | Utility | Simulation evaluation |
| `system_architect.rb` | Utility | System architecture utilities |
| `task_execution_engine.rb` | Engine | Task execution engine v1 |
| `task_execution_engine_v2.rb` | Engine | Task execution engine v2 |
| `test_scenario_extractor.rb` | Test utility | Test scenario extraction |
| `depot_adapter.rb` | Adapter | Depot interface adapter |
| `manifest_parser.rb` | Utility | Manifest parsing utilities |

## Subdomain Breakdown

### Super Mars Settlement
- **SuperMarsSettlementService** — Core settlement patterns (moonless planet, large moon, depot building)
- **ColonyManager** — Manages NPC colonies and player colony auto-management
- **SettlementManager** — Strategy selection and resource coordination for settlements

### Terraforming Pipeline
- **TerraformingManager** — Central terraforming coordinator; determines phases and calculates gas needs
- **AtmosphericExtractionService** — Skimmer-based atmospheric extraction with raw transfer mode
- **AtmosphericHarvesterService** — Harvester-specific atmospheric processing
- **ResourcePositioningService** — AI-powered resource placement on generated maps

### Wormhole Network
- **WormholeManager** — Mass monitoring, shift discharge, EM bloom harvesting
- **WormholePlacementService** — Gravitational Lagrange point calculations for optimal placement
- **WormholeScoutingService** — Scouting opportunity evaluation and artificial wormhole creation
- **TransitFeeService** — Fee collection and dividend distribution
- **UniversalDockingService** — Universal docking protocol between any entities
- **SkimmerCyclerHandshakeService** — Skimmer-cycler cargo transfer protocols
- **ConsortiumManager** — Network health monitoring and orphaned system handling
- **MultiWormholeEventHandler** — Multi-wormhole event coordination

### Expansion & Exploration
- **ExpansionService** — Multi-phase expansion with probe deployment and wormhole coordination
- **ProbeDeploymentService** — Scout probe deployment for intelligence gathering
- **SystemDiscoveryService** — System discovery within wormhole range
- **SystemIntelligenceService** — System status reporting and sustainability analysis

### Mission Planning
- **MissionPlannerService** — Full mission simulation with timeline, resources, costs, sourcing
- **MissionProfileAnalyzer** — Mission profile analysis
- **MissionScorer** — Mission scoring and evaluation
- **LLMPlannerService** — LLM-based planning integration

### Economy & Market
- **EconomicForecasterService** — Resource demand forecasting and scenario comparison
- **MarketStabilizationService** — NPC market stabilization (buyer/producer/importer of last resort)
- **FinancialService** — Financial calculations for settlements
- **ProcurementService** — Local production vs market purchase decisions

### Construction & Production
- **ConstructionService** — Facility building with resource validation
- **ProductionManager** — Resource management for construction plans
- **ResourceAcquisitionService** — Low-level resource ordering (local vs external)
- **ResourceFulfillmentService** — Supply need fulfillment via MaterialRequestService

### Pattern Learning & Knowledge Base
- **PrecursorCapabilityService** — Precursor technology capability assessment
- **PrecursorLearningService** — Learned pattern application for precursor tech
- **WorldKnowledgeService** — ISRU technology catalog and celestial body assessment
- **PatternValidationService** / **PatternValidator** — Pattern validation logic and algorithms
- **PatternLoader** — Load trained patterns from data store
- **PatternTargetMapper** — Map pattern names to target locations

### Escalation & Emergency Response
- **EscalationService** — Resource shortage handling, expired order escalation
- **EmergencyMissionService** — Emergency mission creation and execution
- **ContractCreationService** — AI contract generation for resource acquisition

### Decision Support & Analytics
- **PriorityArbitrator** — Priority conflict resolution across competing needs
- **PerformanceTracker** — Settlement performance tracking with adaptation recommendations
- **ISREvaluator** / **ISROptimizer** — In-situ resource utilization evaluation and optimization
- **LogisticsCoordinator** — Multi-system logistics coordination
- **NetworkOptimizer** — Network-aware optimization

## Service Dependency Graph

```
OperationalManager (main decision loop)
├── AiPrioritySystem (priority weighting)
├── WorldKnowledgeService (ISRU knowledge)
├── PerformanceTracker (adaptation recommendations)
├── MarketStabilizationService (market health)
├── EscalationService (resource shortages)
├── ExpansionService (expansion decisions)
├── FinancialService (financial checks)
└── EmergencyMissionService (emergency response)

ExpansionService
├── ProbeDeploymentService (intelligence gathering)
├── BootstrapResourceAllocator (resource requirements)
├── WormholeCoordinator (network coordination)
└── SettlementPlanGenerator (plan generation)

MissionPlannerService
├── PrecursorCapabilityService (local resource availability)
├── PatternTargetMapper (target location resolution)
├── EconomicForecasterService (cost analysis)
└── MaterialLookupService (material pricing)

TerraformingManager
├── PatternLoader (terraforming patterns)
├── AtmosphericExtractionService (gas calculations)
└── ResourcePositioningService (resource placement)

WormholeManager
├── StationPlacementService (AWS placement)
├── TransitFeeService (fee collection)
└── ConsortiumManager (network health)

SkimmerCyclerHandshakeService
├── AtmosphericExtractionService (extraction execution)
└── UniversalDockingService (docking protocol)

ProductionManager
├── ResourceAcquisitionService (resource ordering)
├── ConstructionService (facility building)
└── ProcurementService (procurement decisions)

ResourceFulfillmentService
└── MaterialRequestService (market procurement)
```

## How to Add a New Service

See: [Adding an AI Manager Service](./adding-ai-manager-service.md)
