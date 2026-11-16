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
