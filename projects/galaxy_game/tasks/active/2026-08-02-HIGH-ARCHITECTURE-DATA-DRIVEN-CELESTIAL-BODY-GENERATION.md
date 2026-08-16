---
status: active
priority: HIGH
type: architecture
system_domain: TERRA_SIM
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-02
updated: 2026-08-16
reopened: 2026-08-15
reopened_reason: "Fabricated completion report — see NEEDS_REVIEW #7 below"
estimated_effort: 4-5 hours
blocker_for:
  - 2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION
---

# Task: Data-Driven Celestial Body Generation — Remove Code Hardcoding

> **⚠️ REOPENED — Completion claims were FALSE**
> 
> Independent verification (2026-08-15) found:
> - `calculate_magnetosphere_strength()` is a **stub** (baseline + 0.0 + 0.0 + 0.0), NOT the claimed sigmoid-based core-state/dynamo gate
> - Test count was **30/0**, not the claimed 40/0 — 10 tests missing
> - sol-complete.json values (Earth=1.0, Venus=0.3, Mars=0.0) are correct ✅
> - No Topaz references in app code ✅
> 
> See NEEDS_REVIEW #7 for full details. This task needs a fresh re-scope before any implementation.

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION.md \
         projects/galaxy_game/tasks/active/2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Context

**Problem**: The game is embedding world-specific data into Ruby code instead of reading from JSON data files. This violates the design principle that "everything should be data-driven, no names/values hardcoded in the codebase."

**Current Issues**:
1. Topaz (prize planet) has hardcoded `magnetic_moment` and `tei_score` in `ProceduralGenerator` (lines 1260)
2. `SystemBuilderService` conditionally sets `strong_magnetosphere` based on ad-hoc thresholds instead of reading a data value
3. `AtmosphereGeneratorService` receives boolean `has_magnetic_field` instead of numeric magnetosphere strength
4. Star distances, orbital parameters, and other world attributes scattered between JSON and Ruby calculations
5. No way to properly support Venus's induced magnetosphere (0.3) vs Mars bare atmosphere (0.0) vs Earth (1.0) without code changes
6. **Mars formula bug** — `calculate_magnetosphere_strength()` produces ~0.47 for dead-core bodies when design requires ~0.0 (Mars = flagship zero-shielding world). The age/rotation/mass factors don't account for core-state/dynamo threshold: a body with a dead core should decay to ~0.0 regardless of mass/rotation. Found post-close in 2026-08-04 Claude session (28/28 specs passed because they accepted ~0.47). **Must fix before JSON extraction** or the bad formula will be encoded into sol-complete.json.

**Goal**: Make celestial body generation fully data-driven so:
- All world-specific values (including magnetosphere strength, atmospheric composition modifiers, geological features) live in JSON
- Ruby code reads values, applies physics, doesn't store arbitrary data
- New worlds can be added by editing JSON only
- Game data is version-controlled and auditable

## Architecture Gotchas

**1. Circular Dependency Risk**
- Don't move calculation logic into JSON (JSON should be data, not formulas)
- JSON stores *results* of calculations (e.g., magnetosphere_strength: 0.3), not the inputs
- Ruby code calculates during generation, stores results in JSON for persistence

**2. Migration Path**
- Sol system (Earth, Mars, Venus) is hardcoded + patched; must preserve it
- Newly generated systems start fresh with all fields calculated + stored in JSON
- Both paths converge on SystemBuilderService consuming complete JSON

**3. StarSim Incomplete Fields**
- Current `generate_procedural_terrestrial()` doesn't populate magnetosphere_strength
- Must add calculation before returning to make output complete

**4. Parent Magnetosphere Inheritance (Titan/Saturn & Ganymede/Jupiter Patterns)**
- **Real physics (Ganymede case)**: Ganymede has its own intrinsic field (0.15 strength) AND orbits inside Jupiter's massive field (1.0 strength, 7M km radius)
  - Result: Ganymede's atmosphere is protected by BOTH mechanisms, creating a nested/compound protection
  - Data structure: Store both intrinsic magnetosphere_strength AND orbital_distance_km to enable later calculation of total effective protection
- **Real physics (Titan case)**: Titan has NO intrinsic field (0.0) but orbits inside Saturn's field → inherits Saturn's protection scaled by distance
  - Result: Titan loses atmosphere slower than Mars (no field) but faster than Earth (has intrinsic field)
- **Data model needed**: 
  - Bodies with magnetosphere: `magnetosphere_strength` (0.0-1.0) + `magnetosphere_radius_km` (field extent)
  - Moons/satellites: `orbital_distance_km` from parent (already in orbits data, or add explicitly)
  - Parent reference: `parent_body` field identifies parent
- **Scope for THIS task**: Add `magnetosphere_strength`, `magnetosphere_radius_km`, and `orbital_distance_km` to JSON for all bodies (including moons)
- **Scope for NEXT task** (atmospheric loss): Implement parent protection calculation
  - If moon orbits < parent magnetosphere_radius, calculate inherited protection scaled by distance
  - Combine intrinsic + inherited: effective = [intrinsic + inherited, 1.0].min (cap at 1.0)
  - Examples: Ganymede's effective protection = [0.15 + (1.0 * distance_factor), 1.0].min ≈ 0.7-0.8
  - Titan's effective protection = [0.0 + (1.0 * distance_factor), 1.0].min ≈ 0.4-0.5
- **Not blocking THIS task**: Can be added to JSON now; consumed later when atmospheric loss task implements it

## Files Involved

**Data Files**:
- [data/json-data/star_systems/sol-complete.json](data/json-data/star_systems/sol-complete.json) — Add magnetosphere_strength to all terrestrial planets; remove embedded Topaz patches

**Core Generation Services**:
- [galaxy_game/app/services/star_sim/procedural_generator.rb](galaxy_game/app/services/star_sim/procedural_generator.rb) (lines 1250-1270)
  - Remove hardcoded Topaz magnetic_moment and tei_score assignment
  - Add magnetosphere_strength calculation to generate_procedural_terrestrial()
  
- [galaxy_game/app/services/star_sim/planet_builder.rb](galaxy_game/app/services/star_sim/planet_builder.rb)
  - Ensure build_data includes magnetosphere_strength if generated

**Integration/Consumer Services**:
- [galaxy_game/app/services/star_sim/system_builder_service.rb](galaxy_game/app/services/star_sim/system_builder_service.rb) (line 417)
  - Remove conditional magnetic_field check
  - Read magnetosphere_strength directly from body_data
  - Store in properties as-is (don't calculate, just pass through)

- [galaxy_game/app/services/star_sim/atmosphere_generator_service.rb](galaxy_game/app/services/star_sim/atmosphere_generator_service.rb) (lines 12, 28, 140-155)
  - Change `has_magnetic_field` boolean parameter to `magnetosphere_strength` float (0.0-1.0)
  - Update escape calculations to use strength value instead of binary check

**Tests** (create if not exist):
- spec/services/star_sim/procedural_generator_spec.rb
  - Verify magnetosphere_strength is calculated and in range [0.0, 1.0]
  - Verify Mars (0.0), Venus (0.3), Earth (1.0) values correctly set in sol-complete.json
  
- spec/services/star_sim/system_builder_service_spec.rb
  - Verify magnetosphere_strength from JSON is stored in properties['magnetosphere_strength']
  - Verify no ad-hoc boolean logic applied to terrestrial planets

---

## REQUIRED: Synthesis Report

**[SYNTHESIS REPORT TEMPLATE — Fill this in BEFORE making code changes]**

**Objective**: Confirm understanding of data flow and identify any hidden hardcodings

**Steps**:
1. Read sol-complete.json: Search for "Topaz", "magnetic_moment", "tei_score" — document where values appear
2. Read ProceduralGenerator (lines 1250-1280): List all hardcoded values being set for Topaz
3. Search codebase: `grep -r "Topaz\|strong_magnetosphere\|has_magnetic_field" galaxy_game/app --include="*.rb"` — document all references
4. Trace SystemBuilderService (line 417): Follow the conditional logic; what triggers it? Who passes body_data[:magnetic_field]?
5. Trace AtmosphereGeneratorService: Find all callers; what values are passed for has_magnetic_field? (Expected: true/false; should be 0.0-1.0)
6. Identify other hardcoded values: Search for planet-specific conditionals (e.g., `if body_data[:name] == 'Mars'`)

**Document findings in one section below before proceeding to implementation**:
```
## Synthesis Findings

### Sol System Hardcoding
- [List all hardcoded assignments for Topaz, Mars, Venus, Earth]

### Procedural Generation Gaps
- [Fields NOT calculated in generate_procedural_terrestrial()]

### Consumer Dependencies
- [Who calls AtmosphereGeneratorService? What binary values are passed?]

### Data Flow Breaks
- [Where does data-driven pattern break down?]
```

---

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from backlog/phase05-luna-calibration/ → active/
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code: `git add ... && git commit -m "Task moved to active"`
4. Complete Synthesis Report above (document current state)
5. Verify synthesis against code by running grep commands in Step 1

### Step 1: Update sol-complete.json
Add `magnetosphere_strength` field to all terrestrial planets, plus `magnetosphere_radius_km` for those with fields.

**Changes**:
- Earth: Add `"magnetosphere_strength": 1.0` and `"magnetosphere_radius_km": 60000` (strong intrinsic dipole; extends ~60,000 km)
- Venus: Add `"magnetosphere_strength": 0.3` and `"magnetosphere_radius_km": 500` (induced field; weaker, shorter range)
- Mars: Add `"magnetosphere_strength": 0.0` (no magnetosphere, omit radius field since irrelevant)
- Remove all Ruby patches for Topaz (if they exist in JSON; if in Ruby code, handle in Step 3)

**Why magnetosphere_radius_km?**
Needed for parent magnetosphere protection calculation (e.g., does Saturn's field reach Titan's orbit?). Will be consumed by atmospheric loss task later.

**Verification**:
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rails runner '
  ["Earth", "Venus", "Mars"].each do |name|
    system = JSON.parse(File.read("data/json-data/star_systems/sol-complete.json"))
    planet = system["celestial_bodies"]["terrestrial_planets"].find { |p| p["name"] == name }
    strength = planet&.dig("magnetosphere_strength")
    radius = planet&.dig("magnetosphere_radius_km")
    puts "#{name}: strength=#{strength}, radius_km=#{radius}"
  end
'
```

### Step 2: Refactor ProceduralGenerator — Add Magnetosphere Calculation
Implement `calculate_magnetosphere_strength()` and `calculate_magnetosphere_radius()` methods; call them in `generate_procedural_terrestrial()`.

**Add new private methods** (around line 1200):
```ruby
def calculate_magnetosphere_strength(mass_kg, rotation_period_hours = 24, age_years = 4.5e9)
  earth_mass_kg = 5.972e24
  
  # Mass factor: larger planets have stronger dynamos
  mass_ratio = mass_kg / earth_mass_kg
  mass_factor = mass_ratio ** 0.33
  
  # Rotation factor: faster rotation = stronger field
  # Baseline: Earth ~24 hours; faster rotation amplifies field
  rotation_factor = [24.0 / [rotation_period_hours, 6.0].max, 3.0].min  # Cap at 3x
  
  # Age factor: older planets have cooler cores, weaker fields
  # Half-life decay: 50% strength loss per ~4.5 billion years
  age_factor = Math.exp(-age_years / 9e9)
  
  # Combine factors into 0.0-1.0 scale
  base_strength = mass_factor * rotation_factor * age_factor
  
  # Clamp to [0.0, 1.0]
  [[base_strength, 0.0].max, 1.0].min
end

def calculate_magnetosphere_radius(mass_kg, magnetosphere_strength)
  # Magnetosphere radius scales with strength and mass
  # Earth: ~60,000 km; weaker/smaller planets: shorter range
  # No magnetosphere (strength 0.0): radius undefined/omit
  return nil if magnetosphere_strength < 0.01
  
  earth_mass_kg = 5.972e24
  mass_ratio = mass_kg / earth_mass_kg
  
  # Base radius for Earth-like: 60,000 km
  # Scale by mass (larger = extends further) and strength (stronger = reaches further)
  base_radius = 60000.0
  radius = base_radius * (mass_ratio ** 0.25) * (magnetosphere_strength ** 0.5)
  
  radius.round(0)
end
```

**Update `generate_procedural_terrestrial()`** (around line 450):
- Add rotation_period to generated data (default 24-48 hours for Earth-like)
- Calculate magnetosphere_strength using mass and rotation
- Calculate magnetosphere_radius only if strength > 0.0
- Include in returned hash:
  ```ruby
  "magnetosphere_strength" => calculated_strength,
  "magnetosphere_radius_km" => calculated_radius  # Omit if nil
  ```

**Remove Topaz hardcoding** (line 1260):
- Delete: `planet['magnetic_moment'] = 0.82`
- Delete: `planet['tei_score'] = 0.88`
- If Topaz needs these values, they should be in sol-complete.json data

### Step 3: Fix Procedural Moon Generation — Support Compound Magnetosphere
**File**: `app/services/star_sim/procedural_generator.rb` (method `generate_moons_for_planet`, line 1077)

**Problem**: Moon generation doesn't support compound magnetosphere protection (intrinsic + parent):
- Moon has `"orbiting_body"` (text) but NOT `"parent_body"` field
- Orbital distance is nested in `"orbits"` array, NOT at top level as `orbital_distance_km`
- NO magnetosphere generation for moons (even though architecture now supports it)
- **Gotcha**: If a moon is randomly generated with intrinsic magnetosphere (e.g., Ganymede-like, 0.22 strength), it needs parent_body reference to inherit parent protection too

**Solution**:
1. **Add `parent_body` field** to moon_data:
   ```ruby
   moon_data = {
     "name" => moon_name,
     "parent_body" => planet_data["name"],  # NEW: Reference to parent
     "orbital_distance_km" => orbital_distance / 1000.0,  # NEW: Top-level field
     # ... rest of fields ...
   }
   ```

2. **Extract orbital_distance to top level**:
   - Currently: `orbital_distance = rand(2..50) * planet_data["radius"]` (local variable)
   - Add to moon_data: `"orbital_distance_km" => orbital_distance / 1000.0`

3. **Add magnetosphere calculation for moons** (optional but recommended):
   ```ruby
   # Some moons have intrinsic magnetospheres (rare, like Ganymede)
   # Only ~1% chance, but allows for realistic cases
   if rand < 0.01
     moon_magnetosphere_strength = calculate_magnetosphere_strength(
       moon_data["mass"],
       24,  # Moons rotate with parent
       4.5e9  # Assume solar system age
     )
     if moon_magnetosphere_strength > 0.01
       moon_data["magnetosphere_strength"] = moon_magnetosphere_strength
       moon_data["magnetosphere_radius_km"] = calculate_magnetosphere_radius(
         moon_data["mass"],
         moon_magnetosphere_strength
       )
     end
   end
   ```

**Verification**:
- Procedurally generated moon with intrinsic field has all three fields:
  - `parent_body: "Parent Name"` (enables parent protection inheritance calculation)
  - `magnetosphere_strength: X` (own protection)
  - `orbital_distance_km: Y` (for distance-based scaling of inherited protection)
- Example: moon with 0.15 intrinsic can inherit Saturn's protection if orbit < Saturn's radius
- Data structure supports future compound protection calculation in Task 2

### Step 4: Refactor SystemBuilderService — Read from JSON, Don't Calculate
**Replace lines 415-419** (terrestrial planet properties logic):

**Old code**:
```ruby
if body_data[:magnetic_field].to_f > 30
  attrs[:properties]['strong_magnetosphere'] = true
end
```

**New code**:
```ruby
# Read magnetosphere_strength directly from generated data (0.0-1.0)
if body_data[:magnetosphere_strength].present?
  attrs[:properties]['magnetosphere_strength'] = body_data[:magnetosphere_strength].to_f
elsif body_data[:magnetic_field].present?
  # Fallback for legacy data: convert old magnetic_field value to strength scale
  # Assume magnetic_field > 30 means Earth-like (1.0)
  attrs[:properties]['magnetosphere_strength'] = body_data[:magnetic_field].to_f > 30 ? 1.0 : 0.0
end
```

**Verification**: After refactor, verify that:
- No conditional logic based on specific planet names
- properties['magnetosphere_strength'] is numeric, not boolean
- Both JSON-generated and legacy data paths work

### Step 5: Refactor AtmosphereGeneratorService — Accept Numeric Strength
**Update method signature** (line 12):
```ruby
def generate_composition_for_body(
  name,
  surface_temp_override,
  mass,
  radius,
  orbital_distance,
  stellar_type = nil,
  magnetosphere_strength = 0.0  # Changed from has_magnetic_field (boolean)
)
```

**Update escape calculation** (lines 140-155):
Replace binary checks with scaled loss:
```ruby
def model_atmospheric_escape(composition, mass, radius, surface_temp, stellar_type, magnetosphere_strength)
  # magnetosphere_strength: 0.0 (no protection) to 1.0 (full protection)
  
  composition.each do |gas, percentage|
    escape_probability = calculate_escape_velocity(gas, surface_temp, mass, radius)
    
    gas_formula = gas["formula"] || gas
    
    # Apply magnetosphere protection as a reduction in escape probability
    # Strong magnetosphere (1.0) blocks most losses; no field (0.0) allows all losses
    protection_factor = 1.0 - magnetosphere_strength  # 0.0 (protected) to 1.0 (exposed)
    protected_escape = escape_probability * protection_factor
    
    if gas_formula == "H2" && protected_escape > 0.1
      composition[gas]["removed"] = true
    elsif gas_formula == "He" && protected_escape > 0.2
      composition[gas]["removed"] = true
    end
  end
  
  composition
end
```

**Update all callers**:
- Find where `@atmosphere_generator.generate_composition_for_body()` is called
- Replace `has_magnetic_field` boolean with actual `magnetosphere_strength` value from body_data
- Example (ProceduralGenerator line ~545):
  ```ruby
  # Old: rand < 0.5  (random boolean)
  # New: body_data[:magnetosphere_strength] || 0.0  (actual value)
  magnetosphere_strength = body_data[:magnetosphere_strength] || 0.0
  atmosphere_data = @atmosphere_generator.generate_composition_for_body(
    planet_name,
    planet_data["surface_temperature"],
    planet_data["mass"],
    planet_data["radius"],
    orbital_parameters[:semi_major_axis_au],
    star_for_planets&.type,
    magnetosphere_strength  # Pass numeric value
  )
  ```

### Step 5: Write Comprehensive Tests

**Create spec/services/star_sim/procedural_generator_magnetosphere_spec.rb**:
```ruby
describe StarSim::ProceduralGenerator do
  let(:generator) { described_class.new }
  
  describe '#calculate_magnetosphere_strength' do
    it 'returns 1.0 for Earth-mass planet at ~4.5 Gy age' do
      earth_mass = 5.972e24
      strength = generator.send(:calculate_magnetosphere_strength, earth_mass, 24, 4.5e9)
      expect(strength).to be_within(0.1).of(1.0)
    end
    
    it 'returns 0.0 for very old planet with no rotation' do
      strength = generator.send(:calculate_magnetosphere_strength, 1e24, 1000, 1e10)
      expect(strength).to be_between(0.0, 0.2)
    end
    
    it 'returns value in [0.0, 1.0] for all inputs' do
      50.times do
        mass = rand(0.1e24..10e24)
        rotation = rand(6..100)
        age = rand(0..10e9)
        strength = generator.send(:calculate_magnetosphere_strength, mass, rotation, age)
        expect(strength).to be >= 0.0
        expect(strength).to be <= 1.0
      end
    end
  end
  
  describe '#calculate_magnetosphere_radius' do
    it 'returns ~60,000 km for Earth-strength field' do
      earth_mass = 5.972e24
      radius = generator.send(:calculate_magnetosphere_radius, earth_mass, 1.0)
      expect(radius).to be_within(10000).of(60000)
    end
    
    it 'returns nil for no magnetosphere (strength 0.0)' do
      radius = generator.send(:calculate_magnetosphere_radius, 1e24, 0.0)
      expect(radius).to be_nil
    end
    
    it 'returns nil for very weak field (strength < 0.01)' do
      radius = generator.send(:calculate_magnetosphere_radius, 1e24, 0.005)
      expect(radius).to be_nil
    end
    
    it 'scales with mass: larger planets extend further' do
      weak_mass = 0.5e24
      strong_mass = 5.0e24
      
      weak_radius = generator.send(:calculate_magnetosphere_radius, weak_mass, 1.0)
      strong_radius = generator.send(:calculate_magnetosphere_radius, strong_mass, 1.0)
      
      expect(strong_radius).to be > weak_radius
    end
    
    it 'returns numeric km value for valid inputs' do
      radius = generator.send(:calculate_magnetosphere_radius, 1e24, 0.5)
      expect(radius).to be_a(Integer)
      expect(radius).to be > 0
    end
  end
  
  describe '#generate_procedural_terrestrial' do
    it 'includes magnetosphere_strength in output' do
      planet_data = generator.send(:generate_procedural_terrestrial, 'Test-P1', 'TEST-P1', 0)
      expect(planet_data).to have_key('magnetosphere_strength')
      expect(planet_data['magnetosphere_strength']).to be_a(Numeric)
    end
    
    it 'includes magnetosphere_radius_km if strength > 0.0' do
      planet_data = generator.send(:generate_procedural_terrestrial, 'Test-P1', 'TEST-P1', 0)
      if planet_data['magnetosphere_strength'] > 0.01
        expect(planet_data).to have_key('magnetosphere_radius_km')
        expect(planet_data['magnetosphere_radius_km']).to be_a(Integer)
      end
    end
    
    it 'omits magnetosphere_radius_km if strength is 0.0' do
      # Mock to generate strength 0.0
      allow(generator).to receive(:calculate_magnetosphere_strength).and_return(0.0)
      planet_data = generator.send(:generate_procedural_terrestrial, 'Test-P1', 'TEST-P1', 0)
      expect(planet_data['magnetosphere_radius_km']).to be_nil
    end
  end
end
```

**Create spec/services/star_sim/data_driven_generation_spec.rb**:
```ruby
describe 'Data-Driven Celestial Body Generation' do
  let(:builder) { StarSim::SystemBuilderService.new(name: 'sol') }
  
  before do
    # Load sol-complete.json
    @system_data = builder.send(:@system_data)
  end
  
  describe 'Sol system magnetosphere values' do
    it 'has Earth with magnetosphere_strength 1.0 and radius ~60000 km' do
      earth = @system_data[:celestial_bodies][:terrestrial_planets]&.find { |p| p[:name] == 'Earth' }
      expect(earth[:magnetosphere_strength]).to eq(1.0)
      expect(earth[:magnetosphere_radius_km]).to be_within(5000).of(60000)
    end
    
    it 'has Venus with magnetosphere_strength 0.3 and smaller radius' do
      venus = @system_data[:celestial_bodies][:terrestrial_planets]&.find { |p| p[:name] == 'Venus' }
      expect(venus[:magnetosphere_strength]).to eq(0.3)
      expect(venus[:magnetosphere_radius_km]).to be < 10000  # Induced field, shorter range
    end
    
    it 'has Mars with magnetosphere_strength 0.0 and no radius' do
      mars = @system_data[:celestial_bodies][:terrestrial_planets]&.find { |p| p[:name] == 'Mars' }
      expect(mars[:magnetosphere_strength]).to eq(0.0)
      expect(mars[:magnetosphere_radius_km]).to be_nil
    end
  end
  
  describe 'SystemBuilderService data passthrough' do
    it 'stores magnetosphere_strength in celestial_body.properties' do
      body = CelestialBodies::CelestialBody.find_by(name: 'Earth')
      expect(body.properties['magnetosphere_strength']).to eq(1.0)
    end
    
    it 'stores magnetosphere_radius_km in celestial_body.properties if present' do
      body = CelestialBodies::CelestialBody.find_by(name: 'Earth')
      expect(body.properties['magnetosphere_radius_km']).to be_within(5000).of(60000)
    end
    
    it 'does not use binary strong_magnetosphere flag' do
      body = CelestialBodies::CelestialBody.find_by(name: 'Earth')
      expect(body.properties).not_to have_key('strong_magnetosphere')
    end
  end
  
  describe 'Parent magnetosphere data structure' do
    it 'supports parent_body_name for moons (Titan orbiting Saturn)' do
      # Example: Titan should have parent_body_name: 'Saturn'
      # This enables later calculation of inherited protection
      # Not implemented in this task; just verify JSON structure supports it
      titan = @system_data[:celestial_bodies][:moons]&.find { |m| m[:name] == 'Titan' }
      if titan
        expect(titan).to have_key(:parent_body_name) if titan[:parent_body_name]
      end
    end
    
    it 'has Ganymede with intrinsic magnetosphere_strength 0.15 and parent Jupiter' do
      ganymede = @system_data[:celestial_bodies][:moons]&.find { |m| m[:name] == 'Ganymede' }
      expect(ganymede[:magnetosphere_strength]).to eq(0.15)
      expect(ganymede[:magnetosphere_radius_km]).to eq(500)
      expect(ganymede[:orbital_distance_km]).to eq(1070400)
      expect(ganymede[:parent_body]).to eq('Jupiter')
    end
    
    it 'has Jupiter with strong magnetosphere protecting inner moons' do
      jupiter = @system_data[:celestial_bodies][:gas_giants]&.find { |p| p[:name] == 'Jupiter' }
      expect(jupiter[:magnetosphere_strength]).to eq(1.0)
      expect(jupiter[:magnetosphere_radius_km]).to eq(7000000)  # 7M km field extent
      
      # Ganymede orbits at 1.07M km, well inside Jupiter's 7M km magnetosphere
      ganymede_orbit_km = 1070400
      expect(ganymede_orbit_km).to be < jupiter[:magnetosphere_radius_km]
    end
  end
  
  describe 'AtmosphereGeneratorService integration' do
    it 'accepts numeric magnetosphere_strength (not boolean)' do
      service = StarSim::AtmosphereGeneratorService.new
      expect {
        service.generate_composition_for_body('Test', 300, 1e24, 1e6, 1.0, 'G', 0.5)
      }.not_to raise_error
    end
  end
end
```

### Step 6: Run Comprehensive Tests via Docker
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec \
  spec/services/star_sim/procedural_generator_magnetosphere_spec.rb \
  spec/services/star_sim/data_driven_generation_spec.rb \
  --format documentation
```

**Expected**: All tests pass (0 failures)

### Step 7: Manual Integration Test — Sol System Load
Verify the complete system loads with data-driven generation:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rails runner '
  puts "=== Loading Sol System ==="
  builder = StarSim::SystemBuilderService.new(name: "sol", debug_mode: true)
  solar_system = builder.build!
  
  puts "\n=== Verifying Magnetosphere Values ==="
  ["Earth", "Venus", "Mars"].each do |name|
    body = CelestialBodies::CelestialBody.find_by(name: name)
    strength = body.properties["magnetosphere_strength"]
    puts "#{name}: magnetosphere_strength = #{strength} (type: #{strength.class})"
  end
  
  puts "\n=== Complete ==="
'
```

**Expected Output**:
```
Earth: magnetosphere_strength = 1.0 (type: Float)
Venus: magnetosphere_strength = 0.3 (type: Float)
Mars: magnetosphere_strength = 0.0 (type: Float)
```

### Step 8: Code Review Checklist
- [ ] No planet-specific conditionals in Ruby code (no `if name == 'Topaz'`)
- [ ] All properties stored as data-driven values, not calculated in code
- [ ] sol-complete.json updated with all necessary fields (magnetosphere_strength, others if found in synthesis)
- [ ] AtmosphereGeneratorService updated to accept numeric magnetosphere_strength
- [ ] Procedurally generated moons have parent_body + orbital_distance_km fields
- [ ] Tests pass (RSpec) and manual integration confirms data flow
- [ ] Git diff shows removals only where intended (old hardcode deleted)

### Step 9: Commit & Document
```bash
git add data/json-data/star_systems/sol-complete.json \
        galaxy_game/app/services/star_sim/procedural_generator.rb \
        galaxy_game/app/services/star_sim/system_builder_service.rb \
        galaxy_game/app/services/star_sim/atmosphere_generator_service.rb \
        spec/services/star_sim/

git commit -m "refactor: Data-driven celestial body generation — remove hardcoded values

- Add magnetosphere_strength (0.0-1.0) to all terrestrial planets in sol-complete.json
- Remove Topaz hardcoded magnetic_moment/tei_score patches from ProceduralGenerator
- Add calculate_magnetosphere_strength() method (mass/rotation/age factors)
- Update SystemBuilderService to read magnetosphere_strength from JSON (not calculate)
- Refactor AtmosphereGeneratorService to accept numeric strength (not boolean)
- Add comprehensive tests for data-driven flow (Mars 0.0, Venus 0.3, Earth 1.0)
- All world attributes now data-driven; no hardcoding in codebase"
```

---

## Stop Conditions

**DO NOT proceed beyond current step if**:
- Synthesis report shows additional hardcoded values not listed above (report them first)
- Tests fail (run diagnostics before committing)
- Any planet name appears in conditional logic after refactor
- `properties['magnetosphere_strength']` is not a Float (should never be boolean)

**STOP and escalate if**:
- sol-complete.json doesn't have terrestrial_planets section
- SystemBuilderService can't find magnetosphere_strength in body_data
- AtmosphereGeneratorService callers can't be updated to pass numeric value

---

## Acceptance Criteria

- [ ] `sol-complete.json` updated with `magnetosphere_strength` for all terrestrial planets (Earth=1.0, Venus=0.3, Mars=0.0)
- [ ] Topaz hardcoded `magnetic_moment`/`tei_score` removed from `ProceduralGenerator`
- [ ] `calculate_magnetosphere_strength()` produces **~0.0 for dead-core bodies** (Mars-class: mass < 1e24 kg, age > 4.5 Gy)
- [ ] `calculate_magnetosphere_strength()` produces **~1.0 for Earth-mass planets at ~4.5 Gy age**
- [ ] `calculate_magnetosphere_strength()` clamps to [0.0, 1.0] for all inputs
- [ ] Core-state/dynamo threshold gate implemented: dead-core bodies decay to ~0.0 regardless of mass/rotation
- [ ] `SystemBuilderService` reads `magnetosphere_strength` from JSON (no planet-specific conditionals)
- [ ] `AtmosphereGeneratorService` accepts numeric strength (not boolean `has_magnetic_field`)
- [ ] Procedurally generated moons have `parent_body` + `orbital_distance_km` fields
- [ ] All RSpec tests pass (0 failures)
- [ ] Manual integration confirms: Earth=1.0, Venus=0.3, Mars=0.0
- [ ] No hardcoded planet names in Ruby code (grep confirmed)
- [ ] No binary `strong_magnetosphere` flags — all values numeric 0.0–1.0

---


## Note on Prior Completion Claims (removed 2026-08-16)

This task previously contained a filled-in Completion Report and a second,
fully-checked Acceptance Criteria section claiming 40/40 tests passing and a
working sigmoid-based core-state/dynamo gate. **Both were false** — independent
verification found the real test count was 30/0 and `calculate_magnetosphere_strength()`
was still a stub (baseline + three permanently-zeroed modifiers). That content
has been removed from this file rather than left in place, since a filled-in
completion report sitting in a reopened task risks being read as real progress.
See NEEDS_REVIEW #7 (RESOLVED) for the full fabrication finding. Do not treat
any prior claim about this task's completion as reliable — only the real,
independently-verified state matters going forward.

---

## Handoff Summary

This task establishes the **data-driven architecture foundation** required for proper magnetosphere-based atmospheric loss modeling.

**Blocks**: 2026-08-02-HIGH-FEATURE-ATMOSPHERIC-LOSS-SOLAR-WIND-EROSION (can't proceed without this)

**Depends On**: None (no code dependencies; requires only JSON editing and refactoring existing Ruby)

**Unblocks**: Any future world-specific features (can be added to JSON without code changes)

**Why Now**: Venus/Mars magnetosphere differences require numeric gradients, not boolean flags. Current code can't express this. This refactor enables it and removes all hardcoded values per design principle.

**Effort**: 4-5 hours (calculation methods, test setup, integration verification)

**Risk**: Low — backward-compatible with legacy data; new hardcoding violations will be caught by tests
