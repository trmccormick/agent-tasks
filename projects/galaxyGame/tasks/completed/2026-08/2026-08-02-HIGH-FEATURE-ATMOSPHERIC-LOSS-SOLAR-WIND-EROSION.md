---
status: active
priority: HIGH
type: feature
system_domain: TERRA_SIM | TERRAFORMING | MAGNETOSPHERE
mvp_alignment: MAGNETOSPHERE_INFRASTRUCTURE | TERRAFORMING_PHYSICS
local_worker_safe: true
blocked_by:
  - 2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION
blocker_reason: "Requires magnetosphere_strength (0.0-1.0) to be available from CelestialBody.properties; currently only binary has_magnetosphere flag exists. Data-driven task establishes proper data model."
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md \
         projects/galaxy_game/tasks/active/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md

Then update YAML status: backlog → active

Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, architecture gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/

Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file.

---

# TASK: Implement Atmospheric Loss Due to Solar Wind Erosion (Magnetosphere Gating)

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-02
**Last Updated**: 2026-08-02

---

## Context

Magnetosphere infrastructure exists in the codebase as a boolean flag (`CelestialBody#has_magnetosphere` / `has_magnetosphere_protection?`), but the physics model is incomplete: **atmospheric replenishment via outgassing is implemented and wired into every simulation pass, but atmospheric loss due to lack of magnetosphere protection is not modeled at all**.

Current state:
- ✅ `GeosphereSimulationService#simulate_volatile_phase_transitions` runs every simulation pass
- ✅ `VolatilePhaseTransitionService` handles outgassing (geosphere → atmosphere) and freezing/deposition (atmosphere → geosphere)
- ✅ `Geosphere#calculate_volatile_release(temperature)` reads `stored_volatiles` and returns release amounts based on storage location and temperature
- ❌ **No atmospheric loss mechanism exists** — `AtmosphereSimulationService#simulate_atmospheric_loss` has only a placeholder `calculate_solar_wind_factor` returning `0.0001` (a constant, ignoring magnetosphere status)

**The gap**: Without magnetic protection, a body loses atmosphere to solar wind erosion. Mars with magnetosphere_protection? == false should lose atmospheric mass each simulation step. This loss should be gated by the `has_magnetosphere` flag — when true, loss is negligible; when false, loss is significant.

Tracy's magnetosphere design ("stop the bleed and let it thicken") requires both sides:
1. ✅ Outgassing/thickening side (implemented)
2. ❌ Loss/erosion side (needs to be built)

This task implements the erosion side, completing the feedback loop.

**Relevant Architecture Docs**:
- [TERRAFORMING_PATTERNS.md](../../../../docs/architecture/services/ai_manager/TERRAFORMING_PATTERNS.md) — magnetosphere efficiency factor listed but not implemented
- [sol-complete.json](../../../../../../../data/json-data/star_systems/sol-complete.json) — Mars data with stored_volatiles structure and magnetosphere flag
- [MAGNETOSPHERE_PROTOCOL.md](../../../../docs/MAGNETOSPHERE_PROTOCOL.md) — if it exists; check for design docs on how magnetosphere should affect atmospheric dynamics

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Don't duplicate VolatilePhaseTransitionService logic**
- ❌ Wrong: Create a separate "atmospheric erosion service" that runs independently
- ✅ Right: Extend or integrate with the existing `AtmosphereSimulationService#simulate_atmospheric_loss` method, which already exists as a stub
- Why: `GeosphereSimulationService` already calls `AtmosphereSimulationService.simulate()` every pass. The erosion logic should be part of this existing call chain, not a parallel service invocation.

⚠️ **GOTCHA 2: Magnetosphere flag is a property, not a dedicated table**
- ❌ Wrong: Query a separate `Magnetosphere` model or table
- ✅ Right: Check `celestial_body.has_magnetosphere` (stored in `CelestialBody#properties` JSONB field)
- Why: The flag is a scalar boolean, not a relationship. No separate model exists.

⚠️ **GOTCHA 3: Loss is per-gas, not per-atmosphere**
- ❌ Wrong: Calculate one loss rate and apply it uniformly to all gases
- ✅ Right: Different gases escape at different rates depending on:
  - Gravity (escape velocity)
  - Temperature (kinetic energy of molecules)
  - Molecular mass (lighter gases escape faster)
- Example: H₂ escapes much faster than CO₂; light noble gases escape faster than heavy gases
- Why: Without per-gas differentiation, a Mars atmosphere (95% CO₂) will behave unrealistically compared to Earth atmosphere (78% N₂, 21% O₂, 0.9% Ar)

⚠️ **GOTCHA 4: Solar wind factor must vary by distance from star**
- ❌ Wrong: Hardcode a constant loss rate for all worlds
- ✅ Right: Use the body's distance to its star (`celestial_body.star_distances`) to calculate solar flux / wind intensity
- Why: Venus (108M km from Sol) experiences much stronger solar wind than Mars (228M km); loss rate should reflect this
- Hint: Stefan-Boltzmann `solar_constant` already exists on CelestialBody — you can use this or derive from star distance

⚠️ **GOTCHA 5: Parent magnetosphere inheritance (Titan/Saturn & Ganymede/Jupiter patterns)**
- **Scenario 1 (Titan)**: No intrinsic field (0.0), orbits inside Saturn's magnetosphere
  - Saturn: magnetosphere_strength 1.0, radius 60M km
  - Titan orbit: ~1.2M km (well inside Saturn's field)
  - Effective protection: inherited from Saturn, scaled by distance ratio
  - Result: Titan loses atmosphere slower than Mars (which has 0.0) but faster than Earth (1.0)

- **Scenario 2 (Ganymede)**: Has intrinsic field (0.15) AND orbits inside Jupiter's magnetosphere
  - Ganymede: intrinsic 0.15, radius 500 km
  - Jupiter: magnetosphere_strength 1.0, radius 7M km (data now in sol-complete.json)
  - Ganymede orbit: 1.07M km (inside Jupiter's 7M km field)
  - Real physics: Creates "mini-magnetosphere embedded inside" Jupiter's field (NASA discovery, 1996)
  - Effective protection: intrinsic + inherited = [0.15 + (1.0 × distance_factor), 1.0].min ≈ 0.7–0.8
  - Result: Ganymede's atmosphere loss is lowest among large moons (both protection sources)

- **Implementation**:
  - Check if body has `parent_celestial_body` association
  - If yes, look up parent's magnetosphere_strength and magnetosphere_radius_km
  - Compare body's orbital_distance_km to parent's radius
  - If orbit < parent_radius: calculate distance_factor = 1 - (orbital_distance / parent_radius)
  - Calculate inherited_protection = parent_strength × distance_factor
  - Combine: effective = [intrinsic_strength + inherited_protection, 1.0].min (cap at 1.0)
  - Use effective_protection in solar wind loss calculation

- **Not blocking**: Implement intrinsic protection first; parent inheritance is enhanced logic if time permits

---

## Files Involved

### Primary Files — you will edit these

| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/terra_sim/atmosphere_simulation_service.rb` | Atmosphere simulation (pressure, greenhouse, loss) | `#simulate_atmospheric_loss` (line ~115, currently stub) |
| `app/models/celestial_bodies/spheres/atmosphere.rb` | Atmosphere model (gases, mass tracking) | `#recalculate_mass!`, `#update_pressure_from_mass!` |
| `spec/services/terra_sim/atmosphere_simulation_service_spec.rb` | Test suite for atmosphere simulation | Tests for erosion rate calculation and gas loss |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `app/models/celestial_bodies/celestial_body.rb` | Understand `has_magnetosphere`, `star_distances`, `solar_constant` accessors |
| `app/services/terra_sim/volatile_phase_transition_service.rb` | See how bidirectional gas exchange is modeled (outgassing ↔ freezing) |
| `data/json-data/star_systems/sol-complete.json` | Mars data structure: `stored_volatiles`, `has_magnetosphere` flag, atmospheric composition |

### Migrations

- [ ] No migration needed — `Atmosphere` table already has all required columns
- [ ] Verify: `atmospheres` table has `pressure`, `total_atmospheric_mass`, foreign key to `celestial_bodies`

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any files, running any commands, or modifying anything, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Atmospheric Loss Due to Solar Wind Erosion (Magnetosphere Gating)
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Implement atmospheric loss calculation in AtmosphereSimulationService, gated by magnetosphere protection flag. Loss varies per-gas (lighter gases escape faster) and by stellar distance. When magnetosphere_protection? == false, atmosphere loses mass; when true, loss is negligible.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/terra_sim/atmosphere_simulation_service.rb` | Implement `#calculate_solar_wind_factor` and integrate with `#simulate_atmospheric_loss` | not started |
| `app/models/celestial_bodies/celestial_body.rb` | Verify `has_magnetosphere`, `star_distances`, `solar_constant` exist and are accessible | not started |
| `spec/services/terra_sim/atmosphere_simulation_service_spec.rb` | Add tests for per-gas loss rates and magnetosphere gating | not started |
| `data/json-data/star_systems/sol-complete.json` | Reference Mars data structure | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas (integrate with existing method, not duplicate; use celestial_body.has_magnetosphere flag; per-gas loss rates; stellar distance factor)
- ✅ Understand magnetosphere is stored in properties JSONB, not a separate model

### Expected Outcomes
- Atmosphere loses mass each simulation step when magnetosphere_protection? == false
- Loss rate varies per-gas (H₂ > He > N₂ > CO₂ > O₂, roughly by molecular mass and gravity)
- Loss rate varies by stellar distance (Venus closer = higher loss rate than Mars)
- Loss rate negligible when magnetosphere_protection? == true
- Mars with false magnetosphere shows measurable pressure drop over time; with true magnetosphere shows stable/rising pressure from outgassing
- All existing atmosphere tests pass; new tests cover loss calculation and magnetosphere gating

### Critical Gotchas I Will Avoid
- ❌ Create a new "erosion service" — instead ✅ extend the existing `simulate_atmospheric_loss` method
- ❌ Apply uniform loss rate to all gases — instead ✅ calculate per-gas loss based on escape velocity formula
- ❌ Hardcode a constant loss factor — instead ✅ calculate from stellar distance / solar flux
- ❌ Query a separate Magnetosphere table — instead ✅ use `celestial_body.has_magnetosphere` property

### Acceptance Criteria Met
- ✅ `calculate_solar_wind_factor` returns 0 when magnetosphere_protection? == true
- ✅ `calculate_solar_wind_factor` returns non-zero value when magnetosphere_protection? == false, scaled by stellar distance
- ✅ Per-gas loss rates implemented (lighter gases lose faster)
- ✅ Atmosphere#gases updated with new mass after loss calculation
- ✅ Mars simulation step: pressure drops measurably without magnetosphere, stable/rises with magnetosphere
- ✅ All isolation tests pass (atmosphere simulation, gas loss, magnetosphere gating)
- ✅ No regressions in related specs

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

**Current behavior**: Atmospheric mass is only added (via outgassing) or moved between states (via freezing). No atmospheric loss mechanism exists. A world without magnetosphere protection retains its atmosphere indefinitely, making the magnetosphere flag a purely narrative element with no mechanical impact.

**Expected behavior**: A world without magnetosphere protection should lose atmosphere to solar wind erosion at a rate proportional to:
1. **Presence of magnetic shield** — magnetosphere_protection? == false → loss; == true → negligible loss
2. **Stellar distance** — closer to star = higher solar wind intensity → faster loss
3. **Gas composition** — lighter gases (H₂, He) escape faster than heavier ones (CO₂, Ar)
4. **Planetary gravity** — lower gravity = easier escape

**Mechanical implication**: "Stop the bleed and let it thicken" means:
- Deploy magnetosphere (magnetosphere_protection? = true) → erosion stops
- Atmosphere from outgassing/import now accumulates instead of being stripped away
- Pressure rises over time from the combination of stopped loss + continued outgassing

Without the loss mechanism, deploying a magnetosphere has no observable effect on pressure trends (outgassing alone would be happening regardless).

---

## Implementation Steps

All agents: follow these steps exactly in order. Do not skip or reorder.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md \
       projects/galaxy_game/tasks/active/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md
```

Update the YAML status field in the moved file:
```
status: backlog  →  status: active
```

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md"
```

**Paste the output in chat.** Expected: exactly one result at the `active/` path.

### Step 1 — Understand the current placeholder implementation

Read the current `AtmosphereSimulationService#simulate_atmospheric_loss` method in full:
```bash
grep -A 30 "def simulate_atmospheric_loss" \
  /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/terra_sim/atmosphere_simulation_service.rb
```

**Note**: It should show:
```ruby
def simulate_atmospheric_loss
  atmosphere = @celestial_body.atmosphere
  return unless atmosphere.present?

  loss_factor = calculate_solar_wind_factor  # ← currently returns 0.0001

  atmosphere.gases.each do |gas|
    next if gas.mass.nil?
    new_mass = [gas.mass - gas.mass * loss_factor, 0].max
    gas.update(mass: new_mass)
  end

  atmosphere.recalculate_mass!
  atmosphere.update_pressure_from_mass!
end

def calculate_solar_wind_factor
  # Placeholder for future magnetic field-based formula
  0.0001
end
```

This is the integration point. You will replace `calculate_solar_wind_factor` with a real implementation.

### Step 2 — Understand CelestialBody accessors for magnetosphere and stellar distance

Read the celestial_body model to confirm these exist:
```bash
grep -E "(has_magnetosphere|star_distances|solar_constant)" \
  /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/celestial_bodies/celestial_body.rb | head -20
```

**Expected**: To see method definitions or store_accessor calls for:
- `has_magnetosphere` (boolean, stored in properties JSONB)
- `star_distances` (array of hashes with star_name and distance)
- `solar_constant` (calculated from star distance, or can derive from it)

If any are missing, note it now — do not create them as part of this task (flag as blocker).

### Step 3 — Understand atmosphere gas structure and loss mechanics

Read how gases are stored and updated:
```bash
grep -A 10 "class Gas" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/celestial_bodies/materials/gas.rb | head -20
```

**Verify**: Each gas has at least:
- `atmosphere_id` (foreign key)
- `name` (string: 'CO2', 'H2O', 'N2', 'O2', 'H2', 'He', 'Ar', etc.)
- `mass` (numeric: total mass of this gas in kg)
- `percentage` (numeric: percentage of total atmosphere)

### Step 4 — Implement `calculate_solar_wind_factor`

Replace the stub in `AtmosphereSimulationService` with a real implementation:

```ruby
private

def calculate_solar_wind_factor
  # Step 4a: Check magnetosphere protection
  return 0.00001 if @celestial_body.has_magnetosphere  # Nearly zero loss with protection

  # Step 4b: Calculate base solar wind intensity from stellar distance
  # Closer to star = higher solar flux = stronger wind
  # Use inverse-square law: intensity ∝ 1 / distance²
  
  star_distance = @celestial_body.star_distances&.first&.dig('distance')
  return 0.0001 if star_distance.nil? || star_distance <= 0  # Fallback if distance unavailable

  # Reference: Earth distance from Sol ≈ 149.6M km (1 AU)
  # Solar constant at Earth ≈ 1361 W/m²
  # At Mars (1.52 AU) ≈ 589 W/m² (inverse square)
  
  au_to_km = 149_600_000.0
  earth_distance_km = au_to_km
  distance_au = star_distance.to_f / au_to_km
  
  # Inverse square law for solar wind intensity
  # intensity_ratio = (earth_distance / actual_distance)² 
  intensity_ratio = (earth_distance_km / star_distance.to_f) ** 2
  
  # Base loss rate: ~0.00001 at Earth distance with no magnetosphere
  # Scales by intensity ratio
  base_loss_rate = 0.00001
  
  intensity_ratio * base_loss_rate
end
```

**Testing**: Add a puts statement to verify calculation:
```ruby
def calculate_solar_wind_factor
  puts "DEBUG: magnetosphere=#{@celestial_body.has_magnetosphere}, distance=#{@celestial_body.star_distances&.first&.dig('distance')}"
  # ... rest of method
  puts "DEBUG: loss_factor=#{result}"
  result
end
```

Remove debug puts before commit.

### Step 5 — Extend `simulate_atmospheric_loss` to handle per-gas loss rates

**Current code** (from Step 1) treats all gases equally. **Update it** to:

```ruby
def simulate_atmospheric_loss
  atmosphere = @celestial_body.atmosphere
  return unless atmosphere.present?

  loss_factor = calculate_solar_wind_factor
  return if loss_factor <= 0  # No loss if magnetosphere active

  # Apply loss per-gas, adjusted by molecular mass / escape velocity
  atmosphere.gases.each do |gas|
    next if gas.mass.nil? || gas.mass <= 0

    # Step 5a: Get molecular mass for this gas (use a lookup or hardcode)
    gas_mass_factor = molecular_mass_factor(gas.name)
    
    # Step 5b: Calculate adjusted loss rate (lighter gases escape faster)
    # Escape velocity ∝ sqrt(gravity / molecular_mass)
    # So loss rate ∝ 1 / sqrt(molecular_mass) relative to baseline
    
    adjusted_loss_rate = loss_factor * gas_mass_factor
    
    # Step 5c: Update gas mass
    new_mass = [gas.mass - gas.mass * adjusted_loss_rate, 0].max
    gas.update(mass: new_mass)
  end

  # Step 5d: Recalculate atmosphere properties
  atmosphere.recalculate_mass!
  atmosphere.update_pressure_from_mass!
end

private

def molecular_mass_factor(gas_name)
  # Relative escape rates based on molecular mass
  # H₂ escapes fastest, CO₂ escapes slowest
  # Factors relative to a baseline (here, CO₂ = 1.0)
  
  factors = {
    'H2' => 5.0,   # Hydrogen: very light, escapes 5x faster than CO₂
    'He' => 3.5,   # Helium: light noble gas
    'N2' => 1.2,   # Nitrogen: lighter than CO₂
    'O2' => 1.1,   # Oxygen: slightly lighter than CO₂
    'Ar' => 0.9,   # Argon: slightly heavier than CO₂
    'CO2' => 1.0,  # Carbon dioxide: baseline
    'H2O' => 0.8,  # Water vapor: heavier, escapes slower
    'CH4' => 1.15  # Methane: between N₂ and CO₂
  }
  
  factors.fetch(gas_name, 1.0)  # Default to 1.0 (CO₂-like) if unknown
end
```

**Rationale**: Lighter molecules have higher thermal velocities and lower gravitational binding; they escape proportionally faster.

### Step 6 — Write tests for the new functionality

Add tests to `spec/services/terra_sim/atmosphere_simulation_service_spec.rb`:

```ruby
describe '#simulate_atmospheric_loss' do
  let(:mars_with_magnetosphere) do
    create(:celestial_body, 
      name: 'Mars Protected',
      has_magnetosphere: true,
      star_distances: [{ 'star_name' => 'Sol', 'distance' => 227_900_000.0 }]
    )
  end

  let(:mars_without_magnetosphere) do
    create(:celestial_body,
      name: 'Mars Unprotected',
      has_magnetosphere: false,
      star_distances: [{ 'star_name' => 'Sol', 'distance' => 227_900_000.0 }]
    )
  end

  describe 'with magnetosphere protection' do
    it 'causes negligible atmosphere loss' do
      mars_with_magnetosphere.build_atmosphere
      mars_with_magnetosphere.atmosphere.gases.create!(name: 'CO2', mass: 1000.0, percentage: 100.0)
      
      initial_mass = mars_with_magnetosphere.atmosphere.total_atmospheric_mass
      
      simulator = TerraSim::Simulator.new(mars_with_magnetosphere)
      simulator.calc_current
      
      # Reload to get updated mass
      mars_with_magnetosphere.reload
      final_mass = mars_with_magnetosphere.atmosphere.total_atmospheric_mass
      
      # Loss should be << 1% per step
      loss_pct = ((initial_mass - final_mass) / initial_mass) * 100
      expect(loss_pct).to be < 0.1
    end
  end

  describe 'without magnetosphere protection' do
    it 'causes measurable atmosphere loss' do
      mars_without_magnetosphere.build_atmosphere
      mars_without_magnetosphere.atmosphere.gases.create!(name: 'CO2', mass: 1000.0, percentage: 100.0)
      
      initial_mass = mars_without_magnetosphere.atmosphere.total_atmospheric_mass
      
      simulator = TerraSim::Simulator.new(mars_without_magnetosphere)
      simulator.calc_current
      
      mars_without_magnetosphere.reload
      final_mass = mars_without_magnetosphere.atmosphere.total_atmospheric_mass
      
      # Loss should be measurable but not > 1% per step
      loss_pct = ((initial_mass - final_mass) / initial_mass) * 100
      expect(loss_pct).to be_between(0.0001, 1.0)
    end

    it 'loses lighter gases faster than heavier gases' do
      mars_without_magnetosphere.build_atmosphere
      mars_without_magnetosphere.atmosphere.gases.create!(name: 'H2', mass: 100.0, percentage: 50.0)
      mars_without_magnetosphere.atmosphere.gases.create!(name: 'CO2', mass: 100.0, percentage: 50.0)
      
      h2_initial = mars_without_magnetosphere.atmosphere.gases.find_by(name: 'H2').mass
      co2_initial = mars_without_magnetosphere.atmosphere.gases.find_by(name: 'CO2').mass
      
      simulator = TerraSim::Simulator.new(mars_without_magnetosphere)
      simulator.calc_current
      
      mars_without_magnetosphere.reload
      h2_final = mars_without_magnetosphere.atmosphere.gases.find_by(name: 'H2').mass
      co2_final = mars_without_magnetosphere.atmosphere.gases.find_by(name: 'CO2').mass
      
      h2_loss_pct = ((h2_initial - h2_final) / h2_initial) * 100
      co2_loss_pct = ((co2_initial - co2_final) / co2_initial) * 100
      
      # H₂ should lose ~5x faster than CO₂
      expect(h2_loss_pct).to be > co2_loss_pct
      expect(h2_loss_pct / co2_loss_pct).to be_between(3, 7)
    end

    it 'loss rate scales inversely with stellar distance' do
      venus = create(:celestial_body,
        name: 'Venus',
        has_magnetosphere: false,
        star_distances: [{ 'star_name' => 'Sol', 'distance' => 108_000_000.0 }]  # Closer than Mars
      )
      
      mars = create(:celestial_body,
        name: 'Mars',
        has_magnetosphere: false,
        star_distances: [{ 'star_name' => 'Sol', 'distance' => 227_900_000.0 }]
      )
      
      venus.build_atmosphere
      venus.atmosphere.gases.create!(name: 'CO2', mass: 1000.0, percentage: 100.0)
      
      mars.build_atmosphere
      mars.atmosphere.gases.create!(name: 'CO2', mass: 1000.0, percentage: 100.0)
      
      venus_sim = TerraSim::Simulator.new(venus)
      venus_sim.calc_current
      
      mars_sim = TerraSim::Simulator.new(mars)
      mars_sim.calc_current
      
      venus.reload
      mars.reload
      
      venus_loss_pct = ((1000.0 - venus.atmosphere.gases.find_by(name: 'CO2').mass) / 1000.0) * 100
      mars_loss_pct = ((1000.0 - mars.atmosphere.gases.find_by(name: 'CO2').mass) / 1000.0) * 100
      
      # Venus should lose more atmosphere than Mars (closer to star)
      expect(venus_loss_pct).to be > mars_loss_pct
    end
  end
end
```

### Step 7 — Verify with docker exec RSpec

Run the atmosphere simulation tests:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/terra_sim/atmosphere_simulation_service_spec.rb 2>&1 | tail -50'
```

Expected result: All tests pass, including the new loss and magnetosphere tests.

### Step 8 — Manual integration test (Mars scenario)

Create a quick script to simulate Mars over 10 time steps and see pressure trends:

```bash
docker exec -it web bash -c '
unset DATABASE_URL
RAILS_ENV=test bundle exec rails runner "
mars_unprotected = create(:celestial_body, name: \"Mars Unprotected\", has_magnetosphere: false, star_distances: [{\"star_name\" => \"Sol\", \"distance\" => 227_900_000.0}])
mars_unprotected.build_atmosphere
mars_unprotected.atmosphere.gases.create!(name: \"CO2\", mass: 2.5e16, percentage: 100.0)

mars_protected = create(:celestial_body, name: \"Mars Protected\", has_magnetosphere: true, star_distances: [{\"star_name\" => \"Sol\", \"distance\" => 227_900_000.0}])
mars_protected.build_atmosphere
mars_protected.atmosphere.gases.create!(name: \"CO2\", mass: 2.5e16, percentage: 100.0)

puts \"Initial state:\"
puts \"  Mars Unprotected: #{mars_unprotected.atmosphere.pressure.round(6)} bar\"
puts \"  Mars Protected: #{mars_protected.atmosphere.pressure.round(6)} bar\"

10.times do |i|
  TerraSim::Simulator.new(mars_unprotected).calc_current
  TerraSim::Simulator.new(mars_protected).calc_current
  
  mars_unprotected.reload
  mars_protected.reload
  
  if (i + 1) % 2 == 0
    puts \"After #{i + 1} steps:\"
    puts \"  Mars Unprotected: #{mars_unprotected.atmosphere.pressure.round(6)} bar (loss: #{((2.5e16 - mars_unprotected.atmosphere.total_atmospheric_mass) / 2.5e16 * 100).round(2)}%)\"
    puts \"  Mars Protected: #{mars_protected.atmosphere.pressure.round(6)} bar\"
  end
end
" 2>&1
'
```

Expected behavior:
- **Mars Unprotected**: Pressure drops steadily with each step (loss visible)
- **Mars Protected**: Pressure remains stable or increases slightly (no loss, or loss negligible)

### Step 9 — Commit

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add app/services/terra_sim/atmosphere_simulation_service.rb
git add spec/services/terra_sim/atmosphere_simulation_service_spec.rb
git commit -m "feat: atmospheric loss due to solar wind erosion (magnetosphere gating)

- Implement calculate_solar_wind_factor: scales with stellar distance (inverse square)
- Loss rate ~0 when magnetosphere_protection? == true, measurable when false
- Per-gas loss rates: lighter gases (H2, He) escape faster than heavier (CO2, H2O)
- Integrate with existing simulate_atmospheric_loss in AtmosphereSimulationService
- Add comprehensive tests: magnetosphere gating, per-gas differentiation, stellar distance scaling
- Mars scenario: unprotected loses ~0.1% per step, protected loses <0.001% per step"
```

Then move task file to completed:

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md

git add projects/galaxy_game/tasks/completed/2026-08/2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md
git commit -m "chore: move 2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION.md to completed/"
```

---

## Stop Conditions — escalate to user immediately if:
- `calculate_solar_wind_factor` method doesn't have access to magnetosphere flag or stellar distance data
- Atmosphere model doesn't support per-gas mass tracking (assumes all gases tracked together)
- Existing tests fail after implementation (indicates regression in related code)
- Per-gas loss differentiation causes pressure calculation errors (numerical instability)
- Mars scenario test shows unrealistic loss rates (>10% per step or <0.0001% per step)

---

## Acceptance Criteria
- [ ] `calculate_solar_wind_factor` returns ~0 when magnetosphere is active
- [ ] `calculate_solar_wind_factor` returns non-zero value proportional to 1/distance² when magnetosphere is inactive
- [ ] Per-gas loss rates implemented (H₂ > He > N₂ > O₂ > Ar > CO₂ > H₂O)
- [ ] Mars integration test shows measurable loss without magnetosphere, stable/rising pressure with magnetosphere
- [ ] All new tests pass (magnetosphere gating, per-gas differentiation, stellar distance scaling)
- [ ] No regressions in existing atmosphere/simulation tests
- [ ] Task file moved to completed/ with commit message

---

## Dependencies
**Blocked by**: none
**Blocks**: none directly (but unblocks magnetosphere-dependent terraforming features)
**Related tasks**: 
- Any magnetosphere deployment tasks
- Tracy's magnetosphere design doc (review for expected loss rates, equilibrium timing)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `atmosphere_simulation_service.rb` — replaced `calculate_solar_wind_factor` stub with full implementation (stellar distance scaling, magnetosphere gating)
- `atmosphere_simulation_service.rb` — extended `simulate_atmospheric_loss` to handle per-gas loss rates
- `atmosphere_simulation_service_spec.rb` — added 4 new test cases covering magnetosphere gating, per-gas differentiation, stellar distance scaling

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary
*Filled in at end of session*

HANDOFF SUMMARY: AtmosphereSimulationService extended with magnetosphere-gated erosion | Per-gas loss rates (lighter gases escape faster) | Stellar distance scaling (inverse square) | 4 new tests passing | Mars unprotected loses ~0.1% per step, protected <0.001%
