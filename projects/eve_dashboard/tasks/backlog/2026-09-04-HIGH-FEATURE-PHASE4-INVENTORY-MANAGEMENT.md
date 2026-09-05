---
status: backlog
priority: HIGH
type: feature
system_domain: EVE_ONLINE_INTEGRATION
mvp_alignment: PLEX_FUND_OPTIMIZATION
local_worker_safe: true
---

## 🔴 Task Readiness Checklist

- [x] Dispatch Interface complete
- [x] Depends on Phase 3B (price tracking)
- [x] Synthesis report template provided

**READY for dispatch (after Phase 3B completes).**

---

## 🔴 Agent Dispatch Interface

```
You are **Phase 4 Implementation Agent** for Eve Dashboard Inventory Management.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md

STEP 0 — MOVE TASK FILE:
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md \
         projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md
  Then: status: backlog → status: active

CRITICAL: Save synthesis report to summaries/2026-09-04-PHASE4-INVENTORY-SYNTHESIS.md
```

---

# TASK: Phase 4 — Inventory Management & Asset Tracking

**Status**: BACKLOG (blocked on Phase 3B)
**Priority**: HIGH
**Type**: feature
**Depends On**: Phase 3B (prices available for valuation)

---

## Prerequisites — READ FIRST

1. **Project README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Phase 3B Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE3B-PRICE-TRACKING-SYNTHESIS.md`
3. **This Task File**: Everything below

---

## Handoff from Phase 3B

**What was completed in Phase 3B:**

✅ Price history tracking
- Daily snapshot of ice/ore prices (Jita region)
- 30-day trend analysis (average, high, low)
- Sell signal logic: "Now is a good time to haul"

✅ Price analysis table
- `price_analysis` table shows current prices vs 30-day average
- Trend field indicates rising/falling/stable
- Alerts generated when prices spike

**Current State:**
- Daily price snapshots stored automatically
- 30-day trends calculated and visible
- Sell signals generated when prices favorable
- Ready for inventory visibility

**Why Phase 4 is next:**
Phase 3B showed WHEN to sell (prices). Phase 4 shows WHAT you have (inventory): real-time ore/refined materials holdings, storage usage, and ISK value at current prices. Combined: Phase 3B + Phase 4 = "We have 50k Clear Icicle and prices are up 8% — time to haul!"

---

## Context

Real-time visibility into ore/ice holdings across fleet. Shows what you have, where it is, and value at current prices. Alerts user when storage is full or materials ready to sell.

---

## Implementation Steps

### Step 0: Move Task File

```bash
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md \
       projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md
```

### Step 1: Add Inventory Tables (app/db.py)

```python
c.execute('''
    CREATE TABLE IF NOT EXISTS inventory (
        id INTEGER PRIMARY KEY,
        character_id INTEGER,
        item_id INTEGER,
        item_name TEXT,
        quantity INTEGER,
        location_id INTEGER,
        location_name TEXT,
        location_type TEXT,
        item_group TEXT,
        isk_value_at_current_price REAL,
        last_updated DATETIME,
        FOREIGN KEY (character_id) REFERENCES characters(id)
    )
''')

c.execute('''
    CREATE TABLE IF NOT EXISTS refining_efficiency (
        character_id INTEGER PRIMARY KEY,
        skill_level INTEGER,
        refining_efficiency REAL,
        last_checked DATETIME,
        FOREIGN KEY (character_id) REFERENCES characters(id)
    )
''')
```

### Step 2: Create Inventory Module (app/inventory.py)

```python
import httpx
from app.db import get_db

def sync_inventory_for_character(character_id, access_token):
    """Fetch assets for one character from ESI"""
    headers = {"Authorization": f"Bearer {access_token}"}
    url = f"https://esi.evetech.net/latest/characters/{character_id}/assets/?datasource=tranquility"
    
    try:
        resp = httpx.get(url, headers=headers, timeout=10)
        if resp.status_code == 200:
            assets = resp.json()
            
            conn = get_db()
            c = conn.cursor()
            
            # Filter and store ore/refined materials only
            for asset in assets:
                item_id = asset['item_id']
                quantity = asset['quantity']
                location_id = asset['location_id']
                
                # Determine if this is ore/refined (filter out other items)
                if is_ore_or_refined(item_id):
                    item_name = get_item_name(item_id)
                    location_name = get_location_name(location_id)
                    item_group = classify_item(item_id)
                    
                    # Get price from Phase 3B
                    price = get_current_price(item_id)
                    value = quantity * price if price else 0
                    
                    c.execute('''
                        INSERT OR REPLACE INTO inventory
                        (character_id, item_id, item_name, quantity, location_id, location_name, item_group, isk_value_at_current_price)
                        VALUES (?, ?, ?, ?, ?, ?, ?, ?)
                    ''', (character_id, item_id, item_name, quantity, location_id, location_name, item_group, value))
            
            conn.commit()
    except Exception as e:
        logger.exception(f"Inventory sync error: {e}")

def aggregate_fleet_inventory():
    """Sum ore holdings across all mining fleet pilots"""
    conn = get_db()
    c = conn.cursor()
    
    c.execute('''
        SELECT item_name, SUM(quantity) as total, SUM(isk_value_at_current_price) as total_isk
        FROM inventory
        WHERE character_id IN (SELECT id FROM characters WHERE name LIKE "Neon%")
        GROUP BY item_name
        ORDER BY total_isk DESC
    ''')
    
    return c.fetchall()

def get_storage_usage():
    """Show storage capacity usage per location"""
    pass
```

### Step 3: Add Routes & Template

```python
@app.get("/inventory")
async def inventory_dashboard():
    fleet_inv = aggregate_fleet_inventory()
    return templates.TemplateResponse("inventory.html", {
        "request": request,
        "fleet_inventory": fleet_inv
    })
```

### Step 4: Create Inventory Template (templates/inventory.html)

Shows holdings by location, refining status, storage alerts.

### Step 5: Integrate with app/sync.py

```python
def sync_all():
    # ... existing code ...
    for char in list_characters():
        access_token = get_access_token(char['id'])
        from app.inventory import sync_inventory_for_character
        sync_inventory_for_character(char['id'], access_token)
```

---

## Acceptance Criteria

- [ ] Inventory table populated with ore/refined materials
- [ ] Fleet totals calculated correctly
- [ ] Values calculated using Phase 3B prices
- [ ] Per-location breakdown accurate
- [ ] Storage capacity alerts working
- [ ] Data updates on sync
- [ ] No exceptions in logs

---

## Synthesis Report

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE4-INVENTORY-SYNTHESIS.md`
