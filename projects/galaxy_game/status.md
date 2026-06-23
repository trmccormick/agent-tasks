# Galaxy Game — Project Status & Task Tracking  
**Last Updated:** 2026-06-23 10:00 UTC (Blueprint cleanup in progress: robot_charging_port duplicate removed ✅, gas_separator duplicate deletion pending. Ryzen cleanup task and m4 Phase 4 implementation task both RESUMED in active/ status. Claude free session ended early; real work resuming in evening session.)

---

## Project History (background, rarely changes)
Real-world development timeline, as told by Tracy 2026-06-20 — useful context for understanding why certain old code/docs/tasks exist or read as dated:
- **~10+ years ago**: earliest concepts written in Java.
- **~2 years ago**: research and prototyping phase, determining actual need/scope for the game.
- **Various progression after that**: first test implementations using hardcoded data — the rake task appeared to work, but lots of RSpec failures at this stage.
- **~February–March 2026**: RSpec stabilization period. **This is the key mechanism behind today's backlog state**: as failures were found during stabilization, a large volume of task files got generated describing fixes — the priority at the time was getting the suite green, not closing out every task file immediately. This created a backlog spike.
- **March 2026–present**: MVP focus (Luna precursor build, AI Manager service integration, current active work).

**Why the backlog has so much stale/contradictory material**: it's not simply "old code from different eras coexists" — it's specifically that the stabilization-era task-generation spike produced two kinds of leftover debris that audits keep tripping over:
1. **Silently-resolved tasks**: some stabilization-era tasks got fixed *incidentally* later — as a side effect of unrelated refactors or other bug fixes — without the original task file ever being closed out, archived, or removed. The task file is stale not because it's wrong, but because the problem it describes no longer exists.
2. **Incomplete concepts**: other stabilization-era tasks were written quickly during the crunch and never fully thought through — abandoned mid-design, not because they were solved, but because they were never finished as ideas.
Both look superficially similar during a backlog audit (an old task file that doesn't match current reality), but they need opposite treatment: silently-resolved tasks should be verified-then-archived; incomplete concepts need a real decision (finish, drop, or rescope) before archiving. **Do not assume "stale" means "already handled" — check which kind it is.**

---

## Baseline & Test Status
- **RSpec Baseline (ai_manager suite):** 746 examples, 0 failures, 4 pending (last confirmed full-suite run; `luna_settlement_integration_spec.rb` re-confirmed 4 examples, 0 failures after Step 4 port-model revision, not yet folded into a fresh full-suite run)
- **Last Full Suite Run:** June 15, 2026 (00:08 UTC) — not re-run since; due for a fresh pass once Step 5 lands
- **Action for next session:** **Ryzen 7 HLT thread (COMPLETED Phases 1-3, ready for Phase 4 sign-off)**: Phases 1-3 fully complete — all mass data backfilled, HLT mk1 versioning + payload limit confirmed in place, `robot_charging_port` dual-state deployment lifecycle corrected. Agent paused per plan before Phase 4 to let planner review completion + bundle Phase 4 design approval. Phase 4 scope: design new `CargoMassCalculation` service layer + integration into `LaunchPaymentService` to validate manifest cargo mass against `max_payload_kg` capacity limit (legacy test scripts show this was always intended, just not implemented in production code). **Luna Precursor V2 rake (M4/Ryzen, BLOCKED on gas-separator naming collision)**: paused on a genuine duplicate `gas_separator_unit` blueprint (two files, same ID, different masses: 1800kg under production/refineries/, 1200kg under units/infrastructure/ — both created during this session's troubleshooting). Real fix is lookup disambiguation at the service layer, not more file edits. Needs user decision: which file to delete (1800kg or 1200kg)? Once decided, can resume rake execution + resolve whether the new `task_site_prep_foundation.json` and Phase reordering are stable through a fresh run. Separately: RTG now confirmed to belong in manifest v2 draft (Tracy 2026-06-22) — add `radioisotope_thermoelectric_generator` x1 to required_hardware with task_affinity once the manifest reconciliation happens (Phase 4 also blocks this, since Phase 4 design will impact what "cargo" means in the manifest). **PRICE_DISCOVERY_LIFECYCLE.md** fully resolved (restored, committed). **AI-MANAGER-SERVICE-INTEGRATION archive action** still pending (one remaining archival task noted in completed section below, low priority). No blockers preventing either session from resuming once these decisions/approvals land.
- **Flaky specs (never touch):**
  - `spec/services/construction/orbital_shipyard_service_spec.rb:129`
  - `spec/services/generators/game_data_generator_spec.rb:13`
  - `spec/services/lookup/material_lookup_service_spec.rb:251`
- **Branch:** main
- **Recent Milestone:** Luna Phase 4 complete (consumption-aware ordering); Phase 5 (simulation observation) underway; Luna Precursor V2 verification/implementation actively in progress

---

## Active Tasks

### 🔄 Luna Precursor V2 Sequence Reconciliation (HIGH)
- **Task file:** `2026-06-19-HIGH-VERIFICATION-LUNA-PRECURSOR-V2-SEQUENCE-RECONCILIATION.md`
- **Machine:** M4/Ryzen (Copilot) — **resumed live iteration 2026-06-22, not held for a separate fresh-review session as originally planned**
- **Status:** Steps 1-4 previously confirmed (see history below). Step 5 (`luna_mission.rake`) is **actively being debugged and corrected in real time** — significant progress since the 2026-06-21 holding point, several real bugs found and fixed, currently at **3 passed / 5 not-passed (8 total tasks)**, narrowing toward genuine sequencing/data gates rather than harness bugs.
  - Step 4 port-model revision **confirmed actioned 2026-06-20**: `connect_units_from_effect` rewritten to use generic typed-count port categories (`internal_unit_ports`/`propulsion_ports`/`rig_ports`/`storage_ports`) via `determine_port_category` + `check_and_decrement_port`; named effect strings (`"power_output"`, `"main_power_in"`) correctly treated as human-readable labels only, never resolved against unit data; `HeavyLander`'s `charging_ports` correctly excluded as a special case, not generalized. Existing spec (`luna_settlement_integration_spec.rb`): 4 examples, 0 failures.
  - 3c/3d explicitly held as report-only per direct instruction — **not wired in, not built**, still correctly flagged in rake output as of latest run.
  - **2026-06-22 session — real bugs found and fixed during dev-DB rerun:**
    - **Port lookup bug**: engine was reading port counts from a flat top-level key, but canonical blueprints (per `rig_blueprint_v1.2`/`module_blueprint_v1.2` templates) nest them under a `ports` block. Engine's `check_and_decrement_port` updated to read `bp['ports'][port_category]` first, falling back to legacy top-level for compatibility.
    - **`solar_expansion_rig_bp.json` and `gas_separator_bp.json` rewritten** to match current template versions, with proper `ports` blocks (`rig_ports: 1` for the rig; `internal_unit_ports: 1` / `storage_ports: 3` for the gas separator module).
    - **Duplicate blueprint caught and corrected**: an erroneous duplicate `solar_expansion_rig` blueprint was created under `units/power/` during troubleshooting — Tracy caught this (a rig is not a unit) and it was removed; canonical version remains only under `rigs/power/`.
    - **Deployed-unit attachment bug fixed**: units were being created with `owner` only, not attached to `@settlement` (attachable), which broke `connect_units_from_effect`/`set_unit_state_from_effect` lookups. Fixed in `TaskExecutionEngineV2`.
    - **Phase ordering bugs fixed**: Phase 1 reordered so PPMU deploys before the solar rig connects to it (matches legacy `npc-base-deploy` intent); Phase 3 reordered so tanks deploy before the gas separator connects to them.
    - **`central_utility_hub` vs `planetary_umbilical_hub` naming conflict identified and being resolved**: confirmed as a leftover concept from an earlier agent/era, not a deliberate two-hub design — engine references being updated to use `planetary_umbilical_hub` consistently. **In progress as of last update, not yet confirmed complete or re-tested.**
    - **New task file created**: `task_site_prep_foundation.json` — added to satisfy a `foundation_sintered` gate that was blocking inflatable tank deployment with no task anywhere setting it true. **This is new scope beyond the original 8 tasks_v2 files — flag for review, not yet formally reconciled against the manifest v2 draft's task_affinity list.**
    - **Cape Canaveral launch-origin seeding now confirmed working** against the reset dev DB — rake correctly finds `EARTH-01`/Cape Canaveral Spaceport/AstroLift seed data and stages manifest cargo through an HLT launch flow rather than direct settlement seeding.
    - **🛑 Gas separator naming collision discovered, session paused on this — not yet resolved.** Two legitimately distinct things exist: (1) a **module** (`gas_separator_bp.json`, `modules/production/`) for HLT skimmer atmospheric processing during skimming operations, and (2) a **unit** (`gas_separator_unit_bp.json`) for planetary ISRU infrastructure (this is the one the Luna precursor manifest needs). **The actual bug**: while debugging port-lookup failures, the agent found — and then itself created another copy of — `gas_separator_unit_bp.json` in a second folder (`units/infrastructure/`, in addition to the original `units/production/refineries/`), with different physical properties (1200kg/54m³ vs 1800kg/12m³) and both claiming `id: "gas_separator_unit"`. This is a genuine duplicate the agent created, not a deliberate two-variant design — confirmed by Tracy. **Root cause is a lookup disambiguation problem, not a data problem**: `BlueprintLookupService.find_blueprint('Gas Separator')` needs to reliably resolve to the *unit* variant for ISRU task effects, without colliding with the *module* variant's name/id space. Renaming one or both (e.g. module → `"HLT Gas Separator"` / `id: "gas_separator_module"`) is the likely fix, but needs to be decided before any more blueprint file edits — **agent was explicitly told to stop rewriting blueprint content and instead report the current file/id/ports state for review.**
  - **🔧 FOLLOW-UP FIX (2026-06-23 morning, before resume):** A second duplicate was discovered — `robot_charging_port_bp.json` at `modules/energy/` (250kg, malformed `id: "robot_charging_port_bp"` with `_bp` suffix). This file was blocking the same way: two robot_charging_port files with similar IDs, different masses, ambiguous lookup. **FIXED**: Deleted the module version; kept canonical unit version (`units/infrastructure/robot_charging_port_bp.json`, 350kg, `id: "robot_charging_port"`). Committed as 1dfb2a0d to galaxyGame. Same pattern as gas_separator—one file is malformed, one is canonical. **Outstanding**: gas_separator still needs the same fix (delete one of the two duplicate files). No further cleanup before final rake can proceed.
  - **🛑 SESSION PAUSED (2026-06-22) — do not resume without review.** Pause message sent: stop all further blueprint edits to gas-separator files; the agent had done multiple raw `cat > file << EOF` full-file rewrites this session without diff review (`solar_expansion_rig_bp.json`, `gas_separator_bp.json` ×2 locations, `gas_separator_unit_bp.json` ×2 locations including the duplicate created mid-session), plus a `sed -i` pass on `task_execution_engine_v2.rb`. Asked to report the exact current file list for anything with "gas_separator" in the name (path + id + ports block only, no further rewriting) and hold.
  - **Methodology note for next reviewer**: this session repeatedly ran into the same root cause — blueprints and operational data created by different agents at different times, some stale or non-conforming to current template versions (`unit_blueprint_v1.3`, `module_blueprint_v1.2`, `rig_blueprint_v1.2`). Tracy's standing correction throughout: when a gate fails, check whether the underlying blueprint/operational-data needs updating to match current templates and intent — don't just patch the gate or the engine to accept stale data. Apply this same scrutiny to any blueprint/operational-data file not yet reviewed this way.
  - **Known risk carried over from `CLAUDE_FREE_GUIDE.md`'s logged lessons**: `sed -i` was used directly on `task_execution_engine_v2.rb` for the hub-naming fix. This tool previously corrupted `strategy_selector.rb` in an earlier session (see `What Works` lessons). Tracy reviewed and accepted the risk for this edit — flagging only so the corrected file gets a real diff review once the session settles, not because Tracy asked for it to be redone.
- **🔄 SESSION RESUMED (2026-06-23, morning) — Both Ryzen and m4 tasks back in active/ status.** Claude free session ended early; resuming with fresh planning. Cleanup handoff updated with robot_charging_port fix completed. Ryzen continues with: (1) delete gas_separator duplicate, (2) run final rake validation. m4 continues with: Phase 4 implementation (create tasks, wire profile, validate). Both tasks blocking each other on final rake validation — sequencing: Ryzen cleanup first, then m4 rake. Evening session will coordinate both agents' completion reports.

### ✅ Manifest v1 → v2 Draft Complete (Luna Precursor) — ready for review, not yet committed
- **Deliverable**: `lunar_precursor_manifest_v2_DRAFT.json` — draft v2 manifest for the Luna precursor mission, all 8 `tasks_v2/` task files' `task_id` values confirmed and mapped via `task_affinity`.
- **All 8 task_ids confirmed** (none match their filenames — v1 guess to use filename-as-task_id was correctly rejected): `deploy_solar_rig`, `deploy_comms_equipment`, `deploy_puh_and_ppmu`, `deploy_thermal_extraction_unit`, `deploy_pve_unit`, `inflatable_tank_deployment`, `deploy_gas_separator`, `deploy_volatiles_storage`.
- **Two real gaps found and resolved in the draft** (not just converted from v1 — actually corrected):
  1. **Gas Separator was never in v1 at all** — root cause confirmed: `task_deploy_gas_separator.json` is dated 2026-05-02, while the other 7 task files are dated 2026-04-09. The task didn't exist yet when v1 was authored. Added fresh to v2 (`gas_separator` x1).
  2. **Inflatable Gas Storage was also never in v1** — `deploy_volatiles_storage` requires it (confirmed real per 06-19 finding 3b), but v1 never stocked it. Added fresh to v2 (`inflatable_gas_storage` x1).
  3. **`inflatable_cryo_tank` count corrected from 3 → 5**: `deploy_gas_separator`'s 3 `connect_units` calls (h2/o2/he3 outputs) imply 3 dedicated tanks; `deploy_volatiles_storage` separately deploys 2 more. Combined demand is 5, not v1's 3. **Tracy confirmed (2026-06-20): draft with corrected counts, don't just flag the shortfall.**
  4. **`inflatable_pressure_tank` count corrected from 1 → 2**: same pattern — v1 had 1, but `deploy_volatiles_storage` deploys an additional 1 on top of existing usage.
- **Open items, not yet resolved, flagged inline in the draft via `_note` fields** (strip before this becomes a real production file):
  - v1's rover, surveyor, harvester, RTG, and solar panels (Regolith Harvester Rover, SMR-500 Surveyor, HRV-400 Harvester, RTG, Compact Solar Panel ×10) still don't map to any of the 8 task files and have no `task_affinity` available — remain unplaced in the draft. (CAR-300 robots and Robot Charging Port were added 2026-06-20 despite the same gap — see correction below — but these five remain genuinely excluded for now since Tracy hasn't separately confirmed they belong at this stage.)
  - `task_affinity` as a comma-separated string for items shared across 2 tasks (`inflatable_pressure_tank`, `inflatable_cryo_tank`) is **improvised** — no v2 example shows a shared-task item. May need to be an array instead. Check against real schema/parser if one exists before treating as final.
  - No v2 example shows an equivalent to v1's `supplies` category (repair kits, fuel tanks, N2) — drafted under `consumables` for now, unconfirmed as correct placement.
  - `structural_assets` left empty — nothing in the precursor task set clearly maps there, but the field exists in the industrial-kit v2 example.
- **Correction (2026-06-20, post-draft review, round 1)**: `water_jug_pack_5gal` x10 was carried over from v1's `supplies` list uncritically — but this is a **crew-support item, and the Luna precursor mission is unmanned** (no people, just the AI Manager and the base, per `AI-MANAGER-LUNA-BEHAVIOR-GOALS.md`). Removed from the draft.
- **Correction (2026-06-20, round 2)**: same blind spot, caught again — `compressed_nitrogen_tank`, `methane_refill_tank`, and `lox_refill_tank` were also carried over from v1 without checking actual relevance. Per `LUNA_BASE_ESTABLISHMENT.md` design canon: **N2's only documented use is habitat atmosphere mix** (no habitats exist at this precursor stage); **CH4's only uses are skimmer refueling and Sabatier feedstock** (no skimmer is part of this manifest, no Sabatier loop yet); **LOX is for skimmer refueling** (same — no skimmer in this manifest). All three removed. Tracy's framing: this stage is comms, power, tank farm, robots — don't bring fuel/atmosphere consumables for systems that don't exist yet.
- **Resolved ISRU-vs-scope tension**: Tracy's "comms, power, tank farm, robots" framing initially seemed to conflict with the draft's inclusion of TEU/PVE/Gas Separator (the ISRU chain) — resolved by cross-checking against `npc_base_deploy_manifest_v3.json`, a generic multi-world template from last year. That template includes the full ISRU/construction set (TEU, PVE, 3D Regolith Shell Printer, I-Beam Printer, Mining Harvester, Surface Prep Unit) alongside comms/power/tanks — confirming ISRU units belong in "initial infrastructure," Tracy's correction was specifically about **consumables** (don't bring fuel/atmosphere stock you don't need yet), not about excluding ISRU hardware.
- **New finding from the generic template comparison**: `npc_base_deploy_manifest_v3.json` also has **Inflatable Habitat Unit x2 and Inflatable Greenhouse Unit x2** — correctly **NOT carried over** to this draft, since those imply crew/biological support and would contradict the no-people constraint (same category of mistake as the water jugs, caught proactively this time by cross-checking against the constraint before adding).
- **Robots and charging port added (2026-06-20, Tracy confirmed)**: `car_300_deployment_robot_mk1` x2 (present in v1 and the generic template) and `robot_charging_port` x2 (present only in the generic template, added per Tracy's confirmation that the 2 CAR-300 robots need a charging point) — both added to the draft. **Real gap exposed**: neither has a `task_affinity` — no `tasks_v2` file currently has a `deploy_unit` effect for CAR-300 or a charging port. This is a genuine missing-task-coverage finding, not an omission in this manifest; flagged inline in the draft (`task_affinity: null`) for a future `deploy_car_300_robots`-style task file. Also connects to the 06-19 finding that `HeavyLander`'s charging ports should NOT be generalized — a dedicated charging-station unit (like `robot_charging_port`) is the correct pattern, confirmed again here.
- **🔄 Correction (2026-06-22) — robot_charging_port's `task_affinity: null` note is now stale, and the "dedicated charging-station unit" framing above needs revisiting.** A `task_deploy_robot_charging_port.json` task file was found to already exist (dated 2026-04-10) — the "no tasks_v2 file currently deploys this" claim above was incorrect, the file existed all along and got missed. **Separately, the design itself has changed**: Tracy clarified the charging port isn't simply a stationary charging-station unit — it needs a transit-then-surface-deployment lifecycle (rides on the HLT during transport, relocates to the lunar surface after landing, continues charging CAR-300 robots until/through the power grid coming online). This may mean the manifest entry's role/count is still right but the underlying task/blueprint behavior needs review before this is truly closed — **the manifest draft's `_note` field for `robot_charging_port` needs rewriting, not just a task_affinity fill-in.** See the HLT Mass-Capacity entry below for the parallel blueprint-side half of this finding.
- **🔄 RTG needs to be added to this manifest (2026-06-22, not yet done)** — was one of the "5 unplaced v1 items" noted above (line 66). Tracy confirmed 2026-06-22: RTG belongs in the manifest as backup/night power alongside the solar rig (primary). Blueprint mass data now exists (280kg, see HLT Mass-Capacity entry below) but the manifest entry itself (count, role, task_affinity) has not yet been drafted. **Action needed**: add `radioisotope_thermoelectric_generator` (or matching id) to `required_hardware`, confirm whether a `deploy_rtg`-style task exists or needs creating.
- **Methodology lesson, restated after two rounds of the same mistake**: the first draft pass treated v1's (and now the generic template's) inventory as trustworthy source data to carry forward, rather than checking each item against this specific mission's actual constraints (unmanned, infrastructure-stage-only). Caught twice on consumables (water, then N2/CH4/LOX) before catching it proactively on the habitat/greenhouse units. Apply this same scrutiny to anything still unreviewed — particularly the unplaced v1 items below.
- **New unchecked dimension (2026-06-20): payload mass.** This draft has never been checked against HLT cargo capacity at all. **Update 2026-06-22: actively being closed, not just researched** — see the HLT Mass-Capacity Research & Fix entry directly below for current state. Do not treat this draft as launch-ready on weight until that thread reports a stable, reviewed conclusion — Phase 4 (real cargo-mass validation logic) in particular is not yet built, only proposed.

**Everything else: none currently active** — see Parked/Not Yet Filed section below.

---

### � HLT Mass-Capacity Research & Implementation (PHASES 1-3 COMPLETE)
- **Task file:** `2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md` — moved from `backlog/phase5/` to `active/` on 2026-06-22. YAML status: `ACTIVE`.
- **Machine:** Ryzen 7 (Qwen3.5-27B, Copilot pinned)
- **Status:** ✅ **PHASES 1-3 COMPLETE** (2026-06-22, 21:30-21:50 UTC). Phase 4 holding for sign-off.
- **Important correction to prior status.md framing**: earlier notes (2026-06-21) said `heavy_lift_transport_bp.json` might not exist on disk / be a "web-session-only" file. **This was wrong** — the file does exist at `data/json-data/blueprints/crafts/space/spacecraft/heavy_lift_transport_bp.json`. The agent's initial search tooling missed it (and several other real files — gas separator, car_300 robot blueprint, robot_charging_port-adjacent code) on first pass; Tracy corrected this multiple times during the session. **Lesson for future sessions**: when an agent reports "file does not exist," treat that as provisional until grep/search has actually been run with correct, current paths — don't propagate it into status.md as settled fact (as happened here).
- **Confirmed findings (corrected, current as of 2026-06-22):**
  - `empty_mass_kg: 163,000.0` is authoritative on the (now-confirmed-real) HLT blueprint, used by `HasMassCalculation`. The materials-list sum (193,000 kg) discrepancy is a real +30,000 kg gap, assessed as likely construction-input vs. final-assembled-mass (machining waste, TPS margin) rather than a data error — no fix applied, documented as acceptable.
  - Cargo-mass calculation **confirmed missing** — `HasMassCalculation`/`LaunchPaymentService` only sum mounted units (base_units/base_modules/base_rigs), nothing sums manifest/inventory cargo.
  - Capacity-limit check **confirmed missing** — `cargo_capacity_m3: 1000.0` exists (volume only), no mass-based limit existed prior to this session's edits.
  - **New context found this session**: legacy integration scripts (`blueprint_integrity_checker.rb`, `starship_integration_test.rb`, `test_build.rb`) show the *original* design intent explicitly required a `max_cargo_mass_kg`-style field and cargo-validation logic — this was a real, built-out intent that was never carried into the current production code. Treat this as confirmed design history, not speculation.
  - mk1/mk2/mk3 versioning scheme (previously logged as "not yet started," see 2026-06-20 clarification entry below) **has now actually begun**: HLT blueprint renamed `heavy_lift_transport_bp.json` → `heavy_lift_transport_mk1_bp.json`, with `max_payload_kg: 150,000.0` (the borrowed real-world Starship figure, see below) added to `physical_properties`.
- **Implementation — Phases 1-3 COMPLETE (2026-06-22)**:
  
  | Phase | Scope | Status | Details |
  |-------|-------|--------|---------|
  | 1 | HLT naming (mk1 versioning) | ✅ Already Complete | File: `heavy_lift_transport_mk1_bp.json` at `crafts/space/spacecraft/` |
  | 2 | HLT payload limit | ✅ Already Complete | `max_payload_kg: 150000.0` in `physical_properties` |
  | 3a | solar_expansion_rig mass | ✅ COMPLETE (2026-06-22) | Added `physical_properties.empty_mass_kg: 800.0` |
  | 3b | gas_separator_unit mass | ✅ COMPLETE (2026-06-22) | Added `physical_properties.empty_mass_kg: 1200.0` |
  | 3c | car_300_deployment_robot_mk1 mass | ✅ COMPLETE (2026-06-22) | Added `physical_properties.empty_mass_kg: 4500.0` |
  | 3d | RTG mass (radioisotope_thermoelectric_generator) | ✅ COMPLETE (2026-06-22) | Added `physical_properties.empty_mass_kg: 280.0` (NASA MMRTG-scaled estimate) |
  
  All 4 JSON files validated (`python3 -m json.tool`). All use convention `empty_mass_kg` matching existing Luna precursor blueprints. Agent task file YAML confirmed `ACTIVE`.

- **Correction: robot_charging_port lifecycle** ✅ (2026-06-22, post-Phases-1-3)
  - **Earlier conclusion (now corrected)**: "may not need its own blueprint, it's craft operational data"
  - **Actual design** (Tracy clarification): general-purpose dockable unit with dual lifecycle support:
    - Can be **mounted permanently** on a craft (HLT, cycler, station) for long-term robot support, OR
    - Can be **deployed to a surface** as standalone infrastructure (Luna: CAR-300 robots charge during HLT transit, port stays on surface post-landing)
  - **File found**: `robot_charging_port_bp.json` exists at `units/infrastructure/` (350 kg with `charging_spec`)
  - **Correction applied**: Added `deployment_data` section with `deployable: true`, `default_deployment_time_hours`, `printer_required: false`, confirming Earth-manufactured + no field assembly needed
  - **JSON validated** ✅
  - **Design principle**: Don't assume a unit's first known use case is its only one (applies to gas-separator module too — designed for HLT skimmers, but not exclusively)

- **Phase 4 — HOLDING before design** (not yet started)
  - **Scope**: Design new `CargoMassCalculation` service layer (or `HasPayloadCapacity` concern) to sum manifest inventory items' blueprint masses and validate against `max_payload_kg: 150000`
  - **Integration**: Into `LaunchPaymentService.pay_for_launch!` as fail-fast gate before launch cost calculation
  - **Pattern**: Matches legacy test scripts' original design intent (blueprint_integrity_checker.rb, starship_integration_test.rb, test_build.rb all show validation was always planned, never implemented in production)
  - **Status**: Agent confirmed ready, paused per plan to let planner review Phases 1-3 completion + bundle Phase 4 design approval. Awaiting sign-off to proceed.

---

## Luna Phase Queue

| Phase | Service | Status | Gate |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager integration | ✅ Complete (commit cd4d6800, archived f5fcbbe) | — |
| 2 | `Logistics::ManifestGenerator` → AI Manager integration | ✅ Complete (commit 21b10ef0) | Phase 1 complete ✅ |
| 3 | `Logistics::ShortageDetector` + `Logistics::ImportRequestGenerator` → AI Manager | ✅ Complete (commit 32b31f54) | Phase 1 & 2 complete ✅ |
| 4 | Consumption-aware ordering + precursor phase awareness | ✅ Complete (commit 5a2af17a, re-implemented lost logic) | Phase 3 complete ✅ |
| 5 | Simulation observation — market emergence, stockpile accumulation, no new feature work | 🔄 Underway — Return Cargo Profit Optimization committed (`d458cf88`); Luna Precursor V2 verification active (see Active Tasks); atmospheric transfer Titan/Venus rescope in progress | Phase 4 complete ✅ |

**Note:** A separate System B *settlement-expansion* numbering scheme exists in narrative/storyline docs and is now canonically defined in `DEVELOPMENT_ROADMAP.md` (2026-06-19, supersedes the original Jan 2026 roadmap): **Phase 5+** = Luna simulation calibration/observation (current focus, no new features) → **Phase 6+** = L1/LEO Depot + gas processing pipeline (off-screen "Pre-Act 2 Infrastructure," requires Phase 5+ complete) → **Phase 9+** = shipyard, Phobos/Deimos hollowing, wormhole topology, multi-system coordination (folds in old "Phase 7-8" work, requires Act 2 Eden maturity). This is **not contradictory** with the AI-Manager-service-integration phases in this table — they track different things: this table = service integration milestones (complete); System B = world/settlement expansion milestones (not started, gated behind Luna fuel loop proof). Per the 2026-06-18 clarification, Phase 6+/9+ work is explicit "off-screen technical groundwork" between narrative Act 1 and Act 2, not a player-facing Act of its own. See 2026-06-16/17/18/20 Completed entries below for full reconciliation history, and `DEVELOPMENT_ROADMAP.md` + `docs/planning/GALAXY-GAME-PHASE-ALIGNMENT.md` for the current canonical reference going forward.

---

### 📌 NEW (2026-06-21) — Two small pending items from docs reorganization completion report
1. **Agent folder cleanup** — migrating old tasks from the legacy `agent/` folder into the `agent-tasks` repo. Reported as needing a separate task, not yet created.
2. **Remove `agent/` folder after migration is complete** — Tracy's decision, not yet made (and gated on #1 above happening first).
Both low priority, no urgency — logged so they don't silently drop.

### ⚠️ CORRECTED (2026-06-20) — mass calculation system exists for installed components; cargo/payload mass tracking still unconfirmed
While reviewing the manifest v2 draft for the Heavy Lift Transport, Tracy raised a real constraint: **launches shouldn't underweight or overload the craft.** Initial check of the 4 HLT files found only `cargo_capacity_m3` (volume) and `empty_mass_kg` (craft's own dry mass) — no payload mass limit. **Tracy then provided `HasMassCalculation` (Ruby concern)**, which corrects the picture:
- **The mass-calculation architecture is real and already implemented.** `get_base_craft_mass` reads `physical_properties.empty_mass_kg` from operational_data or blueprint (matches `heavy_lift_transport_bp.json`'s `empty_mass_kg: 163000.0` exactly). `get_unit_mass`/`get_module_mass`/`get_rig_mass` each look up a blueprint and expect `physical_properties.mass_kg` (or `empty_mass_kg`) to be populated, raising a clear error if not found — consistent with the "fail fast, don't fake success" pattern already established elsewhere in the project.
- **This is a dry-mass/structural-mass calculator** — it sums the craft's own mass plus units/modules/rigs *installed on ports* (`base_units`, `base_modules`, `base_rigs`). **It is NOT confirmed to calculate cargo/payload mass** — items sitting in a manifest's `inventory` (to be deployed planetside, not mounted to the craft) are a structurally different thing from `installed_units`/`hooked_to_port` items, per the v1/generic manifest schema reviewed this week. Whether cargo mass is tracked separately is **unconfirmed — worth checking** (Tracy's own assessment).
- **`LaunchPaymentService` reviewed next, confirms and extends the picture**: this service consumes `craft.calculate_mass` (preferring the `HasMassCalculation` method directly, only falling back to manual recalculation if unavailable) to compute **launch cost** (`mass_kg × cost_per_kg + base_cost`), not capacity validation — still no comparison anywhere against `cargo_capacity_m3` or any "too heavy/too light" check. Confirms mass gets calculated and charged for, but not yet validated against a limit. Real, mature, working code (multi-currency payment, bond issuance for unpaid balance) — not aspirational/scope-drift scaffolding like the `rand()`-gated rake tasks found earlier this week.
- **Data discrepancy found, not yet resolved**: `LaunchPaymentService`'s `extract_unit_mass` fallback estimates mass from summing `required_materials` where unit is kilogram, when no explicit mass field exists. Applying that logic to `heavy_lift_transport_bp.json`'s own `blueprint_data.materials` list (stainless_steel 150000kg + electronics 5000kg + thermal_protection 8000kg + titanium_alloy 20000kg + carbon_fiber 10000kg) sums to **193,000 kg — not the same as the stated `empty_mass_kg: 163000.0`**. Worth resolving which figure is authoritative before relying on either for capacity math.
- **Real remaining gap, narrowed**: even with calculation architecture confirmed, it depends on every candidate unit blueprint having `physical_properties.mass_kg` populated. Not yet confirmed for any of the Luna precursor manifest's actual items (Solar Expansion Rig, TEU, PVE, Gas Separator, inflatable tanks, CAR-300, etc.). And **no capacity-limit check exists anywhere found so far** — mass gets calculated/charged, never validated against a max.
- **Provenance clarified (2026-06-20)**: the HLT was originally prototyped as "Starship" (renamed later — `starship_integration_precursor_mission.rb`'s stale filename, confirmed 06-19, is a leftover from this). Tracy confirmed the original design basis was real-world Starship's payload capability. **Baseline payload figure established for documentation purposes**: real-world Starship's design target is consistently cited as **150 metric tons (150,000 kg) payload to LEO in fully-reusable mode** across current sources (June 2026) — Wikipedia, eoPortal, and industry coverage all converge on this figure (with reusable-mode range generally given as 100-150t, expendable-mode ceiling 200-250t). 150,000 kg reusable-mode is the most consistent, citable baseline and is being adopted as **HLT's documented payload mass limit going forward**, distinct from `empty_mass_kg` (craft's own dry mass) and `cargo_capacity_m3` (volume only). **This is a borrowed real-world figure adopted by decision, not something derived from in-universe design docs** — no prototyping-phase research doc with an original target figure was found to exist; Tracy confirmed using the real-world figure as the baseline instead of searching further.
- **Still needs deciding**: where this 150,000 kg figure should actually live in the data (new field on `heavy_lift_transport_bp.json`? a method addition to `HasMassCalculation` or a new capacity-check service?), and the parallel effort to get `mass_kg`/`empty_mass_kg` populated on individual deployable unit blueprints so the Luna precursor manifest's actual loadout can be checked against it.
- **Next action**: grep/confirm (per standing rule) whether a cargo/payload mass calculation or any capacity-limit check exists anywhere in the codebase beyond what's been reviewed here — do not assume either way. **Task file generated 2026-06-21**: `2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md` — ready to dispatch to a local agent. Covers all 4 open steps (resolve empty_mass_kg/materials-sum discrepancy, confirm cargo-mass calculation status, confirm capacity-check status, audit mass data on the 12 Luna precursor manifest items) with everything already-confirmed pre-loaded so the agent doesn't redo settled work. Not yet dispatched as of session close.

### 📌 Clarified (2026-06-20) — mk1/mk2/mk3 HLT versioning is separate from base/lunar/landing variants
Tracy confirmed: the planned **mk1/mk2/mk3 craft versions are a distinct versioning scheme** — separate from the existing `heavy_lift_transport` / `heavy_lift_transport_lunar` / `heavy_lift_transport_landing` operational-data variants reviewed above (those are differentiated by mission profile, not mark/generation). Do not conflate the two. **Update 2026-06-22: no longer "not yet created" — `heavy_lift_transport_mk1_bp.json` now exists** (renamed from `heavy_lift_transport_bp.json` as part of the HLT mass-capacity task, see Active Tasks above). mk2/mk3 still don't exist — only mk1 so far. Flagging so a future session doesn't assume the lunar/landing variants satisfy this versioning need, and doesn't assume mk2/mk3 exist just because mk1 now does.

- **Deposit/Spawning gap** — confirmed real (`Deposit::PlausibilityEngine` + `DepositSpawner` designed in full, never implemented; `ResourceDeposit` model shipped but unused). Explicitly deprioritized below Luna MVP — TEU/PVE byproduct chain is the documented MVP water source. Pick up by drafting implementation task file(s) once Luna MVP has headroom.
- **Backlog Hygiene Audit** — content drafted (file corruption scanning pattern, stale-belief propagation pattern, deposit/spawning finding, `ResourceAcquisitionService`'s orphaned JSON config, DigitalTwinService re-verification, reference-doc existence checks) but not yet saved as a task file. Sequenced for phase6+ or later. Needs to be regenerated as a file from session history if filing is desired.
- **Shell-printing/tank-stage-advancement gap (3c)** — confirmed (refined 2026-06-20): even once `task_print_inflatable_tank_shells.json` and `task_regolith_shell_printer_operations.json` are inserted into phase order, **neither one actually advances `deployment.stages` on the tank itself** — one only sets printer state, the other has no effects at all. Inserting them gets sequencing right but does not close the real gap. Nothing currently moves a tank through `inflate → print_shell → pressurize`. This is a genuine missing-feature finding, not a wiring fix — needs its own implementation task, not a sequencing task. Do not mistake as resolved.
- **Landing pad insertion (3d)** — `task_surface_preparation_unit_operations.json` confirmed covers it (metadata only, no effects); agreed insertion point is the final task of Phase 3, before any Mission 2 resupply. Report-only for now — not wired in.
- **`ResourceAcquisitionService` hardcoding** — confirmed `is_local_resource?` hardcodes a static array instead of reading `resource_acquisition_logic_v1.json` (exists, completely orphaned). Same pattern as `ai_manager_*.rake` scope-drift files. Held as corroborating evidence for Backlog Hygiene Audit above, no separate task needed.
- **Drone Bay Operational Data** — task file created 2026-06-19 (`2026-06-19-LOW-DESIGN-DRONE-BAY-OPERATIONAL-DATA.md`), not yet copied to repo. Scopes `drone_bay_ops.json` for the Heavy Drone Bay Module; connects to a design clarification that robot charging belongs on its own unit, not on `HeavyLander`. LOW priority, not MVP-critical.
- **Venus skimmer CO2/O2/CO handling** — correctly NOT scoped as a task yet; likely just Inventory-layer tracking (gas species as line items). Decision: let Phase 5 simulation observation reveal actual gaps before pre-designing. Venus orbital-shade/CNT terraforming context confirmed as real design intent but out of scope until Mars/Venus/Titan/Ceres ops + cyclers/logistics are live (phase6+/7+ prerequisite).
- **DigitalTwinService** — confirmed real, well-specified, never implemented; blocker ("test suite under 10 failures") already satisfied, but explicitly sequenced after Mars/Venus/Titan/Ceres ops + cyclers/logistics, same gate as Venus item above. Do not dispatch early.
- **AI-MANAGER-SERVICE-INTEGRATION placeholder** — confirmed by two independent review passes (Claude + Ryzen 7) to be an unfilled generic template, never real content. Agreed to archive to `deprecated/` — **the actual `git mv` + header + commit has not been executed yet. This is the one remaining pending-archive action.**
- **Titan atmospheric transfer task file** — 27B offered to create `2026-06-18-HIGH-REFACTOR-ATMOSPHERIC-TRANSFER-TITAN-DATA-DRIVEN.md`; confirmation that the file was actually created was not captured before the thread moved on. **Needs verification next session.**
- **ROUTING_LOGIC.md update** — mid-revision since 2026-06-16 (machine topology corrections, simplified agent stack, 35B validation status, dual-machine concurrent workflow finding), not finalized.
- **L1/LEO Depot task** (third of Perplexity's three research handoffs) — not yet reviewed by Claude.
- **`TASK-luna-mvp-ai-manager-settlement.md`** — ready to dispatch since 2026-06-17, not yet started. Assigns Qwen 35B to implement real logic for the remaining V2 stub handlers not covered by the active precursor task, build a real (non-`rand()`) settlement-siting decision function, wire it into an observable rake task. **Governing design doc: `AI-MANAGER-LUNA-BEHAVIOR-GOALS.md` (new 2026-06-20)** — defines decision principles (data over randomness, real state checks only, fail-fast, explainable logged decisions) and core behavior areas (siting, initial buildout, production/inventory, ongoing loop) this task should implement against. Read it before dispatching.
- **Image asset generation** — `GALAXY-GAME-IMAGE-ASSETS.md` style guide complete; "Not generated" inventory rows (Cycler priority-10 component list) still need ChatGPT generation time.
- **`PHASE_ALIGNMENT_SUMMARY_2026-06-17.md` polish item** — `01_story_arc.md`'s Act cross-reference table jumps Act 1 (phase5+) → Act 2 (phase9+) with no row for phase6+/7+/8+. Feedback sent to storyline agent; not blocking, polish-only.

---

## Completed

### 2026-06-20 Session — Canonical Phase Docs Received: `DEVELOPMENT_ROADMAP.md` + `PHASE_ALIGNMENT_SUMMARY_2026-06-18.md`
- ✅ **`DEVELOPMENT_ROADMAP.md`** (dated 2026-06-19) — now the single canonical reference for System A (complete, Phases 1-4) + System B (5+/6+/9+) + Narrative Acts cross-mapping. Explicitly supersedes the original January 2026 roadmap (old Phase 1-5 Sol→Wormhole NPC buildout sequence — fully retired, mapped onto the new structure in its "Historical Context" section). Confirms Phase 5+ is Luna calibration **only** — explicit principle: "Only create tasks that are prerequisites needed BEFORE the Luna simulation can run. Do NOT add new features to phase5+." Lists supporting docs for both ISRU/operations and planning — including `GALAXY-GAME-PHASE-ALIGNMENT.md` and `AI-MANAGER-LUNA-BEHAVIOR-GOALS.md`, confirming both those docs (reviewed earlier this session) are correctly cross-referenced from the canonical source.
- ✅ **`PHASE_ALIGNMENT_SUMMARY_2026-06-18.md`** — follow-up to the 06-17 reconciliation, closes the one minor open item flagged then (the missing "Pre-Act 2 Infrastructure" row). Confirms: Phase 6+/7-8 work is explicit off-screen technical groundwork between Act 1 and Act 2, not its own narrative Act. Phase 7-8 work (shipyard, Phobos/Deimos hollowing) folds into `phase9+/` rather than getting its own number, gated on "Act 2 Eden system maturity achieved" rather than a numbered phase.
- 📌 **This closes the loop on status.md's own "Phase 5-11" language**, which was based on the original (pre-06-18) 7-step Phase 5-11 framing from the 06-16/17 handoff. The Luna Phase Queue note above has been updated to reflect the current canonical 5+/6+/9+ structure (7+/8+ folded into 9+, 10+/11+ dropped from the active framing).

### 2026-06-20 Session — `PRICE_DISCOVERY_LIFECYCLE.md` Recovered, Then a Deeper Phase-Numbering Conflict Found
- ✅ **File recovered and restored** — after Time Machine was confirmed exhausted (file didn't exist as of 03-17 snapshot, already corrupted by 03-23), a complete uncorrupted version was found in a separate manual archive: `docs-backup-03032026`. All four previously-identified gaps (LDC mint/virtual ledger intro, GCC/USD peg intro, EAP transport-category intro, AI Manager profitability-evaluation intro) confirmed filled cleanly. **Deliverable**: `PRICE_DISCOVERY_LIFECYCLE_RECOVERED.md`. The duplicate "Phase 4" header noted on first pass (two consecutive Phase 4 sections) was **fixed by Qwen in a follow-up pass** — confirmed clean sequential 0→6 numbering in the restored file as currently uploaded.
- ⚠️ **Found, accuracy corrected after discussion**: `PRICE_DISCOVERY_LIFECYCLE.md`'s "Infrastructure Progression & Price Impact" Phase 0-6 sequence (economic/infrastructure milestones) is a genuinely separate axis from System A/B — **but this was initially overstated as needing an immediate rename/fix**. Correction: it's low-priority (nothing has reached even this doc's Phase 1 "Luna ISRU online" in concrete simulation terms yet — see below), and the live, actually-blocking phase confusion is in the **backlog folder contents**, not this doc. Revisit renaming this doc's headers later if it actually causes confusion in practice; not urgent.
- 📌 **What "verifying Luna MVP readiness" actually means right now**: the active Luna Precursor V2 Sequence Reconciliation task (Steps 1-4) has been verifying that everything needed to start Luna base build testing is in place. Simulation testing, the Step 5 rake task rewrite, and integration tests have **not started yet**. (Note: this was previously logged using a "Phase 4" label — see attribution correction below; that numbering was not Tracy's and should not be treated as established terminology.)
- 📌 **Phase folders are not a strict architectural gate**: `phase5+/`, `phase6+/`, etc. are an ongoing, imperfect best-effort attempt to sort backlog tasks by roughly when they'll be needed. They are provisional and openly being discussed/adjusted — having pre-existing tasks in them is not itself a process violation.
- 📌 **Correction on attribution (2026-06-20)**: "Phase 4" as a label for "verifying everything needed for Luna MVP testing is in place" was **Claude's term, not Tracy's** — introduced casually (likely echoing System A's "Phases 1-4 complete" framing) and then used back by Tracy until the documentation conflicts this caused became clear. This is itself a contributing example of the exact problem being sorted out in the third thread below — Claude should not invent or assert phase-numbering/labeling on Tracy's behalf going forward; report findings and let Tracy or the dedicated documentation-sorting session set the terms.
- ✅ **Docs folder reorganization — COMPLETE, committed and pushed** (2026-06-21, Ryzen 7, commit `415805b7`). 27+ → 18 directories, 49 files changed. Deleted empty `tutorials/`; merged `operations/`+`data/` into `developer/`; merged `ai_manager/`+`economy/`+`stations/`+`systems/`+`simulation/`+`wormhole/` into `architecture/`; other merges into `gameplay/`/`wiki/`/`archive/`. README updated with all paths fixed and a new structure table. Task file `2026-06-20-MEDIUM-DOCS-REORGANIZATION.md` moved to active folder, marked complete. **Two items still pending, not part of this commit**: (1) agent folder cleanup — migrating old tasks into the agent-tasks repo — flagged as needing a separate task; (2) review the restored `PRICE_DISCOVERY_LIFECYCLE.md` for minor artifacts post-restore — Tracy's call when reviewed. Both planning-doc placement questions from earlier (is `planning/` the right home for `GALAXY-GAME-PHASE-ALIGNMENT.md` etc., and `patterns/`'s destination) are presumably resolved by this commit but not explicitly confirmed in the report — verify against the new structure table next time those docs are touched.
- ✅ **THIRD THREAD — documentation/phase-language reconciliation, ran on Ryzen 7 (not a separate machine) — COMPLETE, reported 2026-06-21**: Tracy opened this as a separate session specifically to sort out conflicting phase terminology across documentation, prompted directly by the inconsistencies surfaced this session (including Claude's own "Phase 4" mislabeling above, the `PRICE_DISCOVERY_LIFECYCLE.md` Phase 0-6 axis, and the folder-vs-canonical-doc mismatches from the M4 audit below). **Correction**: this ran on the same Ryzen 7 machine as the Luna Precursor V2 task, used while waiting for session time to resume that work — not a fourth, independent machine/thread. **Results**:
  - **`DEVELOPMENT_ROADMAP.md`** rewritten and moved to `planning/` — updated from the stale Jan 2026 version to the current canonical phase structure (the version already reviewed and logged in this file's 2026-06-20 entry above).
  - **`PRICE_DISCOVERY_LIFECYCLE.md`** restored from the manual backup (same recovery already logged above) AND the cosmetic "Phase 4 Expansion" → "Phase 3" duplicate-header fix applied — this is the exact fix Qwen was asked to make in an earlier session this week; confirmed landed.
  - Docs reorganization (separate entry above) also closed out in this same report.
  - So there are really **two machines in play, three lines of work, all now resolved or in a clean state**: Ryzen 7 carried the Luna Precursor V2 task (Steps 1-5 done, holding for review), the documentation-reconciliation session (now complete, see above), and the docs folder reorg (now complete, see above); M4 carries the separate backlog-relevance audit (still ongoing as of last update).
- 📌 **What the M4 audit is actually surfacing**: during backlog review, Qwen is **rewriting/extracting tasks against the current codebase and current task-file template** to pull out what's still actionable — not just classifying tasks as resolved or not. This is a third category alongside the two from Project History above:
  1. **Silently-resolved**: problem already fixed elsewhere, verify then archive.
  2. **Incomplete concept**: never fully thought through, needs a real finish/drop/rescope decision.
  3. **Correct-but-format-stale**: the underlying idea is still right, it just needs rewriting against the current template/codebase to become actionable again — this is the category most of the M4 work is actually about.
- 🎯 **Resolved (2026-06-20): what `phase6+/` should actually be building** — the **Luna lava-tube base**, specifically, as the literal next milestone after the Luna Precursor V2 simulation testing loop proves out. This supersedes both Qwen's earlier guess ("construction pipeline/worldhouse sealing") and `DEVELOPMENT_ROADMAP.md`'s description ("L1 Depot, LEO Depot, gas processing pipeline") as the operative answer going forward — those weren't wrong so much as incomplete/pre-decision. **The worldhouse tasks scattered across `phase6+/` markdown tasks, `tasks_v2/` JSON tasks, and the `2026-06/` mega-structure consolidation task should be grouped together**, because they all serve this one next step (lava-tube base construction), not treated as separate scattered efforts. This is the concrete fix for the "construction-pipeline work split three ways" finding noted below.
- ⚠️ **Found via Qwen (M4) backlog audit — folder contents vs. canonical doc descriptions (lower-priority structural note, not the main finding)**: actual backlog folder contents don't match any of the three documents that describe them (`DEVELOPMENT_ROADMAP.md`, the 06-16/17/18 handoffs, or Qwen's own proposed redefinition). This is a real inconsistency worth resolving eventually but is secondary to the MVP-relevance review above:
  - `phase5+/` — 23 tasks (AI Manager, ISRU, Luna bootstrap, atmospheric simulation) — consistent with canonical "Luna calibration" framing, no conflict here.
  - `phase6+/` — 23 tasks, but contents are **Mission Planner, Resource Deposits, Orbital Construction, TTR/Failure Cascade** — this does NOT match `DEVELOPMENT_ROADMAP.md`'s description of phase6+ as "L1 Depot, LEO Depot, gas processing pipeline." Real mismatch between label and actual folder contents.
  - `phase7+/` — 6 tasks, **purpose undefined**, not described in any canonical doc.
  - `phase9+/` — 11 tasks (Worldhouse AI learning, state schema, wormhole topology) — roughly consistent with "far future/advanced systems" framing.
  - `phase12+/` — 1 task (monitor fix) — not mentioned in any canonical doc at all.
  - Other findings: `tasks_v2/` has 7 worldhouse/lava-tube JSON tasks not referenced in any roadmap doc; `backlog_april_2026/` has 218 old-format files, many referencing deprecated architecture docs; a `reorganization attempt 2` folder exists and should be cleaned up; **construction-pipeline work (worldhouse sealing, structural enclosure) is split three ways** across `phase6+/` markdown tasks, `tasks_v2/` JSON tasks, and a `2026-06/` mega-structure consolidation task — **now resolved as "should be grouped together," see entry above: these are all the same Luna lava-tube base milestone.**
  - **Qwen's phase-definition doc proposal is partially superseded**: the `phase6+` meaning question is now answered (lava-tube base, see above) rather than open. The doc itself — formally defining Phase 5/6/6+/7+/9+ in writing — may still be worth doing for documentation clarity, but the actual decision it was meant to produce for Phase 6 has already been made in conversation. **Tracy has held off on greenlighting the formal doc for now**; this doesn't block grouping the worldhouse tasks in the meantime.
  - **Explicit decision (2026-06-20)**: this M4 audit thread and Ryzen 7's Luna Precursor V2 Step 5 are **independent — Step 5 proceeds without waiting** for this audit to finish, even though both ultimately feed into "is Luna ready for simulation testing." If the M4 audit eventually surfaces something that directly contradicts or blocks Step 5's rake task, that will need a fresh look at that time — but absent that, no blocking dependency assumed.
- 📌 **Workflow lesson**: a manual backup folder (`docs-backup-03032026`) preserved this file where Time Machine couldn't. Worth checking for similar manual backups before assuming Time Machine is the only recovery path for docs corrupted in the same March 17-23 window.

### 2026-06-20 Session — Planning Goals Doc + Flagged Data-Integrity Concern
- ✅ **`GALAXY-GAME-PLANNING-GOALS.md`** — new planning-direction doc: Luna-first focus, "stable simulation over rapid expansion," explicit out-of-scope list (broad expansion, late-game stations, cycler/tug, wormhole/multi-system, cosmetic-only lore). Reviewed against current ground truth — consistent with `GALAXY-GAME-PHASE-ALIGNMENT.md` and everything already locked in this file. No corrections needed. Belongs in `planning/` alongside the alignment doc.
- ✅ **`PRICE_DISCOVERY_LIFECYCLE.md`** — initially confirmed created corrupted with Time Machine path closed (didn't exist 03-17, already corrupted by 03-23). **Superseded later same session**: recovered from a manual backup archive instead — see the dedicated 2026-06-20 "Recovered" entry above for full detail.

### 2026-06-20 Session — Two New Reference Docs + Phase Alignment Doc Correction
- ✅ **`AI-MANAGER-LUNA-BEHAVIOR-GOALS.md`** — new design/requirements doc defining what "good AI Manager behavior" looks like on Luna (data over randomness, real state checks only, fail-fast not fake-success, single source of sequencing truth via `TaskExecutionEngineV2`/`tasks_v2`, explainable logged decisions). Covers settlement siting, initial buildout from cargo manifest, production/inventory tracking, ongoing simulation loop, and operator observability (`rake ai:luna:settle` style). Explicitly out of scope: L1/LEO depot, station/cycler/tug, wormhole/multi-system. **Reviewed against current ground truth — internally consistent, no corrections needed.** Cross-references `TASK-luna-mvp-ai-manager-settlement.md` (still parked, ready to dispatch) as the implementation vehicle for the 3 stub handlers this doc's principles apply to (`construct_structure_from_effect`, `check_unit_state_from_effect`, `manufacture_from_effect` — the other 2 of 5 stubs are now implemented as of yesterday's Step 4 work).
- ✅ **`GALAXY-GAME-PHASE-ALIGNMENT.md`** — new doc unifying System A (service integration phases, source: status.md) and System B (world/settlement-expansion phases, source: 06-16/17 handoff + `10_implementation_phases.md`) into one task-placement reference. **Found and corrected one stale citation**: its System A table listed Phase 5 as "Wormhole topology integration (backlog)" — the pre-06-17 placeholder number, not the corrected "simulation observation" meaning locked in during the 2026-06-17 reconciliation. Fixed with an inline correction note pointing back to the reconciliation. Rest of the doc (System B phase descriptions, folder mapping rules, placement checklist) verified consistent with current status.md — no other corrections needed. **Recommend this doc become the canonical phase-placement reference going forward**, replacing ad-hoc reconciliation notes scattered across handoffs.
- 📌 **Pattern flagged**: this is now the second alignment/reference doc to cite a stale phase number from before the 06-17 reconciliation (the first being the original status.md text itself, fixed 06-19/20). Any doc written or copied before 06-17 that mentions "Phase 5" needs a sanity check against the reconciled meaning before being trusted.
- ✅ **`TASK_OVERVIEW.md`** (stale since 2026-05-29/30, predated Luna Phase 1 implementation) — confirmed it duplicated ground `status.md` already covers in more detail and stays current. Archived with header note explaining retirement and pointing to `status.md` as single source of truth. **Moved to `deprecated/` under the project in agent-tasks (`git mv` + commit executed by Tracy).** Preserved only as historical record of original Luna task-breakdown and the Haiku/GPT-4.1/Sonnet agent-coordination model used at project start — superseded by current multi-agent stack.

### 2026-06-20 Session (Luna Precursor V2 — Steps 1-4 Executed by Ryzen 7/Copilot)
- ✅ **Steps 1-3 re-executed and confirmed** — Ryzen 7 (via Copilot) independently re-ran file existence verification, stub handler audit, and reconciliation findings 3a-3g; results match the 2026-06-19 dispositions exactly (see that entry below for full detail). 3a fix applied correctly (before Step 4 test run, per instruction). 3e confirmed no action. 3c/3d explicitly held as report-only.
- ✅ **3c finding refined** — clarified that inserting the two shell-printing tasks into phase order would NOT close the gap, since neither one advances `deployment.stages` on the tank itself. Confirmed as a genuine missing-feature finding needing its own implementation task. See Parked section above.
- ✅ **Step 4 implemented and corrected** — `connect_units_from_effect` and `set_unit_state_from_effect` built with real validation; first pass used named-port matching (incorrect), revised same session to the generic typed-count port model (`internal_unit_ports`/`propulsion_ports`/`rig_ports`/`storage_ports` via `determine_port_category` + `check_and_decrement_port`). `HeavyLander` charging ports correctly excluded as a special case per instruction. `luna_settlement_integration_spec.rb`: 4 examples, 0 failures, both before and after the port-model revision.
- ✅ **Step 5 explicitly held** — not started, per direct instruction, pending review and go-ahead.
- 📌 **Path-reference correction discovered**: actual JSON data location is `data/json-data/missions/` (`luna_base_establishment/` for manifest+phases+profile, `tasks_v2/` for the 8 task files, `npc-base-deploy/` for generic reference) — not under `docs/new_agent/.../tasks_v2/` as the task file's Step 1 instructions implied. Caused initial dead-end searches before Tracy corrected the path mid-session. **Task file template / GUARDRAILS path references should be updated to prevent recurrence.**
- **Files modified**: `task_deploy_gas_separator.json` (3a fix, applied fresh this session — same fix as 06-19, confirming it had not yet landed in the actual file), `task_execution_engine_v2.rb` (`connect_units_from_effect` + `set_unit_state_from_effect`, full implementation + port-model correction)
- **Note on 3a timing**: 2026-06-19 handoff stated the 3a fix was "applied" that day, but this session's transcript shows the same edit being made fresh on 2026-06-20 with no indication it pre-existed. Likely the 06-19 "applied" note described a decision/approval, not a landed file change, or the file didn't survive between sessions. Not a regression — just confirms the edit is now actually in place. Worth a quick `git log -p task_deploy_gas_separator.json` check next session to clear up which account is literal.

### 2026-06-19 Session (Luna Precursor Verification + Backlog Reconciliation)
- 🔄 **Luna Precursor V2 Sequence Reconciliation** — Steps 1-3 complete, Step 4 in progress (see Active Tasks above for current state)
  - Task: `2026-06-19-HIGH-VERIFICATION-LUNA-PRECURSOR-V2-SEQUENCE-RECONCILIATION.md`
  - Step 1 (file existence): ✅ all 8 `tasks_v2/` files + 3 phase files + profile confirmed real
  - Step 2 (stub handler status): ✅ all 5 `TaskExecutionEngineV2` stubs confirmed still stubs as of 2026-06-16 audit; Luna v2 sequence only needs `connect_units_from_effect` + `set_unit_state_from_effect`
  - Step 3 findings (7 items, all dispositioned):
    - 3a naming mismatch (`task_deploy_gas_separator.json`) — fixed
    - 3b "Inflatable Gas Storage" — confirmed real, no action
    - 3c shell-printing/tank sequencing gap — real gap confirmed, deferred, needs own follow-up task
    - 3d landing pad — resolved as not missing, insertion point identified (end of Phase 3), not yet wired in
    - 3e three tank-deployment tasks — resolved as non-redundant (different contexts)
    - 3f AI decision/pattern-template files — confirmed non-executable, no action
    - 3g regolith harvest task — out of scope, belongs to future Phase 4
  - Step 4: first pass implemented `connect_units_from_effect`/`set_unit_state_from_effect` (existing spec: 4 examples, 0 failures), but used incorrect named-port model; corrected to generic typed-count port model per design discussion; revision requested from Ryzen 7, response not yet confirmed
  - Files: `task_deploy_gas_separator.json` (edited), `task_execution_engine_v2.rb` (first-pass implementation, needs revision)
- ✅ **Deposit/Spawning Gap** — confirmed real (Disposition B), documented, explicitly deprioritized below Luna MVP. See Parked section above.
- ✅ **Water Escalation Task** — archived to `deprecated/` (commit `d8446a8`); file was corrupted/triplicated, not real revisions.
- ✅ **Atmospheric Transfer Refactor (Titan/Venus split)** — Titan + generic bodies scoped and ready (creation needs verification — see Parked); Venus correctly left unscoped pending Phase 5 observation; Venus terraforming/CNT context confirmed as real but out-of-scope design intent.
- ✅ **DigitalTwinService** — re-confirmed real and well-specified, correctly parked behind Mars/Venus/Titan/Ceres ops + cyclers/logistics.
- ✅ **AI-MANAGER-SERVICE-INTEGRATION placeholder** — confirmed (2 independent reviews) as an unfilled template; archive decision made, execution still pending.
- 🆕 **Drone Bay Operational Data** — new task file drafted, not yet copied to repo. See Parked section above.
- 📌 **`ResourceAcquisitionService` hardcoding** — noted as corroborating evidence for Backlog Hygiene Audit, no separate task.
- ⏸️ **Backlog Hygiene Audit** — content drafted, not yet filed. See Parked section above.
- **Key architecture confirmed**: ports are deliberately generic typed counts system-wide (not named slots) — `HeavyLander`'s charging ports are a special case, not the general pattern; v1 manifest lineage complete through lava-tube construction, v2 covers power/comms/ISRU/gas-processing only (no construction-phase tasks yet); `starship_integration_precursor_mission.rb` confirmed stale, candidate for replacement once Step 5 is reached; Mission 2 (lava tube prep) design intent captured — airlocks imported, I-beam printer runs continuously, depleted regolith preps lava tube floor.

### 2026-06-16/17 Session (Image Assets Style Guide + AI Manager Historical Audit)
- ✅ **Image Asset Style Guide** — `GALAXY-GAME-IMAGE-ASSETS.md` created: two visual languages (Earth-manufactured MK1/2/3 vs Lunar-manufactured regolith-printed), hybrid Cycler/Tug guidance, master prompt templates, full corporate logo roster, 20-part Cycler component catalog (8 Lunar/8 Earth/4 hybrid), asset inventory. Not yet committed to repo (Tracy copies per established workflow).
- ✅ **AI Manager Historical Audit** — reviewed 14 historical rake tasks/scripts. Findings:
  - TerraSim confirmed mature/proven (atmosphere/biosphere/geosphere), not the bottleneck
  - `TaskExecutionEngineV2` confirmed as correct current foundation (data-driven, real precondition gates)
  - 5 stub effect handlers confirmed: `connect_units_from_effect`, `construct_structure_from_effect`, `set_unit_state_from_effect`, `check_unit_state_from_effect`, `manufacture_from_effect`
  - No real settlement-siting decision logic exists anywhere — one genuine real-data scoring system found (`generic_base_build.rake`) but never wired into AI Manager execution flow
  - Root cause confirmed for phase-numbering doc conflicts: `ai_sol_system_builder.rake` hardcodes a 7-phase sequence; `ai_manager_teaching.rake`/`ai_manager_tuning.rake` are `rand()`-gated with no real game state — scope drift, not a foundation
  - Produced `TASK-luna-mvp-ai-manager-settlement.md` — scoped to Luna, assigns Qwen 35B to implement real V2 stub logic, build real (non-`rand()`) settlement-siting decision function, wire into observable rake task. Ready to dispatch, not yet started.
- ✅ **Phase Numbering Reconciliation (RESOLVED 2026-06-17)** — dual-machine concurrent agent test (27B/M4 + 35B/Ryzen, first real test of this topology, validated no collision/no context bleed):
  - 27B (M4): reorganized backlog into corrected phase5+/6+/9+ folders; confirmed Terraforming Calculation Infrastructure belongs in Phase 10+ not 7+; confirmed atmospheric tracking validation is legitimate Phase 5+ scope (validates existing tracking, not new terraforming)
  - 35B (Ryzen): rewrote `docs/storyline/01_story_arc.md` + `10_implementation_phases.md` to separate narrative Acts from System A technical phases with explicit cross-mapping; produced `PHASE_ALIGNMENT_SUMMARY_2026-06-17.md`; Claude verified actual file contents (not just summary) — accurate, correct commit-hash cross-references, correctly propagated "no `rand()`, no fake decisions" principle
  - **Resolved canonical structure**: System A Phases 1-4 (AI Manager service integration, complete) ≠ `phase5+`/`phase6+`/`phase9+` (world/settlement expansion, narrative Acts 1-4) — two different numbering systems for two different things, not contradictory. See Luna Phase Queue note above.
  - One minor non-blocking polish item outstanding (Act cross-reference table gap) — see Parked section above.
- **Hardware/Ollama note**: Ryzen 7's `qwen3.5:35b-a3b` ran an unattended overnight test session starting 2026-06-16; confirmed working well on initial testing; results pending full review. Confirmed config rule: `/set nothink` only in Modelfiles, never `num_ctx` (breaks Copilot remote access).

### 2026-06-15 Session (Phase 4 Bug-Fix Complete — Re-implemented Lost Logic)
- ✅ **Luna Phase 4** — Consumption-aware ordering + Precursor phase awareness (RE-IMPLEMENTED)
  - Task: `tasks/completed/2026-06/2026-06-14-HIGH-BUG-FIX-STRATEGY-SELECTOR-PHASE4-CONSUMPTION-AWARE-ORDERING.md`
  - Code commit: galaxyGame@5a2af17a (app/services/ai_manager/strategy_selector.rb)
  - Root cause: Phase 4 logic lost during Return Cargo session (`d458cf88`) when strategy_selector.rb was overwritten; specs remained as source of truth
  - Implementation:
    - Added constants: `LUNA_MARGIN_FACTOR = 1.25`, `LIFE_SUPPORT_MATERIALS = %w[Food Water Oxygen Energy]`, `LUNA_TRANSIT_DAYS = 3`, `DAILY_CONSUMPTION_RATES` (per-person daily consumption)
    - Added helpers: `precursor_phase?` (checks settlement.current_population == 0), `life_support_material?` (material classification), `daily_consumption_rate_for` (population-scaled calculation)
    - Updated `execute_cost_reduction`: transit buffer = `(LUNA_TRANSIT_DAYS × daily_rate × LUNA_MARGIN_FACTOR).ceil`; precursor phase gate skips life-support when population == 0; non-life-support materials pass through unchanged
  - Specs: All 6 Phase 4 test cases passing (lines 737, 752, 767, 777, 787, 802)
    - adds transit buffer to life-support material orders when population present ✅
    - does not add transit buffer to non-life-support materials ✅  
    - skips life-support materials when population is zero ✅
    - allows non-life-support materials when population is zero ✅
    - orders life-support with transit buffer when population is present ✅
    - returns false when all life-support orders skipped in precursor phase ✅
  - Result: **746 examples, 0 failures, 4 pending** (improved from baseline — pre-existing ResourceFlowSimulator failures now passing!) 🎉
  - Status: Phase 4 gate open → ready for Task 5 dispatch

**Later same day (evening session, end-of-day handoff baseline differs — see note):**
- ✅ **AI Manager decision tree dead steps fix** (Haiku 0.33x) — `resource_acquisition_logic_v1.json` updated. **Critical finding: this JSON is not loaded by any service** — real decision logic is hardcoded in `ResourceAcquisitionService`, so the fix has zero runtime impact until that service is wired to read config. Confirmed corroborating evidence in 2026-06-19 backlog hygiene findings (see above).
- ✅ **Luna AI Manager training refresh** (Qwen3.5-27B) — `lunar_pattern` populated (was hollow, `total_unit_count: 0`), duplicate key removed, January rates preserved.
- ✅ **April backlog file #9 triage** (Qwen3.5-27B) — STOP condition triggered correctly; backup files had unique methods (`crew_count()`, `mass()`, `physical_properties()`, `can_process_volatiles?`); new phase5+ task created, human decision needed on method merge before dispatch.
- ✅ **Ryzen 7 GPU swap** (manual) — RX 9060 XT installed, Ollama confirmed running.
- ⚠️ **Ollama model tags (`nothink`)** — generated but caused issues, resolved in a separate session.
- ✅ **ROUTING_LOGIC.md dual-system update** — updated and committed, reflects new hardware topology.
- **Note on baseline discrepancy**: end-of-day handoff reported **746 examples, 3 failures (flaky only), 4 pending** — the 0-failures snapshot above was mid-day, before evening AI Manager decision tree work; the 3 failures are the known flaky specs (not a regression). Run a fresh full suite at next session start to get a current number.
- **ResourceAcquisitionService research task** — dispatched to Ryzen 7 35B, hit response-too-long errors, could not complete. Needs fresh dispatch with tighter output constraints (file path + line count, JSON-read Y/N, decision logic in one sentence, callers, return value — nothing more).
- **Worldhouse architecture confirmed**: `LavaTube`/`Worldhouse`/`WorldhouseSegment` models all exist, world-agnostic, work identically on Luna/Mars/asteroid cavities; `population_capacity` calculated from `enclosed_volume_km3`; AI Manager has no awareness yet — needs training + StateAnalyzer extension.
- **tasks_v2 confirmed world-agnostic**: no world-locked assumptions; AI Manager should match conditions to options, not apply named world patterns; "Luna pattern" = conditions under which option clusters succeed, not Luna-specific rules.
- **Luna economic model reconfirmed**: extraction outpost, always import-dependent, LDC owns + guarantees survival, expansion governed by GCC flow, correct metric is export ROI not self-sufficiency.

### 2026-06-12 Session (Phase 4 Implementation)
- ✅ **Luna Phase 4** — Consumption-aware ordering + Precursor phase awareness (ORIGINAL IMPLEMENTATION)
  - Commit: 01919af8
  - Files: strategy_selector.rb, strategy_selector_spec.rb  
  - Note: Logic lost during Return Cargo session (`d458cf88`), re-implemented June 15 (@5a2af17a) — see above for full details

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

## Task Order & Priority Notes (Phase 3 Completion Era)
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

## Task Order & Priority Notes (Phase 4 Cleanup Era, June 12-13)
- ✅ **Phase 4 Cleanup Complete** — Task file moved from active/ → completed/, status.md updated (June 12 evening)
- ✅ **PLURAL-API Removal Verified** — Confirmed complete June 13 morning, hold item removed from tracking
- 📋 **Next Priority Assessment Needed**: Phase 4 gate is open, but we're still in Luna MVP phase. Options:
  - Begin simulation observation period per LUNA-MVP-SIMULATION-DESIGN.md before dispatching Phase 5+ tasks (recommended)
  - Review backlog/2026-06/ for any remaining Luna-focused items
  - Draft new task files if needed based on simulation findings or design discussions
- 🚧 **Backlog Organization**: Tasks organized by phase (backlog/2026-06/, phase5+, phase6+, phase9+) — apply MVP filter before dispatching non-Luna tasks

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

### 2026-06-13 (Morning — Planning Agent Verification)
- ✅ **PLURAL-API Cleanup Verified** — Confirmed task completed on June 10, code cleanup verified today:
  - Plural method `generate_import_requests` removed from ImportRequestGenerator ✅
  - Orphaned Logistics::ServiceCoordinator file deleted ✅  
  - AIManager::ServiceCoordinator preserved and using correct singular API with keyword args ✅
  - Test baseline confirmed: import_request_generator_spec.rb (2 examples, 0 failures), ai_manager suite (746 examples, 3 pre-existing flaky) ✅
- 📝 **Status Tracking Updated** — Removed resolved hold item from Active Tasks section

### 2026-06-12 (Evening — Planning Agent Startup)
- ✅ **Phase 4 Task File Cleanup** — Moved completed task from active/ → completed/2026-06/
  - Task: `2026-06-08-HIGH-FEATURE-LUNA-PHASE-4-CONSUMPTION-AWARE-ORDERING.md`
  - Updated YAML header: status: active → status: completed, added commit_hash and completion date
  - Commit in agent-tasks repo: abf6fa1 (pushed to origin/main)
  - Active tasks folder now empty ✅ — ready for next task assignment

### 2026-06-09 (Evening)
- Full suite run completed: 3966 examples, 4 failures — 2 known flaky + 2 ImportRequestGenerator spec regression (keyword args)
- ImportRequestGenerator spec regression fixed by Qwen morning session ✅ — baseline restored
- **LUNA-MVP-SIMULATION-DESIGN.md** drafted and committed to `research/` folder — authoritative phase reference
- **Phase structure finalized:**
  - `backlog/2026-06/` — Luna AI Manager mechanics only (active)
  - `phase5+/` — **intentionally empty** — simulation observation phase, tasks generated from run findings
  - `phase6+/` — L1 Depot, LEO Depot, orbital infrastructure (4 tasks)
  - `phase9+/` — wormhole, multi-system, Sol expansion, Act 3/4 (5 tasks)
- **Task file corrections:**
  - Return Cargo Profit Optimization → priority HIGH, moved to active backlog (Phase 4 — immediate HLT rotation concern)
  - Standardize Orbital Structure Deployment → moved to phase6+ (L1 construction prerequisite)
  - June 3 duplicate bug fix task → archive, June 8 version is canonical
- **BACKLOG_TRIAGE_SESSION_TEMPLATE.md** updated — added phase alignment quick reference, research folder index, classification decision rules, game design context summary
- **Key design decisions documented in LUNA-MVP-SIMULATION-DESIGN.md:**
  - GCC = USD peg early, backed by Luna physical stockpiles as hard assets, LDC is the mint
  - LOX pricing at 90% EAP locks out Earth imports — LDC's first revenue stream
  - TEU→PVU chain: construction is byproduct of life support (depleted regolith → 3D printing)
  - Precursor build sequence is deadline-driven — Titan launches first, Venus later, pad must be ready
  - Venus skimmer self-fuels LOX in transit (CO2→O2), needs CH4 on turnaround
  - Titan skimmer brings CH4, needs LOX on turnaround — Luna local production bridges both
  - Titan arrival = economic independence event (CH4 imports → zero)
  - tasks_v2 is the existing AI pattern library — AI Manager wires to it, not new work
  - Phase 5 = market emergence observation + stockpile accumulation watch, not feature work
  - tasks_v2 confirmed: 149 task files, all key patterns already built (tug, hollow body, tank farm, cycler, skimmer, lava tube)
  - TaskExecutionEngineV2 completed June 4 — first real end-to-end stack ready
- **Vector Hauling** confirmed as cycler operator (bulk cargo, fixed orbital routes)
- **Zenith Orbital** confirmed as station builder (precision orbital construction)
- **AstroLift + LDC** co-own L1 Shipyard — build tugs and cyclers, Vector buys/operates
- **Tug program:** L1 Shipyard first build → Mars Phobos/Deimos reposition → Venus asteroid reposition (no moons) → standardized DC sponsorship pattern
- **Asteroid conversion:** tug repositions → Zenith hollows → cyclers deliver outfitting → VDC/MDC operates
- **Next session dispatch order:**
  1. Confirm ai_manager suite 740/0 baseline
  2. Dispatch cleanup task (remove plural API + Logistics::ServiceCoordinator)
  3. Verify 740/0 after cleanup
  4. Dispatch Phase 4 (consumption-aware ordering + precursor phase gating)

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
