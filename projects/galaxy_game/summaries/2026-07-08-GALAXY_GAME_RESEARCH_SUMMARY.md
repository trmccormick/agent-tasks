# Galaxy Game Industrial Research Summary

## 1. Overview
This document outlines the industrial architecture for the Galaxy Game, specifically focusing on the Heavy Lift Transport (HLT) platform, the transition to V2.1 Bus-Topology, and the logistics staging model involving Skimmers and Cyclers.

## 2. HLT Craft Architecture (MK1 Platform)
The HLT MK1 serves as a universal industrial platform.
* **Chassis Design**: Modular chassis with standardized dimensions (50m x 9m x 9m) and payload capacity of 150,000 kg [cite: heavy_lift_transport_mk1_bp.json].
* **Modular Strategy**: Uses a "Chassis + Modules" approach to support mission-specific hardware for Venus, Mars, and Titan [cite: heavy_lift_transport_venus_data.json, heavy_lift_transport_mars_data.json, heavy_lift_transport_titan_data.json].

## 3. V2.1 Bus-Topology Integration
To modernize the port system, we have transitioned from legacy port counting to a dynamic Bus-Topology.
* **Bridge Strategy**: The `LegacyPortAdapter` allows V2.1-compliant modules (using `register_to_bus`) to interface with legacy Rake-based mission suites [cite: PORT_CONNECTION_SYSTEM.md].
* **Connection Schema**: Blueprints now utilize `mounting_slots` and `bus_id` parameters to define functional connectivity [cite: PORT_CONNECTION_SYSTEM.md].

## 4. Environment-Specific Industrial Loops
Each HLT variant utilizes specialized hardware to handle atmospheric conditions:
* **Venus Variant**: Features sacrificial $SO_2$ scrubbers, MOXIE/SOXE processing stacks for $O_2$ extraction, and refined gas-handling to monetize $CO$ [cite: heavy_lift_transport_venus_data.json].
* **Mars Variant**: Optimized for thin-atmosphere extraction, focusing on $CO_2$ splitting, and utilizing Phobos/Deimos as regional refining and storage depots [cite: heavy_lift_transport_mars_data.json, 2026-01-08].
* **Titan Variant**: Focused on cryogenic condensation for $CH_4$ fuel and $N_2$ harvesting [cite: heavy_lift_transport_titan_data.json].

## 5. Logistics Staging & Economic Strategy
* **Bootstrap Stage (P2P)**: Skimmers perform direct Point-to-Point delivery (Harvest-to-Luna/Depot). This phase is used to build the L1 shipyards and depots.
* **Infrastructure Stage (Cycler-Backbone)**: Once L1 shipyards are operational, Cyclers are deployed for continuous interplanetary loops. Skimmers transition to "last-mile" shuttles, docking with cyclers in orbit to offload cargo via V2.1 docking protocols [cite: 2026-03-02].
* **Market vs. Build**: The AI Manager utilizes a decision matrix that prioritizes local harvesting over interplanetary imports, only triggering the latter when local buffers (especially $N_2$) drop below critical thresholds [cite: 2026-02-24, 2026-03-02].
* **Easter Eggs**: Intent to include nods to classic sci-fi media [cite: 2026-01-18].
