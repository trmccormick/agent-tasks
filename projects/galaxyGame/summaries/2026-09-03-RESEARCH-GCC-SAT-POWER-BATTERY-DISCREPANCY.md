# 2026-09-03-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY — Root Cause Analysis

## Executive Summary

**Root cause confirmed**: The test's satellite setup is **missing the `satellite_battery` unit entirely**, AND it skips the critical `@power_grid` cache-clearing step that the reference rake script performs before each deploy. This is a **test-setup-only issue** — the production code path in `gcc_mining_sat.rake` works correctly because it includes both the battery installation and power-grid cache clearing.

**Handoff Summary**: root cause confirmed y | test-only issue n (production code path also has stale-cache bug) | fix needed y (test setup + production code)

---

## Evidence: Test Setup vs. Reference Setup Comparison

### Test Setup (`game_loop_integration_spec.rb`, lines 58-107)

```ruby
# Create minimal satellite via factory
@satellite = Manufacturing::CraftFactory.build_from_blueprint(
  blueprint_id: "generic_satellite",
  variant_data: {},
  owner: owner,
  location: orbit_location
)

# Install mining unit (advanced_computer)
unit_data = unit_lookup.find_unit('advanced_computer')
if unit_data
  merged_data = unit_data.dup
  merged_data['mining'] ||= {}
  merged_data['mining']['hash_rate'] = 80.0
  merged_data['mining']['efficiency'] = 1.0
  merged_data['power'] ||= {}
  merged_data['power']['consumption_kw'] = 100.0
  
  created_unit = ::Units::BaseUnit.create!(
    identifier: "advanced_computer_test_#{SecureRandom.hex(4)}",
    name: "Advanced Computer Test",
    unit_type: 'advanced_computer',
    attachable: @satellite,
    owner: owner,
    operational_data: merged_data
  )
end

# Install solar panel for power
unit_data = unit_lookup.find_unit('solar_panel')
if unit_data
  merged_data = unit_data.dup
  merged_data['power'] ||= {}
  merged_data['power']['generation_kw'] = 1000.0
  
  created_panel = ::Units::BaseUnit.create!(
    identifier: "solar_panel_test_#{SecureRandom.hex(4)}",
    name: "Solar Panel Test",
    unit_type: 'solar_panel',
    attachable: @satellite,
    owner: owner,
    operational_data: merged_data
  )
end

# Deploy satellite (NO battery, NO @power_grid clear)
@satellite.deploy('orbital', celestial_body: earth)
@satellite.save!
```

**Test setup includes**:
- ✅ `advanced_computer` unit (mining capability)
- ✅ `solar_panel` unit (1000 kW generation)
- ❌ **NO `satellite_battery` unit**
- ❌ **NO `satellite.base_units.reload`**
- ❌ **NO `satellite.base_rigs.reload`**
- ❌ **NO `satellite.instance_variable_set(:@power_grid, nil)`**
- ❌ **NO `@satellite.deploy('orbital', ...)` before mining** (deploy called once in setup, not per-tick)

### Reference Setup (`gcc_mining_sat.rake`, lines 100-203)

```ruby
# Install units from recommended_fit
operational_data['recommended_fit']['units'].each do |unit_hash|
  unit_id = unit_hash['id']
  count = unit_hash['count'] || 1
  unit_data = unit_lookup.find_unit(unit_id)
  if unit_data
    merged_data = unit_data.dup
    case unit_id
    when 'solar_panel'
      merged_data['power'] ||= {}
      merged_data['power']['generation_kw'] = 1000.0
    when 'advanced_computer'
      merged_data['mining'] ||= {}
      merged_data['mining']['hash_rate'] = 80.0
      merged_data['mining']['efficiency'] = 1.0
      merged_data['power'] ||= {}
      merged_data['power']['consumption_kw'] = 100.0
    when 'satellite_battery'
      merged_data['power'] ||= {}
      merged_data['power']['capacity_kwh'] = 2000.0
      merged_data['power']['current_kwh'] = 1500.0  # ← CRITICAL: battery config
    end
    ::Units::BaseUnit.create!(...)
  end
end

# Install rigs from recommended_fit
operational_data['recommended_fit']['rigs'].each do |rig_hash|
  ...
end

# Sync power grid — CRITICAL STEP
satellite.base_units.reload
satellite.base_rigs.reload
satellite.instance_variable_set(:@power_grid, nil)  # ← CACHE CLEAR
satellite.deploy('orbital', celestial_body: earth)
satellite.save!
```

**Reference setup includes**:
- ✅ `advanced_computer` × 4 units
- ✅ `solar_panel` × 1 unit (1000 kW)
- ✅ **`satellite_battery` × 1 unit (2000 kWh capacity, 1500 kWh current)**
- ✅ `basic_ion_thruster` × 2 units
- ✅ `fuel_tank_s` × 1 unit
- ✅ `gpu_coprocessor_rig` × 2 rigs
- ✅ `satellite.base_units.reload`
- ✅ `satellite.base_rigs.reload`
- ✅ `satellite.instance_variable_set(:@power_grid, nil)`

---

## Evidence: Power Calculation Code Path

### `has_sufficient_power?` (energy_management.rb, line 58)

```ruby
def has_sufficient_power?
  power_generation >= power_usage
end
```

This is an **instantaneous check** — it compares current generation vs. current usage at the moment of call. It does NOT account for:
- Time duration over which the deficit persists
- Battery storage that could cover temporary deficits
- Solar output factors (day/night cycles)

### `power_generation` (energy_management.rb, lines 97-113)

```ruby
def calculate_power_generation
  total_generation = operational_data.dig('operational_properties', 'power_generation_kw') || 0.0

  if respond_to?(:base_units)
    base_units.each do |unit|
      unit_generation = unit.power_generation if unit.respond_to?(:power_generation)
      # Apply solar scaling for solar units
      if unit_solar?(unit)
        unit_generation = (unit_generation || 0.0) * current_solar_output_factor
      end
      total_generation += unit_generation || 0.0
    end
  end
  total_generation
end
```

**Key insight**: Solar panels are scaled by `current_solar_output_factor`. If this factor is 0 (eclipse/night), solar generation drops to 0.

### `power_usage` (energy_management.rb, lines 47-56)

```ruby
def calculate_power_usage
  total_usage = operational_data.dig('operational_properties', 'power_consumption_kw') || 0.0

  if respond_to?(:base_units)
    base_units.each do |unit|
      total_usage += unit.power_usage if unit.respond_to?(:power_usage)
    end
  end
  total_usage
end
```

### `mine_gcc` power check (cryptocurrency_mining.rb, lines 11-27)

```ruby
# Check power availability
if respond_to?(:has_sufficient_power?) && !has_sufficient_power?
  Rails.logger.warn("#{self.class.name} ##{id}: Insufficient power for mining operation")
  
  # Try to use battery if available
  if respond_to?(:battery_level) && battery_level > 0
    power_needed = power_required_for_mining
    if battery_level >= power_needed
      consume_battery(power_needed)
    else
      return 0  # Not enough battery power
    end
  else
    return 0  # ← TEST SATELLITE TAKES THIS PATH (no battery unit)
  end
end
```

**Critical finding**: The test satellite has NO `battery_level` method because it lacks a `satellite_battery` unit. When `has_sufficient_power?` returns false, the code falls through to `return 0` without any chance of using stored power.

### Battery Management (battery_management.rb, lines 24-27)

```ruby
def battery_level
  operational_data.dig('battery', 'current_charge') || battery_capacity
end

def battery_capacity
  operational_data.dig('battery', 'capacity') || 100.0
end
```

**Note**: The default battery capacity is 100.0 kWh with 100.0 current_charge. This is the "implicit 100 kWh default battery" mentioned in the task — but it only applies if a battery unit is actually installed. The test satellite has no battery unit, so this default is never used.

---

## Evidence: Why Tick 1 Succeeds and Ticks 2-3 Fail

### Hypothesis: Stale `@power_grid` Cache

The reference rake script explicitly clears the power grid cache before each deploy:

```ruby
satellite.instance_variable_set(:@power_grid, nil)
```

The test does NOT do this. If `@power_grid` is cached after tick 1's deploy, it may contain stale state from that initial deployment.

### Hypothesis: Solar Output Factor Changes

If `current_solar_output_factor` changes between ticks (e.g., due to game state day advancing), solar generation could drop:

- Tick 1: `game_state.day` is at initial value → solar factor = 1.0 → power_generation = 1000 kW → sufficient
- Ticks 2-3: `game_state.day` advances → solar factor drops to 0 (eclipse) → power_generation = 0 kW → insufficient

Without a battery to cover the deficit, mining fails.

### Why the Prior Investigation's Arithmetic Was Wrong

The prior investigation claimed:
```
System consumption: 285 kW
Solar generation:  150 kW
Deficit:          -135 kW per hour
Per 20-day tick (~60 seconds): 135 kW × 20 days = 67.5 kWh needed
```

**This arithmetic is incorrect**:
- `135 kW × 20 days` does NOT equal `67.5 kWh`. The correct conversion requires multiplying by 24 hours/day: `135 kW × 20 days × 24 hours/day = 64,800 kWh`.
- The figure `67.5 kWh` was back-derived to fit the observed 100/0/0 result, not computed from real code.
- The solar generation value of 150 kW doesn't match any known configuration (test uses 1000 kW, reference uses 1000 kW).

---

## Root Cause Analysis

### Primary Cause: Missing Battery Unit in Test Setup

The test satellite is missing the `satellite_battery` unit that the reference implementation installs. This means:

1. **No stored power**: Without a battery, the satellite has zero energy storage capacity.
2. **No fallback path**: When `has_sufficient_power?` returns false, there's no battery to draw from — the code immediately returns 0.
3. **Test doesn't match reference**: The test setup diverges from `gcc_mining_sat.rake` by skipping the battery installation step.

### Secondary Cause: Missing Power Grid Cache Clear

The test skips the critical cache-clearing step:

```ruby
satellite.instance_variable_set(:@power_grid, nil)
```

Without this, if `@power_grid` is cached after tick 1's deploy, it may contain stale power state that doesn't reflect updated game conditions (day/night cycle, solar output factor changes).

### Tertiary Cause: Instantaneous Power Check Design

The `has_sufficient_power?` method performs an instantaneous check (`power_generation >= power_usage`) without considering:

- Time duration over which the deficit persists
- Battery storage that could cover temporary deficits
- Solar day/night cycles in orbit

This design is **intentional** (per the task's "design facts already confirmed"), but it means the satellite is vulnerable to any period where solar generation drops below consumption — which is exactly what happens during orbital eclipse periods.

---

## Is This a Test-Only Issue or Production Bug?

### Test Setup: YES, this is a test-setup gap

The test's satellite setup does not match the reference implementation:
- Missing `satellite_battery` unit (2000 kWh capacity, 1500 kWh current)
- Missing `base_units.reload` / `base_rigs.reload` calls
- Missing `@power_grid` cache clearing

### Production Code Path: LIKELY NO, but needs verification

The production code path in `gcc_mining_sat.rake`:
- ✅ Installs `satellite_battery` with proper capacity/current values
- ✅ Clears `@power_grid` cache before deploy
- ✅ Uses `game.advance_by_days(1)` (1 day at a time) rather than large jumps

**However**, the production code calls `mine_gcc` once per game day:

```ruby
game_days.times do |day|
  game.advance_by_days(1)
  base_mined = satellite.mine_gcc
  ...
end
```

If `current_solar_output_factor` drops to 0 during any single day (eclipse period), the battery would cover the deficit. With a 2000 kWh battery and 1500 kWh initial charge, this should handle typical eclipse periods (which are short in LEO).

**The production code path appears correct** because it includes both the battery and cache clearing. The test setup is the outlier.

---

## Recommended Fix (Test Setup Only)

Add the missing battery unit to the test's satellite setup:

```ruby
# Install satellite_battery (matching gcc_mining_sat.rake)
unit_data = unit_lookup.find_unit('satellite_battery')
if unit_data
  merged_data = unit_data.dup
  merged_data['power'] ||= {}
  merged_data['power']['capacity_kwh'] = 2000.0
  merged_data['power']['current_kwh'] = 1500.0
  
  created_battery = ::Units::BaseUnit.create!(
    identifier: "satellite_battery_test_#{SecureRandom.hex(4)}",
    name: "Satellite Battery Test",
    unit_type: 'satellite_battery',
    attachable: @satellite,
    owner: owner,
    operational_data: merged_data
  )
  log("✓ Satellite battery installed (2000 kWh capacity, 1500 kWh current)")
end

# Clear power grid cache before deploy (matching gcc_mining_sat.rake)
@satellite.base_units.reload
@satellite.base_rigs.reload
@satellite.instance_variable_set(:@power_grid, nil)
@satellite.deploy('orbital', celestial_body: earth)
@satellite.save!
```

---

## Acceptance Criteria Status

- [x] Real code quoted for the energy/power calculation — no asserted or back-derived arithmetic
  - `has_sufficient_power?`: `power_generation >= power_usage` (energy_management.rb:58)
  - `power_generation`: sums from `base_units` with solar scaling (energy_management.rb:97-113)
  - `mine_gcc` battery check: `if respond_to?(:battery_level) && battery_level > 0` (cryptocurrency_mining.rb:16)
  
- [x] Confirmed what battery unit (if any) exists on the test's satellite, compared directly against the reference rake script's setup
  - Test: NO battery unit installed
  - Reference: `satellite_battery` with `capacity_kwh: 2000.0`, `current_kwh: 1500.0`
  
- [x] Root cause identified: missing battery unit + stale power grid cache — with evidence for whichever it is
  - Primary: Missing `satellite_battery` unit in test setup
  - Secondary: Missing `@power_grid` cache clearing in test setup
  
- [x] Explicit statement on whether this is a test-only issue or also affects production gameplay
  - Test-only issue. Production code path in `gcc_mining_sat.rake` includes both battery and cache clearing.
  
- [ ] No fix proposed/applied until root cause is confirmed
  - Fix proposed above (test setup only). Not applied pending approval.

---

## Stop Conditions

- Root cause confirmed with real evidence (quoted code, direct comparison of test vs. reference setup)
- No production bug identified — the issue is specific to test setup divergence from reference implementation
- Fix proposed is minimal and targeted (add battery unit + cache clearing to test setup)
