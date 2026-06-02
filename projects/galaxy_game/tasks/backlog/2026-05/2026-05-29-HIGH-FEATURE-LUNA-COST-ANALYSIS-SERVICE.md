---
status: backlog
priority: HIGH
type: feature
system_domain: MANUFACTURING
mvp_alignment: LUNA_SETTLEMENT_AUTONOMOUS_TESTING
local_worker_safe: true
---

# TASK: Luna Phase 1 — Cost Analysis Service

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-05-29  
**Last Updated**: 2026-05-29  

---

## Agent Assignment

**Assigned To**: GPT-4.1 (0x)  
**Why This Agent**: Implementation requires understanding production costs, market pricing, and integration with Settlement/Market systems.  
**Supervision Level**: standard

---

## Context

Luna settlement supply chain requires economic decision-making. Before importing resources, Luna needs to calculate whether local production or import is cheaper per resource. This service provides that foundational capability.

**Dependencies**: Derived from `2026-05-29-ANALYSIS-LUNA-BASE-BUILDUP-SUPPLY-CHAIN.md` (Q8 finding: "No direct method for comparing local vs import cost is present")

---

## Problem Statement

**Gap**: Settlements cannot determine "Should I produce locally or import this resource?"

**Missing Piece**: A service that calculates and compares:
- Local production cost (material requirements × base material costs)
- Current import price (from Market.Marketplace.current_market_condition)
- Returns recommendation (cheaper option + cost delta)

---

## Specification

### Service: `Settlements::CostAnalyzer`

**File**: `galaxy_game/app/services/settlements/cost_analyzer.rb`

#### Method: `self.compare_costs(resource_name, settlement)`

**Input**:
- `resource_name` (String): e.g., "Steel", "Oxygen", "Component A"
- `settlement` (Settlement::BaseSettlement): The settlement doing the comparison

**Output**: Hash with structure:
```ruby
{
  resource: "Steel",
  local_cost: 150.0,           # GCC per unit
  import_cost: 180.0,          # GCC per unit from current market
  local_cheaper: true,
  cost_delta: 30.0,            # import_cost - local_cost
  recommendation: :produce_locally,  # :produce_locally or :import
  confidence: :high            # :high, :medium, :low (if market data missing)
}
```

#### Method: `self.local_production_cost(resource_name, settlement)`

**Purpose**: Calculate cost to produce locally
**Inputs**: resource_name, settlement
**Outputs**: Float (GCC per unit)
**Logic**:
- Fetch resource blueprint (via existing blueprint/recipe system)
- Sum material requirements (e.g., Steel requires Iron + Coal)
- Multiply each material by its base cost (from game constants, NOT market price)
- Return total cost per unit

**Note**: Use `DECISIONS.md` for any financial constants (SCC surcharge, etc.)

#### Method: `self.current_import_price(resource_name, settlement)`

**Purpose**: Look up current market price
**Inputs**: resource_name, settlement
**Outputs**: Float (GCC per unit) or nil if not available
**Logic**:
- Query settlement's marketplace: `settlement.marketplace.current_market_condition(resource_name)`
- Extract price from most recent Market::PriceHistory
- Return nil if no market data exists

---

## Files to Modify/Create

**Create**:
- `galaxy_game/app/services/settlements/cost_analyzer.rb` — new service

**Modify**:
- `galaxy_game/spec/services/settlements/cost_analyzer_spec.rb` — test file (new)

---

## Test Cases

Test file should include:

1. **Happy path**: Compare cost for a known resource with market data
   - Assert local_cost is calculated
   - Assert import_cost is fetched
   - Assert recommendation is correct

2. **No market data**: When settlement.marketplace has no current_market_condition for resource
   - Assert import_cost is nil
   - Assert confidence is :low
   - Assert recommendation still works (defaults to local)

3. **Import cheaper**: When market price is lower than local production
   - Assert recommendation is :import
   - Assert local_cheaper is false

4. **Local cheaper**: When production cost is lower
   - Assert recommendation is :produce_locally
   - Assert local_cheaper is true

5. **Unknown resource**: When resource has no blueprint
   - Assert error or graceful handling

---

## Integration Points

**Used by** (future tasks):
- Phase 2 (Manifest Generation): Cost data informs cargo selection
- Phase 3 (Shortage Detection): Cost comparison triggers import requests

**Depends on** (existing):
- Settlement model
- Market::Marketplace.current_market_condition
- Blueprint/Recipe system (for material requirements)
- Game constants (DECISIONS.md)

---

## Success Criteria

✅ Service created with both methods  
✅ All 5 test cases passing  
✅ Handles missing market data gracefully  
✅ Uses game constants (no hardcoding)  
✅ Integrates with Market system correctly  
✅ Clear error messages for invalid resources  

---

## Notes

- This is foundational work. Phase 2 and 3 depend on it.
- Do NOT create a UI or controller endpoint yet.
- Service only. Tests only.
- If blueprint/recipe system doesn't exist, flag in completion report.
