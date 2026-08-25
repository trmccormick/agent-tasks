# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-08-25 — .gitignore negation-pattern fix + fabrication_plant task reversion

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.

---

## 🔴 Critical Fix (2026-08-25) — .gitignore Negation Pattern Purge + fabrication_plant Reversion

### Issue: Agent-added negation patterns bypassed `/data/` gitignore rule
- **Root cause**: Commit `13d7d7fd` added `!/data/json-data/blueprints/` and `!/data/json-data/schemas/` to `.gitignore`, intentionally bypassing the `/data/` exclusion rule that was designed to keep all data files out of git (backed up via Time Machine instead)
- **Impact**: 18 blueprint JSON files were incorrectly tracked in git for months

### Fix Applied:
- Removed both negation patterns from `.gitignore`
- Purged all 17 blueprint files + `galaxy_game_tileset.json` from git index (files remain on disk)
- Reverted fabrication_plant blueprint commit (`cc1d5cb3` → `9cafcc34`) — it was committed via `git add -f` which violated the standing convention
- Moved fabrication_plant task file from `completed/` back to `backlog/current/` with status=backlog and notes documenting why it's deferred

### Commits:
- `fc1a50c3` (galaxyGame): fix: remove all data/ files from git tracking, restore correct .gitignore
- `588de210` (galaxyGame): fix: remove galaxy_game_tileset.json from git tracking
- `7191168` (agent-tasks): Revert fabrication_plant task to backlog

### Verification:
- `git ls-files --cached data/` → **0 files** (was 18)
- `.gitignore` now correctly excludes all of `./data/` with no negation patterns

---
> 
> **ARCHIVED:** All entries from 2026-07-09 through 2026-08-02 moved to
> `status_archive/` folder.

---

## 📋 Active Tasks: 0 ✅

All active tasks cleared. Two completions today:
1. Luna oxygen fixture test (material type lookup fix)
2. Material thermal properties data gap (stale backup removal)

---

## ✅ Just Completed (2026-08-24)

### AtmosphereGeneratorService @body_data nil/wrong Bug Fix ✅
- **Root cause**: Swapped arguments in `ProceduralGenerator` initialization (line 29)
  - Before: `AtmosphereGeneratorService.new(material_lookup, {})` — material_lookup passed as celestial_body_data
  - After: `AtmosphereGeneratorService.new({}, material_lookup)` — correct order
- **Impact**: `@body_data` became a MaterialLookupService instance instead of a hash; `@material_lookup` became empty `{}`
- **Fix**: Swapped argument order in `galaxy_game/app/services/star_sim/procedural_generator.rb` line 29
- **Cleanup**: Removed workaround in `procedural_generator_magnetosphere_spec.rb` that mocked `generate_composition_for_body` to avoid triggering this bug
- **Test result**: 85 examples, 0 failures (51 procedural_generator + 22 magnetosphere + 12 data_driven_generation)
- **Task file**: moved to completed/2026-08/ in agent-tasks repo
- **Commits**: `113f88fc` (galaxyGame), `7d5e6d8` (agent-tasks)

---

## ✅ Just Completed (2026-08-22)

### Harvester Completion Job — Oxygen Fixture Fix ✅
- **Root cause**: Material type lookup used wrong field (`'type'` instead of `'category'`)
- **Fix**: `inventory.rb` line 159 — changed `dig('type')` → `dig('category')`
- **Test result**: 20 examples, 0 failures (full escalation_integration_spec passes)
- **Task file**: moved to completed/ in agent-tasks repo
- **Commits**: `680b6a04` (galaxyGame), `6bbc855` (agent-tasks)

### Material Thermal Properties Data Gap ✅
- **Root cause**: `refined_metals_backup/` directory had stale iron.json (missing `boiling_point`) overwriting correct cache entry
- **Fix**: Removed entire `refined_metals_backup/` directory (8 duplicate IDs: iron, aluminum, copper, nickel, steel, titanium, gold, silver)
- **Test fix**: `material_management_concern_spec.rb:194` — changed expectation from `"iron"` → `"Fe"`
- **Test result**: 57 examples, 0 failures across material/geosphere/material_management specs
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
| **Epoxy Resin Blueprint** | `backlog/phase10-venus/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` | READY — next dispatch item |
| **Fabrication Plant Blueprint** | `backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` | DEFERRED (Phase 11+) — blueprint drafted but premature; git tracking violated standing convention and was reverted; do not re-dispatch until Phase 11+ work begins |
| **Orbital Mechanics Data Layer** | `backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md` | Phase 1-4 complete, Phase 5 pending |
| **Launch Window + Transit Timing Engine** | `backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` | Architecture feature |

### MEDIUM Priority
| Task | Location | Notes |
|------|----------|-------|
| **Classify 19 Blueprints** | `backlog/current/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md` | NEEDS_REVIEW #4 |
| **CNT Fabricator Collision** | `backlog/current/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md` | NEEDS_REVIEW #5 |
| **Material Thermal Properties Data Gap** | `backlog/current/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md` | ✅ COMPLETED (moved to completed/) |

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
