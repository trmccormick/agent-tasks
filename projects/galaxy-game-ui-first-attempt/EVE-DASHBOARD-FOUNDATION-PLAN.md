# Galaxy Game UI — Clean Foundation Plan

**Date**: 2026-10-22  
**Phase**: Proper Planning (based on actual codebase analysis)  
**Approach**: Use EVE Dashboard CSS exactly as-is, build Galaxy Game content around it

---

## Actual Current State

### What Exists
✅ Rails 8 app structure (galaxy-game-ui)  
✅ Tailwind CSS v4 configured with galaxy-* colors  
✅ Routes defined: dashboard, wormholes, admin, sessions  
✅ DashboardController created (references MockDataHelper methods)  
✅ Dashboard view partially started (Panel H + Panel 1)  
✅ Application layout with simple nav (Dashboard, Wormholes, Admin)  
✅ EVE Dashboard CSS available for import  
✅ Main Galaxy Game landing page structure available to extract  
✅ **Main Galaxy Game has complete data models**:
  - Player, Colony, Settlement, Inventory, Item
  - Mission (with status: in_progress, completed, stalled, failed)
  - Market models (Order, Marketplace, PriceHistory, Trade)
  - Craft models (Harvester, Rover, Spaceship variants)
  - Financial/Account system

### What Doesn't Exist (BLOCKERS)
❌ **MockDataHelper** — Referenced in controller but file doesn't exist  
❌ Landing page view  
❌ Login/session view (has broken WVU CSS)  
❌ EVE Dashboard CSS imported/configured  
❌ Application layout using EVE Dashboard structure  
❌ Navigation menu system (Wealth, Assets, Market, Tasks, Mail, Notes)  
❌ Wormholes view  
❌ Admin view  

### Current Error
Dashboard won't load because `MockDataHelper` doesn't exist. Solution: Extract actual Galaxy Game data structures and wrap in MockDataHelper for testing.

---

## Actual Data Mapping (From Galaxy Game)

**From Galaxy Game Models** (don't invent, pull existing):

| UI Needs | Galaxy Game Source | Data Fields |
|----------|-------------------|-------------|
| `system_status` | Player + Colony + Settlement | name, active_location, account_balance, current_time |
| `bay_inventory` | Inventory + Item (polymorphic) | item.name, item.amount, item.metadata |
| `player_ships` | Craft models (Spaceship, Harvester, Rover) | name, fuel_capacity, cargo_capacity, crew_capacity, units |
| `npc_buy_orders` | Market::Order | asset, quantity, price, order_type (buy/sell), timestamp |
| `active_missions` | Mission | identifier, settlement, status (in_progress/completed/stalled/failed), operational_data |
| `market_quick_panel` | Market::Marketplace + Market::PriceHistory | asset_name, current_price, price_change, volume_today |

**Example MockDataHelper Return Values** (structurally matching actual game):
```ruby
# system_status uses Player, Colony, Settlement data
{ 
  player_name: "Commander Name",
  settlement_name: "Settlement 1",
  account_balance: 50000,
  timestamp: "2026-10-22 14:30"
}

# bay_inventory mirrors Inventory::Item structure
[
  { name: "Iron Ore", quantity: 5000, max_qty: 10000, unit: "kg" },
  { name: "Water", quantity: 3000, max_qty: 8000, unit: "L" }
]

# player_ships mirrors Spacecraft/Craft model
[
  { name: "Harvester-01", fuel: 1000, fuel_max: 1500, cargo: 500 }
]

# npc_buy_orders mirrors Market::Order
[
  { asset: "Iron Ore", quantity: 1000, price: 50, timestamp: "2h ago" }
]

# active_missions mirrors Mission model
[
  { id: 1, name: "Explore Settlement 2", status: "in_progress" }
]

# market_quick_panel mirrors Market::Marketplace
{ iron_ore: 50, copper: 75, water: 25 }
```

---

## Clean Task Sequence

### Task 1: Create MockDataHelper (BLOCKING — must do first)
**Why**: DashboardController references it; app won't load without it  
**Scope**:
- Create `app/helpers/mock_data_helper.rb`
- Pull data from actual Galaxy Game structures (don't invent):
  - `system_status` — Extract from Player, Colony, Settlement (player name, location, account balance)
  - `bay_inventory` — Use Inventory/Item model data (colonized resources like in Spacecraft inventory)
  - `player_ships` — Use Craft models (Harvester, Rover, Spaceship variants with fuel/capacity stats)
  - `npc_buy_orders` — Use Market::Order model data (real order structure from marketplace)
  - `active_missions` — Use Mission model (status: in_progress, completed, stalled, failed)
  - `market_quick_panel` — Use Market::Marketplace and Market::PriceHistory data
- Use Faker for names/generated details, but structure matches actual game data
- Return data structures matching what controllers/views expect

**Data Mapping** (from actual models):
- Player: name, active_location, inventory
- Inventory: items (with name, amount, owner, metadata)
- Mission: identifier, settlement, status, operational_data
- Market::Order: asset, quantity, price, order_type (buy/sell)
- Spacecraft: fuel_capacity, current_fuel, cargo_capacity, crew_capacity
- Colonies/Settlements: name, population, resources

**Estimate**: 1.5-2 hours  
**Verification**: Dashboard loads without errors, displays actual game-structured data

---

### Task 2: Import EVE Dashboard CSS (Foundation)
**Why**: Need CSS foundation before styling any views  
**Scope**:
- Copy EVE Dashboard CSS from `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`
- Create `app/assets/stylesheets/eve-dashboard.css`
- Include sections:
  - CSS variables (`:root { --bg, --panel, --panel2, --line, --text, --muted, --accent, --green, --red }`)
  - Resets/normalize
  - Layout framework (topbar, main, panels, buttons)
  - Typography, tables, scrollbars
- Import into `application.css`
- Do NOT modify colors or styling; use exactly as-is
- Keep Tailwind config as-is (galaxy-* utilities available as fallback)

**Estimate**: 1 hour  
**Verification**: CSS compiles, page structure works, no color conflicts

---

### Task 3: Create Application Layout using EVE Dashboard
**Why**: Establish navigation/menu structure that all pages will use  
**Scope**:
- Update `app/views/layouts/application.html.erb`:
  - Use EVE Dashboard `.topbar` structure (header with brand + nav)
  - Create nav menu with items: **Wealth**, **Assets**, **Market**, **Tasks**, **Mail**, **Notes**
  - Use `.main-inner` for content container
  - Replace current simple nav with EVE-style topbar
- Map Galaxy Game concepts to EVE menu:
  - **Wealth** → Player account/balance view
  - **Assets** → Ship inventory + Bay inventory
  - **Market** → Buy/Sell orders, quick prices
  - **Tasks** → Manufacturing, research, missions
  - **Mail** → Messages (placeholder)
  - **Notes** → Notes (placeholder)
- Use EVE Dashboard `.btn`, `.panel`, styling throughout

**Deliverable**:
```html
<div class="topbar">
  <div class="brand"><a href="/">Galaxy Game</a></div>
  <div class="toolbar">
    <a href="#" class="btn">Wealth</a>
    <a href="#" class="btn">Assets</a>
    <a href="#" class="btn">Market</a>
    <a href="#" class="btn">Tasks</a>
    <a href="#" class="btn">Mail</a>
    <a href="#" class="btn">Notes</a>
  </div>
</div>

<main>
  <div class="main-inner">
    <%= yield %>
  </div>
</main>
```

**Estimate**: 1-1.5 hours  
**Verification**: Page layout uses EVE Dashboard structure, nav renders correctly

---

### Task 4: Create Landing Page
**Why**: Entry point for new users (before login)  
**Scope**:
- Extract structure from main Galaxy Game landing page (`/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/landing/index.html.erb`)
- Create `app/views/landing/index.html.erb`:
  - Hero section (Galaxy Game logo, title, tagline)
  - Auth buttons (Sign In, Create Account, Learn More)
  - Galaxy Status section (4 metric cards)
  - Active Systems section (grid of system cards)
- Apply EVE Dashboard CSS styling:
  - Use `.panel` for status cards
  - Use `.card` for system cards
  - Use `.btn` for auth buttons
  - Use EVE variables for colors
- Create LandingController that passes mock data

**Deliverable**: Landing page using EVE CSS, Galaxy Game content structure  

**Estimate**: 1.5-2 hours  
**Verification**: Landing page loads, uses EVE styling, mock data displays

---

### Task 5: Fix Session/Login View
**Why**: Users need to log in before accessing dashboard  
**Scope**:
- Update `app/views/sessions/new.html.erb`:
  - Remove all `wvu-*` CSS references
  - Apply EVE Dashboard `.panel` styling
  - Use `.btn.primary` for submit
  - Use EVE CSS variables for colors
  - Update text (WVU → Galaxy Game branding)
- Add SessionsController if needed

**Deliverable**: Login form using EVE CSS  

**Estimate**: 45 min  
**Verification**: Form renders with proper styling, no broken classes

---

### Task 6: Complete Dashboard with EVE Styling
**Why**: Dashboard is started but needs proper styling + remaining panels  
**Scope**:
- Update `app/views/dashboard/index.html.erb`:
  - Apply EVE `.panel` class to all panels
  - Replace Tailwind gradient classes with EVE CSS
  - Remove inline `style=` attributes (use CSS instead)
  - Complete Panels 2-5 (Ships, Manufacturing, Orders, Market)
  - Use MockDataHelper data in all panels
- Verify all 6 mock data methods are being used

**Deliverable**: Complete dashboard using EVE CSS  

**Estimate**: 2-3 hours  
**Verification**: All panels render, mock data displays, no styling issues

---

### Task 7: Create Wormholes View
**Why**: Needed for navigation (in app nav)  
**Scope**:
- Create `app/views/wormholes/index.html.erb`:
  - Network visualization placeholder (or simple grid)
  - Settlement list with wormhole connections
  - Use EVE `.panel` and `.card` styling
  - Create WormholesController

**Estimate**: 1.5-2 hours  

---

### Task 8: Create Admin View
**Why**: Needed for navigation (in app nav)  
**Scope**:
- Create `app/views/admin/index.html.erb`:
  - System overview metrics
  - Settlement management section
  - Use 2-column layout (sidebar + content) with EVE CSS
  - Create AdminController

**Estimate**: 1.5-2 hours  

---

## Complete Sequencing

```
Task 1: MockDataHelper (1-1.5h) ← BLOCKS everything
  ↓
Task 2: Import EVE Dashboard CSS (1h)
  ↓
Task 3: Application Layout with EVE (1-1.5h)
  ↓
Tasks 4-8 (can start after Task 3):
  ├─ Task 4: Landing Page (1.5-2h)
  ├─ Task 5: Session/Login (45 min)
  ├─ Task 6: Complete Dashboard (2-3h)
  ├─ Task 7: Wormholes (1.5-2h)
  └─ Task 8: Admin (1.5-2h)

TOTAL: 12-15 hours
Critical Path: Tasks 1→2→3 (3.5-4 hours) then parallel 4-8 (7-9 hours)
```

---

## Why This Approach is Clean

1. **No color mixing** — EVE Dashboard used exactly as-is
2. **No aesthetic conflicts** — Single CSS foundation for all views
3. **Clear sequencing** — MockDataHelper must exist first
4. **Data-driven** — Uses actual mock data from controller
5. **Navigation clear** — EVE Dashboard menu pattern applied to Galaxy Game
6. **Landing page pattern proven** — Extracted from working main project
7. **Parallelize-able** — After foundation tasks, can work on multiple views

---

## What's Different from Previous Plan

✅ No color overrides (use EVE Dashboard exactly)  
✅ No Galaxy Game main project aesthetic mixing  
✅ Focus on blocking tasks first (MockDataHelper)  
✅ Clear task sequencing based on actual code  
✅ Proper navigation menu structure (not just links)  
✅ Landing page before dashboard (proper UX flow)  
✅ Based on what actually exists, not theory  

---

## Next Steps

**Approval needed**:
1. Is this sequencing correct?
2. Should Tasks 4-8 be dispatched in parallel after Task 3 completes?
3. Any adjustments to the scope?

**If approved**:
Create individual task files for each of Tasks 1-8 with detailed specs, code examples, acceptance criteria.
