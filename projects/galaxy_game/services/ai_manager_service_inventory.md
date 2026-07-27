# AI Manager Service Inventory

> **Last Updated**: 2026-07-26
> **Total Services**: 121 (93 ai_manager + 31 manufacturing + 1 terraforming import — 7 noise files excluded)
> **Related Domains**: Manufacturing (31 services), Terraforming (imported service)

## Overview

The AI Manager layer is the largest subsystem in galaxy_game, containing **121 Ruby files** across multiple directories: `app/services/ai_manager/` (93 files), `app/services/manufacturing/` (31 files including subdirectories), and `app/services/import/terrain_terraforming_service.rb` (1 file).

The AI Manager is responsible for autonomous decision-making across the game world: settlement management, mission planning, expansion, terraforming, wormhole infrastructure, market stabilization, and resource logistics. It orchestrates NPC behavior, procedural events, and complex multi-system coordination.

The entry point is `app/services/ai_manager.rb`, which acts as a require-bundler loading ~35 core files. Many other services exist independently and are loaded on-demand.

**Ground Truth**: 128 total `.rb` files found via `grep -l "^class\|^module"` — 7 noise files excluded (`ai_manager.rb` bundler, `errors.rb`, `testing.rb`, `testing/*` ×4) = **121 real services**. Of these, 53 were already documented; this update adds the remaining 68.

**MVP/Phase 2 Split**: 16 MVP + 52 Phase 2 = 68 newly audited services (plus 53 existing = 121 total).

**Ground Truth**: 128 total `.rb` files found via `grep -l "^class\|^module"` — 7 noise files excluded (`ai_manager.rb` bundler, `errors.rb`, `testing.rb`, `testing/*` ×4) = **121 real services**. Of these, 53 were already documented; this update adds the remaining 68.

**MVP/Phase 2 Split**: 16 MVP + 52 Phase 2 = 68 newly audited services (plus 53 existing = 121 total).

## Core Services (MVP-relevant)

### Settlement & Colony Management

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **OperationalManager** | `operational_manager.rb` | Main decision loop for settlements; orchestrates all other AI services | `make_decision`, `check_critical_priorities`, `assess_operational_state` | MVP |
| **SettlementManager** | `settlement_manager.rb` | Settlement strategy selection and resource coordination | `initialize_capabilities`, `collect_resource_requests` | MVP |
| **ColonyManager** | `colony_manager.rb` | NPC colony management + player colony auto-management | `manage_colonies`, `manage_npc_colonies`, `handle_player_trade` | MVP |
| **SuperMarsSettlementService** | `super_mars_settlement_service.rb` | Super Mars settlement patterns (moonless planet, large moon, depot building) | `moonless_planet_pattern`, `large_moon_pattern`, `choose_pattern` | MVP |
| **AiColonyManager** | `ai_manager/ai_colony_manager.rb` | Manages NPC colonies and player colony auto-management; delegates autonomous tasks to each colony | `add_colony`, `set_player_colony`, `manage_colonies` | MVP |
| **SettlementPlanGenerator** | `ai_manager/settlement_plan_generator.rb` | Generates enhanced settlement plans with asteroid tug integration for moon/asteroid targets; creates base plans with mission type, infrastructure, phases, crew requirements, and economic models | `generate_settlement_plan`, `create_base_plan`, `determine_mission_type` | Phase 2 |

### Mission & Expansion

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **MissionPlannerService** | `mission_planner_service.rb` | Mission simulation, timeline, cost calculation, sourcing strategy | `simulate`, `calculate_timeline`, `calculate_resource_requirements` | MVP |
| **ExpansionService** | `expansion_service.rb` | Multi-phase expansion with probe deployment and wormhole coordination | `expand_with_pattern`, `expand_with_intelligence`, `expand_with_network_awareness` | MVP |
| **ExpansionDecisionService** | `expansion_decision_service.rb` | Expansion decision logic | (see file) | MVP |
| **StrategicEvaluator** | `ai_manager/strategic_evaluator.rb` | Comprehensive strategic analysis of systems — classifies into categories (prize world, brown dwarf hub, wormhole nexus, resource world, frontier world) and calculates strategic value, risk, economic forecast, expansion priority | `evaluate_system`, `classify_system`, `calculate_strategic_value` | Phase 2 |
| **SurfaceSuitabilityAnalyzer** | `ai_manager/surface_suitability_analyzer.rb` | Evaluates surface suitability for landing/expansion using terrain_map data (elevation, resource_grid, biomes); scores individual grid cells and finds best sites in regions | `score`, `find_best_sites`, `score_entire_surface` | Phase 2 |

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

### Newly Audited Services (2026-07-26 Inventory Audit)

All 68 services audited across 5 batches. MVP = 16, Phase 2 = 52.

| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **AccessPointInstallationService** | `manufacturing/construction/access_point_installation_service.rb` | Schedules installation of access point units (airlocks, hatches) at lava tube locations; calculates materials and equipment needs based on access point size | `schedule_installation`, `calculate_materials`, `create_equipment_requests` | Phase 2 |
| **AiColonyManager** | `ai_manager/ai_colony_manager.rb` | Manages NPC colonies and player colony auto-management; delegates autonomous tasks to each colony | `add_colony`, `set_player_colony`, `manage_colonies` | MVP |
| **AiPrioritySystem** | `ai_manager/ai_priority_system.rb` | Singleton priority management system that applies multipliers to critical/operational priorities and checks settlement status for life support, atmosphere, and debt crises | `instance`, `effective_critical_priorities`, `check_critical`, `check_operational` | MVP |
| **AssemblyService** | `manufacturing/assembly_service.rb` | Handles blueprint assembly at settlements with material validation, tenant fee calculation, and optional missing material purchasing from NPC stock | `start_assembly`, `check_material_availability`, `calculate_tenant_fee` | Phase 2 |
| **Builder** | `ai_manager/builder.rb` | Executes construction plans from the AI by creating jobs for habitat modules, power plants, storage facilities, and domes based on recommended builds | `execute_construction_plan`, `can_build?`, `suggest_location` | MVP |
| **ByproductManufacturingService** | `manufacturing/byproduct_manufacturing_service.rb` | Generates O2 as a byproduct during silicon mining operations; configurable material-to-gas ratios for other byproducts | `process_mining_byproducts` | Phase 2 |
| **ComponentProductionService** | `manufacturing/component_production_service.rb` | Handles component production via 3D printers with printer capability validation, material resolution/substitution logic, ISRU chain triggering, and waste product management | `produce_component`, `complete_job` | Phase 2 |
| **AiManager::Construction** | `ai_manager/construction.rb` | Manages the construction queue, translating high-level plans from LLMPlannerService and emergency needs from DecisionTree into actionable jobs for the Builder component | `queue_construction_step`, `queue_emergency_fix`, `construction_status` | MVP |
| **Manufacturing::ConstructionManager** ⚠️ STUB | `manufacturing/construction/construction_manager.rb` | Assigns builders to construction entities and checks completion status — INCOMPLETE: both methods are stubs with TODO implementations, no real logic yet | `assign_builders`, `complete?` | Phase 2 |
| **CorporateRoles** | `ai_manager/corporate_roles.rb` | Defines corporate assignments (Zenith Orbital, Astrolift, LDC, Interstellar Shipping, Precursor Industries) and their capabilities for different mission types | `corporation_for_mission`, `capabilities_for_corporation` | Phase 2 |
| **CostCalculator** | `manufacturing/cost_calculator.rb` | Calculates Cost of Goods Sold (COGS) for manufactured units based on raw component costs and production waste factor; provides detailed cost breakdowns per material | `calculate_cogs`, `breakdown` | Phase 2 |
| **CoveringCalculator** | `manufacturing/construction/covering_calculator.rb` | Calculates materials needed for covering structures (skylights, domes) including I-beams, panels, fasteners, regolith, and sealant based on dimensions | `calculate_materials`, `get_skylight_dimensions`, `calculate_regolith_needed` | Phase 2 |
| **CoveringService** | `manufacturing/construction/covering_service.rb` | Schedules construction jobs for covering structures (skylights, domes) with panel type selection; calculates materials and creates material/equipment requests | `schedule_construction`, `start_construction`, `calculate_materials` | Phase 2 |
| **CraftFactory** | `manufacturing/craft_factory.rb` | Builds craft entities from blueprints with variant data support; determines correct craft class (satellite, heavy lander) and creates them with unique names | `build_from_blueprint`, `determine_craft_class` | Phase 2 |
| **CraterDomeConstructionService** ⚠️ STUB | `manufacturing/construction/crater_dome_construction_service.rb` | Creates construction jobs for crater domes — minimal implementation with only basic job creation and status updates, no real material/equipment logic | `construct`, `start_construction` | Phase 2 |
| **DecisionTree** | `ai_manager/decision_tree.rb` | Top-level AI decision-making prioritizing survival (life support), operational stability (resources), then expansion; uses priority heuristics and delegates to construction/builder services | `make_decisions`, `assess_settlement_state` | MVP |
| **DepotAdapter** | `ai_manager/depot_adapter.rb` | Creates or finds OrbitalSettlement depots for worlds; calculates orbital altitude based on celestial body properties — adapter for depot creation only | `create_depot`, `calculate_orbital_altitude` | Phase 2 |
| **DomeCalculator** | `manufacturing/construction/dome_calculator.rb` | Calculates materials needed for crater domes (hemisphere structures) using skylight calculator as base with dome-specific adjustments for curvature and depth | `calculate_materials`, `calculate_dome_adjustments`, `calculate_construction_cost` | Phase 2 |
| **DomeService** | `manufacturing/construction/dome_service.rb` | Schedules crater dome construction with player vs contractor payment handling; creates material/equipment requests based on who performs the construction | `schedule_construction`, `start_construction` | Phase 2 |
| **EquipmentManager** | `manufacturing/construction/equipment_manager.rb` | Releases fulfilled equipment requests back to inventory for construction jobs — minimal single-method utility | `release_equipment` | Phase 2 |
| **EquipmentRequest** | `manufacturing/equipment_request.rb` | Creates and fulfills equipment requests for construction jobs; transfers equipment from service provider inventory to settlement site | `create_equipment_requests`, `all_equipment_fulfilled?`, `fulfill_from_provider` | Phase 2 |
| **HammerProtocol** | `ai_manager/hammer_protocol.rb` | Schedules high-mass transit to breach wormhole mass threshold and force Sol-side exit shift; checks for legendary anomalies before execution | `execute`, `legendary_anomaly?` | Phase 2 |
| **HangarService** | `manufacturing/construction/hangar_service.rb` | Converts large access points into rover hangars with material calculation, construction job creation, and equipment requests — validates only large access points can be converted | `schedule_construction`, `start_construction` | Phase 2 |
| **Manager** | `ai_manager/manager.rb` | Main AI Manager entry point that orchestrates service coordination, economic metrics, strategy selection, and mission processing for settlements; delegates to ServiceCoordinator and StrategySelector | `advance_time`, `start_mission`, `acquire_resource`, `check_resource_availability` | MVP |
| **ManifestParser** | `ai_manager/manifest_parser.rb` | Parses JSON manifest files to extract craft fit data, inventory details (deployable units, supplies, consumables), and economic profiles (import ratios, estimated costs) | `extract_equipment_from_manifest`, `extract_craft_fit`, `extract_inventory`, `calculate_economics` | Phase 2 |
| **Manufacturing** ⚠️ EMPTY MODULE | `manufacturing.rb` | Empty namespace module — contains only a comment, no classes or methods; appears to be a require-bundler placeholder | *(none)* | Phase 2 |
| **ManufacturingService** | `manufacturing_service.rb` | Top-level manufacturing entry point that validates blueprints, checks licensing, calculates BOM costs via NpcPriceCalculator, and creates manufacturing jobs with material availability checks | `manufacture`, `check_materials`, `calculate_construction_cost` | Phase 2 |
| **Manufacturing::MaterialProcessing** | `manufacturing/material_processing.rb` | Processes raw regolith input by extracting gases (O2 from oxides), processing solids to base materials, and creating construction materials with compression/thermal/radiation properties | `process_material`, `extract_gases`, `process_solids` | Phase 2 |
| **Manufacturing::MaterialProcessingService** | `manufacturing/material_processing_service.rb` | Creates material processing jobs for specific unit types (volatile/thermal extraction); validates input inventory and creates Job records with production time estimates | `create_processing_job`, `process` | Phase 2 |
| **Manufacturing::MaterialRequest** | `manufacturing/material_request.rb` | Creates and fulfills material requests for construction jobs; checks inventory availability, withdraws materials, handles pressurization requests for enclosed environments | `create_material_requests`, `fulfill_request`, `cancel_request`, `create_pressurization_requests` | Phase 2 |
| **Manufacturing::MaterialRequestSystem** | `manufacturing/material_request_system.rb` | System-level material request orchestration that finds missing materials, creates requests, and triggers resource gathering (mine/refine/import) based on availability | `check_and_request`, `find_missing_materials`, `trigger_resource_gathering` | Phase 2 |
| **PriorityHeuristic** | `ai_manager/priority_heuristic.rb` | Evaluates settlement state against priority thresholds (O2 <15%, N2 <15%, negative accounts, high debt) and returns prioritized action list for the AI decision loop | `get_priorities`, `oxygen_critical?`, `nitrogen_critical?`, `account_negative?` | MVP |
| **Manufacturing::Processing** | `manufacturing/processing.rb` | Processes manufacturing by verifying GCC funds, deducting materials from owner inventory, creating unassembled items and byproducts based on blueprint — full transactional production pipeline | `process`, `verify_gcc`, `handle_materials`, `create_unassembled_items` | Phase 2 |
| **Manufacturing::ProductionService** ⚠️ PARTIAL STUB | `manufacturing/production_service.rb` | Orchestrates ISRU chain for final component production with PVE/TEU cycle calculations — heavily stubbed: only PVE metrics calculation works, all logistics/consumption steps are TODOs | `manufacture_component`, `run_unit_cycle` | Phase 2 |
| **Manufacturing::RegolithProcessingService** | `manufacturing/regolith_processing_service.rb` | Processes regolith using temperature-based extraction; extracts oxygen and creates processed regolith items in inventory with composition metadata — full transactional implementation | `process_with_temperature` | Phase 2 |
| **ResourceAllocator** | `ai_manager/resource_allocator.rb` | Allocates resources across settlements based on arbitrated requests; identifies potential transfers between settlements by matching needs to capabilities; includes default supply requirements for bootstrap | `allocate_resources`, `identify_transfers` | Phase 2 |
| **ResourceFlowSimulator** | `ai_manager/resource_flow_simulator.rb` | Models resource dependency chains and production timelines for Luna base development; defines build sequences (GCC satellite → Titan harvester → Venus harvester → lava tube base) with inputs/outputs/build times | `RESOURCE_CHAINS`, `calculate_production_timeline` | Phase 2 |
| **ResourcePlanner** | `ai_manager/resource_planner.rb` | Generates resource procurement plans by identifying shortfalls, determining priority (critical/high/medium), selecting procurement methods, and delegating to Fulfillment Service for execution | `generate_procurement_plan`, `execute_procurement_plan`, `resource_job_status` | MVP |
| **ScoutLogic** | `ai_manager/scout_logic.rb` | System-agnostic scouting analysis that extracts celestial bodies, detects primary characteristics, identifies terraformable/resource-rich/water bodies, calculates EM signatures, and enhances with probe data | `analyze_system_patterns`, `extract_celestial_bodies`, `detect_primary_characteristic` | Phase 2 |
| **SegmentCoveringService** | `manufacturing/construction/segment_covering_service.rb` | Subclass of CoveringService for covering Worldhouse segments; adds complexity factor for large-scale structures and calculates panel-specific materials (transparent aluminum, structural steel) with scaled equipment needs | `cover!`, `calculate_panel_specific_materials`, `calculate_equipment_requirements` | Phase 2 |
| **Manufacturing::Service** ⚠️ DEAD CODE | `manufacturing/service.rb` | Top-level manufacturing entry point that validates blueprints, checks affordability via settlement construction cost multiplier, and creates unit assembly jobs — functionally duplicates ManufacturingService at a different path; zero production callers | `manufacture`, `check_materials`, `consume_materials` | Phase 2 |
| **ServiceCoordinator** | `ai_manager/service_coordinator.rb` | Coordinates AI services (task execution, resource acquisition, scouting) via event-driven architecture; manages mission lifecycle (start/advance/status) and delegates to TaskExecutionEngine and ResourceAcquisitionService | `handle_event`, `start_mission`, `advance_mission`, `get_mission_status`, `acquire_resource` | MVP |
| **ServiceOrchestrator** | `ai_manager/service_orchestrator.rb` | Orchestrates service execution and priorities across the AI system; monitors service health, balances loads, optimizes priorities, and coordinates interdependent operations (resource acquisition with scouting, missions with resource support) | `orchestrate_services`, `execute_coordinated_operation`, `orchestration_status` | MVP |
| **SettlementPlanGenerator** | `ai_manager/settlement_plan_generator.rb` | Generates enhanced settlement plans with asteroid tug integration for moon/asteroid targets; creates base plans with mission type, infrastructure, phases, crew requirements, and economic models based on analysis input | `generate_settlement_plan`, `create_base_plan`, `determine_mission_type` | Phase 2 |
| **SharedContext** | `ai_manager/shared_context.rb` | Central event notification system for AI Manager — manages mission queue, resource requests, scouting results, active missions, and economic state; provides listener pattern for decoupled service communication | `add_listener`, `notify_listeners`, `queue_mission`, `request_resource`, `store_scouting_result` | MVP |
| **ShellPrintingService** | `manufacturing/shell_printing_service.rb` | Encloses inflatable tanks with regolith shell printing; validates tank readiness and printer capability, calculates shell thickness from atmosphere, consumes materials, creates shell printing jobs, and marks completion | `enclose_inflatable`, `print_shell`, `complete_job`, `validate_tank_ready` | Phase 2 |
| **SimEvaluator** ⚠️ PARTIAL STUB | `ai_manager/sim_evaluator.rb` | System Integration Mission Evaluator for complex orbital deployments using blueprint templates; Venus station pattern has full step-by-step logic but uses `puts` logging and TODO placeholders for actual construction queuing | `evaluate_system_integration`, `deploy_venus_pattern` | Phase 2 |
| **SkylightCalculator** | `manufacturing/construction/skylight_calculator.rb` | Legacy alias — simply re-exports CoveringCalculator as SkylightCalculator for backward compatibility; no unique logic of its own (see CoveringCalculator) | *(alias to CoveringCalculator)* | Phase 2 |
| **SkylightService** | `manufacturing/construction/skylight_service.rb` | Subclass of CoveringService for skylight covering with panel-specific materials (silicate glass, aluminum frame, ceramic composite); complexity factor varies by panel type; calculates equipment needs for lava tube skylights | `default_panel_type`, `calculate_panel_specific_materials`, `complexity_factor` | Phase 2 |
| **StateAnalyzer** | `ai_manager/state_analyzer.rb` | Analyzes settlement state including unfilled buy orders, inventory, surface storage, power availability, and cost analysis (import vs. produce locally) with cost pressure scoring; delegates to Settlements::CostAnalyzer for material-specific comparisons | `analyze_state`, `calculate_power_available`, `aggregate_cost_analysis` | Phase 2 |
| **StationCalculator** | `manufacturing/construction/station_calculator.rb` | Calculates materials needed for orbital station construction (cylindrical structures) including I-beams, modular panels, sealant, fasteners, and insulation based on radius/length/type; estimates construction time with complexity multipliers | `calculate_materials`, `estimate_construction_time` | Phase 2 |
| **StationConstructionService** ⚠️ STUB | `manufacturing/construction/station_construction_service.rb` | Orchestrates station shell and core module construction (airlocks, docking ports, utility connections) — stubbed: calculates materials via StationCalculator but actual job creation/equipment requests are TODOs with placeholder `puts` statements | `build_station_shell`, `install_airlock`, `install_docking_port` | Phase 2 |
| **StationConstructionStrategy** | `ai_manager/station_construction_strategy.rb` | Determines optimal station construction strategy by analyzing local resources, evaluating strategic requirements (wormhole anchor, resource processing, defensive position, trade hub, research outpost), and selecting via cost-benefit analysis | `determine_optimal_station_strategy`, `evaluate_station_type_suitability` | Phase 2 |
| **StationCostBenefitAnalyzer** | `ai_manager/station_cost_benefit_analyzer.rb` | Analyzes construction options by calculating financial metrics (NPV), operational benefits, risk adjustments, strategic alignment, and timeline efficiency; produces composite scores to rank and select optimal strategies | `select_optimal_strategy`, `analyze_construction_option`, `calculate_financial_metrics` | Phase 2 |
| **StationPlacementService** | `ai_manager/station_placement_service.rb` | Places AWS (Artificial Wormhole Station) 180° opposite the largest mass body in a system for synthetic stability; minimal but functional single-method implementation | `place_aws` | Phase 2 |
| **StrategicEvaluator** | `ai_manager/strategic_evaluator.rb` | Comprehensive strategic analysis of systems — classifies into categories (prize world, brown dwarf hub, wormhole nexus, resource world, frontier world) and calculates strategic value, risk, economic forecast, expansion priority, and development timeline | `evaluate_system`, `classify_system`, `calculate_strategic_value` | Phase 2 |
| **StrategySelector** | `ai_manager/strategy_selector.rb` | Main decision-making method that evaluates settlement state via StateAnalyzer, generates mission options, scores them via MissionScorer, performs strategic trade-off analysis, and selects optimal action (resource acquisition, scouting, expansion, infrastructure building, cost reduction) | `evaluate_next_action`, `execute_action`, `generate_mission_options`, `score_mission_options` | MVP |
| **SurfaceSuitabilityAnalyzer** | `ai_manager/surface_suitability_analyzer.rb` | Evaluates surface suitability for landing/expansion using terrain_map data (elevation, resource_grid, biomes); scores individual grid cells, finds best sites in regions, and scores entire surfaces with safe fallbacks for missing data | `score`, `find_best_sites`, `score_entire_surface` | Phase 2 |
| **SystemArchitect** ⚠️ PARTIAL STUB | `ai_manager/system_architect.rb` | Wormhole link ROI calculator that applies Sabatier refinements, calculates maintenance costs with multipliers, implements 120% threshold rule for negative ROI links, and triggers unilateral shift sequences — partially stubbed: core logic exists but `trigger_mass_dump` and `retrieve_assets_to_sol_anchor` are TODOs | `calculate_link_roi`, `execute_unilateral_shift_sequence`, `apply_sabatier_refinements` | Phase 2 |
| **SystemOrchestrator** | `ai_manager/system_orchestrator.rb` | System-wide orchestration coordinator that manages settlement managers, system state, resource allocation, priority arbitration, and logistics coordination across all settlements; registers/unregisters settlements and provides system status | `orchestrate_system`, `register_settlement`, `unregister_settlement`, `system_status` | MVP |
| **SystemState** | `ai_manager/system_state.rb` | Central state management for the AI system — tracks total resources, settlement states, system health, strategic objectives, dependencies, and economic balance; analyzes overall health and coordinates expansion plans across settlements | `update_from_settlements`, `analyze_system_health`, `update_strategic_objectives`, `coordinate_expansion` | MVP |
| **TaskExecutionEngine** | `ai_manager/task_execution_engine.rb` | Mission task execution engine that loads task lists and manifests, executes tasks sequentially with progress tracking, manages concurrent/paused tasks, and handles orbital resupply cycle management between L1 station and lunar settlements | `start`, `execute_next_task`, `orbital_resupply_cycle`, `check_material_surplus` | MVP |
| **TaskExecutionEngineV2** | `ai_manager/task_execution_engine_v2.rb` | Data-driven task execution engine that works with tasks_v2 JSON library; accepts target body and manifest/profile, loads environment and task library, plans tasks based on phases or capabilities, and parameterizes tasks for generic execution | `settle`, `plan_tasks`, `execute_tasks`, `extract_required_capabilities_from_manifest` | Phase 2 |
| **Import::TerrainTerraformingService** | `import/terrain_terraforming_service.rb` | Transformation rules mapping terraformed terrain back to barren terrain for different planet types (oceanic, temperate, arid, ice world); defines reverse maps for water features, vegetation, cold regions, and arid zones | `TERRAFORMING_REVERSE_MAPS`, `reverse_map` | Phase 2 |
| **TestScenarioExtractor** | `ai_manager/test_scenario_extractor.rb` | Extracts training scenarios from OperationalManager specs and mission manifests; creates structured scenario data with settlement states, expected decisions, and success criteria for critical life support, resource procurement, and expansion scenarios | `extract_training_scenarios`, `extract_patterns_from_missions` | Phase 2 |
| **Manufacturing::UnitDeployment** | `manufacturing/unit_deployment.rb` | Deploys units from blueprints or inventory items; creates unit records with operational data, configures ports and storage containers based on blueprint, initializes specialized functionality (power generators), and handles deployment from both direct creation and inventory consumption | `deploy`, `deploy_from_inventory`, `configure_unit` | Phase 2 |
| **Manufacturing::UnitModuleAssembly** | `manufacturing/unit_module_assembly.rb` | Assembles crafts from inventory items with recommended fit data; creates craft records, inventories, and builds units/modules from operational data; handles both instance and class method patterns for craft assembly | `build_units_and_modules`, `build_units_and_modules(target:, settlement_inventory:)` | Phase 2 |
| **WormholeCoordinator** | `ai_manager/wormhole_coordinator.rb` | Calculates optimal multi-system expansion routes via wormhole network graph analysis; coordinates parallel settlement development across systems with interdependency analysis, resource sharing opportunities, and coordination timelines | `calculate_optimal_routes`, `coordinate_parallel_development`, `build_wormhole_network_graph` | Phase 2 |

### Known Incomplete Services

These services have incomplete or stubbed implementations. All are flagged in the table above with ⚠️ markers.

| Service | File | Stub Level | Notes |
|---|---|---|---|
| **Manufacturing::ConstructionManager** | `manufacturing/construction/construction_manager.rb` | STUB | Both methods are TODOs, no real logic |
| **CraterDomeConstructionService** | `manufacturing/construction/crater_dome_construction_service.rb` | STUB | Only basic job creation/status updates |
| **StationConstructionService** | `manufacturing/construction/station_construction_service.rb` | STUB | Placeholder `puts` statements for actual construction logic |
| **Manufacturing::ProductionService** | `manufacturing/production_service.rb` | PARTIAL STUB | PVE metrics work; all logistics/consumption steps are TODOs |
| **SimEvaluator** | `ai_manager/sim_evaluator.rb` | PARTIAL STUB | Venus pattern has full steps but uses `puts` logging and TODO placeholders |
| **SystemArchitect** | `ai_manager/system_architect.rb` | PARTIAL STUB | Core ROI logic exists; `trigger_mass_dump` and `retrieve_assets_to_sol_anchor` are TODOs |

### Utility Calculators

Five calculator utilities in the manufacturing/construction namespace. All are fully implemented.

| Calculator | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| **CostCalculator** | `manufacturing/cost_calculator.rb` | Calculates COGS for manufactured units based on raw component costs and production waste factor | `calculate_cogs`, `breakdown` | Phase 2 |
| **CoveringCalculator** | `manufacturing/construction/covering_calculator.rb` | Calculates materials needed for covering structures (skylights, domes) including I-beams, panels, fasteners, regolith, and sealant | `calculate_materials`, `get_skylight_dimensions`, `calculate_regolith_needed` | Phase 2 |
| **DomeCalculator** | `manufacturing/construction/dome_calculator.rb` | Calculates materials needed for crater domes (hemisphere structures) using skylight calculator as base with dome-specific adjustments | `calculate_materials`, `calculate_dome_adjustments`, `calculate_construction_cost` | Phase 2 |
| **SkylightCalculator** | `manufacturing/construction/skylight_calculator.rb` | Legacy alias to CoveringCalculator for backward compatibility — no unique logic (see CoveringCalculator above) | *(alias)* | Phase 2 |
| **StationCalculator** | `manufacturing/construction/station_calculator.rb` | Calculates materials needed for orbital station construction (cylindrical structures) including I-beams, modular panels, sealant, fasteners, and insulation | `calculate_materials`, `estimate_construction_time` | Phase 2 |

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
