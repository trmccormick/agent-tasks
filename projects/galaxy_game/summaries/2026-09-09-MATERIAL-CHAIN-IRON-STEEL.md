# Iron & Steel Production Chain — Research Note

**Date**: 2026-09-09  
**Author**: Planning Agent (research-only, no code changes)  
**Purpose**: Define a plausible real-world iron/steel production chain as a template for modeling material input chains in the game data model.

---

## Assumptions About Our Existing Schema

Before proceeding, here are the minimal assumptions I'm making about our current material schema:

1. **Two schema families coexist** — Some materials (like `iron.json`) use an older flat schema with top-level `sources`, `uses`, `applications` fields and no `production.input_materials`. Others follow template v1.6 which has a `production` block with `input_materials` and `byproducts` arrays.
2. **Template v1.6 structure** — The v1.6 template defines:
   ```json
   "production": {
     "facility_type": "",
     "energy_kwh_per_kg": 0.0,
     "input_materials": [
       { "id": "", "amount": 0.0 }
     ],
     "byproducts": [
       { "id": "", "amount": 0.0 }
     ]
   }
   ```
3. **`production.input_materials` is universally empty** — Only ~10 of ~207 materials have populated input chains.
4. **Byproduct materials exist** — `slag.json`, `metal_byproduct.json`, `manufacturing_dust.json`, `waste_gas.json` etc. exist in `byproducts/`.
5. **Ore materials exist** — `hematite.json`, `magnetite.json`, `iron_ore.json` (in `raw/geological/ore/`) exist as raw ore definitions.
6. **No concentrate or intermediate metal files exist** — There is no `concentrate.json`, `pig_iron.json`, `crude_steel.json`, or similar in the materials directory.

---

## 1. Real-World Iron & Steel Production Chain

### Stage 1: Ore Extraction & Beneficiation

**Input**: Raw iron ore (hematite Fe₂O₃, magnetite Fe₃O₄, or mixed iron_ore)  
**Process**: Mining → crushing → grinding → magnetic/flotation separation  
**Output**: Iron concentrate (60–70% Fe content)  
**Byproducts**: Tailings (waste rock), off-gas dust

**Key inputs per tonne of concentrate:**
- ~2–3 tonnes raw ore (depends on ore grade)
- Electricity for crushing/grinding (~30–50 kWh/t)
- Water for flotation (~5–10 m³/t)
- Reagents (collectors, depressants — small mass)

**Why this matters for the game**: The ore → concentrate stage represents a **mass reduction** (2–3:1 input:output ratio). This is where the "upfront capital cost" of building a beneficiation plant on Luna/Mars becomes economically relevant — you can't just smelt raw regolith directly; you need to concentrate it first.

### Stage 2: Reduction/Smelting

**Input**: Iron concentrate (or direct-use ore)  
**Process**: Blast furnace (BF) or direct reduction (DRI)  
**Output**: Pig iron (~93–95% Fe, high carbon 3.5–4.5%) or DRI/sponge iron (~90–94% Fe)

#### Route A: Blast Furnace (traditional, Earth-standard)
- **Inputs per tonne of pig iron:**
  - ~1.5 tonnes iron concentrate
  - ~0.4–0.6 tonnes coke (reductant, from coking coal)
  - ~0.3–0.5 tonnes limestone (flux)
  - ~3–5 GJ thermal energy (from coke combustion)
- **Byproducts:**
  - ~1.2 tonnes slag (per tonne pig iron)
  - BF off-gas (CO/CO₂-rich, can be recaptured as fuel)

#### Route B: Direct Reduction (gas-based, increasingly common)
- **Inputs per tonne of DRI:**
  - ~1.4–1.6 tonnes concentrate/ore pellets
  - ~8–12 GJ natural gas or hydrogen (reductant gas)
- **Byproducts:**
  - Reduced slag (~0.5–0.8 t/t)
  - DR off-gas (recyclable)

#### Route C: Future ISRU — Hydrogen Direct Reduction (Luna/Mars-relevant)
- **Inputs per tonne of DRI:**
  - ~1.4–1.6 tonnes concentrate/ore pellets
  - ~15–20 MWh electricity (for H₂ production via electrolysis if no natural gas)
- **Byproducts:**
  - Water vapor (can be condensed and recycled)
  - Minimal slag

**Why this matters**: On Luna/Mars, Route C is the only viable path initially. The game should model hydrogen DRI as a distinct route with different inputs (electricity-heavy vs coke-heavy) than Earth's BF route. This creates meaningful economic differentiation between Earth-imported steel and locally-produced steel.

### Stage 3: Steelmaking

**Input**: Pig iron (from BF) or DRI (from DR)  
**Process**: Basic Oxygen Furnace (BOF) or Electric Arc Furnace (EAF)  
**Output**: Crude steel (~98–99% Fe, carbon 0.02–2%)

#### Route A: BOF (pig iron route)
- **Inputs per tonne of crude steel:**
  - ~0.75 tonnes molten pig iron
  - ~0.15–0.20 tonnes scrap steel (recycled)
  - ~0.05–0.08 tonnes lime (flux)
  - ~0.02–0.03 tonnes oxygen
- **Byproducts:**
  - BOF slag (~0.3 t/t)

#### Route B: EAF (scrap-based, dominant in US)
- **Inputs per tonne of crude steel:**
  - ~0.7–1.0 tonnes scrap steel
  - ~0.3–0.5 tonnes DRI or hot briquetted iron (HBI) as diluent
  - ~400–450 kWh electricity
  - ~0.02–0.04 tonnes lime
- **Byproducts:**
  - EAF dust (~0.03 t/t, hazardous — contains zinc)

**Why this matters**: On a frontier world with no scrap infrastructure, EAF is initially infeasible (no scrap to melt). BOF requires pig iron, which requires smelting. This creates a **production chain dependency** — you can't make steel without first establishing smelting capacity. The game should reflect this: early-game steel production is impossible until the full ore → concentrate → pig iron chain exists.

### Stage 4: Secondary Metallurgy & Casting

**Input**: Crude steel  
**Process**: Ladle furnace refining → continuous casting → rolling  
**Output**: Refined steel grades (structural, plate, rebar, sheets) + semi-finished products (billets, blooms, slabs)

- **Inputs per tonne of refined steel:**
  - Alloying elements (manganese, chromium, nickel, molybdenum — small mass, high value)
  - Electricity (~50–100 kWh/t for refining)
  - Refractory materials (consumable lining)
- **Byproducts:**
  - Slag (from refining)
  - Scale/scale oxide (from rolling)

**Why this matters**: This stage is where alloying happens. The game can abstract casting/rolling into a single "steel mill" step, but the **alloying inputs** are economically significant — adding chromium makes stainless steel (high value), adding nickel improves toughness, etc.

---

## 2. Proposed In-Game Material Set for Iron/Steel Chain

### Recommended Materials (8 total)

| # | Material ID | Category/Subcategory | Rationale |
|---|------------|---------------------|-----------|
| 1 | `iron_ore` | raw_materials / geological / ore | Raw extracted material. Already exists as hematite/magnetite files, but needs a generic `iron_ore` aggregate for game simplicity. |
| 2 | `iron_concentrate` | raw_materials / processed / concentrate | **NEW** — intermediate between ore and smelting. Represents beneficiation output. Mass ratio ~3:1 ore:concentrate. |
| 3 | `coke` | raw_materials / processed / reductant | **NEW** — coking coal product used as reductant in BF route. Can be imported from Earth or produced via ISRU coal carbonization (if coal exists). |
| 4 | `limestone` | raw_materials / geological | Already exists at `raw/geological/limestone.json`. Use as-is for flux. |
| 5 | `pig_iron` | intermediate / metals | **NEW** — smelting output, pre-steelmaking. High carbon content distinguishes from iron. |
| 6 | `crude_steel` | intermediate / metals | **NEW** — steelmaking output before alloying/refining. The "bulk" steel that gets rolled into forms. |
| 7 | `steel` | processed / metals | Already exists at `processed/metals/iron.json` (id: "iron"). **Rename to `steel`** or keep `iron` as the pure element and use `steel` for the alloy. Recommend keeping both: `iron` = pure Fe element, `steel` = Fe+C alloy. |
| 8 | `slag` | byproducts / general | Already exists at `byproducts/slag.json`. Use as-is; iron smelting produces ~1.2 t slag per tonne pig iron. |

### Optional Materials (for depth)

| # | Material ID | When to Add |
|---|------------|-------------|
| 9 | `hydrogen_gas` | If modeling hydrogen DRI route — needs its own gas file or reuse existing H₂ file |
| 10 | `scrap_steel` | For EAF route and recycling loop — represents post-consumer/industrial scrap |
| 11 | `alloying_elements` (or individual: `chromium`, `nickel`, `manganese`) | When alloying becomes economically relevant |

### High-Level Input/Output Relationships

```
Stage 1 (Beneficiation):
  iron_ore (3.0 t) + electricity (40 kWh) + water → iron_concentrate (1.0 t) + tailings (2.0 t)

Stage 2a (Blast Furnace — Earth route):
  iron_concentrate (1.5 t) + coke (0.5 t) + limestone (0.4 t) + thermal_energy → pig_iron (1.0 t) + slag (1.2 t)

Stage 2b (Hydrogen DRI — Frontier route):
  iron_concentrate (1.5 t) + electricity (18 MWh) → DRI/pig_iron (1.0 t) + water_vapor (byproduct)

Stage 3a (BOF Steelmaking):
  pig_iron (0.75 t) + scrap_steel (0.15 t) + lime (0.06 t) + oxygen → crude_steel (1.0 t) + BOF_slag (0.3 t)

Stage 3b (EAF Steelmaking — requires scrap infrastructure):
  scrap_steel (0.85 t) + DRI (0.25 t) + electricity (420 kWh) + lime → crude_steel (1.0 t) + EAF_dust (0.03 t)

Stage 4 (Refining/Alloying):
  crude_steel (1.0 t) + alloy_elements (0.01–0.05 t) + electricity (75 kWh) → steel (1.0 t) + scale (byproduct)
```

### Where to Abstract Away Detail

The game can reasonably skip these intermediates without breaking economic plausibility:

1. **DRI vs pig_iron distinction** — Merge into a single `pig_iron` material for simplicity. The hydrogen DRI route is just a different *input mix* for producing the same output.
2. **Crude steel → refined steel** — Merge into a single `steel` material. Alloying can be modeled as optional input modifiers (adding chromium increases cost but improves properties).
3. **Casting/rolling forms** — Billets, blooms, slabs are just geometric forms of the same material. Don't create separate material files for each.

**Minimum viable chain**: `iron_ore → iron_concentrate → pig_iron → steel` (4 materials, 3 production stages). This captures the mass reduction, energy intensity, and capital cost progression without over-complicating the data model.

---

## 3. Generalization Pattern for Other Materials

### Universal Pattern: Ore → Concentrate → Metal → Alloy/Semi-finished

This pattern applies to **all** base metals:

| Material | Ore | Concentrate | Metal | Alloy/Semi |
|----------|-----|-------------|-------|------------|
| Iron | iron_ore / hematite / magnetite | iron_concentrate | pig_iron → crude_steel → steel | structural_steel, stainless_steel |
| Aluminum | bauxite | alumina (Al₂O₃) | aluminum | aluminum_alloy |
| Copper | copper_ore / chalcopyrite | copper_concentrate | blister_copper → refined_copper | copper_alloy, wire_grade_copper |
| Titanium | ilmenite / rutile | titanium_tetrachloride (intermediate) | titanium_sponge | titanium_alloy |
| Nickel | pentlandite | nickel_concentrate | nickel_matte → refined_nickel | nickel_alloy |

### Material-Specific Variations

Not every material follows the exact same path:

1. **Aluminum is different** — Bauxite → alumina (Bayer process, chemical) → aluminum (Hall-Héroult electrolysis). The concentrate step is chemically distinct (alumina is not a physical concentrate but a chemically refined intermediate). Energy intensity is extreme (~13–15 MWh/t vs ~5 GJ/t for iron BF).

2. **Titanium is different** — Kroll process produces titanium sponge from TiCl₄. Multiple chemical intermediates. Very energy and capital intensive. Not suitable for early-game frontier production.

3. **Silicon is different** — Quartz → metallurgical silicon (electric arc furnace) → polysilicon (chemical purification for semiconductors). The ore → metal step is direct; the value jump is in purity, not form.

4. **Precious metals (gold, silver)** — No concentrate/smelt chain needed at game scale. Ore → refined metal via cyanide leaching or direct smelting. Low mass throughput, high value density.

### What's Universal Across All Metals

| Aspect | Universal? | Notes |
|--------|-----------|-------|
| Ore extraction (mining) | ✅ Yes | Always the first stage; raw material from geological deposits |
| Concentration/beneficiation | ✅ Yes (but form varies) | Physical or chemical — always reduces mass before metal extraction |
| Reduction/smelting/electrolysis | ✅ Yes | Energy-intensive core step; determines route feasibility |
| Refining/purification | ✅ Yes | Always needed to reach usable purity |
| Alloying/semi-finished forms | ⚠️ Optional | Only relevant for materials used in manufacturing, not as raw inputs |
| Byproduct generation | ✅ Yes | Slag, off-gas, dust — always present, often economically recoverable |

---

## 4. Recommendations for `production.input_materials` Schema

### Current State

Our template v1.6 defines:
```json
"input_materials": [
  { "id": "", "amount": 0.0 }
]
```

This is too simple for multi-stage chains. Here's what it needs to support:

### Recommendation 1: Support Mass Ratios (Input:Output)

The `amount` field should represent **kilograms of input per kilogram of output** (or tonnes per tonne — same ratio). This enables cost propagation through the chain:

```json
"production": {
  "facility_type": "beneficiation_plant",
  "energy_kwh_per_kg": 0.04,
  "input_materials": [
    { "id": "iron_ore", "amount": 3.0 },
    { "id": "water", "amount": 0.01 }
  ],
  "byproducts": [
    { "id": "tailings", "amount": 2.0 }
  ]
}
```

**Interpretation**: To produce 1 kg of `iron_concentrate`, you need 3.0 kg of `iron_ore` and 0.01 kg of water, yielding 2.0 kg of `tailings`.

### Recommendation 2: Support Alternative Routes

Use a `routes` array to model BF-BOF vs EAF vs hydrogen DRI:

```json
"production": {
  "routes": [
    {
      "route_id": "bf_bof",
      "route_name": "Blast Furnace / Basic Oxygen Furnace",
      "facility_type": "blast_furnace_complex",
      "energy_kwh_per_kg": 2.0,
      "input_materials": [
        { "id": "iron_concentrate", "amount": 1.5 },
        { "id": "coke", "amount": 0.5 },
        { "id": "limestone", "amount": 0.4 }
      ],
      "byproducts": [
        { "id": "slag", "amount": 1.2 }
      ]
    },
    {
      "route_id": "hydrogen_dri",
      "route_name": "Hydrogen Direct Reduction (ISRU)",
      "facility_type": "hydrogen_reactor",
      "energy_kwh_per_kg": 5000.0,
      "input_materials": [
        { "id": "iron_concentrate", "amount": 1.5 },
        { "id": "electricity", "amount": 0.0 }
      ],
      "byproducts": [
        { "id": "water_vapor", "amount": 0.3 }
      ]
    }
  ]
}
```

**Why**: On Luna/Mars, the hydrogen DRI route is the only viable early option. The game should let players choose routes based on available infrastructure (coke requires coal carbonization; hydrogen requires electrolysis). Each route has different cost structure and resource dependencies.

### Recommendation 3: Byproduct Credit System

Byproducts should have a `credit` field to model their economic value as co-products or waste disposal costs:

```json
"byproducts": [
  { "id": "slag", "amount": 1.2, "credit": true },
  { "id": "manufacturing_dust", "amount": 0.01, "credit": false }
]
```

- `credit: true` — byproduct has positive value (can be sold or reused); reduces net production cost
- `credit: false` — byproduct is waste; may have disposal cost (not modeled yet)

**Example**: Slag from iron smelting can be sold for construction use, offsetting pig iron production cost. This creates an economic incentive for integrated production chains.

### Recommendation 4: Yield Loss Tracking

Add a `yield` field to account for mass losses in processing:

```json
"production": {
  "yield": 0.92,
  "input_materials": [ ... ],
  "byproducts": [ ... ]
}
```

**Interpretation**: 92% of input mass becomes usable output; 8% is lost to process inefficiency (not captured by explicit byproducts). This prevents the data model from requiring every gram of waste to be explicitly tracked.

### Recommendation 5: Facility Type as Economic Gate

The `facility_type` field should serve as an **economic gate** — it tells the game what infrastructure must exist before this material can be produced locally:

```json
"facility_type": "beneficiation_plant"    // requires: mining equipment, water
"facility_type": "blast_furnace_complex"  // requires: coke production, oxygen plant
"facility_type": "hydrogen_reactor"       // requires: electrolysis infrastructure
"facility_type": "steel_mill"             // requires: pig_iron supply, scrap infrastructure
```

This enables the game to:
- Block local production until prerequisite facilities exist
- Show players what infrastructure they need to build
- Model transport costs for intermediate materials (can't make steel on Luna without first making pig iron there)

---

## 5. Concrete Example: What `iron_concentrate.json` Should Look Like

Here's a conceptual sketch of how the iron_concentrate material file should be structured using template v1.6 with the recommendations above:

```json
{
  "template": "material_v1.6",
  "id": "iron_concentrate",
  "name": "Iron Concentrate",
  "description": "Beneficiated iron ore with 65% Fe content, produced by crushing and separating raw iron ore.",
  "classification": {
    "category": "raw_materials",
    "subcategory": "processed",
    "type": "concentrate"
  },
  "properties": {
    "unit_of_measurement": "kg",
    "chemical_formula": "Fe₂O₃ (enriched)",
    "molar_mass_g_mol": 159.69,
    "density_stp_kg_m3": 2500,
    "iron_content_percent": 65
  },
  "cost_data": {
    "purchase_cost": {
      "currency": "USD",
      "amount": [RESEARCH_VALUE]
    },
    "import_config": {
      "mass_per_unit_kg": 1.0,
      "transport_category": "bulk_material",
      "hazard_multiplier": 1.0
    }
  },
  "production": {
    "routes": [
      {
        "route_id": "beneficiation",
        "route_name": "Crushing and Magnetic Separation",
        "facility_type": "beneficiation_plant",
        "energy_kwh_per_kg": 0.04,
        "input_materials": [
          { "id": "iron_ore", "amount": 3.0 },
          { "id": "water", "amount": 0.01 }
        ],
        "byproducts": [
          { "id": "tailings", "amount": 2.0, "credit": false }
        ]
      }
    ],
    "yield": 0.33
  },
  "storage_requirements": {
    "preferred_phase": "solid",
    "containment_type": "bulk_bin",
    "safety_protocol": "dust_control"
  }
}
```

**Key design decisions in this sketch:**
- `yield: 0.33` — 1 kg of concentrate requires 3 kg of ore (matches real-world ~3:1 ratio)
- `iron_content_percent: 65` — distinguishes concentrate from raw ore (~30–40% Fe) and from smelted metal (~95%+ Fe)
- `transport_category: "bulk_material"` — cheap to transport relative to value (unlike refined steel)
- Byproduct tailings has `credit: false` — waste with no immediate economic value (but could be used in construction later)

---

## 6. Summary of Recommendations

| # | Recommendation | Priority | Impact |
|---|---------------|----------|--------|
| 1 | Add `iron_concentrate`, `pig_iron`, `crude_steel` as new materials | P0 | Enables multi-stage chain modeling |
| 2 | Populate `production.input_materials` for iron.json and all processed metals | P0 | Fixes the 195/207 empty input chains |
| 3 | Support alternative production routes via `routes` array | P1 | Models BF-BOF vs EAF vs hydrogen DRI |
| 4 | Add `credit` field to byproducts for co-product economics | P1 | Enables slag value recovery modeling |
| 5 | Use `yield` field for mass loss tracking | P2 | Prevents need for exhaustive waste tracking |
| 6 | Treat `facility_type` as economic gate for local production blocking | P1 | Enables infrastructure dependency logic |
| 7 | Generalize pattern to aluminum, copper, titanium, nickel | P1 | Completes base metal chain coverage |
| 8 | Create `coke` material for BF route reductant | P2 | Needed for Earth-standard steel production |

---

*This is a research note only. No files have been modified. All recommendations are conceptual and should be reviewed before implementation.*
