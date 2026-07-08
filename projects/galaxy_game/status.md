# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-07-04 — Planning Agent (Claude web + Qwen Ryzen) — full day multi-session

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.

---

## Project History (background, rarely changes)
- **~10+ years ago**: earliest concepts in Java.
- **~2 years ago**: research and prototyping phase.
- **~Feb–Mar 2026**: RSpec stabilization period — large backlog spike generated during
  this crunch. Two debris types: (1) silently-resolved tasks fixed incidentally without
  closing the file; (2) incomplete concepts never finished as ideas. Do not assume
  "stale" means "already handled" — check which kind it is.
- **Mar 2026–present**: MVP focus (Luna precursor build, AI Manager service integration).

---

## Baseline & Test Status
- **RSpec Baseline (full suite):** 4097 examples, 7 failures — run 2026-07-04
  - 4 confirmed flaky (never touch): `game_data_generator_spec.rb:13`,
    `material_lookup_service_spec.rb:251`, `orbital_shipyard_service_spec.rb:129`,
    `procedural_generator_spec.rb:144` (passes 3/3 targeted but failed overnight — genuine flaky)
  - 3 accepted new failures (SurfaceSuitabilityAnalyzer Phase 1 scope limits):
    - `surface_suitability_analyzer_spec.rb:71` — buildability :too_steep for flat
      terrain; slope calc logic bug, task file in `backlog/current/`
    - `surface_suitability_analyzer_spec.rb:152 + :157` — Atmosphere model has no
      `name` attribute; spec schema wrong, task file in `backlog/current/`
  - **escalation_service_spec.rb:429** — ✅ FIXED 2026-07-04 (hash_including matcher),
    committed and pushed
  - **procedural_generator_spec.rb:144** — confirmed flaky (added to confirmed list)
- **Suite growth:** 4059 → 4097 (+38 examples)
- **Branch:** main
- **Material identifier convention (locked):** chemical formulas (`O2`, `H2O`, `N2`,
  `CH4`) are the Ruby codebase standard. Display names are JSON/UI only.

---

## Active Tasks

### 📌 Phase 4 Robot Logistics — ACTIVE (2026-07-05)
- Task file moved from `backlog/phase5+/` → `active/phase5+/` and status updated to `active`
- Committed: `move: Phase 4 robot logistics task from backlog to active (status: active)`
- Implementation in progress — creating V2 task files, phase definition, wiring into profile

### 📌 Backlog reorganization — completed (Qwen session 2026-07-04)
- Moved 6 stale task files from backlog root to correct phase folders:
  - `2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md` → `phase5+/` (Luna robots)
  - `2026-06-30-LOW-PHASE-8B-DEFERRAL-WORMHOLE-AND-ECONOMICS.md` → `phase14+/` (post-wormhole Eden)
  - `2026-07-03-MEDIUM-DOCUMENTATION-CLEANUP-LEGACY-AGENT-REPO.md` → `deferred-cleanup/`
  - 3 Phase 8 specs → `phase8+/`
- Created `current/` folder for urgent fixes (3 files moved in)
- Created `deferred-cleanup/` folder for non-urgent cleanup tasks
- Moved 6 files from `backlog/deprecated/` → `tasks/deprecated/`, removed empty `backlog/deprecated/`
- Removed `reorganization_attempt_3/` folder (empty, only README)

### 📌 Review folder audit — completed (Qwen session 2026-07-04)
- Analyzed 874 files across `review/`, `backlog_april_2026/`, `reorganization attempt 2/`
- Only **14 of 874 (1.6%)** found in current locations — rest are stale
- Created two summary reports:
  - `summaries/2026-07-04-REVIEW-FOLDER-AUDIT-ACTIONABILITY.md` — actionability assessment
  - `summaries/2026-07-04-REVIEW-FOLDER-CONTENT-TYPE-CATEGORIZATION.md` — content type breakdown for Claude's review
- **Key finding**: `reorganization attempt 2/` is confirmed not needed (abandoned reorg)
- **Claude note**: Focus on HIGH-FEATURE/HIGH-ARCHITECTURE files first (~15 files may contain useful specs)

### 📌 Misplaced Phase 8 files — source identified, Claude has updates
- Source of stale Phase 8 task files: `refactored-task-files/2026-06-30-TASK-UPDATE-1` (stale task generation)
- Claude has a few updates before corrected files can be moved to `phase8+/`

### 📌 GUARDRAILS.md consolidation — completed (Ryzen session)

---

## V2 Mission System — Implementation Ready (2026-07-07)

Both tasks successfully cleaned up and ready for dispatch. No more duplicate content or path confusion.

- **`2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md`** (backlog/current) — Rake path resolution only
  - Scope: Update `luna_mission.rake` with `missions_v2/` path resolution + legacy fallback, verify 17 tasks still hold
  - Status: ✅ READY — removed old rejected engine-scope, kept Claude-approved rake-only work
  
- **`2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md`** (backlog/current) — Registry creation only
  - Scope: Create `phase_registry.json` AI Manager lookup index (4 phases, 17 Luna tasks already done)
  - Status: ✅ READY — removed out-of-scope work, clarified registry-only scope, updated all paths to `missions_v2/`

---

## Luna Phase Queue

| Phase | Service | Status | Gate |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager | ✅ Complete (`cd4d6800`) | — |
| 2 | `Logistics::ManifestGenerator` → AI Manager | ✅ Complete (`21b10ef0`) | Phase 1 ✅ |
| 3 | `Logistics::ShortageDetector` + `ImportRequestGenerator` | ✅ Complete (`32b31f54`) | Phase 1+2 ✅ |
| 4 | Consumption-aware ordering + precursor phase awareness | ✅ Complete (`5a2af17a`) | Phase 3 ✅ |
| 5 | Simulation observation — market emergence, stockpile accumulation | 🔄 Underway | Phase 4 ✅ |

**Phase 5+ onward** (canonical, per `DEVELOPMENT_ROADMAP.md`):
- Phase 5+: Luna simulation calibration/observation (current focus, no new features)
- Phase 6+: L1/LEO Depot + gas processing pipeline (gated on Phase 5+ complete)
- Phase 9+: Shipyard, Phobos/Deimos hollowing, wormhole topology (gated on Act 2 Eden maturity)

---

## Backlog Structure (updated 2026-07-04)

New folders established this session:
- `backlog/current/` — urgent fixes outside phase structure (2 HIGH SurfaceSuitabilityAnalyzer tasks)
- `backlog/deferred-cleanup/` — non-urgent housekeeping (1 file: legacy agent repo cleanup)
- `backlog/phase8+/` — received 3 spec tasks (SystemOrchestrator, LogisticsCoordinator, PriorityArbitrator)
- `backlog/phase14+/` — received wormhole/economics deferral doc
- `backlog/deprecated/` — REMOVED (merged into `tasks/deprecated/`, 6 files migrated)
- `backlog/reorganization_attempt_3/` — REMOVED (only contained README)

Task file movements this session:
- Phase 4 Robot Logistics → `phase5+/` (robots needed for Luna settlement)
- Multi-Settlement Orchestrator Spec + 2 related specs → `phase8+/`
- Wormhole/Economics deferral → `phase14+/`
- Legacy agent repo cleanup → `deferred-cleanup/`

---

## Parked / Not Yet Filed

- **Review folder audit complete** — 874 files analyzed, 98.4% stale. Two summary
  reports in `summaries/`: `2026-07-04-REVIEW-FOLDER-AUDIT-ACTIONABILITY.md` and
  `2026-07-04-REVIEW-FOLDER-CONTENT-TYPE-CATEGORIZATION.md`. Claude to review ~13-18
  HIGH-FEATURE/ARCHITECTURE files for salvageable specs. `reorganization_attempt_2/`
  confirmed not needed — abandoned reorg. `backlog_april_2026/` is the authoritative source.
- **has_facility? refactor** — task created in `backlog/phase6+/`. Not blocking
  current work. Fix: replace dead `has_facility?` abstraction with direct unit checks
  in `NpcPriceCalculator` and `ProcurementService`.
- **Luna equipment provisioning task** — ARCHIVED to `tasks/deprecated/` (2026-07-04).
  Task was based on flawed assumption. Architecture already handles equipment via
  DRAFT manifest `required_hardware` → rake → settlement inventory. No resources
  section was ever designed for mission profiles.
- **TASK-luna-mvp-ai-manager-settlement.md** — ready to dispatch; implements real V2
  stub handlers + non-`rand()` settlement-siting decision function. Read
  `AI-MANAGER-LUNA-BEHAVIOR-GOALS.md` before dispatching.
- **Shell-printing/tank-stage-advancement gap (3c)** — confirmed real missing feature.
  Needs own implementation task. Do not mistake as resolved.
- **Landing pad insertion (3d)** — `task_surface_preparation_unit_operations.json`
  covers it (metadata only); agreed insertion point is end of Phase 3. Report-only,
  not wired in.
- **Drone Bay Operational Data** — task file `2026-06-19-LOW-DESIGN-DRONE-BAY-OPERATIONAL-DATA.md`
  created, not yet copied to repo. LOW priority.
- **AI-MANAGER-SERVICE-INTEGRATION placeholder** — confirmed empty template; archive
  to `deprecated/` decision made, `git mv` not yet executed.
- **Titan atmospheric transfer task file** — creation unconfirmed; verify before
  assuming it exists.
- **ROUTING_LOGIC.md** — mid-revision since 2026-06-16; not finalized.
- **L1/LEO Depot task** (Perplexity research handoff) — not yet reviewed.
- **Image asset generation** — `GALAXY-GAME-IMAGE-ASSETS.md` style guide complete;
  Cycler priority-10 component images still need ChatGPT generation time.
- **HLT payload capacity gaps** — task file created, not yet dispatched. 150,000 kg
  payload baseline adopted (Starship reusable-mode).
- **Deposit/Spawning gap** — `Deposit::PlausibilityEngine` + `DepositSpawner` designed,
  never implemented. Explicitly deprioritized below Luna MVP.

---

## Completed (recent — see handoffs for full detail)

| Date | Item | Commit |
|---|---|---|
| 2026-07-08 | Profiles V2 canonical implementation — rake path resolution | `836b0998` |
| 2026-07-04 | escalation_service_spec.rb:429 matcher fix (hash_including) | committed + pushed |
| 2026-07-04 | Luna equipment provisioning task archived to deprecated/ | — |
| 2026-07-04 | Backlog structure reorganized (current/, deferred-cleanup/, phase folders) | — |
| 2026-07-04 | Review folder audit — 874 files analyzed, reports in summaries/ | — |
| 2026-07-03 | Hub Bus Routing — register_to_bus engine action | `bd549c15` |
| 2026-07-03 | Sabatier Reactor JSON files + methane.json update | `1daf2ab4` |
| 2026-07-03 | strategy_selector.rb duplicate method removed | `7ea5bd44` |
| 2026-07-03 | SurfaceSuitabilityAnalyzer Phase 1 service | `a2c6a680` |
| 2026-07-03 | cell_num NameError fixed in SurfaceSuitabilityAnalyzer | `e59cce02` |
| 2026-07-03 | sabatier_reactor_spec.rb paths fixed + redundant tests removed | committed |
| 2026-07-03 | escalation_integration_spec.rb O2/H2O formula fix | committed |
| 2026-07-03 | GUARDRAILS consolidation committed (verification pending) | Ryzen session |
| 2026-07-02 | LegacyPortAdapter task closed | `e196ff4` |
| 2026-07-02 | Luna Precursor V2 Sequence Reconciliation closed | rake 13/13 confirmed |
| 2026-07-01 | Phase C (StrategySelector→V2 bridge) | `4441a74d` |
| 2026-07-01 | EscalationService resource-shortage handling | `50f70486` |
| 2026-06-29 | GCC Account Refactor Phase 1 | `2bf82245` |
| 2026-06-29 | `advance_deployment_stages` dispatch fix | committed |
| 2026-06-27 | LegacyPortAdapter core (Steps 1–4) | `b3beff34` + `fd0b96d7` |
| 2026-06-23 | Docs reorganization | `415805b7` |

---

## Today's Work (2026-07-05 — Planning Agent)

### Phase 8 Task Reorganization — completed
- Updated handoff sections on all 7 Phase 8 tasks with lifecycle + synthesis MD pattern
- Moved deferral task from refactored-task-files to phase14+ with Dependencies section
- Renamed and moved summary files to summaries/ folder (TASK_REFACTOR_SUMMARY → 2026-06-30-SUMMARY-TASK-REFACTOR-COMPLETION.md)
- Moved systemorchestrator_development.md review file to deprecated/
- Deleted refactored-task-files/2026-06-30-TASK-UPDATE-1/ (all stale copies removed)
- Committed + pushed: c84865b

### Emergency Mission System Design Task — completed
- Created `backlog/design/2026-07-05-LOW-ARCHITECTURE-EMERGENCY-MISSION-SYSTEM-DESIGN.md`
- EmergencyMissionService exists (not missing) — post-player-entry design work, not Phase 5 bugfix
- Renamed from RESEARCH to ARCHITECTURE type per TASK_TEMPLATE convention
- Moved from tasks/design/ to backlog/design/ for consistency with other design tasks
- Deleted stale fix_ai_manager_escalation_dependencies.md from phase5+
- Committed + pushed: bb244b3

### Template Fixes — completed (earlier session)
- Updated TASK_TEMPLATE.md handoff section with lifecycle instructions (backlog → active → completed)
- Added synthesis MD file pattern to all task files (replaced "in chat" pattern)
- Committed + pushed: 47a22dc


### Skimmer Intent Doc Update — completed (Qwen session 2026-07-05)
- Updated `docs/architecture/intent/skimmer_craft_intent.md` processing model
- Section 2 rewritten: "Limited Onboard, Mostly Raw Delivery" with phases table
- Clarified onboard output is fuel self-sufficiency only (LOX/CH4 for skimmer's own tanks)
- Section 3 expanded with Storage Contribution and Surface Contingency details
- Committed + pushed: a7adb30a

### Skimmer Validation Task Rewrite — completed (Qwen session 2026-07-05)
- Rewrote Phase 5 skimmer validation task to current template format
- Split terraforming content to separate Phase 9+ task (`phase9+/2026-07-05-LOW-RESEARCH-TERRAFORMING-ATMOSPHERIC-GAP-ANALYSIS.md`)
- Phase 5 task now MVP scope only: Titan CH4 harvesting and Venus CO2/N2 delivery as fuel delivery scenarios
- Updated architecture gotchas (GOTCHA 3: limited onboard processing, GOTCHA 4: docked skimmers augment base capacity)
- Committed + pushed to agent-tasks: d3f682f

---

## Today's Work (2026-07-07 — Planning Agent)

### Stale Task Review: Settlement Pattern Serialization (`2026-02-15`)
- Audited against current codebase: `PatternSerializer` and `SettlementPattern` don't exist
- `docs/architecture/patterns/settlement_patterns.md` exists but is Super-Mars/L1-Depot orbital strategy (different concept)
- Overlap check: `phase6+/2026-06-22-MEDIUM-FEATURE-WORLDHOUSE-AI-LEARNING-INTEGRATION.md` covers pattern learning for worldhouses specifically
- **Decision**: Underlying need is valid but approach (standalone SettlementPattern model) was confirmed obsolete in 2026-06-25 handoff
- Created new research task: `backlog/research/2026-07-07-MEDIUM-RESEARCH-PATTERN-BASED-SETTLEMENT-DECISION-LOGIC.md` — explores pattern learning via current architecture (tasks_v2 + StrategySelector/StateAnalyzer)
- Moved stale task to `deprecated/` with notes referencing new research task
- Committed + pushed: dd55cda

### Admin Monitor Canvas Task Modernization (`2026-02-11`)
- Audited against current codebase: monitor.js has partial fix (turbo:load listener at line 1364), no feature spec exists
- **Decision**: UI infrastructure task, not Phase 5 — placed in `backlog/ui/` for Phase 14+ relevance
- Created modernized task: `backlog/ui/2026-07-06-MEDIUM-BUGFIX-ADMIN-MONITOR-TURBO-CANVAS-INIT.md` (MEDIUM priority)
- Created spec gap task: `backlog/current/2026-07-06-HIGH-BUGFIX-MONITOR-CANVAS-FEATURE-SPEC-GAP.md` (HIGH priority, closes RSpec coverage gap)
- Moved stale task to `deprecated/`
- Committed + pushed: e8414a7

### Skimmer Intent Doc Update
- Updated `docs/architecture/intent/skimmer_craft_intent.md` processing model
- Section 2 rewritten: "Limited Onboard, Mostly Raw Delivery" with phases table
- Clarified onboard output is fuel self-sufficiency only (LOX/CH4 for skimmer's own tanks)
- Section 3 expanded with Storage Contribution and Surface Contingency details
- Committed + pushed: a7adb30a

### Skimmer Validation Task Rewrite
- Rewrote Phase 5 skimmer validation task to current template format
- Split terraforming content to separate Phase 9+ task (`phase9+/2026-07-05-LOW-RESEARCH-TERRAFORMING-ATMOSPHERIC-GAP-ANALYSIS.md`)
- Phase 5 task now MVP scope only: Titan CH4 harvesting and Venus CO2/N2 delivery as fuel delivery scenarios
- Updated architecture gotchas (GOTCHA 3: limited onboard processing, GOTCHA 4: docked skimmers augment base capacity)
- Committed + pushed to agent-tasks: d3f682f

### Status.md Update
- Added today's work section documenting all above items
- Consolidated multiple "Today's Work" sections into single 2026-07-07 entry
- Committed + pushed to agent-tasks: 9191aa8


---

## Today's Work (2026-07-08 — Planning Agent)

### Stale Active Task Cleanup — completed
- Reviewed 3 tasks in active/ that had no agent working on them
- **Monitor Canvas**: ✅ COMPLETED — spec created (9 examples, 0 failures), committed as `51549438`, task moved to completed/2026-07/
- **EscalationService**: ❌ No work done — moved back to backlog/current/
- **Profiles V2**: ❌ No work done — moved back to backlog/current/ (Claude already updated it)
- Committed + pushed: `87da03a`

### Workflow Protection — completed
- Added Stale Active Task Protocol to README.md Hard Rules
- Added GUARDRAILS.md Rule 27 with violation history
- Committed + pushed: `936142d`

### V2.1 Template Versioning — completed
- Created `unit_blueprint_v1.4.json` with connection_schema block (fixed invalid JSON comment from v1.3)
- Updated ethane engine task file to reference v1.4 template
- Updated hydrolox engine task file to reference v1.4 template + RL10/SSME heritage
- Committed + pushed: `cfd03f23`

### RSpec Baseline
- 4106 examples, 2 failures (GameDataGenerator + MaterialLookupService) — confirmed clean for V2 work
- Overnight run completed Jul 8 15:20

---
---

## Active Tasks

### 📌 Manifest ID ↔ Deploy Key Mismatch — READY (2026-07-08)
- Task file: `backlog/current/2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md`
- Scope: Normalize manifest hardware IDs during inventory seeding (_mk1 strip, inflatable_cryo_tank alias), fix robot_logistics phase name
- Status: ✅ READY — created after V2 rake run revealed 9 cascading failures from manifest ID mismatch
- Blocks: Phase Registry dispatch (need clean rake baseline first)

### 📌 Phase Registry Creation — READY (2026-07-07)
- Task file: `backlog/current/2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md`
- Scope: Create phase_registry.json AI Manager lookup index
- Status: ✅ READY — dispatch after Manifest ID task completes (need clean rake baseline)

