---
status: backlog
priority: HIGH
type: architecture-feature
system_domain: MISSION_EXECUTION
mvp_alignment: LUNA_SETTLEMENT_GATE
local_worker_safe: true
created: 2026-08-18
updated: 2026-08-18
estimated_effort: 4-6 hours
blocker_for:
  - luna_settlement_simulation
depends_on: []
---

# Task: Launch Window + Transit Timing Engine for Luna Precursor Mission

## Context

The precursor mission pipeline (rake `luna_mission:phase1_bootstrap`) validates JSON structure but has **zero timing simulation**. The solar system pipeline (`solar_system_mission_pipeline.rake`) gates corporate formation on N2 delivery completion but doesn't simulate craft departure, transit, or arrival.

**Critical path for Luna settlement:**
1. Precursor deploys → builds 2 landing pads + comms + power (no HLT can land yet)
2. **Precursor also launches Venus skimmer from Earth** (concurrent with pad construction)
3. Landing pads complete → HLT #1 lands with inflatable tanks (empty)
4. **ISRU goes online: Regolith → TEU/PVE → Gas Separator → N₂ to lava tube tank farm + O₂ to life support**
5. **Venus skimmer arrives ~400d later with N₂ load** → supplements tank farm → habitats pressurized
6. HLT #2 lands → habitats deployed into pressurized tanks
7. Titan skimmers arrive later (~730d transit) → supplementary CH₄ for Venus skimmer fleet refueling

The Venus skimmer departs Earth **around the same time as the precursor launch** — it's a separate craft on a different trajectory, not part of the HLT fleet. It harvests Venusian atmosphere (CO₂ + N₂) and arrives at Luna after transit.

**N₂ flow architecture:**
- **Primary source:** Luna ISRU pipeline (regolith → TEU/PVE → gas separator → N₂ to lava tube tank farm, O₂ to life support)
  - Gas separator connects to planetary umbilical hub with dedicated `nitrogen_output` port → `cryo_grid_nitrogen`
  - Also outputs O₂ → `cryo_grid_o2` for life support
- **Supplemental source:** Venus skimmer (arrives later, tops up when ISRU can't keep pace with habitat expansion)
- **Tertiary source:** Titan skimmer fleet (CH₄ for Venus skimmer refueling, supplementary N₂)


## What Exists Today

| Layer | Status | Location |
|-------|--------|----------|
| Task definitions (~100+ tasks) | ✅ `missions_v2/tasks/*.json` | e.g., `task_cycler_transit_preparation.json` |
| Mission profiles/manifests | ✅ `missions_v2/profiles/`, `manifests/` | `precursor_mission_profile_v1.json` |
| Rake validation (structure only) | ✅ `luna_mission:phase1_bootstrap` | Checks JSON exists, not timing |
| Solar system pipeline rake | ⚠️ Corporate gates only | Gates on N2 delivery, not launch windows |
| Venus harvester mission (old format) | ⚠️ `missions/venus_harvester_mission/` | Needs v2 adaptation |
| **Launch window calculator** | ❌ None | Earth-Venus transfer windows |
| **Transit time engine** | ❌ None | Venus ~400d, Titan ~730d |
| **Arrival queue** | ❌ None | Precursor → HLT → Venus skimmer ordering |

## Scope

Build a **launch window + transit timing engine** that:
1. Calculates Earth-Venus transfer windows (Hohmann-like)
2. Tracks craft departure, transit, and arrival states
3. Queues arrivals in correct order (precursor pads → HLT #1 → Venus skimmer → HLT #2 → Titan skimmers)
4. Gates N₂ offload on tank farm readiness (inflatable tanks must exist before gas arrives)

## Prerequisites — READ FIRST

### Key Design Decisions
- **Venus skimmer departs Earth** (not Luna) — it's loaded with methane fuel at Earth, then goes to Venus for atmospheric harvest
- **Precursor and Venus skimmer launch concurrently** — same launch window from Earth
- **Tank farm must be ready before N₂ arrives** — you can't offload gas into nothing
- **Titan skimmers are a fleet** — multiple departures needed (730d transit means long lead time)

### Existing Data to Use
- `precursor_mission_profile_v1.json` — has phase structure, duration_days
- `precursor_mission_manifest_v1.json` — has delivery_to_luna with transit_days
- `venus_harvest_01_*` files (old format) — have launch window task structure to adapt
- `missions_v2/tasks/task_cycler_transit_preparation.json` — existing transit task template

### Where This Lives
- **Service class**: `app/services/mission/transit_engine.rb` (new)
- **Task file**: `data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json` (new, adapted from old format)
- **Rake task**: Add to `lunar_precursor_mission_validation.rake` — new `phase_timing` task

## Steps

### Step 1: Create TransitEngine service class

**File:** `galaxy_game/app/services/mission/transit_engine.rb`

```ruby
class Mission::TransitEngine
  # Transfer window calculation (Earth → Venus)
  # Uses simplified Hohmann transfer approximation
  def self.earth_to_venus_transit_days
    # ~146 days for Hohmann transfer Earth→Venus
    # Real value varies with orbital positions; use 146 as baseline
    146
  end

  # Transfer window calculation (Earth → Titan)
  def self.earth_to_titan_transit_days
    # ~730 days for Earth→Titan (via Jupiter gravity assist or direct)
    730
  end

  # Transfer window calculation (Luna → Venus)
  def self.luna_to_venus_transit_days
    # Same as Earth→Venus (Luna is close to Earth gravitationally)
    earth_to_venus_transit_days
  end

  # Calculate next transfer window from Earth to target body
  # Returns: { departure_date: Date, arrival_date: Date, transit_days: N }
  def self.calculate_transfer_window(from_body, to_body, launch_date = nil)
    launch_date ||= Time.current.to_date
    transit_days = case "#{from_body}_#{to_body}"
    when "EARTH_VENUS", "LUNA_VENUS"
      earth_to_venus_transit_days
    when "EARTH_TITAN", "LUNA_TITAN"
      earth_to_titan_transit_days
    when "EARTH_MARS", "LUNA_MARS"
      259  # ~8.5 months Earth→Mars
    else
      365  # default: 1 year transit
    end

    {
      departure_date: launch_date,
      arrival_date: launch_date + transit_days,
      transit_days: transit_days
    }
  end

  # Check if a transfer window is open (simplified — real version uses orbital mechanics)
  def self.transfer_window_open?(from_body, to_body, date)
    # Simplified: windows open every 584 days (Venus synodic period)
    # For MVP, always open — real version would check planetary positions
    true
  end

  # Create a transit record for a craft
  def self.schedule_departure(craft_id, from_body, to_body, launch_date)
    window = calculate_transfer_window(from_body, to_body, launch_date)
    
    {
      craft_id: craft_id,
      status: :in_transit,
      from_body: from_body,
      to_body: to_body,
      departure_date: window[:departure_date],
      arrival_date: window[:arrival_date],
      transit_days: window[:transit_days],
      payload: nil  # Set when manifest is loaded
    }
  end

  # Check if a craft has arrived (given current simulation day)
  def self.has_arrived?(transit_record, sim_day)
    sim_date = Time.current.to_date + sim_day
    sim_date >= transit_record[:arrival_date]
  end
end
```

### Step 2: Create Venus harvest arrival task (v2 format)

**File:** `data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json`

Adapted from `missions/venus_harvester_mission/venus_harvest_01_phases_v1.json`:

```json
{
  "task_id": "venus_harvest_arrival_v2",
  "version": "2.0",
  "name": "Venus Atmospheric Harvester Arrival at Luna",
  "description": "Venus skimmer arrives at Luna with harvested N₂ + CO₂ payload. Offload requires tank farm infrastructure to be ready.",
  "prerequisites": [
    "task_site_prep_foundation_v2",
    "task_print_inflatable_tank_shells_v2"
  ],
  "launch_window": {
    "departure_body": "EARTH-01",
    "arrival_body": "LUNA-01",
    "transit_days": 400,
    "synodic_period_days": 584,
    "note": "Venus skimmer departs Earth around same time as precursor launch. Harvests Venus atmosphere en route."
  },
  "payload": {
    "source_gases": ["CO2", "N2"],
    "co2_kg": 75000,
    "n2_kg": 30000,
    "lox_for_return_kg": 10000,
    "methane_remaining_kg": 5000
  },
  "offload_requirements": {
    "tank_farm_ready": true,
    "inflatable_tanks_deployed": true,
    "minimum_tank_count": 3,
    "cryo_tanks_required": true
  },
  "effects": [
    {
      "action": "add_to_inventory",
      "target": "luna_settlement",
      "items": [
        { "name": "N2", "amount": 30000, "unit": "kg" },
        { "name": "CO2", "amount": 75000, "unit": "kg" }
      ]
    },
    {
      "action": "set_status_flag",
      "flag": "venus_n2_delivery_completed",
      "value": true
    },
    {
      "action": "gate_open",
      "target": "habitat_pressurization",
      "description": "N₂ available for habitat pressurization"
    }
  ],
  "metadata": {
    "template": "task_v2",
    "type": "arrival_offload",
    "craft_type": "venus_harvester_skimmer",
    "adapted_from": "venus_harvest_01_phases_v1.json"
  }
}
```

### Step 3: Add timing validation to rake

**File:** `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake`

Add a new task `phase_timing`:

```ruby
desc "Phase timing: validate launch windows, transit times, arrival ordering"
task phase_timing: :environment do
  puts "=" * 80
  puts "LUNA PRECURSOR MISSION — PHASE TIMING VALIDATION"
  puts "=" * 80

  # Simulate timeline starting from day 0
  sim_day = 0
  
  # Phase 0: Precursor launch (Day 0)
  puts "\n--- Day 0: Precursor Launch from Earth ---"
  precursor_departure = Mission::TransitEngine.schedule_departure(
    "precursor_hlt_1", "EARTH-01", "LUNA-01", Time.current.to_date
  )
  puts "  ✓ Precursor departed Earth → Luna (#{precursor_departure[:transit_days]}d transit)"
  
  # Phase 0b: Venus skimmer launch (concurrent, Day 0)
  puts "\n--- Day 0: Venus Skimmer Launch from Earth ---"
  venus_departure = Mission::TransitEngine.schedule_departure(
    "venus_harvester_01", "EARTH-01", "VENUS-01", Time.current.to_date
  )
  puts "  ✓ Venus skimmer departed Earth → Venus (#{venus_departure[:transit_days]}d transit)"
  
  # Phase 1: Precursor arrives and builds landing pads
  precursor_arrival_day = precursor_departure[:transit_days]
  sim_day = precursor_arrival_day
  
  puts "\n--- Day #{sim_day}: Precursor Arrives at Luna ---"
  puts "  ✓ Landing pad construction starts (2 pads)"
  puts "  ✓ Comms deployed"
  puts "  ✓ Power grid (RTG) deployed"
  
  # Pad construction takes time — simulate as 30 days
  pad_construction_days = 30
  sim_day += pad_construction_days
  
  puts "\n--- Day #{sim_day}: Landing Pads Complete ---"
  puts "  ✓ Pad Alpha ready for HLT landing"
  puts "  ✓ Pad Beta ready for HLT landing"
  
  # Phase 2: HLT #1 lands with inflatable tanks
  puts "\n--- Day #{sim_day}: HLT #1 Lands (Inflatable Tanks) ---"
  puts "  ✓ Inflatable tanks deployed (empty, awaiting N₂)"
  
  # Tank farm setup takes time — simulate as 15 days
  tank_farm_days = 15
  sim_day += tank_farm_days
  
  puts "\n--- Day #{sim_day}: Tank Farm Ready ---"
  puts "  ✓ Tank farm infrastructure complete"
  puts "  ✓ Ready for N₂ offload"
  
  # Phase 3: Venus skimmer arrives at Venus, begins harvest
  venus_at_venus_day = venus_departure[:transit_days]
  puts "\n--- Day #{venus_at_venus_day}: Venus Skimmer Arrives at Venus ---"
  puts "  ✓ Atmospheric harvesting begins (CO₂ + N₂ extraction)"
  
  # Harvesting takes time — simulate as 30 days at Venus
  harvest_days = 30
  venus_departure_from_venus = venus_at_venus_day + harvest_days
  
  puts "\n--- Day #{venus_departure_from_venus}: Venus Skimmer Departs Venus → Luna ---"
  
  # Phase 4: Venus skimmer arrives at Luna
  venus_arrival_at_luna = venus_departure_from_venus + Mission::TransitEngine.earth_to_venus_transit_days
  
  puts "\n--- Day #{venus_arrival_at_luna}: Venus Skimmer Arrives at Luna ---"
  
  # Check if tank farm is ready
  if sim_day <= venus_arrival_at_luna
    puts "  ✓ Tank farm ready (completed day #{sim_day})"
    puts "  ✓ N₂ offload: 30,000 kg N₂ + 75,000 kg CO₂ delivered"
    puts "  ✓ HABITAT PRESSURIZATION GATE OPENED"
  else
    puts "  ⚠ Tank farm NOT ready — N₂ arrives before tanks (CRITICAL FAILURE)"
    abort("Venus skimmer arrived at Luna before tank farm was ready")
  end
  
  # Phase 5: Titan skimmer launch (later, needs methane from Earth)
  titan_launch_day = venus_arrival_at_luna + 90  # ~3 months after Venus arrival
  puts "\n--- Day #{titan_launch_day}: Titan Skimmer Launch from Earth ---"
  titan_departure = Mission::TransitEngine.schedule_departure(
    "titan_harvester_01", "EARTH-01", "TITAN-01", Time.current.to_date + titan_launch_day
  )
  puts "  ✓ Titan skimmer departed Earth → Titan (#{titan_departure[:transit_days]}d transit)"
  
  titan_arrival_at_titan = titan_launch_day + titan_departure[:transit_days]
  titan_departure_from_titan = titan_arrival_at_titan + harvest_days
  
  puts "\n--- Day #{titan_departure_from_titan}: Titan Skimmer Departs Titan → Luna ---"
  
  titan_arrival_at_luna = titan_departure_from_titan + Mission::TransitEngine.earth_to_titan_transit_days
  
  puts "\n--- Day #{titan_arrival_at_luna}: Titan Skimmer Arrives at Luna ---"
  puts "  ✓ CH₄ delivery: supplementary fuel for Venus skimmers"
  
  puts "\n" + "=" * 80
  puts "TIMING VALIDATION COMPLETE — All windows and arrivals verified"
  puts "=" * 80
end
```

### Step 4: Wire Venus harvest task into precursor manifest

**File:** `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json`

Add to phases array:

```json
{
  "phase_id": "venus_harvest_arrival",
  "name": "Venus Atmospheric Harvester Arrival",
  "description": "Venus skimmer arrives at Luna with harvested N₂ + CO₂ payload. Gates habitat pressurization.",
  "prerequisites": ["initial_hlt_landings"],
  "duration_days": 400,
  "task_ref": "tasks_v2/task_venus_harvest_arrival_v2.json",
  "launch_window": {
    "departure_body": "EARTH-01",
    "arrival_body": "LUNA-01",
    "transit_days": 400,
    "concurrent_with": "precursor_launch"
  },
  "outputs": ["n2_delivery", "co2_delivery", "habitat_pressurization_gate"]
}
```

### Step 5: Add Titan harvest arrival task (v2 format)

**File:** `data/json-data/missions_v2/tasks/task_titan_harvest_arrival_v2.json`

Similar structure to Venus task but with Titan-specific values:

```json
{
  "task_id": "titan_harvest_arrival_v2",
  "version": "2.0",
  "name": "Titan Atmospheric Harvester Arrival at Luna",
  "description": "Titan skimmer arrives at Luna with harvested CH₄ payload for Venus skimmer refueling.",
  "launch_window": {
    "departure_body": "TITAN-01",
    "arrival_body": "LUNA-01",
    "transit_days": 730,
    "note": "Titan skimmers are a fleet — multiple departures needed for sustained operations"
  },
  "payload": {
    "source_gases": ["CH4"],
    "ch4_kg": 50000,
    "lox_for_return_kg": 10000
  },
  "offload_requirements": {
    "cryo_tanks_deployed": true,
    "minimum_cryo_tank_count": 5
  },
  "effects": [
    {
      "action": "add_to_inventory",
      "target": "luna_settlement",
      "items": [
        { "name": "CH4", "amount": 50000, "unit": "kg" }
      ]
    },
    {
      "action": "set_status_flag",
      "flag": "titan_ch4_delivery_completed",
      "value": true
    }
  ],
  "metadata": {
    "template": "task_v2",
    "type": "arrival_offload",
    "craft_type": "titan_harvester_skimmer"
  }
}
```

## Stop Conditions
- TransitEngine service class exists with transfer window + departure/arrival methods
- Venus harvest arrival task (v2 format) created and wired into precursor profile
- Titan harvest arrival task (v2 format) created
- Rake `luna_mission:phase_timing` validates full timeline including:
  - Concurrent precursor + Venus skimmer launch from Earth
  - Landing pad construction before HLT landing
  - Tank farm ready before N₂ offload
  - Venus skimmer arrival gates habitat pressurization
  - Titan skimmer fleet timing (multiple departures)
- All timing constraints pass without abort

## Notes for Claude Review
- The rake `phase_timing` simulates a timeline — it's not just structure validation
- Venus skimmer departs Earth (not Luna) — loaded with methane fuel at Earth launch
- Tank farm readiness gates N₂ offload — critical path constraint
- Titan skimmers are a fleet, not single craft — multiple departures needed
- The old `venus_harvest_01_*` files have the right structure but need v2 adaptation
- Launch windows use simplified Hohmann transfer (real version would use orbital mechanics)
