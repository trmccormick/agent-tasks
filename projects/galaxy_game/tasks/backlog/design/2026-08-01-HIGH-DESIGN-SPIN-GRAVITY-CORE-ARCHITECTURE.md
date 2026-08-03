---
status: backlog
priority: HIGH
type: DESIGN
system_domain: Settlement Infrastructure
mvp_alignment: SETTLEMENT_PHASE_4
local_worker_safe: true
created: 2026-08-01
last_updated: 2026-08-01
relates_to:
  - Gemini design chat session (spin-gravity core context)
  - Settlement Infrastructure Tiers (subsurface architecture)
  - Magnetic Levitation Engineering (tech tree tier)
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Design Researcher**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md \
         projects/galaxy_game/tasks/active/2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-01-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Spin-Gravity Core Architecture & Settlement Integration
**Status**: BACKLOG
**Priority**: HIGH
**Type**: DESIGN
**Created**: 2026-08-01
**Last Updated**: 2026-08-01

## Context

The **Spin-Gravity Core** is an underground magnetic-levitation centrifuge that provides artificial gravity for crew conditioning in settlements. It was designed in a Gemini game design chat session but needs detailed architecture documentation integrating it into settlement infrastructure.

### Current State
- ✅ Blueprint exists: `spin_gravity_core_mk1_bp.json` (8000h build, 250kW, 500k GCC)
- ✅ Operational data exists: `spin_gravity_core_mk1_data.json` (gravity specs, pod config, bearing systems)
- ✅ Technology reference: `gravitational_engineering` (confirmed in particle_physics.json tier_1b)
- ⚠️ Integration design needed: How does it connect to settlement infrastructure tiers?

## DESIGN DECISIONS (RESOLVED) ✅

### 1. Gravity Performance & Crew Conditioning Model

**RPM Scaling & Ramp Curves:**
```
Mars (0.38g → 1.0g):      8.2 RPM → 10.5 RPM
Luna (0.166g → 1.0g):     4.1 RPM → 10.5 RPM
Ramp Rate:                 0.5 RPM/minute (prevents vestibular disorientation)

Transition Time Examples:
- Luna surface to Earth-normal: ~13 minutes of gradual spin acceleration
- Mars surface to Earth-normal: ~4.6 minutes (smaller delta)
```

**Crew Health & Morale Mechanics:**
- **Not a production multiplier** — acts as crew upkeep/debuff prevention system
- **Conditioning Buff (Maintained):** Crew with 14+ weekly hours (e.g., 8hr sleep + 2hr exercise):
  - Bone density: 95% maintained
  - Cardiovascular health: 90% maintained
  - Work efficiency: No penalties, morale bonus (+15%)
  
- **Debuff/Penalty (Deconditioning):** Crew with <10 weekly hours:
  - Mars: -1.5% health/week
  - Luna: -2.5% health/week (more severe due to lower gravity)
  - Work efficiency: Gradual reduction, medical care costs increase
  - Eventually: Crew become unusable for specialized roles

**Specialist Role Unlock:**
- High-tier specialists (Senior Researchers, Advanced Metallurgists) require Earth-normal health baseline
- Spin-Gravity Core enables hiring/deployment of these roles

---

### 2. Subsurface Installation & Physics Specs

**Excavation Footprint:**
```
Vault Shape:    Vertical cylindrical chamber
Diameter:       25 meters
Depth:          15 meters
Entry:          Single elevator/lift shaft from surface
Foot print:     ~490 m² excavation volume
```

**Anchor System:**
```
Type:           8-point heavy bedrock anchor assembly
Material:       3D-printed I-beam braces anchoring into raw basalt bedrock
Attachment:     Top and bottom of central mag-lev spindle
Load Rating:    Designed for 20,000+ kg rotating mass + centrifugal forces
Foundation:     Requires bedrock contact (not regolith-only excavation)
```

**Magnetic Bearing & Vibration Isolation:**
```
Bearing Type:              Magnetic levitation (no mechanical contact)
Magnetic Field Strength:   2.5 Tesla
Bearing Count:             8 (distributed load)
Backup System:             Emergency mechanical bearings (engaged on power loss)
Alignment Tolerance:       ±0.5 mm (precision manufacturing requirement)
Vibration Isolation:       99.9% (0.1% transmission to habitat)
                           ← Protects adjacent Workshop 3D printers & Hydroponics

Vibration Impact:
- Rotating core does NOT rattle adjacent facilities
- Safe for co-location with precision equipment
```

**Power Loss & Emergency Braking:**
```
Emergency Trigger:         Total base power loss
Brake Type:                4 electromagnetic caliper brakes
Engagement Time:           0.5 seconds
Deceleration Rate:         -2.0 RPM/second
Full Stop (from max RPM):  5.25 seconds
Backup Power:              Emergency brake system draws 50kW
Safe Mode:                 Centrifuge comes to complete stop, mechanical bearings lock
```

---

### 3. Settlement Tier Integration

**Tier Placement:** **Phase 3 Subsurface Infrastructure (Settlement Specialization)**

**Phase Timeline:**
```
Phase 1 (Automated Precursor):
  - Harvester, TEU, MPVE
  - Solar/RTG power grid
  - No crew, or scout teams (<7 days)

Phase 2 (Basic Subsurface Landing & Survival):
  - Habitat Pods (2-4 crew sleeping capacity)
  - Utility Hub (scrubbers, compressors, water recycling)
  - Workshop (basic 3D printing)
  - Main elevator/access shaft
  → Short-term habitation (30-90 days)

Phase 3 (Long-Term Industrial Sustainability) ← SPIN-GRAVITY CORE HERE
  - Spin-Gravity Core (new)
  - Hydroponics Vault (food production for 10-50+ crew)
  - Advanced Workshop (smelting, precision manufacturing)
  - The Gem (water-ceiling atrium, thermal mass + aesthetics)
  → Permanent settlement (6+ months, industrial operations)
```

**Essential vs. Optional:**
- **Optional** for short scout/precursor missions (<30 days)
- **Mandatory** for permanent settlement status and long-duration crew
- **Required** for high-tier specialist roles (Senior Researchers, etc.) who need Earth-normal health to operate

**Power Grid Integration:**
```
Power Allocation: High-priority baseline load (180–220 kW sustained)
Priority Tier:    Behind critical atmospheric scrubbers, before discretionary systems
Power Source:     RTG baseload + solar + settlement grid
Cycling:          Can be powered down during emergency power rationing
                  (but health buffs degrade quickly if offline >24 hours)
```

**Upgrade & Scaling:**
- **Single Core:** Standard settlement (10-50 crew)
- **Multiple Cores:** Major industrial settlements or planetary capitals (50-100+ crew)
- Mk2/Mk3 variants enable scaling without duplicating excavation

---

### 4. Tech Tree Alignment & Upgrade Path

**Tech Gate:** `gravitational_engineering` (particle_physics.json tier_1b) ✅

**Prerequisites (Must research before gravitational_engineering):**
1. `magnetic_systems` — For 2.5 Tesla mag-lev bearing systems
2. `advanced_composites` — For carbon fiber suspension arms & pod hulls
3. `precision_manufacturing` — For ±0.5mm alignment tolerance requirements

**Variant Upgrade Path:**

| Variant | Pod Config | Crew Capacity | Rotation | Power | Build Time | Cost | Unlock Requirement |
|---------|-----------|---------------|----------|-------|------------|------|-------------------|
| **Mk I (Current)** | 2-pod tether | 2 crew | 10.5 RPM max | 250 kW | 8000h | 500k GCC | gravitational_engineering |
| **Mk II (Expansion)** | 4-pod cross-tether | 8 crew | 10.5 RPM max | 220 kW (more efficient) | 12000h | 800k GCC | superconductor_efficiency |
| **Mk III (Endgame)** | Multi-ring hub | 50+ crew | Variable zones | 400+ kW | 20000h+ | 2M+ GCC | advanced_habitation_integration |

**Upgrade Path Logic:**
- Mk I → Mk II: Requires existing Mk I + 150kg superconductor_materials + new 4-pod assemblies
- Mk II → Mk III: Major architectural redesign (planetary capital scale), may require new excavation

---

### 5. Visual Representation & Sprite Design

**Isometric/Sprite View:**
```
Primary Visual:    Circular subterranean excavation pit
Central Element:   Glowing blue mag-lev spindle (visible core)
Detail:            Exposed bedrock walls with anchor points visible
Depth Suggestion:  Layered shading showing underground chamber
Scale Indicator:   Elevator shaft/personnel entrance visible at top
```

**State Animations:**

**Idle/Unpowered State:**
- Pods rest stationary at retracted position (arms drawn inward to ~2m)
- Central spindle: Dark/unlit (no magnetic field active)
- No rotation
- Appears as "sleeping" structure

**Ramp-Up State (0.5-2 min):**
- Telescoping arms extend outward to 10m radius (smooth animation)
- Central spindle begins to glow with blue LED indicator
- Rotation speed gradually increases
- Pulsing blue light intensity matches RPM

**Active Operation State (Steady-State):**
- Smooth high-speed rotation (motion-blurred carbon fiber arms)
- Continuous pulsing blue LED ring track along pit walls
- Pod lights on (indicating occupied)
- Telemetry displays (if UI visible): Current RPM, crew count, power draw

**Power Loss/Emergency Braking State:**
- Rotation decelerates visibly (reverse ramp effect)
- Blue glow dims/pulses erratically
- Mechanical brakes engage (visual caliper glow/sparks optional)
- Comes to stop over 5.25 second sequence
- Falls to Idle/Unpowered state

**Thermal/Wear Indicators (Advanced):**
- After long operation: Faint heat shimmer around spindle
- Bearing maintenance cycle: Visual "service" animation (bearings light up differently)

## Prerequisites

Before starting synthesis:
1. ✅ **Gravity Performance Model** — COMPLETE (see Design Decisions section)
2. ✅ **Physical Installation Specs** — COMPLETE (25m diameter, 15m depth, 8-point anchor, 2.5T mag-lev, 99.9% vibration isolation)
3. ✅ **Settlement Tier Integration** — COMPLETE (Phase 3 subsurface, mandatory for permanent settlements)
4. ✅ **Tech Tree Alignment** — COMPLETE (gravitational_engineering + prerequisites: magnetic_systems, advanced_composites, precision_manufacturing)
5. ✅ **Visual Design** — COMPLETE (isometric pit with rotating spindle, blue LED glow, state animations)
6. ✅ **Upgrade Path** — COMPLETE (Mk I current, Mk II expansion, Mk III endgame planned)

**All core design questions have been answered.** This task is now ready for:
- Blueprint/operational_data validation
- Implementation/data updates
- Tech tree integration verification
- JSON compliance review

## Architecture Gotchas

⚠️ **Gotcha 1: Crew Conditioning vs. Settlement Function**
- Don't conflate "gravity for health" with "settlement support function"
- Gravity affects *crew morale/health* but doesn't directly support production
- This is a lifestyle upgrade, not a production multiplier (unless explicitly designed)

⚠️ **Gotcha 2: Power vs. Rotation Speed**
- Higher rotation = higher centripetal g-forces = lower power required (basic physics)
- But also = higher stress on bearings
- Design must balance: efficiency (RPM) vs. durability (bearing life)

⚠️ **Gotcha 3: Subsurface Real Estate**
- Every 1 unit deep of excavation = exponential cost increase on hostile worlds
- Don't design it as a massive cavern if a compact vertical shaft works

⚠️ **Gotcha 4: Mk1 → Mk2/Mk3 Path**
- If variants exist, each must have clear upgrade path + material requirements
- Check existing Mk2/Mk3 patterns (PVE, TEU) for consistency

## REQUIRED: Implementation Validation Report

**Design decisions have been finalized.** Now validate blueprint/operational_data files against design specs:

### Part 1: Blueprint Validation (spin_gravity_core_mk1_bp.json)

Verify these fields match design:
- [ ] `build_time_hours`: 8000 ✅
- [ ] `power_consumption_kw`: 250 ✅
- [ ] `required_technology`: `gravitational_engineering` ✅
- [ ] `cost_gcc`: 500000 ✅
- [ ] `required_materials`: includes 8 magnetic_bearing_assemblies, 4 carbon_fiber_suspension_arms, 2 modular_habitat_pods
- [ ] `deployment_data.deployment_time_hours`: 1200 (20 days) — aligns with deep excavation requirement
- [ ] `hazard_level`: 3 (high) — correct for magnetic field + rotation
- [ ] `metadata.lava_tube_settlement_critical`: true ✅
- [ ] `metadata.designed_for`: ["Mars", "Luna"] ✅

**Check for missing fields:**
- [ ] subsurface_depth_meters: Should be 15 (verify if present)
- [ ] excavation_diameter_m: Should be 25 (verify if present)
- [ ] vibration_isolation_spec: Should note 99.9% isolation

### Part 2: Operational Data Validation (spin_gravity_core_mk1_data.json)

Verify these fields match design:
- [ ] `gravity_performance.minimum_gravity_g`: 0.38 ✅
- [ ] `gravity_performance.maximum_gravity_g`: 1.0 ✅
- [ ] `gravity_performance.mars_surface_rpm`: 8.2 ✅
- [ ] `gravity_performance.luna_surface_rpm`: 4.1 ✅
- [ ] `gravity_performance.earth_normal_rpm`: 10.5 ✅
- [ ] `gravity_performance.rpm_adjustment_rate_per_minute`: 0.5 ✅
- [ ] `pod_specifications.pod_count`: 2 ✅
- [ ] `pod_specifications.pod_capacity_crew`: 1 ✅
- [ ] `bearing_system.magnetic_field_strength_tesla`: 2.5 ✅
- [ ] `bearing_system.bearing_count`: 8 ✅
- [ ] `bearing_system.alignment_tolerance_mm`: 0.5 ✅
- [ ] `safety_systems.emergency_braking.stops_from_max_rpm_seconds`: 5.25 ✅
- [ ] `crew_health_outcomes.recommended_weekly_hours`: 14 ✅
- [ ] `crew_health_outcomes.minimum_weekly_hours_for_health`: 10 ✅
- [ ] `integration_notes.excavation_diameter_m`: 25 ✅
- [ ] `integration_notes.excavation_depth_m`: 15 ✅
- [ ] `mars_specific_considerations.crew_deconditioning_rate_percent_per_week`: 1.5 ✅
- [ ] `luna_specific_considerations.crew_deconditioning_rate_percent_per_week`: 2.5 ✅

**Check for missing fields:**
- [ ] vibration_isolation_percent: Should be 99.9
- [ ] anchor_point_count: Should be 8
- [ ] upgrade_path_mk2: Outline Mk II specs (4-pod, 8 crew, superconductor efficiency)
- [ ] power_grid_priority: Should note "high-priority baseline load"
- [ ] specialist_role_unlock: List roles enabled (Senior Researcher, etc.)

### Part 3: Tech Tree Integration Check

Verify in particle_physics.json tier_1b:
- [ ] `gravitational_engineering` tech entry exists
- [ ] Prerequisites include: `magnetic_systems`, `advanced_composites`, `precision_manufacturing`
- [ ] Unlocks: List should include `spin_gravity_core_mk1` unit
- [ ] Research cost, points, time defined
- [ ] Upgrade path to Mk II documented

### Part 4: Settlement Architecture Consistency

Cross-check against settlement infrastructure tiers:
- [ ] Spin-Gravity Core listed in Phase 3 subsurface tier
- [ ] Marked as "mandatory for permanent settlements"
- [ ] Integration notes with Habitat Pods, Workshop, Hydroponics Vault consistent
- [ ] Power grid allocation documented (180-220 kW baseline)
- [ ] Crew health system references correct (deconditioning penalties, specialist unlock)

### Deliverables

When validation is complete, provide:

1. **Validation Report** (markdown file):
   - [ ] All blueprint fields verified or documented as missing
   - [ ] All operational_data fields verified or documented as missing
   - [ ] Tech tree entry status (exists/missing/incomplete)
   - [ ] List of any inconsistencies found
   - [ ] Recommendations for updates/fixes

2. **Updated Files** (if changes needed):
   - [ ] spin_gravity_core_mk1_bp.json (if fields need adding/correcting)
   - [ ] spin_gravity_core_mk1_data.json (if fields need adding/correcting)
   - [ ] particle_physics.json (if tech_tree entry needs creating/updating)

3. **Design-to-Implementation Mapping**:
   - [ ] Cross-reference from each design decision to its corresponding JSON field
   - [ ] Verify all design specs are represented in data files
   - [ ] Confirm no discrepancies between design intent and implementation

## Stop Conditions

✅ **Design Phase Complete** — All architectural questions answered. Now in implementation validation phase.

**Stop validation when you have:**
- [ ] Confirmed all blueprint fields (existing or need-to-add documented)
- [ ] Confirmed all operational_data fields (existing or need-to-add documented)
- [ ] Verified tech_tree entry exists and is complete
- [ ] Identified any discrepancies between design intent and current JSON data
- [ ] Generated complete validation report with cross-reference mapping
- [ ] Listed all recommended updates/fixes with priority

## Completion Report

When validation is complete, provide in chat:

1. **Validation Summary**: "All fields verified" or "X fields need updates"
2. **Missing/Incomplete Fields**: Detailed list if any
3. **Discrepancies Found**: Any conflicts between design intent and current data
4. **Recommended Updates**: Priority-ordered list of JSON changes needed
5. **Tech Tree Status**: Exists/complete/needs-update/missing
6. **Next Steps**: Ready for implementation OR list blocking issues

**Validation artifacts location:**
- Save full report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-01-DESIGN-SPIN-GRAVITY-CORE-VALIDATION.md`

## Handoff Summary

Once synthesis is complete and logged, agent transitions to implementation (if needed) or task moves to completed.

---

## Existing Blueprint Data (COMPLETE REFERENCE FOR WEB AGENT)

### spin_gravity_core_mk1_bp.json (Current Full Spec)
```json
{
  "template": "unit_blueprint",
  "id": "spin_gravity_core_mk1",
  "name": "Spin-Gravity Core Mk I",
  "description": "Underground centrifuge system providing variable artificial gravity (0.38g to 1.0g) for crew physical conditioning and sleep cycles. Mag-lev bearing system eliminates vibration transmission to habitat.",
  "category": "infrastructure",
  "subcategory": "gravity_systems",
  
  "physical_properties": {
    "length_m": 20.0,
    "width_m": 20.0,
    "height_m": 12.0,
    "empty_mass_kg": 15000.0,
    "volume_m3": 4800.0
  },
  
  "required_materials": {
    "advanced_composites": 2000,
    "titanium_alloy": 3000,
    "magnetic_bearing_assemblies": 8,
    "carbon_fiber_suspension_arms": 4,
    "high_performance_electronics": 500,
    "precision_gyroscope_systems": 2,
    "emergency_braking_calipers": 4,
    "modular_habitat_pods": 2
  },
  
  "production_data": {
    "time_hours": 8000,
    "power_consumption_kw": 250,
    "required_facility_type": "manufacturing_plant",
    "required_technology": "gravitational_engineering",
    "base_material_efficiency": 0.90,
    "base_time_efficiency": 0.85
  },
  
  "cost_data": {
    "purchase_cost_gcc": 500000,
    "maintenance_repair_hours": 600,
    "maintenance_repair_cost_gcc": 50000
  },
  
  "hazard_level": 3,
  "special_handling": ["high_speed_rotation", "magnetic_field_containment", "precision_alignment_required"],
  
  "operational_data_reference": {
    "gravity_range_g": [0.38, 1.0],
    "rotation_radius_m": 10.0,
    "max_rpm": 10.5,
    "pod_capacity": 2,
    "dual_pod_tether": true
  },
  
  "deployment_data": {
    "deployable": true,
    "requires_shell": true,
    "default_deployment_time_hours": 1200,
    "deployment_notes": "Requires underground excavation and precision leveling. Magnetic bearing system must be calibrated post-installation."
  },
  
  "metadata": {
    "version": "1.0",
    "lava_tube_settlement_critical": true,
    "designed_for": ["Mars", "Luna"],
    "crew_support": "Essential for long-duration habitation to maintain 1.0g conditioning cycles for crew health"
  }
}
```

### spin_gravity_core_mk1_data.json (Current Full Spec)
```json
{
  "id": "spin_gravity_core_mk1",
  "name": "Spin-Gravity Core Mk I Operational Data",
  "unit_type": "infrastructure",
  
  "gravity_performance": {
    "minimum_gravity_g": 0.38,
    "maximum_gravity_g": 1.0,
    "rotation_radius_m": 10.0,
    "earth_normal_rpm": 10.5,
    "mars_surface_rpm": 8.2,
    "luna_surface_rpm": 4.1,
    "rpm_adjustment_rate_per_minute": 0.5,
    "vibration_isolation_rating": "ultra_low_vibration"
  },
  
  "operational_modes": {
    "sleep_mode": {
      "duration_hours": 8,
      "target_gravity_g": 1.0,
      "power_consumption_kw": 180,
      "pod_capacity": 2
    },
    "exercise_mode": {
      "duration_hours": 2,
      "target_gravity_g": 1.0,
      "power_consumption_kw": 220,
      "pod_capacity": 2
    },
    "maintenance_mode": {
      "power_consumption_kw": 50,
      "pod_capacity": 0,
      "notes": "Stationary for bearing inspection and lubrication"
    }
  },
  
  "pod_specifications": {
    "pod_count": 2,
    "pod_dimensions_m": [3.0, 2.5, 2.0],
    "pod_mass_kg": 500,
    "pod_capacity_crew": 1,
    "suspension_system": "carbon_fiber_telescoping_arms",
    "arm_length_m": 10.0
  },
  
  "bearing_system": {
    "type": "magnetic_levitation",
    "bearing_count": 8,
    "bearing_load_rating_kg": 2000,
    "magnetic_field_strength_tesla": 2.5,
    "alignment_tolerance_mm": 0.5,
    "maintenance_interval_hours": 2000,
    "backup_mechanical_bearings": true
  },
  
  "safety_systems": {
    "emergency_braking": {
      "type": "electromagnetic_caliper",
      "caliper_count": 4,
      "brake_engagement_time_seconds": 0.5,
      "stops_from_max_rpm_seconds": 5.25
    },
    "gyroscopic_stabilization": {
      "system_count": 2,
      "maintenance_interval_hours": 500
    },
    "rotation_monitoring": {
      "rpm_sensor_count": 3,
      "acceleration_sensor_count": 4,
      "alignment_sensor_count": 6,
      "anomaly_detection_threshold_g": 0.05
    }
  },
  
  "power_requirements": {
    "startup_power_kw": 300,
    "sustained_operation_min_kw": 180,
    "sustained_operation_max_kw": 220,
    "idle_standby_kw": 15,
    "power_source_priority": ["primary_rtg", "solar_array", "settlement_grid"]
  },
  
  "environmental_performance": {
    "operational_temperature_range_celsius": [-40, 60],
    "radiation_shielding_thickness_cm": 20,
    "vacuum_rated": true,
    "vibration_transmission_to_habitat_percent": 0.1
  },
  
  "crew_health_outcomes": {
    "bone_density_maintenance_percent": 95,
    "cardiovascular_fitness_maintenance_percent": 90,
    "muscle_atrophy_prevention_percent": 92,
    "sleep_quality_improvement_percent": 85,
    "recommended_weekly_hours": 14,
    "minimum_weekly_hours_for_health": 10,
    "maximum_safe_weekly_hours": 28
  },
  
  "integration_notes": {
    "requires_underground_excavation": true,
    "excavation_diameter_m": 25,
    "excavation_depth_m": 15,
    "foundation_requirements": "bedrock_anchoring_at_8_points",
    "access_requirements": "personnel_elevator_or_ladder_well_to_pod_level"
  },
  
  "mars_specific": {
    "surface_gravity_g": 0.38,
    "crew_deconditioning_rate_percent_per_week": 1.5,
    "dust_storm_operation": "fully_sealed_underground_location_immune"
  },
  
  "luna_specific": {
    "surface_gravity_g": 0.166,
    "crew_deconditioning_rate_percent_per_week": 2.5,
    "radiation_environment": "extreme_cosmic_ray_exposure_mitigated_by_regolith",
    "thermal_stability": "underground_location_maintains_stable_-40C"
  }
}
```

---

## Related Documents

- [ISRU Chain Validation Report](file:///Users/tam0013/Documents/git/galaxyGame/ISRU_CHAIN_VALIDATION_2026-08-01.md)
- Settlement Infrastructure Tiers (from design chat)
- Gravitational Engineering Tech Tree Entry (particle_physics.json tier_1b)
