# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-09-07 — AI Manager Acquisition Surface Inventory COMPLETED + Canonical-Path decision DRAFT (Path B recommended, awaiting Tracy)

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.

---

## 🟢 Current Work (2026-09-07)

### AI Manager Acquisition Surface Inventory — COMPLETED ✅
- **Task**: `2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
- **Synthesis Report**: `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
- **Findings**: Four acquisition services confirmed (EscalationService 627L, ProcurementService 112L, ResourceAcquisitionService 148L, ResourceFulfillmentService 33L). Two parallel paths in manager loop. Placeholder pricing reachable but non-functional.
- **Gaps**: EAP enforcement, cycler preference, excess-listing-after-self-harvest, unified "can afford" logic.
- **Recommendation**: Clarify canonical path before implementing gaps.
- **Commits**: `6b3dbf1`, `f8d8a49`, and closing commits

### Acquisition Canonical Path — DECISION DRAFT (awaiting Tracy) ⏸️
- **Decision doc**: `summaries/2026-09-07-ARCHITECTURE-DECISION-ACQUISITION-CANONICAL-PATH.md` (DRAFT, not dispatched)
- **Recommendation**: Make **Path B** (`ResourcePlanner` → `ResourceAcquisitionService` → `ResourceFulfillmentService` → `MaterialRequestService`) canonical — already uses real NPC pricing + contract creation.
- **Path A deprecation**: Keep ISRU/`can_produce_locally?` check; deprecate no-op market path.
- **Service boundaries**: EscalationService = shortage/emergency/strategy; Path B = execution; ProcurementService = local-capability check only.
- **Gaps to close**: EAP enforcement, cycler/resupply preference, excess-listing-after-self-harvest, unified "can afford", remove placeholder `base_prices`.
- **Blocks**: Material Sourcing & Acquisition Architecture task (`backlog/ai-manager/2026-09-03-...`) — remains DRAFT until Tracy advances this.

---

## 📋 Active Tasks: 0

> No tasks currently in `active/`. All current work is documented above; no agents currently assigned to active tasks.

---

## 📋 Current Backlog — Ready for Dispatch

### 🆕 Asset/UI Workstream (2026-09-01) — HELD / READY FOR REVIEW
| Task | Location | Notes |
|------|----------|-------|
| **Asset/UI Tasks A1–A6, B1–B3, C1–C5, D1–D3** (17 files) | `backlog/current/2026-08-31-*-ASSET-UI-*.md` | Created, content-verified, prerequisite gaps fixed. A1 is the natural starting point. |

### HIGH Priority
| Task | Location | Notes |
|------|----------|-------|
| ~~**Epoxy Resin Blueprint**~~ | `completed/2026-08/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` | ✅ COMPLETED — sourcing structure insufficient; see rework task below |
| **Epoxy Resin Sourcing Rework** | `backlog/current/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md` | HELD for review — rework to facility-based + market-driven pattern |
| **Fabrication Plant Blueprint** | `backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` | DEFERRED (Phase 11+) — do not dispatch until Phase 11+ work begins |
| **Orbital Mechanics Data Layer** | `backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md` | Phase 1-4 complete, Phase 5 pending |
| **Launch Window + Transit Timing Engine** | `backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` | Architecture feature |

### MEDIUM Priority
| Task | Location | Notes |
|------|----------|-------|
| **Classify 19 Blueprints** | `active/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md` | NEEDS_REVIEW #4 — Moved to active 2026-09-07 for dispatch |
| **19-Blueprint Operational Data Writing** | backlog (to be created) | Follow-up: for blueprints classified as "active" in task above |

### LOW Priority
| Task | Location | Notes |
|------|----------|-------|
| **Backlog Folder Cleanup Sweeps** (14 tasks) | `backlog/*/2026-09-03-LOW-DOCUMENTATION-CLEANUP-*.md` | One per folder — work through slowly; not urgent |
| **Financial Transaction Enum** | `review/2026-05-28-LOW-FEATURE-FINANCIAL-TRANSACTION-ENUM-AND-SPEC.md` | SUPERSEDED |

---

## 📊 Baseline & Test Status
- **RSpec Baseline:** 4714 examples, 174 failures, 55 pending (from 08-13/14 pre-push audit)
- **Rake Baseline:** 17/17 ✅ all phases PASSED — verified in container

---

## 📋 NEEDS_REVIEW — OPEN Entries (Summary)
| # | Date | Issue | Status |
|---|------|-------|--------|
| 1 | 07-31 | Sprite/biome/unit assets replaced with placeholders + mount architecture bug | **OPEN** — mount verified working; real sprites restored from Time Machine |
| 2 | 07-31 | Gemini Lava Tube Outpost specs review gaps | **OPEN** |
| 3 | 08-01 | Unit naming conventions (mk{num} vs codenames) — blocked on wiki reorg | **OPEN** |
| 4 | 08-02 | 19 renamed blueprints have no operational data | **OPEN** — task filed, now in active/ for dispatch |
| 5 | 08-02 | Possible CNT fabricator naming collision | **RESOLVED** ✅ — industrial variant renamed to `cnt_industrial_weaver_mk1` |
| 6 | 08-05 | Magnetosphere: 41 bodies defaulting to 0.5 | **OPEN** — low urgency, surface when Task 2 runs |
| 7 | 08-15 | **FABRICATED COMPLETION**: Data-driven task claims done but stubs remain, test counts don't match | **OPEN** — critical trust issue; see re-opened task file for details |
| 8 | 08-22 | **Oxygen fixture chain-tracing**: Storage-bucket fix works but O2 may short-circuit ISRU chain | **OPEN** — Claude handoff #1A pending verification |

> See `projects/galaxy_game/NEEDS_REVIEW.md` in agent-tasks repo for full verbatim entries.

---

## 🎯 Priority Queue for Next Session

### Must Do First:
1. **Dispatch Classify 19 Blueprints task** — moved to active/ 2026-09-07, ready for agent assignment

### Ready to Dispatch (No Sign-off Needed):
2. **Orbital Mechanics Data Layer Phase 5** — TransitEngine integration pending (needs verification pass first)
3. **Launch Window + Transit Timing Engine** — Architecture feature (must complete before Orbital Phase 5)

### Waiting for Review/Decision:
- **Acquisition Canonical Path decision** — awaiting Tracy's review of Path B recommendation
- **Asset/UI Workstream** — held pending review, A1 is natural start point
- **Epoxy Resin Sourcing Rework** — held pending review

### Do NOT Touch This Session:
- `market-fee-hold` branch — Synthesis Report drafted, awaiting sign-off before push
- Anything touching shared/global code without Synthesis Report + approval

---

## 📝 Notes from Previous Sessions
- Agent commits use Tracy's git identity by default — commit authorship is not evidence of independent human verification.
- Green tests are not sufficient sign-off for shared/global code changes — Synthesis Report + approval required before committing, not after.
- Backlog folder cleanup sweeps were created after the Lookup Service Caching duplicate incident — work through slowly alongside Phase 05 work.

---

## 🔴 Pending Handoff to Grok (AI Manager Development Lead)

### Material Sourcing Convention (Pass to Grok)
- **Issue**: Material JSON had hardcoded location keys (`lunar/martian/earth`) — doesn't scale to procedural worlds
- **Convention**: Materials carry recipes/pricing, NOT sourcing options; routing is runtime AI Manager decision
- **Documented**: `/memories/repo/material_sourcing_convention.md`
- **Handoff file**: `backlog/ai-manager/2026-09-03-ADJUSTMENT-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md`

### AI Manager Acquisition Logic (Pass to Grok)
- **Decision tree**: market scan → depot check → cost comparison → present options
- **Key constraint**: Travel time + transport cost drive ISRU-first strategy
- **Scope**: Requires runtime implementation; architecture defined, not yet coded
- **Integration point**: ProcurementService when AI Manager needs material

---

## 📌 Archive Reference
Historical entries from 2026-09-03 through 2026-08-20 archived to:  
`status_archive/2026-09-07-archived-2026-09-03-to-2026-08-20.md`
