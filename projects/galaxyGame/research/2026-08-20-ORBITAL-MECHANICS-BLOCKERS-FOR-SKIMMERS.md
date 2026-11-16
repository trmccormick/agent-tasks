# Orbital Mechanics Blockers for Skimmer Implementation

**Date:** 2026-08-20  
**Context:** Architecture clarification (mk2 early unlock, HLT harvesters Phase 0, stationary skimmers Phase 11+) now reveals which orbital mechanics research findings are critical vs. deferred.

---

## Blocker Classification

### 🔴 CRITICAL BLOCKERS (Skimmers cannot launch without resolving)

#### 1. Boil-off Enforcement Not Implemented in Code (CRITICAL)
**Research finding:** Storage blueprints define boil-off rates (0.3% mk1, 0.15% mk2, 0.07% mk3 per day) but **zero code applies these losses** to settlement inventory or craft cargo during transit.

**Impact on skimmer architecture:**
- **Phase 0 HLT harvesters** equipped with mk2 tanks (0.15% daily loss) require: 8-10 month transit loss calculation from Venus/Titan → Luna
- **Phase 11+ stationary skimmers** with cycler integration require: Boil-off applied to cycler transits carrying volatile cargo
- Without enforcement: Both systems appear uneconomical (losses unaccounted for in mission planning)
- With enforcement: mk2 cooling gates become visible (mk1 too expensive for long transits, mk2 viable)

**Files needing implementation:**
- `Settlement::BaseSettlement` — apply daily boil-off to stored volatiles based on tank type + power status
- `Craft` or `Mission` class — apply boil-off to cargo inventory per transit day
- Propellant/fuel calculation — reduce available LOX/methane by boil-off losses when planning return trip

**Why it's critical now:** Without boil-off enforcement, players cannot understand why Phase 0 HLT harvesters need mk2 cooling or why Phase 11+ stationary skimmers with cyclers are economically viable. The entire boil-off→tech gate progression becomes invisible.

---

#### 2. Epoch Design for Mean Anomaly (BLOCKS TransitEngine)
**Research finding:** `mean_anomaly` values in JSON (e.g., `20.02°` for Jupiter) are meaningless without an epoch reference. Cannot compute current orbital phase without knowing "what date were these angles measured?"

**Impact on skimmer architecture:**
- Early HLT harvesters (Phase 0) departing Earth → Venus require: Hohmann transfer calculation using actual orbital phase angles
- Transfer windows exist only when planetary positions align correctly
- Without epoch: Cannot compute if Venus is "in transfer window" from Earth
- TransitEngine is blocked on this design decision

**Decision needed:**
```
Option A: Add epoch field to orbital_elements JSON
  "orbital_elements": {
    "semi_major_axis": 108208000000.0,
    "eccentricity": 0.0068,
    "inclination": 3.39,
    "mean_anomaly": 50.12,
    "epoch": "J2000.0"  // or ISO timestamp
  }

Option B: Use global epoch constant
  All bodies share one reference epoch (e.g., J2000.0 = 2000-01-01)
  Simpler but less flexible for long-term simulation accuracy

Option C: Derive from simulation start time
  mean_anomaly always relative to system creation time
  Works for closed games but breaks if save/restore spans real time
```

**Why it's critical now:** Once stationary skimmers deploy Phase 11+, cycler transits need transfer window calculations. Cannot launch cycler mission without knowing current orbital alignment. This unblocks Phase 5 TransitEngine but is NOT needed for Phase 0 HLT harvesters (assuming simplified transfer window logic for early game).

---

### 🟡 HIGH PRIORITY (Skimmers work without, but economics are wrong)

#### 3. Docked Craft Capacity Pooling Not Verified in Code
**Research finding:** Documentation describes depot-craft capacity pooling (settlement should sum docked craft storage → craft's units). Association exists (`Settlement.has_many :docked_crafts`), but pooling calculation code may be missing or incomplete.

**Impact on skimmer architecture:**
- **Stationary skimmers (Phase 11+) dock at depot/station** to offload cargo
- Depot total capacity = base storage + sum of docked craft cargo tanks
- If pooling doesn't work: Skimmer cargo tanks appear unavailable; depot looks full when it shouldn't be
- Players won't understand why docked skimmers "increase" depot capacity

**Files needing verification:**
- `Settlement::BaseSettlement` — does `total_capacity` or `available_storage` method exist and sum docked craft units?
- Inventory check during skimmer unload — does it count docked skimmers' storage as available?

**Why it's high priority:** Stationary skimmers cannot function without this. Early-game Earth import chains don't need it (tankers don't dock), but Phase 11+ skimmer economics break completely if pooling doesn't work.

---

#### 4. Settlement Power Grid Validation for Cooling
**Research finding:** Boil-off rates scale with power status (active cooling: 0.15% mk2 / standby: 2-3% mk2 / offline: 10%+ mk2). But settlement power grid doesn't validate tank cooling requirements.

**Impact on skimmer architecture:**
- **Phase 11+ stationary skimmers** operating at Venus/Titan depots require active cooling power
- If depot power drops: Volatile cargo boil-off accelerates (uneconomical)
- If code doesn't check: Players won't see power as a constraint; cooling appears free
- Mission planning becomes "just leave it docked" when it should be "need 50 kW active cooling"

**Files needing implementation:**
- Settlement power grid service — check total cooling power requirements for all stored volatiles
- Alert system — warn when cooling demand exceeds available power
- Boil-off acceleration — scale loss rates up when power insufficient

**Why it's high priority:** Without this, stationary skimmers at remote depots appear infinitely scalable (no cooling cost). Economics flatten out.

---

### 🟢 MEDIUM PRIORITY (Deferred, Phase 5+)

#### 5. Orbital Data Coverage Sparse (6/49 bodies with orbital_elements)
**Research finding:** Only 6 out of 49 Sol bodies have orbital_elements in sol-complete.json. Mercury, Saturn, Uranus, Neptune, all moons except Luna/Titan, dwarf planets all missing.

**Impact on skimmer architecture:**
- Early skimmer routes (Phase 0-11) only need Earth, Venus, Mars, Luna, Titan
- All 5 of these exist in orbital_elements (6 known bodies includes Jupiter, which we don't visit)
- Sparse coverage becomes issue only when Phase 13+ extends beyond inner system

**Why it's deferred:** Three-layer design (known → procedural → survey) handles coverage. Procedural generation fills unknowns for gameplay. Full dataset not needed for initial launch.

---

#### 6. TransitEngine Simplified vs. Full Ephemeris
**Research finding:** Current code has no real-time position tracking. Real orbital mechanics would require adding `current_position` + `epoch` to CelestialBody model, or accepting that transit times are computed from fixed phase angles only.

**Impact on skimmer architecture:**
- Early HLT harvesters can use **simplified transfer window logic**: "Venus is available Phase 0; launch window is Month 5"
- Phase 11+ cycler routes benefit from full ephemeris: "Cycler departs Earth on day X of launch window; compute exact transfer based on current alignment"
- Neither requires full real-time position tracking immediately

**Why it's deferred:** Gameplay can function with simplified transfer windows (fixed phase angle grid) until Phase 11+. Full ephemeris is optimization/realism, not requirement.

---

## Implementation Priority for Skimmer Launch

### Phase 0 HLT Harvesters (Month 5 Venus, Month 18 Titan)
**Required:**
- ✅ mk2 storage blueprints exist and simplified (already done)
- ✅ HLT mk1 compatibility with mk2 tanks (already done)
- ⚠️ **Boil-off enforcement** (CRITICAL — need implementation)
- ⚠️ **Simplified transfer window logic** (can use fixed dates; no epoch needed yet)

**Not required:**
- Full ephemeris/epoch design (deferred to Phase 11)
- Docked craft capacity pooling (HLT tankers don't dock at Earth)
- Settlement power grid cooling validation (Earth has unlimited power)

---

### Phase 11+ Stationary Skimmers & Cycler Integration
**Required:**
- ✅ Boil-off enforcement (required Phase 0; reused here)
- ✅ Epoch design (needed for TransitEngine launch window calculations)
- ⚠️ **Docked craft capacity pooling** (CRITICAL — skimmers dock at depots)
- ⚠️ **Settlement power grid validation for cooling** (CRITICAL — depot cooling is resource cost)
- ✅ Orbital data for Earth/Venus/Mars/Luna/Titan (all covered in known data)

---

## Action Items

### Must Implement Before Phase 0 HLT Launch:
1. **Boil-off Enforcement** (Settlement + Craft daily tick consumption)
   - Estimated: 2-3 tasks (settlement losses, craft transit losses, refuel/arrival validation)
   - Blocks: Phase 0 HLT economic viability

2. **Simplified Transfer Window Logic** (Month-based or phase-angle-based launch windows)
   - Estimated: 1 task
   - Blocks: HLT mission planning (can use fixed dates per phase)

### Must Implement Before Phase 11 Cycler Activation:
1. **Epoch Design Decision** (Add to orbital_elements or use global constant)
   - Estimated: Design discussion + 1 implementation task
   - Blocks: TransitEngine phase angle calculations

2. **Docked Craft Capacity Pooling** (Settlement sums docked craft storage)
   - Estimated: 1-2 tasks (verification + fix if missing)
   - Blocks: Skimmer cargo offload capacity

3. **Settlement Power Grid Cooling Validation** (Check cooling power availability)
   - Estimated: 2-3 tasks (power requirement checks, acceleration on low power, alerts)
   - Blocks: Depot operating cost visibility

---

## Summary: Orbital Mechanics Enables Skimmer Progression

The research findings validate that **boil-off enforcement is the gate**:
- Without it: All storage tiers identical; early harvesters uneconomical; skimmer depots appear free
- With it: mk1 insufficient for long transits; mk2/mk3 unlock as techs become necessary; depot cooling becomes visible resource cost

This aligns perfectly with our Phase Structure clarification:
- **Phase 1-4 (Foundation):** mk2 research unlocked; Earth can produce mk2 cooling tanks
- **Phase 0:** HLT harvesters launch with mk2 tanks; boil-off losses already calculated
- **Phase 11+:** Stationary skimmers with full cycler integration; epoch-based transfer windows; cooling as resource constraint

Boil-off enforcement is the master key that makes both the early harvester progression AND the Phase 11+ skimmer economics work. All other orbital mechanics features support it but aren't critical until Phase 11.
