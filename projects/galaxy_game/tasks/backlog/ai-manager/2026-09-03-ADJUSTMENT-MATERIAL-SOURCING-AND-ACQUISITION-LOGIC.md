---
status: backlog
priority: MEDIUM
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
created: 2026-09-03
depends_on: 2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md
---

# ARCHITECTURAL ADJUSTMENT: Material Sourcing & Acquisition Logic
**For Grok (AI Manager Development Lead)**

## What Changed (Session 2026-09-03)

During material sourcing convention refinement, we discovered and **fixed an architectural anti-pattern** that affects how the AI Manager routes material acquisition.

### The Problem We Solved

Material JSON files had hardcoded location keys (`lunar/martian/earth`) in sourcing blocks — a pattern that **doesn't scale to procedurally generated worlds or unknown settlements**.

**Example of dead/wrong pattern** (regolith_composite.json):
```json
"sourcing": {
  "lunar": { "availability": "very_high", "in_situ": true },
  "martian": { "availability": "very_high", "in_situ": true },
  "earth": { "availability": "none" }
}
```

**Investigation finding**: This sourcing block is **completely unread** by code. Runtime sourcing is calculated dynamically, not read from JSON.

### The Solution We Implemented

**epoxy_resin.json** is now the **canonical pattern** for all manufactured materials:

```json
{
  "production": {
    "facility_type": "chemical_synthesis_plant",
    "input_materials": [...],
    "energy_kwh_per_kg": 8.5,
    "production_time_hours": 0.5
  },
  "cost_data": {
    "purchase_cost": { "amount": 10000 },
    "import_config": { "transport_category": "standard" }
  },
  "pricing": {
    "lunar_production": {
      "available": true,
      "facility_required": "chemical_synthesis_plant",
      "cost_per_kg": 7500,
      "energy_kwh_per_kg": 8.5
    }
  }
}
```

**Key properties:**
- **No location-based keys** — facility_type is location-agnostic
- **Earth fallback price** in purchase_cost
- **Local production cost** in pricing.lunar_production (works anywhere in game with the facility)
- **Scales**: Sol (known bodies) → Eden (partially known) → procedural worlds (unknown)

---

## What the AI Manager Needs to Do (Acquisition Logic)

When a settlement base needs a material it cannot produce:

### Decision Tree
1. **Market scan** — Check each accessible celestial body for settlement market listings (other players/NPCs selling)
2. **Depot check** — Look for stockpiles in transit depots (L1, LEO, asteroid staging)
3. **Routing calculation** — Compute transport time + fuel cost via cycler network
4. **Cost comparison**:
   - Does any settlement have `facility_type` from `production.facility_type`?
   - Local production cost = `pricing.lunar_production.cost_per_kg` + input material costs
   - Earth fallback cost = `cost_data.purchase_cost.amount` + transport overhead + travel time
5. **Decision fork**:
   - **Urgent need** → Earth (highest cost, immediately available)
   - **Normal resupply** → Lowest total cost route (production + transport)
   - **Strategic** → ISRU + stockpile (long-term beats all imports)
6. **Present options** — Show base commander 2–3 routes with cost/time trade-offs

### Key Constraint (Drives Strategy)
**Travel time + transport cost are the real blockers.** This is why ISRU-first strategy is optimal.

---

## What Needs Implementation

### 1. Material JSON Audit (Non-blocking)
- [ ] Audit all manufactured material JSON files for location-keyed sourcing blocks
- [ ] Refactor any using the old `lunar/martian/earth` pattern to the new facility-based structure
- [ ] Files to check: `/data/json-data/resources/materials/processed/` (polymers, chemicals, composites, metals, alloys, etc.)
- **Status**: Not urgent for Foothold Planner MVP; document needed materials and defer

### 2. Acquisition Logic in Procurement Service (Blocking for Foothold)
- [ ] Implement market-scan for settlements with matching `facility_type`
- [ ] Calculate: local production cost vs. Earth import cost vs. depot availability
- [ ] Integration point: When `ProcurementService` or equivalent checks "can we get material X?", it should now:
  1. Check if base has `facility_type` from material.production → produce locally
  2. Check market listings (other settlements)
  3. Check depots (L1, LEO, etc.)
  4. Compare costs: production + transport vs. Earth baseline
  5. Return routing options (not just yes/no)

### 3. Cost Model Updates
- [ ] Earth baseline prices come from `cost_data.purchase_cost`
- [ ] Local production prices come from `pricing.lunar_production.cost_per_kg`
- [ ] Transport cost calculation: distance × fuel_per_km × fuel_price
- [ ] Total cost = material cost + transport + handling + time urgency factor
- [ ] Present to base commander: "Route A: 5000 USD, 30 days | Route B: 8000 USD, 3 days | Route C: 10000 USD immediate"

### 4. Stockpile/ISRU Incentive Structure
- [ ] Long-term: settlement + facility = production that beats import forever
- [ ] Medium-term: seasonal stockpiling for travel windows (cycler availability)
- [ ] Short-term: emergency Earth import only when time-critical
- [ ] AI Manager must explicitly recommend "build this facility" when import costs exceed threshold

---

## Files Changed (For Your Awareness)

### Data (Local Time Machine backup, NOT in git)
- `/data/json-data/resources/materials/processed/polymers/epoxy_resin.json` — Refactored to canonical pattern

### Documentation (In agent-tasks repo)
- `/memories/repo/material_sourcing_convention.md` — Updated with full AI Manager decision tree + anti-patterns
- `/projects/galaxy_game/status.md` — Session summary (commit 14c90a1)

---

## Integration with Resource-First Foothold Planner

The Foothold Planner task (`2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md`) already encodes this philosophy:
- ISRU-first (local extraction before import)
- Cost-aware (evaluates Earth baseline against production capability)
- Market-aware (can check for player/NPC listings)

**This adjustment** makes the **runtime acquisition logic explicit and scalable** so Foothold Planner has a clear interface to work with.

---

## Acceptance Criteria (For Implementation)

- [ ] Material JSON audit completed; refactor list documented (or deferred with justification)
- [ ] Procurement service routes material requests through market-first logic
- [ ] Cost comparison returns 2–3 options (Earth, local production, market purchase)
- [ ] Base commander sees routing choices with cost/time breakdown
- [ ] ISRU recommendation triggers when local production ROI is positive
- [ ] Test case: Base needs epoxy_resin; has chemical_synthesis_plant → gets local cost; doesn't have facility → market check + Earth fallback
- [ ] No hardcoded location-based routing (Luna/Mars/Venus separate code paths forbidden)

---

## Questions for Clarification

Ask if:
- Should depot routing be in this task or deferred to transport system?
- Are cycler transit times calculated as part of acquisition routing?
- Does "urgency" create price premiums (e.g., emergency Earth import costs 1.5x)?
- Should bases show "build this facility" recommendations proactively?

---

## Reference Documents

- `/memories/repo/material_sourcing_convention.md` — Full convention + decision tree
- `/data/json-data/resources/materials/processed/polymers/epoxy_resin.json` — Canonical pattern
- `docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md` — Existing philosophy
- `docs/architecture/services/ai_manager/AI_MANAGER_CONSTRUCTION_ECONOMICS.md` — Cost model precedent
