# Luna MVP Simulation Design
**Prepared:** 2026-06-09
**Last Updated:** 2026-07-05 — Planning Agent (Qwen3.6-35b)
**Status:** Authoritative reference — supersedes scattered handoff notes
**Purpose:** Define what the Luna simulation must prove before any Phase 5+ work begins
**Scope:** MVP = Luna fuel loop + skimmer delivery chain (Venus/Titan → Luna). Terraforming is Phase 9+.

> **⚠️ SCOPE NOTE:** This document covers MVP scope only. Skimmers deliver fuel/processing gases to Luna — they do NOT terraform. Terraforming begins in Phase 9+ (Mars/Venus orbital + surface settlement). Do not conflate skimmer operations with terraforming intent.

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

## The Skimmer Program — MVP Fuel Delivery (Not Terraforming)

### Core Intent
Skimmers are **specialized atmospheric harvesters** owned and operated by AstroLift. Their purpose is to deliver raw atmospheric gases from Venus/Titan to Luna for processing into fuel, habitat atmosphere, and ISRU feedstock. This is **MVP fuel delivery**, not terraforming.

From `skimmer_craft_intent.md`:
- **Venus Skimmer**: CO2 → LOX via Sabatier Reactor / CO2 Scrubbing (primary output: LOX)
- **Titan Skimmer**: CH4 fractionation / H2 Separation (primary output: CH4)
- Both carry "Lite" AtmosphericRefineryService for onboard processing during transit
- Docking capability: L1 Depot (active refinement, +15% throughput per skimmer) AND Cyclers

### MVP Fuel Loop Architecture
```
Venus Skimmer:
  Harvests CO2 (~96%) + N2 (~3.5%) from Venus atmosphere
  → Onboard processing: CO2 → O2 (fills own LOX tank for return)
  → Delivers to Luna: N2 + CO2 mixed payload (unprocessed raw gas)
  → Needs on turnaround: CH4 only (from Titan deliveries or Earth bridge)
  → Value: LOX self-fueling economy, CO2 feedstock for Sabatier synthesis

Titan Skimmer:
  Harvests CH4 (~5%) + N2 (~95%) from Titan atmosphere
  → Onboard processing: CH4 fractionation (fills own CH4 tank for return)
  → Delivers to Luna: CH4 + N2 mixed payload (unprocessed raw gas)
  → Needs on turnaround: LOX only (from LDC local production)
  → Value: Bulk CH4 delivery, breaks Earth import dependency
```

### Key MVP Facts
- **Cargo is NOT pre-sorted.** AstroLift delivers raw mixed atmospheric gas. LDC's onsite processing units determine what value is extracted from the mix.
- **Skimmers are continuous mobile processing plants** — they process continuously during transit and docked dwell time, not just on delivery.
- **Venus and Titan are complementary supply chains.** Together they eventually break all Earth propellant imports.
- **N2 delivery is marginal per trip** from Venus (100 kg/hr), not bulk. Titan is the primary N2 source by volume (760 kg/hr).
- **CO output from Venus skimmer** — currently vented, but Luna metal processing chain may use CO as reducing agent for oxide reduction. Assess before finalizing gas handling policy.

### Phase Alignment
| Phase | Skimmer Relevance |
|-------|------------------|
| Phase 5 | Luna validation — skimmers NOT yet operational (infrastructure not built) |
| Phase 6 | Luna surface & infrastructure — tank farm, landing pad ready for skimmer arrival |
| Phase 7 | Orbital infrastructure — L1 Depot online, skimmers can offload raw gas directly |
| Phase 8 | Shipyard/craft — purpose-built skimmers produced at L1 Shipyard |
| Phase 9+ | Mars/Venus terraforming — skimmers now support terraforming feedstock (post-MVP) |

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

## Phase 5 Calibration — Acceptance Criteria (MVP Scope Only)

> **SCOPE LIMIT:** Phase 5 = Luna validation only. Skimmer operations are NOT part of Phase 5 calibration. The simulation must prove the Luna fuel loop works BEFORE skimmers arrive.

The simulation calibration phase is not "tune until it feels right." These are the specific things the simulation must demonstrate before Phase 6 (L1 infrastructure) is unlocked.

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
## Addendum — 2026-06-10: Skimmer Operations, Gas Economics, Virtual Ledger, and Sol Market Dynamics

---

### Skimmer Variants — Production Scope

Only two harvester variants are production-scoped for Luna MVP:

- **Venus Harvester** (`heavy_lift_transport_harvester_venus`) — `co2_splitter` x2, solar power
- **Titan Harvester** (`heavy_lift_transport_harvester_titan`) — `gas_separator` x3, RTG power

A Mars harvester variant exists in the data (`heavy_lift_transport_harvester_mars`) but is
prototype/concept status only. Do not include Mars in Luna MVP supply chain planning or AI
Manager decision logic. Mars harvester requires separate design review before any mission
scoping.

---

### Skimmer as Continuous Mobile Processing Plant

A skimmer is not a simple delivery vehicle — it is a mobile processing plant that operates
continuously whenever it has raw gas in storage and power available. Processing does not stop
at delivery. The full operational cycle:

**In transit outbound**
- Empty storage, systems nominal, en route to harvest location

**At harvest location**
- Scoops atmosphere, fills raw storage
- Processing begins immediately as storage fills
- Self-fueling from own processed output (LOX for Venus, CH4 for Titan)

**In transit inbound**
- Raw storage full or near-full
- Processing runs continuously entire return leg
- Craft arrives at Luna with partially or fully processed payload depending on transit duration
- Longer transit = more processed on arrival, less raw gas remaining to process while docked

**Docked at Luna or depot**
- Mandatory inspection, repair, and maintenance (`maintenance_interval_hours: 750-800`)
- Launch window wait — orbital mechanics, not negotiable
- Processing continues on remaining raw gas in storage during entire docked period
- Processed output delivered continuously to tank farm or depot via docking connection
- Propellant loaded for next outbound leg during this window

**Pre-launch offload — mandatory before departure**
- All raw gas transferred to depot or Luna settlement storage before window opens
- Depot processes locally if capacity available — skimmer does not need to divert to Luna
- If depot storage full → divert raw gas offload to Luna settlement network
- Craft departs with raw storage empty or near-empty — carrying unprocessed raw gas
  wastes fuel and payload capacity
- Only exception: network storage full across all available nodes — planning failure,
  not normal ops

**Launch window opens**
- Departs with raw storage cleared
- Propellant loaded, maintenance complete

**Key implication for AI Manager:**
Total gas delivered per mission = (processed on inbound transit) + (processed during docked
dwell time). Tank farm receives a continuous trickle during docked period, not a single bulk
transfer on landing. Docked dwell time is driven by mandatory inspection/repair schedule and
launch window — not by processing completion. Processing during dwell is a free operational
byproduct, not a reason to extend turnaround.

---

### Complete Operational Flow — Processing, Distribution, and Depot

Luna is the processing hub. Tankers are the distribution network. Depots are the retail nodes.

```
Skimmer harvests atmosphere
  → offloads raw gas at L1 depot (primary)
      depot processes locally → finished products in depot storage
  OR
  → offloads raw gas at Luna settlement (if depot full or unavailable)
      Luna processes (TEU/PVU, co2_splitter, gas_separator)
      → finished products in Luna tank farm
          → Tanker loads finished products from Luna tank farm
              → delivers to L1 depot / LEO depot / cycler intercept

Depot storage (finished products from either path)
  → refuels incoming craft on demand — gas station model
  → transfers to cycler intercept via tanker
  → exports to LEO depot via tanker
```

**Depot dual capability:**
- Accepts raw gas directly from skimmers — processes locally, skimmer never needs to
  divert to Luna surface if depot has capacity
- Accepts finished products from Luna tankers — stores and distributes
- Depot processing is an offload relief valve and direct skimmer service capability —
  not a replacement for Luna's industrial processing backbone

**Tanker role — finished product distribution only:**
- No processing modules — pure bulk transport of processed gases
- Moves finished products between Luna tank farm, depots, and cycler intercepts
- Cycler intercept capability — tanker matches velocity, transfers finished product,
  cycler never stops
- Stores: LOX, CH4, N2, He3 — all finished products, nothing raw

**Offload routing hierarchy:**
1. L1 depot direct — skimmer offloads raw gas, depot processes locally
2. Luna settlement network — if depot full or unavailable, divert to Luna surface
3. Never depart with offloadable raw gas — always clear raw storage before window

**Capacity grows over time:**
- Early game — single Luna settlement, L1 depot under construction — offload constrained
- Mid game — multiple Luna settlements, L1 depot online — offload comfortable
- Late game — full Luna network, multiple depots — offload never a bottleneck
- Each new Luna settlement adds raw gas storage and processing capacity to the network

---

### Turnaround Dependencies

Turnaround is mandatory. AI Manager must ensure these are in place before skimmer arrives:

- **Maintenance parts and consumables** — if parts not on Luna, skimmer misses its window
- **Correct propellant pre-positioned** — Venus needs CH4, Titan needs LOX
- **Tank farm capacity** — must have room for continuous processed gas output during dwell
- **Repair capability** — inspection and repair must complete before window opens

Missing any of these is a window miss. A missed window costs a full orbital cycle.

---

### Venus Harvester — Value Proposition

**Module config:** `co2_splitter` x2, `solar_array` x6
**Power:** Solar — viable and efficient at Venus distance, no RTG cost
**Output rates:** O2 200 kg/hr, N2 100 kg/hr, CO currently vented
**Self-fuels:** LOX from own O2 output — arrives at Luna with oxidizer tanks refilled
**Needs on turnaround:** CH4 only

**Primary mission justification:**
- LOX self-fueling economy — every Venus trip reduces Luna LOX expenditure on outbound missions
- CO2 feedstock for Luna processing chain
- Shorter transit than Titan — faster cycle time, more trips per year
- Solar power — lower build and operating cost than RTG variants

**CO output — pending design decision:**
Currently `"vent": ["carbon_monoxide"]` in gas handling policy. If Luna metal processing
chain can use CO as a reducing agent for oxide reduction, vent policy should change to store.
CO value at Luna should be assessed before finalizing Venus gas handling policy. This is a
potentially recoverable revenue stream currently being thrown away.

**N2 note:** Venus delivers modest N2 per trip (100 kg/hr). Venus N2 is supplementary
accumulation, not bulk supply. Titan is the primary N2 source by volume (760 kg/hr).

**Critical dependency:** Venus has no CH4 source. All Venus skimmer operations and Venus
settlement propulsion requires CH4 imports. Venus DC is structurally dependent on whoever
controls the CH4 supply chain until local Sabatier synthesis becomes viable.

---

### Titan Harvester — Value Proposition

**Module config:** `gas_separator` x3, `rtg_power_unit` x1
**Power:** RTG only — no solar viable at Titan distance. Power-constrained operation.
**Output rates:** N2 760 kg/hr, CH4 140 kg/hr
**Self-fuels:** CH4 from own output — arrives at Luna with methane tanks refilled
**Needs on turnaround:** LOX only — no O2 source at Titan, Luna ISRU is the critical path

**Primary mission justification:**
- N2 bulk delivery — 760 kg/hr, 7.6x Venus N2 rate. Titan is the primary N2 source.
- CH4 delivery — path to breaking Earth CH4 import dependency
- Titan arrival is the economic independence event for both N2 and CH4

**Power constraint implications:**
- RTG limits processing rate — gas_separator x3 running on constrained power budget
- Long transit partially offsets this — more processing time on inbound leg
- Storage fills faster than processing can convert — raw gas accumulates in cryogenic storage
- Craft makes strategic storage allocation: prioritize CH4 or N2 based on AI Manager mission mode

---

### Per-Mission Gas Handling Policy

The `gas_handling_policy` in craft operational data is not static — it is a **per-mission
configuration** the AI Manager sets at dispatch time based on Luna inventory projections
at estimated arrival.

**Titan storage tradeoff:**
Titan atmosphere is 95% N2, 5% CH4. Storage fills rapidly with N2. To accumulate more CH4
the craft must vent N2 to make room — but venting is not free:
- **Power cost** — separator ran to produce gas now being vented
- **Time cost** — harvest time spent on gas not kept
- **Module wear** — processing hours consumed for zero delivered value

**The lazy default is no venting** — deliver natural atmospheric composition, let Luna tank
farm sort it out. Only vent when value differential and inventory need clearly justify the
waste. If Luna is flush with CH4, venting N2 to chase CH4 is actively harmful.

**Vent decisions require inventory projections at arrival, not just current state.**
AI Manager must project what Luna will need at estimated arrival time — current inventory
minus consumption over transit duration — before setting mission vent policy.

---

### CH4 Sources — MVP Scope

CH4 sourcing for Luna MVP in order of availability:

1. **Earth direct import (AstroLift)** — expensive bridge, always available
2. **Titan skimmer** — bulk delivery, long cycle, primary long-term source
3. **Venus CO/CO2 processing** — limited yield, byproduct of existing Venus mission only

**Future seam — not MVP scope:**
Luna polar ice → H2 electrolysis + Venus CO2 → Sabatier CH4 synthesis
(CO2 + 4H2 → CH4 + 2H2O). Architecturally sound, worth preserving as a future seam,
but ice mining infrastructure is not part of initial base setup. Ice extraction develops
as a parallel Luna surface track during Phase 6/7 as the industrial base matures — but
only after biological water demands (drinking, food production, sanitation) are fully met.
The market determines when surplus ice makes synthesis economically viable vs Titan imports.

**Saturn H2 (Phase 9+):**
Gas giant harvesting delivers essentially unlimited H2. Saturn H2 + Venus CO2 → Sabatier
synthesis makes Venus CH4 independent. Luna ice→H2 pathway becomes less critical.
Titan value shifts fully toward N2 primary mission. This reshapes the entire Sol CH4
economy — but it is a Phase 9+ event, not current architecture.

---

### Source Selection is Market-Determined

**Do not hardcode source priority tables.** The AI Manager evaluates source selection at
runtime against current EAP-anchored costs, current inventory, and projected demand.
The market determines the right answer — not design doc prescriptions.

What changes over time:
- New engine technology → transit times drop → cycle frequency increases → supply increases
- Cyclers arrive → bulk cargo economics change → per-unit delivery cost drops
- New settlements online → demand side grows → price signals shift
- Better processing modules → extraction efficiency improves → local production costs drop
- Population grows → biological demand increases → ice allocation economics shift
- Saturn settlement → unlimited H2 → CH4 and propellant economics restructure entirely

The AI Manager re-evaluates continuously. A decision correct at Luna Year 1 may be wrong
at Luna Year 5. No fixed priority survives a dynamic economy.

---

### Virtual Ledger — Purpose and Function

The Virtual Ledger is an NPC-only mechanism. Players always transact in real GCC.

**What the Virtual Ledger is:**
- The AI Manager's diagnostic instrument — the P&L statement per NPC settlement
- Tracks NPC economic health: revenue vs costs, project affordability, inter-NPC balances
- Primary purpose: tell the AI Manager what is working and what isn't

**What the Virtual Ledger is not:**
- A substitute for real GCC in player transactions — never
- A permanent credit facility — sustained negative balance is a failure signal, not normal ops

**NPC transaction rules:**
- NPC-to-NPC → real GCC preferred, Virtual Ledger as fallback when GCC insufficient
- All NPCs (DCs and commercial operators alike) can settle via Virtual Ledger with other NPCs
- NPC-to-Player → real GCC only, no exceptions
- Player-to-NPC → real GCC only, no exceptions

**Self-funding NPC loops:**
When NPC commercial operators transact within a local economy, real GCC may never need
to move. Example — AstroLift Luna operation:
- Delivers imports → sells to LDC → receives GCC credit
- Buys LOX from LDC for return trip → pays GCC debit
- Net balance settles via Virtual Ledger — real GCC only moves if there is a net surplus
  or deficit that can't be internally resolved

This is intentional. The Virtual Ledger lubricates NPC bootstrapping before real GCC
volume is sufficient. Real GCC's primary purpose is getting currency into players' hands
through productive economic activity — selling resources, taking contracts, providing
services the NPC economy needs.

**AI Manager response tiers based on ledger health:**

| Ledger State | AI Manager Response |
|---|---|
| Positive and stable | Continue operations, evaluate expansion opportunities |
| Declining trend | Adjust strategy autonomously — shift priorities, cut non-essential projects |
| Deeply negative, recoverable | Escalate to game admins with ledger data and diagnosis |
| Deeply negative, no clear path | Game admins adjust simulation parameters, constants, or unlock new capabilities |

The Virtual Ledger surfaces economic design failures with data — not silently. If Luna
simulation runs deeply negative under current EAP/consumption constants, those constants
need tuning. The AI Manager reports the problem; game admins fix the underlying design.

---

### DC Structure — Non-Profit vs Commercial

**Development Corporations (non-profit):**
- LDC, MDC, TDC, BDC, VDC — settlement establishment and ongoing operations
- Goal is settlement viability, not profit accumulation
- Surplus GCC reinvested into operations, expansion, and seeding new DCs
- LDC seeds new DCs with GCC — seed capital determines initial operating capacity
- Virtual Ledger tracks DC project affordability — gates what projects can be undertaken

**Commercial operators (for-profit):**
- AstroLift, Vector Hauling, Zenith Orbital — logistics, construction, transport
- Accumulate real GCC over time as economy matures
- Virtual Ledger settlement accepted with other NPCs but profit motive means they
  expect the overall relationship to be GCC-positive over time
- Persistent Virtual Ledger imbalance with a commercial operator is an AI Manager
  warning signal — DC not generating enough real GCC revenue

---

### Sol Market Dynamics — Emergent, Not Scripted

Each world has a distinct economic personality driven by physical characteristics.
The market determines outcomes — design doc does not prescribe them.

**Venus — gas exporter, CH4 dependent:**
- Atmospheric harvesting at scale → massive O2, N2, CO2, CO export potential
- No local CH4 source — structurally dependent on Titan route or Sabatier synthesis
- Venus DC wealthy in gas exports but vulnerable to CH4 supply chain disruption
- Venus terraforming = atmosphere removal = the waste product is the product
  CO2 removed → processed → sold across Sol → terraforming potentially self-funding
- As atmosphere thins → solar power increases → surface cools → water ice delivery viable

**Mars — terraforming demand sink:**
- Requires massive sustained imports — N2, O2, water, equipment
- All imports cost GCC — Virtual Ledger governs sustainable terraforming pace
- Mars DC must generate revenue to fund imports: minerals, CO2 processing, strategic position
- Early Mars is a net drain — AI Manager finds minimum viable pace that keeps ledger survivable
- Players accelerate terraforming by injecting GCC through contracts and resource purchases
- As atmosphere thickens → local production increases → import dependency decreases

**Venus vs Mars market interaction:**
- Mars needs gas imports → demand up → prices rise
- Venus exports gas → supply up → prices down
- They partially offset each other in Sol market
- Players positioned between both worlds profit from the flow
- Neither terraforming path is certain — Virtual Ledger and market determine what's viable

**New settlements push demand:**
- Each new settlement establishment increases resource demand across Sol
- Price signals rise → players motivated to find new sources → exploration and expansion
- First player to establish a new supply route gains competitive advantage
- AI Manager NPC settlements respond to same price signals — no scripted balance needed

**Players first — always:**
- AI Manager establishes footholds, runs operations, evaluates mega projects — all NPC activity
- Player decisions always take priority over AI Manager operations
- AI Manager creates economic conditions and opportunities — players exploit them
- As player population grows, players gradually take over roles AI Manager was filling
- AI Manager recedes to background operations as player economy matures
- Real GCC flows to players through productive activity — selling, contracting, servicing NPC needs

---

### Outstanding Design Questions Deferred to Simulation

The following questions are intentionally not answered in this document.
Supply, demand, and EAP will work them out in simulation:

- At what Luna population level does ice→CH4 synthesis become economically viable?
- Does Venus N2 accumulation justify mission cost independent of LOX self-fueling value?
- What is the CH4 vent threshold on Titan missions given Earth import as fallback?
- When does Titan N2 delivery make Earth N2 imports uneconomical?
- What is the break-even LOX production volume where Luna revenue offsets all imports?
- How fast can Mars terraforming proceed while keeping DC Virtual Ledger positive?
- Does Venus terraforming self-fund through gas sales at current EAP?

These are measurement targets for Phase 5 calibration, not design decisions.
The simulation surfaces the answers. Game admins tune constants if answers are wrong.

---

### Pre-Player Arc — Full NPC Self-Building

Players do not enter the game at Luna bootstrap. The entire Sol civilization arc from
Luna establishment through portal tech development runs as a full NPC simulation first.

**Pre-player phase scope:**
- AI Manager runs everything — Luna bootstrap through Sol expansion
- DCs establish footholds, build infrastructure, run supply chains via Virtual Ledger
- Market emerges, matures, finds equilibrium — entirely NPC driven
- Recommended fits used throughout — AI Manager running known mission profiles
- Phase 1 through Phase 8 arc may complete entirely before first player arrives
- The NPC-built economy is the world players arrive into — its quality is everything

**The Snap Event → Wormhole Tech → Portal Tech progression:**

**Natural wormhole event (the Snap):**
- Unexpected natural phenomenon — catches Sol civilization by surprise
- Forces crisis collaboration between all factions — LDC, AstroLift, Vector Hauling,
  Zenith Orbital, all DCs
- WTC (Wormhole Transit Consortium) forms from this crisis — not seeded, emerges from event
- First contact with wormhole physics — Sol must figure out what just happened

**Artificial wormhole research:**
- Snap event provides data needed to understand wormhole mechanics
- Research program begins — WTC led, DC funded via Virtual Ledger
- First artificial wormhole unstable, short range, expensive — proof of concept
- Gradual improvement — longer range, more stable, lower cost
- WormholeNavigator BFS pathfinding becomes operationally relevant as gate network emerges

**Portal tech — player entry point:**
- Mature artificial wormhole technology — stable, affordable, fast
- Gate network spans Sol — players can reach anywhere at human gameplay timescales
- Players arrive into a mature, functioning NPC economy with portal access to all of it
- Infrastructure exists, supply chains running, market prices established
- Players have immediate context, opportunity, and meaningful decisions from day one

---

### Portal Tech — Capabilities and Hard Constraints

Portal tech is precision point-to-point transport for low mass. It does not replace
bulk logistics infrastructure and never will.

**What portals are:**
- Paired quantum entanglement units — both ends must be installed and operational
- Both ends required — you cannot open a portal to a location without installed hardware
- Low mass transfer only — people, data, small high-value cargo, components
- Fast and cheap per unit for what it handles
- Intra-solar only — entanglement breaks at interstellar distances
  Sol portal network cannot connect to Alpha Centauri or any other system
- Fixed infrastructure — requires station installation at both ends

**What portals do not replace:**
- **Cyclers** — bulk cargo, large volumes, mass transport between worlds — backbone forever
- **HLTs/haulers** — bulk resources, large equipment, infrastructure components
- **Skimmers** — atmospheric harvesting, no portal substitute for that mission profile
- **Tugs** — orbital mechanics, repositioning, construction support
- **Physical supply chains** — LOX, CH4, N2, water, structural materials all move via
  haulers and cyclers regardless of portal network maturity

**Cyclers with portal tech installed:**
- A cycler can carry portal units — allows fast crew transfer and small cargo exchange
  while cycler remains in continuous transit
- Cycler never stops — portal handles people and small items, cycler keeps its orbital arc
- Effectively a moving portal waypoint — significantly increases cycler utility as
  crew transit node
- High value installation — not standard fit, strategic asset

**Terraforming boundary:**
- Cannot pipe Venus atmosphere to Mars through a portal — mass transfer limit
- Cannot move bulk gas, water, or resources through portals for terraforming purposes
- Physical haulers and cyclers remain the only terraforming logistics mechanism
- Portal tech adds a people and small cargo layer — never replaces the physical economy

**The physical economy is permanent.** Cyclers, haulers, and skimmers remain essential
infrastructure from Luna bootstrap through full Sol expansion and beyond. Portal tech
makes people fast — it does not make bulk cargo fast.
## Addendum — 2026-06-10: Skimmer Operations, Gas Economics, Virtual Ledger, and Sol Market Dynamics

---

### Skimmer Variants — Production Scope

Only two harvester variants are production-scoped for Luna MVP:

- **Venus Harvester** (`heavy_lift_transport_harvester_venus`) — `co2_splitter` x2, solar power
- **Titan Harvester** (`heavy_lift_transport_harvester_titan`) — `gas_separator` x3, RTG power

A Mars harvester variant exists in the data (`heavy_lift_transport_harvester_mars`) but is
prototype/concept status only. Do not include Mars in Luna MVP supply chain planning or AI
Manager decision logic. Mars harvester requires separate design review before any mission
scoping.

---

### Skimmer as Continuous Mobile Processing Plant

A skimmer is not a simple delivery vehicle — it is a mobile processing plant that operates
continuously whenever it has raw gas in storage and power available. Processing does not stop
at delivery. The full operational cycle:

**In transit outbound**
- Empty storage, systems nominal, en route to harvest location

**At harvest location**
- Scoops atmosphere, fills raw storage
- Processing begins immediately as storage fills
- Self-fueling from own processed output (LOX for Venus, CH4 for Titan)

**In transit inbound**
- Raw storage full or near-full
- Processing runs continuously entire return leg
- Craft arrives at Luna with partially or fully processed payload depending on transit duration
- Longer transit = more processed on arrival, less raw gas remaining to process while docked

**Docked at Luna or depot**
- Mandatory inspection, repair, and maintenance (`maintenance_interval_hours: 750-800`)
- Launch window wait — orbital mechanics, not negotiable
- Processing continues on remaining raw gas in storage during entire docked period
- Processed output delivered continuously to tank farm or depot via docking connection
- Propellant loaded for next outbound leg during this window

**Pre-launch offload — mandatory before departure**
- All raw gas transferred to depot or Luna settlement storage before window opens
- Depot processes locally if capacity available — skimmer does not need to divert to Luna
- If depot storage full → divert raw gas offload to Luna settlement network
- Craft departs with raw storage empty or near-empty — carrying unprocessed raw gas
  wastes fuel and payload capacity
- Only exception: network storage full across all available nodes — planning failure,
  not normal ops

**Launch window opens**
- Departs with raw storage cleared
- Propellant loaded, maintenance complete

**Key implication for AI Manager:**
Total gas delivered per mission = (processed on inbound transit) + (processed during docked
dwell time). Tank farm receives a continuous trickle during docked period, not a single bulk
transfer on landing. Docked dwell time is driven by mandatory inspection/repair schedule and
launch window — not by processing completion. Processing during dwell is a free operational
byproduct, not a reason to extend turnaround.

---

### Complete Operational Flow — Processing, Distribution, and Depot

Luna is the processing hub. Tankers are the distribution network. Depots are the retail nodes.

```
Skimmer harvests atmosphere
  → offloads raw gas at L1 depot (primary)
      depot processes locally → finished products in depot storage
  OR
  → offloads raw gas at Luna settlement (if depot full or unavailable)
      Luna processes (TEU/PVU, co2_splitter, gas_separator)
      → finished products in Luna tank farm
          → Tanker loads finished products from Luna tank farm
              → delivers to L1 depot / LEO depot / cycler intercept

Depot storage (finished products from either path)
  → refuels incoming craft on demand — gas station model
  → transfers to cycler intercept via tanker
  → exports to LEO depot via tanker
```

**Depot dual capability:**
- Accepts raw gas directly from skimmers — processes locally, skimmer never needs to
  divert to Luna surface if depot has capacity
- Accepts finished products from Luna tankers — stores and distributes
- Depot processing is an offload relief valve and direct skimmer service capability —
  not a replacement for Luna's industrial processing backbone

**Tanker role — finished product distribution only:**
- No processing modules — pure bulk transport of processed gases
- Moves finished products between Luna tank farm, depots, and cycler intercepts
- Cycler intercept capability — tanker matches velocity, transfers finished product,
  cycler never stops
- Stores: LOX, CH4, N2, He3 — all finished products, nothing raw

**Offload routing hierarchy:**
1. L1 depot direct — skimmer offloads raw gas, depot processes locally
2. Luna settlement network — if depot full or unavailable, divert to Luna surface
3. Never depart with offloadable raw gas — always clear raw storage before window

**Capacity grows over time:**
- Early game — single Luna settlement, L1 depot under construction — offload constrained
- Mid game — multiple Luna settlements, L1 depot online — offload comfortable
- Late game — full Luna network, multiple depots — offload never a bottleneck
- Each new Luna settlement adds raw gas storage and processing capacity to the network

---

### Turnaround Dependencies

Turnaround is mandatory. AI Manager must ensure these are in place before skimmer arrives:

- **Maintenance parts and consumables** — if parts not on Luna, skimmer misses its window
- **Correct propellant pre-positioned** — Venus needs CH4, Titan needs LOX
- **Tank farm capacity** — must have room for continuous processed gas output during dwell
- **Repair capability** — inspection and repair must complete before window opens

Missing any of these is a window miss. A missed window costs a full orbital cycle.

---

### Venus Harvester — Value Proposition

**Module config:** `co2_splitter` x2, `solar_array` x6
**Power:** Solar — viable and efficient at Venus distance, no RTG cost
**Output rates:** O2 200 kg/hr, N2 100 kg/hr, CO currently vented
**Self-fuels:** LOX from own O2 output — arrives at Luna with oxidizer tanks refilled
**Needs on turnaround:** CH4 only

**Primary mission justification:**
- LOX self-fueling economy — every Venus trip reduces Luna LOX expenditure on outbound missions
- CO2 feedstock for Luna processing chain
- Shorter transit than Titan — faster cycle time, more trips per year
- Solar power — lower build and operating cost than RTG variants

**CO output — pending design decision:**
Currently `"vent": ["carbon_monoxide"]` in gas handling policy. If Luna metal processing
chain can use CO as a reducing agent for oxide reduction, vent policy should change to store.
CO value at Luna should be assessed before finalizing Venus gas handling policy. This is a
potentially recoverable revenue stream currently being thrown away.

**N2 note:** Venus delivers modest N2 per trip (100 kg/hr). Venus N2 is supplementary
accumulation, not bulk supply. Titan is the primary N2 source by volume (760 kg/hr).

**Critical dependency:** Venus has no CH4 source. All Venus skimmer operations and Venus
settlement propulsion requires CH4 imports. Venus DC is structurally dependent on whoever
controls the CH4 supply chain until local Sabatier synthesis becomes viable.

---

### Titan Harvester — Value Proposition

**Module config:** `gas_separator` x3, `rtg_power_unit` x1
**Power:** RTG only — no solar viable at Titan distance. Power-constrained operation.
**Output rates:** N2 760 kg/hr, CH4 140 kg/hr
**Self-fuels:** CH4 from own output — arrives at Luna with methane tanks refilled
**Needs on turnaround:** LOX only — no O2 source at Titan, Luna ISRU is the critical path

**Primary mission justification:**
- N2 bulk delivery — 760 kg/hr, 7.6x Venus N2 rate. Titan is the primary N2 source.
- CH4 delivery — path to breaking Earth CH4 import dependency
- Titan arrival is the economic independence event for both N2 and CH4

**Power constraint implications:**
- RTG limits processing rate — gas_separator x3 running on constrained power budget
- Long transit partially offsets this — more processing time on inbound leg
- Storage fills faster than processing can convert — raw gas accumulates in cryogenic storage
- Craft makes strategic storage allocation: prioritize CH4 or N2 based on AI Manager mission mode

---

### Per-Mission Gas Handling Policy

The `gas_handling_policy` in craft operational data is not static — it is a **per-mission
configuration** the AI Manager sets at dispatch time based on Luna inventory projections
at estimated arrival.

**Titan storage tradeoff:**
Titan atmosphere is 95% N2, 5% CH4. Storage fills rapidly with N2. To accumulate more CH4
the craft must vent N2 to make room — but venting is not free:
- **Power cost** — separator ran to produce gas now being vented
- **Time cost** — harvest time spent on gas not kept
- **Module wear** — processing hours consumed for zero delivered value

**The lazy default is no venting** — deliver natural atmospheric composition, let Luna tank
farm sort it out. Only vent when value differential and inventory need clearly justify the
waste. If Luna is flush with CH4, venting N2 to chase CH4 is actively harmful.

**Vent decisions require inventory projections at arrival, not just current state.**
AI Manager must project what Luna will need at estimated arrival time — current inventory
minus consumption over transit duration — before setting mission vent policy.

---

### CH4 Sources — MVP Scope

CH4 sourcing for Luna MVP in order of availability:

1. **Earth direct import (AstroLift)** — expensive bridge, always available
2. **Titan skimmer** — bulk delivery, long cycle, primary long-term source
3. **Venus CO/CO2 processing** — limited yield, byproduct of existing Venus mission only

**Future seam — not MVP scope:**
Luna polar ice → H2 electrolysis + Venus CO2 → Sabatier CH4 synthesis
(CO2 + 4H2 → CH4 + 2H2O). Architecturally sound, worth preserving as a future seam,
but ice mining infrastructure is not part of initial base setup. Ice extraction develops
as a parallel Luna surface track during Phase 6/7 as the industrial base matures — but
only after biological water demands (drinking, food production, sanitation) are fully met.
The market determines when surplus ice makes synthesis economically viable vs Titan imports.

**Saturn H2 (Phase 9+):**
Gas giant harvesting delivers essentially unlimited H2. Saturn H2 + Venus CO2 → Sabatier
synthesis makes Venus CH4 independent. Luna ice→H2 pathway becomes less critical.
Titan value shifts fully toward N2 primary mission. This reshapes the entire Sol CH4
economy — but it is a Phase 9+ event, not current architecture.

---

### Source Selection is Market-Determined

**Do not hardcode source priority tables.** The AI Manager evaluates source selection at
runtime against current EAP-anchored costs, current inventory, and projected demand.
The market determines the right answer — not design doc prescriptions.

What changes over time:
- New engine technology → transit times drop → cycle frequency increases → supply increases
- Cyclers arrive → bulk cargo economics change → per-unit delivery cost drops
- New settlements online → demand side grows → price signals shift
- Better processing modules → extraction efficiency improves → local production costs drop
- Population grows → biological demand increases → ice allocation economics shift
- Saturn settlement → unlimited H2 → CH4 and propellant economics restructure entirely

The AI Manager re-evaluates continuously. A decision correct at Luna Year 1 may be wrong
at Luna Year 5. No fixed priority survives a dynamic economy.

---

### Virtual Ledger — Purpose and Function

The Virtual Ledger is an NPC-only mechanism. Players always transact in real GCC.

**What the Virtual Ledger is:**
- The AI Manager's diagnostic instrument — the P&L statement per NPC settlement
- Tracks NPC economic health: revenue vs costs, project affordability, inter-NPC balances
- Primary purpose: tell the AI Manager what is working and what isn't

**What the Virtual Ledger is not:**
- A substitute for real GCC in player transactions — never
- A permanent credit facility — sustained negative balance is a failure signal, not normal ops

**NPC transaction rules:**
- NPC-to-NPC → real GCC preferred, Virtual Ledger as fallback when GCC insufficient
- All NPCs (DCs and commercial operators alike) can settle via Virtual Ledger with other NPCs
- NPC-to-Player → real GCC only, no exceptions
- Player-to-NPC → real GCC only, no exceptions

**Self-funding NPC loops:**
When NPC commercial operators transact within a local economy, real GCC may never need
to move. Example — AstroLift Luna operation:
- Delivers imports → sells to LDC → receives GCC credit
- Buys LOX from LDC for return trip → pays GCC debit
- Net balance settles via Virtual Ledger — real GCC only moves if there is a net surplus
  or deficit that can't be internally resolved

This is intentional. The Virtual Ledger lubricates NPC bootstrapping before real GCC
volume is sufficient. Real GCC's primary purpose is getting currency into players' hands
through productive economic activity — selling resources, taking contracts, providing
services the NPC economy needs.

**AI Manager response tiers based on ledger health:**

| Ledger State | AI Manager Response |
|---|---|
| Positive and stable | Continue operations, evaluate expansion opportunities |
| Declining trend | Adjust strategy autonomously — shift priorities, cut non-essential projects |
| Deeply negative, recoverable | Escalate to game admins with ledger data and diagnosis |
| Deeply negative, no clear path | Game admins adjust simulation parameters, constants, or unlock new capabilities |

The Virtual Ledger surfaces economic design failures with data — not silently. If Luna
simulation runs deeply negative under current EAP/consumption constants, those constants
need tuning. The AI Manager reports the problem; game admins fix the underlying design.

---

### DC Structure — Non-Profit vs Commercial

**Development Corporations (non-profit):**
- LDC, MDC, TDC, BDC, VDC — settlement establishment and ongoing operations
- Goal is settlement viability, not profit accumulation
- Surplus GCC reinvested into operations, expansion, and seeding new DCs
- LDC seeds new DCs with GCC — seed capital determines initial operating capacity
- Virtual Ledger tracks DC project affordability — gates what projects can be undertaken

**Commercial operators (for-profit):**
- AstroLift, Vector Hauling, Zenith Orbital — logistics, construction, transport
- Accumulate real GCC over time as economy matures
- Virtual Ledger settlement accepted with other NPCs but profit motive means they
  expect the overall relationship to be GCC-positive over time
- Persistent Virtual Ledger imbalance with a commercial operator is an AI Manager
  warning signal — DC not generating enough real GCC revenue

---

### Sol Market Dynamics — Emergent, Not Scripted

Each world has a distinct economic personality driven by physical characteristics.
The market determines outcomes — design doc does not prescribe them.

**Venus — gas exporter, CH4 dependent:**
- Atmospheric harvesting at scale → massive O2, N2, CO2, CO export potential
- No local CH4 source — structurally dependent on Titan route or Sabatier synthesis
- Venus DC wealthy in gas exports but vulnerable to CH4 supply chain disruption
- Venus terraforming = atmosphere removal = the waste product is the product
  CO2 removed → processed → sold across Sol → terraforming potentially self-funding
- As atmosphere thins → solar power increases → surface cools → water ice delivery viable

**Mars — terraforming demand sink:**
- Requires massive sustained imports — N2, O2, water, equipment
- All imports cost GCC — Virtual Ledger governs sustainable terraforming pace
- Mars DC must generate revenue to fund imports: minerals, CO2 processing, strategic position
- Early Mars is a net drain — AI Manager finds minimum viable pace that keeps ledger survivable
- Players accelerate terraforming by injecting GCC through contracts and resource purchases
- As atmosphere thickens → local production increases → import dependency decreases

**Venus vs Mars market interaction:**
- Mars needs gas imports → demand up → prices rise
- Venus exports gas → supply up → prices down
- They partially offset each other in Sol market
- Players positioned between both worlds profit from the flow
- Neither terraforming path is certain — Virtual Ledger and market determine what's viable

**New settlements push demand:**
- Each new settlement establishment increases resource demand across Sol
- Price signals rise → players motivated to find new sources → exploration and expansion
- First player to establish a new supply route gains competitive advantage
- AI Manager NPC settlements respond to same price signals — no scripted balance needed

**Players first — always:**
- AI Manager establishes footholds, runs operations, evaluates mega projects — all NPC activity
- Player decisions always take priority over AI Manager operations
- AI Manager creates economic conditions and opportunities — players exploit them
- As player population grows, players gradually take over roles AI Manager was filling
- AI Manager recedes to background operations as player economy matures
- Real GCC flows to players through productive activity — selling, contracting, servicing NPC needs

---

### Outstanding Design Questions Deferred to Simulation

The following questions are intentionally not answered in this document.
Supply, demand, and EAP will work them out in simulation:

- At what Luna population level does ice→CH4 synthesis become economically viable?
- Does Venus N2 accumulation justify mission cost independent of LOX self-fueling value?
- What is the CH4 vent threshold on Titan missions given Earth import as fallback?
- When does Titan N2 delivery make Earth N2 imports uneconomical?
- What is the break-even LOX production volume where Luna revenue offsets all imports?
- How fast can Mars terraforming proceed while keeping DC Virtual Ledger positive?
- Does Venus terraforming self-fund through gas sales at current EAP?

These are measurement targets for Phase 5 calibration, not design decisions.
The simulation surfaces the answers. Game admins tune constants if answers are wrong.

---

### Pre-Player Arc — Full NPC Self-Building

Players do not enter the game at Luna bootstrap. The entire Sol civilization arc from
Luna establishment through portal tech development runs as a full NPC simulation first.

**Pre-player phase scope:**
- AI Manager runs everything — Luna bootstrap through Sol expansion
- DCs establish footholds, build infrastructure, run supply chains via Virtual Ledger
- Market emerges, matures, finds equilibrium — entirely NPC driven
- Recommended fits used throughout — AI Manager running known mission profiles
- Phase 1 through Phase 8 arc may complete entirely before first player arrives
- The NPC-built economy is the world players arrive into — its quality is everything

**The Snap Event → Wormhole Tech → Portal Tech progression:**

**Natural wormhole event (the Snap):**
- Unexpected natural phenomenon — catches Sol civilization by surprise
- Forces crisis collaboration between all factions — LDC, AstroLift, Vector Hauling,
  Zenith Orbital, all DCs
- WTC (Wormhole Transit Consortium) forms from this crisis — not seeded, emerges from event
- First contact with wormhole physics — Sol must figure out what just happened

**Artificial wormhole research:**
- Snap event provides data needed to understand wormhole mechanics
- Research program begins — WTC led, DC funded via Virtual Ledger
- First artificial wormhole unstable, short range, expensive — proof of concept
- Gradual improvement — longer range, more stable, lower cost
- WormholeNavigator BFS pathfinding becomes operationally relevant as gate network emerges

**Portal tech — player entry point:**
- Mature artificial wormhole technology — stable, affordable, fast
- Gate network spans Sol — players can reach anywhere at human gameplay timescales
- Players arrive into a mature, functioning NPC economy with portal access to all of it
- Infrastructure exists, supply chains running, market prices established
- Players have immediate context, opportunity, and meaningful decisions from day one

---

### Portal Tech — Capabilities and Hard Constraints

Portal tech is precision point-to-point transport for low mass. It does not replace
bulk logistics infrastructure and never will.

**What portals are:**
- Paired quantum entanglement units — both ends must be installed and operational
- Both ends required — you cannot open a portal to a location without installed hardware
- Low mass transfer only — people, data, small high-value cargo, components
- Fast and cheap per unit for what it handles
- Intra-solar only — entanglement breaks at interstellar distances
  Sol portal network cannot connect to Alpha Centauri or any other system
- Fixed infrastructure — requires station installation at both ends

**What portals do not replace:**
- **Cyclers** — bulk cargo, large volumes, mass transport between worlds — backbone forever
- **HLTs/haulers** — bulk resources, large equipment, infrastructure components
- **Skimmers** — atmospheric harvesting, no portal substitute for that mission profile
- **Tugs** — orbital mechanics, repositioning, construction support
- **Physical supply chains** — LOX, CH4, N2, water, structural materials all move via
  haulers and cyclers regardless of portal network maturity

**Cyclers with portal tech installed:**
- A cycler can carry portal units — allows fast crew transfer and small cargo exchange
  while cycler remains in continuous transit
- Cycler never stops — portal handles people and small items, cycler keeps its orbital arc
- Effectively a moving portal waypoint — significantly increases cycler utility as
  crew transit node
- High value installation — not standard fit, strategic asset

**Terraforming boundary:**
- Cannot pipe Venus atmosphere to Mars through a portal — mass transfer limit
- Cannot move bulk gas, water, or resources through portals for terraforming purposes
- Physical haulers and cyclers remain the only terraforming logistics mechanism
- Portal tech adds a people and small cargo layer — never replaces the physical economy

**The physical economy is permanent.** Cyclers, haulers, and skimmers remain essential
infrastructure from Luna bootstrap through full Sol expansion and beyond. Portal tech
makes people fast — it does not make bulk cargo fast.
