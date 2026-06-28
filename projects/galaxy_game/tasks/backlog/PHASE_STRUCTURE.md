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

**Scope**: Earth → Luna → Mars → Venus → Ceres/Titan → Psyche → Terraforming initiation
**Player Experience**: None during Act 1. Players inherit the result post-Snap.

**Critical Process for Act 1**: Mission Validation → Settlement Option Testing → AI Training → Autonomous Decision Making

Each settlement location follows this workflow:
1. **Mission Validation**: Run JSON mission profiles to ensure they work with current codebase
2. **Settlement Option Testing**: Test multiple settlement approaches (orbital vs surface, different material sources, various infrastructure options)
3. **AI Training**: Feed validated patterns to AI Manager for pattern learning
4. **Autonomous Decision Making**: AI learns to evaluate options and pick by real cost (ROI, available resources, economics)

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

### Phase 9 — Mars Orbital & Surface: Testing & AI Training
**Goal**: Test Mars settlement options (orbital + surface); train AI on multi-option selection logic.

**Scope**: Mars footholds including Phobos/Deimos repositioning AND surface settlement options
**Structure**:
- **Phase 9a**: Mars orbital setup mission validation (Phobos/Deimos repositioning, hollowing, station construction — current mars_settlement/phases/phase0*)
- **Phase 9b**: Mars surface outposts mission validation (surface infrastructure, mining, resource processing — current mars_settlement/phases/phase1-3)
- **Phase 9c**: Mars surface settlement validation (worldhouse, habitat infrastructure — current mars_settlement/phases/phase4-6)
- **Phase 9d**: Mars terraforming initiation validation (atmospheric enrichment, great warming options — current mars_settlement/phases/phase5-8)
- **Phase 9e**: Mars option comparison and AI training (AI learns to evaluate which Mars approaches provide best ROI given local conditions)

**Key Deliverables**:
- Tug deployment and testing mission validation
- Phobos/Deimos hollowing infrastructure mission profiles validated
- Moon repositioning mechanics tested
- Mars surface outpost establishment options validated
- Mars surface resource infrastructure options tested
- Mars worldhouse construction mission validation
- Mars terraforming initiation options tested
- First cycler return/resupply loop established

**Critical Note**: Mars has MULTIPLE settlement approaches in JSON mission profiles:
- Orbital-first: Station on Phobos/Deimos, minimal surface
- Surface-first: Direct surface outposts, then orbital support
- Mixed approach: Parallel orbital + surface development
- Terraforming-focused: Surface + atmospheric work from phase 1
AI learns to evaluate these by real cost and pick the pattern that works best given Mars-specific conditions (moon availability, surface resources, economics, etc.)

**Gate**: Mars settlement patterns tested, validated, and AI trained on multi-option selection. Mars station operational, first cycler resupply loop validated, terraforming pathway established. Ready for Phase 10 (Venus repeat with different options).

---

### Phase 10 — Venus Orbital & Surface: Testing & AI Training
**Goal**: Test Venus settlement options (asteroid capture, orbital, surface); train AI on adaptation to moon-free environment.

**Scope**: Venus footholds including asteroid capture, alternative orbital approaches, surface options
**Structure**:
- **Phase 10a**: Venus asteroid capture mission validation (multiple asteroid options, different trajectories)
- **Phase 10b**: Venus orbital station options mission validation (stationed asteroid variants)
- **Phase 10c**: Venus surface settlement options mission validation (if available)
- **Phase 10d**: Venus option comparison and AI training (AI learns how to adapt when moons aren't available; different cost hierarchy applies)

**Key Deliverables**:
- Asteroid identification and capture mission profiles validated
- Asteroid repositioning into Venus orbit mission profiles tested
- Hollowing and station construction mission profiles validated
- Alternative orbital infrastructure for moon-free worlds tested
- Parallel second cycler-tug pair construction validated
- Cost comparison: asteroid capture vs other options

**Critical Note**: Venus has NO moons, so cost hierarchy differs from Mars:
- Can't reposition existing moons
- Must capture asteroid from belt
- Requires different transport/logistics
- Different economics apply
AI learns this different pattern and adapts decision logic accordingly.

**Gate**: Venus settlement patterns tested, validated, and AI trained on moon-free adaptation. Venus station operational, alternative orbital approaches proven, logistics loop with Mars validated.

---

### Phase 11 — Multi-World Logistics Maturation: Testing & AI Training
**Goal**: Test and validate cycler logistics, docking, cargo transfer across multiple worlds; train AI on complex logistics management.

**Scope**: Earth-Mars-Venus cycler logistics validation and optimization
**Structure**:
- **Phase 11a**: Cycler docking mission validation (multiple cycler-world pair combinations)
- **Phase 11b**: Cargo transfer mission validation (docking, undocking, cargo movement protocols)
- **Phase 11c**: Heavy logistics stress testing mission validation (multiple simultaneous transit legs)
- **Phase 11d**: Multi-world logistics AI training (AI learns to coordinate multiple simultaneous operations)

**Key Deliverables**:
- Docking/undocking reliability across all three footholds validated
- Cargo transfer validation under realistic load scenarios
- Heavy logistics stress testing with multiple simultaneous transits
- Cycler scheduling and coordination patterns tested
- Multi-world resource arbitrage patterns identified

**Gate**: Earth-Mars-Venus cycler logistics loop stable and repeatable. Core inner-system trade network proven, ready for Phase 12 (branch expansion options testing).

---

### Phase 12 — Optional Branch Expansion: Testing & AI Training
**Goal**: Test optional branch settlement approaches (Ceres/Titan-Saturn); train AI on parallel expansion decisions.

**Scope**: Belt mining infrastructure, gas giant moon settlement options
**Structure**:
- **Phase 12a**: Ceres belt mining mission profiles validation (multiple mining approaches)
- **Phase 12b**: Titan/Saturn settlement mission profiles validation (if available)
- **Phase 12c**: Branch economics and AI training (AI learns to evaluate whether branch expansion is cost-effective given current state)

**Key Deliverables**:
- Ceres belt mining infrastructure options validated (if pursued)
- Titan/Saturn settlement support options validated (if pursued)
- Branch vs core loop economics compared
- Parallel expansion patterns tested

**Gate**: N/A (optional) — branches can be pursued independently or skipped based on AI economic evaluation; does not gate Phase 13 progression.

---

### Phase 13 — Psyche Mining & Terraforming: Testing & AI Training
**Goal**: Test advanced mining (core-remnant extraction) and terraforming options; train AI on late-stage expansion patterns.

**Scope**: Psyche core-remnant mining + multi-world terraforming infrastructure validation
**Structure**:
- **Phase 13a**: Psyche mining mission profiles validation (core-remnant-specific extraction patterns)
- **Phase 13b**: Mars/Venus terraforming mission profiles validation (atmospheric enrichment, gas export logistics)
- **Phase 13c**: Advanced terraforming AI training (AI learns to coordinate multi-world atmospheric engineering)

**Key Deliverables**:
- Psyche mining infrastructure options validated
- Mars terraforming pathway options validated
- Venus/Titan gas export mission profiles validated
- Multi-world atmospheric engineering patterns tested
- Long-haul logistics for terraforming support tested

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

## Current Backlog State (as of 2026-06-28)

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

## Migration Path to Mars and Beyond (Updated)

```
Act 1: Sol Expansion (NPC-Only AI Training)          Act 2: Eden Test (NPC-Only)     Act 3: Snap Crisis (NPC-Only)
Phase 1-4 → Phase 5 → Phase 6 → Phase 7 → Phase 8 → Phase 9 → Phase 10 → Phase 11 → Phase 12 → Phase 13 → Phase 14 → Phase 15 → SNAP → PLAYER ENTRY (Act 2 gameplay)
Foundation      Luna    Lava-Tube  Depots   Shipyard  Mars      Venus      Cycler     Ceres/     Psyche/    AI         Unplanned   (Act 2
(no humans)     base    (orbital) (tug/    footholds footholds  logistics  Titan-     terra-     independence Eden       begins)
                                          cycler)   (moons)   (asteroid) maturation Saturn     forming    test         overbuild
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
