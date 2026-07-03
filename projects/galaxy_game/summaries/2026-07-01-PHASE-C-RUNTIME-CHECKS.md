# Phase C: NpcPriceCalculator Runtime Checks

**Date**: 2026-07-01  
**Purpose**: Verify which NpcPriceCalculator class is active and its method availability before implementing request_import

---

## Check 1 — Active NpcPriceCalculator Methods (Runtime)

**Command**:
```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails runner "puts Market::NpcPriceCalculator.instance_methods(false).sort; puts Market::NpcPriceCalculator.singleton_methods(false).sort" 2>&1' | grep -v "^2026\|^tam0013"
```

**Real Output**:
```
calculate_ask
calculate_bid
calculate_spread
```

**Interpretation**: The **service-based** `NpcPriceCalculator` (`app/services/market/npc_price_calculator.rb`) is the active one. It has `calculate_ask`, `calculate_bid`, and `calculate_spread` as instance methods — matching the full implementation, not the model stub.

---

## Check 2 — `can_produce_locally` / `calculate_import_cost` in Service File

**Command**:
```bash
grep -n "can_produce_locally\|calculate_import_cost" galaxy_game/app/services/market/npc_price_calculator.rb
```

**Real Output**:
```
92:        if can_produce_locally?(settlement, resource_name)
201:      def can_produce_locally?(settlement, resource_name)
```

The service-based file has both methods. The `can_produce_locally?` check at line 92 is called from within `calculate_import_cost`, which itself is called from `cost_based_ask` and `cost_based_bid`. This confirms the pricing chain flows through:

```
calculate_bid → cost_based_bid → calculate_import_cost → can_produce_locally? → calculate_earth_import_cost → Tier1PriceModeler.calculate_eap
```

---

## Key Takeaway for Step 3

The service-based `NpcPriceCalculator` is authoritative. Its `calculate_bid` already implements the full pricing chain (local production cost → Earth import cost via `Tier1PriceModeler`). The task is to wire this into `request_import` instead of using the magic threshold.
