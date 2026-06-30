---
name: "Raw Resource Extraction Pricing Architecture"
priority: HIGH
phase: phase8
created: 2026-06-21
status: backlog
type: architecture
---

# TASK: Raw Resource Extraction Pricing — Break-Even Cost Model

**Priority**: HIGH  
**Phase**: phase8 (Cycler Arc)  
**Type**: architecture/design  
**Created**: 2026-06-21  

## Problem Statement

Harvested raw resources (mined gases, raw ore from asteroids/moons) have no material purchase cost — the resource itself is free. However the true cost of extraction includes craft fuel, depreciation, energy, and risk. Without a floor price model, NpcPriceCalculator has no basis for valuing these resources and cannot determine whether local extraction is economically viable vs Earth import.

**Current**: `NpcPriceCalculator` has no model for raw harvested resources; falls back to Earth import cost  
**Expected**: A break-even cost model that computes the minimum viable sell price for a harvested resource based on actual mission costs

## Evidence of Incompleteness

```bash
grep -n "extraction\|break_even\|floor_price" galaxy_game/app/services/economics/npc_price_calculator.rb 2>/dev/null | head -5
# Likely returns no results or stub implementation
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/economics/npc_price_calculator.rb` | Price calculation service | Add extraction pricing method |
| `galaxy_game/app/models/tier1_price_modeler.rb` | Earth Anchor Price reference | Ceiling price logic |

## Acceptance Criteria

- [ ] Break-even cost model computes minimum viable sell price for harvested resources
- [ ] Formula includes: fuel_cost_round_trip + craft_depreciation_per_mission + energy_cost + risk_factor
- [ ] Floor price feeds into `cost_based_bid` as the floor price for NPC buyers
- [ ] If local extraction break-even exceeds Earth Anchor Price (EAP), importing from Earth is cheaper
- [ ] Design document includes formula derivation and example calculations

## Implementation Steps

1. Define break-even formula: `floor_price_per_kg = (fuel_cost_round_trip + craft_depreciation_per_mission + energy_cost + risk_factor) / total_kg_extracted`
2. Integrate with NpcPriceCalculator as floor price source for raw resources
3. Compare against EAP from Tier1PriceModeler — if break-even > EAP, mark resource as "import viable" not "extract viable"
4. Document design with example calculations

## Commit Instructions

```bash
git add galaxy_game/app/services/economics/npc_price_calculator.rb docs/architecture/
git commit -m "feat: add raw resource extraction break-even pricing model"
```
