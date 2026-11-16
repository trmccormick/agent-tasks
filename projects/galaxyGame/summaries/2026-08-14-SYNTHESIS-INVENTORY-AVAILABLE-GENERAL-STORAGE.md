# Synthesis: Inventory#available_general_storage Silent-Zero Bug

**Date:** 2026-08-14
**Task:** 2026-08-09-HIGH-BUGFIX-INVENTORY-AVAILABLE-GENERAL-STORAGE-SILENT-ZERO
**Status:** Synthesis complete — fix strategy identified

---

## Synthesis Findings

### Current Code State

#### BaseUnit#storage_type (line 237)
```ruby
def storage_type
  operational_data&.dig('storage', 'type')
end
```
- `storage_type` DOES exist as a derived attribute from `operational_data['storage']['type']`
- Returns `nil` when no storage type is configured (NOT the string `'general'`)

#### Inventory#available_general_storage (line 152-156)
```ruby
def available_general_storage
  return 0 unless inventoryable.respond_to?(:base_units)
  
  general_storage_units = inventoryable.base_units.select { |u| u.storage_type == 'general' }
  general_storage_units.sum(&:available_capacity)
end
```
- **BUG:** Checks `u.storage_type == 'general'` but units with no specific storage type return `nil`, not `'general'`
- `nil == 'general'` is `false` → all units filtered out → always returns 0

#### HasStorage#storage_capacity_by_type (line 23-28) — THE CORRECT PATTERN
```ruby
def storage_capacity_by_type
  return {} unless respond_to?(:base_units)

  base_units.each_with_object(Hash.new(0)) do |unit, capacities|
    type = unit.storage_type || 'general'   # ← nil defaults to 'general'
    capacities[type] += unit.storage_capacity
  end
end
```
- **This is the established pattern:** `storage_type || 'general'` treats nil as general storage

#### HasStorage#find_storage_unit_for (line 105-110) — ALSO USES THE PATTERN
```ruby
def find_storage_unit_for(material_type)
  return nil unless respond_to?(:base_units)

  base_units.find do |unit|
    unit.storage_type == material_type_to_storage(material_type) &&
      unit.operational_data&.dig('storage', 'capacity').to_i > 0
  end
end
```
- This one is OK because `material_type_to_storage` maps to specific types (liquid, gas, etc.)

### Caller Analysis

#### Callers of `available_general_storage`:
1. **`Inventory#can_store?`** (line 74): `available_general_storage >= amount` — primary caller
2. **`Inventory#add_item`** (line 52): passes to capacity check
3. **Historical:** Previous synthesis doc (`2026-07-29-BUGFIX-INVENTORY-CAN-STORE-TEST-BYPASS.md`) references it

#### Other code checking `u.storage_type`:
1. **`HasStorage#storage_capacity_by_type`** (line 26): ✅ Uses correct pattern `|| 'general'`
2. **`HasStorage#find_storage_unit_for`** (line 108): OK — compares against specific material types
3. **`BaseUnit#compatible_storage?`** (line 429-439): Returns false if nil — this is intentional (no type = can't store)

### Fix Strategy

**Fix:** Change `Inventory#available_general_storage` to use the established pattern from `HasStorage#storage_capacity_by_type`:

```ruby
# Before (buggy):
general_storage_units = inventoryable.base_units.select { |u| u.storage_type == 'general' }

# After (correct):
general_storage_units = inventoryable.base_units.select { |u| u.storage_type.nil? || u.storage_type == 'general' }
```

**Rationale:**
- Uses the same pattern already established in `HasStorage#storage_capacity_by_type`
- No migration needed — `storage_type` already exists as a derived attribute
- Minimal scope — only one method changed
- Nil means "no specific storage type" = general-purpose storage (consistent with domain model)

### Test Impact

#### Shell-printing spec mocks to remove (commit 04460b23):
1. **Line 104-105:** `allow(settlement.inventory).to receive(:can_store?).and_return(true)`
2. **Line 209-210:** Same mock in second test block

These mocks were added because `can_store?` internally calls `available_general_storage` which always returns 0, making real capacity checks impossible. Once fixed, the mocks can be removed and tests will use real storage calculations.

#### Other specs that stub `can_store?`:
- **`spec/models/inventory_spec.rb`** (~7 tests): These test `add_item` branching logic — they should KEEP their mocks (correct pattern for unit-testing branches)
- **`spec/models/craft/harvester_spec.rb`** (line 48): Tests harvester full-storage behavior — keep mock

---

## Implementation Plan

### Step 1: Fix Inventory#available_general_storage
- File: `galaxy_game/app/models/inventory.rb`, line ~155
- Change: `u.storage_type == 'general'` → `u.storage_type.nil? || u.storage_type == 'general'`

### Step 2: Remove shell-printing spec mocks
- File: `galaxy_game/spec/services/manufacturing/shell_printing_service_spec.rb`
- Remove lines 104-105 and 209-210 (the `allow(...).to receive(:can_store?).and_return(true)` blocks)

### Step 3: Verification
- Run shell-printing spec: `rspec spec/services/manufacturing/shell_printing_service_spec.rb`
- Run inventory spec: `rspec spec/models/inventory_spec.rb`
- Verify no regressions in affected specs

---

## Acceptance Criteria Mapping
- [x] Fix uses established codebase pattern (`|| 'general'`) from HasStorage
- [x] No migration needed — storage_type is a derived attribute
- [x] Only one method changed (minimal scope)
- [x] Shell-printing mocks removed (2 instances)
- [x] Other specs' mocks preserved (correct usage pattern)
