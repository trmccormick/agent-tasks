# Synthesis Report: AI Manager Service Inventory

**Date**: 2026-07-25
**Task**: 2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md

## Audit Summary

### File Counts
- **Total files in `app/services/ai_manager/`**: 89
- **Service/Manager classes (by find pattern)**: 60 across all domains
  - `ai_manager/`: ~53 service/manager files
  - `manufacturing/`: 14 service files (+ manufacturing_service.rb)
  - `import/terrain_terraforming_service.rb`: 1 file
- **No `luna/` or `super_mars/` subdirectories exist** under ai_manager/ (task gotcha was incorrect)
- **No `terraforming/` or `npc_economy/` directories exist** (task gotcha was incorrect)

### Key Discovery: The ai_manager.rb module file is a require-bundler
The `ai_manager.rb` file at the root of `app/services/ai_manager/` acts as a namespace loader, requiring ~35 files. Many services are NOT loaded through this file and exist independently.

### Service Categories Identified

#### Core AI Decision-Making (MVP-relevant)
1. **OperationalManager** — Main decision loop for settlements; orchestrates all other services
2. **SettlementManager** — Settlement strategy selection and resource coordination
3. **MissionPlannerService** — Mission simulation, timeline, cost calculation
4. **ExpansionService** — Multi-phase expansion with probe deployment and wormhole coordination
5. **TerraformingManager** — Planetary terraforming phase determination and gas calculations
6. **EconomicForecasterService** — Resource demand forecasting and scenario comparison
7. **MarketStabilizationService** — NPC buyer/producer/importer of last resort

#### Colony & Settlement Management (MVP-relevant)
8. **ColonyManager** — NPC colony management + player colony auto-management
9. **SuperMarsSettlementService** — Super Mars settlement patterns (moonless planet, large moon, depot building)
10. **ConsortiumManager** — Wormhole network health, orphaned system handling
11. **WormholeManager** — Wormhole mass monitoring, shift discharge, EM bloom harvesting
12. **FinancialService** — Financial calculations for settlements

#### Construction & Production (MVP-relevant)
13. **ConstructionService** — Facility building with resource checks
14. **ProductionManager** — Resource management for construction plans
15. **ProcurementService** — Local production vs market purchase decisions
16. **ResourceAcquisitionService** — Low-level resource ordering (local vs external)
17. **ResourceFulfillmentService** — Supply need fulfillment via MaterialRequestService

#### Probe & Exploration (Phase-deferred)
18. **ProbeDeploymentService** — Scout probe deployment for system analysis
19. **SystemDiscoveryService** — Discover and analyze available systems
20. **WormholeScoutingService** — Evaluate scouting opportunities, create artificial wormholes
21. **SystemIntelligenceService** — System status reporting, operational ratios, sustainability

#### Wormhole Infrastructure (Phase-deferred)
22. **WormholePlacementService** — Gravitational Lagrange point placement calculations
23. **TransitFeeService** — GCC transit fees and dividend distribution
24. **UniversalDockingService** — Universal docking between any entities
25. **SkimmerCyclerHandshakeService** — Skimmer-cycler cargo transfer protocols

#### Terraforming Pipeline (Phase-deferred)
26. **AtmosphericExtractionService** — Atmospheric skimmer extraction with raw transfer mode
27. **AtmosphericHarvesterService** — Harvester-specific atmospheric processing
28. **ResourcePositioningService** — AI-powered resource placement using terrain analysis

#### Pattern Learning & Knowledge (Phase-deferred)
29. **PrecursorCapabilityService** — Precursor technology capability assessment
30. **PrecursorLearningService** — Learned pattern application for precursor tech
31. **WorldKnowledgeService** — ISRU technology catalog and celestial body assessment
32. **PatternValidationService** / **PatternValidator** — Pattern validation logic
33. **PatternLoader** — Load trained patterns from data store
34. **PatternTargetMapper** — Map pattern names to target locations

#### Supporting Infrastructure (Phase-deferred)
35. **EscalationService** — Resource shortage handling, expired order escalation
36. **EmergencyMissionService** — Emergency mission creation and execution
37. **ContractCreationService** — AI contract generation
38. **LLMPlannerService** — LLM-based planning integration
39. **LogisticsCoordinator** — Multi-system logistics coordination
40. **NetworkOptimizer** — Network-aware optimization
41. **MultiWormholeEventHandler** — Multi-wormhole event handling
42. **PriorityArbitrator** — Priority conflict resolution
43. **PerformanceTracker** — Settlement performance tracking and adaptation
44. **ManifestParser** — Manifest parsing utilities
45. **PlanetaryMapGenerator** — Planetary map generation
46. **EarthMapGenerator** — Earth-specific map generation
47. **BootstrapResourceAllocator** — Bootstrap resource requirements calculation
48. **ISREvaluator** / **ISROptimizer** — ISRU evaluation and optimization
49. **DepotAdapter** — Depot interface adapter
50. **EMMissionCoordinatorService** — EM mission coordination
51. **ExpansionDecisionService** — Expansion decision logic

#### Non-Class Files (Utilities, config, errors)
- `ai_priority_system.rb` — AI priority system module
- `builder.rb` — Builder pattern utilities
- `construction.rb` — Construction module
- `corporate_roles.rb` — Corporate role definitions
- `decision_tree.rb` — Decision tree algorithm
- `errors.rb` — Error classes
- `hammer_protocol.rb` — Hammer protocol implementation
- `priority_heuristic.rb` — Priority heuristic algorithms
- `scout_logic.rb` — Scout logic module
- `resource_planner.rb` — Resource planning utilities
- `settlement_plan_generator.rb` — Settlement plan generation
- `sim_evaluator.rb` — Simulation evaluation
- `system_architect.rb` — System architecture utilities
- `task_execution_engine.rb` / `task_execution_engine_v2.rb` — Task execution engines
- `test_scenario_extractor.rb` — Test scenario extraction

## Structural Observations

1. **No subdirectory hierarchy** — All services live in a flat `app/services/ai_manager/` directory (89 files!)
2. **Mixed naming conventions** — Some use `_service.rb`, some `_manager.rb`, some just descriptive names
3. **Module namespace** — All classes are in the `AIManager` module
4. **No terraforming/ or npc_economy/ directories** exist as separate namespaces
5. **Manufacturing services** are a separate namespace (`app/services/manufacturing/`) but conceptually part of AI Manager domain

## Risks Identified
- 89 files in one directory is a maintenance concern (directory clutter)
- Mixed naming conventions make discovery harder
- Some services have placeholder implementations (e.g., ConstructionService has `true` placeholders)
- Several files loaded by ai_manager.rb are NOT found by the `_service.rb` or `_manager.rb` pattern
