# Galaxy Game: Technical Integration & Design Requirements

This document serves as the bridge between current research and implementation, consolidating the core constraints and logic discussed for the Galaxy Game.

## 1. Modular Panel Architecture & Component System
The panel system is defined by a modular I-beam support framework. All generated assets must conform to this physical constraint.

### Component Logic & Ports
* **Support Framework:** All panels utilize I-beam edges.
* **Connectivity:** Panels must support defined `connection_types` for modular assembly:
    * `i-beam_socket`: For structural connection.
    * `power_bus_link`: For integrated energy panels.
    * `data_bus_node`: For sensors/controls.
* **Variant System:** Panels are categorized by functional intent (e.g., Transparent/Cheap, Rugged/Industrial, Solar/Energy-Integrating).
* **Metadata Requirement:** Every generated asset must include a schema defining its `connection_types` and `material_class` (e.g., `regolith_sintered`, `trans_plastic`, `photovoltaic`).

## 2. Production Pipeline: Market vs. Build Logic
Asset generation is linked to the industrial economy. Assets must be evaluated against the "Market vs. Build" logic.

### Metadata Schema for Assets
Each asset blueprint must contain:
* `fabrication_cost_index`: Raw material cost to print on-site.
* `utility_weight`: Performance/Functionality score.
* `import_feasibility`: Boolean or rating for whether it is cheaper to source via Earth/Corporation import or local fabrication.

## 3. Canonical Normalization & Asset Pipeline
To resolve the Alias Resolution gap, we are moving to a strict canonical system.

### Canonical Mapping Service
Replace all substring-based fallbacks with a `CanonicalMap`.
* **Definition:** A centralized `CANONICAL_TILE_MAP` (JSON/Ruby) that maps all incoming tile aliases to one of the 16 canonical types.
* **Implementation:** The `BiomeRenderer` and `BiomeValidator` must query this map *before* rendering or placement validation.

## 4. Documentation Mandate
As established, all development cycles must adhere to the following rule:
* **Documentation Update:** Any modification to `surface_view.js`, `BiomeRenderer.js`, or associated data models *must* be accompanied by an update to the corresponding `docs/architecture/` files.
* **Scope:** This applies to all four core commands in the production pipeline.

## 5. Strategic Context for Implementation
* **Super-Mars/Luna Pattern:** Industrial loops follow the settlement model of establishing a depot (Earth L1/Luna pattern) before surface colonization.
* **Market Coupling:** Initial phase assumes `GCC = USD`. Future code must allow for decoupling when market volume reaches threshold.
* **Hard Sci-Fi Constraint:** Visuals and branding must remain grounded in functional, industrial design (e.g., raw regolith-sintered textures, visible modular ports, corporate branding consistent with a USD-based Earth supply chain).
