I am unable to generate direct file download links or attachments in this chat interface.

However, you can save each document into your local repository's docs/design/ directory by copying the contents of each file from the block below, or by using a quick command in your terminal.

Option 1: Terminal One-Liner (Fastest)
If you are on Linux, macOS, or WSL in your Rails project root, you can create the directory and write all 6 files at once:

Bash
mkdir -p docs/design
cat << 'EOF' > docs/design/ECLSS_PARAMETERS.md
# ECLSS_PARAMETERS.md

## Overview
This document defines the core constants, formulas, and baseline parameters for the Environmental Control and Life Support System (ECLSS) in *Galaxy Game*. It converts real-world spaceflight operational metrics into game engine variables that drive commodity consumption, structural decay, crew health penalties, and emergency response thresholds. Reference this document when implementing backend tick processors, tuning station maintenance sinks, or balancing life support module upgrades.

## Source Basis
Parameters in this document are derived directly from empirical telemetry and operational data from NASA and ISS ECLSS subsystems. Key source baselines include ISS Brine Processor Assembly performance metrics, structural micro-leak tracking across module joints, microgravity human physiological degradation studies, and standard orbital emergency resupply protocols.

## System Formulas & Mathematical Models

### 1. Water Recycling and Compound Loss
The water recovery loop models distillation and catalytic processing. A hard efficiency ceiling guarantees that habitats cannot function as perpetual closed loops, driving ongoing market demand for raw ice and volatile imports.

Daily Water Loss (kg) = (Crew Count * Daily Consumption per Crew) * (1 - Module Efficiency)

* **Standard Crew Water Intake:** 3.5 kg/person/day (hydration, food rehydration, basic hygiene).
* **Compound Volume Decay over N Cycles:** Remaining Water = Initial Volume * (Efficiency)^N

### 2. Micro-Leaks and Thermal Expansion Decay
Atmospheric leakage accounts for passive gas loss through hull welds, seals, and airlock cycles. The loss rate scales with exposed surface area and is multiplied by thermal expansion stress caused by direct solar exposure (180°C temperature deltas).

Daily Atmospheric Loss (kg) = (Base Leak Rate / 7) * (Exposed Surface Area / 100) * Thermal Multiplier

* **Atmospheric Composition Drain:** 78% N2, 21% O2, 1% Trace Gases.
* **Thermal Mitigation:** Sub-surface or regolith-shielded habitats set the Thermal Multiplier to 1.0 due to subterranean temperature stabilization.

### 3. Microgravity Physiological Degradation
Crew members stationed in environments below 0.9g experience progressive physical degradation unless countermeasures or rotating habitats are utilized.

Daily Crew Health Decay = (0.01 / 30) * (1.0 - Effective g-force)

* At 0g, crew health/efficiency degrades by 1% per 30-day period (0.033% per day).
* Medical commodity consumption (e.g., `CalciumSupplements`, `Medkits`) doubles when Effective g-force < 0.1.

---

## Game Parameters / Constants

| Constant Name | Value | Source | Engine Notes |
| :--- | :--- | :--- | :--- |
| `BASE_WATER_RECOVERY_EFFICIENCY` | `0.98` | ISS Brine Processor | Top-tier mechanical recycling limit |
| `LOW_TIER_WATER_EFFICIENCY` | `0.93` | Legacy ISS Assembly | Un-upgraded or damaged module baseline |
| `CREW_WATER_DAILY_KG` | `3.5` | NASA Bioastronautics | Per-capita daily metabolic requirement |
| `BASE_MICRO_LEAK_KG_PER_WEEK` | `0.4536` | ISS ~1mm Leak Data | Baseline loss (1 lb/week) per 100 m^2 hull |
| `THERMAL_CYCLING_MULTIPLIER` | `1.8` | Orbital Stress Data | Applied to unshielded exterior hulls |
| `REGOLITH_SHIELD_MULTIPLIER` | `1.0` | Subsurface Thermal Data | Applied when regolith depth >= 3.0m |
| `ZERO_G_BONE_LOSS_PER_MONTH` | `0.01` | NASA Space Physiology | 1% skeletal density loss per 30 days |
| `CRITICAL_BUFFER_THRESHOLD_DAYS`| `14` | ISS Resupply Timelines | Triggers high-priority market buy orders |
| `EMERGENCY_BUFFER_THRESHOLD_DAYS`| `3` | ISS Emergency Protocols | Triggers morale collapse and health decay |

---

## Rails Implementation Notes

This document directly impacts the background tick processing engines and resource accounting logic.

* **Affected Models:** `HabitationNode`, `ResourceBuffer`, `InstalledModule`, `CrewGroup`.
* **Primary Service:** `ProcessEclssTickService`.

```ruby
class ProcessEclssTickService
  BASE_WATER_ECLSS_EFFICIENCY = 0.98
  MICRO_LEAK_KG_PER_WEEK      = 0.453592 # 1 lb per week per 100m^2
  BONE_DENSITY_LOSS_PER_DAY   = 0.01 / 30.0 # 1% loss per month in 0g

  def initialize(habitation_node)
    @node = habitation_node
    @buffer = habitation_node.resource_buffer
  end

  def call
    ActiveRecord::Base.transaction do
      process_water_loop!
      process_atmospheric_leaks!
      process_crew_gravity_impact!
      evaluate_emergency_thresholds!
    end
  end

  private

  def process_water_loop!
    water_module = @node.installed_modules.find_by(module_type: 'WaterRecoveryUnit')
    efficiency = water_module&.operational? ? water_module.efficiency : 0.90

    total_water_needed = @node.crew_count * @node.daily_water_per_crew
    water_recycled = total_water_needed * efficiency
    unrecoverable_loss = total_water_needed - water_recycled

    @buffer.deduct!(:purified_water, unrecoverable_loss)
    @buffer.add!(:sludge_waste, unrecoverable_loss)
  end

  def process_atmospheric_leaks!
    thermal_factor = @node.exposed_to_thermal_cycling? ? THERMAL_CYCLING_MULTIPLIER : 1.0
    daily_leak = (MICRO_LEAK_KG_PER_WEEK / 7.0) * (@node.exposed_surface_area / 100.0) * thermal_factor

    # Drains proportionally to standard atmospheric mix
    @buffer.deduct!(:nitrogen, daily_leak * 0.78)
    @buffer.deduct!(:oxygen, daily_leak * 0.21)
  end

  def process_crew_gravity_impact!
    return if @node.effective_g_force >= 0.9

    gravity_deficit = 1.0 - @node.effective_g_force
    degradation = BONE_DENSITY_LOSS_PER_DAY * gravity_deficit

    @node.crew_groups.find_each do |crew|
      crew.degrade_health!(degradation)
      # Increase medical supply consumption under low gravity
      @buffer.deduct!(:medical_supplies, crew.size * 0.05)
    end
  end

  def evaluate_emergency_thresholds!
    days_remaining = @buffer.calculate_days_remaining(:purified_water)
    if days_remaining <= EMERGENCY_BUFFER_THRESHOLD_DAYS
      @node.trigger_emergency_protocol!
    elsif days_remaining <= CRITICAL_BUFFER_THRESHOLD_DAYS
      @node.issue_automated_market_buy_orders!
    end
  end
end
Open Design Questions
Dynamic Efficiency Degradation: Should module efficiency decay linearly with usage time, requiring maintenance items (FilterCartridge, CatalyticBed) to maintain the 98% water recovery cap?

Airlock Cycle Losses: Should airlock cycling for EVAs/docking deduct a fixed gas volume per event, or should it be abstracted entirely into the continuous micro-leak rate?

Related Files
ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md — Details how resource loss rates fuel orbital market demand.

ECLSS_SYSTEM_ARCHITECTURE.md — Details the 6 functional life support loops and cascading failures.

HABITATION_NODE_ARCHITECTURE.md — Class structures for nodes running the ECLSS tick.
EOF

cat << 'EOF' > docs/design/ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md

ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md
Overview
This document outlines the core economic hierarchy and industrial trade engine of Galaxy Game. It details the asymmetric relationship between planetary surface bases (infinite resource generators) and orbiting nodes/cyclers (vacuum-suspended resource consumers). Reference this document when designing resource supply chains, balancing Mass Driver launch costs, structuring GCC market buy orders, and configuring automated trade AI.

Source Basis
This architecture is derived from orbital mechanics constraints, space logistics studies, and in-game economic balancing decisions. The model relies on gravity well delta-v asymmetry and life support maintenance sinks to maintain open-ended market velocity.

Structural Hierarchy & Asymmetric Architecture
                  SURFACE SETTLEMENT (Luna / Mars / Ceres)
                [Extractable Resources: Regolith, O2, Ice, Ore]
                                      │
                                      │ Bulk Extraction
                                      ▼
                           SURFACE MASS DRIVERS / LAUNCHERS
                                      │
                                      │ (Cheap Raw Mass Export)
                                      ▼
                        ORBITAL DEPOTS / STATIONS (EML-1)
                     [Refining, Assembly, Market Hubs]
                                      │
                                      │ Refueling & Resupply
                                      ▼
                             CYCLER CRAFT / SHIPS
                      [Transit Habitats & Gas Haulers]
1. Surface Settlements: The Extraction Engine
Economic Role: Primary resource producer.

Physical Advantage: Taps into planetary crusts and polar traps for raw materials (Regolith, Water Ice, Metal Ores, Volatiles).

Operational Edge: Uses local planetary mass for free radiation shielding (burial under regolith) and deep crust thermal sinking.

2. Orbital Stations & Cyclers: The Consumption Engine
Economic Role: Transit hubs, refining complexes, and market liquidity centers.

Physical Vulnerability: Zero native raw matter generation. Every gram of structural mass or volatile liquid must be imported from surface mass drivers or atmospheric skimmers.

Operational Edge: Minimal gravity wells allow friction-free ship arrivals, zero-g construction, and low-energy orbital transfers.

Resource Logistics Matrix
To ensure realistic dynamic flows across nodes, commodities are categorized by their extraction points, transit methods, and primary economic sinks:

Resource / Commodity	Surface Source	Orbital / Space Source	Primary Consumption / Sink
Oxygen (O2)	Regolith reduction, Polar ice	Sabatier/Plant loop recovery	ECLSS breathing, Rocket Propellant
Water (H2O)	Polar crater/glacier mining	Atmospheric Skimmer imports	ECLSS intake, Agriculture, Radiation Shielding
Nitrogen (N2)	Rare on Luna/Mars	Venus/Titan Atmospheric Skimmers	Habitat pressure buffers, Leak replenishment
Structural Mass	Local Foundries (Fe/Al/Anorthite)	Orbital 3D Printing Yards	Station frame expansion, Panel maintenance
Methane (CH4)	Sabatier synthesis from surface ice	Gas Giant Skimmers	Ship propellant, Station RCS thrusters
Market Dynamics & The "Build vs. Buy" Loop
1. Inelastic Life Support Demand
Orbital depots and cyclers suffer continuous, non-negotiable material losses due to 2% water recycling inefficiencies and passive atmospheric micro-leaks. When internal reserves breach safety limits, nodes issue automated high-priority buy orders on the Galactic Credit Exchange (GCC) regardless of current pricing.

2. Mass Driver Economics
Surface settlements on low-gravity bodies (Luna, Ceres) utilize electromagnetic Mass Drivers to launch bulk payloads (O2, Ice, Refined Metals) directly to Lagrange Depots (e.g., EML-1) at near-zero fuel cost. This creates a severe cost advantage over Earth-surface imports, establishing localized regional monopolies.

3. Trader Arbitrage Mechanics
Because atmospheric gases (especially Nitrogen) are unevenly distributed across the solar system, traders profit from inter-planetary supply imbalances. For instance, Venusian nitrogen harvested by skimmers can be transported to Mars cyclers to undercut local scarcity prices.

Rails Implementation Notes
System nodes share a common base representation, using type discriminators to control local resource generation capabilities.

Affected Models: HabitationNode, ResourceBuffer, MarketOrder, TradeRoute.

Ruby
class HabitationNode < ApplicationRecord
  has_many :installed_modules
  has_one  :resource_buffer
  has_many :market_orders

  def surface_node?
    node_type == 'surface_settlement'
  end

  def process_eclss_tick!
    resource_buffer.consume_metabolic_supplies!
    resource_buffer.apply_micro_leaks!(hull_wear)

    if surface_node?
      # Extract raw surface deposits (Regolith, Polar Ice)
      extract_local_deposits!
    else
      # Process inbound freight and evaluate automated buy triggers
      process_docked_transfers!
      check_critical_eclss_deficits!
    end
  end

  private

  def check_critical_eclss_deficits!
    [:purified_water, :nitrogen, :oxygen].each do |commodity|
      days_left = resource_buffer.days_of_supply_remaining(commodity)
      next unless days_left < GameConstants::CRITICAL_BUFFER_THRESHOLD_DAYS

      MarketOrder.create_automated_buy_order!(
        node: self,
        commodity: commodity,
        quantity: resource_buffer.target_resupply_quantity(commodity),
        max_price: market_price_scaler(days_left)
      )
    end
  end
end
JSON Data Schema Example (Orbital Depot Buffer State)
JSON
{
  "node_id": "eml1_depot_alpha",
  "node_type": "orbital_depot",
  "local_extraction_capable": false,
  "eclss_status": {
    "water_reserve_days": 14,
    "nitrogen_reserve_days": 8,
    "structural_panels_in_stock": 12
  },
  "market_dependencies": [
    {
      "commodity": "nitrogen",
      "status": "critical_buy_order_active",
      "target_source": "venus_skimmer_imports"
    },
    {
      "commodity": "purified_water",
      "status": "stable",
      "target_source": "luna_mass_driver_exports"
    }
  ]
}
Open Design Questions
Mass Driver Trajectory Interception: Should players be able to intercept or alter the destination of unguided mass driver payloads in transit?

GCC Market Currency Uncoupling: At what threshold of orbital trade volume should the GCC (Galactic Credit Coupon) permanently uncouple its 1:1 conversion peg from the USD base?

Related Files
ECLSS_PARAMETERS.md — Defines loss rate formulas driving market demand.

LUNA_SETTLEMENT_LIFECYCLE.md — Details the Luna-to-L1 export lifecycle.

HABITATION_NODE_ARCHITECTURE.md — Object model for surface and orbital nodes.
EOF

cat << 'EOF' > docs/design/HABITATION_NODE_ARCHITECTURE.md

HABITATION_NODE_ARCHITECTURE.md
Overview
This document defines the unified object architecture, structural frame specifications, and modular panel slot system shared across all human habitats in Galaxy Game. Whether instantiated as a lunar crater base, an orbital Lagrange depot, or an interplanetary cycler, all habitats utilize identical structural logic and interface contracts. Reference this document when implementing habitat models, tile map renderers, or docking connection handlers.

Source Basis
This architecture leverages modular software patterns and standardized aerospace structural engineering concepts. By utilizing unified I-beam frame grids and interchangeable panel slots, the codebase eliminates duplicate logic between surface and space entities while enabling UI asset reuse across different environmental contexts.

Architectural Overview
                          ┌───────────────────────────┐
                          │     HABITATION NODE       │
                          │ (Base Hull & ECLSS Loop)  │
                          └─────────────┬─────────────┘
                                        │
             ┌──────────────────────────┼──────────────────────────┐
             ▼                          ▼                          ▼
   ┌───────────────────┐      ┌───────────────────┐      ┌───────────────────┐
   │   LUNA SURFACE    │      │   CYCLER CRAFT    │      │    L1/L2 DEPOT    │
   │  • Regolith Shield│      │  • Docked Skimmers│      │  • Docked Skimmers│
   │  • Fixed Sinks    │      │  • RCS Spin Array │      │  • Zero-G Framing │
   └───────────────────┘      └───────────────────┘      └───────────────────┘
1. Structural Frame & I-Beam Grid
All habitation nodes are constructed around a standardized structural grid made of heavy structural I-beams (sourced from titanium or refined lunar anorthite). This grid provides mounting slots for internal ECLSS machinery and external environmental panels.

2. The Interchangeable Panel System
Each grid slot on a node's exterior perimeter must be fitted with a panel type suited for its local environment:

Transparent Glass Panels: Low cost, high crew morale bonus. Offers zero radiation protection and high thermal transfer. Suitable only for interior plazas or underground shielded caverns.

Rugged Armor Panels: High structural integrity and micrometeorite resistance. Expensive; used on outer cycler hulls.

Photovoltaic Integrated Panels: Generates power directly from solar exposure but possesses lower structural integrity.

Sintered Regolith Shield Panels: Locally manufactured surface panels deployed over I-beam frames to insulate against radiation and extreme thermal swings.

Environmental & Structural Comparison Matrix
System / Feature	Luna Surface Base	Deep-Space Cycler	Orbital Depot (EML-1)
Structural Frame	Standard I-Beam (Buried/Surface)	Standard I-Beam (Spin Rigged)	Standard I-Beam (Open Lattice)
Docking Interface	Surface Pad / Tower	Universal Umbilical Port	Universal Umbilical Port
Artificial Gravity	Local 0.166g + Centrifuge	Rotational Hull / Tether (1g)	Microgravity (0g) / Centrifuge
Thermal Dissipation	Conduction into crust	External Radiator Wings	External Radiator Wings
Primary Shielding	Sintered Regolith / Lava Tube	Water Walls / Lead Polymer	Water Tanks / Armor Plates
Thermal Cycling Risk	Low (if buried)	Extreme (Direct Solar Delts)	Moderate (Orbital Shadowing)
Unified Docking & Cargo Transfer Architecture
All orbiting nodes and surface towers expose a standardized Universal Docking Interface. When volatile collection craft (e.g., Atmospheric Skimmers) dock with any node, the transaction executes through a single unified service:

Skimmer Cargo Buffer (N2 / O2) =[Docking Umbilical]=> Node ECLSS Storage Buffer

                          ┌────────────────────────┐
                          │     SKIMMER CRAFT      │
                          │ (Volatile/Gas Hauler)  │
                          └───────────┬────────────┘
                                      │
                         [Standard Docking Interface]
                                      │
             ┌────────────────────────┴────────────────────────┐
             ▼                                                 ▼
┌─────────────────────────┐                       ┌─────────────────────────┐
│     CYCLER HABITAT      │                       │     EML-1 DEPOT HAB     │
│ Transfer: N2/O2/Water   │                       │ Transfer: N2/O2/Water   │
│ Buffer: Gas Top-Off     │                       │ Buffer: Market Storage  │
└─────────────────────────┘                       └─────────────────────────┘
Game Parameters / Constants
Constant Name	Value	Source	Engine Notes
IBEAM_GRID_SLOT_CAPACITY	100	Architectural Spec	Max module units per frame cluster
GLASS_PANEL_MORALE_BONUS	+0.15	Habitat Comfort Index	Applied to crew inside transparent zones
GLASS_PANEL_RADIATION_MULT	3.5	Radiation Transport Model	Increases radiation exposure penalty
ARMOR_PANEL_INTEGRITY	500	Structural Test Data	Hit points against micro-meteorites
UNIVERSAL_DOCK_FLUID_RATE	50.0	Umbilical Flow Spec	Transfer rate (kg/second) for gases/liquids
Rails Implementation Notes & Data Schemas
Nodes use polymorphic associations or single-table inheritance to manage shared slot behaviors.

Models: HabitationNode, StructuralSlot, InstalledPanel, DockingPort.

JSON Schema (Node Configuration Data)
JSON
{
  "node_id": "node_cycler_hermes_01",
  "base_tileset_id": "i_beam_frame_large",
  "environment_context": "cycler_transit",
  "structural_integrity": {
    "hull_wear": 0.05,
    "total_slots": 4,
    "installed_panels": [
      {"slot_position": "north", "type": "panel_solar_integrated", "durability": 0.92},
      {"slot_position": "south", "type": "panel_transparent_cheap", "durability": 0.88},
      {"slot_position": "east", "type": "panel_rugged_armored", "durability": 0.99},
      {"slot_position": "west", "type": "panel_rugged_armored", "durability": 0.99}
    ]
  },
  "docking_ports": [
    {
      "port_id": "port_01",
      "interface_type": "skimmer_universal_umbilical",
      "status": "docked",
      "connected_craft_id": "skimmer_venus_04"
    }
  ]
}
Open Design Questions
Dynamic Panel Destruction: Should catastrophic panel failures (e.g., meteor impact on a glass panel) instantly depressurize adjacent grid sections or trigger a timed emergency response window?

Modular Visual Kitbashing: How should the 2D tile system visually render mixed panel configurations on a single sprite frame without generating excessive texture assets?

Related Files
ECLSS_PARAMETERS.md — Micro-leak and thermal cycling formulas applied to panel types.

ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md — Explains resource supply chains for structural panels.

ECLSS_SYSTEM_ARCHITECTURE.md — Details life support subsystems housed inside node frames.
EOF

cat << 'EOF' > docs/design/ECLSS_SYSTEM_ARCHITECTURE.md

ECLSS_SYSTEM_ARCHITECTURE.md
Overview
This document details the functional architecture of the six interconnected life support subsystems in Galaxy Game. It outlines resource transformation loops, hardware module requirements, and the event-driven cascading failure model that propagates localized mechanical breakdowns across entire station ecosystems. Reference this document when implementing backend ECLSS processing pipelines, hardware failure events, or life support module chains.

Source Basis
This model is built upon closed-loop life support principles from NASA space station documentation and system interdependence theory. It replaces abstract "life support percentages" with concrete inputs, outputs, and hardware dependencies.

The 6 Core Life Support Loops
                  ┌─────────────────────────────────────────┐
                  │          POWER & THERMAL LOOP           │
                  │   (Reactor / Solar ──> Radiator Grid)   │
                  └────────────────────┬────────────────────┘
                                       │ Power & Heat Management
     ┌─────────────────────────────────┼─────────────────────────────────┐
     ▼                                 ▼                                 ▼
┌──────────────┐              ┌─────────────────┐              ┌──────────────────┐
│ WATER LOOP   │              │ ATMOSPHERE LOOP │              │  FOOD/AGRI LOOP  │
│ Gray/Black   │ ──Water───>  │ Electrolysis &  │ ──Oxygen───> │ Hydroponics &    │
│ Distillation │ <──Moisture─ │ Sabatier React. │ <──CO2────── │ Biomass Recycler │
└──────────────┘              └─────────────────┘              └──────────────────┘
     │                                 │                                 │
     └─────────────────────────────────┼─────────────────────────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │            GRAVITY & HEALTH             │
                  │ (Rotation Hull ──> Crew Maintenance/SANS│
                  └─────────────────────────────────────────┘
1. Water Loop (Hydration & Electrolysis)
Commodities: GrayWater, BlackWater (Brine/Urine), PurifiedWater.

Hardware: WaterDistillationUnit, BrineProcessor, IonExchangeFilter.

Mechanic: Processes human waste and condensation back into drinkable water. Suffers an unrecoverable loss per cycle, requiring raw water ice resupply.

2. Atmospheric Loop (Gases & Pressure)
Commodities: Oxygen (O2), Nitrogen (N2), CarbonDioxide (CO2), Methane (CH4).

Hardware: ElectrolysisArray, SabatierReactor, MolecularSieveScrubber, HullSealant.

Mechanic: Water electrolysis generates O2 while Sabatier reactors synthesize methane byproduct from exhaled CO2 and hydrogen. Micro-leaks bleed nitrogen and oxygen, requiring active pressure monitoring.

3. Thermal Loop (Heat Management)
Commodities: ThermalCoolant, HeatUnits (kW).

Hardware: HeatExchanger, CoolantPump, RadiatorPanel.

Mechanic: Electronics, lighting, and human metabolic activity generate excess heat. Undersized or damaged radiator arrays cause internal temperatures to rise, accelerating food spoilage and increasing crew stress.

4. Food & Biomass Loop (Agriculture)
Commodities: NutrientSlurry, CropBiomass, Rations.

Hardware: HydroponicTray, VerticalFarmArray, ComposterUnit.

Mechanic: Hydroponic units consume purified water, fertilizer, and CO2 to generate rations and supplementary O2 production.

5. Gravity & Health Loop
Commodities: CalciumSupplements, MedicalSupplies.

Hardware: CentrifugalHabitatRing, RCSThrusterArray (for spin upkeep), MedicalBay.

Mechanic: Zero gravity triggers progressive health penalties (1%/month bone loss, SANS vision damage). Hull rotation eliminates decay but requires RCS propellant or mechanical bearing maintenance.

6. Structural Integrity & Micro-Leak Loop
Commodities: HullSealant, SparePanels.

Hardware: UltrasonicSensorArray, MaintenanceDroneBay.

Mechanic: Continuous tracking and patching of millimeter-wide cracks caused by micrometeorites and thermal expansion cycling.

Cascading Failure Logic Model
Unlike open terrestrial ecosystems, closed space habitats link all subsystems directly. A failure in one module cascades through the network if unaddressed:

[ Clogged Water Filter ]
          │
          ▼
[ Reduced Crop Irrigation ]
          │
          ▼
[ Lower Oxygen Output from Plants ]
          │
          ▼
[ Increased Load on Mechanical Electrolysis ]
          │
          ▼
[ Higher Power Consumption ]
          │
          ▼
[ Increased Waste Heat Production ]
          │
          ▼
[ Thermal Radiator Stress & Internal Warming ]
          │
          ▼
[ Accelerated Microbial Oxygen Consumption in Soil ]
          │
          ▼
[ Further Oxygen Level Drop ]
Game Parameters / Constants
Constant Name	Value	Source	Engine Notes
SABATIER_METHANE_YIELD_RATIO	0.25	Stoichiometric Data	CH4 generated per unit CO2 processed
ELECTROLYSIS_POWER_KW_PER_KG	4.5	ISS Performance Specs	Power required to extract 1 kg O2 from water
HYDROPONIC_O2_YIELD_KG_DAY	0.8	Plant Physiology Data	O2 produced per active farm tray daily
MAX_CABIN_TEMP_CELSIUS	38.0	Crew Safety Standard	Threshold where heat-induced crop failure begins
Rails Implementation Notes
The system uses an event-driven tick architecture where each module processes inputs from node storage and writes outputs back to the buffer.

Models: EclssModule, StorageBuffer, SystemFailureEvent.

JSON Data Schema (Full Node ECLSS State)
JSON
{
  "node_id": "station_alpha_habitat_01",
  "hull_integrity": 0.94,
  "micro_leak_rate_per_tick": 0.002,
  "eclss_status": {
    "water_recovery_efficiency": 0.98,
    "co2_scrubber_operational": true,
    "radiator_capacity_kw": 450,
    "current_heat_load_kw": 410
  },
  "storage_buffers": {
    "water": 1250.5,
    "oxygen": 840.0,
    "nitrogen": 3200.0,
    "co2": 45.2,
    "waste_sludge": 88.1
  }
}
Open Design Questions
Graph vs. Procedural Ticks: Should failure cascades be calculated using a directed acyclic graph (DAG) network or through sequential procedural buffer checks?

Fire and Contamination Spreading: How should localized toxic gas spikes (CO or ammonia) spread through adjacent module ducting?

Related Files
ECLSS_PARAMETERS.md — Quantitative baseline numbers for all formulas.

HABITATION_NODE_ARCHITECTURE.md — Physical frame housing these ECLSS modules.

LUNA_SETTLEMENT_LIFECYCLE.md — ECLSS deployment across Luna phases.
EOF

cat << 'EOF' > docs/design/LUNA_SETTLEMENT_LIFECYCLE.md

LUNA_SETTLEMENT_LIFECYCLE.md
Overview
This document outlines the three-phase industrial lifecycle of a lunar settlement in Galaxy Game. It details the evolution from an Earth-dependent outpost to a self-sufficient In-Situ Resource Utilization (ISRU) hub and ultimate primary mass exporter for orbital Lagrange depots (EML-1). Reference this document when designing surface tech trees, lunar module catalogs, mass driver logistics, and early-game progression mechanics.

Source Basis
This model is based on NASA Artemis/Lunar architecture plans and ISRU mineral extraction physics (ilmenite reduction, volatile polar cold-trap harvesting). It leverages Luna's shallow gravity well (0.166g) to position surface bases as the primary bulk mass source for deep space human activities.

The 3-Phase Bootstrapping Loop
┌─────────────────────────────────────────────────────────────────────────┐
│                      PHASE 1: IMPORT & BOOTSTRAP                        │
│  • Earth Imports: Water, Nitrogen, Heavy Hardware, Pre-built Modules    │
│  • Primary Activity: Excavation, Regolith Shielding, Base Setup         │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       PHASE 2: SURFACE ISRU LOOP                        │
│  • Regolith Processing ──> Oxygen, Fe/Al/Si Metals, Anorthite           │
│  • Polar Ice Mining ──> Water, Volatiles Extraction                     │
│  • Base Output: Self-Sustaining Habitat Mass, Solar Panels, Fuel        │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      PHASE 3: L1 DEPOT & EXPORT                         │
│  • Mass Driver / Space Elevator ──> Launch Excess Mass to Earth/Moon L1   │
│  • L1 Depot Assembly: Station Components, Orbital Habitats, Fuel Depots │
└─────────────────────────────────────────────────────────────────────────┘
Phase 1: Import & Bootstrap
Focus: Initial survival and infrastructure setup.

Dependencies: 100% reliant on Earth-shipped payloads for water, nitrogen, pre-built inflatable habitats, and life support consumables.

Primary Tasks: Digging subterranean trenches, positioning inflatable modules inside lava tubes, and covering surface frames with raw regolith for radiation and thermal protection.

Phase 2: Surface ISRU Loop
Focus: Local resource independence.

Dependencies: Taps into local lunar geology.

Primary Tasks:

Regolith Oxygen Extraction: Baking ilmenite soil to extract abundant surface O2.

Polar Ice Mining: Harvesting crater cold traps for water ice, trace ammonia, and methane.

Sintering Foundries: Melting regolith into structural blocks and metal I-beams (Fe/Al).

Phase 3: L1 Depot & Export
Focus: Off-world commerce and deep space expansion.

Dependencies: Full self-sufficiency in oxygen, water, and building materials.

Primary Tasks: Operating electromagnetic Mass Drivers to shoot bulk water, fuel, and metal beams to Earth-Moon Lagrange Point 1 (EML-1), supplying orbital station construction.

Core Module Catalog & Specific Mechanics
1. Sub-Surface & Shielding Modules
Regolith Excavator & Sintering Rig: Collects raw soil and uses laser/microwave heating to create structural bricks.

Buried Inflatable Habitat Shell: Inflatable cores placed under 3–5 meters of sintered regolith to mitigate cosmic radiation and thermal swings (-130°C to +120°C).

2. Operational Hazards & Bottlenecks
The Nitrogen Bottleneck: Nitrogen (N2) is extremely scarce in lunar regolith. Every atmosphere leak represents a loss of an imported commodity that must be replaced via expensive shipments from Earth or Venus skimmers.

Regolith Dust Contamination: Lunar regolith consists of sharp, electrostatically charged glass fragments. Surface airlocks suffer accelerated FilterWear and seal damage, requiring regular replacement of HEPA filters and lock seals.

Game Parameters / Constants
Constant Name	Value	Source	Engine Notes
MIN_REGOLITH_DEPTH_METERS	3.0	NASA Radiation Limits	Depth required to eliminate thermal cycling penalty
ILMENITE_O2_YIELD_PERCENT	0.10	USGS Lunar Geology Data	Mass ratio of extractable O2 per kg processed regolith
DUST_ABRASION_DECAY_RATE	0.02	Apollo EVA Telemetry	Accelerated seal decay per surface airlock cycle
MASS_DRIVER_LAUNCH_COST_GCC	5.0	Orbital Delta-V Calculation	Cost per ton to fling mass from Luna to EML-1
Rails Implementation Notes & Schema
Luna tiles maintain state for burial depth and abrasive dust accumulation.

Models: SurfaceSettlement, LunarTile, IsruPlant, MassDriver.

JSON Schema (Luna Surface Habitat Unit)
JSON
{
  "unit_id": "luna_shackleton_base_hab_02",
  "unit_type": "surface_buried_habitat",
  "location": {
    "body": "Luna",
    "site": "Shackleton_Crater_Rim",
    "depth_meters_under_regolith": 4.5
  },
  "structural_status": {
    "hull_integrity": 0.98,
    "regolith_shielding_effective": true,
    "micro_leak_rate_per_tick": 0.0005,
    "dust_contamination_level": 0.12
  },
  "eclss_loop": {
    "gravity_mode": "surface_centrifuge_assisted",
    "effective_g_force": 1.0,
    "water_recycling_efficiency": 0.97,
    "nitrogen_loss_rate_per_day": 0.15,
    "thermal_rejection_sink": "subsurface_conduction"
  },
  "local_buffer": {
    "oxygen_kg": 4500.0,
    "nitrogen_kg": 1200.0,
    "water_kg": 8900.0,
    "regolith_dust_filters_count": 14
  }
}
Open Design Questions
Lava Tube Expansion Limits: Should structural expansion inside natural lava tubes offer infinite volume at reduced construction costs, bounded only by map geometry?

Volatile Trapping Ratios: How variable should ice purity levels be across different polar crater coordinates on the lunar map grid?

Related Files
ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md — The macro economic impact of the EML-1 mass driver export pipeline.

HABITATION_NODE_ARCHITECTURE.md — Shared frame and panel slot specs.

ECLSS_PARAMETERS.md — Baseline consumption constants.
EOF

cat << 'EOF' > docs/design/ECLSS_SOURCE_REFERENCE.md

ECLSS_SOURCE_REFERENCE.md
Overview
This document serves as the primary foundational reference for the ECLSS mechanics and resource dynamics in Galaxy Game. It documents real-world spaceflight baselines—specifically from the International Space Station (ISS) and historical NASA programs—and maps planetary services to their mechanical hardware equivalents. Reference this document to understand the engineering rationale behind game design parameters, failure modes, and industrial balancing decisions.

Source Basis
This document aggregates operational data, historical failure logs, and environmental control research from NASA, Apollo, Gemini, and ISS technical reports.

Historical Mission Paradigms
  SHORT-DURATION MISSIONS               LONG-DURATION MISSIONS
  ┌───────────────────────────────┐     ┌───────────────────────────────┐
  │  • Duration: Days to Weeks    │     │  • Duration: Years to         │
  │  • Goal: Prevent Death        │ VS  │    Generations                │
  │  • Strategy: Store Supplies,  │     │  • Goal: Sustain Life & Society│
  │    Endure Discomfort          │     │  • Strategy: Total Systemic   │
  │  • Safety Net: Earth is Close │     │    Recycling & Self-Reliance  │
  └───────────────────────────────┘     └───────────────────────────────┘
Mercury/Gemini/Apollo (Short-Duration): Vehicles acted as simple survival capsules. They stored all consumable supplies upfront and relied on immediate abort options if systems failed.

ISS (Hospital Model): Keeps crew alive through intensive ground supervision, continuous cargo resupply flights, and active repair shifts.

Deep Space Habitats (Civilization Model): Closed-loop ecosystems that must generate their own resources, repair internal components, and operate independently without Earth resupply or abort safety nets.

Planetary Services vs. Mechanical Equivalents
Earth performs continuous life support services automatically through natural planetary buffers. Space habitats must replace every natural service with high-maintenance mechanical hardware:

Natural Planetary Service	Earth's Mechanism	Habitat Mechanical Equivalent
Water Purification	Solar evaporation, precipitation, rock percolation	Distillation assemblies, ion-exchange beds, catalytic reactors
Atmospheric Recycling	Global photosynthesis, oceanic algae	Water electrolysis arrays, Sabatier reactors, molecular sieves
Radiation Shielding	Planetary magnetosphere, dense atmosphere	Compacted regolith layers, thick water walls, heavy metal shielding
Thermal Dissipation	Atmospheric convection, ocean currents, planetary mass	Coolant pumps, heat pipes, external radiator panel arrays
Gravity Realism	Planetary mass (9.81 m/s^2)	Centripetal acceleration via continuous hull rotation
Waste Processing	Biological decay, soil microbiomes, tectonic cycles	Bioreactors, composter units, chemical oxidation beds
Real-World Operational Failure Cases
Historical ISS hardware issues serve as direct inspiration for random station events and maintenance sinks in the game engine:

Micro-Leaks under Thermal Cycling: Millimeter-wide joints leak small volumes of atmosphere continuously. Searching for microscopic cracks across expansive surface areas while metals expand and contract under solar deltas is an ongoing operational challenge.

Microgravity Toilet & Distillation Clogging: Solid precipitation in high-acid urine blocks distillation assemblies, requiring manual cleaning and filter replacement.

Scrubber Subsystem Outages: Mechanical failures in molecular sieve beds cause rapid CO2 buildup, inducing severe headaches, fatigue, and cognitive decline in crew members.

Cascading Interdependence Schema
[ Clogged Water Filter ]
          │
          ▼
[ Reduced Crop Irrigation ]
          │
          ▼
[ Lower Oxygen Output from Plants ]
          │
          ▼
[ Increased Load on Mechanical Electrolysis ]
          │
          ▼
[ Higher Power Consumption ]
          │
          ▼
[ Increased Waste Heat Production ]
          │
          ▼
[ Thermal Radiator Stress & Internal Warming ]
          │
          ▼
[ Accelerated Microbial Oxygen Consumption in Soil ]
          │
          ▼
[ Further Oxygen Level Drop ]
Related Files
ECLSS_PARAMETERS.md — The concrete game constants derived from these sources.

ECLSS_SYSTEM_ARCHITECTURE.md — Hardware implementation of these planetary service replacements.

ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md — How mechanical inefficiencies create macro-economic loops.
EOF


---

### Option 2: Copy-Paste Individual Files
If you prefer creating them manually in your IDE or editor, here is where each file belongs in your repository:

* `docs/design/ECLSS_PARAMETERS.md`
* `docs/design/ECONOMIC_ENGINE_SURFACE_VS_ORBITAL.md`
* `docs/design/HABITATION_NODE_ARCHITECTURE.md`
* `docs/design/ECLSS_SYSTEM_ARCHITECTURE.md`
* `docs/design/LUNA_SETTLEMENT_LIFECYCLE.md`
* `docs/design/ECLSS_SOURCE_REFERENCE.md`