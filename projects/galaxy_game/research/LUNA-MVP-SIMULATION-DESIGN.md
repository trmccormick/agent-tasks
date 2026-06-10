# Luna MVP Simulation Design
**Prepared:** 2026-06-09
**Status:** Authoritative reference — supersedes scattered handoff notes
**Purpose:** Define what the Luna simulation must prove before any Phase 5+ work begins

---

## Why This Document Exists

Everything downstream of Luna — L1 Depot, LEO Depot, L1 Shipyard, Mars operations — depends on the Luna fuel and economic loop being proven viable in simulation first. This document captures the full design intent so task files, calibration criteria, and AI Manager behavior are all built against the same foundation.

---

## The Economic Foundation

### GCC = USD Peg (Early Phase)
- LDC's GCC balance is effectively a USD balance during the pre-player phase
- Import costs from Earth are anchored to the **Earth Anchor Price (EAP)** — already implemented in the codebase
- As real-world launch costs drop, EAP drops, and Luna import prices drop with it automatically
- Simulation constants (margin factor, consumption rates, transit buffer) must produce economically coherent behavior at real-world price anchors — not arbitrary game balance values
- GCC/USD peg separates when transaction volume across settlements is large enough to float the currency independently — estimated late Phase 6 / early Phase 7

### LOX Pricing Strategy (Pre-Player Phase)
- AI Manager sets LOX sale price at **90% of EAP equivalent** for Earth LOX imports
- Effect: Earth LOX imports become uneconomical — effectively locked out of Luna market
- LDC captures 100% of Luna LOX demand at margin
- Formula self-adjusts as EAP changes — no manual repricing needed
- This is LDC's **first real revenue stream** during the pre-player phase

---

## The Volatiles Production Chain

Luna's volatiles are primarily locally sourced, not imported. This is the foundation of economic viability.

```
Raw regolith
  └─ TEU (Thermal Electric Unit)
       Bakes regolith — releases easy volatiles (water vapor, CO2, trace gases)
       └─ PVU (Planetary Volatiles Unit)
            Unlocks oxygen from oxides
            Outputs: O2 (LOX) + depleted regolith (byproduct)
            └─ LOX → primary tank farm inventory (HLT fuel, skimmer turnaround)
            └─ Depleted regolith → 3D printing units feedstock
                  └─ I-beams, panels, structural components
                  └─ Tank shells (pressurized with regolith processing output)
```

**Key insight:** Construction is a byproduct of life support. You cannot make oxygen without also producing building material. TEU/PVU must be operational before the tank farm can be built.

### Earth Imports (Targeted, Not Bulk)
- **N2 (nitrogen)** — no local source, required for habitat atmosphere buffering
- **CH4 (methane)** — no local hydrogen source early; some CH4 possible from CO2 processing but limited
- **Equipment/components** — items that cannot be 3D printed yet
- Water, oxygen, structural materials are **not** bulk import categories — locally produced

---

## The Precursor Build Sequence

The precursor phase is **deadline-driven**, not just resource-constrained. HLT tankers and skimmers launch from Earth before the precursor robots land. The infrastructure must be ready before those craft arrive.

### Hard Sequential Dependency Chain

```
1. Power grid         ← everything depends on this, must be first
2. Comms              ← remote operation from Earth requires this
3. TEU online         ← begins regolith processing
4. PVU online         ← produces O2 + depleted regolith
5. Regolith feedstock available
6. 3D print tank shells on surface
7. Pressurize shells with regolith processing output
8. Tank farm operational
9. Landing pad construction (now tank farm can support operations)
```

**This is not a parallel build list.** Each step unlocks the next. Throwing more robot workers at step 6 does not help if step 5 is incomplete. The AI Manager precursor scheduler must model this as a dependency graph.

### The Tank Farm Is a Critical Scheduling Asset

The tank farm does more than store gas — it is the **transfer hub** for skimmer turnarounds:

- Venus skimmer lands → offloads N2 + CO2 payload → transfers pre-positioned CH4 to primary tanks → ready for next launch window
- Titan skimmer lands → offloads CH4 payload → transfers pre-positioned LOX to primary tanks → ready for next launch window

**AI Manager must pre-position correct propellant based on inbound skimmer type:**
- Venus inbound → ensure CH4 in raw storage tanks before arrival
- Titan inbound → ensure LOX in raw storage tanks before arrival
- Wrong propellant or empty tank → turnaround delayed → launch window missed → full Venus/Titan orbital cycle wasted

### Tank Farm Capacity Planning

Tank farm must simultaneously hold:
- LOX reserves (HLT operations + Titan turnaround pre-position)
- CH4 reserves (HLT operations + Venus turnaround pre-position)
- N2 reserves (habitat atmosphere)
- CO2 incoming (for processing chain)
- Payload staging (freshly delivered skimmer cargo)

Tank farm sizing is a mission planning problem. Too small = can't pre-position for turnarounds while supporting HLT ops. Too large = over-invested precious precursor construction resources.

---

## The Skimmer Program

### Launch Timing
HLT tankers and skimmers launch from Earth **before** the Luna precursor mission starts. The intent is to give the precursor robots lead time to build infrastructure before craft need to land.

**Titan launches first** — longest flight time, must depart earliest.
**Venus launches later** — shorter flight time, departs after Titan.
**All three must converge** so landing pad and tank farm are ready before first skimmer arrives.

The equation:
```
Venus arrival = Venus launch date + Venus flight time
Landing pad ready = Precursor start + build time (power → comms → TEU → PVU → tanks → pad)

Constraint: Landing pad ready ≤ Venus skimmer arrival
```

### If Landing Pad Is Not Ready
The skimmer enters **orbital holding pattern**:
- Station-keeping burns propellant continuously
- Payload value degrades per day in orbit
- Extended hold → skimmer arrives nearly empty
- Worst case → mission loss

Every day a skimmer orbits waiting for a pad is:
- Lost N2/CH4 payload that LDC still has to import from Earth
- Lost LOX revenue opportunity
- Continued GCC burn

**This is a key simulation failure mode to watch for in calibration.**

---

## The Skimmer Fuel Loops

### Venus Skimmer — Self-Fueling on Oxidizer Side

```
Outbound (Earth/Luna → Venus):
  Fueled with LOX + CH4

At Venus:
  Harvests CO2 from atmosphere (~96% CO2, ~3.5% N2)
  Onboard processing: CO2 → O2 + CO (in-flight on return)
  Arrives at Luna with LOX tanks refilled from own processing

Luna turnaround:
  Needs: CH4 only (raw storage tank pre-loaded)
  Delivers: N2 (small fraction — limited separation capacity) + CO2 payload
  Repairs/maintenance
  Relaunch on next Venus window
```

**Economic note:** Each Venus trip cost = CH4 for turnaround + repairs. Revenue = N2 payload value + CO2 payload value (EAP-anchored). This is calculable ROI per cycle.

**N2 delivery is marginal per trip**, not bulk. Multiple trips needed before Earth N2 imports meaningfully decline. Timing the first arrival correctly means accumulation starts sooner.

### Titan Skimmer — Mirror Problem

```
Outbound (Earth/Luna → Titan):
  Fueled with LOX + CH4

At Titan:
  Harvests CH4 from atmosphere (~95% N2, ~5% CH4)
  Cannot produce LOX — no oxygen source
  Returns with full CH4 payload, LOX depleting entire transit home

Luna turnaround:
  Needs: LOX only (raw storage tank pre-loaded — LDC local production)
  Delivers: CH4 (abundant — this is the big payload)
  Repairs/maintenance
  Relaunch on next Titan window
```

### The Symmetry

| | Venus Skimmer | Titan Skimmer |
|---|---|---|
| Produces in transit | LOX (from CO2) | Nothing (no O2 source) |
| Needs on Luna turnaround | CH4 | LOX |
| Delivers to Luna | N2 + CO2 | CH4 |
| LDC supplies from | Earth imports (early) / Titan payload (later) | Local LOX production |

**Venus and Titan are complementary supply chains.** Together they eventually break all Earth propellant imports. Luna's local LOX production is the bridge that makes both turnarounds possible.

---

## The Import Dependency Curve

| Phase | N2 | O2/LOX | CH4 |
|---|---|---|---|
| Precursor (pre-TEU/PVU) | Earth import | Earth import | Earth import |
| TEU/PVU online | Earth import | Local LOX | Partial local (CO2 processing) |
| First Venus skimmer cycling | Declining (Venus N2) | Local + CO2 path | Still constrained — Earth bridge |
| Titan skimmer arrives | Venus supply | Abundant local | **Unlocked — Earth imports → zero** |

**Titan arrival is the economic independence event.** Once Titan CH4 starts flowing:
- CH4 imports from Earth → zero
- Venus skimmer gets CH4 from Titan deliveries, not Earth
- LDC's import bill drops to N2 top-off and equipment/supplies only
- LOX sales become the primary LDC revenue stream

---

## The CH4 Bridge Period

Between TEU/PVU online and Titan arrival, the entire fleet runs on:
- Local LOX (good — covered by PVU)
- Earth CH4 imports + limited Venus CO2 processing output

**CH4 is the constrained resource during the bridge period.** It is consumed by:
1. HLT tanker operations
2. Venus skimmer turnaround/relaunch
3. Any other propulsion needs

**AI Manager must arbitrate CH4 allocation** between these competing demands. If a Venus launch window opens but CH4 is reserved for HLT ops, the skimmer sits another full Venus cycle. That is a significant GCC cost in lost N2/CO2 deliveries.

---

## Phase 5 Calibration — Acceptance Criteria

The simulation calibration phase is not "tune until it feels right." These are the specific things the simulation must demonstrate before Phase 6 (L1 infrastructure) is unlocked.

### Build Sequence Validation
- [ ] Precursor dependency chain completes in correct order (power → comms → TEU → PVU → tanks → pad)
- [ ] Landing pad and tank farm operational before first Venus skimmer arrival window
- [ ] No skimmer enters orbital holding due to late pad construction

### Propellant Economics
- [ ] LOX production crossover: local LOX output offsets Earth oxidizer imports within expected timeline
- [ ] CH4 bridge period is economically survivable — GCC balance stays positive throughout
- [ ] Venus skimmer ROI positive per cycle at current EAP values
- [ ] Titan arrival triggers measurable drop in Earth CH4 import orders
- [ ] Earth CH4 imports reach zero within N cycles after Titan begins regular cycling

### Tank Farm Coordination
- [ ] AI Manager correctly pre-positions CH4 before Venus arrivals
- [ ] AI Manager correctly pre-positions LOX before Titan arrivals
- [ ] No turnaround delayed due to wrong propellant pre-positioned
- [ ] Tank farm capacity sufficient for concurrent HLT + skimmer operations

### ImportRequestGenerator Behavior
- [ ] N2 orders from Earth decline as Venus N2 deliveries accumulate
- [ ] N2 orders stop when Venus supply is sufficient for habitat + processing needs
- [ ] CH4 orders from Earth decline as Titan deliveries accumulate
- [ ] No Earth LOX imports (locked out by 90% EAP pricing)
- [ ] Inbound skimmer manifests suppress redundant Earth import orders for same material

### Economic Viability
- [ ] LDC GCC balance stays positive through entire pre-player arc
- [ ] LOX revenue offsets N2 + CH4 import costs at some point before Titan arrival
- [ ] Post-Titan: LDC operating cash-flow positive on local production + LOX sales alone
- [ ] Break-even point identified: at what LOX production volume does revenue offset imports?

### Simulation Failure Modes to Watch
- **Death spiral:** CH4 imports cost more than LOX savings, GCC drains, ships ground, no revenue
- **Over-ordering:** ImportRequestGenerator too conservative, excess stock ties up GCC
- **Under-ordering:** Transit buffer too tight, ships ground waiting for delivery
- **Skimmer strand:** Landing pad not ready, skimmer in orbit burning payload
- **Launch window miss:** CH4 not available when Venus window opens, skimmer waits full cycle
- **Premature expansion:** Phase 6 starts before fuel loop proven, LDC overextended

---

## AI Manager Requirements (New Capabilities Needed)

The following capabilities are required for the simulation to run correctly. These become task file targets.

### Inbound Cargo Awareness
ImportRequestGenerator must check inbound skimmer manifests before ordering from Earth. If Venus skimmer is 3 days out with N2 payload, do not order N2 from Earth today.

### Multi-Source Supply Modeling
The current architecture assumes Earth as the only import source. The system must support:
- Multiple sources for the same material (Earth, Venus, Titan)
- Source priority ordering (local production → skimmer delivery → Earth import)
- Automatic source switching as new supply chains come online

### CH4 Allocation Arbitration
Priority queue for scarce methane during bridge period:
- Define priority order: HLT ops vs Venus skimmer relaunch window vs habitat reserves
- Reserve allocation for upcoming launch windows
- Alert if CH4 reserves insufficient for next scheduled launch window

### Precursor Build Scheduler
- Model build sequence as dependency graph (not task list)
- Track completion of each dependency gate
- Calculate projected completion dates against skimmer arrival windows
- Escalate if critical path is slipping relative to arrival window

### Skimmer Turnaround Coordinator
- Know inbound skimmer type (Venus vs Titan)
- Pre-position correct propellant in raw storage before arrival
- Schedule maintenance/repair assessment on landing
- Track next launch window and reserve propellant accordingly

---

## Phase Structure (Revised)

Based on this design, the correct phase structure is:

**Phase 4 — Consumption-Aware Ordering** *(active)*
Mechanical foundation: transit-aware ordering + precursor phase gating. `current_population.zero?` as precursor gate. Life support materials buffered correctly.

**Phase 5 — Luna Simulation Calibration** *(next)*
Not a feature phase — a validation phase. Run the simulation, observe behavior against acceptance criteria above, tune constants. Specific focus: LOX crossover point, CH4 bridge survivability, skimmer timing. **This phase does not complete until all acceptance criteria pass.**

**Phase 6 — L1 Depot Infrastructure** *(after Phase 5 validated)*
Venus and Titan skimmers need an unloading/refueling point that isn't the Luna surface. Initial gas processing at depot. Unlocks HLT tanker operations at scale.

**Phase 7 — LEO Depot** *(after L1 viable)*
Earth-side revenue node. Gas sales. Craft leaving Earth fuel up here. GCC currency volume starts building toward peg separation.

**Phase 8 — L1 Shipyards** *(after depot network profitable)*
Unlocks tug and cycler construction. First tug goes to Mars for Phobos/Deimos repositioning.

**Phase 9+ — Sol Expansion** *(after L1 Shipyard operational)*
Cyclers, Mars settlement, Phobos/Deimos repositioning, outer Sol expansion.

---

## Outstanding Architecture Questions

These need answers before Phase 5 task files can be written:

1. **Flight time constants** — are Venus and Titan transit times already defined in the codebase, or do these need to be established?
2. **Multi-source supply architecture** — does `ImportRequestGenerator` currently support multiple supply sources, or is this new work?
3. **Inbound cargo manifest tracking** — is there a mechanism to track "craft X is inbound with payload Y, arriving in Z days"?
4. **Precursor build dependency graph** — is construction sequencing modeled anywhere, or is this new?
5. **CH4 allocation arbitration** — is there a priority queue mechanism for scarce resources, or is this new?
6. **Skimmer as asset type** — are skimmers modeled as persistent assets with fuel state, location, and mission phase?

---

## Notes for Qwen Backlog Triage

When reviewing April 2026 backlog tasks, categorize against this phase structure:

- **Keep in active backlog (Phase 4):** Luna AI Manager mechanics, consumption modeling, precursor phase gating, life support material ordering
- **Move to phase5+/ (Phase 5-6):** Skimmer processing, multi-source supply, tank farm architecture, inbound cargo awareness, CH4 arbitration, flight time modeling
- **Move to phase6+/ (Phase 7-8):** LEO depot, L1 depot revenue, L1 shipyard, cycler construction
- **Move to future/ (Phase 9+):** Mars, Phobos/Deimos, outer Sol, wormhole topology

**Critical gaps to create bug fix tasks for immediately (Qwen identified these):**
- `processed_slag` resource undefined — needed for hollowing operations
- GCC tax variables missing for transport/hollowing
- Foundry prerequisites may be incomplete (Lunar Space Elevator gate logic)

These should be captured as HIGH priority bug fix tasks before Phase 5 work begins.
