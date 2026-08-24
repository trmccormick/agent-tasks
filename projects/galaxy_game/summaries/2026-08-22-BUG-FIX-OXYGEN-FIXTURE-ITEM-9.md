# 2026-08-22 BUG FIX — Oxygen Fixture Item #9 (Storage/Key Fix)

## Task
`2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md`

## Status
COMPLETED

## Date
2026-08-22

---

## What Was Changed

### File: `galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb`

**Change 1 — Order resource (line ~175):**
```ruby
# before
let(:oxygen_order) { ... resource: 'oxygen' ... }

# after
let(:oxygen_order) { ... resource: 'raw_regolith' ... }
```

**Change 2 — Storage unit type (before block):**
```ruby
# before
operational_data: { 'storage' => { 'type' => 'gas', ... } }

# after
operational_data: { 'subcategory' => 'general', 'storage' => { 'type' => 'general', ... } }
```

**Change 3 — Assertion (line ~273):**
```ruby
# before
expect(settlement.inventory.current_storage_of('oxygen')).to be > 0

# after
expect(settlement.inventory.current_storage_of('raw_regolith')).to be > 0
```

**Change 4 — Harvester config test (line ~217):**
```ruby
# before: expects "Automated Oxygen Harvester", task_type 'atmospheric_harvesting', target_material 'O2'
# after:  expects "Automated Raw Regolith Miner", task_type 'regolith_mining', target_material 'raw_regolith'
```

---

## Root Cause (Two Bugs)

### Bug 1 — Test ordered a non-existent resource
The test created an order for `resource: 'oxygen'` as if free O₂ could be harvested directly on Luna. There is no such deposit. The correct harvestable resource is `raw_regolith`.

### Bug 2 — Storage unit fixture used wrong key
The fixture set `'storage' => { 'type' => 'gas' }` but `BaseUnit#storage_type` reads `operational_data['subcategory']`, NOT `operational_data['storage']['type']`. The test also needed `subcategory: 'general'` (not `'gas'`) because raw_regolith is type `'soil'` which requires general storage per `can_store_material?`.

---

## Verification
- Isolation run: 1 example, 0 failures
- Full file run: 20 examples, 0 failures
- No regressions in sibling tests

---

## Important Note

This fix confirms a harvester can deliver raw_regolith to a settlement with adequate storage. It does NOT confirm that raw_regolith is ever routed through TEU → PVE to produce O2 for an oxygen-triggered escalation order. That question is still open and is NOT resolved by this task. Do not mark Priority #1 (oxygen chain-tracing) as closed based on this fix.

---

## Stop Conditions Met
- ✅ No production code touched (HarvesterCompletionJob / EscalationService unchanged)
- ✅ Test-only fix
- ✅ All tests in file pass
