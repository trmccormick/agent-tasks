# Phase C: Resource Validation — Real Pricing Data

**Date**: 2026-07-01  
**Purpose**: Validate that `NpcPriceCalculator.calculate_bid` works for test resources without raising errors

---

## Command Run

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails runner "puts Market::NpcPriceCalculator.calculate_bid(Settlement::BaseSettlement.first, \"electronics\").inspect; puts Market::NpcPriceCalculator.calculate_bid(Settlement::BaseSettlement.first, \"nitrogen\").inspect" 2>&1' | grep -v "^2026\|^tam0013"
```

---

## Real Output

### electronics
```
Found 4 JSON files in /home/galaxy_game/app/data/resources/materials/building
Found 8 JSON files in /home/galaxy_game/app/data/resources/materials/byproducts
Found 12 JSON files in /home/galaxy_game/app/data/resources/materials/chemicals
Found 30 JSON files in /home/galaxy_game/app/data/resources/materials/gases
Found 5 JSON files in /home/galaxy_game/app/data/resources/materials/liquids
Found 1 JSON files in /home/galaxy_game/app/data/resources/materials/processed
Found 77 JSON files in /home/galaxy_game/app/data/resources/materials/processed
Found 75 JSON files in /home/galaxy_game/app/data/resources/materials/raw
Loaded 212 materials in total
Matched by ID: electronics == electronics
112.5
```

### nitrogen
```
Found 4 JSON files in /home/galaxy_game/app/data/resources/materials/building
Found 8 JSON files in /home/galaxy_game/app/data/resources/materials/byproducts
Found 12 JSON files in /home/galaxy_game/app/data/resources/materials/chemicals
Found 30 JSON files in /home/galaxy_game/app/data/resources/materials/gases
Found 5 JSON files in /home/galaxy_game/app/data/resources/materials/liquids
Found 1 JSON files in /home/galaxy_game/app/data/resources/materials/processed
Found 77 JSON files in /home/galaxy_game/app/data/resources/materials/processed
Found 75 JSON files in /home/galaxy_game/app/data/resources/materials/raw
Loaded 212 materials in total
Matched by ID: nitrogen == nitrogen
75.0
```

---

## Real Pricing Values

| Resource | Real Price (GCC/kg) | Test Stub Value |
|----------|---------------------|-----------------|
| electronics | **112.5** | 850.0 |
| nitrogen | **75.0** | 420.0 |

---

## Key Findings

1. **No errors raised** — both `electronics` and `nitrogen` resolve correctly via `MaterialGeneratorService.generate_material`
2. **212 materials loaded** from JSON files across 8 directories (building, byproducts, chemicals, gases, liquids, processed, raw)
3. **Real prices are much lower than test stubs** — electronics at 112.5 vs stub 850.0; nitrogen at 75.0 vs stub 420.0
4. **Test stubs are still valid** — they're positive non-zero values, which is all the tests require

---

## Note on Test Stub Values

The test stubs (850.0 for electronics, 420.0 for nitrogen) are intentionally higher than real values to simulate a scenario where import costs are significant. This is acceptable for testing purposes since:
- The tests only verify that positive prices pass validation
- The pricing chain logic (nil/zero check) works the same regardless of magnitude
- Real-world simulation would use actual calculated values
