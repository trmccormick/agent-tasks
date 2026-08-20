---
title: Phase Structure and Roadmap — Updated 2026-06-27
date: 2026-06-27
status: active
intent: NPC-only Sol system expansion and early terraforming — AI Manager training through autonomous pattern-based settlement using JSON mission data
---

# Galaxy Game Development Phases — Updated Framework

## Core Intent (Read This First)

**Act 1 = Phases 1–13: NPC-only Sol system expansion and early terraforming.**
This is **not player-facing content**. Players do not enter the game during Act 1. Instead, they inherit a living universe that has been built by an AI Manager learning to expand autonomously using pattern-based decision-making trained on JSON mission data.

**Act 2 = Phase 14+: Eden system expansion test.**
The AI Manager takes full operational control of Sol and discovers/begins expanding into the Eden system using learned patterns. This is the first test of whether the AI can successfully apply Sol-system training to a new environment.

**Act 3 = Phase 15+: Snap crisis event.**
Unplanned Eden expansion pushes past natural wormhole mass-limit stability, triggering the Snap crisis. Post-Phase 15 is where player-facing gameplay (Act 2) begins.

**Act 4 = Not yet planned.**
Post-Snap narrative content (wormhole mastery, Hammer Protocol, etc.) — deferred until Act 3 framework is established.

### Key Principle: NPC-Only Expansion During Act 1

During Phases 1–13, the AI Manager expands across the Sol system **autonomously**, using:
- Pattern-based decision-making trained on JSON mission data
- Real cost evaluation for each settlement opportunity
- No hardcoded sequences — the AI evaluates options and picks by real cost
- Multiple possible settlement strategies (not limited to one prescribed path)

**This is simulation work.** We're testing whether the AI Manager can:
1. Learn deployment patterns from JSON mission definitions
2. Apply those patterns across different celestial bodies with varying conditions
3. Make autonomous expansion decisions that result in a coherent, living universe

The resulting world-state becomes the backstory players inherit when they arrive post-Snap.

---

## Act 1: Sol System Expansion (Phases 1–13) — NPC-Only AI Training

**Goal**: Validate AI Manager can autonomously expand across Sol system using pattern-based decision-making, then initiate early terraforming efforts.

**Scope**: Earth → Luna → (parallel: Mars + Venus + Optional Branches + Psyche) → Coordinated multi-world operations
**Player Experience**: None during Act 1. Players inherit the result post-Snap.

**CRITICAL ARCHITECTURE: Tug/Cycler Deployment Sequence Gates Expansion**

Infrastructure and craft deployment follow a dependency chain, NOT parallel branching:

1. **Luna base** (Phase 5)
2. **L1 + LEO depots** (Phase 6–7)
3. **Shipyards at L1** (Phase 8) — main construction hub
4. **Tug built and deployed to Mars** — uses slag propellant from moon hollowing; generates its own fuel as it repositions Phobos/Deimos into stable orbit
5. **Cycler built and deployed with Mars moon-fitting equipment** — arrives alongside tug
6. **Tug departs Mars for asteroid belt** — leaves with full slag propellant tank from hollowing operations; captures 2 Phobos/Deimos-sized asteroids
7. **Cycler begins standing Earth→Mars→Venus loop** — both Mars and Venus now equipped for settlement
8. **Phase 11+: Multi-world coordination** — logistics network now spans all three worlds

**Flow Structure**:
```
Phase 5: Luna (foundation + mission validation)
    ↓
Phase 6–7: Orbital Infrastructure (LEO/L1 depots)
    ↓
Phase 8: Shipyards at L1
    ↓
Phase 9a: Tug deployment to Mars (moon hollowing + repositioning)
    ├─ Tug generates slag propellant as it hollows moons
    ├─ Tug uses slag to reposition bodies into stable orbit
    └─ Tug departs with full slag tank for asteroid belt
    ↓
Phase 9b–e: Mars surface settlement + cycler deployment (cycler arrives with tug)
    ├─ Cycler carries moon-fitting equipment
    └─ Mars footholds established (orbital + surface + terraforming pathways)
    ↓
Phase 10: Venus asteroid capture + cloud city foundation
    ├─ Tug arrives at asteroid belt with slag propellant
    ├─ Tug captures 2 asteroids, repositions to Venus orbit
    └─ Cycler carries Venus depot/shipyard payload (same cycler pair from Mars)
    ↓
Phase 11: Multi-world logistics maturation (Earth→Mars→Venus standing cycler loop)
    ├─ Cycler docking/cargo transfer across all three worlds
    └─ AI Manager learns coordinated multi-world operations
    ↓
Phase 12: Optional branches (Ceres belt mining, Titan/Saturn settlement)
    ↓
Phase 13: Psyche mining + coordinated terraforming
```

**Key Mechanic: Slag Propellant Closed Loop**
- Tug hollows Phobos/Deimos → generates slag
- Uses generated slag to reposition bodies into stable orbit
- Leaves Mars with remaining slag tank for belt journey
- Slag propellant is self-sustaining mining byproduct, not Earth import

---

## Current Backlog State — Tug/Cycler Deployment Sequence

| Phase | Structure | Status | Deployment Role | Purpose |
|-------|-----------|--------|---|---------|
| **1-4** | Foundation | ✅ Complete | Sequential baseline | AI Manager core logic + service integration |
| **5** | `phase05-luna-calibration/` | ACTIVE | Luna foundation | Luna validation → option testing → AI training |
| **6** | `phase06-lava-tube-base/` | QUEUED | Surface settlement | Surface-first bootstrap construction |
| **7** | `phase07-depot-building/` | QUEUED | Orbital infrastructure | LEO/L1 depots enable shipyard construction |
| **8** | `phase08-shipyards/` | QUEUED | Large craft manufacturing | Shipyard builds tug, cycler at L1 |
| **9** | `phase09-mars/` | PLANNED | Tug deployment to Mars | Tug hollows moons (generates slag propellant), repositions them; Cycler arrives with equipment |
| **10** | `phase10-venus/` | PLANNED | Tug→Belt→Venus | Tug travels to asteroid belt with slag propellant, captures asteroids, repositions to Venus orbit |
| **11** | `phase11-logistics/` | PLANNED | Multi-world operations | Standing Earth→Mars→Venus cycler loop established (both worlds now equipped) |
| **12** | `phase12-optional-branches/` | PLANNED (optional) | Branch expansion | Optional: Ceres belt mining and/or Titan/Saturn settlement |
| **13** | `phase13-psyche/` | PLANNED | Advanced mining | 16 Psyche mining + coordinated terraforming |
| **14+** | TBD | FUTURE | Sequential gate | AI operational independence + Eden expansion |

**Sequential Dependency Chain**:
- Phase 8 completion → Tug + Cycler built at L1 shipyard
- Phase 9 → Tug deployed to Mars; Moon hollowing generates slag propellant; Cycler arrives with equipment
- Phase 10 → Tug departs Mars with full slag tank; travels to asteroid belt; captures and repositions asteroids to Venus
- Phase 11 → Cycler begins standing loop; both Mars and Venus now operational; multi-world logistics coordination begins
- Phases 12–13 → Optional/parallel expansion occurs while standing cycler loop sustains main world infrastructure

**Key mechanic**: Slag propellant generated during moon hollowing is self-sustaining; tug doesn't need Earth resupply for Mars→Belt→Venus journey.

**Why This Structure**:
- **Phases 5-8**: Sequential foundation (can't build Mars infrastructure before orbital infrastructure exists)
- **Phases 9-13**: Parallel execution (Mars surface doesn't wait for Venus cloud cities; they develop concurrently)
- **AI complexity**: Learning to manage 4-5 simultaneous expansion efforts teaches AI realistic coordination decisions
- **Resource prioritization**: When cyclers are scarce, AI learns to allocate them across competing world demands
- **Stress testing**: Logistics and economics tested under realistic multi-world load, not in isolation

---

### Phase 1–4: Foundation (Already Complete)
**Status**: ✅ Complete — System A AI Manager Service Integration
- CostAnalyzer, ManifestGenerator, ShortageDetector, ImportRequestGenerator
- These form the base decision logic for all subsequent phases

### Phase 5 — Luna: Mission Validation & AI Training
**Goal**: Validate Luna settlement mission profiles and train AI Manager on proven patterns.

**Scope**: Luna simulation testing prerequisites + mission profile validation
**Structure**: 
- **Phase 5a**: Luna mission profile validation (test JSON missions against codebase)
- **Phase 5b**: Luna settlement option testing (test multiple approaches, pick best by cost)
- **Phase 5c**: Luna AI training (feed validated patterns to AI Manager decision logic)

**Key Deliverables**:
- ✅ AI Manager decision logic (site selection, resource allocation, escalation)
- ✅ ISRU Track A validation (TEU/PVE production tests)
- ✅ Luna fuel loop proof (LOX + water production working)
- ✅ Settlement deployment logic (manifest-driven, no-magic sourcing)
- ✅ Data-driven mission profiles (manifest, task definitions, phases)
- ✅ Atmospheric/environmental state tracking (validation layer)
- ✅ Monitor/Canvas rendering bugfix (foundational UI for observation)
- ✅ GGMap visualization layers (player-entry UI infrastructure)

**Gate**: Luna mission profiles validated, settlement patterns tested, AI Manager trained on Luna-specific options. Luna simulation loop proves viable before Phase 6 expansion begins.

---

### Phase 6 — Luna Surface & Infrastructure: Testing & AI Training
**Goal**: Test Luna surface settlement (worldhouse construction) and orbital infrastructure options; train AI on proven patterns.

**Scope**: Luna lava-tube base construction + mission profile validation
**Structure**:
- **Phase 6a**: Luna surface mission profile validation (worldhouse construction, pressurization, habitat)
- **Phase 6b**: Luna orbital infrastructure testing (LEO options, initial depot concepts)
- **Phase 6c**: Luna infrastructure AI training (feed patterns to decision logic)

**Key Deliverables**:
- Worldhouse segment fabrication and transport mission validation
- Worldhouse installation and bracing mission validation
- Panel deployment and sealing mission validation
- Lava tube habitat preparation mission validation
- Pressurization TTR metric and failure cascade modeling
- Mission Planner no-magic sourcing for construction materials

**Gate**: Luna surface settlement and infrastructure patterns tested and validated. AI Manager trained on Luna worldhouse/infrastructure options. Ready for Phase 7 (depot expansion).

---

### Phase 7 — Orbital Infrastructure: Testing & AI Training
**Goal**: Test orbital depot infrastructure options; train AI on cost-optimized infrastructure selection.

**Scope**: LEO Depot + resource deposits mission validation
**Structure**:
- **Phase 7a**: LEO Depot mission profile validation (multiple depot configurations)
- **Phase 7b**: Resource deposit spawning and management testing
- **Phase 7c**: Infrastructure cost comparison and AI training (AI learns to evaluate depot options by real cost)

**Key Deliverables**:
- Resource deposit model validation across mission profiles
- Plausibility engine testing for deposit spawning
- Deposit-gated equipment availability testing
- LEO depot staging infrastructure options validated
- Transport cost reduction scenarios modeled
- Orbital structure deployment standardization patterns tested

**Gate**: Orbital infrastructure patterns tested, validated, and AI trained. Economics impact verified: transport costs from LEO operations drop ~30%.

**Dependency Chain Begins**: Phase 8 triggers the tug/cycler deployment sequence (Phases 9–10), which gates subsequent multi-world expansion (Phases 11–13).

---

### Phase 8 — Orbital Construction & Craft: Testing & AI Training
**Goal**: Test shipyard construction and large spacecraft options; train AI on manufacturing decisions.

**Scope**: Shipyard + large craft construction mission validation
**Structure**:
- **Phase 8a**: L1 shipyard construction mission profile validation (multiple shipyard configurations)
- **Phase 8b**: Tug and cycler construction mission profile validation (design variations, material sourcing)
- **Phase 8c**: Orbital manufacturing AI training (AI learns cost-optimized manufacturing decisions)

**Key Deliverables**:
- L1 shipyard construction options validated
- Orbital construction logistics mission profiles tested
- Heavy Lift Transport design validation (multiple variants)
- Cycler route establishment mission validation (Earth-Mars variants)
- Orbital gas processing pipeline mission validation
- Orbital structure positioning tested under various conditions

**Gate**: Shipyard and large craft construction patterns tested, validated, and AI trained. Economics impact verified: ship construction from Luna materials eliminates Earth launch costs.

---

### Phase 9 — Mars Orbital & Surface: Tug Deployment & Settlement
**Goal**: Deploy tug to Mars for moon relocation (generates slag propellant through hollowing); establish Mars footholds; prepare tug for asteroid belt departure.

**Scope**: Mars footholds including Phobos/Deimos relocation, surface settlement options, tug mission logistics
**Structure**:
- **Phase 9a**: Mars orbital setup + tug deployment mission validation (Phobos/Deimos hollowing, slag generation, moon repositioning)
- **Phase 9b**: Mars surface outposts mission validation (surface infrastructure, mining, resource processing)
- **Phase 9c**: Mars surface settlement validation (worldhouse, habitat infrastructure)
- **Phase 9d**: Mars terraforming initiation validation (atmospheric enrichment, great warming options)
- **Phase 9e**: Mars option comparison and AI training (AI learns to evaluate which Mars approaches provide best ROI)

**Key Deliverables**:
- Tug deployment to Mars and hollowing operations validated
- **Slag propellant generation during moon hollowing confirmed** (hollowing generates slag; slag is used to reposition bodies; tug departs Mars with full slag tank for asteroid belt journey)
- Phobos/Deimos repositioning mechanics tested (moon repositioning into stable orbit using slag propellant)
- Depot-first conversion sequencing validated: depot (gas processing, docking, refueling, propellant/material storage) comes online before shipyard conversion completes
- Cycler deployment with tug (both arrive simultaneously; cycler carries moon-fitting equipment)
- Mars surface outpost establishment options validated
- Mars surface resource infrastructure options tested
- Mars worldhouse construction mission validation
- Mars terraforming initiation options tested
- **Tug departs Mars with full slag propellant tank** → ready for asteroid belt (Phase 10)
- First cycler return/resupply loop established

**Critical Note**: Tug fuel economy is self-sustaining:
- Hollowing moons → generates slag byproduct
- Slag used as propellant for repositioning operations
- Tug leaves Mars fully fueled (slag tank) for 6+ month journey to asteroid belt
- No Earth resupply needed between Mars and belt operations
**BOIL-OFF ENFORCEMENT NOTE**: Phase 9 does NOT require boil-off code implementation yet. Early Mars supply comes from Earth (3-day transit, minimal loss even without cooling enforcement). Boil-off enforcement and skimmer infrastructure are deferred to Phase 11+ when Venus long-haul cycler operations begin.
**Parallel Dependencies**: 
- Requires Phase 8 complete (tug built at L1 shipyard)
- Phase 9 must complete before Phase 10 (tug must be positioned at Mars ready for belt departure)

**Gate**: Mars footholds established, tug fueled and ready for asteroid belt. Cycler begins return to Earth for Venus payload. Phase 10 triggered.

---

### Phase 10 — Venus Cloud Cities & Adaptation: Asteroid Capture & Settlement
**Goal**: Deploy tug to asteroid belt for asteroid capture; position asteroids in Venus orbit; establish Venus footholds. **Sequential gate: Phase 9 must complete first (tug must be fueled and ready).**

**Scope**: Venus footholds including asteroid capture, cloud cities, and orbital infrastructure
**Structure**:
- **Phase 10a**: Venus orbital infrastructure mission validation (depot options, different architectures)
- **Phase 10b**: Asteroid belt deployment mission validation (tug journey from Mars to belt with slag propellant, asteroid capture and relocation to Venus orbit)
- **Phase 10c**: Venus cloud city mission validation (atmospheric floating platforms, habitats, industrial operations)
- **Phase 10d**: Venus surface/atmospheric operations mission validation (cloud harvesting, terraforming pathways)
- **Phase 10e**: Venus settlement comparison and AI training (AI learns to evaluate which Venus approach provides best ROI for moon-free world)

**Key Deliverables**:
- **Tug transit from Mars to asteroid belt validated** (uses full slag propellant tank from Phase 9 Mars operations)
- **Asteroid capture and relocation mechanics tested** (tug captures 2 Phobos/Deimos-sized objects from asteroid belt, repositions both to Venus orbit using slag propellant)
- Venus orbital depot establishment validated
- Depot-first conversion sequencing (same as Mars — depot online before shipyard conversion completes)
- Cloud city habitation options tested
- Atmospheric harvesting mission profiles validated
- Industrial integration infrastructure tested
- **Cycler payload reassignment validated** (cycler that delivered Mars equipment now carries Venus-bound depot/shipyard payloads after Mars resupply cycle complete)
- Terraforming pathways identified
- Cost comparison: asteroid capture vs pure atmospheric operations vs cloud cities

**Critical Note**: Venus differs from Mars because:
- NO natural moons (must use captured asteroids from belt)
- Tug must survive long Mars→Belt→Venus journey
- Slag propellant from Mars hollowing is the only fuel source for this leg
- Multiple settlement approaches possible (cloud-city-first vs asteroid-first vs hybrid)
- AI learns these distinctions and adapts decision logic

**Sequential Dependencies**: 
- **Requires Phase 9 COMPLETE** (tug must depart Mars fueled with slag propellant)
- **Requires Phase 8** (cycler for Venus payloads built at L1)
- Tug cannot arrive at Venus orbit with captured asteroids before completing Mars operations
**BOIL-OFF ENFORCEMENT NOTE**: Phase 10 does NOT require boil-off code implementation yet. Early Venus supply comes from Earth (cycler is Earth-supplied initially). Boil-off enforcement activates in Phase 11+ when Venus skimmer operations begin and cycler transits carry volatile resources with long transit times.
**Gate**: Venus footholds established with captured asteroids in orbit, cloud city infrastructure beginning, cycler positioned for Venus supply chain. Phase 11 triggered (standing Earth→Mars→Venus cycler loop now ready to begin).

---

### Phase 11 — Multi-World Logistics & Skimmer Activation: Testing & AI Training
**Goal**: Test and validate cycler logistics, docking, cargo transfer across Mars and Venus now both operational. Activate skimmer resource harvesting at Venus (CH4/H2) and enforce boil-off loss on cycler transits. Sequential gate: Phases 9-10 must complete first (both worlds must be equipped).

**Scope**: Earth-Mars-Venus cycler logistics + Venus skimmer operations + boil-off enforcement validation
**Structure**:
- **Phase 11a**: Cycler docking mission validation (cycler-world pairs: Earth-Mars, Mars-Venus, Venus-Earth)
- **Phase 11b**: Cargo transfer mission validation (docking, undocking, cargo movement protocols across multiple worlds)
- **Phase 11c**: Venus skimmer proof-of-concept activation (mk2 cooling assumed active; harvest CH4 or H2 from Venus atmosphere -> orbital depot -> cycler integration)
- **Phase 11d**: Boil-off enforcement on cycler transits (apply loss rates to Earth->Venus->Mars->Earth legs; validate supply chain economics with mk2 cooling)
- **Phase 11e**: Standing cycler loop stress testing mission validation (Earth->Mars->Venus->Earth repeating cycle, concurrent traffic, resource flow tracking)
- **Phase 11f**: Multi-world logistics AI training (AI learns to coordinate multiple simultaneous operations, prioritize resources, manage resource arbitrage between worlds)

**Skimmer Resource Targets** (Activated Phase 11+):
- **Venus**: Harvest CH4 (methane) or H2 (hydrogen) from atmosphere via stationary depot-based skimmers -> accumulate in orbital storage -> transfer to cycler -> transport to Earth/Mars/Luna (mk2 cooling manages boil-off on cycler transits)

**Key Deliverables**:
- Standing non-stop cycler loop validated: Earth -> Mars -> Venus -> repeat
- Docking/undocking reliability across both footholds validated
- Cycler functions as mobile station: craft dock directly (not point-to-point transport only); craft can dock and ride multi-leg journeys
- **Venus skimmer operations validated** (atmospheric harvesting proof-of-concept at planetary scale)
- **Boil-off loss enforcement validated** (settlement daily tick + cycler transit loss calculations implemented and active)
- Cargo transfer validation under realistic load scenarios with concurrent traffic AND resource loss (boil-off)
- Resource arbitrage patterns identified across all three worlds (accounting for boil-off cost)
- AI learns conflict resolution when both worlds compete for cycler capacity
- First complete tri-world logistics cycle proven with realistic supply chain constraints

**Sequential Dependencies**:
- **Requires Phase 9 COMPLETE** (Mars footholds + tug positioned for belt departure)
- **Requires Phase 10 COMPLETE** (Venus footholds established with asteroids, cycler payload positioned)
- Assumes mk2 cooling technology available for Venus skimmer operations
- Cannot begin multi-world coordination until both worlds are operational and equipped

**Gate**: Multi-world cycler logistics loop proven operational under realistic concurrent load. Boil-off enforcement validated (supply chain now accounts for real atmospheric loss). Venus skimmer proof-of-concept proven. AI trained on complex coordination across two simultaneous expansion efforts with real resource constraints. Inner-system trade network operational with resource harvesting integrated.

---

### Phase 12 — Ceres Belt Mining & Titan Skimming: Testing & AI Training
**Goal**: Test advanced asteroid mining (belt location) and Titan skimmer operations (O2/LOX harvesting, mk3 cooling) running while standing cycler loop sustains core infrastructure.

**Scope**: Ceres belt mining + Titan skimmer operations + multi-world resource routing validation
**Structure**:
- **Phase 12a**: Ceres asteroid discovery + characterization profiles validation (belt selection + spectral analysis patterns)
- **Phase 12b**: Automated belt mining mission profiles validation (mining operation profiles, material sorting, resource batching)
- **Phase 12c**: Titan skimmer proof-of-concept activation (mk3 cooling assumed active; harvest O2/LOX from Titan atmosphere -> orbital depot -> cycler integration)
- **Phase 12d**: Multi-world resource routing AI training (AI learns to route belt-mined metals + Titan O2/LOX through shipyards across three worlds; manages resource arbitrage and boil-off economics)

**Skimmer Resource Targets** (Phase 12+):
- **Titan**: Harvest O2/LOX (liquid oxygen) from atmosphere via stationary depot-based skimmers -> accumulate in orbital storage -> transfer to cycler -> transport across system (mk3 cooling manages higher boil-off on extended transits)

**Key Deliverables**:
- Ceres mining infrastructure options validated
- Automated belt mining mission profiles validated
- **Titan skimmer operations validated** (O2/LOX harvesting at planetary scale; mk3 cooling required due to longer boil-off transit times)
- Belt mining mission profiles validated
- Multi-world metal + gas resource routing tested
- AI learns coordinated belt mining + Titan resource harvesting + shipyard utilization patterns
- Standing cycler loop continues sustaining core infrastructure while branch resources added
- Triple-world resource network (Luna + Mars + Venus + Belt + Titan) validated

**Critical Note**: Ceres and Titan are independently optional paths - pursuing one doesn't require the other. Ceres has early-design relationship with Mars (belt extraction/logistics coordination). Titan requires mk3 cooling technology maturity.

**Dependencies**:
- **Runs after Phase 11 complete** (standing cycler loop sustains core infrastructure; branch expansion can now be evaluated)
- Titan skimming requires mk3 cooling technology available
- Optional - AI can decide not to pursue branches if core world expansion ROI is higher
- Doesn't gate Phase 13 or other priorities

**Gate**: Optional - branches can be pursued independently or skipped based on AI economic evaluation; does not gate Phase 13 or later progression. If pursued: belt mining + Titan skimming validated; extended resource network operational.

---

### Phase 13 — Psyche Mining & Terraforming: Testing & AI Training
**Goal**: Test advanced mining (core-remnant extraction) and coordinated terraforming options running while standing cycler loop sustains core infrastructure.

**Scope**: Psyche core-remnant mining + multi-world terraforming infrastructure validation
**Structure**:
- **Phase 13a**: Psyche mining mission profiles validation (core-remnant-specific extraction patterns)
- **Phase 13b**: Multi-world terraforming mission profiles validation (Mars + Venus atmospheric enrichment, gas export logistics)
- **Phase 13c**: Coordinated terraforming AI training (AI learns to coordinate multi-world atmospheric engineering while managing main settlements)

**Key Deliverables**:
- Psyche mining infrastructure options validated
- Mars/Venus terraforming pathway options validated
- Venus/Titan gas export mission profiles validated
- Multi-world atmospheric engineering patterns tested
- Long-haul logistics for terraforming support tested
- AI learns to balance terraforming efforts with main settlement operations

**Dependencies**:
- **Runs after Phase 11 complete** (standing cycler loop sustains core infrastructure; terraforming coordination can now be validated)
- Coordinates with Phase 11 for long-haul cycler logistics to support terraforming
- Phase 12 may run concurrently or be skipped based on AI evaluation

**Gate**: Terraforming initiation systems operational, gas export pipelines validated. All Sol system settlement and infrastructure patterns tested, validated, and AI trained. AI Manager ready for operational independence test in Phase 14+.

---

---

## Act 2: Eden Expansion Test (Phase 14+) — NPC-Only AI Training Continues

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

## Act 3: Snap Crisis (Phase 15+) — NPC-Only Crisis Response

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

## Current Backlog State (as of 2026-06-28) — SUPERSEDED, kept for history

**This section is historical.** The folder names below (`phase13+`, `phase14+`, `phase15+`
shorthand) predate the 2026-08-16 folder-naming correction in the "Current Backlog State —
Parallel Execution Model" table above. Refer to that table, not this one, for current
folder names. Kept here only as a record of the earlier sub-phase task-count breakdown.

### Key Process: Mission Validation → Settlement Option Testing → AI Training → Autonomous Decision Making

Each phase now includes explicit sub-phases for testing multiple settlement options before the AI Manager trains on them. This ensures:
1. **Mission profiles work** with the codebase before using them for AI training
2. **Multiple approaches tested** so AI doesn't just learn one scripted path
3. **Economics compared** so AI learns to pick the cost-optimal pattern
4. **Autonomous decisions** based on real game state, not predetermined sequences

| Phase | Sub-Phases | Count | Status | Intent |
|-------|-----------|-------|--------|--------|
| **phase5** | 5a (validation)<br>5b (option testing)<br>5c (AI training) | 28 | **ACTIVE** | Luna mission validation + AI training |
| **phase6** | 6a (surface validation)<br>6b (orbital options)<br>6c (AI training) | 44 | **NEXT** | Luna infrastructure validation + AI training |
| **phase7** | 7a (LEO validation)<br>7b (depot options)<br>7c (AI training) | 31 | **PLANNED** | Orbital infrastructure validation + AI training |
| **phase8** | 8a (shipyard validation)<br>8b (craft options)<br>8c (AI training) | 10 | **PLANNED** | Shipyard/craft validation + AI training |
| **phase9** | 9a (orbital validation)<br>9b (surface outposts validation)<br>9c (surface settlement validation)<br>9d (terraforming validation)<br>9e (Mars option comparison + AI training) | 14 | **PLANNED** | Mars multi-option validation + AI training |
| **phase10** | 10a (asteroid validation)<br>10b (orbital options)<br>10c (surface options)<br>10d (Venus option comparison + AI training) | TBD | **PLANNED** | Venus moon-free adaptation + AI training |
| **phase11** | 11a (docking validation)<br>11b (cargo transfer validation)<br>11c (stress testing)<br>11d (multi-world logistics AI training) | TBD | **PLANNED** | Multi-world logistics validation + AI training |
| **phase12** | 12a (Ceres validation)<br>12b (Titan/Saturn validation)<br>12c (branch economics + AI training) | 1 | **PLANNED (optional)** | Branch expansion option testing |
| **phase13** | 13a (Psyche validation)<br>13b (terraforming validation)<br>13c (advanced AI training) | TBD | **PLANNED** | Advanced mining/terraforming validation + AI training |
| **phase14+** | AI Manager operational independence test | TBD | **FUTURE** | Eden system expansion test |
| **phase15+** | Unplanned Eden expansion & Snap crisis | TBD | **FUTURE** | Crisis event trigger |
| phase13+ | TBD | PLANNED — Psyche mining & Mars terraforming | Core-remnant mining, terraforming initiation |
| phase14+ | TBD | PLANNED — AI Manager independence test | Sol autonomous handoff, Eden discovery |
| phase15+ | 1 | PLANNED — Unplanned Eden expansion / Snap trigger | Mass-limit discovery, Snap crisis event |
| design/ | 4 | EXTERNAL — Asset generation and style guides | Not part of MVP task flow |
| deprecated/ | 5 | ARCHIVED — Stale/silently-resolved tasks | Verified obsolete or superseded |
| backlog_april_2026 | 218 | SOURCE — Original old-format files | Being audited via reorganization attempts |

---

## Migration Path: Tug/Cycler Deployment Sequence

```
Act 1: Sol System Expansion (NPC-Only AI Training)
Phase 1-4 → Phase 5 → Phase 6 → Phase 7 → Phase 8 → Phase 9 → Phase 10 → Phase 11 → Phase 12/13 → Phase 14 → Phase 15 → SNAP → PLAYER ENTRY
Foundation      Luna    L1/LEO  Depots   Shipyard  Mars      Venus        Cycler     (Optional/    AI         Unplanned   (Act 2 begins)
(baseline)      base    depots  (orbit)  (L1)      (tug→     (tug→belt    loop       parallel)     independence Eden
                        ready    enable   builds    moons)    →Venus)      (standing   branches      test        overbuild
                        tug/     tug &    tug &     Cycler    Cycler       E-M-V)     Ceres/                  wormhole
                        cycler   cycler   arrives   deployed  payload      repeat     Psyche       reaches      shift
                        buildout buildout w/equip   to Mars   delivered               parallel     instability

                                          ↓
                        TUG DEPLOYMENT SEQUENCE (Sequential Dependency Chain):
                        - Phase 8 tug built at L1
                        - Phase 9: Tug→Mars, hollows moons (generates slag), repos moons, cycler arrives
                        - Tug departs Mars with FULL SLAG TANK
                        - Phase 10: Tug→Belt→Venus (uses slag propellant), captures asteroids, repos to Venus
                        - Phase 11: Cycler begins standing loop (both worlds equipped)
                        - Phases 12–13: Optional branch + terraforming run while cycler sustains core
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
