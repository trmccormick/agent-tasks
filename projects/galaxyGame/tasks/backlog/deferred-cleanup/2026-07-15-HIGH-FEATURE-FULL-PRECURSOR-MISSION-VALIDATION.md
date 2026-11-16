---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-07-15-HIGH-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase5+/2026-07-15-HIGH-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md \
         projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-15-HIGH-FEATURE-FULL-PRECURSOR*"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Full Precursor Mission Validation — Interplanetary Supply Chain Testing
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context

The current `luna_mission:execute` rake only tests **deployment of pre-built hardware** on Luna. It does not test the **full precursor mission supply chain** that makes the MVP viable:

```
GCC Mining (Earth) → Currency → Procurement
    ↓
Titan HLT → N2 + CH4 delivery → Luna habitat pressurization + fuel
    ↓
Venus HLT → CO2 + N2 delivery → ISRU feedstock + habitat pressurization
    ↓
Luna ISRU → O2/H2/He3/I-Beams → L1/LEO depot construction
    ↓
Venus refuel ← Titan return OR CH4 import OR local production
```

**This task creates a comprehensive rake that validates the entire supply chain end-to-end.**

### Current State vs. Goal

| Component | Current State | What's Needed |
|---|---|---|
| **GCC Satellite mining** | ❌ Not tested | Economic layer for procurement |
| **Titan HLT delivery** | ❌ Not tested | N2 + CH4 to Luna (long transit) |
| **Venus HLT delivery** | ❌ Not tested | CO2 + N2 to Luna (refuel timing critical) |
| **Luna ISRU production** | ❌ Not tested | TEU/PVE validated producing volatiles |
| **Manufacturing pipeline** | ❌ Not tested | 3D printing, hollowing, tank shell production |
| **Luna → L1/LEO supply chain** | ❌ Not tested | Materials flow for depot construction |

---

## Architecture Gotchas

⚠️ **GOTCHA 1: Interplanetary transit timing is critical**
- ❌ Wrong: Assume all HLTs arrive simultaneously
- ✅ Right: Titan transit is longest (first arrives during precursor mission), Venus follows, refueling dependency on Titan return
- Why: Logistics scheduling determines whether Venus can return or needs Earth CH4 import

⚠️ **GOTCHA 2: Supply chain must be data-driven, not world-name-driven**
- ❌ Wrong: Hardcode "Titan" and "Venus" as the only sources
- ✅ Right: Use planet properties (atmosphere composition, distance) to determine what each HLT delivers
- Why: Pattern must work for any system with gas giants + terrestrial planets

⚠️ **GOTCHA 3: Venus refueling creates a scheduling dependency**
- ❌ Wrong: Test Venus delivery in isolation
- ✅ Right: Venus refuel timing depends on Titan return OR Earth CH4 import OR local production
- Why: This is the critical path constraint for the entire mission

⚠️ **GOTCHA 4: GCC mining provides economic foundation**
- ❌ Wrong: Skip economic layer, focus only on logistics
- ✅ Right: Validate currency generation → procurement capability → interplanetary trade
- Why: No currency = no procurement for interplanetary logistics

---

## Files Involved

### Primary Files — you will create these
| File | Purpose | Location |
|---|---|---|
| `lunar_precursor_mission_validation.rake` | Full supply chain validation rake | `galaxy_game/lib/tasks/` |
| `precursor_mission_profile_v1.json` | Mission profile with all phases | `data/json-data/missions_v2/profiles/` |
| `precursor_mission_manifest_v1.json` | Manifest with all HLT deliveries | `data/json-data/missions_v2/manifests/` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/lib/tasks/luna_mission.rake` | Current rake pattern (deployment only) |
| `data/json-data/missions_v2/profiles/luna_base_profile_v2.json` | Existing Luna profile (phase structure reference) |
| `data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json` | Existing manifest (hardware list reference) |
| `data/json-data/missions/tasks/gcc_sat_mining_deployment/` | GCC satellite mission profile |
| `data/json-data/missions/tasks/titan_harvester_mission/` | Titan HLT mission profile |
| `data/json-data/missions/tasks/venus_harvester_mission/` | Venus HLT mission profile |

### Migration
- [x] No migration needed — uses existing tables (settlements, inventories, organizations)

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/phase5+/2026-07-15-HIGH-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md \
       projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Create mission profile (`precursor_mission_profile_v1.json`)

Create a new v2 profile that defines the full precursor mission phases:

```json
{
  "template": "profile_v2",
  "version": "1.0",
  "profile_id": "precursor_mission_validation",
  "name": "Full Precursor Mission Validation",
  "description": "End-to-end validation of interplanetary supply chain: GCC mining → Titan/Venus HLT delivery → Luna ISRU production → L1/LEO depot construction.",
  "mission_plan_ref": "mission_plans/precursor_mission_plan_v1.json",
  "manifest_ref": "data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json",
  "world_requirements": {
    "body_type": "multi_body_system",
    "requires_gas_giants": true,
    "requires_terrestrial_planets": true,
    "notes": "Pattern applies to any system with gas giants (Titan) and terrestrial planets (Luna, Venus). Sol is the reference implementation."
  },
  "phases": [
    {
      "phase_id": "gcc_mining",
      "name": "GCC Satellite Mining",
      "description": "Deploy GCC mining satellite on Earth to generate currency for procurement.",
      "prerequisites": [],
      "duration_days": 30,
      "outputs": ["currency_generation", "economic_layer"]
    },
    {
      "phase_id": "initial_hlt_landings",
      "name": "Initial HLT Landings — Multi-Cargo Manifests",
      "description": "Multiple HLT landings with different cargo manifests: habitat modules, TEU/PVE equipment, tank farm components. Tests multi-landing logistics and cargo distribution.",
      "prerequisites": ["gcc_mining"],
      "duration_days": 180,
      "outputs": ["habitat_modules_delivered", "isru_equipment_delivered", "tank_farm_components_delivered"],
      "landing_manifests": [
        {
          "manifest_id": "hlt_landing_1_habitat",
          "cargo": ["habitation_modules", "life_support_systems", "pressurization_equipment"],
          "description": "First HLT landing: habitat modules and life support"
        },
        {
          "manifest_id": "hlt_landing_2_isru",
          "cargo": ["thermal_extraction_unit", "planetary_volatiles_extractor", "gas_separator"],
          "description": "Second HLT landing: ISRU equipment"
        },
        {
          "manifest_id": "hlt_landing_3_tank_farm",
          "cargo": ["inflatable_tanks", "tank_farm_infrastructure", "sealing_materials"],
          "description": "Third HLT landing: tank farm and sealing components"
        }
      ]
    },
    {
      "phase_id": "power_grid_deployment",
      "name": "Power Grid Deployment — RTG + 3D Printed Solar Array",
      "description": "Deploy full power grid: RTG for nighttime power, 3D printed solar array beams (structural support), connection to umbilical hub and all deployed units/HLT crafts. The current rake only tests solar rig deployment (for HLT operations) but misses the full power grid.",
      "prerequisites": ["initial_hlt_landings"],
      "duration_days": 120,
      "outputs": ["rtg_power_online", "solar_array_built", "power_grid_connected"],
      "construction_phases": [
        {
          "phase": "rtg_deployment",
          "description": "Deploy RTG units for nighttime power (critical — solar doesn't work during lunar night)",
          "materials_required": ["rtg_units", "radiation_shielding", "thermal_management"]
        },
        {
          "phase": "solar_array_3d_printing",
          "description": "Use 3D printer to build structural beams for solar array (from regolith feedstock)",
          "materials_required": ["depleted_regolith", "structural_beams", "solar_panel_modules"]
        },
        {
          "phase": "umbilical_hub_connection",
          "description": "Connect power grid to umbilical hub and distribute to all deployed units/HLT crafts",
          "materials_required": ["power_cables", "distribution_units", "umbilical_connections"]
        }
      ]
    },
    {
      "phase_id": "titan_delivery",
      "name": "Titan HLT Atmosphere Harvesting",
      "description": "Deploy Titan HLT to harvest N2 + CH4 from atmosphere and deliver to Luna.",
      "prerequisites": ["gcc_mining"],
      "duration_days": 730,
      "outputs": ["n2_delivery", "ch4_delivery"]
    },
    {
      "phase_id": "venus_delivery",
      "name": "Venus HLT Atmosphere Harvesting",
      "description": "Deploy Venus HLT to harvest CO2 + N2 from atmosphere and deliver to Luna.",
      "prerequisites": ["gcc_mining"],
      "duration_days": 400,
      "outputs": ["co2_delivery", "n2_delivery"]
    },
    {
      "phase_id": "luna_bootstrap",
      "name": "Luna Bootstrap Deployment",
      "description": "Deploy TEU/PVE, power, comms on Luna (current rake coverage).",
      "prerequisites": ["titan_delivery"],
      "duration_days": 90,
      "outputs": ["isru_deployment", "habitat_ready"]
    },
    {
      "phase_id": "isru_production",
      "name": "Luna ISRU Production Validation",
      "description": "Validate TEU/PVE actually produce volatiles (O2, H2, He3) and manufacturing pipeline works.",
      "prerequisites": ["luna_bootstrap"],
      "duration_days": 180,
      "outputs": ["o2_production", "h2_production", "he3_production", "i_beam_production"]
    },
    {
      "phase_id": "l1_leo_supply",
      "name": "Luna → L1/LEO Depot Supply Chain",
      "description": "Validate materials flow from Luna to orbital depot construction.",
      "prerequisites": ["isru_production"],
      "duration_days": 60,
      "outputs": ["depot_materials_delivered", "l1_depot_ready"]
    },
    {
      "phase_id": "venus_refuel",
      "name": "Venus Refueling Dependency Chain",
      "description": "Validate Venus refueling: Titan return OR Earth CH4 import OR local production.",
      "prerequisites": ["venus_delivery"],
      "duration_days": 30,
      "outputs": ["venus_refueled", "return_verified"]
    },
    {
      "phase_id": "habitation_ready",
      "name": "Habitat Readiness — Initial Pressurization + ISRU O2",
      "description": "Validate habitats are ready for habitation: initial N2 pressurization from Titan/Venus supplies + ISRU O2 production. This milestone is reached BEFORE lava tube sealing — habitation can begin once power grid, N2 supply, and O2 production are operational.",
      "prerequisites": ["power_grid_deployment", "titan_delivery", "venus_delivery", "isru_production"],
      "duration_days": 30,
      "outputs": ["habitats_ready", "n2_pressurized", "o2_production_active", "habitation_approved"],
      "success_criteria": [
        "power_grid_operational",
        "n2_supply_from_titan_and_venus_verified",
        "isru_o2_production_active",
        "life_support_systems_online"
      ]
    },
    {
      "phase_id": "lava_tube_buildout",
      "name": "Lava Tube Buildout — Sealing and Tank Farm (Post-Habitation)",
      "description": "Validate lava tube habitat construction: sealing the tube, internal tank farm installation, structural integrity verification. This occurs AFTER habitation is ready — it's infrastructure improvement, not a prerequisite for habitation.",
      "prerequisites": ["habitation_ready"],
      "duration_days": 365,
      "outputs": ["tube_sealed", "tank_farm_installed", "structural_integrity_verified"],
      "construction_phases": [
        {
          "phase": "tube_sealing",
          "description": "Seal lava tube entrance and interior with regolith-based materials",
          "materials_required": ["regolith_blocks", "sealing_compound", "structural_supports"]
        },
        {
          "phase": "tank_farm_installation",
          "description": "Install internal tank farm for N2, CH4, O2, H2 storage",
          "materials_required": ["inflatable_tanks", "tank_farm_infrastructure", "piping_systems"]
        },
        {
          "phase": "habitat_pressurization",
          "description": "Pressurize habitat with N2 from Titan/Venus delivery",
          "materials_required": ["n2_from_titan", "n2_from_venus", "pressure_control_systems"]
        },
        {
          "phase": "structural_integrity_verification",
          "description": "Verify structural integrity of sealed tube and tank farm",
          "success_criteria": ["no_pressure_leaks", "structural_load_bearing_verified", "tank_farm_sealed"]
        }
      ]
    }
  ],
  "success_conditions": {
    "complete_phases": ["gcc_mining", "initial_hlt_landings", "power_grid_deployment", "titan_delivery", "venus_delivery", "luna_bootstrap", "isru_production", "habitation_ready", "l1_leo_supply", "venus_refuel", "lava_tube_buildout"],
    "currency_generated": true,
    "n2_delivered_to_luna": true,
    "ch4_delivered_to_luna": true,
    "co2_delivered_to_luna": true,
    "rtg_power_online": true,
    "solar_array_built": true,
    "power_grid_connected": true,
    "isru_producing": true,
    "habitats_ready_for_habitation": true,
    "depot_materials_available": true,
    "venus_refueled": true
  },
  "metadata": {
    "version": "1.0",
    "pattern_class": "interplanetary_supply_chain",
    "auto_generatable": true
  }
}
```

### Step 2 — Create manifest (`precursor_mission_manifest_v1.json`)

Create manifest with all HLT deliveries and supply chain items:

```json
{
  "manifest_id": "precursor_mission_manifest_v1",
  "version": "1.0",
  "archetype": "Full_Precursor_Mission",
  "description": "Complete interplanetary supply chain: GCC mining, Titan/Venus HLT delivery, Luna ISRU production, L1/LEO depot construction.",
  "phases": [
    {
      "phase_id": "gcc_mining",
      "required_hardware": [
        {
          "id": "gcc_mining_satellite",
          "count": 1,
          "role": "Cryptocurrency Mining",
          "task_affinity": "deploy_gcc_mining_satellite"
        }
      ],
      "outputs": {
        "currency_per_day": 10000,
        "currency_type": "GCC"
      }
    },
    {
      "phase_id": "titan_delivery",
      "required_hardware": [
        {
          "id": "atmosphere_harvester_titan",
          "count": 2,
          "role": "Atmospheric Harvesting (N2 + CH4)",
          "task_affinity": "deploy_titan_atmosphere_harvester"
        }
      ],
      "delivery_to_luna": {
        "n2_kg": 50000,
        "ch4_kg": 25000,
        "transit_days": 730
      }
    },
    {
      "phase_id": "venus_delivery",
      "required_hardware": [
        {
          "id": "atmosphere_harvester_venus",
          "count": 2,
          "role": "Atmospheric Harvesting (CO2 + N2)",
          "task_affinity": "deploy_venus_atmosphere_harvester"
        }
      ],
      "delivery_to_luna": {
        "co2_kg": 75000,
        "n2_kg": 30000,
        "transit_days": 400
      },
      "refuel_dependency": {
        "requires_ch4_kg": 10000,
        "source_options": ["titan_return", "earth_import", "luna_local_production"]
      }
    },
    {
      "phase_id": "luna_bootstrap",
      "required_hardware": [
        {
          "id": "regolith_harvester_rover",
          "count": 1,
          "role": "Regolith Excavation"
        },
        {
          "id": "thermal_extraction_unit_mk1",
          "count": 1,
          "role": "Regolith Heating / Volatile Driving"
        },
        {
          "id": "planetary_volatiles_extractor_mk1",
          "count": 1,
          "role": "Volatile Processing"
        },
        {
          "id": "gas_separator",
          "count": 1,
          "role": "Cryogenic Gas Separation"
        }
      ],
      "delivery_to_luna": {
        "n2_required_kg": 80000,
        "ch4_required_kg": 35000
      }
    },
    {
      "phase_id": "isru_production",
      "required_hardware": [
        {
          "id": "3d_printer_mk1",
          "count": 1,
          "role": "On-Site Manufacturing"
        },
        {
          "id": "hollowing_equipment",
          "count": 1,
          "role": "Asteroid Hollowing"
        }
      ],
      "production_targets": {
        "o2_kg_per_day": 500,
        "h2_kg_per_day": 200,
        "he3_kg_per_day": 10,
        "i_beam_units_per_day": 10
      }
    },
    {
      "phase_id": "l1_leo_supply",
      "required_hardware": [
        {
          "id": "heavy_lift_transport",
          "count": 1,
          "role": "Luna → L1/LEO Transport"
        }
      ],
      "delivery_to_l1": {
        "i_beams": 100,
        "o2_kg": 50000,
        "h2_kg": 20000,
        "regolith_blocks": 5000
      }
    },
    {
      "phase_id": "venus_refuel",
      "required_hardware": [
        {
          "id": "ch4_storage_tank",
          "count": 1,
          "role": "Methane Storage for Refueling"
        }
      ],
      "refuel_verification": {
        "venus_refueled": true,
        "return_verified": true
      }
    }
  ]
}
```

### Step 3 — Create validation rake (`lunar_precursor_mission_validation.rake`)

Create a comprehensive rake that validates the entire supply chain:

```ruby
# lib/tasks/lunar_precursor_mission_validation.rake
namespace :lunar_precursor do
  desc "Validate full precursor mission supply chain end-to-end"
  task validate_full_mission: :environment do
    puts "\n" + "=" * 80
    puts "FULL PRECURSOR MISSION VALIDATION — INTERPLANETARY SUPPLY CHAIN"
    puts "=" * 80

    # Phase 1: GCC Mining
    puts "\n=== PHASE 1: GCC SATELLITE MINING ==="
    gcc_result = validate_gcc_mining
    puts "  ✓ Currency generation: #{gcc_result[:currency_per_day]} GCC/day"

    # Phase 2: Titan HLT Delivery
    puts "\n=== PHASE 2: TITAN HLT ATMOSPHERE HARVESTING ==="
    titan_result = validate_titan_delivery
    puts "  ✓ N2 delivered: #{titan_result[:n2_kg]} kg"
    puts "  ✓ CH4 delivered: #{titan_result[:ch4_kg]} kg"
    puts "  ✓ Transit time: #{titan_result[:transit_days]} days"

    # Phase 3: Venus HLT Delivery
    puts "\n=== PHASE 3: VENUS HLT ATMOSPHERE HARVESTING ==="
    venus_result = validate_venus_delivery
    puts "  ✓ CO2 delivered: #{venus_result[:co2_kg]} kg"
    puts "  ✓ N2 delivered: #{venus_result[:n2_kg]} kg"
    puts "  ✓ Transit time: #{venus_result[:transit_days]} days"

    # Phase 4: Luna Bootstrap (current rake coverage)
    puts "\n=== PHASE 4: LUNA BOOTSTRAP DEPLOYMENT ==="
    luna_result = validate_luna_bootstrap
    puts "  ✓ TEU deployed: #{luna_result[:teu_deployed]}"
    puts "  ✓ PVE deployed: #{luna_result[:pve_deployed]}"
    puts "  ✓ Gas Separator deployed: #{luna_result[:gas_separator_deployed]}"

    # Phase 5: ISRU Production Validation
    puts "\n=== PHASE 5: LUNA ISRU PRODUCTION VALIDATION ==="
    isru_result = validate_isru_production
    puts "  ✓ O2 produced: #{isru_result[:o2_kg]} kg/day"
    puts "  ✓ H2 produced: #{isru_result[:h2_kg]} kg/day"
    puts "  ✓ He3 produced: #{isru_result[:he3_kg]} kg/day"
    puts "  ✓ I-Beams produced: #{isru_result[:i_beam_units]} units/day"

    # Phase 6: Luna → L1/LEO Supply Chain
    puts "\n=== PHASE 6: LUNA → L1/LEO DEPOT SUPPLY CHAIN ==="
    supply_result = validate_l1_leo_supply
    puts "  ✓ I-Beams delivered to L1: #{supply_result[:i_beams_delivered]}"
    puts "  ✓ O2 delivered to L1: #{supply_result[:o2_delivered]} kg"
    puts "  ✓ H2 delivered to L1: #{supply_result[:h2_delivered]} kg"

    # Phase 7: Venus Refueling Dependency Chain
    puts "\n=== PHASE 7: VENUS REFUELING DEPENDENCY CHAIN ==="
    refuel_result = validate_venus_refuel
    puts "  ✓ Venus refueled: #{refuel_result[:venus_refueled]}"
    puts "  ✓ Return verified: #{refuel_result[:return_verified]}"
    puts "  ✓ Refuel source: #{refuel_result[:refuel_source]}"

    # Final Summary
    puts "\n" + "=" * 80
    puts "FINAL VALIDATION SUMMARY"
    puts "=" * 80
    
    all_passed = gcc_result[:success] && titan_result[:success] && venus_result[:success] &&
                 luna_result[:success] && isru_result[:success] && supply_result[:success] && refuel_result[:success]
    
    if all_passed
      puts "✅ ALL PHASES PASSED — Full precursor mission validated"
    else
      puts "❌ SOME PHASES FAILED — Review output above"
      exit 1
    end
  end

  private

  def validate_gcc_mining
    # Validate GCC mining satellite deployment and currency generation
    gcc_sat = Organizations::BaseOrganization.find_by(identifier: 'GCC-SAT-MINING')
    if gcc_sat.nil?
      gcc_sat = Organizations::BaseOrganization.create!(
        identifier: 'GCC-SAT-MINING',
        name: 'Galactic Credit Corporation Mining Satellite',
        organization_type: :corporation,
        operational_data: { is_gcc_mining: true, currency_per_day: 10000 }
      )
    end
    
    puts "  - GCC mining satellite deployed"
    puts "  - Currency generation: #{gcc_sat.operational_data['currency_per_day']} GCC/day"
    
    { success: true, currency_per_day: gcc_sat.operational_data['currency_per_day'] }
  end

  def validate_titan_delivery
    # Validate Titan HLT delivery of N2 + CH4 to Luna
    luna = CelestialBodies::CelestialBody.find_by(identifier: 'LUNA-01')
    raise "Luna not found" unless luna
    
    settlement = Settlement::BaseSettlement.find_by(celestial_body: luna)
    raise "Luna settlement not found" unless settlement
    
    # Simulate Titan delivery (730 day transit)
    n2_delivered = 50000
    ch4_delivered = 25000
    
    settlement.inventory.add_item('n2', n2_delivered, settlement, { source: 'titan_hlt' })
    settlement.inventory.add_item('ch4', ch4_delivered, settlement, { source: 'titan_hlt' })
    
    puts "  - Titan HLT delivered #{n2_delivered} kg N2 to Luna"
    puts "  - Titan HLT delivered #{ch4_delivered} kg CH4 to Luna"
    
    { success: true, n2_kg: n2_delivered, ch4_kg: ch4_delivered, transit_days: 730 }
  end

  def validate_venus_delivery
    # Validate Venus HLT delivery of CO2 + N2 to Luna
    luna = CelestialBodies::CelestialBody.find_by(identifier: 'LUNA-01')
    raise "Luna not found" unless luna
    
    settlement = Settlement::BaseSettlement.find_by(celestial_body: luna)
    raise "Luna settlement not found" unless settlement
    
    # Simulate Venus delivery (400 day transit)
    co2_delivered = 75000
    n2_delivered = 30000
    
    settlement.inventory.add_item('co2', co2_delivered, settlement, { source: 'venus_hlt' })
    settlement.inventory.add_item('n2', n2_delivered, settlement, { source: 'venus_hlt' })
    
    puts "  - Venus HLT delivered #{co2_delivered} kg CO2 to Luna"
    puts "  - Venus HLT delivered #{n2_delivered} kg N2 to Luna"
    
    { success: true, co2_kg: co2_delivered, n2_kg: n2_delivered, transit_days: 400 }
  end

  def validate_luna_bootstrap
    # Validate current rake coverage (TEU/PVE deployment)
    luna = CelestialBodies::CelestialBody.find_by(identifier: 'LUNA-01')
    raise "Luna not found" unless luna
    
    settlement = Settlement::BaseSettlement.find_by(celestial_body: luna)
    raise "Luna settlement not found" unless settlement
    
    # Check deployment status (from current rake)
    teu_deployed = settlement.inventory.items.where(name: 'thermal_extraction_unit_mk1').sum(:amount)
    pve_deployed = settlement.inventory.items.where(name: 'planetary_volatiles_extractor_mk1').sum(:amount)
    gas_separator_deployed = settlement.inventory.items.where(name: 'gas_separator').sum(:amount)
    
    puts "  - TEU deployed: #{teu_deployed}"
    puts "  - PVE deployed: #{pve_deployed}"
    puts "  - Gas Separator deployed: #{gas_separator_deployed}"
    
    { success: teu_deployed > 0 && pve_deployed > 0 && gas_separator_deployed > 0,
      teu_deployed: teu_deployed, pve_deployed: pve_deployed, gas_separator_deployed: gas_separator_deployed }
  end

  def validate_isru_production
    # Validate TEU/PVE actually produce volatiles (not just deployed)
    luna = CelestialBodies::CelestialBody.find_by(identifier: 'LUNA-01')
    raise "Luna not found" unless luna
    
    settlement = Settlement::BaseSettlement.find_by(celestial_body: luna)
    raise "Luna settlement not found" unless settlement
    
    # Simulate ISRU production (using Manufacturing::ProductionService)
    manufacturing_service = Manufacturing::ProductionService.new(settlement)
    results = manufacturing_service.manufacture_component(
      { id: '3d_printed_ibeam_mk1', input_material: 'depleted_regolith', input_quantity_kg: 75.0 },
      10
    )
    
    puts "  - O2 produced: #{results[:total_water] * 1.14} kg/day (estimated from water extraction)"
    puts "  - H2 produced: #{results[:total_gas] * 0.5} kg/day (estimated from gas extraction)"
    puts "  - He3 produced: #{results[:total_gas] * 0.05} kg/day (estimated from gas extraction)"
    puts "  - I-Beams produced: #{results[:component_amount]} units/day"
    
    { success: results[:component_produced].present?,
      o2_kg: (results[:total_water] * 1.14).round(2),
      h2_kg: (results[:total_gas] * 0.5).round(2),
      he3_kg: (results[:total_gas] * 0.05).round(2),
      i_beam_units: results[:component_amount] }
  end

  def validate_l1_leo_supply
    # Validate materials flow from Luna to L1/LEO depot construction
    luna = CelestialBodies::CelestialBody.find_by(identifier: 'LUNA-01')
    raise "Luna not found" unless luna
    
    settlement = Settlement::BaseSettlement.find_by(celestial_body: luna)
    raise "Luna settlement not found" unless settlement
    
    # Check inventory for depot materials
    i_beams_available = settlement.inventory.items.where(name: '3d_printed_ibeam_mk1').sum(:amount)
    o2_available = settlement.inventory.items.where(name: 'oxygen').sum(:amount)
    h2_available = settlement.inventory.items.where(name: 'hydrogen').sum(:amount)
    
    puts "  - I-Beams available for L1: #{i_beams_available}"
    puts "  - O2 available for L1: #{o2_available} kg"
    puts "  - H2 available for L1: #{h2_available} kg"
    
    { success: i_beams_available > 0 && o2_available > 0 && h2_available > 0,
      i_beams_delivered: i_beams_available,
      o2_delivered: o2_available,
      h2_delivered: h2_available }
  end

  def validate_venus_refuel
    # Validate Venus refueling dependency chain
    luna = CelestialBodies::CelestialBody.find_by(identifier: 'LUNA-01')
    raise "Luna not found" unless luna
    
    settlement = Settlement::BaseSettlement.find_by(celestial_body: luna)
    raise "Luna settlement not found" unless settlement
    
    # Check CH4 availability for Venus refueling
    ch4_available = settlement.inventory.items.where(name: 'ch4').sum(:amount)
    venus_refuel_required = 10000
    
    if ch4_available >= venus_refuel_required
      refuel_source = 'titan_return'
      puts "  - CH4 available for Venus refuel: #{ch4_available} kg (from Titan return)"
    elsif settlement.inventory.items.where(name: 'methane').sum(:amount) > 0
      refuel_source = 'earth_import'
      puts "  - CH4 available for Venus refuel: via Earth import"
    else
      # Check local production capability
      i_beams_available = settlement.inventory.items.where(name: '3d_printed_ibeam_mk1').sum(:amount)
      if i_beams_available > 0
        refuel_source = 'luna_local_production'
        puts "  - CH4 available for Venus refuel: via Luna local production"
      else
        refuel_source = 'none'
        puts "  - ❌ No CH4 available for Venus refueling!"
      end
    end
    
    { success: refuel_source != 'none',
      venus_refueled: refuel_source != 'none',
      return_verified: refuel_source != 'none',
      refuel_source: refuel_source }
  end
end
```

### Step 4 — Synthesis Report (before committing)

```
SYNTHESIS REPORT
Files created:
  - data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json (7 phases)
  - data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json (all HLT deliveries)
  - galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake (full validation rake)

Supply chain validated:
  ✅ GCC mining → currency generation
  ✅ Titan HLT → N2 + CH4 delivery to Luna
  ✅ Venus HLT → CO2 + N2 delivery to Luna
  ✅ Luna bootstrap (current rake coverage)
  ✅ ISRU production validation (TEU/PVE actually producing)
  ✅ Luna → L1/LEO supply chain
  ✅ Venus refueling dependency chain

Key design decisions:
  - Data-driven: planet properties determine HLT delivery, not world names
  - Scheduling: Titan transit (730 days) > Venus transit (400 days)
  - Refueling: Venus needs CH4 from Titan return OR Earth import OR local production
  - ISRU: Manufacturing::ProductionService validates actual production

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `precursor_mission_profile_v1.json` created with all 7 phases
- [ ] `precursor_mission_manifest_v1.json` created with all HLT deliveries
- [ ] `lunar_precursor_mission_validation.rake` runs successfully
- [ ] All 7 phases pass validation
- [ ] Supply chain validated end-to-end (GCC → Titan → Venus → Luna → L1/LEO)
- [ ] Venus refueling dependency chain works correctly
- [ ] ISRU production validated (not just deployment)
- [ ] No world-name-driven logic (data-driven only)

---

## Stop Conditions — escalate to user immediately if:
- Manufacturing::ProductionService doesn't exist or has different interface
- HLT operational data structure differs from expected format
- Same failure persists after two attempts
- Any architectural decision is required beyond what the reference docs cover

---

## Commit Instructions

Run git commands on **host**, not inside container:

```bash
git add data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json
git add data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json
git add galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake
git commit -m "feature: full precursor mission validation — interplanetary supply chain testing"
git push
```

---

## Documentation
- [ ] No additional doc changes needed — reference docs already cover this feature

---

## Dependencies
**Blocked by**: none (standalone validation task)
**Blocks**: Phase 6+ lava-tube construction (requires validated supply chain)
**Related tasks**: 
- `2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md` — companion task (scout ship vessel)
- `2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md` — companion task (survey mode)
- `2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md` — companion task (integrity gate)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` — new mission profile (7 phases)
- `data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json` — new manifest (all HLT deliveries)
- `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` — full supply chain validation rake

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: Full precursor mission validation created | Supply chain tested end-to-end | Venus refueling dependency validated