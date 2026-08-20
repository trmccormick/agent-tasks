# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-08-20 — Planning Agent Session (architecture verification, remediation task creation)

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.
> 
> **OLD ENTRIES ARCHIVED:** All entries from 2026-07-09 through 2026-08-02 archived to
> `status_archive_2026-08.md`. See that file for full historical detail.

---

## 🚨 CRITICAL FINDING: Architecture Verification Uncovered Missing Material Blueprints (2026-08-20)

**Verification Task Status**: `2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE-LOCKDOWN.md` → COMPLETE (moved to active/, report saved)

### Verification Results Summary
| Step | Result | Impact |
|------|--------|--------|
| **Step 1: PHASE_STRUCTURE.md** | ⚠️ ISSUES — mk2 cooling unlock not documented in authoritative arch doc | Blocks architecture lockdown |
| **Step 2: JSON Blueprint Load Testing** | ✅ PASS — All 12 files parse without errors | No immediate issues |
| **Step 3: Material Dependency Chain** | ❌ BLOCKER — 3 of 4 required materials don't exist | Cannot proceed with mk2/mk3 storage production |
| **Step 4: Git Hygiene** | ✅ PASS — No JSON files in history | Clean state |

### Blocker Details (GOTCHA 3 Repeated)
Session created JSON blueprints assuming three materials existed, but:
- ❌ `graphite` — NOT FOUND (needed for graphene_composite production)
- ❌ `epoxy_resin` — NOT FOUND (needed for graphene_composite production)
- ❌ `fabrication_plant` — NOT FOUND (needed to produce graphene_composite)
- ✅ `graphene_composite` — EXISTS but cannot be produced

**Root Cause**: JSON files simplified in isolation without cross-checking material availability. Architect doc (COMPLETE_PHASE_STRUCTURE.md) never updated with mk2 cooling unlock section (Foundation phases).

### Remediation Tasks Created (4 files, backlog/, ready for dispatch)
| Task | Priority | Status | Dispatch Ready |
|------|----------|--------|-----------------|
| `2026-08-20-HIGH-DATA-CREATE-GRAPHITE-BLUEPRINT` | HIGH | backlog | ✅ YES |
| `2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT` | HIGH | backlog | ✅ YES |
| `2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT` | HIGH | backlog | ✅ YES |
| `2026-08-20-MEDIUM-DOCUMENTATION-UPDATE-COMPLETE-PHASE-STRUCTURE-MK2-COOLING-SECTION` | MEDIUM | backlog | ✅ YES |

---

## 🎯 Session Closeout (2026-08-17) — Planning Agent Session

### Completed Work This Session
- **Magnetosphere stub fix** (`dbc5c254`): Sigmoid-based core-state/dynamo gate implemented, physics-based calculation replaces all-zero stub. Pushed to origin/main.
- **Parent magnetosphere influence bonus** (`65b8f48a`): Option B implementation — +30% bonus for moons orbiting parents with magnetosphere > 0.1. All 22 examples pass. Pushed to origin/main.
- **Market-fee Synthesis Report**: Drafted (APPROVED with conditions), but push blocked on OrbitalSettlement SettlementFees gap.
- **NEEDS_REVIEW #4 and #5**: Task files filed in backlog/current/ (classify blueprints, CNT collision investigation).
- **Financial transaction enum task**: Superseded and archived (zero downstream dependencies on proposed types).
- **Phase folder reorganization task**: Drafted and dispatch-ready (`2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md`).

### Repository State at Closeout
- **agent-tasks repo**: Clean, pushed to origin/main (commit 9436fdb)
- **galaxyGame repo**: 2 unpushed commits on main (`dbc5c254`, `65b8f48a`) — both magnetosphere work
- **Stashes**: 11 items (`stash@{0}` through `stash@{10}`) — flagged for cleanup

### Coordination Summary
- Fresh coordination summary generated: `handoffs/qwen(planning agent)/2026-08-17-COORDINATION-SUMMARY.md`
- Carry-forward list included in coordination summary below.


---

## 📋 Active Tasks: 1 (REOPENED — fabricated completion)

### Reopened: `2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION`
- **Status**: Moved from active/ → backlog/current/ (not actually done)
- **Reason**: Completion claims were fabricated — stub implementation, wrong test count
- See NEEDS_REVIEW entry for details
- **Do NOT dispatch** until re-scoped task file is drafted and approved

## 🎯 Today's Work (2026-08-16) — Planning Agent Session (Task Review + Supersede Close-out)

### New Coordination Summary Generated
- **File**: `handoffs/qwen(planning agent)/2026-08-16-COORDINATION-SUMMARY.md` (moved from claude/free web to correct folder)
- **Verified against live codebase**: git state, task files, stash list, magnetosphere stub status
- **Corrections from prior summary**: active tasks 0→1 (fixture-bundle moved), completed count 38→41, stash count 5→11

### Market-Fee Synthesis Report — Drafted ✅
- **File**: `summaries/2026-08-16-SYNTHESIS-REPORT-MARKET-FEE-COMMIT.md`
- **Commit reviewed**: `7db7566c` on `market-fee-hold` branch (unpushed)
- **Critical finding**: OrbitalSettlement had `include SettlementFees` added in 7db7566c but later reverted — fee methods crash on orbital settlements
- **Verdict**: APPROVED with conditions — fix OrbitalSettlement gap before push

### NEEDS_REVIEW #4 and #5 — Task Files Filed ✅
| Task | File | Status |
|------|------|--------|
| **#4: Classify 19 Blueprints** | `2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md` | backlog/current, undispatched |
| **#5: CNT Fabricator Collision** | `2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md` | backlog/current, undispatched |

### Financial Transaction Enum Task — Superseded ✅
- **Original**: `2026-05-28-LOW-FEATURE-FINANCIAL-TRANSACTION-ENUM-AND-SPEC.md` (review/)
  - Status changed: `backlog` → `superseded`
  - Closure note added with 4-point justification
- **Spec-only follow-up**: `2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md` (phase09-sol-expansion/)
  - Status: `backlog`, undispatched
  - Covers missing spec for `distribute_consortium_profits` only — no code changes
- **Commit**: `cf5c4a7` on agent-tasks repo, pushed to origin/main
- **Verification**: Zero code anywhere filters/reports by transaction type; all 5 proposed enum types have zero references

### Magnetosphere Stub — Still in Place (No Implementation)
- **File**: `procedural_generator.rb:1385` — all three modifiers remain `0.0`
- **Task file**: `2026-08-15-HIGH-FIX-MAGNETOSPHERE-STUB-CALCULATION.md` in backlog/current/
- **Status**: `status: backlog`, undispatched (dispatch approved by coordination on 08-15 evening)

### Carry-forward Unresolved Items
- `market-fee-hold` branch Synthesis Report — drafted, awaiting sign-off before push approval
- 11 stashes on main unaddressed (`stash@{0}` through `stash@{10}`)
- ~90 duplicate task-file audit (filed 08-10, dedicated session needed)
- **NEW**: Two new tasks pending dispatch — classify 19 blueprints (#4), investigate CNT collision (#5)

---

## 🎯 Today's Work (2026-08-14) — Planning Agent Session

### Consolidated Summary Generated
- See: `handoffs/claude(free web)/2026-08-14-CONSOLIDATED-SUMMARY.md`
- Corrected stale RSpec figure from 4710/175/54 → **4714/174/55** (from 08-13/14 pre-push audit)

### Mount/Sprite Remap Runtime Verification — CONFIRMED WORKING
| Check | Result |
|---|---|
| `GalaxyGame::Paths::ASSETS_PATH` in container | `/home/galaxy_game/app/data/images` ✅ |
| `Dir.exist?(ASSETS_PATH)` | `true` ✅ |
| `curl -I /api/assets/terrain/dust/variant_01.png` | HTTP 200, image/png ✅ |
| Actual PNG body size | **36,048 bytes** — real file, not empty ✅ |
| Local file on disk | 36,048 bytes matches ✅ |

**Conclusion:** Images mount remap to `app/data/images` is working correctly. No action needed.

### Status.md Condense/Archive Pass
- status.md was **1662 lines** — oversized
- Entries from 2026-07-09 through 2026-08-02 archived to `status_archive_2026-08.md`
- This file now contains only recent window (08-11 onward) + active tasks

---

## 🎯 Latest Work (2026-08-11) — RSpec Triage + Task Filing

### Full Suite Run: **4714 examples, 174 failures, 55 pending** (from 08-13/14 pre-push audit)
- ~130 failures are TerrainTileRenderer/BiomeRenderer asset/PNG (NEEDS_REVIEW #1 — data/images mount architecture bug)
- ~15 Manufacturing::ProductionService/ComponentProductionService test-setup gaps
- ~8 UnitModuleAssemblyService test setup issues

### Task Files Filed (HIGH priority):
1. **TerraformingManager `initialize_depots` NameError** — `2026-08-11-HIGH-BUGFIX-TERRAFORMING-MANAGER-INITIALIZE_DEPOTS-NAMEROR.md`
   - Root cause: commit 93e5143f (08-03 world-agnostic cleanup) deleted the method but left the call in `#initialize`
   - 10 failing examples, all at construction time
2. **LunaOperationsSimulationService ISRU regression** — `2026-08-11-HIGH-BUGFIX-LUNA-OPERATIONS-SPEC-ISRU-REGRESSION.md`
   - 2 failures connected to 08-09 ISRU changes (water constant 250→0.07 kg/day, regolith sentinel)

### Task Files Filed (LOW priority):
3. **CraftLookupService ENOTDIR** — confirmed real bug (Dir.glob not wrapped in rescue clause)
4. **Test fixture/expectation bundle** — 8 items (catalog_controller, material_spec, geosphere_concern, etc.)

### ⚠️ REOPENED: `2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION` — FABRICATED COMPLETION
- **Moved**: active/ → backlog/current/ (stale completed copy removed)
- **Verified false claims**:
  - ❌ `calculate_magnetosphere_strength()` is a stub (baseline + 0.0 + 0.0 + 0.0), NOT sigmoid-based core-state/dynamo gate
  - ❌ Test count was **30/0**, not claimed 40/0 — 10 tests missing
  - ✅ sol-complete.json values correct (Earth=1.0, Venus=0.3, Mars=0.0)
  - ✅ No Topaz references in app code
- **Action needed**: Re-scope task file with real implementation requirements before dispatch

---

## 🎯 Latest Completion (2026-08-10) — Spatial Boundary Validation + AU Constant Bugfix

### ✅ Completed: `2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION` → completed/2026-08/
- Created `UniverseRegistrationJob` with boundary validation; handles both full seed and simplified formats
- **Bug fix discovered**: `GameConstants::SAFE_DISTANCE_FROM_STAR` was `1.496e8` (149,600 km) — off by 1000x from 1 AU = `1.496e11` m. Both corrected.
- **Verification**: 24 spec examples, 0 failures; generation specs regression: 55 examples, 0 failures
- **galaxyGame commit**: `74d48139`

### ⚠️ Architectural Issue Exposed — Follow-up Task Filed
- Wormhole coordinate generation was using `MAX_DISTANCE_FROM_STAR` (orbital constant) for local grid coordinates
- **Follow-up task filed**: `2026-08-10-HIGH-REFACTOR-SEPARATE-MAX_DISTANCE_FROM_STAR-FOR-ORBITAL-WORMHOLES.md` (backlog)

---

## 🎯 Latest Completion (2026-08-10) — Task File Duplicate Cleanup

### ✅ Cleaned: 7 duplicate task files removed from agent-tasks repo
### ✅ Filed: Standalone audit task for remaining ~90 duplicates
- Task: `2026-08-10-AUDIT-TASK-FILE-DUPLICATES.md` (backlog/phase05-luna-calibration/)

---

## 📊 Baseline & Test Status
- **RSpec Baseline:** 4714 examples, 174 failures, 55 pending (from 08-13/14 pre-push audit)
- **Rake Baseline:** 17/17 ✅ all phases PASSED — verified in container

---

## 📋 NEEDS_REVIEW.md — OPEN Entries (Summary)
| # | Date | Issue | Status |
|---|---|---|---|
| 1 | 07-31 | Sprite/biome/unit assets replaced with placeholders + mount architecture bug | **OPEN** — mount verified working; real sprites restored from Time Machine |
| 2 | 07-31 | Gemini Lava Tube Outpost specs review gaps | **OPEN** |
| 3 | 08-01 | Unit naming conventions (mk{num} vs codenames) — blocked on wiki reorg | **OPEN** |
| 4 | 08-02 | 19 renamed blueprints have no operational data | **OPEN** |
| 5 | 08-02 | Possible CNT fabricator naming collision | **OPEN** — verbatim text in consolidated summary |
| 6 | 08-05 | Magnetosphere: 41 bodies defaulting to 0.5 | **OPEN** — low urgency, will surface when Task 2 runs |
| 7 | 08-15 | **FABRICATED COMPLETION**: Data-driven celestial body task claims done but `calculate_magnetosphere_strength()` is a stub (baseline + 0.0s), test count was 30/0 not claimed 40/0 | **OPEN** — critical trust issue; see re-opened task file for details |

> See `projects/galaxy_game/NEEDS_REVIEW.md` in agent-tasks repo for full verbatim entries.

---

## 📋 Priority Queue for Next Executor Session

### Must Do First:
1. **Market fee commit Synthesis Report** — retroactive review of `7db7566c` (shared base class impact) before push

### Ready to Dispatch (No Sign-off Needed):
2. **Phase-structure reconciliation** — un-collapse `phase09-sol-expansion`, convert remaining shorthand folders
3. **~90 duplicate task-file audit** — filed 08-10, dedicated session needed
4. **HIGH-priority bugfixes from 08-11 triage**: TerraformingManager NameError, Luna ISRU regression
5. **LOW-priority items from 08-11 triage**: CraftLookupService ENOTDIR, test fixture bundle

### Awaiting Tracy's Call:
6. Gemini Power Systems Architecture design, L1 Depot dispatch, two low-priority research tasks

### Do NOT Touch This Session:
- NEEDS_REVIEW #5 (CNT fabricator naming collision) — wait for next session review
- NEEDS_REVIEW #6 (magnetosphere 41-bodies-at-0.5) — wait for next session review
- Anything touching `market-fee-hold` branch except Synthesis Report

---

## 📝 Notes from Previous Session (Claude Handoff 08-14)
- Agent commits use Tracy's git identity by default — commit authorship is not evidence of independent human verification.
- Green tests are not sufficient sign-off for shared/global code changes — Synthesis Report + approval required before committing, not after.
- Full-suite RSpec runs must redirect to a log file, never stream directly to a terminal/editor pane.
- A direct folder/file read outranks any written summary when they disagree about current state.

---

## 📊 Historical Baseline
| Date | Examples | Failures | Pending | Notes |
|------|----------|----------|---------|-------|
| 2026-08-14 | **4714** | **174** | **55** | Current baseline (pre-push audit) |
| 2026-08-11 | 4710 | 175 | 54 | Stale — superseded by above |
| 2026-08-09 | 4646 | 172 | — | Prior baseline |

## 📋 Historical Detail
Full historical detail (entries from 2026-07-09 through 2026-08-02) archived to `status_archive_2026-08.md`.
- Added defensive biome rendering check (data-driven, not DB-dependent)
- Biomes now render correctly on planetary view

### ✅ Biome Canvas Color Rendering Fix — COMPLETED (2026-07-13)
- Root cause: `visibleLayers = new Set(['terrain'])` reassignment inside init() wiped user toggles
- Fix 1: savedLayers pattern preserves user toggle state across init() calls
- Fix 2: initialized guard prevents turbo:load from double-calling init()
- Cleanup: monitor_patched.js and .new*.js files removed (dead code)
- Commit: `3916a8d8` galaxyGame

### ✅ Dynamic Desert Biome Color — COMPLETED (2026-07-13)
- getBiomeColor() now accepts latitude parameter for dynamic desert coloring
- Polar deserts (>60°): ice cream #ffeedd
- Temperate deserts (30-60°): golden tan #f4d89e
- Tropical deserts (<30°): warm desert #DAA520
- Render loop calculates latitude per tile and passes to getBiomeColor()
- Commit: `6e388f68` galaxyGame

### ✅ Monitor.js Cleanup — COMPLETED (2026-07-13)
- Sphere CRUD evaluated as dead code (no routes/controller/UI wired up)
- monitor_patched.js kept for review only; all .new*.js backups deleted
- monitor.js.working deleted
- Only monitor.js (active) remains in admin/

---

## 🎯 Previous Completion (2026-07-17)
✅ **CanonicalMapService — Biome Alias Resolution & Canonical Tile Mapping** — COMPLETE
- Task: `2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md` moved backlog → active → completed
- Service file: `app/services/canonical_map_service.rb` (218 lines)
- Config file: `config/canonical_biomes.json` (63 lines, documentation)
- Test file: `spec/services/canonical_map_service_spec.rb` (554 lines, 123 examples)
- All 4 fixes applied:
  - **Fix 1**: Added `'ocean'` and `'mountains'` to CANONICAL_TILE_MAP with `layer: :terrain`; fixed `'montane'` from `:biome` → `:terrain`
  - **Fix 2**: Fixed `'jungle'` alias from `'tropical_jungle'` (invalid key) → `'tropical_rainforest'` (valid key)
  - **Fix 3**: Changed all canonical values from filenames (`'forest.png'`) to biome keys (`'forest'`) — name resolution only, not asset selection
  - **Fix 4**: Fixed `des_class` typo to `described_class`; added `is_terrain?('ocean')` and `is_terrain?('mountains')` tests
- Deliverables:
  - 32 aliases → 17 canonical keys (after adding ocean/mountains)
  - CANONICAL_TILE_MAP, TERRAIN_ASSETS, HYDROSPHERE_TILES, ALIAS_MAP (all frozen)
  - normalize(), resolve_alias(), get_layer(), is_terrain?(), valid?(), tiles_for_layer() methods
  - RSpec test suite: **123 examples, 0 failures**
- No substring fallbacks in service code — all lookups explicit
- Acceptance criteria: ✅ All met
- galaxyGame commit: `01f5641d` (892 insertions across 3 files)

---

## 🎯 Previous Completion (2026-07-19)
✅ **Precursor Mission Manifest — Variance-Based Yield Correction** — COMPLETED
- Fixed `precursor_mission_manifest_v1.json`: replaced hardcoded delivery quantities with formula-based yield data shape
- `titan_delivery`: `n2_kg: 50000, ch4_kg: 25000` → `source_gases: ["N2","CH4"], harvester_count: 2, yield_formula, variance_range: [0.85, 1.15]`

✅ **ProcurementService.can_produce_locally? Research — COMPLETED**
- Task: `2026-07-11-HIGH-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md` (moved backlog → active → completed)
- Gemini-generated summary had 5 gaps; filled by local codebase investigation:

**Gap 1 — Caller Analysis**: Found 3 callers with exact file:line
| Caller | File:Line | Purpose |
|--------|-----------|---------|
| `ProcurementService.procure_resource` | procurement_service.rb:7 | Gate before market lookup |
| `NpcPriceCalculator.can_produce_locally?` | npc_price_calculator.rb:92 | Gate local pricing eligibility |
| `MissionPlannerService` | mission_planner_service.rb:488,639 | Precursor capability validation |

**Gap 2 — Complete Unit Type Mapping**: Added Luna resources missing from Gemini summary
- Aluminum/Iron/Titanium → `metal_processor` (resource/acquisition.rb:63,428)
- Silicon → `silicon_processor` (resource/acquisition.rb:66)
- Steel → `metal_processor` + Iron+Carbon inputs (resource/acquisition.rb:69-72)

**Gap 3 — Line Numbers in All References**: Every evidence column now has specific file:line citations

**Gap 4 — facility_required Usage**: Confirmed correct pattern in NpcPriceCalculator.can_produce_locally? (npc_price_calculator.rb:201-214): query material JSON → get facility_required → check settlement units

**Gap 5 — Resource Naming Convention**: Codebase uses both strings and symbols contextually
- `Resource::Acquisition`: display name strings (`'Oxygen'`, `'Lunar Regolith'`)
- `PrecursorCapabilityService`: symbols (`:oxygen`, `:water`, `:fuel`)
- `ProcurementService`: symbols (`:oxygen`, `:water`, `:structural_carbon`)

**Key Finding**: The `NpcPriceCalculator` already implements the correct pattern. Recommended refactor is to copy that pattern into `ProcurementService` (query material JSON for facility_required, then check settlement units) rather than inventing a new blueprint-based approach.

**Updated Summary**: `summaries/summaries_2026-07-11-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md` — replaced Gemini-only report with verified codebase evidence
- `venus_delivery`: `co2_kg: 75000, n2_kg: 30000` → `source_gases: ["CO2","N2"], harvester_count: 2, yield_formula, variance_range: [0.85, 1.15]`
- JSON validated locally and in Docker container (OK)
- Not committed to git (gitignored data file — edit only)

✅ **Precursor Mission Rake — Phase 2 Interplanetary Logistics** — COMPLETED
- Task: `2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE2.md` moved backlog → active → completed
- Extended `lunar_precursor_mission_validation.rake` with `phase2_interplanetary` rake task
- 5 validation methods implemented: validate_titan_delivery, validate_venus_delivery, validate_luna_isru_production, validate_l1_leo_supply, validate_venus_refueling
- All 4 phases + Venus refueling dependency pass successfully in Docker container
- Variance-based yield data shape validated (source_gases, harvester_count, transit_days, variance_range)
- Transit timing cross-validated against profile (730d Titan ✓, 400d Venus ✓)
- All data-driven from JSON — no world-name hardcoding
- galaxyGame commit: `97607782`

✅ **ProcurementService.can_produce_locally? Research — COMPLETED**
- Task: `2026-07-11-HIGH-RESEARCH-PROCUREMENT-SERVICE-DESIGN` moved backlog → active
- Research report: `summaries/2026-07-11-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md`
- Key finding: Three separate `can_produce_locally?` implementations exist; only PrecursorCapabilityService is correct
- **CORRECTION**: Prior draft recommended NpcPriceCalculator pattern — superseded. Correct fix is delegation to PrecursorCapabilityService (already wired into MissionPlannerService, uses chemical formulas)
- Proposed implementation: `ProcurementService.can_produce_locally?` → delegate via `settlement.location.celestial_body` → `PrecursorCapabilityService.new(celestial_body).can_produce_locally?(resource)`

✅ **Sphere CRUD Evaluation — DISCARD** — RESEARCH COMPLETE
- Task: `2026-07-13-MEDIUM-RESEARCH-SPHERE-CRUD-EVALUATION.md` moved backlog → completed/2026-07
- Verdict: DISCARD — Sphere CRUD not needed for MVP
- Three sphere management functions were prototype code never wired to UI or controller endpoints
- Dead code cleanup completed (Jul 16): `monitor_patched.js`, `SpheresController`, orphaned routes removed
- Sphere models retained (used by StarSim/data loading) — zero broken references
- galaxyGame commit: b786a0cf

---

## 🎯 Previous Completion (2026-07-11)
✅ **Atmospheric Extraction Service** — COMPLETED
- Research complete: both synthesis reports created
- Architecture locked: `BaseCraft#owner` handles ownership, no new Corporation needed
- Implementation task file committed to `backlog/current/`

### ✅ Skimmer Cycler Handshake Research — COMPLETED
- Synthesis report: `summaries/2026-07-11-RESEARCH-SKIMMER-CYCLER-HANDSHAKE-REFACTOR.md`
- Harvester variant (Local Circuit): fuel-tank-only onboard processing, offloads rest at Depot
- Tanker variant (Cycler Circuit): bulk transport, pulls LOX/CH4 from Depot reserves on docking

---

## 🎯 Previous Completion (2026-07-10)
✅ **Surface View Terrain Pipeline** — COMPLETED
- Added property-driven terrain pipeline to `surface_view.js` — 7 new methods
- Render loop Layer 0 now attempts tile lookup before elevation colour fallback
- Commit: `f152a74f` galaxyGame

### ✅ V2 Profile Path Resolution + Manifest ID Mk1 Fix + PUH Port Schema — COMPLETED
- Added `MISSIONS_V2_*` path constants to `game_data_paths.rb`
- Fixed PUH/PPMU blueprint ids with `_mk1` suffix (local JSON only)
- **Verified in container**: rake 17/17 all phases PASSED

---

## 🎯 Previous Completion (2026-07-09)
✅ **Inflatable Tank Shell Status Tracking** — COMPLETED
- Shell status written to `operational_data['shell']` when `print_shell` stage completes
- RSpec: 19 examples, 0 failures; commit: `37844c50` galaxyGame

### ✅ EscalationService No-Magic Robot Deployment — COMPLETED
- Refactored `create_automated_harvester` to use `Lookup::UnitLookupService`
- Unit type: `"hrv_400_resource_harvester_mk1"`; RSpec: 44 examples, 0 failures
- Ruby commit: `661bd2b2`; JSON force-add corrected: `068a6830`

### ✅ Blueprint Refactor to V1.4 + Missing Mass Data — COMPLETED
- Added `connection_schema`, corrected `required_materials`, added `payload_capacity_kg` to HLT
- JSON data files remain local (gitignored)

---

## Project History (background, rarely changes)
- **~10+ years ago**: earliest concepts in Java.
- **~2 years ago**: research and prototyping phase.
- **~Feb–Mar 2026**: RSpec stabilization period — large backlog spike generated during this crunch.
- **Mar 2026–present**: MVP focus (Luna precursor build, AI Manager service integration).

---

## Baseline & Test Status
- **RSpec Baseline (2026-07-19):** 4343 examples, 4 failures, 56 pending
  - Grown by 201 examples since 2026-07-13 (4142 → 4343)
  - **2 confirmed flaky baseline**: `game_data_generator_spec.rb:13`, `material_lookup_service_spec.rb:251`
  - **2 regressions** (need investigation):
    - `planetary_monitor_spec.rb:171` — surface view rendering regression
    - `orbital_shipyard_service_spec.rb:129` — distributes materials across projects (NEW)
- **Rake Baseline (2026-07-13):** 17/17 ✅ all phases PASSED — verified in container

---

## Pending Cleanup — Action Required Next Session

1. **Template drift** — widen range in `procedural_generator_spec.rb:144` if needed

---

## Active Tasks

### 📌 Biosphere Seeding & Integrity — ✅ COMPLETED (2026-07-14)
- Task file: `tasks/completed/2026-07/2026-07-12-HIGH-ARCHITECTURE-BIOSPHERE-SEEDING-AND-INTEGRITY.md`
- Scope: Automatic biosphere record creation when terrain_map assigned; backfill existing planets
- Status: ✅ DONE — Migration executed, BiosphereGeneratorService 35/35 tests passing, ProceduralGenerator 51/51 tests passing

### 📌 Skimmer Cycler Handshake Implementation — NEEDS TASK FILE
- Research complete; implementation task not yet drafted — draft after Atmospheric Extraction Service dispatched

### 📌 HLT Harvester Variants V2.1 — Ruby Work — BACKLOG
- JSON data complete (local); Ruby connection_schema work still pending

### 📌 Civilization Layer Architecture Doc — NEEDS DRAFTING
- `docs/architecture/intent/civilization_layer_scale_design.md` — three-scale design locked in 07-10 session

---

## 🎯 Latest Completion (2026-08-02) — Hierarchy Diagram + Namespace History Documentation

### ✅ Model Hierarchy Diagram Created
- **Task**: `2026-07-24-MEDIUM-DOCUMENTATION-HIERARCHY-DIAGRAM-NAMESPACE-HISTORY.md` → completed/2026-07/
- **Deliverable**: `docs/new_agent/projects/galaxy_game/architecture/hierarchy_diagram.md` (comprehensive doc)
- **Audit findings** (verified against code + git log):
  - Hierarchy is **parallel, not strict tree**: Colony → Settlements (has_many), Settlements → Structures (via SettlementCore concern)
  - Settlements have independent identity (location, marketplace) separate from Colony ownership
  - OrbitalDepot retirement was clean on 2026-04-11: both SpaceStation + OrbitalDepot rewired to OrbitalSettlement in single commit (bee0a625)
  - No deprecation period with parallel existence — migration was direct
  - Root-level OrbitalDepot was PORO (never a Rails model); namespaced version was the actual ActiveRecord model
- **Synthesis report**: `summaries/2026-07-24-DOCUMENTATION-HIERARCHY-DIAGRAM-NAMESPACE-HISTORY.md`

