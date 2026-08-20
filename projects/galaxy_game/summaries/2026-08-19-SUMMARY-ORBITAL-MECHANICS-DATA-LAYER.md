# Orbital Mechanics Data Layer — Session Report for Claude

**Date:** 2026-08-19  
**Sessions covered:** Claude (pushback on Qwen research), Gemini (Venus skimmer logistics)  
**Task file:** `2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md`

---

## Research Methodology & Key Finding

**This document is research into the CURRENT STATE, not a status report of completed work.**

Research approach:
1. **Code examination** — Read source files to verify implementation exists
2. **Data verification** — Parsed JSON files to confirm actual data coverage
3. **Test coverage check** — Listed RSpec spec files but DID NOT execute tests (this is research only)
4. **Design gap analysis** — Identified missing pieces (epochs, incomplete data coverage)

**Key finding:** The orbital mechanics data layer is **partially implemented** with **significant gaps and untested code**:
- Code exists but has never been run via RSpec
- JSON data coverage is sparse (6/49 bodies in sol-complete.json, 4/8 in sol.json)
- Critical design decision (epoch convention) is unresolved
- Phase 5 (TransitEngine) cannot start until epoch is designed

This is NOT a completion summary. It's a research report documenting what exists, what's untested, what's missing, and what needs design decisions.

---

## CRITICAL FINDING: Boil-off Data Exists But Is Not Consumed by Settlement OR Craft Systems

**The bottleneck for Luna base economics:** Storage blueprints define boil-off rates (e.g., 0.3% per day for methane), but **zero code applies these losses**—neither to settlement inventory nor to craft cargo during transit. 

**Design intent for Luna volatiles:** Luna is carbon-poor and lacks N₂, CH₄, CO₂—critical for atmosphere, fuel, and ISRU synthesis. Early-game strategy: import these from Venus/Titan atmospheres:
- Venus atmosphere: Rich in CO₂, traces of N₂ → harvest and import to Luna
- Titan atmosphere: N₂-rich → harvest and import to Luna
- Addresses Luna's volatiles deficit without requiring local production

**The unaccounted-for consequence:** Early designs did NOT account for boil-off during multi-month transits:
- Venus→Luna (5–6 months): 45–54% loss if cooling fails; 9–16% baseline loss with active cooling
- Titan→Luna (6–8+ months): 54–72% loss if cooling fails; 15–24% baseline loss with active cooling

**If boil-off enforcement makes Venus/Titan imports uneconomical:**
- Luna falls back to Earth imports (only viable source)
- Earth→Luna transit: ~3 days, ~1% boil-off (tolerable)
- **But Earth's deep gravity well makes exports expensive**
- Luna's shallow gravity well (1/6 Earth) makes re-export cheap
- Creates economic incentive: import to Luna once, use Luna as export hub

| Import Source | Transit Time | Boil-off Loss | Launch Cost | Economics | Bottleneck |
|---|---|---|---|---|---|
| Venus atmosphere | 5–6 months | 9–16% | Low (shallow well) | Marginal/unviable | Multi-month decay |
| Titan atmosphere | 6–8+ months | 15–24% | Low (shallow well) | Unviable | Multi-month decay |
| Earth (fallback) | 3 days | ~1% | HIGH (deep well) | Viable but expensive | Earth production + launch capacity |
| Local production | N/A | 0% | Low from Luna | Most resilient | Luna processing power |

**Strategic implication:** If boil-off is enforced, the volatiles supply chain becomes:
- Early-game: Earth launches (expensive gravity well), sends to Luna (cheap 3-day transit)
- Luna becomes volatiles hub: Low gravity efficient for re-export to Mars, cyclers, other bases
- Mid-game: Venus local skimming → Venus orbit depot (short hop, stays local)
- Late-game: Venus→Mars cycler (mk2 cooling, 5–8% loss tolerable for cycler fuel)
- End-game: Titan harvesting viable only with mk3 cooling + wormhole speed

**Earth's gravity well as game mechanic:**
- Makes Earth expensive to launch from (delta-v penalty)
- Makes Luna the ideal volatiles redistribution hub
- Justifies why Luna base is economically central (efficient re-export point)
- Explains why local Luna production reduces system vulnerability (no Earth supply line needed)

**Critical design constraint: Methane sourcing**

Original design used multi-purpose cryo tanks to import N₂ from Earth → lava tube storage farm. But HLT uses methane as fuel, which requires separate resupply:

| Methane Source | Transit Time | Boil-off Loss | Viability | Constraint |
|---|---|---|---|---|
| Earth methane tankers | 3 days | ~1% | Viable but expensive | Gravity well cost for each resupply |
| Titan methane | 6–8+ months | 15–24% | Uneconomical | Loss exceeds value; late-game only with mk3 |
| Venus CO₂ → synthesize | 5–6 months | 9–16% | Marginal | Boil-off + multi-month supply chain |
| Local synthesis (H from PSR + CO₂) | N/A | 0% | Best option but constrained | **CO₂ competes with greenhouse food production** |

**New constraint: Competing CO₂ demand**

Settlers produce CO₂ from respiration. That CO₂ can theoretically be converted to methane via Sabatier reaction (CO₂ + H → CH₄ + H₂O). BUT:
- Greenhouses need CO₂ for food production
- Each ton of CO₂ sent to Sabatier reactor is NOT available for crops
- Luna's food production cannot be sacrificed for fuel

**Therefore:** Local synthesis from settler CO₂ is possible but severely limited:
- Excess CO₂ only (after greenhouse demand satisfied)
- Probably insufficient to fuel HLT operations
- Methane tankers from Earth remain primary fuel source

**Revised methane sourcing reality:**

| Path | Primary Source | Supplementary | Trade-off |
|---|---|---|---|
| **Path A: Earth-dependent** | Earth CH₄ tankers (1% loss, continuous) | Settler CO₂ overflow (minimal) | Reliable; costs gravity well fee indefinitely |
| **Path B: Venus import** | Venus CO₂ (9–16% loss at mk1) | Settler CO₂ overflow (minimal) | Reduces Earth dependency; expensive boil-off |
| **Path C: PSR + Venus** | Venus CO₂ (with mk2 storage) + PSR H₂ | Settler CO₂ overflow (minimal) | Most self-sufficient; requires both techs |

**Key insight:** Settler respiration CO₂ cannot solve Luna's methane deficit because food production always takes priority. Earth supply (or distant imports with better cooling) remains essential.

---

## Alternative Design: Hydrolox Propulsion

**Problem statement:** If HLT uses methane (CH₄), it requires either:
1. Continuous Earth methane imports (gravity well cost)
2. Venus/Titan CO₂ or CH₄ imports (9–24% boil-off)
3. Local synthesis from settler CO₂ (insufficient for fuel needs)

**Alternative solution:** Switch HLT to hydrolox engines (H₂/LOX)
- H₂ and O₂ both produced from PSR ice via water electrolysis
- Zero imported propellant needed from Earth/Venus/Titan
- Eliminates methane sourcing problem entirely
- ISP 450 vacuum (highest chemical efficiency) vs. methane ~365

**New constraint: LH2 boil-off is WORSE than methane**
- Methane boils at -161°C
- Liquid hydrogen boils at -253°C (far colder)
- LH2 boil-off rates exceed methane significantly
- Requires MORE advanced cooling (mk2+ for viable long-term storage)

**Trade-off analysis:**

| Propellant | Fuel Source | Sourcing Problem | Boil-off Challenge | Cooling Requirement |
|---|---|---|---|---|
| Methane (current) | Earth/Venus/Titan | Import logistics + gravity well cost | Moderate (0.3% mk1) | mk1 viable for short-term |
| Hydrolox | Local PSR H₂ + O₂ | None (fully local) | SEVERE (~0.5–0.7% mk1) | mk2+ REQUIRED for storage |

**Hydrolox path implications:**
- Removes all import dependencies for propellant
- Makes PSR ice mining CRITICAL (not optional)
- Requires advanced cooling much earlier than methane path
- mk1 cooling insufficient; must unlock mk2 storage earlier
- Total system reliability depends on PSR ice extraction capacity
- Power requirements for LH2/LOX production + cooling higher than methane alternative

**Design decision:** Methane (import-dependent, easier cooling) vs. Hydrolox (self-sufficient, harder cooling). Each path changes the early-game bottleneck.

---

## Resolution: The Skimmer Progression Gate

**The elegant design cycle:**
1. **Early-game (mk1 era):** Luna base only, no off-world infrastructure
   - Earth methane tankers resupply Luna base (1% loss, 3-day transit)
   - Earth N₂ tankers for life support via lava tube storage
   - Gravity well cost expensive but unavoidable
   - Focus: establishing Luna settlement, PSR mining, local production
   - Zero depot/station infrastructure elsewhere

2. **Mid-game (mk2 unlock):** First depot + skimmer network at Venus
   - Build Venus station + depot + mk2 cooled storage
   - Venus skimmers harvest CO₂ in repeating cycles
   - Depot collects, batches, prepares for cycler pickup
   - Venus-Luna cycler begins carrying pre-cooled cargo
   - Reduces Earth dependency for carbon/CO₂ supply

3. **Late-game (mk3 unlock):** Regional cycler + depot + skimmer network
   - Multiple depots: Venus, Titan, Mars, Luna
   - Skimmers operating at each location
   - Cycler routes: Venus→Luna, Titan→Luna, Luna→Mars
   - Mature infrastructure, Earth supply becomes emergency backup
   - Network effects fully realized

**Why early game has no skimmers:**
- Skimmers need orbits to operate from (Venus/Titan atmosphere + orbital station)
- Stations + depots are mid-game infrastructure unlocks
- Early game focused on Luna base establishment only
- Earth imports bridge the gap until infrastructure can be built elsewhere

**Boil-off enforcement creates natural tech gates:**
- mk1 cooling: Only viable for 3-day Earth transits, not 5–8 month Venus/Titan routes
- mk2 cooling: Opens Venus as viable supply source (9–10% loss acceptable)
- mk3 cooling: Opens Titan as viable source (15–18% loss acceptable)
- Skimmers were always the intended solution; boil-off determines when players unlock them

**Full system architecture: Depots → Cyclers → Tankers**

Evolution from early skimmers to mature system:

| Phase | Infrastructure | Gas Movement | Economics |
|---|---|---|---|
| Early (mk1) | None; direct Earth import | Point-to-point tankers from Earth | Expensive gravity well; immediate delivery |
| Mid (mk2) | Station + depot at Venus/Titan | Skimmers harvest → short hop to depot → depot processing | Short transit = minimal boil-off; depot storage optimized |
| Late (mk3) | Regional depot network | Skimmers repeat cycle; cyclers collect processed cargo | Optimized cycler loops; depot cooling absorbs long-term storage |

**Skimmer operation cycle:**
1. Skimmer enters planet atmosphere (Venus CO₂, Titan CH₄/N₂)
2. Harvests volatiles into cargo tank
3. Short transit to orbital depot/station (minutes to hours, not days)
4. Unloads at depot storage (mk2+ cooling tanks)
5. Repeats cycle immediately

**Depot infrastructure role:**
- Collects frequent small harvests from skimmer repeating cycles
- Provides optimized long-term storage with mk2/mk3 cooling
- Batches cargo into cycler attachment points
- Handles processing/compression before cycler transport

**Cycler integration:**
- Venus-Luna cycler arrives at Venus depot on its schedule
- Collects pre-processed/pre-cooled cargo
- Transports to Luna on permanent orbit route
- Luna depot receives, redistributes, or re-attaches to Luna-Mars cycler
- Minimal per-cycle boil-off because cargo is pre-optimized

**Boil-off economics with this architecture:**
- Skimmer→depot hop: ~0% loss (seconds to minutes, already pressurized atmosphere)
- Depot storage between harvests: Minimal (short intervals between cycles)
- Depot→cycler transfer: Pre-cooled cargo in optimized tanker
- Cycler transport: Shared infrastructure spreads boil-off cost across multiple cargo units
- Total system loss: Dominated by depot's mk2+ cooling efficiency, not transport

**Why this solves the boil-off problem:**
- Skimmers harvest locally with repeating short cycles
- Depot becomes buffer/accumulator/optimizer
- Long-distance travel only happens via cooled cycler infrastructure
- Boil-off enforcement makes the depot+cycler combination necessary and valuable
- Without it, simple tanker runs would be more economical than building depot infrastructure

**The design issue resolution:** Boil-off enforcement forces players to build depot infrastructure and cycler networks instead of simple tanker runs. This creates emergent gameplay around supply chain optimization.

## 1. Claude's Pushback Questions — Status

### Question 1: Does every Sol body have `orbital_elements`?
**Status: ⚠️ PARTIAL — Only 6 out of 49 bodies have orbital_elements**

**sol-complete.json:**
- Total bodies: 49
- With orbital_elements: **6** (Venus, Earth, Mars, Jupiter, Luna, Titan)
- Without orbital_elements: **43** (Mercury, Saturn, Uranus, Neptune, all major moons: Ganymede, Callisto, Io, Europa, Rhea, Iapetus, Dione, Tethys, Enceladus, Mimas, Titania, Oberon, Umbriel, Ariel, Miranda, Triton, Nereid, and dwarf planets: Pluto, Ceres, Eris, Haumea, Makemake, and dozens of minor moons)

**sol.json (primary reference):**
- Total bodies: 8
- With orbital_elements: **4** (Venus, Earth, Mars, Jupiter)
- Without orbital_elements: **4** (Mercury, Saturn, Uranus, Neptune)

**What's actually present:**

| Body | sol-complete.json | sol.json | semi_major_axis (m) | eccentricity | inclination (°) | mean_anomaly (°) |
|------|-------------------|----------|---------------------|--------------|-----------------|------------------|
| Venus | ✓ | ✓ | 108208000000.0 | 0.0068 | 3.39 | 50.12 |
| Earth | ✓ | ✓ | 149597870700.0 | 0.0167 | 0.0 | 357.52 |
| Mars | ✓ | ✓ | 227943800000.0 | 0.0934 | 1.85 | 19.38 |
| Jupiter | ✓ | ✓ | 778600000.0 | 0.0489 | 1.304 | 20.02 |
| Luna | ✓ | — | 384400000.0 | 0.0549 | 5.14 | 115.34 |
| Titan | ✓ | — | 1221870000.0 | 0.0288 | 0.33 | 0.0 |

**Research finding:** The known data layer is extremely sparse. Only 12% of sol-complete.json bodies (6/49) and 50% of sol.json bodies (4/8) have orbital_elements. The three-layer design (known → procedural → survey) was meant to handle this, but it means the "known" layer is mostly empty.

### Question 2: Does JSON data get loaded into CelestialBody at runtime?
**Status: ⚠️ CODE PRESENT, NEVER TESTED AT RUNTIME**

Phase 2 made three changes:
1. **Removed `:orbital_elements` from exclusion list** in `system_builder_service.rb:255` — confirmed via file read, the field is no longer in `special_keys_to_exclude`
2. **Added migration** `20260819120107_add_orbital_elements_to_celestial_bodies.rb` — adds JSONB column with default `{}`
3. **Added `store_accessor :orbital_elements, :semi_major_axis, :eccentricity, :inclination, :mean_anomaly`** in CelestialBody model

**Test coverage:**
- RSpec files in `spec/services/star_sim/` include: `system_generator_service_spec.rb`, `data_driven_generation_spec.rb`, and 4 others
- **None of these files contain tests verifying that orbital_elements actually load from JSON into the database**
- No test for: "Given a system build with sol-complete.json, does Venus.orbital_elements persist?"

**Research finding:** Phase 2 code exists and looks correct, but the actual runtime behavior has never been verified with RSpec. The data flow assumption (JSON loads → persists to DB → accessible via store_accessor) is untested.

### Question 3: Where exactly on the model?
**Status: ✅ REAL JSONB COLUMN + store_accessor**

- Column: `celestial_bodies.orbital_elements` (JSONB, default `{}`, null: false)
- Accessors via `store_accessor`: `semi_major_axis`, `eccentricity`, `inclination`, `mean_anomaly`
- These are **not** in the `properties` JSONB blob — they're a separate column

### Question 4: Is `mean_anomaly` tied to a known reference epoch?
**Status: ❌ UNANSWERED — BLOCKING for transit calculations**

The `mean_anomaly` values in sol-complete.json (e.g., Jupiter's `20.02`) are **angles at an epoch**, not absolute positions. Without knowing the reference epoch (the date/time these angles were measured), they cannot be propagated forward to compute "today's" position.

**Current state:** No epoch documentation found in:
- JSON files (no comments, no `epoch` field)
- Loaders (SystemBuilderService has no epoch handling)
- READMEs or design docs

**Impact:** TransitEngine can read `mean_anomaly` but cannot compute current phase angles without an epoch. This is a **design gap**, not a code bug — the data structure needs an `epoch` field added to `orbital_elements`.

---

## 2. Gemini Chat Findings

### Design Decisions Confirmed
1. **Venus-to-Luna skimmer route** — full round-trip logistics loop (Earth→Venus→Luna)
2. **Docked craft capacity pooling** — settlement should sum docked craft → craft's units into available storage
3. **Fuel bootstrapping sequence:** Earth-delivered methane initially → Luna PSR ice processing later
4. **HLT tanker stays docked** at settlement as LOX buffer for L1 depot transfers
5. **Titan loop economics** — late-game play, not worth it early due to boil-off/distance

### Gemini's Key Questions (from Claude's follow-up)

#### Question 5: Does the settlement-level method that sums docked-craft capacity exist?
**Status: ⏳ USER SAID "IT SHOULD ALREADY EXIST" — NOT VERIFIED**

Evidence found:
- `Settlement::BaseSettlement` has `has_many :docked_crafts` (line 24)
- Documentation (`skimmer_craft_intent.md`) states: *"Skimmer cargo tanks become temporary base storage while docked, increasing total depot capacity"*
- Documentation (`modular_refinery_integration.md`) shows formula: `Total Throughput = Base_Depot_Capacity + (Sum(Docked_Skimmer_Capacity) * Efficiency_Modifier)`

**Gap:** The documentation describes the design intent, but no actual code method was found that sums docked craft capacity into settlement inventory checks. The association exists (`docked_crafts`), but the pooling logic may be missing or incomplete.

#### Question 6: Does it recurse through craft → craft's units?
**Status: ❓ UNKNOWN — NEEDS CODE REVIEW**

If docked craft capacity pooling exists, does it walk:
- `settlement.inventory.total_capacity` → `docked_crafts.sum(&:max_cargo_mass)` → `craft.units.sum(&:storage_capacity)`?

Or does it stop at the craft level only? If it stops short, LOX capacity would be undercounted once the HLT starts filling up.

#### Question 7: Does the HLT craft operational data define a boil-off/loss coefficient?
**Status: ⚠️ COOLING ARCHITECTURE EXISTS IN BLUEPRINTS, BUT CODE DOESN'T ENFORCE IT**

**Known fact:** Starship-class HLT cannot carry cryogenic fuel across multi-month transits (Venus 5–6 months, Titan 6–8+ months) without active cooling. Uncontrolled boil-off = mission failure.

**Existing cooling architecture:**
- `cryogenic_methane_tank_bp.json`: 2 kW active cooling → 0.3% per day baseline
- `methane_storage_tank_mk1_data.json`: 8 kW active_cooling stage (vs 1.5 kW standby)
- `cryogenic_storage_unit_bp.json`: Designed with thermal processing and power requirements
- HLT operational fit: **5 cryogenic_storage_unit + 1 lox_tank + 1 methane_tank** carrying 150,000 kg cargo

**Design progression (mk{num} scaling):**
- **mk1 tanks:** 0.3% per day baseline (current spec)
- **mk2 tanks:** ~0.15-0.2% per day (improved insulation, more efficient cooling)
- **mk3+ tanks:** ~0.05-0.1% per day (advanced cryogenic tech, passive thermal management)
- **Progression impact:** Early-game Venus/Titan routes marginal with mk1 cooling; become viable as mk2/mk3 tech reduces boil-off

**Technology gates route availability:**
- **Early game (mk1):** Venus local operations, Luna transfers → short transits tolerate 9–15% loss; depot-hopping acceptable
- **Mid-game (mk2):** mk2 storage (~0.15% per day, ~5–8% loss over 5–6 months) → Venus→Mars cycler tanker becomes viable; intercycler freight becomes profitable
- **Late-game (mk3+):** mk3 storage (~0.05% per day, ~2–4% loss) → Titan expeditions viable; advanced tech reduces cooling power needs
- **Wormhole era:** Speed-of-light travel + advanced_storage = minimal cumulative loss; distant routes routine

**Player choice within tech tiers:**
- Depot-hopping Venus → can stay on mk1, losses acceptable for short hops
- Intercycler tanker Venus→Mars → MUST upgrade to mk2 or mission fails economically
- Titan long-haul (10+ month journey) → Needs mk3 even with wormhole tech speed boost
- All tech tiers kept available: mk1 useful for low-cost local ops, advanced storage for premium long-haul

**Power allocation and cooling efficiency:**
- HLT total budget: **150–155 kW**
- Cooling demand (mk1): ~40 kW (5 tanks × 8 kW active cooling)
- Remaining for propulsion/nav/life support: ~110 kW
- **mk{num} progression may reduce cooling power needs:** mk2 could need only 5–6 kW per tank (~30 kW total); mk3 could need 3–4 kW per tank (~20 kW), freeing power for wormhole drive or faster transit

**The actual gap (not architecture, but enforcement):**

1. **Boil-off losses not applied during transit** — Code doesn't reduce craft cargo/fuel inventory by loss rate per day
2. **Power allocation not tracked** — Code doesn't verify craft has sufficient power budgeted for cooling during journey
3. **mk{num} scaling not implemented** — All tanks treated as identical; no loss rate improvement or power requirement scaling with advanced blueprints
4. **Loss acceleration not enforced** — If cooling fails/powers down, boil-off doesn't accelerate
5. **Mission planning doesn't account for cargo loss** — Arrival manifest assumed equal to departure manifest
6. **Technology gates not enforced** — Players can't see which storage tier is required/viable for which routes; can't understand boil-off economics

**Research finding:** The cooling architecture and mk{num} progression design is sound and creates natural tech gates + mission profile differentiation. Early-game players focus on local Venus skimming; mid-game unlock cycler freight via mk2 cooling tech; late-game Titan expeditions via mk3 + wormhole integration. The code just doesn't enforce boil-off consumption, power budgeting, or mk{num} scaling. Mission viability depends on implementing these constraints at each tech level.

---

## 3. Gaps Between Planned Design and Current Implementation

### Phase 1: JSON Data Entry — ⚠️ INCOMPLETE
- **Planned:** All Sol bodies have orbital_elements
- **Actual:** Only 6 out of 49 bodies in sol-complete.json; only 4 out of 8 in sol.json
- **Gap:** 43 bodies missing from sol-complete.json (Mercury, Saturn, Uranus, Neptune, major moons)
- **Gap:** 4 critical bodies missing from sol.json (Mercury, Saturn, Uranus, Neptune)
- **Impact:** Procedural generation + survey discovery must fill this gap; "known" data layer is 12% coverage

### Phase 2: Database Schema + SystemBuilderService — ⚠️ CODE PRESENT, UNTESTED
- **Planned:** JSON → CelestialBody.orbital_elements at runtime
- **Actual:** Migration exists, code exists, exclusion list fixed
- **Gap:** No RSpec test verifies this data flow actually works
- **Impact:** Assumption that JSON loads into DB has never been exercised; could silently fail in production

### Phase 3: Procedural Generation — ✅ CODE PRESENT
- **Planned:** Generate orbital_elements for unknown bodies
- **Actual:** OrbitalParametersGenerator generates all 4 fields with index-based + randomness
- **Gap:** Generated values lack epoch (same problem as known data)
- **Impact:** Cannot propagate generated angles forward in time

### Phase 4: Survey Discovery — ✅ CODE PRESENT
- **Planned:** Discover orbital_elements through gameplay tasks
- **Actual:** TaskExecutionEngine.perform_survey() checks if body has data, generates if missing
- **Gap:** No test verifies this code path works; generated values lack epoch
- **Impact:** Survey mechanic implemented but untested; epoch problem propagates to discovered data

### Phase 5: TransitEngine Integration — ❌ NOT STARTED
- **Planned:** Read orbital_elements and compute phase angles/launch windows
- **Actual:** Nothing started
- **Blocking gap:** Cannot implement without epoch design decision — mean_anomaly is meaningless without a reference epoch
- **Impact:** Venus→Luna skimmer route cannot be calculated; orbital mechanics simulation cannot run

---

## 4. Recommended Future Work

### Priority 1: Epoch Design (Blocks Phase 5)
**Decision needed from Claude:** How should `mean_anomaly` epochs be handled?

Options:
1. **Add `epoch` field to `orbital_elements` in JSON** — each body gets its own epoch timestamp
2. **Use a global constant** — all bodies share one epoch (e.g., J2000.0 = 2000-01-01T12:00:00 UTC)
3. **Derive from simulation time** — mean_anomaly is always relative to "now" at system creation

**Implementation if option 1:**
```json
"orbital_elements": {
  "semi_major_axis": 778600000.0,
  "eccentricity": 0.0489,
  "inclination": 1.304,
  "mean_anomaly": 20.02,
  "epoch": "J2000.0"
}
```

**Implementation if option 2:**
- Add constant in SystemBuilderService or a config file
- All bodies share the same epoch reference
- Simpler but less accurate for long-term simulations

### Priority 2: Verify Phase 2 Data Flow
**Test needed:** Confirm JSON data actually loads onto CelestialBody records.

Suggested test approach:
```ruby
# In Rails console or spec:
system = StarSim::SystemBuilderService.build('sol')
earth = CelestialBody.find_by(identifier: 'EARTH-01')
puts earth.orbital_elements.inspect
# Expected: {"semi_major_axis"=>149597870700.0, "eccentricity"=>0.0167, ...}
```

### Priority 3: Verify Docked Craft Capacity Pooling
**Code review needed:** Does the settlement inventory method actually sum docked craft capacity?

Files to check:
- `Settlement::BaseSettlement` — look for `total_capacity`, `available_storage`, or similar methods
- `Craft::BaseCraft` — check for `max_cargo_mass`, `storage_capacity` fields
- Any service that computes settlement inventory totals

### Priority 4: Check HLT Craft Boil-off Data
**File search needed:** Locate `heavy_lift_transport_harvester_venus_data.json` or equivalent craft operational data.

Check for:
- `boil_off_rate` field
- `loss_coefficient` field
- `cryogenic_tier` or `containment_tier` that implies a loss rate
- `gas_handling_policy` with storage/vent settings

### Priority 4b: Verify Boil-off Mechanics Enforcement
**Code review needed:** Boil-off parameters exist in storage blueprints but may not be enforced in gameplay.

**Gemini's research questions:**
1. **Do settlement power grids check `requires_cooling` on docked tanks?**
   - If power drops below threshold, does boil-off accelerate?
   - Is there a penalty/mechanic for running tanks without sufficient cooling?

2. **Is boil-off decay actually applied during transit?**
   - When HLT tanker is in flight (not docked), do volatile cargoes lose mass per day?
   - Does this scale with transit duration? (Venus→Luna is ~3 days; loss could be 1% of cargo)

3. **Do lava tube vaults actually reduce boil-off?**
   - Are underground storage locations treated differently from surface depots?
   - Is there a geothermal cooling bonus that slows decay?

4. **Is boil-off loss accounted in mission planning?**
   - When calculating fuel availability for return trip, does the system subtract expected boil-off?
   - Or is it assumed crews have enough ROE to handle the loss?

Files to check:
- `Settlement::BaseSettlement` or settlement service — power grid validation logic
- Cargo decay/loss mechanics (if they exist separate from boil-off)
- Inventory management during craft transit
- Long-duration storage mechanics (if implemented)

### Priority 4c: Wire Boil-off Consumption into Settlement AND Craft Systems (CRITICAL FOR LUNA VIABILITY)
**Implementation needed:** Both settlement storage AND crafts in transit must consume volatiles based on tank boil-off rates.

**Current gap:** Boil-off rates are defined in storage blueprints (e.g., 0.3% per day for methane) but NO CODE applies these losses—either to settlement inventory OR to craft cargo during transit.

**Settlement-level losses:**
1. **Daily tick calculation** — Each settlement update cycle, calculate boil-off loss for each stored volatile:
   - Tank with 10,000 kg methane @ 0.3% per day loses 30 kg
   - LOX @ similar rate (verify exact rate from blueprints)
   - Loss compounds with power availability (no active cooling = faster loss)

2. **Power-dependent acceleration** — If tank loses cooling power:
   - Normal loss: 0.3% per day
   - Under-cooled: 0.5–1.0% per day  
   - No power: 2–5% per day (rapid boil-off)

3. **Tank-inventory tracking** — Settlement must know:
   - Which tank stores which volatile
   - Tank power status (active/degraded/offline)
   - Current volatile mass in each tank

**Craft-level losses (during transit):**
1. **In-flight boil-off** — When HLT tanker travels Venus→Luna (3-day transit):
   - Carries 100 tons LOX + 50 tons methane
   - Loses 0.3% per day = loses 1.5 tons combined cargo over journey
   - Arrives with ~98.5 tons instead of 150 tons (1% loss)
   - This affects return-trip mission feasibility

2. **Craft power requirements during transit** — Tanker must have active cooling powered during flight:
   - If power drops: boil-off accelerates significantly
   - If power fails: rapid loss (possible total loss if uncontrolled boil-off)
   - Tanker fuel budget must include cooling power for entire transit

3. **Transit duration scaling** — Longer routes = bigger losses:
   - Venus→Luna: 3 days, ~1% loss
   - Mars→Luna: 6+ days, ~2% loss
   - Titan expedition: 10+ days, ~3% loss
   - This directly impacts route economics and mission ROE

**Production impact on Luna base economics:**
- Settlement storage: 10 tons methane storage → 30 kg/day loss → 900 kg/month replacement needed
- Craft arrivals: Every tanker loses ~1% of cargo in transit from Venus
- Combined effect: Luna must produce enough to replace both storage decay + transit loss
- Justifies local production over import dependency

**Why this blocks Luna simulation:**
- Without boil-off: "Import LOX once, it stays; use as needed" → base looks profitable with minimal production
- With boil-off: "Lose gas constantly in storage; lose more in transit; need continuous production" → completely different economic model
- Local regolith processing becomes viable/necessary (not just filler mechanics)
- Longer routes become economically unfeasible without proportional production increase

Files to modify:
- `Settlement::BaseSettlement` — add boil-off calculation in daily tick for stored tanks
- `Craft` or `Mission` class — apply boil-off to cargo inventory for each day of transit
- Settlement resource simulation service — apply losses to inventory
- Tank unit model — track power status and boil-off rate
- Mission planner — account for expected transit losses in route ROE calculations (arrival manifest ≠ departure manifest)

---

### Priority 5: TransitEngine Integration (Phase 5)
**Design + implementation needed:** Once epoch is decided, wire TransitEngine to read orbital_elements.

TransitEngine needs:
- `mean_anomaly` + `epoch` → propagate position forward from epoch to current time
- `semi_major_axis` + `eccentricity` → compute delta-v for transfer orbits (Hohmann, bi-elliptic)
- `inclination` → plane change costs
- `orbital_period` → synodic period calculations for launch windows

### Priority 6: Titan Loop Economics (Long-term)
**Unresolved design question from Gemini:** Is the Luna-to-Titan N₂/CH₄ loop economically viable?

Factors to model:
- Transit duration scaling with distance (Saturn ~9-10 AU)
- Boil-off rates by containment tech tier
- Delta-v costs for braking/climbing Saturnian system
- Alternative: Sabatier synthesis on Luna using PSR ice + Venus CO₂

---

## 5. Summary of What's Ready vs. What Needs Claude

### Code is present but UNTESTED:
- ⚠️ Phase 2: JSON → CelestialBody data flow (no RSpec verification)
- ⚠️ Phase 3: mean_anomaly procedural generation (code present, no test execution)
- ⚠️ Phase 4: Survey discovery mechanic (code present, no test execution)
- ⚠️ Docked craft capacity pooling (design documented but code needs verification)
- ⚠️ Boil-off enforcement in gameplay (parameters exist in tank blueprints but power grid enforcement unclear)

### Code is MISSING (critical gaps):
- ❌ Boil-off consumption loop in settlement daily simulation (blueprints define rates, but no code applies them to stored inventory)
- ❌ Boil-off consumption loop in craft transit (blueprints define rates, but no code applies them to cargo during multi-day journeys)
- ❌ Power-dependent boil-off acceleration (no code increases loss when cooling unavailable)
- ❌ Tank-inventory tracking in settlements (which tank stores which volatile?)
- ❌ Mission planner awareness of cargo loss during transit (arrival manifest ≠ departure manifest)

### Incomplete (not enough data):
- ❌ Phase 1 data coverage: Only 6/49 bodies in sol-complete.json, only 4/8 in sol.json
- ❌ Mercury, Saturn, Uranus, Neptune missing from sol.json
- ❌ All generated + surveyed orbital_elements lack epoch field

### Blocked (needs design decision):
- 🚫 Phase 5: TransitEngine cannot start without epoch design

### Ready:
- ✅ Database migration applied
- ✅ CelestialBody model has store_accessor
- ✅ SystemBuilderService code fixed
- ✅ Boil-off rate parameters defined in storage blueprints (0.3% per day cryogenic methane, power tiers 2–35 kW, etc.)
- ✅ Storage tank structure supports power monitoring and thermal processing states

### Needs Claude input:
1. **Epoch design decision** (Priority 1 — blocks Phase 5)
   - How should mean_anomaly epochs be handled?
   - J2000.0? Global constant? Per-body? Simulation-relative?
2. **Test verification** (Priority 2 — verify code actually works)
   - Does Phase 2 JSON→DB flow work at runtime?
   - Do Phase 3 + Phase 4 code paths execute without errors?
3. **Docked craft capacity pooling** (Priority 3 — Venus skimmer prerequisites)
   - Does Settlement model sum docked craft capacity correctly?
   - Does it recurse through craft → units deep enough?
4. **Boil-off mechanics enforcement** (Priority 4b — Venus loop modeling)
   - Are settlement power grids actually checking `requires_cooling` on docked tanks?
   - Is boil-off loss applied during craft transit?
   - Do lava tube vaults reduce boil-off decay rates?
   - Is loss accounted in mission planning/ROE calculations?
5. **Boil-off consumption implementation** (Priority 4c — CRITICAL FOR LUNA SIMULATION)
   - Should Settlement daily tick apply boil-off losses to stored inventory?
   - Should Craft inventory lose volatiles during multi-day transit?
   - How to model power-dependent acceleration (no cooling = faster loss)?
   - Design decision: Which volatiles track boil-off? (methane, LOX, N₂, etc.)
   - Transit arrival manifest ≠ departure manifest (loss accumulates over journey) — must mission planner account for this?
6. **TransitEngine integration plan** (Priority 5 — orbital mechanics)
   - Once epoch is decided, how to wire orbital data to launch window calculations?
   - How to integrate boil-off loss into route planning (cost vs. time tradeoff)?
7. **Titan loop economics** (Priority 7 — long-term viability)
   - Is Luna-to-Titan N₂/CH₄ loop viable given transit distance and boil-off?
   - Better path: Sabatier synthesis on Luna instead?
