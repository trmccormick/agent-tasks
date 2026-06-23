---
title: Phase Structure and Roadmap
date: 2026-06-20
status: active
intent: Code pathway to Mars — precursor mission through mature cycler network
---

# Galaxy Game Development Phases

## Phase 5+ — Luna Simulation Testing Prerequisites (NO NEW FEATURES)
**Goal**: Build ONLY the prerequisites needed to run Luna settlement simulation testing. AI Manager + ISRU must work correctly before simulation can validate anything.

**CRITICAL PRINCIPLE** (from DEVELOPMENT_ROADMAP.md):
> "Only create tasks that are prerequisites needed BEFORE the Luna simulation can run. Do NOT add new features to phase5+."

**Scope**: Luna simulation bootstrap infrastructure ONLY
**task_v2 count**: 3 phase JSON files (power_comms, isru_deployment, gas_processing phases)
**Markdown tasks**: ~39 in `backlog/phase5+/` (after 2026-06-22 cleanup; target: ~25-30)

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

## Phase 9+ — Tug to Luna Testing (Structural Relocation)
**Goal**: Test tug capability with Phobos/Demos hollowing and repositioning.

**Scope**: Tug operations, moon relocation, structural testing
**task_v2 count**: None yet
**Markdown tasks**: ~11 in `backlog/phase9+/`

**Key Deliverables**:
- Tug deployment and testing against Luna
- Phobos/Demois hollowing infrastructure
- Moon repositioning mechanics
- Wormhole integration (Easter egg)
- Multi-system resource coordination

**Note**: Currently has wormhole/advanced topics — needs cleanup for tug focus.

---

## Phase 12+  — Advanced Systems
**Goal**: Far future — wormhole mechanics, advanced AI, dimensional rifts.

**task_v2 count**: None
**Markdown tasks**: 1 in `backlog/phase12+/`

---

## Current Backlog State (as of 2026-06-22)

| Phase | Count | Status | Intent |
|-------|-------|--------|--------|
| phase5+ | 39 | **ACTIVE** (cleanup in progress) — Luna simulation testing prerequisites ONLY | Reduce to ~25-30 by completing cleanup |
| reorganization_attempt_3 | 7 | REVIEW NEEDED | Misplaced tasks (design, UI, audit) awaiting reclassification to correct phases |
| phase6+ | 7 | **NEXT** — Lava-tube base construction (7 task_v2 JSON + consolidation) | Structural enclosure, pressurization, failure analysis |
| phase7+ | 16 | PLANNED — Depot building | Resource deposits, LEO infrastructure |
| phase8+ | 10 | PLANNED — Shipyards & cyclers | Large craft construction |
| phase9+ | 11 | PLANNED (needs cleanup) — Tug testing | Phobos/Deimos hollowing, wormhole integration |
| phase12+ | 1 | FUTURE — Advanced systems | Monitor fix (should be phase5+) |
| design/ | TBD | EXTERNAL — Asset generation and style guides | Not part of MVP task flow |
| backlog_april_2026 | 218 | AUDIT IN PROGRESS | Old-format files being verified or archived |

---

## Migration Path to Mars

```
Phase 5 → Phase 6 → Phase 7 → Phase 8 → Phase 9 → ...
Precursor  Lava-Tube Depots   Shipyard  Tug→Luna  Interplanetary
(no humans) (sealed base)      (orbital) (relocation)
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
1. Moved 7 misplaced tasks to `reorganization_attempt_3/` holding area
2. Updated PHASE_STRUCTURE.md with explicit IN SCOPE / OUT OF SCOPE lists
3. phase5+ now clearer on intent: simulation testing prerequisites ONLY
4. Each misplaced task can now be reviewed and routed to correct phase/folder

**Action for Next Session**:
- Review each task in `reorganization_attempt_3/` 
- Determine correct destination (phase6+, design/, post-MVP backlog, etc.)
- Add header to each task explaining why it was moved and where it should go
- Move tasks to correct locations
- Delete `reorganization_attempt_3/` when complete

1. **task_v2 JSON files** in `data/json-data/missions/tasks_v2/` are the actual operational tasks executed by TaskExecutionEngineV2.
2. **Markdown files** in `backlog/phase*/` are planning/scoping tasks — they describe what needs to happen.
3. **Economic progression** in `PRICE_DISCOVERY_LIFECYCLE.md` maps to these phases.
4. **backlog_april_2026** contains 218 old-format files — being audited and rewritten to current TASK_TEMPLATE format or archived as obsolete.

