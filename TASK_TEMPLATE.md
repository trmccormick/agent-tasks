# Port & Connection System Specification

## 1. Intent & Architectural Philosophy
Galaxy Game utilizes a **Bus-Topology** for all resource and data management. Connections are not merely "plumbing"; they represent logical network nodes that allow daisy-chaining of power, fuel, and data across crafts, rigs, modules, and surface bases.

* **Formula-First:** All resource validation is governed by chemical formulas (e.g., `CH4`, `LOX`, `H2O`) to ensure systemic consistency.
* **Bus-Topology:** Resources are shared across a `bus_id`. When units dock, their `bus_ids` bridge, creating a unified resource pool.
* **Structural Separation:** The system strictly separates physical mounting (`mounting_slots`) from logical throughput (`utility_ports`).

## 2. Interface Contract (v1.9 Standard)
All blueprints and operational data must implement the `connection_schema` block.

### A. Mounting Slots (Structural)
Defines physical compatibility.
* `id`: Unique slot identifier.
* `type`: e.g., `universal_dock`, `fragile_mount`, `integrated_slot`.
* `location`: `internal` or `external`.
* `max_mass_kg`: Physical load limit.

### B. Utility Ports (Functional)
Defines resource/data flow.
* `id`: Port identifier.
* `type`: `power`, `fluid_in`, `fluid_out`, `data`, `control`.
* `bus_id`: The network/grid this port connects to (e.g., `DEFAULT_PARENT`).
* `allowed_formulas`: Array of chemical formulas (or empty for non-fluid).
* `throughput_limit`: Numerical limit per tick.
* `mounted_to_slot`: Reference to a defined `mounting_slot`.

## 3. Daisy-Chaining Logic
* **Parent/Child Relationship:** Rigs and Modules register to a `bus_id` provided by the host (the "Parent" Craft or Hub).
* **Bus Bridging:** Upon docking, the simulation merges the `bus_id` of the Craft with the `bus_id` of the Surface Base. 
* **Dynamic Inventory:** The engine calculates total resources by summing the inventory of all units currently registered to a specific `bus_id`.

## 4. Migration & Versioning Policy
* **v1.9 Compliance:** All new assets must use `v1.9` templates.
* **Legacy Support:** Files marked `v1.2` or lower are considered "Legacy." They must be migrated to `v1.9` before they can interact with the Bus-Topology system.
* **Zero-Drift Enforcement:** No `ports` or `connections` blocks are permitted outside of the `connection_schema` object.