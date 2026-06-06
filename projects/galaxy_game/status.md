# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-06-06

---

## Baseline & Test Status
- **RSpec Baseline (ai_manager suite):** 727 examples, 0 failures, 4 pending
- **Last Full Suite Baseline:** 3948 examples, 3 failures (all confirmed flaky — do not fix)
- **Action for next session:** Run full suite to confirm 3948+ examples still clean after Phase 2 commits
- **Flaky specs (never touch):**
  - `spec/services/generators/game_data_generator_spec.rb:13`
  - `spec/services/lookup/material_lookup_service_spec.rb:251`
  - `spec/services/star_sim/procedural_generator_spec.rb:144`
- **Branch:** main
- **Recent Milestone:** Luna Phase 2 complete — ManifestGenerator wired into AI Manager decision loop

---

## Active Tasks

| File | Agent | Status |
|---|---|---|
| `2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API.md` | Hold | Needs human review — `generate_import_requests` (plural) confirmed live in ServiceCoordinator. Reassess removal. |

---

## Luna Phase Queue

| Phase | Service | Status | Gate |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager integration | ✅ Complete (commit cd4d6800, archived f5fcbbe) | — |
| 2 | `Logistics::ManifestGenerator` → AI Manager integration | ✅ Complete (just committed) | Phase 1 complete ✅ |
| 3 | `Logistics::ShortageDetector` + `Logistics::ImportRequestGenerator` → AI Manager | Backlog — ready to plan | Phase 1 & 2 complete ✅ |

---

## Completed

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
  - Commit: feature: Luna Phase 2 — ManifestGenerator integration into AI Manager (just pushed)
  - Files: strategy_selector.rb, strategy_selector_spec.rb
  - Changes:
    - Replace log-only execute_cost_reduction with real manifest creation via Logistics::ManifestGenerator
    - Add find_source_settlement using Cape Canaveral Spaceport as Earth source settlement
    - Add default_quantity_for returning 10 units per resource (Phase 2 stub)
    - Graceful fallback to logging when no source settlement available
    - ManifestError rescue path returns false and logs warning
  - Specs: Added 4 focused specs for execute_cost_reduction behavior

---

## Task Order & Priority Notes
- Run full RSpec suite at next session start to confirm clean baseline after Phase 2 commits
- Luna phases must be implemented sequentially: Phase 1 ✅ → Phase 2 ✅ → Phase 3 (next)
- Phase 3 task file needs drafting — ShortageDetector + ImportRequestGenerator integration into AI Manager
- PLURAL-API removal task still on hold — needs human decision before reassessment
- PLURAL-API removal task still on hold — needs human decision before reassessment
- Small models (9B, 4B) had tool call failures on 2026-06-05 — use 27B minimum for file/git work

---

## Special Warnings & Conventions
- Never commit code unless all RSpec tests are green
- Task management lives in `/Documents/git/agent-tasks/` — galaxyGame git tracks code only
- Cloud agents (GPT-5 mini, Raptor mini) are 0.33x tier — reserved for two-local-failure fallback only
- Qwen3.5-27B is the primary implementation worker
- DECISIONS.md hardware/routing section is stale — see ROUTING_LOGIC.md and AGENT_ROUTING.md (committed 2026-06-03)

---

## Confirmed Architecture (as of 2026-06-05)

### AI Manager Decision Loop
- Entry: `AIManager::Manager#advance_time` → `@strategy_selector.evaluate_next_action`
- `StateAnalyzer#analyze_state` now returns `cost_analysis: { viable:, cost_pressure:, recommendations: }`
- `StrategySelector` handles: `:resource_acquisition`, `:system_scouting`, `:settlement_expansion`, `:infrastructure_building`, `:cost_reduction` (Phase 1)
- Strategic focus areas: `:resource_focus`, `:scouting_focus`, `:building_focus`, `:cost_focus`, `:balanced_approach`
- `EscalationService` entry point: `handle_expired_buy_orders` only — not in AI Manager decision loop
- `TaskExecutionEngineV2` is the active engine — `load_settlement` now uses find-or-create

### Logistics Layer
- `Logistics::Manifest` — fully implemented, migration confirmed
- `Logistics::ImportRequest` — fully implemented, migration confirmed
- `Logistics::Contract` transport methods: `orbital_transfer`, `surface_conveyance`, `drone_delivery`, `direct_import`, `contracted_harvesting`
- Luna HLT transport method: `surface_conveyance` (confirmed from model)
- `EARTH_LUNA_TRANSIT_DAYS = 3` constant on `Logistics::Contract`
- `generate_import_requests` (plural) is live — called by ServiceCoordinator. Do not remove without review.

### CostAnalyzer
- `Settlements::CostAnalyzer.compare_costs(resource_name, settlement)` — confirmed public API
- Returns: `{ resource:, local_cost:, import_cost:, local_cheaper:, cost_delta:, recommendation:, confidence:, error: }`
- Raises `ResourceNotFoundError` if no blueprint exists
- Stub material costs (Iron=10, Coal=5, Oxygen=2) — TODO replace with constants (future task)

---

## Session Notes

### 2026-06-05
- Full suite not run — only ai_manager suite (727 examples). Schedule full run at next session start.
- Small model tool call failures (9B on M4, 4B on Ryzen) — use 27B minimum for any file/git work.
- Leaner workflow confirmed: strategist drafts task files and handoffs directly, no intermediate agent overhead. Synthesis report review before approval is the key gate worth keeping.
- Premium usage: 11% as of June 5th — healthy runway for rest of month.
- Phase 2 (ManifestGenerator) is unblocked. Task file to be drafted next session — drop ManifestGenerator file to strategist at startup.

### 2026-06-04
- EscalationService reviewed — no hook into StrategySelector. Phase 1 clear of it.
- surface_conveyance confirmed as Luna HLT transport method from Logistics::Contract model.
- EARTH_LUNA_TRANSIT_DAYS = 3 noted for Phase 2/3 contract creation.
- initiated_by polymorphic optional on Contract — set to settlement/AI Manager when creating contracts in Phase 2+.

### 2026-06-03 (Evening)
- ShortageDetector + ServiceCoordinator fixes committed.
- Task lifecycle updated in agent-tasks. Documentation pushed.
