# Galaxy Game — Project Status & Task Tracking  
**Last Updated:** 2026-06-09 (Morning Session — ImportRequestGenerator Spec Fix Complete)

---

## Baseline & Test Status
- **RSpec Baseline (ai_manager suite):** 740 examples, 0 failures, 4 pending ✅ PRESERVED
- **Full Suite Regression Fixed:** Was 4 failures → Now back to baseline (2 known flaky specs only)
- **Last Full Suite Run:** June 9 morning — ImportRequestGenerator spec regression identified and fixed
- **Action for next session:** Check overnight full suite log before dispatching any tasks
  ```
  cat ~/phase3_full_suite.log
  ```
- **Flaky specs (never touch):**
  - `spec/services/construction/orbital_shipyard_service_spec.rb:129`
  - `spec/services/generators/game_data_generator_spec.rb:13`
  - `spec/services/lookup/material_lookup_service_spec.rb:251`
- **Branch:** main
- **Recent Milestone:** Luna Phase 3 complete — ShortageDetector + ImportRequestGenerator wired into AI Manager

---

## Active Tasks

| File | Agent | Status |
|---|---|---|
| `2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API.md` | Hold | Needs human decision — plural method uses wrong manifest API. Remove or fix? |

**Note:** ImportRequestGenerator spec keyword args regression (June 9 morning) ✅ FIXED and archived to completed/2026-06-09-spec-fixes/

---

## Luna Phase Queue

| Phase | Service | Status | Gate |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager integration | ✅ Complete (commit cd4d6800, archived f5fcbbe) | — |
| 2 | `Logistics::ManifestGenerator` → AI Manager integration | ✅ Complete (commit 21b10ef0) | Phase 1 complete ✅ |
| 3 | `Logistics::ShortageDetector` + `Logistics::ImportRequestGenerator` → AI Manager | ✅ Complete (commit 32b31f54) | Phase 1 & 2 complete ✅ |
| 4 | Consumption-aware ordering + precursor phase awareness | Backlog — ready to draft | Phase 3 complete ✅ |

---

## Completed

### 2026-06-09 Session (Morning — Spec Regression Fix)
- ✅ **ImportRequestGenerator spec keyword args fix** — Phase 3 regression resolved
  - Task: `tasks/completed/2026-06-09-spec-fixes/2026-06-09-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-SPEC-KEYWORD-ARGS.md`
  - Code commit: galaxyGame@9129ae80 (spec/services/logistics/import_request_generator_spec.rb)  
  - Changes: Updated spec calls from positional args to keyword args API (`source:, destination:, shortage:`)
  - Result: `import_request_generator_spec.rb`: 2 examples, 0 failures; AI Manager suite preserved at 740 examples, 0 failures, 4 pending

### 2026-05-29 Session
- ✅ Fixed factory identifier collision (DatabaseCleaner config, SecureRandom identifiers)
- ✅ Luna supply chain 12-question analysis (all answers with code evidence)
- ✅ Created 3 Luna implementation tasks in agent-tasks repo
- ✅ Committed archive cleanup and removed docs/agent from .gitignore

### 2026-05-30 Session
- ✅ **GMS Audit** — GuaranteedMarketSale service audit + NPC integration
  - Task: `tasks/completed/2026-05-29-MEDIUM-FEATURE-GUARANTEED-MARKET-SALE-LDC-BUYER.md`
  - Commit: 972185e

### 2026-06-02 Session
- ✅ **RSpec Failure Resolution** — 11 failures → 3 (all remaining confirmed flaky)
- ✅ **Task Triage System** established

### 2026-06-03 Session
- ✅ **Flaky spec verification** — 3 specs confirmed passing with correct invocation
- ✅ **ShortageDetector + ServiceCoordinator bug fixes** — JSONB string key + hash iteration fix
- ✅ **ImportRequestGenerator calculate_cost fix** — compare_costs fix, 2 examples 0 failures
- ✅ **Logistics models confirmed** — Manifest and ImportRequest fully implemented with migrations
- ✅ **Overnight full suite** — 3948 examples, 3 failures (all flaky). Baseline clean.

### 2026-06-05 Session
- ✅ **LOAD-SETTLEMENT fix** — TaskExecutionEngineV2#load_settlement find-or-create pattern
  - Commit: bug-fix: task_execution_engine_v2 — load_settlement find-or-create
  - Result: 722 examples, 0 failures
- ✅ **Luna Phase 1** — CostAnalyzer → AI Manager integration
  - Commits: cd4d6800 (code), f5fcbbe (task archived)
  - Files: state_analyzer.rb, strategy_selector.rb, strategy_selector_spec.rb
  - Result: 727 examples, 0 failures, 4 pending
  - CostAnalyzer API confirmed: `compare_costs(resource_name, settlement)`
  - Luna HLT transport method confirmed: `surface_conveyance`

### 2026-06-06 Session
- ✅ **Luna Phase 2** — ManifestGenerator → AI Manager integration
  - Commit: 21b10ef0
  - Files: strategy_selector.rb, strategy_selector_spec.rb
  - Changes:
    - Replace log-only execute_cost_reduction with real manifest creation via Logistics::ManifestGenerator
    - Add find_source_settlement using Cape Canaveral Spaceport as Earth source settlement
    - Add default_quantity_for returning 10 units per resource (Phase 2 stub)
    - Graceful fallback to logging when no source settlement available
    - ManifestError rescue path returns false and logs warning
  - Specs: Added 4 focused specs for execute_cost_reduction behavior
  - Result: 731 examples, 0 failures, 4 pending

### 2026-06-08 Evening Session (April Backlog Triage)
- ✅ **Orbital Market System Architecture Review** — April 2026 task superseded by implementation
  - Extracted: `2026-06-08-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md` (Phase 5+)  
  - Archived original to deprecated/ with header note explaining gas processing gap extraction
  
- ✅ **Orbital Structure Deployment Standardization Review** — April 2026 task partially implemented, needs completion
  - Extracted: `2026-06-08-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md` (Phase 5+ Prerequisite)  
  - Extracted: `2026-06-08-MEDIUM-FEATURE-MULTI-STRUCTURE-ROUTING-PER-SETTLEMENT.md` (Phase 6+)
  - Extracted: `2026-06-08-MEDIUM-FEATURE-DYNAMIC-ORBITAL-POSITION-SIMULATION.md` (Phase 6+)  
  - Archived original to deprecated/ with header note explaining task split rationale

**Total Tasks Created Tonight**: 4 new high-fidelity tasks across Phase 5+ and Phase 6+ folders
**Files Archived to Deprecated/**: 2 legacy April backlog items (`ai_manager_autonomous_expansion.md` from June 7th + orbital market system + orbital structure deployment)  
**Remaining in April Backlog**: ~47 files awaiting triage review

---

## Task Order & Priority Notes
- ✅ **Luna Phase 3** — ShortageDetector + ImportRequestGenerator → AI Manager
  - Commit: 32b31f54
  - Files: strategy_selector.rb, strategy_selector_spec.rb, import_request_generator.rb, service_coordinator.rb, manager.rb, manager_integration_spec.rb, manager_system_orchestrator_integration_spec.rb
  - Changes:
    - Replace default_quantity_for stub with real deficit quantities from ShortageDetector
    - Fix source/destination bug in ImportRequestGenerator (Cape Canaveral → Luna)
    - Change generate_import_request to keyword args signature
    - Fix ServiceCoordinator call site for new keyword args signature
    - Fix manager.rb advance_time using send for private ServiceCoordinator methods
    - Update integration specs for private method stubbing pattern
    - Add 8 Phase 3 focused specs
  - Result: 740 examples, 0 failures, 4 pending
- ✅ **Backlog triage** — 2 completed tasks confirmed and originals removed from backlog
- ✅ **Phase 5+ tasks moved** — 5 tasks moved to tasks/backlog/phase5+ subfolder
- ✅ **WormholeNavigator upgrade task drafted** — replaces stale topology task
- ✅ **April 2026 backlog reviewed** — ai_manager_autonomous_expansion.md archived to deprecated

---

## Task Order & Priority Notes
- Check overnight full suite log first thing next session
- PLURAL-API removal task on hold — human decision needed before reassessment
- Phase 4 task file needs drafting — consumption-aware ordering, precursor phases
- FootholdManager task needs scope update — Sol system settlements are first real use case
- Backlog growing faster than completions — apply Luna MVP filter before adding new tasks
- Agents use create instead of mv — always verify originals removed after agent file moves

---

## Special Warnings & Conventions
- Never commit code unless all RSpec tests are green
- Task management lives in `~/Documents/git/agent-tasks/` — galaxyGame git tracks code only
- Qwen3.5-27B on M4 is primary implementation worker
- Ryzen 7 4B is parallel task review/triage worker — read-only work only
- One model per session per machine — no switching (causes context accumulation + token limit errors)
- Cloud agents reserved for two-local-failure fallback only
- DECISIONS.md hardware/routing section is stale — see ROUTING_LOGIC.md and AGENT_ROUTING.md
- Git commits run on Intel MacBook host — never inside docker container
- Private methods on explicit receivers cause NoMethodError — use send() in production code

---

## Confirmed Architecture (as of 2026-06-07)

### AI Manager Decision Loop
- Entry: `AIManager::Manager#advance_time` → `@strategy_selector.evaluate_next_action`
- `advance_time` calls private ServiceCoordinator methods via send() — do not call on explicit receiver
- `StateAnalyzer#analyze_state` returns `cost_analysis: { viable:, cost_pressure:, recommendations: }`
- `StrategySelector` handles: `:resource_acquisition`, `:system_scouting`, `:settlement_expansion`, `:infrastructure_building`, `:cost_reduction`
- Strategic focus areas: `:resource_focus`, `:scouting_focus`, `:building_focus`, `:cost_focus`, `:balanced_approach`
- `EscalationService` entry point: `handle_expired_buy_orders` only — not in AI Manager decision loop
- `TaskExecutionEngineV2` is the active engine — `load_settlement` uses find-or-create

### Logistics Layer
- `Logistics::Manifest` — fully implemented, migration confirmed
- `Logistics::ImportRequest` — fully implemented, migration confirmed
- `Logistics::Contract` transport methods: `orbital_transfer`, `surface_conveyance`, `drone_delivery`, `direct_import`, `contracted_harvesting`
- Luna HLT transport method: `surface_conveyance` (confirmed from model)
- `EARTH_LUNA_TRANSIT_DAYS = 3` constant on `Logistics::Contract`
- `generate_import_request` (singular, keyword args) — confirmed canonical API
- `generate_import_requests` (plural) is live in ServiceCoordinator — do not remove without review
- Source/destination bug fixed — Cape Canaveral as source, Luna as destination

### CostAnalyzer
- `Settlements::CostAnalyzer.compare_costs(resource_name, settlement)` — confirmed public API
- Returns: `{ resource:, local_cost:, import_cost:, local_cheaper:, cost_delta:, recommendation:, confidence:, error: }`
- Raises `ResourceNotFoundError` if no blueprint exists
- Stub material costs (Iron=10, Coal=5, Oxygen=2) — TODO replace with constants (future task)

### Organizations (Seeded)
- **LDC** (Lunar Development Corporation) — development_corporation, GCC anchor/mint, sponsors new DCs
- **AstroLift** — orbital_logistics, owns Cape Canaveral, HLT operations, Earth→Luna→L1
- **Zenith Orbital** — station_construction, orbital station ops
- **Vector Hauling** — cargo_transport, interplanetary bulk cargo, outer Sol routes
- **WTC** (Wormhole Transit Consortium) — NOT seeded, forms during Snap Event storyline

### Supply Chain Architecture (Design Intent — not yet implemented)
- Earth launches → L1 Station (joint venture LDC + AstroLift, Zenith builds)
- Luna ISRU → L1 Station (regolith-derived metals, oxygen, structural materials)
- L1 → Cyclers (mobile space stations on continuous orbital arcs) → Mars/Venus/Belt
- Core cycler route: L1 → Mars (Phobos/Deimos depot) → Venus (orbital processing) → L1
- Outer routes: Mars ↔ Saturn/Titan — flexible, AI Manager optimizes, Vector Hauling operates
- Cyclers + Tugs assembled at L1 — design intent, not yet implemented
- Venus: orbital processing stations only, skimmers harvest atmosphere, return to L1
- Bootstrap packages for new settlements assembled at L1, delivered via cycler
- Skimmer overflow routing: L1 primary, secondary orbital depot if L1 full,
  Luna surface last resort for solid/regolith materials only
- Gas processing belongs at orbital stations (continuous solar power) NOT Luna surface
  (Luna has 2-week night — energy-intensive liquefaction/compression not viable)
- Cyclers are mobile space stations — smaller craft match velocity, dock briefly to
  transfer cargo/crew. Cycler never stops. Can serve as emergency refuge/waypoint.

### Corporate Structure (Design Intent)
- LDC sponsors new Development Corporations as network expands:
  MDC (Mars), TDC (Titan), BDC (Belt), VDC (Venus) — each seeded with GCC + tech transfer
- AstroLift — Earth→Luna→L1, owns HLTs, Cape Canaveral, cycler operations
- Zenith Orbital — builds and manages stations (L1, orbital depots, processing stations)
- Vector Hauling — outer Sol bulk cargo, natural operator for Mars/Titan/Belt routes
- LDC + AstroLift co-own L1 — combined control of all Sol supply chains
- WTC forms post-Snap from crisis collaboration — NOT seeded yet

### Consumption Model (Design Intent)
- `GameConstants::FOOD_PER_PERSON = 2`, `WATER_PER_PERSON = 1`, `ENERGY_PER_PERSON = 3`
- Crew rotation: 6 months (180 days) — ISS model
- Distance margin: Luna 15%, Mars 50%, Titan 100%
- Precursor phases: no life support consumption until human arrival gate cleared
- Phase 4 task will wire this into ShortageDetector survival targets

---

## Session Notes

### 2026-06-08
- Evening design discussion — no code changes
- Corporate structure clarified: LDC sponsors DCs (MDC, TDC, BDC, VDC) across Sol
- Gas processing clarified: orbital stations preferred (continuous solar), NOT Luna surface
- Skimmer overflow routing: L1 primary, secondary depot, Luna last resort for solids only
- Cycler architecture clarified: mobile space stations, smaller craft dock briefly
- Return cargo task committed by 27B as 4cad2f7 — use that version, discard Claude draft
- Vector Hauling confirmed as outer Sol cycler operator, Zenith builds stations
- Starting fresh session — this session context too long for reliable continued use

### 2026-06-07
- Phase 3 committed clean — 740 examples, 0 failures
- Full suite running overnight — check log before next session
- Overnight suite expected: 3957 examples, 3 failures (confirmed flaky only)
- Note: full suite grew from 3948 → 3957 examples after Phase 2+3 commits
- Flaky spec list updated — orbital_shipyard_service_spec:129 added, procedural_generator_spec:144 removed
- Workflow confirmed: M4 27B for implementation, Ryzen 7 4B for parallel task review
- Architecture discussions: cyclers as mobile space stations, L1 as central hub, LDC as DC sponsor
- Phase 5+ tasks moved to backlog/phase5+ subfolder — do not dispatch until Luna simulation calibrated
- Design intent vs locked architecture: cycler routes, L1 joint venture, FootholdManager scope all flexible pending simulation calibration

### 2026-06-06
- Qwen3.5-27B tool use restored — re-pull model weights after Ollama 0.30.x update
- Continue confirmed as fallback — friction with manual approvals, avoid sed for Ruby edits
- Single model workflow confirmed per session per machine

### 2026-06-05
- Small model tool call failures (9B on M4, 4B on Ryzen) — use 27B minimum for file/git work
- Leaner workflow confirmed: strategist drafts task files and handoffs directly
- Premium usage: 11% as of June 5th

### 2026-06-04
- EscalationService reviewed — no hook into StrategySelector
- surface_conveyance confirmed as Luna HLT transport method
- EARTH_LUNA_TRANSIT_DAYS = 3 noted for contract creation
- initiated_by polymorphic optional on Contract

### 2026-06-03 (Evening)
- ShortageDetector + ServiceCoordinator fixes committed
- Task lifecycle updated in agent-tasks. Documentation pushed.
