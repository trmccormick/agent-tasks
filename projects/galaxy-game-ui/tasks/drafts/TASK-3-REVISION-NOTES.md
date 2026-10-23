# Task 3 Revision Notes — Replace MockDataHelper with GameDataJsonLoader

**Date**: 2026-10-23  
**Reason**: Clarified mock data approach to use proven WVU Moonshot pattern (JSON loader + JSONB field) instead of MockDataHelper helper methods.

---

## Key Changes Required

### 1. Replace MockDataHelper → GameDataJsonLoader Service

**OLD (Step 1 in current Task 3)**:
- Create `MockDataHelper` with 6 methods returning hardcoded data

**NEW**:
- Create `app/services/game_data_json_loader.rb` (mirrors WVU Moonshot `UserCsvLoader` pattern)
- Reads from `Rails.root.join("data", "game_data.json")` (file already mounted in container via docker-compose)
- Loads data into User's `metadata` JSONB field
- Simple class with `self.call(user)` method

### 2. Add User Model Migration

**NEW Step**:
Add migration to add `metadata :jsonb, default: {}` column to users table.

### 3. Update db/seeds.rb

**NEW Step**:
After `UserCsvLoader.call`, add:
```ruby
User.all.each { |user| GameDataJsonLoader.call(user) }
```

### 4. Create Sample Data File

**NEW Step**:
Create `data/imports/game_data.json` with structure:
```json
{
  "systems": [
    { "id": 1, "name": "Sol", "identifier": "SOL-001", "planet_count": 3 },
    { "id": 2, "name": "AOL-732356", "identifier": "AOL-732356", "planet_count": 5 }
  ],
  "settlements": [
    { "id": 1, "name": "Lunar Base Alpha", "system_id": 1, "type": "base", "population": 25,
      "operational_data": { "power_grid": { "status": "online", "efficiency": 0.95 } } }
  ],
  "inventory": [
    { "name": "Regolith", "amount": 500, "material_type": "raw_material" },
    { "name": "Water Ice", "amount": 200, "material_type": "raw_material" }
  ],
  "market_orders": [
    { "resource": "water", "quantity": 50, "order_type": "buy", "price": 10.5 },
    { "resource": "regolith", "quantity": 100, "order_type": "sell", "price": 5.0 }
  ],
  "missions": [
    { "identifier": "mission_1", "status": "in_progress", "progress": 45 },
    { "identifier": "mission_2", "status": "in_progress", "progress": 78 }
  ]
}
```

### 5. Update DashboardController

**CHANGE Step 2**:
Instead of calling MockDataHelper methods, access `current_user.metadata`:
```ruby
class DashboardController < ApplicationController
  before_action :authenticate_user!

  def index
    metadata = current_user.metadata || {}
    @systems = metadata['systems'] || []
    @settlements = metadata['settlements'] || []
    @inventory = metadata['inventory'] || []
    @market_orders = metadata['market_orders'] || []
    @missions = metadata['missions'] || []
  end
end
```

### 6. Update Dashboard View

**CHANGE Step 3**:
Panels reference `@systems`, `@settlements`, etc. directly instead of MockDataHelper.

Examples:
```erb
<%= @settlements.first&.dig('name') %>
<%= @inventory.map { |item| item['name'] }.join(', ') %>
```

---

## Why This Change

✅ Proven pattern (WVU Moonshot UserCsvLoader)  
✅ Uses real game data structure (from galaxyGame factories)  
✅ Simpler: one loader service, one JSONB field, views access it  
✅ Data stays in one place (not scattered across helper methods)  
✅ Easy to swap for real backend later  

---

## Unchanged

- Step 4: Application layout topbar (same)
- Step 5: Verification commands (same, but can add JSON file check)
- Acceptance criteria (same objectives, different implementation)

---

## Files to Modify/Create

**New Files**:
- `app/services/game_data_json_loader.rb`
- `data/imports/game_data.json`
- `db/migrate/[TIMESTAMP]_add_metadata_to_users.rb`

**Modified Files**:
- `app/controllers/dashboard_controller.rb`
- `app/views/dashboard/index.html.erb`
- `db/seeds.rb`

---

## Reference Materials

- WVU Moonshot loader pattern: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/app/services/user_csv_loader.rb`
- galaxyGame factory data: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/factories/`
- Docker mount point: `./data/imports:/home/galaxy_game/data` (already configured)
