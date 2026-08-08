That distinction creates the primary economic engine and industrial hierarchy for Galaxy Game.Because surface settlements sit on top of massive planetary bodies, they are resource generators with infinite material depth. Space stations, orbital depots, and cyclers, on the other hand, are suspended in a vacuum—they produce no raw matter on their own and operate purely as resource consumers and processors.Here is how this fundamental divide structures the game's mechanics, trade flows, and module design across surface settlements, orbital stations, and cyclers.1. The Asymmetric Resource Architecture                  SURFACE SETTLEMENT (Luna / Mars / Ceres)
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
Surface Settlements: Primary Extraction EngineAdvantage: Gravity wells provide infinite raw mass. Settlers tap directly into local deposits to extract Regolith, Water Ice, Iron/Aluminum Ore, and Carbon/Silicates.Primary Role: Mass exporter. They build heavy industrial infrastructure (sintering plants, mass drivers, smelters) to turn dirt into raw commodities.ECLSS Edge: They use local mass for free passive shielding (burying habitats under meters of soil) and can use ground conduction or deep crust sinks for thermal management.Stations & Cyclers: Secondary Processing & Consumption EngineVulnerability: Every single gram of matter on a station or cycler was either shipped from a surface base or mined from a nearby asteroid/skimmer run.Primary Role: Transit, refining, trade hubs, and assembly yards.ECLSS Constraint: They cannot shovel soil over their heads. They rely entirely on imported Water Walls, Heavy Polymer Panels, or metal plating for radiation shielding. Every atmospheric leak or water loss on a station requires an expensive purchase on the market to replace.2. Resource Logistics MatrixTo balance industrial loops, commodities flow dynamically between surface bases and orbiting craft based on where they originate and where they are consumed:Resource / CommoditySurface SourceStation / Cycler SourcePrimary Consumption PointOxygen ($\text{O}_2$)Regolith reduction, Polar iceSurface imports, Plant loopUniversal (ECLSS, Rocket Propellant)Water ($\text{H}_2\text{O}$)Polar crater mining, Subsurface glaciersSurface imports, Skimmer deliveriesUniversal (ECLSS, Hydroponics, Shielding)Nitrogen ($\text{N}_2$)Rare on Luna/Mars; heavy Venus/Titan extractionShipped via Skimmer / FreighterHabitat atmosphere replenishmentStructural Mass (I-Beams, Panels)Local foundries (Anorthite/Iron)Imported or Orbital 3D-printing yardsHabitat expansion, Hull maintenanceVolatiles / Fuel ($\text{CH}_4$)Processed surface ice + $\text{CO}_2$Venus atmospheric skimmersShips, Cyclers, Station RCS thrusters3. The "Build vs. Buy" Market Dynamics for Players & TradersBecause stations and cyclers cannot survive on their own, they create an inelastic market demand for mined or shipped supplies.Station Maintenance Sinks:An orbital depot or cycler running a 98% water recycling loop constantly loses 2% of its fluid per cycle.A cycler suffering thermal cycling leaks nitrogen through hull seals.The station must issue automatic buy orders on the GCC market for PurifiedWater, Nitrogen, and SparePanels.The Trader / Traitor Advantage:If a player (or AI trader) notices a cycler approaching Mars with depleted $\text{N}_2$ buffers, they can jump the market price.If Venusian nitrogen harvested by skimmers is cheaper to transport to Mars cyclers than harvesting it elsewhere, market forces will naturally redirect supply routes there—regardless of political faction lines.Mass Driver Economics:Surface bases on low-G bodies (Luna, Ceres) use electromagnetic Mass Drivers to fling bulk raw materials ($\text{O}_2$, Water Ice, Metal Ingots) to L1 Depots at near-zero fuel cost.This makes surface-extracted resources drastically cheaper at EML-1 than shipping those same supplies up from Earth's deep gravity well.4. Rails Model Implementation: Unified Storage and Source TrackingIn the Ruby on Rails backend, both SurfaceSettlement and SpaceCraft/Station can inherit from an abstract HabitationNode or share a ResourceBuffer association. The main differentiator is their resource extraction capability:Rubyclass HabitationNode < ApplicationRecord
  has_many :installed_modules
  has_one  :resource_buffer
  has_many :market_orders

  # Surface bases have extraction rates from local deposits; 
  # Ships/Stations have an extraction rate of zero unless docked with a resource node.
  def surface_node?
    node_type == 'surface_settlement'
  end

  def process_eclss_tick!
    # Common ECLSS loss logic applies to BOTH
    resource_buffer.consume_metabolic_supplies!
    resource_buffer.apply_micro_leaks!(hull_wear)

    if surface_node?
      # Extract local surface resources (Regolith, Surface Ice, O2)
      extract_local_deposits!
    else
      # Check if shipped supplies or docked skimmers are refilling buffers
      process_docked_transfers!
      check_critical_eclss_deficits! # Triggers auto-buy orders on GCC market if low
    end
  end
end
JSON Buffer Payload Example for an Orbital Depot vs. Surface BaseJSON{
  "node_id": "eml1_depot_alpha",
  "node_type": "orbital_depot",
  "local_extraction_capable": false,
  "eclss_status": {
    "water_reserve_days": 14,
    "nitrogen_reserve_days": 8,
    "structural_panels_in_stock": 12
  },
  "market_dependencies": [
    {"commodity": "nitrogen", "status": "critical_buy_order_active", "target_source": "venus_skimmer_imports"},
    {"commodity": "purified_water", "status": "stable", "target_source": "luna_mass_driver_exports"}
  ]
}
5. Summary of the Game Loop StrategySurface Settlements are the industrial roots. Players build them to tap into local deposits, refine raw dirt into metals and life support gases, and build launch systems.Orbital Depots & Cyclers are the transit and market hubs. They rely completely on the supply chains feeding them from surface mass drivers or atmospheric skimmers.The Interdependence: A player who owns an orbital station must secure trade agreements or supply runs from surface settlements—or risk their station running dry on nitrogen, leaking atmosphere, and suffering catastrophic life support failure.