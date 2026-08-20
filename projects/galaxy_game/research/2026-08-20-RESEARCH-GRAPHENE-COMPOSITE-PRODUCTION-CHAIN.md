# Research: Graphene Composite Production Chain for Galaxy Game

**Status:** Pending Gemini Analysis  
**Created:** 2026-08-20  
**Goal:** Define game-viable production blueprint for `graphene_composite` material

## Context

User approved `graphene_composite` as a producible material IF:
- Production chain routes back to harvestable/importable raw sources
- Chain depth is shallow (2-3 steps max, not deep nesting)
- Production is real-world feasible (current tech acceptable, Earth-sourced materials OK)

## Research Requirements

### 1. Graphene Composite Production Analysis
- **What is graphene composite in real-world manufacturing?**
  - Material composition (what elements/materials combine?)
  - Standard production methods (how is it currently made industrially?)
  - Key input materials (what raw precursors are required?)

### 2. Game-Viable Input Material Mapping
- **Primary input:** Identify which already-exists game material could be the base
  - Is carbon_fiber suitable as primary input?
  - Or does graphite need to be a separate raw material?
  - Or carbide/other carbon source?
- **Secondary inputs:** What processing materials/binders are needed?
  - Real name and production source of any binder/matrix material
  - Can it be simplified for game or does complexity matter?

### 3. Production Blueprint Design
- **Input materials** (list with amounts for producing 10 kg graphene_composite)
- **Facility type** (fabrication_plant or advanced_fabrication_facility?)
- **Production time** (hours to produce 10 kg)
- **Power consumption** (kW during production)
- **Technology requirements** (which tech tree unlocks this?)

### 4. Material Dependencies Validation
Ensure production chain is traceable:
```
Raw Source (extractable/importable) → [Intermediate if needed] → graphene_composite → Advanced Units (mk2/mk3 storage, etc.)
```

Example of acceptable chain:
- Carbon_fiber (extracted) → graphene_composite (produced) → mk3 storage tank (produced) ✓

Example of unacceptable chain:
- Unknown material X → Unknown material Y → Unknown material Z → graphene_composite ✗

## Deliverable

Provide:
1. **Material composition** - exact inputs needed
2. **Production blueprint JSON template** - ready for `/data/json-data/blueprints/materials/`
3. **Integration notes** - which mk2/mk3 blueprints should use graphene_composite and where

---
**Note:** This research unblocks mk2/mk3 storage optimization and boil-off reduction. Venus/Titan skimmer operations can proceed in parallel using mk1 engines + mk2 storage tanks (hybrid approach) while this research completes.
