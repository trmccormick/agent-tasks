# Status Synthesis: Inventory#can_store? Test Env Bypass

**Date**: 2026-08-07
**Task**: 2026-07-29-HIGH-BUGFIX-INVENTORY-CAN-STORE-TEST-BYPASS.md

---

## Root Cause (Step 1 findings)

The bypass was added in commit `5f8c944` ("Add Inventory model with 16 passing examples") on **Jan 8, 2026** by the user. The original code had `return true if Rails.env.test?`. This commit expanded it to `return true if Rails.env.test? || defined?(RSpec)` — likely because specs were running in a non-test Rails env (e.g., development) where `Rails.env.test?` was false but RSpec was still loaded.

**Verdict**: Deliberate stopgap, not an oversight. The author needed tests to pass while the storage-unit infrastructure (`can_store_material?`, base unit factories) wasn't fully wired up yet. It was never removed because specs continued passing (due to the bypass), creating a false sense of correctness.

---

## Proposed Fix (Step 3 approach)

### Approach: (a) Remove bypass + build minimal test infrastructure

**Primary change — `app/models/inventory.rb`:**
```ruby
def can_store?(name, amount)
  # Original logic only — no env check
  return false unless inventoryable

  if specialized_storage_required?(name)
    unit = find_storage_unit(name)
    return false unless unit
    unit.available_capacity >= amount
  else
    available_general_storage >= amount
  end
end
```

**Secondary changes needed:**

1. **`spec/models/inventory_spec.rb`** — ~7 tests stub `can_store?` to `true`. These are testing `add_item`'s branching logic (specialized vs surface storage), not capacity itself. They can keep stubbing `can_store?` since that's the correct pattern for unit-testing `add_item` branches. No change needed there.

2. **New spec** — Add a focused test for `can_store?` itself (private method, so test via `send` or through `add_item` with a real inventoryable). This is where we need a minimal factory:
   - Need a `base_unit` that can store general materials (not specialized)
   - Or stub the private methods (`available_general_storage`, `find_storage_unit`) for targeted testing

3. **Specs that call `add_item` without stubbing** — The manufacturing service specs (`production_service_spec.rb`, `component_production_service_spec.rb`, `shell_printing_service_spec.rb`) all call `add_item` on real settlement inventories. These likely have base_units set up via factories. Need to verify they pass.

---

## Risk Assessment

| Risk | Level | Mitigation |
|------|-------|------------|
| Manufacturing specs silently fail due to missing capacity | MEDIUM | Run them first with bypass removed; fix factories if needed |
| `find_storage_unit` returns nil for inventoryable (no base_units) | HIGH | Most tests use `settlement` as inventoryable — need to verify settlement factory creates base_units |
| Gotcha 2 (`add_pile` signature) needs real work | LOW-MEDIUM | Confirmed: `add_pile(material_name:, ...)` works because it maps `material_name:` → `material_type:` in the find_or_initialize. But `source_unit:` param is accepted and ignored. Not broken, just dead code. |

---

## Gotcha 2 Analysis (SurfaceStorage#add_pile)

**Current signature**: `add_pile(material_name:, amount:, source_unit: nil)`
**MaterialPile validates**: `material_type` (presence), no `source_unit` column

**Finding**: The method is **not broken** — it maps `material_name:` to `material_type:` in the find_or_initialize. The `source_unit:` parameter is accepted but never used (dead code). No schema mismatch because it's just a keyword arg that gets ignored.

**Recommendation**: Clean up `source_unit:` as dead code (trivial, one-line change). Do NOT split into separate task.

---

## Decision Points for User Approval

1. **Approach (a) vs (b)**: I recommend (a) — remove the bypass entirely and fix factories. Approach (b) (narrow stub helper) would leave a half-measure in production code. The real issue is that capacity logic was never tested, not that test env needs special handling.

2. **Scope of factory work**: If manufacturing specs need new base_unit factories, that's additional scope. I'll audit first and report exact count before proceeding.

3. **Gotcha 2 cleanup**: Confirmed trivial (remove unused `source_unit:` param). Proceeding with this in the same pass?

---

## Next Steps (awaiting approval)
1. Remove bypass from `can_store?`
2. Audit manufacturing specs for base_unit factory gaps
3. Add focused `can_store?` spec
4. Clean up `add_pile(source_unit:)` dead code
5. Run affected spec sets, log results
