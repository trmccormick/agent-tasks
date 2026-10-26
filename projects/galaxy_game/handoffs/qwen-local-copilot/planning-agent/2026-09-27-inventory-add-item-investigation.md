---
date: 2026-09-27
session_agent: Qwen (planning agent) — preparing handoff for Grok
status: Ready for Grok continuation
---

# Handoff: Inventory `add_item` Investigation

## Context

A previous session was investigating why `BaseSettlement#inventory.add_item('binding_agent', 50, player)` returns `false` silently (no items persisted). The investigation was interrupted before pinpointing the exact failure point.

## What Was Confirmed

1. **`add_item` returns `false`** — not an exception, a silent rejection
2. **`can_store?` is private** on `Inventory` — calling it directly raises `NoMethodError`
3. **Settlement gets its own Inventory record** (id 35303 in the trace)
4. **Storage unit also gets its own Inventory record** (id 35304) which is **destroyed during factory setup** — this may be a FactoryBot issue or expected behavior
5. **Zero items** persisted after the call

## Code Under Investigation

**File:** `galaxy_game/app/models/inventory.rb`

### `add_item` flow (lines 16-32):
```ruby
def add_item(name, amount, owner = nil, metadata = {})
  result = can_store?(name, amount)
  return false unless result          # ← FIRST FAIL POINT
  owner ||= determine_default_owner

  if specialized_storage_required?(name)
    store_in_inventory(name, amount, owner, metadata)
  elsif capacity_exceeded?(amount + total_stored)
    handle_surface_storage(name, amount, owner, metadata)
  else
    store_in_inventory(name, amount, owner, metadata)
  end
end
```

### `can_store?` (private, lines ~95-106):
```ruby
def can_store?(name, amount)
  return false unless inventoryable   # ← could fail here if inventoryable is nil

  if specialized_storage_required?(name)
    unit = find_storage_unit(name)
    return false unless unit          # ← could fail here if no storage unit found
    unit.available_capacity >= amount
  else
    available_general_storage >= amount  # ← could fail here if capacity is 0
  end
end
```

### Key methods to check:
- `lookup_material_type(name)` — returns material category (liquid/gas/fuel/general)
- `specialized_storage_required?(name)` — true if material type is liquid/gas/fuel
- `find_storage_unit(name)` — finds a base_unit that can store the material type
- `available_general_storage` — sums capacity of general storage units on inventoryable
- `inventoryable_capacity` — sums storage unit capacities or returns 0

## What Needs to Be Done

### Step 1: Clean test DB and reproduce with proper diagnostics
The previous diagnostic command failed with `Identifier has already been taken` (FactoryBot uniqueness violation from leftover records). Need to:
```bash
# Inside web container:
unset DATABASE_URL
bundle exec rails runner -e test "ActiveRecord::Base.connection.execute('DELETE FROM items'); ActiveRecord::Base.connection.execute('DELETE FROM inventories'); ActiveRecord::Base.connection.execute('DELETE FROM base_units'); ActiveRecord::Base.connection.execute('DELETE FROM base_settlements'); ActiveRecord::Base.connection.execute('DELETE FROM players')"
```

Then re-run with step-by-step diagnostics:
```ruby
settlement = FactoryBot.create(:base_settlement)
player = FactoryBot.create(:player)
su = FactoryBot.create(:base_unit, unit_type: 'storage', owner: settlement, operational_data: {'subcategory' => 'general', 'storage': {'capacity' => 10000, 'current_level' => 0}})

inv = settlement.inventory
puts "inventoryable: #{inv.inventoryable.class}"
puts "base_units count: #{inv.inventoryable.base_units.count}"
puts "material_type: #{inv.send(:lookup_material_type, 'binding_agent').inspect}"
puts "specialized_required?: #{inv.send(:specialized_storage_required?, 'binding_agent')}"
puts "available_general_storage: #{inv.send(:available_general_storage)}"
puts "total_stored: #{inv.total_stored}"
puts "capacity: #{inv.send(:inventoryable_capacity)}"

# If specialized: check find_storage_unit
if inv.send(:specialized_storage_required?, 'binding_agent')
  unit = inv.send(:find_storage_unit, 'binding_agent')
  puts "storage unit for binding_agent: #{unit.inspect}"
end

result = inv.add_item('binding_agent', 50, player)
puts "add_item result: #{result.inspect}"
puts "items count: #{inv.items.count}"
```

### Step 2: Identify the exact failure point
Based on the code, the likely culprits are:
1. **`inventoryable` is nil** — `can_store?` returns false immediately
2. **`lookup_material_type('binding_agent')` returns nil or unexpected type** — causes wrong branch in `can_store?`
3. **No storage unit found** for the material type — `find_storage_unit` returns nil
4. **`available_general_storage` is 0** — general storage capacity calculation fails

### Step 3: Fix the root cause
Once the exact failure point is identified, fix it. Most likely outcomes:
- If `inventoryable` is nil → check how Inventory is associated with BaseSettlement
- If material type lookup fails → check if binding_agent exists in material JSON
- If storage unit not found → check `can_store_material?` on the storage unit
- If capacity is 0 → check `inventoryable_capacity` calculation

## Files to Read
- `galaxy_game/app/models/inventory.rb` (full file)
- `galaxy_game/app/models/item.rb` (line 93+ for Item#add_item if relevant)
- `galaxy_game/app/models/base_settlement.rb` (how inventory is built/associated)
- `galaxy_game/app/models/base_unit.rb` (storage_type, can_store_material?)
- `galaxy_game/spec/factories/base_settlement.rb` (factory setup)
- `galaxy_game/spec/factories/base_unit.rb` (factory setup)
- `data/json-data/resources/materials/` — check if binding_agent exists

## What NOT to Do
- Don't assume the storage unit's destroyed Inventory is the bug — that may be expected
- Don't change `can_store?` visibility without understanding why it's private
- Don't fix symptoms — identify the exact validation that fails first
