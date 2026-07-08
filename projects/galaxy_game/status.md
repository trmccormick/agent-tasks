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

## V2 Mission System — Backlog Items (reset 2026-07-07)

Both tasks below were moved from `active/` → `backlog/current/` by Claude.
Claude never approved them for execution. Both need clean rewrites before dispatch:

- **`2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md`** — has duplicate conflicting content (Claude-approved rake-only scope pasted on top of old rejected engine-scope). Needs full rewrite. Canonical examples already exist in `missions_v2/`.
- **`2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md`** — references non-existent `profiles_v2/` folder everywhere. 4 phase files + 17 tasks already done. Remaining scope unclear — needs clarification (likely just `phase_registry.json`).

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

## Today's Work (2026-07-07 — Qwen session)

### EscalationService Robot Blueprint Fix — task corrected and filed
- Reviewed `2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md` in `backlog/current/`
- **Scope corrected**: Original task demanded No-Magic manufacturing hierarchy (inventory checks, manufacturing jobs, GCC fallback) — premature for MVP where equipment is Earth-delivered
- **New scope**: Blueprint ID fix only — use `hrv_400_resource_harvester_mk1` instead of generic `"robot"`, load operational_data from JSON via `Lookup::UnitLookupService`
- **Blueprint audit**: HRV-400 blueprint complete; operational data file (`hrv_400_resource_harvester_mk1_data.json`) MISSING — flagged for Gemini design task
- Harvesting rover craft (`regolith_harvester_rover_bp.json`) is a separate entity (base_craft template) — no conflict
- Task committed to agent-tasks repo with corrected MVP scope

### V2 Mission System Architecture Research — completed
- Research task committed to agent-tasks repo
- Established three-tier architecture (Mission Plans → Registry/Phases → Tasks)
- Documented LegacyPortAdapter production code at `app/services/lookup/legacy_port_adapter.rb` (4 schema versions)

### Canonical V2 Examples Created in missions_v2/
- Luna profile: 4-phase complete with world_requirements, environment, runtime_parameters
- 4 phase files: power_comms, isru_deployment, gas_processing, robot_logistics (with task_refs, applicable_body_types)
- Mission plan: DAG-ordered with dependencies and critical_path
- Task index: maps all 16 Luna tasks to their phases
- Venus migration study: preserves V1 richness (economic data, parallel execution, AI hints)

### 17 V2.1 Task Files Converted
- Copied from `tasks_v2/` → `missions_v2/tasks/` with `_v2.json` suffix
- All have `"template": "task_v2.1"` and `"version": "2.1"` confirmed by grep

### Implementation Tasks Corrected (Claude scope constraints applied)
- `2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md` — updated with Claude's scope constraints, committed to agent-tasks
  - IN SCOPE: production files in missions_v2/, rake path resolution, 17-task verification
  - EXCLUDED: engine changes, parameter interpolation, LegacyPortAdapter, Venus migration, DAG execution
- Both implementation tasks moved back from `active/` → `backlog/current/` (Claude never approved them for execution)

---

## Active Tasks

