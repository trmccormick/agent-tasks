---
title: Phase Structure and Roadmap
date: 2026-06-23
status: active
intent: Code pathway to Mars and beyond — precursor mission through mature cycler network, validating AI Manager autonomous expansion before players arrive
---

# Galaxy Game Development Phases

## Core Intent (Read This First)

**Phases 5 through 15 are AI Manager training and validation — not played-through content.**
Players don't enter the game until the Snap event (post-Phase 15), once at least three
systems are accessible. Everything in Phases 5–15 is the AI Manager learning to expand
autonomously, with the resulting world-state becoming the "living universe" backstory
players inherit when they arrive. Testing and tuning the AI Manager's decision-making is
the actual goal of this work — the backstory is what falls out of having run that test
successfully, not the other way around.

Every phase in this roadmap answers the same underlying question for a new world or
foothold: **given the actual data available at that location (existing moons, capturable
asteroids, surface accessibility, distance from established trade infrastructure), what's
the AI Manager's lowest-real-cost path to a functioning import/export trade route?**

Luna (Phase 5+) is the first answer. Mars (Phase 9+), Venus (Phase 10+), and every
subsequent foothold are the same question re-asked under different local conditions. New
world, new trade route, same underlying decision logic — evaluated by real cost, not a
fixed sequence.

### The Station-Sourcing Cost Hierarchy

Every new settlement eventually needs an orbital station — it's the world's import/export
gateway. Without one, the settlement has no off-world trade leg, which blocks ISRU surplus
sales, resupply, and most `GuaranteedMarketSale` activity. Earth is the one exception: as
the origin point, it has nothing further upstream to import from, so it doesn't need a
station to bootstrap itself.

Where does the material for a new station actually come from? The AI Manager has a ranked
set of options, typically in this order of real cost (cheapest first) — but this is a
**strong default driven by logistics, not a hardcoded rule**. The AI Manager evaluates
whichever options actually exist at a given world and picks by real cost comparison:

1. **Existing large moon** (Luna pattern) — settle the moon, harvest locally, build the
   station from harvested material. Best case: material is already there.
2. **Existing small moons** (Mars: Phobos/Deimos) — reposition into useful orbit, hollow
   out, build station from the hollowed material. Still local material, just needs
   repositioning work first.
3. **Asteroid capture** (Venus, which has no moons) — same hollow-out-into-station pattern
   as Mars, but harder: the asteroid has to be repositioned from elsewhere rather than
   already being nearby.
4. **Import components** (Earth/L1) — last resort, used only when no local-sourcing option
   is viable or cost-competitive.

**Note on README.md's "Super-Mars" section**: that section is a thought experiment —
working through what the AI Manager *would* do for a generated planet with no moons at
all — illustrating this same cost hierarchy under a specific hypothetical. It is not a
separate or competing rule; it's a worked example of the logic above.

---

## Phase 5+ — Luna Simulation Testing Prerequisites (NO NEW FEATURES)
**Goal**: Build ONLY the prerequisites needed to run Luna settlement simulation testing. AI Manager + ISRU must work correctly before simulation can validate anything.

**CRITICAL PRINCIPLE** (from DEVELOPMENT_ROADMAP.md):
> "Only create tasks that are prerequisites needed BEFORE the Luna simulation can run. Do NOT add new features to phase5+."

**Scope**: Luna simulation bootstrap infrastructure ONLY
**task_v2 count**: 3 phase JSON files (power_comms, isru_deployment, gas_processing phases)
**Markdown tasks**: ~39 in `backlog/phase5+/` (after 2026-06-22 cleanup; target: ~25-30)

> ⚠️ **Naming note**: the folder on disk is currently `backlog/phase5/` (no plus), while
> every other phase folder (6+ through 15+) uses a trailing plus. This is a known
> inconsistency — resolve by renaming the folder to match, or confirm and update this doc
> if there's a reason phase5 is meant to be the exception.

**In Scope** (Direct Luna Simulation Testing Prerequisites):
- ✅ AI Manager decision logic (site selection, resource allocation, escalation)
- ✅ ISRU Track A validation (TEU/PVE production tests)
- ✅ Luna fuel loop proof (LOX + water production working)
- ✅ Settlement deployment logic (manifest-driven, no-magic sourcing)
- ✅ Data-driven mission profiles (manifest, task definitions, phases)
- ✅ Atmospheric/environmental state tracking (validation layer)
- ✅ Monitor/Canvas rendering bugfix (foundational UI for observation)
- ✅ GGMap visualization layers (player-entry UI infrastructure)

**OUT OF SCOPE** (Moved to Reorganization Attempt 3 for reclassification):
- ❌ Asset generation (design, not code)
- ❌ Component catalogs (phase6+ reference material)
- ❌ Drone bay design (phase6+ infrastructure)
- ❌ Landing page redesign (user-facing cosmetic)
- ❌ Documentation cleanup (maintenance, not blocking)
- ❌ Terrain quality investigation (post-MVP polish)

**Gate**: Luna Precursor V2 Step 5 rake task executes with 7/8 tasks erroring on data availability (not implementation bugs), and all inventory-related errors are correct behavior per No-Magic Protocol. Simulation loop proves viable before Phase 6+ expansion begins.

---

## Phase 6+ — Luna Lava-Tube Base Construction (Expansion)
**Goal**: Build out the structural enclosure for Luna settlement in lava tube.

**Scope**: Worldhouse + mega-structure construction pipeline + simulation layer
**task_v2 count**: 7 JSON files (worldhouse segment fabrication, transport, installation, bracing, panels, sealing, lava tube prep)
**Markdown tasks**: ~7 in `backlog/phase6+/`

**Key Deliverables**:
- Worldhouse segment fabrication and transport
- Worldhouse installation and bracing
- Panel deployment and sealing
- Lava tube habitat preparation
- Pressurization TTR metric and failure cascade modeling
- Mission Planner no-magic sourcing for construction materials

**Architecture**:
- Structural enclosure can be complete without pressurization (sealing optional)
- Models exist: `Worldhouse`, `WorldhouseSegment`, `Coverable`, `Enclosable`, `MegaProject`
- Future `WorldhouseSimulationService` (analogous to `TerraSim`) for failure analysis

**Gate**: Luna base structural construction complete. Simulation layer ready for Phase 7 expansion.

---

## Phase 7+ — Depot Building (LEO Depot + Resource Deposits)
**Goal**: Build out orbital depot infrastructure and establish resource deposit spawning.

**Scope**: Resource deposits, depot construction, LEO depot operational
**task_v2 count**: None yet
**Markdown tasks**: ~16 in `backlog/phase7+/`

**Key Deliverables**:
- Resource deposit model, plausibility engine, spawning triggers
- Deposit-gated equipment availability
- LEO depot staging infrastructure
- Transport cost reduction from depot operations
- Orbital structure deployment standardization

**Economics Impact**:
- Transport cost to Luna: drops ~30% (AstroLift LEO refueling)
- AstroLift `base_fee_per_kg` update: 150 → ~100 GCC/kg

---

## Phase 8+ — Shipyard Construction & First Large Craft
**Goal**: Build shipyard infrastructure and construct first generation large spacecraft (tugs, cyclers).

**Scope**: Orbital construction logistics, shipyard deployment, cycler design
**task_v2 count**: None yet
**Markdown tasks**: ~10 in `backlog/phase8+/`

**Key Deliverables**:
- L1 shipyard construction from Luna materials
- Orbital construction logistics service
- Heavy Lift Transport design and manufacturing
- Cycler route establishment (Earth-Mars)
- Orbital gas processing pipeline
- Orbital structure position simulation

**Economics Impact**:
- No Earth launch costs for ships (shipyard locally produces)
- Transport cost to Luna: drops ~50% from Phase 0 baseline
- AstroLift `base_fee_per_kg` update: ~100 → ~60 GCC/kg
- Mars bulk cargo costs drop ~60%

---

## Phase 9+ — Mars Footholds (Phobos/Deimos Repositioning & Stationization)
**Goal**: Apply the station-sourcing cost hierarchy to Mars. Reposition Phobos/Deimos-sized
moons into useful orbit, hollow them out, build the first Mars station, and establish the
first cycler return/resupply loop.

> 🔄 **Correction (2026-06-23)**: this phase was previously framed as "Tug to Luna
> Testing" with wormhole/Easter-egg content mixed in. That framing is now superseded —
> wormhole topology belongs to Phase 14+/15+ (AI Manager operational independence and
> unplanned Eden expansion), not here. This entry now matches the storyline docs
> (`10_implementation_phases.md`, `PHASE_ALIGNMENT_SUMMARY_2026-06-18.md`), which both
> define Phase 9+ as the Mars-foothold step in the Sol-system training ladder.

**Scope**: Tug deployment and testing, Phobos/Deimos hollowing, Mars station construction,
first cycler return/resupply loop
**task_v2 count**: None yet
**Markdown tasks**: ~11 in `backlog/phase9+/` (needs cleanup — see correction note above)

**Key Deliverables**:
- Tug deployment and testing (validated against Luna operations first)
- Phobos/Deimos hollowing infrastructure
- Moon repositioning mechanics
- First Mars station construction from hollowed material
- First cycler return/resupply loop established

**Gate**: Mars station operational, first cycler resupply loop validated. Ready for Phase 10+ (Venus repeat).

---

## Phase 10+ — Venus Footholds (Asteroid Capture & Stationization)
**Goal**: Repeat the Mars foothold pattern at Venus, where there are no moons to reposition
— apply the next tier of the station-sourcing hierarchy (asteroid capture) instead.

**Scope**: Asteroid capture and repositioning into Venus orbit, hollowing, station
construction, second cycler-tug pair at Earth/L1
**task_v2 count**: None yet
**Markdown tasks**: TBD — folder exists, content not yet populated/audited

**Key Deliverables**:
- Asteroid identification and capture into Venus orbit (harder than Mars: no nearby moon,
  asteroid must be repositioned from elsewhere)
- Hollowing and station construction (same pattern as Mars, applied to captured asteroid)
- Parallel second cycler-tug pair construction at Earth/L1

**Gate**: Venus station operational. Two independent inner-system footholds (Mars, Venus)
now validated, ready for Phase 11+ logistics maturation.

---

## Phase 11+ — Earth-Mars-Venus Cycler Logistics Maturation
**Goal**: Normalize cycler logistics across the now-established Mars and Venus footholds —
prove the logistics loop works reliably before any branch expansion or terraforming begins.

**Scope**: Docking/undocking validation, cargo transfer validation, heavy logistics testing
across the Earth-Mars-Venus network
**task_v2 count**: None yet
**Markdown tasks**: TBD — folder exists, content not yet populated/audited

**Key Deliverables**:
- Docking/undocking reliability across all three footholds
- Cargo transfer validation under realistic load
- Heavy logistics stress testing (the AI Manager managing multiple simultaneous transit
  legs, not just single point-to-point runs)

**Gate**: Earth-Mars-Venus cycler logistics loop stable and repeatable. Core inner-system
trade network proven before optional branch expansion (Phase 12+) begins.

---

## Phase 12+ — Optional Branch Expansion (Ceres / Titan-Saturn)
**Goal**: Optional parallel branch theaters splitting off the now-stable core loop —
Mars→Ceres belt mining and Mars→Titan/Saturn settlement support.

**Scope**: Belt mining infrastructure (Ceres), gas giant moon settlement support
(Titan/Saturn) — both as optional, parallel expansions rather than required sequence steps
**task_v2 count**: None
**Markdown tasks**: 1 in `backlog/phase12+/` (previously miscategorized as "Advanced
Systems — wormhole mechanics, dimensional rifts" — that framing is superseded; this is
optional Sol-system branch expansion, not wormhole-era content)

**Key Deliverables**:
- Ceres belt mining infrastructure (if pursued)
- Titan/Saturn settlement support (if pursued)
- Both branches are optional and parallel — neither blocks progression to Phase 13+

**Gate**: N/A (optional) — branches can be pursued independently or skipped; does not gate
Phase 13+ progression.

---

## Phase 13+ — Psyche Mining & Mars Terraforming Initiation
**Goal**: Begin Psyche asteroid mining (planetary core remnant — a distinct resource
profile from belt/moon mining) and initiate Mars terraforming, supported by Venus/Titan gas
exports.

**Scope**: Psyche core-remnant mining infrastructure, Mars terraforming initiation,
Venus/Titan gas export pipelines feeding the terraforming effort
**task_v2 count**: None yet
**Markdown tasks**: TBD — folder exists, content not yet populated/audited

**Key Deliverables**:
- Psyche mining infrastructure (core-remnant-specific extraction, distinct from regolith/
  belt mining patterns used elsewhere)
- Mars terraforming initiation systems
- Venus/Titan gas export pipelines supporting terraforming

**Gate**: Terraforming initiation systems operational, gas export pipelines validated.
This is the last "infrastructure-building" phase before the AI Manager's operational
independence test in Phase 14+.

---

## Phase 14+ — AI Manager Operational Independence Test
**Goal**: The actual test this entire ladder has been building toward. The AI Manager takes
full day-to-day operational control of the Sol system and, unsupervised, discovers and
begins Eden system expansion.

**Scope**: Sol system autonomous management handoff, Eden discovery and initial expansion
**task_v2 count**: None yet
**Markdown tasks**: TBD — folder exists, content not yet populated/audited

**Key Deliverables**:
- AI Manager assumes full Sol operational control (no human/scripted intervention in
  day-to-day decisions)
- Eden system discovered and initial expansion begun, using the same pattern-based,
  cost-weighted decision logic validated across Phases 5–13

**Gate**: AI Manager demonstrates sustained independent Sol management. Eden expansion
underway — proceeds to Phase 15+ where this independence gets stress-tested.

---

## Phase 15+ — Unplanned Eden Expansion & Wormhole Mass-Limit Discovery
**Goal**: This is where the test reveals its result. The AI Manager's confidence from
successful Sol patterns leads it to overbuild Eden infrastructure, pushing past the natural
wormhole's mass-limit stability — discovered through operational stress, not by design.

**Scope**: Eden infrastructure buildup (unplanned/uncapped), wormhole stability monitoring,
mass-limit threshold discovery
**task_v2 count**: None yet
**Markdown tasks**: 1 in `backlog/phase15+/`

**Key Deliverables**:
- Eden infrastructure buildup proceeding under full AI Manager autonomy (no artificial cap)
- Wormhole stability/mass-limit modeling
- Detection of the threshold breach that triggers the Snap

**Gate**: Wormhole reaches instability and shifts exit point, orphaning Eden — **this is the
Snap crisis event**. Post-Phase 15 is where Act 2 player-facing gameplay actually begins.
Everything in Phases 5–15 has been building the world-state players inherit at this moment.

---

## Current Backlog State (as of 2026-06-23)

| Phase | Count | Status | Intent |
|-------|-------|--------|--------|
| phase5+ | 39 | **ACTIVE** (cleanup in progress) — Luna simulation testing prerequisites ONLY | Reduce to ~25-30 by completing cleanup |
| reorganization_attempt_3 | ~22 remaining (per 2026-06-22 handoff — confirm against live folder) | REVIEW NEEDED | Misplaced tasks awaiting reclassification to correct phases |
| phase6+ | 7 | **NEXT** — Lava-tube base construction (7 task_v2 JSON + consolidation) | Structural enclosure, pressurization, failure analysis |
| phase7+ | 16 | PLANNED — Depot building | Resource deposits, LEO infrastructure |
| phase8+ | 10 | PLANNED — Shipyards & cyclers | Large craft construction |
| phase9+ | 11 | PLANNED (needs cleanup) — Mars footholds | Phobos/Deimos hollowing, station, first cycler loop |
| phase10+ | TBD | PLANNED — Venus footholds | Asteroid capture, station, second cycler-tug pair |
| phase11+ | TBD | PLANNED — Cycler logistics maturation | Docking/cargo/heavy logistics validation |
| phase12+ | 1 | PLANNED (optional) — Ceres/Titan-Saturn branch expansion | Previously mislabeled "Advanced Systems"; corrected |
| phase13+ | TBD | PLANNED — Psyche mining & Mars terraforming | Core-remnant mining, terraforming initiation |
| phase14+ | TBD | PLANNED — AI Manager independence test | Sol autonomous handoff, Eden discovery |
| phase15+ | 1 | PLANNED — Unplanned Eden expansion / Snap trigger | Mass-limit discovery, Snap crisis event |
| design/ | TBD | EXTERNAL — Asset generation and style guides | Not part of MVP task flow |
| backlog_april_2026 | 218 | AUDIT IN PROGRESS | Old-format files being verified or archived |
| `reorganization attempt 2` | TBD | **UNRESOLVED** — found on disk 2026-06-23, predates attempt 3, not referenced in any current doc | Confirm whether dead/archivable or has unmigrated content |

---

## Migration Path to Mars and Beyond

```
Phase 5 → Phase 6 → Phase 7 → Phase 8 → Phase 9 → Phase 10 → Phase 11 → Phase 12 → Phase 13 → Phase 14 → Phase 15 → SNAP
Precursor  Lava-Tube  Depots   Shipyard  Mars      Venus      Cycler     Ceres/     Psyche/    AI         Unplanned   (Act 2
(no humans)(sealed    (orbital) (tug/    footholds footholds  logistics  Titan-     terra-     independence Eden       begins)
            base)               cycler)  (moons)   (asteroid) maturation Saturn     forming    test         overbuild
                                                                          (optional)
```

---

## What Went Wrong in Reorganization Attempts 1-3

**Problem**: Earlier reorganization attempts accumulated tasks from many different perspectives without enforcing the core phase5+ constraint: **prerequisites for Luna simulation testing ONLY**.

**Result**: 
- Asset generation tasks (not code)
- UI design tasks (cosmetic, not blocking)
- Investigation tasks (post-MVP polish)
- Reference catalogs (phase6+ when needed)
- All ended up in phase5+ because they were "Luna-related"

**Lesson for Agents**:
> ⚠️ **BEFORE adding a task to phase5+, ask: "Does Luna settlement simulation testing require this task to be complete FIRST?"**
> 
> If the answer is NO → It does not belong in phase5+. It belongs in another phase, design/, or post-MVP backlog.

**How Reorganization Attempt 3 Fixes This**:
1. Moved misplaced tasks to `reorganization_attempt_3/` holding area
2. Updated PHASE_STRUCTURE.md with explicit IN SCOPE / OUT OF SCOPE lists
3. phase5+ now clearer on intent: simulation testing prerequisites ONLY
4. Each misplaced task can now be reviewed and routed to correct phase/folder

**Open item carried forward**: a separate `reorganization attempt 2` folder (note: space,
not underscore, in the name) was found on disk 2026-06-23, dated mid-May — predates attempt
3 and isn't referenced in any current document. Status unconfirmed. Treat as an open
question, not yet resolved: someone needs to check whether it's safely archivable or has
content that never made it into attempt 3's process.

**Action for Next Session**:
- Review each remaining task in `reorganization_attempt_3/` one at a time (do not batch)
- Determine correct destination (phase6+ through phase15+, design/, post-MVP backlog, etc.)
- Add header to each task explaining why it was moved and where it should go
- Move tasks to correct locations
- Resolve the `reorganization attempt 2` question above
- Delete `reorganization_attempt_3/` (and attempt 2, if confirmed dead) when complete

---

## Reference Notes

1. **task_v2 JSON files** in `data/json-data/missions/tasks_v2/` are the actual operational tasks executed by TaskExecutionEngineV2.
2. **Markdown files** in `backlog/phase*/` are planning/scoping tasks — they describe what needs to happen.
3. **Economic progression** in `PRICE_DISCOVERY_LIFECYCLE.md` maps to these phases.
4. **backlog_april_2026** contains 218 old-format files — being audited and rewritten to current TASK_TEMPLATE format or archived as obsolete.
5. **Narrative cross-reference**: `docs/storyline/01_story_arc.md` and `10_implementation_phases.md` define the narrative-vs-technical phase mapping this document's Phase 9–15 entries were reconciled against (2026-06-23). If those storyline docs change, this file's Phase 9–15 entries should be re-checked for drift.
