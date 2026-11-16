# Review Folder — Content Type Analysis for Claude's Final Review

**Date**: 2026-07-04  
**Purpose**: Categorize all 113 loose files in `/tasks/review/` so Claude can efficiently determine what's useful vs discardable. No action taken — data only.

---

## Context

The review folder contains files from the early agent-coordination era (Haiku/GPT-4.1/Sonnet). Most represent abandoned work, but some may contain valuable feature specs or architecture decisions worth extracting into new tasks.

**Previous audit**: `2026-07-04-REVIEW-FOLDER-AUDIT-ACTIONABILITY.md` — confirmed 874 total review files, 98.4% stale/not actionable.

---

## Category Breakdown — 113 Loose Files in `/tasks/review/`

### A. Task-Style Files (2026-prefixed) — 45 files

These look like task files but are stale (not found in current locations). They may contain useful feature specs worth extracting.

#### HIGH-FEATURE / HIGH-ARCHITECTURE (~15 files)
*May contain valuable feature specs worth rewriting as new tasks.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `2026-02-11-HIGH-FEATURE-AI-MANAGER-SERVICE-INTEGRATION.md` | AI Manager integration spec | Check if still needed; likely superseded by SystemOrchestrator |
| `2026-03-26-HIGH-DATA-SOLARSYSTEM-CONNECTIVITY-STATUS.md` | Solar system connectivity tracking | Check if GGMap covers this |
| `2026-03-26-HIGH-FEATURE-MISSION-PLANNER-NO-MAGIC-SOURCING.md` | Mission planner sourcing | Check if already implemented in current codebase |
| `2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md` | Resource pricing architecture | Check if covered by DECISIONS.md |
| `2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md` | Marketplace on structures | Check if implemented; likely superseded |
| `2026-04-17-HIGH-ECONOMIC-EM-ECONOMY-PLANNER.md` | Economic planner spec | Check if covered by EconomicForecasterService |
| `2026-04-17-HIGH-MACRO-WORMHOLE-STABILITY-MONITOR.md` | Wormhole stability monitoring | Check if covered by Phase 15+ planning |
| `2026-04-17-HIGH-MESO-WORLDHOUSE-CONSTRUCTION.md` | Worldhouse construction spec | Check if covered by Phase 6+ |
| `2026-04-17-HIGH-MESO-WORLDHOUSE-FAILURE-ANALYSIS.md` | Worldhouse failure analysis | Check if covered by terraforming docs |
| `2026-04-17-HIGH-MESO-WORLDHOUSE-MAINTENANCE.md` | Worldhouse maintenance spec | Check if covered by terraforming docs |
| `2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md` | Orbital resupply refactor | Check if already done |
| `2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md` | Settlement deployment refactor | Check if covered by current codebase |
| `2026-06-01-HIGH-FEATURE-WORMHOLE-EASTER-EGG-INTEGRATION.md` | Wormhole easter egg feature | Likely low-priority; discard unless Claude wants it |
| `2026-06-17-HIGH-FEATURE-PHASE6-TERRAFORMING-CALCULATION-INFRASTRUCTURE.md` | Terraforming calculation infra | Check if covered by Phase 6+ |
| `2026-06-22-HIGH-FEATURE-GGMAP-SCIENTIFIC-DATA-DISPLAY.md` | GGMap data display spec | Check if covered by current GGMap work |
| `2026-06-22-HIGH-FEATURE-GGMAP-STRATEGIC-DATA-DISPLAY.md` | GGMap strategic display spec | Check if covered by current GGMap work |

> **Claude note**: These 15 HIGH files are the most likely to contain useful specs. Focus here first.

#### HIGH-BUGFIX / MEDIUM-BUG-FIX (~8 files)
*Bug fixes that were resolved without a tracked task.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md` | Blueprint port fix | Bug likely fixed; discard |
| `2026-04-01-HIGH-BUGFIX-FITTING-SERVICE-INVENTORY-PARITY.md` | Fitting service parity | Bug likely fixed; discard |
| `2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md` | PVE volatiles fix | Bug likely fixed; discard |
| `2026-04-04-MEDIUM-BUG-FIX-GAME-ADVANCE-BY-DAYS-NEGATIVE-GUARD.md` | Negative guard fix | Bug likely fixed; discard |
| `2026-02-11-CRITICAL-BUGFIX-ADMIN-MONITOR-TURBO-LOADING.md` | Admin monitor loading fix | Bug likely fixed; discard |
| `2026-04-01-MEDIUM-BUG-FIX-ITEM-REGOLITH-GEOSHERE.md` | Regolith geosphere fix | Already in deprecated/ |

> **Claude note**: These are almost certainly already resolved. Quick scan only.

#### LOW-DATA / LOW-DOCUMENTATION (~5 files)
*Schema definitions or documentation audits.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md` | CO2/O2 schema | Check if covered by ISRU docs |
| `2026-04-03-LOW-DOCUMENTATION-AI-MANAGER-FILE-AUDIT-CLASSIFY-ALL-SERVICES.md` | AI Manager audit | Likely superseded; discard |
| `2026-06-22-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-OPERATIONAL-SCHEMA.md` | CO2/O2 operational schema | Check if covered by ISRU docs |
| `2026-05-23-LOW-FEATURE-FINANCIAL-TRANSACTION-ENUM-AND-SPEC.md` | Financial transaction enum | Check if implemented |

> **Claude note**: Schema files may contain useful data models. Quick scan for patterns.

#### ADVANCED-CLAUDE (~4 files)
*Claude-specific instructions from the early agent era.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `2026-04-17-ADVANCED-CLAUDE-DEFINE-GGMAP-FORMAT.md` | GGMap format spec | Check if covered by current GGMap docs |
| `2026-04-17-ADVANCED-CLAUDE-IMPLEMENT-AI-MANAGER-OPERATIONAL-ESCALATION.md` | AI Manager escalation impl | Likely superseded; discard |
| `2026-04-17-ADVANCED-CLAUDE-OPTIMIZE-MISSION-PLANS-AI-TRAINING.md` | Mission plan optimization | Check if patterns still apply |
| `2026-04-17-ADVANCED-CLAUDE-REFINE-MISSION-AI-MANAGER-ALIGNMENT.md` | Mission alignment spec | Check if patterns still apply |

> **Claude note**: These were Claude-specific instructions. May contain useful patterns but likely superseded by current workflow.

#### RESEARCH / ANALYSIS (~5 files)
*Research findings that may contain useful patterns.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `2026-06-11-MEDIUM-RESEARCH-AI-MARKET-CALIBRATION-SERVICE.md` | AI market calibration research | Check if findings still relevant |
| `2026-06-12-MEDIUM-RESEARCH-AI-MANAGER-TRAINING-PIPELINE-AUDIT.md` | AI training pipeline audit | Check if findings still relevant |
| `2026-05-29-ANALYSIS-MARKET-SYSTEM-GUARANTEED-SALE-INTEGRATION.md` | Market system analysis | Check if findings still relevant |
| `2026-06-26-HIGH-RESEARCH-STRATEGY-SELECTOR-ESCALATION.md` | Strategy selector research | **Already in completed/** — work done |

> **Claude note**: These may contain useful patterns or findings. Worth a quick scan for reusable insights.

#### MEDIUM-FEATURE / LOW-FEATURE (~5 files)
*Feature specs that were never formalized into tasks.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `2026-03-24-MEDIUM-FEATURE-SCOUT-SEISMIC-SURVEY.md` | Scout seismic survey feature | Check if still needed |
| `2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md` | Multi-system resource coordination | Check if covered by SystemOrchestrator |
| `2026-06-07-MEDIUM-FEATURE-WORMHOLE-TOPOLOGY-INTEGRATION.md` | Wormhole topology integration | Check if covered by Phase 15+ |
| `2026-06-12-LOW-FEATURE-PROCEDURAL-MAP-GENERATION-QUALITY.md` | Map generation quality | Check if still needed |

> **Claude note**: These are feature specs that were never formalized. Quick scan for useful concepts.

---

### B. Non-Task Files (no date prefix) — 68 files

These are NOT task files — they're notes, scripts, planning docs, and one-off ideas.

#### Agent/Workflow Scripts (~5 files)
*Agent workflow instructions from the early era.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `TASK_DOCS_AGENT_CLEANUP.md` | Agent cleanup task doc | Check if still valid |
| `TASK_GUARDRAILS_SPLIT.md` | Guardrails split task doc | Check if covered by current GUARDRAILS.md |
| `TASK_WEDNESDAY_SURGICAL_AUDIT.md` | Audit workflow doc | Check if still relevant |

> **Claude note**: These are agent workflow instructions. Likely superseded by current workflow docs in `/docs/new_agent/`.

#### Feature Ideas / Brainstorming (~15 files)
*Brainstorming notes that were never formalized into tasks.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `dc_bond_financing_system.md` | DC bond financing concept | Brainstorming; discard unless Claude identifies valuable concept |
| `meaningful_activities_vs_eve_grind.md` | Game design philosophy | Brainstorming; discard unless Claude wants to preserve |
| `player_automation_systems.md` | Player automation concept | Brainstorming; discard unless Claude identifies actionable item |
| `population_morale_wellbeing_system.md` | Population morale system | Brainstorming; discard unless Claude identifies valuable concept |
| `avoiding_eve_online_pitfalls.md` | Game design notes | Brainstorming; discard unless Claude wants to preserve |
| `complete_vision_philosophy.md` | Vision philosophy doc | Brainstorming; discard unless Claude wants to preserve |

> **Claude note**: These are brainstorming notes. Discard unless Claude identifies a valuable concept worth preserving.

#### Architecture Notes (~10 files)
*Architecture discussion notes that may contain decisions not in DECISIONS.md.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `ai_autonomous_expansion_mvp_focus.md` | AI expansion MVP focus | Check if covered by DECISIONS.md |
| `ai_resource_allocation_engine.md` | Resource allocation engine notes | Check if covered by DECISIONS.md |
| `systemorchestrator_development.md` | SystemOrchestrator dev notes | Check if covered by current docs |
| `ai_multi_wormhole_learning_event.md` | Wormhole learning event notes | Check if covered by Phase 15+ planning |
| `ai_wormhole_integration.md` | Wormhole integration notes | Check if covered by DECISIONS.md |
| `ai_site_selection_algorithm.md` | Site selection algorithm notes | Check if covered by current codebase |
| `ai_strategic_evaluation_algorithm.md` | Strategic evaluation notes | Check if covered by current codebase |
| `ai_system_discovery_implementation.md` | System discovery implementation notes | Check if covered by current codebase |
| `ai_manager_task1_scss_layout.md` | AI Manager SCSS layout | Superseded; discard |
| `ai_manager_task2_performance_data.md` | AI Manager performance data | Superseded; discard |

> **Claude note**: These may contain architecture decisions not in DECISIONS.md. Quick scan for missing decisions.

#### Implementation Plans (untracked) (~12 files)
*Untracked implementation plans that were never formalized into tasks.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `create_ai_manager_landing_page.md` | AI Manager landing page plan | Superseded; discard |
| `complete_admin_dashboard_navigation_integration.md` | Admin dashboard integration | Superseded; discard |
| `redesign_admin_celestial_bodies_edit_page.md` | Admin page redesign | Superseded; discard |
| `refactor_css_structure.md` | CSS refactor plan | Superseded; discard |
| `add_ai_manager_priority_controls.md` | Priority controls feature | Check if implemented |
| `add_solar_system_monitor_route.md` | Solar system monitor route | Check if implemented |
| `add_admin_celestial_body_show_view.md` | Admin celestial body view | Check if implemented |

> **Claude note**: These are untracked implementation plans. Likely superseded by current UI work. Discard unless Claude identifies actionable items.

#### Service/Feature Specs (~8 files)
*Feature specs that were never turned into tasks.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `digital_twin_service.md` | Digital twin service spec | Check if covered by current codebase |
| `dynamic_wormhole_pathfinding_service.md` | Wormhole pathfinding service | Check if covered by Phase 15+ |
| `mining_automation_harvesters_ai.md` | Mining automation spec | Check if still needed |
| `megaproject_service_manufacturing_pipeline.md` | Manufacturing pipeline spec | Check if covered by current codebase |
| `dynamic_logistics_refactor.md` | Logistics refactor plan | Check if covered by current codebase |
| `eap_market_integration_completion.md` | EAP market integration | Check if implemented |
| `market_decoupling_logic.md` | Market decoupling logic | Check if covered by DECISIONS.md |
| `marketplace_lookup_fix.md` | Marketplace lookup fix | Bug likely fixed; discard |

> **Claude note**: These are feature specs that were never formalized. Quick scan for useful concepts.

#### UI/Design Notes (~5 files)
*UI design notes that are likely superseded by current UI work.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `planetary-view-phase1.md` | Planetary view design | Superseded; discard |
| `regional-view-phase2.md` | Regional view design | Superseded; discard |
| `settlement-view-phase3.md` | Settlement view design | Superseded; discard |
| `2026-04-17-ADVANCED-CLAUDE-DEFINE-GGMAP-FORMAT.md` | GGMap format spec | Already listed above |

> **Claude note**: These are UI design notes. Likely superseded by current GGMap work. Discard.

#### Cleanup/Maintenance Scripts (~5 files)
*Maintenance tasks that may already be done.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `easter_egg_system_cleanup.md` | Easter egg cleanup | Check if already done |
| `remove_obsolete_poro_storage_classes.md` | Poro storage cleanup | Check if already done |
| `refactor_internal_resource_keys_to_chemical_formulas.md` | Resource key refactor | Check if already done |
| `fix_terrain_grid_rendering_mismatch.md` | Terrain grid fix | Bug likely fixed; discard |
| `fix_biome_validation_button_monitor.md` | Biome validation fix | Bug likely fixed; discard |

> **Claude note**: These are maintenance tasks. Likely already done. Quick scan only.

#### Integration Notes (~3 files)
*Integration notes that may be complete.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `courier_handshake_integration.md` | Courier handshake integration | Check if implemented |
| `scouting_recovery_integration.md` | Scouting recovery integration | Check if implemented |
| `fix_ai_mission_control_section_monitor.md` | Mission control monitor fix | Bug likely fixed; discard |

> **Claude note**: These are integration notes. Likely complete. Quick scan only.

#### Miscellaneous (~15 files)
*General notes that are likely discardable.*

| File | Likely Intent | Claude Action |
|------|--------------|---------------|
| `base_craft_model_refactor.md` | Base craft model refactor | Check if covered by current codebase |
| `blueprint_polymorphic_ownership.md` | Blueprint ownership notes | Check if covered by DECISIONS.md |
| `craft_configuration_system_review.md` | Craft configuration review | Check if still relevant |
| `develop_nasa_data_acquisition_pipeline.md` | NASA data pipeline | Likely low-priority; discard |
| `expand_celestial_body_coverage.md` | Celestial body coverage | Check if covered by current codebase |
| `implement_geotiff_auto_detection_system.md` | GeoTIFF auto-detection | Check if implemented |
| `implement_phosphorus_mechanics.md` | Phosphorus mechanics | Check if implemented |
| `implement_protoplanet_terrain_generation.md` | Protoplanet terrain gen | Check if covered by Phase 5+ |
| `implement_time_resource_simulation.md` | Time/resource simulation | Check if covered by current codebase |
| `separate_biome_data_from_geosphere.md` | Biome data separation | Check if covered by current codebase |
| `settlement_gcc_account_convenience_method.md` | Settlement GCC account method | Likely superseded; discard |
| `refactor_wormhole_generation_job_for_starsim_rules.md` | Wormhole generation refactor | Check if covered by Phase 15+ |
| `archive_critical_terrain_data_assets.md` | Terrain data archive | Likely done; discard |
| `complete_mvp_planning_framework.md` | MVP planning framework | Superseded; discard |
| `complete_terrain_data_system.md` | Terrain data system | Check if covered by current codebase |

> **Claude note**: These are miscellaneous notes. Likely discardable unless Claude identifies valuable concepts.

---

## Summary for Claude's Review

### What to Focus On (Priority Order)

1. **HIGH-FEATURE / HIGH-ARCHITECTURE files** (~15 files) — Most likely to contain useful feature specs worth rewriting as new tasks
2. **Architecture Notes** (~10 files) — May contain decisions not in DECISIONS.md
3. **RESEARCH / ANALYSIS files** (~5 files) — May contain useful patterns or findings
4. **Service/Feature Specs** (~8 files) — May contain useful concepts worth formalizing

### What Can Be Safely Discarded

- All `reorganization attempt 2/` content (abandoned reorg, no actionable items)
- All stale task files (not found in current locations, work was completed or abandoned)
- All brainstorming/brainstorming-style notes (never formalized into tasks)
- All UI/design notes (superseded by current UI work)
- All bug fix files (bugs likely resolved without tracking)
- All maintenance/cleanup scripts (likely already done)

### Estimated Disposition

| Category | Count | Likely Disposition |
|----------|-------|-------------------|
| Task-style files (2026-prefixed) | 45 | **~35-40 discardable**; **~5-10 may contain useful specs** |
| Non-task files (no date prefix) | 68 | **~60+ discardable**; **~5-8 may contain valuable concepts** |
| **TOTAL** | **113** | **~95-100 discardable**; **~13-18 may contain useful content** |

---

*Analysis generated: 2026-07-04*  
*Purpose: Data for Claude's final review — no action taken*
