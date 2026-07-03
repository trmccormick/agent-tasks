# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-07-02 — Planning Agent (Claude web) — post full-suite run + audit workflow session

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
- **RSpec Baseline (full suite):** 4059 examples, 6 failures — run 2026-07-02
  - 3 confirmed flaky (never touch): `game_data_generator_spec.rb:13`,
    `material_lookup_service_spec.rb:251`, `orbital_shipyard_service_spec.rb:129`
  - 1 pre-existing (tracked): `escalation_service_spec.rb:429`
    (`schedule_harvester_completion` — `a_value_within` matcher vs keyword-arg hash)
  - 2 **NEW regressions**: `escalation_integration_spec.rb:205 + :216`
    (`"oxygen"`/`"water"` expected, got `"O2"`/`"H2O"` — target_material string
    normalization, needs task file)
  - 1 **status unclear**: `procedural_generator_spec.rb:144` — removed from flaky list
    2026-06-05, now failing again; needs targeted run to confirm flaky vs regression
- **Suite growth:** 3957 → 4059 (+102 examples, Phase C + EscalationService specs)
- **Branch:** main
- **Material identifier convention (locked):** chemical formulas (`O2`, `H2O`, `N2`)
  are the Ruby codebase standard. Display names are JSON/UI only.

---

## Active Tasks

### 🔴 escalation_integration_spec regressions (x2) — needs task file
- `escalation_integration_spec.rb:205 + :216`
- `"oxygen"`/`"water"` expected, got `"O2"`/`"H2O"` in `operational_data['target_material']`
- String normalization mismatch introduced by EscalationService work. Single-file fix.

### 🔴 procedural_generator_spec.rb:144 — status unclear
- Was removed from flaky list 2026-06-05, now failing again.
- Run targeted spec before classifying. Do not add back to flaky list without confirmation.

### 📌 schedule_harvester_completion — needs task file
- Pre-existing. `a_value_within(60)` matcher vs keyword-arg hash `{wait_until: time}`.
- Single-file spec fix, LOW/MEDIUM, `local_worker_safe: true`.

### 📌 HUB-BUS-ROUTING — dispatched
- Task file: `backlog/phase5+/2026-07-01-HIGH-ARCHITECTURE-HUB-BUS-ROUTING.md`
- Perplexity design pass complete. Governing modifier: capacity-based inventory routing
  (storage/throughput limits), not unit-to-unit topology. Hub = settlement-wide routing
  bus; avoid overcomplicating with unnecessary unit connections when a hub-mediated
  capacity check is sufficient.

### 📌 No-Magic Robot Deployment — ready to dispatch
- Task file: `backlog/phase5+/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md`
- Line numbers confirmed current: 225, 242, 260, 283.

### 📌 Phase 5 SystemOrchestrator pipeline — awaiting authorization
- 8 task files in `proposed new tasks/2026-06-30-TASK-UPDATE-1/` awaiting move to
  `backlog/phase5+/`. Two red flags must be patched before any dispatch:
  - RED 1: Stub spec bodies in Tasks 1 + 2 (hollow coverage)
  - RED 2: `shared_context.id` as Redis cache key in Task 3 (may be nil)

### 📌 GUARDRAILS.md consolidation — workflow ready, test pending
- `STALE_TASK_AUDIT_WORKFLOW.md` + `DISPATCH_STALE_TASK_AUDIT.md` in `refactored-task-files/`
- `TASK_TEMPLATE.md` corrected (stale Continue-era line fixed)
- Canonical file question resolved: four-way sort defined
  (see `2026-07-02-SESSION-SUMMARY-AUDIT-WORKFLOW.md`)
- `em_power_shield_tiers.md` target path still unresolved — carry forward
- Next step: test workflow against GUARDRAILS consolidation task using 2026-07-01
  five-file set as prior research input

### 📌 Luna Precursor V2 rake — post-patch confirmation pending
- Sequence Reconciliation task ✅ closed (rake footer confirmed 2026-07-02).
- Post-patch rake run not yet done (LegacyPortAdapter + Phase C commits landed after
  last known run). Run `rails luna_mission:execute` and confirm gas_processing phase.

### 📌 Non-idempotent rake runs — undrafted
- 453 deployed `BaseUnit`s observed across sequential IDs. Setup block clears inventory
  but not previously-deployed units. Small additive fix to `luna_mission.rake` setup
  block. Quick to draft.

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

## Parked / Not Yet Filed

- **TASK-luna-mvp-ai-manager-settlement.md** — ready to dispatch; implements real V2
  stub handlers + non-`rand()` settlement-siting decision function. Read
  `AI-MANAGER-LUNA-BEHAVIOR-GOALS.md` before dispatching.
- **Shell-printing/tank-stage-advancement gap (3c)** — confirmed real missing feature;
  inserting print tasks into phase order does NOT advance `deployment.stages` on the
  tank. Needs own implementation task. Do not mistake as resolved.
- **Landing pad insertion (3d)** — `task_surface_preparation_unit_operations.json`
  covers it (metadata only); agreed insertion point is end of Phase 3. Report-only,
  not wired in.
- **Drone Bay Operational Data** — task file `2026-06-19-LOW-DESIGN-DRONE-BAY-OPERATIONAL-DATA.md`
  created, not yet copied to repo. LOW priority.
- **AI-MANAGER-SERVICE-INTEGRATION placeholder** — confirmed empty template by two
  independent reviews; archive to `deprecated/` decision made, `git mv` not yet executed.
- **Titan atmospheric transfer task file** — creation unconfirmed; verify before
  assuming it exists.
- **ROUTING_LOGIC.md** — mid-revision since 2026-06-16; not finalized.
- **L1/LEO Depot task** (Perplexity research handoff) — not yet reviewed.
- **Image asset generation** — `GALAXY-GAME-IMAGE-ASSETS.md` style guide complete;
  Cycler priority-10 component images still need ChatGPT generation time.
- **Agent folder cleanup** — migrating old tasks from legacy `agent/` folder into
  `agent-tasks` repo. Low priority, gated before `agent/` removal.
- **Backlog reorganization audit** — `reorganization_attempt_3/` Gemini thread status
  unknown as of 2026-06-27. Confirm scope before assuming M4 cleanup covers it.
- **HLT payload capacity gaps** — task file `2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md`
  created, not yet dispatched. 150,000 kg payload baseline adopted (Starship reusable-mode).
- **Deposit/Spawning gap** — `Deposit::PlausibilityEngine` + `DepositSpawner` designed,
  never implemented. Explicitly deprioritized below Luna MVP.

---

## Completed (recent — see handoffs for full detail)

| Date | Item | Commit |
|---|---|---|
| 2026-07-02 | LegacyPortAdapter task closed | `e196ff4` |
| 2026-07-02 | Luna Precursor V2 Sequence Reconciliation closed | rake footer confirmed |
| 2026-07-01 | Phase C (StrategySelector→V2 bridge) | `4441a74d` |
| 2026-07-01 | EscalationService resource-shortage handling | `50f70486` |
| 2026-06-29 | GCC Account Refactor Phase 1 | `2bf82245` |
| 2026-06-29 | `advance_deployment_stages` dispatch fix | committed |
| 2026-06-27 | LegacyPortAdapter core (Steps 1–4) | `b3beff34` + `fd0b96d7` |
| 2026-06-23 | Docs reorganization | `415805b7` |
| 2026-06-20 | `PRICE_DISCOVERY_LIFECYCLE.md` recovered | manual backup |
| 2026-06-15 | Luna Phase 4 (re-implemented after loss) | `5a2af17a` |
| 2026-06-06 | Luna Phase 2 | `21b10ef0` |
| 2026-06-05 | Luna Phase 1 | `cd4d6800` |
| 2026-06-03 | ShortageDetector + ServiceCoordinator fixes | committed |

**Handoffs**: `agent-tasks/projects/galaxy_game/handoffs/` — full session detail lives there.
