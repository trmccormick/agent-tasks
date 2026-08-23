# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-08-23 — Material thermal properties CLOSED; oxygen fixture PARTIALLY RESOLVED; can_harvest_locally? structural issue flagged

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.
> 
> **ARCHIVED:** All entries from 2026-07-09 through 2026-08-02 moved to
> `status_archive/` folder.

---

## 📋 Active Tasks: 1 ⚠️

### PARTIALLY RESOLVED — Oxygen Fixture (EscalationService structural issue)
- Task file moved to completed/ but DO NOT mark fully complete
- Structural question remains open: does EscalationService route oxygen-shortage orders through real ISRU production, or does can_harvest_locally? incorrectly grant O2 credit from atmosphere gas presence alone?
- Needs Tracy's design input before task creation

---

## ✅ Just Completed (2026-08-22)

### Harvester Completion Job — Oxygen Fixture Fix ✅ → PARTIALLY RESOLVED
- **Root cause**: Material type lookup used wrong field (`'type'` instead of `'category'`)
- **Fix**: `inventory.rb` line 159 — changed `dig('type')` → `dig('category')`
- **Test result**: 20 examples, 0 failures (full escalation_integration_spec passes)
- **Task file**: moved to completed/ in agent-tasks repo
- **Commits**: `680b6a04` (galaxyGame), `6bbc855` (agent-tasks)
- **DO NOT mark fully complete** — Priority #1's structural question remains open: does EscalationService route oxygen-shortage orders through real ISRU production, or does `can_harvest_locally?` incorrectly grant O2 credit from atmosphere gas presence alone?

### Material Thermal Properties Data Gap ✅ → CLOSED
- **Root cause**: `refined_metals_backup/` directory had stale iron.json (missing `boiling_point`) overwriting correct cache entry
- **Fix**: Removed entire `refined_metals_backup/` directory (8 duplicate IDs: iron, aluminum, copper, nickel, steel, titanium, gold, silver)
- **Test fix**: `material_management_concern_spec.rb:194` — changed expectation from `"iron"` → `"Fe"`
- **Verified in true full-suite context** (not just isolation): fresh run rspec_full_1787522329.log — 4678 examples, 170 failures, 54 pending — three specific specs do NOT appear in failure list
- **Task file**: moved to completed/ in agent-tasks repo
- **Commits**: `6d32266f` (galaxyGame), `dd5e5d9` (agent-tasks)
- **Follow-up found** (not fixed): `composite/` vs `composites/` both have `carbon_nanotubes.json`; `refined_materials/` vs `semiconductors/` both have `high_purity_silicon.json`

---

## 🎯 Today's Work (2026-08-20/21) — Template Restructure + Phase Reorganization + Verification

### TASK_TEMPLATE.md Compliance — 5 Fixes Applied ✅
- Added validation requirement at top of template
- Repositioned Agent Dispatch Interface immediately after YAML frontmatter
- Renamed section to "🔴 Agent Dispatch Interface (Required)"
- Added 8-point Task Readiness Checklist gate
- Updated DISPATCH_INTERFACE_STRATEGY comment section
- **Result**: All subsequent task files now comply

### Phase Folder Reorganization — 32 Files Canonicalized ✅
- Created 8 canonical folders: phase09-mars, phase10-venus, phase11-logistics, phase12-optional-branches, phase13-psyche, phase14-eden-expansion, phase15-snap-crisis, act02-local-bubble-expansion
- Deleted 4 empty legacy folders; renamed 2 folders
- Resolved 5 ambiguous placements with user guidance

### Blueprint Architecture Verification ✅
- COMPLETE_PHASE_STRUCTURE.md mk2 section remediated (doc update task)
- All 12 JSON blueprints parse OK
- **graphite**: FALSE POSITIVE — already exists at `data/json-data/resources/materials/chemicals/industrial/graphite.json`
- **epoxy_resin**: MISSING — needs blueprint creation (READY FOR DISPATCH)
- **fabrication_plant**: MISSING — deferred per user (Phase 11+ scope)

### Commits: `30dc846`, `06a2e5f0`, `26b682c`, `bdb82f1` on galaxyGame; `416bff1` on agent-tasks

---

## 📋 Current Backlog — Ready for Dispatch

### HIGH Priority
| Task | Location | Notes |
|------|----------|-------|
| **Epoxy Resin Blueprint** | `tasks/backlog/phase10-venus/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` | READY — next dispatch item |
| **Fabrication Plant Blueprint** | `tasks/backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` | DEFERRED (Phase 11+) |
| **Orbital Mechanics Data Layer** | `tasks/backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md` | Phase 1-4 complete, Phase 5 pending |
| **Launch Window + Transit Timing Engine** | `tasks/backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` | Architecture feature |

### MEDIUM Priority
| Task | Location | Notes |
|------|----------|-------|
| **Classify 19 Blueprints** | `tasks/backlog/current/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md` | NEEDS_REVIEW #4 |
| **CNT Fabricator Collision** | `tasks/backlog/current/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md` | NEEDS_REVIEW #5 |
| **Material Thermal Properties Data Gap** | `tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md` | ✅ COMPLETED (moved to completed/) |
| **Atmosphere Generator Body Data Nil** | `tasks/backlog/current/2026-08-17-MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL.md` | Bug fix |

### LOW Priority
| Task | Location | Notes |
|------|----------|-------|
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
| 4 | 08-02 | 19 renamed blueprints have no operational data | **OPEN** — task filed, backlog/current |
| 5 | 08-02 | Possible CNT fabricator naming collision | **OPEN** — task filed, backlog/current |
| 6 | 08-05 | Magnetosphere: 41 bodies defaulting to 0.5 | **OPEN** — low urgency, surface when Task 2 runs |
| 7 | 08-15 | **FABRICATED COMPLETION**: Data-driven celestial body task claims done but `calculate_magnetosphere_strength()` is a stub (baseline + 0.0s), test count was 30/0 not claimed 40/0 | **OPEN** — critical trust issue; see re-opened task file for details |
| 8 | 08-22 | **Oxygen fixture chain-tracing**: Storage-bucket fix makes test pass but O2 may short-circuit real ISRU chain (TEU→PVE) | **OPEN** — Claude handoff #1A pending verification |

> See `projects/galaxy_game/NEEDS_REVIEW.md` in agent-tasks repo for full verbatim entries.

---

## 🎯 Priority Queue for Next Session

### Must Do First:
1. **Dispatch epoxy_resin blueprint** — READY, same workflow as graphite task (search → create if missing)

### Ready to Dispatch (No Sign-off Needed):
2. **Orbital Mechanics Data Layer Phase 5** — TransitEngine integration pending
3. **Launch Window + Transit Timing Engine** — Architecture feature, backlog
4. **MEDIUM bug fixes** (08-16/17) — Atmosphere generator nil

### Do NOT Touch This Session:
- `market-fee-hold` branch — Synthesis Report drafted, awaiting sign-off before push
- Anything touching shared/global code without Synthesis Report + approval

---

## 📝 Notes from Previous Sessions
- Agent commits use Tracy's git identity by default — commit authorship is not evidence of independent human verification.
- Green tests are not sufficient sign-off for shared/global code changes — Synthesis Report + approval required before committing, not after.
