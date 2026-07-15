# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-07-14 — Harvester Name Mismatch Bugfix

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.

---

## 🎯 Today's Work (2026-07-13) — Sprite Extraction & Surface View Roadmap

### ✅ Terrain Sprite Extraction Complete
- **45 terrain tiles extracted** (138×145 PNG each) from ChatGPT tileset
- Organized by family: dust (9), frozen (9), regolith (9), temperate (9), volcanic (9)
- Fixed white line artifacts (139×145 → 138×145 dimension adjustment)
- All tiles verified consistent via ImageMagick identify
- Batch workflow established: Preview.app fixed-selection extraction → immediate organization → cleanup
- **Location**: `/data/images/terrain/{family}/{type}_{variant}.png`

### ✅ Surface View Task Portfolio Created (7 tasks to backlog/current)
1. **Architecture**: Three-Layer View System (Planetary/Surface/TerrainForge) — scope boundary enforcement
2. **Foundation**: Terrain Sprite Integration — Layer 0 property-driven rendering
3. **Rendering**: Unit Layer Rendering — structures on settlement tiles
4. **Gameplay**: Civ4-Style Surface View Gameplay — city overlays, tile selection, yields
5. **Design**: Generic Terrain Improvements — roads, farms, mines, power plants
6. **Integration**: Settlement Tiles Entry Point — bridge between Surface View and TerrainForge
7. **Clarification**: Architecture refined — TerrainForge is zoomed Surface View, not separate system

### 🔑 Key Architectural Decision: Generic Assets
- All sprites GENERIC (not planet-specific): "Regolith" works on Luna/Mercury/asteroids, not just Luna
- Property-driven pipeline: (temp, pressure, geological_activity) → terrain family → sprite
- Example: Valles Marineris settlement → Surface View shows marker → zoom in → see worldhouse structure and buildings

### ⏳ Ready for Handoff
- 7 HIGH-priority tasks committed to `backlog/current/`
- Sequential dependency chain established (render foundation → gameplay layer → detail view)
- ChatGPT tileset analyzed and mapped to generic asset categories
- All tasks scoped for Qwen agent implementation
- Premium API usage conserved (38% → delegating UI work to lower-cost agent)

---

## 🎯 Today's Work (2026-07-13) — Rake Validation + Baseline Confirmation

### ✅ Rake Confirmed 17/17 — All Phases PASSED
- **db:seed:replant** completed successfully
- **luna_mission:execute** — all phases PASSED:
  - Phase 1: deploy_lspu, site_prep_foundation, deploy_comms_equipment, deploy_puh_and_ppmu, deploy_solar_rig ✅
  - Phase 2: deploy_thermal_extraction_unit, deploy_pve_unit, inflatable_tank_deployment, print_inflatable_tank_shells ✅
  - Phase 3: deploy_volatiles_storage, deploy_gas_separator, surface_preparation_unit_operations ✅
  - Phase 4: deploy_robot_charging_port, car_300_charge_cycle, isru_stockpile_initiation, construction_zone_leveling ✅
- PUH/PPMU `_mk1` blueprint id fix verified in container (resolved port schema failures)
- CAR-300 + ISRU manifest items confirmed working

### ✅ RSpec Baseline Confirmed — 4142 examples, 3 failures
- **Confirmed flaky** (baseline): `game_data_generator_spec.rb:13`, `material_lookup_service_spec.rb:251`
- **Template drift**: `procedural_generator_spec.rb:144` — template count range may need widening

---

## 🎯 Latest Completion (2026-07-14)
✅ **Biosphere Migration + Test Suite Validation** — COMPLETED
- Fixed migration `20260714120000_create_missing_biosphere_records.rb` — Rails version 7.2→7.0, query logic corrected
- BiosphereGeneratorService: 35/35 RSpec tests passing (all complexity levels, auto-detection, era adjustments)
- ProceduralGenerator: 51/51 RSpec tests passing (integration test validates biosphere key structure)
- Fixes applied: CelestialBody method visibility, SystemBuilderService attribute filtering, test data key types
- Full end-to-end biosphere system validated (migration → generation → placement)

✅ **Harvester Name Mismatch Bugfix** — COMPLETED
- Root cause: `escalation_service.rb` used hardcoded "Harvester" suffix for all materials
- Fix: Added material-specific suffix logic — O2→Harvester, H2O→Extractor, regolith→Miner
- All 20 escalation_integration_spec examples pass, 0 failures

---

## 🎯 Latest Completion (2026-07-12)
✅ **Planetary View Biome Rendering** — COMPLETED
- Fixed CSS SCSS syntax errors in `_monitor_styles.scss` and `_shared.scss`
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

## 🎯 Latest Completion (2026-07-11)
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
- **RSpec Baseline (2026-07-13):** 4142 examples, 5 failures
  - 2 confirmed flaky: `game_data_generator_spec.rb:13`, `material_lookup_service_spec.rb:251`
  - 2 new regressions: `escalation_integration_spec.rb:216,228` — naming mismatch (2-line fix each)
  - 1 template drift: `procedural_generator_spec.rb:144` — range may need widening
- **Rake Baseline (2026-07-13):** 17/17 ✅ all phases PASSED — verified in container

---

## Pending Cleanup — Action Required Next Session

1. **Template drift** — widen range in `procedural_generator_spec.rb:144` if needed

---

## Active Tasks

### 📌 Atmospheric Extraction Service — READY (2026-07-11)
- Task file: `backlog/current/2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md`
- Scope: Create `AIManager::AtmosphericExtractionService` delegating to `TerraSim::AtmosphericTransferService`; skimmer→cycler cargo via `definition_data['cargo']`; replace mock calls; write RSpec
- Status: ✅ READY — both research tasks complete, design locked, task file committed

### 📌 Biosphere Seeding & Integrity — BACKLOG
- Task file: `tasks/backlog/current/2026-07-12-HIGH-ARCHITECTURE-BIOSPHERE-SEEDING-AND-INTEGRITY.md`
- Scope: Implement automatic biosphere record creation when terrain_map is assigned; backfill existing planets
- Status: ✅ READY — root cause identified, migration needed

### 📌 Skimmer Cycler Handshake Implementation — NEEDS TASK FILE
- Research complete; implementation task not yet drafted — draft after Atmospheric Extraction Service dispatched

### 📌 HLT Harvester Variants V2.1 — Ruby Work — BACKLOG
- JSON data complete (local); Ruby connection_schema work still pending

### 📌 Civilization Layer Architecture Doc — NEEDS DRAFTING
- `docs/architecture/intent/civilization_layer_scale_design.md` — three-scale design locked in 07-10 session
