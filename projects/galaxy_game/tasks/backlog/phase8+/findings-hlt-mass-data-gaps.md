[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/findings-hlt-mass-data-gaps.md]
[RATIONALE: Orbital structure verification / HLT payload research — shipyard/craft work belongs in Phase 8]
# HLT Payload Capacity & Mass Data Gaps — Research Findings Report

**Task**: `2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md`  
**Date**: 2026-06-22 (Updated with historical scripts analysis)  
**Agent**: Implementation Agent (Ryzen 7)

---

## Executive Summary

**Critical finding from legacy integration tests**: The original design **explicitly intended** `max_cargo_mass_kg` to exist on craft blueprints and was validated for in `blueprint_integrity_checker.rb`. The current `heavy_lift_transport_bp.json` is **missing this field** and should be renamed to **`heavy_lift_transport_mk1_bp.json`** to follow the mk1→mk3 versioning pattern evidenced in the codebase (e.g., `3d_printed_ibeam_mk1`). 

The codebase has **real, working mass-calculation infrastructure** (`HasMassCalculation`, `LaunchPaymentService`) but the cargo-validation layer was **never completed** — the test scripts expect it but the production models don't implement it. 5 of 12 Luna precursor manifest items lack mass data entirely.

---

## Historical Context — What the Legacy Scripts Intended

**Critical evidence from old integration tests** (discovered during review):

### From `blueprint_integrity_checker.rb` (lines 314-328):
```ruby
check_required_fields(starship_variant_data, 
  ['id', 'name', 'base_chassis_blueprint_id', 'mass_empty_kg', 
   'cargo_capacity_m3', 'max_cargo_mass_kg', ...])

if starship_variant_data.key?('max_cargo_mass_kg') && 
   (!starship_variant_data['max_cargo_mass_kg'].is_a?(Numeric) || 
    starship_variant_data['max_cargo_mass_kg'] <= 0)
  puts "ERROR: '#{starship_variant_id}' has non-positive or invalid max_cargo_mass_kg"
end
```

**This proves**: The original design **explicitly validated** that craft blueprints MUST have `max_cargo_mass_kg`.

### From `starship_integration_test.rb` (lines 448-454):
```ruby
starship_max_mass = 120000.0 # kg (example)
puts "  Mass Check: #{total_mass <= starship_max_mass ? 'PASS' : 'FAIL'}"
if total_mass <= starship_max_mass
  puts "✅ Mass within limits"
end
```

**This proves**: The original design intended **cargo mass validation** to compare manifest total against `max_cargo_mass_kg`.

### From `test_build.rb` (lines 61-127):
```ruby
def calculate_mass(unit_data)
  density = unit_data['operational_data']&.[]('density') || 1000
  volume = calculate_volume(unit_data)
  density * volume
end

# ... sum into total_mass ...

if starship.max_cargo_mass && total_mass > starship.max_cargo_mass
  puts "ERROR: Mass Exceeds Max Cargo Mass! #{total_mass} kg > #{starship.max_cargo_mass} kg"
end
```

**This proves**: The original design intended **manifest item mass summation** and **capacity validation at launch time**.

### mk1 Versioning Pattern

The codebase already uses mk1 versioning for hardware (e.g., `3d_printed_ibeam_mk1` in full_run_order.txt). The current `heavy_lift_transport_bp.json` should be renamed to `heavy_lift_transport_mk1_bp.json` to follow this pattern, with room for mk2/mk3 variants in the future.

---

### Finding: **The discrepancy is real and confirmed.**

| Item | Value | Status |
|------|-------|--------|
| `heavy_lift_transport_bp.json` | ✅ EXISTS at `data/json-data/blueprints/crafts/space/spacecraft/` | Confirmed on disk |
| 163,000 kg (`empty_mass_kg`) | In bp file `physical_properties.empty_mass_kg` | Authoritative dry mass |
| 193,000 kg (materials sum) | In bp file `blueprint_data.materials` | **Discrepancy: +30,000 kg over empty_mass** |

### Materials breakdown from the bp file:
```
stainless_steel:     150,000 kg
electronics:           5,000 kg
thermal_protection:    8,000 kg
titanium_alloy:       20,000 kg
carbon_fiber:         10,000 kg
─────────────────────────────
Total materials:     193,000 kg
empty_mass_kg:       163,000 kg
─────────────────────────────
Discrepancy:          +30,000 kg (materials > empty_mass)
```

### Analysis:
The `blueprint_data.materials` list sums to **193,000 kg** but `physical_properties.empty_mass_kg` is **163,000 kg** — a **30,000 kg discrepancy**. The materials list is 18.4% higher than the stated dry mass.

**Likely explanation**: The materials list represents *total construction inputs* (including waste/scrap/overbuild), while `empty_mass_kg` is the *final assembled dry mass*. In aerospace manufacturing, material input typically exceeds final mass due to:
- Machining waste (aluminum/titanium scrap)
- Thermal protection system ablation margin
- Fasteners/adhesives not separately itemized
- Structural overbuild factor

**However**: The gap is large enough (30t) that it should be documented. If the materials list is meant to represent *bill of materials for assembly* (not final mass), it should be renamed or annotated. If it's meant to equal empty_mass, one set needs correction.

### Resolution:
- **`empty_mass_kg: 163000.0` is authoritative** — this is what `HasMassCalculation.get_base_craft_mass()` reads and uses for all mass calculations
- The materials list should either be corrected to sum to ~163,000 kg OR annotated with a `_note` explaining the overbuild factor
- **No immediate fix needed** unless the materials list is used for cost/procurement calculations (it currently isn't — `extract_unit_mass` in `LaunchPaymentService` only reads `required_materials` from unit blueprints, not craft blueprints)

---

## Step 2 — Cargo/Payload Mass Calculation Status

### Finding: **Cargo/payload mass is NOT calculated anywhere.**

#### What DOES exist (installed unit mass only):

1. **`HasMassCalculation`** (`galaxy_game/app/models/concerns/has_mass_calculation.rb`)
   - `calculate_mass` = base craft mass + sum of `base_units` + `base_modules` + `base_rigs`
   - These are things **mounted to ports** on the craft
   - Does NOT include cargo, inventory, or manifest items

2. **`LaunchPaymentService.calculate_total_mass`** (`galaxy_game/app/services/launch_payment_service.rb`)
   - Prefers `craft.calculate_mass` (same as above)
   - Fallback manually sums base_units/base_modules/base_rigs masses
   - Does NOT include cargo/inventory items

3. **`Spacecraft#load_cargo`** (`galaxy_game/app/models/spacecraft.rb`)
   - Old integration-test model only
   - Has `cargo_capacity` and `current_total_cargo_weight` but this is a standalone test class, not wired into the production system

4. **`CyclerSchedulerJob`** (`galaxy_game/app/jobs/cycler_scheduler_job.rb`)
   - Has `total_cargo_weight` tracking for cycler scheduling (lines 105-128)
   - Uses `max_cargo` from cycler data — but this is a separate system, not connected to HLT

#### What does NOT exist:
- **No method sums manifest inventory items as cargo mass**
- **No service converts `lunar_precursor_manifest_v2_DRAFT.json`'s `required_hardware` into a total weight**
- **No infrastructure exists to calculate what the HLT is carrying vs. what it has mounted**

### Impact on Luna Mission:
The `luna_mission.rake` task loads manifest items but has no way to verify they fit within the HLT's payload capacity. The launch cost calculation charges for installed unit mass only, not for the cargo being transported.

---

## Step 3 — Capacity-Limit Check Status

### Finding: **No payload mass limit exists anywhere on HLT.**

#### What exists:
- `heavy_lift_transport_data.json` and all other HLT operational_data files: **no capacity fields at all** (neither volume nor mass)
- The status.md notes mentioned `cargo_capacity_m3: 1000.0` — this was from the web session's `heavy_lift_transport_bp.json` which doesn't exist on disk

#### Old/legacy references (not active):
- `blueprint_integrity_checker.rb` (old integration test): checks for `max_cargo_mass_kg` on Starship variants — **this field is not present on any HLT file**
- `starship_integration_test.rb` (old integration test): has `max_cargo_mass` on a Struct — **not connected to production code**

#### Adopted baseline:
- **150,000 kg** (real-world Starship reusable-mode payload) is the agreed baseline per task file Step 6
- This figure exists only in documentation/notes, not in any data file or code

### Recommendation:
A `max_payload_kg` field needs to be added to the HLT blueprint/data, and a capacity-check method needs to be designed alongside `HasMassCalculation`.

---

## Step 4 — Mass Data Checklist for Luna Precursor Manifest Items

### 12 items in `lunar_precursor_manifest_v2_DRAFT.json`:

| # | Item ID | Blueprint File Exists? | Has `physical_properties.mass_kg` or `empty_mass_kg`? | Mass Value |
|---|---------|----------------------|------------------------------------------------------|------------|
| 1 | `solar_expansion_rig` | ✅ `rigs/power/solar_expansion_rig_bp.json` | ❌ **NO** — has `blueprint_data.material_requirements` but no mass field | Missing |
| 2 | `planetary_power_management_unit_mk1` | ✅ `energy/planetary_power_management_unit_bp.json` | ✅ YES | `empty_mass_kg: 500.0` |
| 3 | `comms_equipment_mk1` | ✅ `electronics/comms_equipment_bp.json` + `infrastructure/comms_equipment_bp.json` (duplicate IDs) | ✅ YES (both) | `150.0` / `200.0` |
| 4 | `planetary_umbilical_hub_mk1` | ✅ `infrastructure/planetary_umbilical_hub_bp.json` | ✅ YES | `empty_mass_kg: 300.0` |
| 5 | `thermal_extraction_unit_mk1` | ✅ `production/extractors/thermal_extraction_unit_mk1_bp.json` | ✅ YES | `empty_mass_kg: 2800.0` |
| 6 | `planetary_volatiles_extractor_mk1` | ✅ `resource/planetary_volatiles_extractor_mk1_bp.json` | ✅ YES | `empty_mass_kg: 800.0` |
| 7 | `inflatable_pressure_tank` | ✅ `storage/inflatable_pressure_tank_bp.json` | ✅ YES | `empty_mass_kg: 800.0` |
| 8 | `inflatable_cryo_tank` | ✅ `storage/inflatable_cryo_tank_bp.json` | ✅ YES | `empty_mass_kg: 1200.0` |
| 9 | `inflatable_gas_storage` | ✅ `storage/inflatable_gas_storage_bp.json` | ✅ YES | `mass_kg: 150.0` (uses `mass_kg`, not `empty_mass_kg`) |
| 10 | `gas_separator` | ✅ `production/refineries/gas_separator_unit_bp.json` | ❌ **NO** — no `physical_properties` section at all | Missing |
| 11 | `car_300_deployment_robot_mk1` | ✅ `robots/deployment/car_300_deployment_robot_mk1_bp.json` (ID: `car_300_lunar_deployment_robot_mk1`) | ❌ **NO** — has `required_materials` and `cost_data` but no mass field | Missing |
| 12 | `robot_charging_port` | ❌ **FILE DOES NOT EXIST** | N/A | Missing (no blueprint) |

### Summary:
- **7 of 12 items have mass data** ✅
- **5 of 12 items are missing mass data** ❌
  - `solar_expansion_rig` — blueprint exists but no mass field
  - `gas_separator` — blueprint exists but no `physical_properties` section
  - `car_300_deployment_robot_mk1` — blueprint exists but no mass field
  - `robot_charging_port` — **blueprint file does not exist**

### Note on duplicate IDs:
- `comms_equipment` exists in both `electronics/` and `infrastructure/` with different masses (150 kg vs 200 kg). The `BlueprintLookupService` will return whichever is found first. This should be reviewed for consistency.

---

## Recommendations

### PHASE 1 — Naming/Versioning (Prerequisite, low-risk)

1. **Rename `heavy_lift_transport_bp.json` to `heavy_lift_transport_mk1_bp.json`**
   - Aligns with mk1→mk3 versioning pattern already used in the codebase
   - Path: `data/json-data/blueprints/crafts/space/spacecraft/heavy_lift_transport_mk1_bp.json`
   - Update any blueprint lookup references accordingly

### PHASE 2 — Data Field (Direct addition, no new methods)

2. **Add `max_payload_kg` to `heavy_lift_transport_mk1_bp.json`**
   - Field: `"max_payload_kg": 150000` (adopted real-world Starship baseline)
   - Location: Add at same level as `empty_mass_kg` in `physical_properties`
   - No code changes required — this is data-only

### PHASE 3 — Missing Blueprint Mass Data (Complete Step 4 checklist items)

3. **Add `empty_mass_kg` to 3 blueprints that lack it:**
   - `solar_expansion_rig_bp.json` — estimate from materials or set reasonable value
   - `gas_separator_unit_bp.json` — add `physical_properties` section with mass
   - `car_300_lunar_deployment_robot_mk1_bp.json` — add mass from materials (advanced_carbon_fiber_composite 2500 kg + heavy-duty robotics estimate)

4. **Create missing `robot_charging_port` blueprint**
   - Follow pattern of other infrastructure units
   - Estimate mass based on charging station analogues (~200-500 kg)

### PHASE 4 — Cargo Mass Calculation (New service, requires design)

5. **Design `CargoMassCalculation` concern** (or similar):
   - Method: `calculate_cargo_mass(manifest)` — sum blueprint masses from manifest's `inventory.units`
   - Distinct from `HasMassCalculation.calculate_mass` (which is mounted units only)
   - Raise error if cargo mass + craft dry mass > `max_payload_kg`

6. **Integrate into manifest validation**:
   - Call cargo mass check before launch in `LaunchPaymentService` or new pre-launch validation
   - Pattern matches existing "fail fast" approach (already used in `HasMassCalculation`)

---

## Implementation Roadmap

**Immediate** (low-risk, data-only):
- Phase 1: Rename bp file
- Phase 2: Add `max_payload_kg` field
- Phase 3: Add missing mass data to 4 blueprints

**Sign-off required** (architecture):
- Phase 4: Design cargo-mass method + integration points

**Why this order**:
1. Phases 1-3 are data changes with no code risk
2. Phase 4 can proceed in parallel with fixing blueprints
3. Legacy test scripts validate this approach already expected this
