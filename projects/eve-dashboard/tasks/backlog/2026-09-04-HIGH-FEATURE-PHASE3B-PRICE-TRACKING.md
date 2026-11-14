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
- [x] Depends on Phase 3 (mining/market data)
- [x] Synthesis report template provided

**READY for dispatch (after Phase 3 completes).**

---

## 🔴 Agent Dispatch Interface

```
You are **Phase 3B Implementation Agent** for Eve Dashboard Price Tracking.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md

STEP 0 — MOVE TASK FILE:
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md \
         projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md
  Then: status: backlog → status: active

CRITICAL: Save synthesis report to summaries/2026-09-04-PHASE3B-PRICE-TRACKING-SYNTHESIS.md
```

---

# TASK: Phase 3B — Price Tracking & Market Analysis

**Status**: BACKLOG (blocked on Phase 3)
**Priority**: HIGH
**Type**: feature
**Depends On**: Phase 3 (mining + market data working)

---

## Prerequisites — READ FIRST

1. **Project README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Phase 3 Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE3-MINING-MARKET-SYNTHESIS.md`
3. **This Task File**: Everything below

---

## Handoff from Phase 3

**What was completed in Phase 3:**

✅ Mining ledger data retrieval
- ESI `/characters/{id}/mining/` endpoint queried for all 7 mining pilots
- `mining_ledger` table populated with ore/ice quantities and timestamps
- Daily ore volume totals calculated

✅ Market order tracking
- `market_orders` table created for tracking buy/sell orders
- Market price lookup implemented (ESI `/markets/{region_id}/orders/`)
- Jita market snapshot stored daily

✅ Basic mining dashboard
- Daily ISK generation displayed per pilot
- Fleet totals shown
- Ore type breakdown visible

**Current State:**
- Mining data flowing into database daily
- Market prices visible in logs
- Basic dashboard showing fleet activity
- Ready for pricing intelligence

**Why Phase 3B is next:**
Phase 3 showed WHAT is being mined. Phase 3B adds pricing history and trends: When are prices high? When should you sell? This enables sale optimization for maximum PLEX fund growth.

---

## Context

Phase 3B enables sales optimization by tracking ice/ore prices over time. User can see when prices peak and sell refined materials for maximum PLEX fund growth.

---

## Critical Gotchas

⚠️ **GOTCHA 1: ESI Market Prices**
- ESI `/markets/prices/` returns region-aggregated prices (not Jita-specific)
- Use `/markets/{region_id}/orders/` for Jita prices (high/low/median)
- ❌ Wrong: Use aggregated ESI prices directly
- ✅ Right: Query Jita region, find median of sell orders

⚠️ **GOTCHA 2: Price History Timing**
- Prices change minute-by-minute in Jita
- Store daily snapshot (1x per day max) to avoid spam
- ❌ Wrong: Store every price check (thousands/day)
- ✅ Right: Store one snapshot per day at fixed time (00:00 UTC)

---

## Implementation Steps

### Step 0: Move Task File

```bash
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md \
       projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md
```

### Step 1: Add Price Tables (app/db.py)

```python
c.execute('''
    CREATE TABLE IF NOT EXISTS price_history (
        id INTEGER PRIMARY KEY,
        item_id INTEGER,
        item_name TEXT,
        price REAL,
        region TEXT,
        timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
        UNIQUE(item_id, region, DATE(timestamp))
    )
''')

c.execute('''
    CREATE TABLE IF NOT EXISTS price_analysis (
        item_id INTEGER PRIMARY KEY,
        item_name TEXT,
        current_price REAL,
        avg_30_day REAL,
        high_30_day REAL,
        low_30_day REAL,
        trend TEXT,
        last_updated DATETIME
    )
''')
```

### Step 2: Create Price Module (app/prices.py)

```python
import httpx
from datetime import datetime, timedelta
from app.db import get_db

ICE_ITEMS = {
    34: 'Condensed Ice',
    36: 'Glacial Mass',
    37: 'Clear Icicle',
    38: 'Dense Icicle'
}

ORE_ITEMS = {
    1: 'Veldspar',
    # ... more ore types
}

def fetch_jita_prices(item_id):
    """Get current Jita price for one item"""
    url = f"https://esi.evetech.net/latest/markets/10000002/orders/?type_id={item_id}&order_type=sell&datasource=tranquility"
    
    try:
        resp = httpx.get(url, timeout=10)
        if resp.status_code == 200:
            orders = resp.json()
            if orders:
                # Return median sell price
                prices = sorted([o['price'] for o in orders])
                return prices[len(prices) // 2]
        return None
    except Exception as e:
        logger.exception(f"Price fetch error: {e}")
        return None

def sync_price_history():
    """Daily snapshot of ice/ore prices"""
    conn = get_db()
    c = conn.cursor()
    
    all_items = {**ICE_ITEMS, **ORE_ITEMS}
    
    for item_id, item_name in all_items.items():
        price = fetch_jita_prices(item_id)
        
        if price:
            c.execute('''
                INSERT OR IGNORE INTO price_history (item_id, item_name, price, region)
                VALUES (?, ?, ?, ?)
            ''', (item_id, item_name, price, 'Jita'))
    
    conn.commit()
    logger.info("Price history synced")

def analyze_prices():
    """Calculate 30/60-day trends"""
    conn = get_db()
    c = conn.cursor()
    
    cutoff_30 = (datetime.now() - timedelta(days=30)).isoformat()
    
    c.execute('''
        SELECT DISTINCT item_id, item_name FROM price_history
    ''')
    
    for item_id, item_name in c.fetchall():
        c.execute('''
            SELECT AVG(price), MAX(price), MIN(price) FROM price_history
            WHERE item_id = ? AND timestamp >= ?
        ''', (item_id, cutoff_30))
        
        avg, high, low = c.fetchone()
        
        # Get current price
        c.execute('''
            SELECT price FROM price_history WHERE item_id = ?
            ORDER BY timestamp DESC LIMIT 1
        ''', (item_id,))
        
        result = c.fetchone()
        current = result[0] if result else avg
        
        # Determine trend
        if current > avg:
            trend = 'rising'
        elif current < avg:
            trend = 'falling'
        else:
            trend = 'stable'
        
        c.execute('''
            INSERT OR REPLACE INTO price_analysis
            (item_id, item_name, current_price, avg_30_day, high_30_day, low_30_day, trend, last_updated)
            VALUES (?, ?, ?, ?, ?, ?, ?, ?)
        ''', (item_id, item_name, current, avg, high, low, trend, datetime.now()))
    
    conn.commit()
```

### Step 3: Add Routes (app/main.py)

```python
@app.get("/api/prices")
async def get_prices():
    conn = get_db()
    c = conn.cursor()
    c.execute('SELECT * FROM price_analysis ORDER BY trend DESC')
    prices = c.fetchall()
    return {"prices": prices}
```

### Step 4: Create Price Dashboard (templates/prices.html)

Shows price leaderboard, trends, sell signals.

### Step 5: Integrate with app/sync.py

```python
def sync_all():
    # ... existing code ...
    from app.prices import sync_price_history, analyze_prices
    sync_price_history()
    analyze_prices()
```

---

## Acceptance Criteria

- [ ] Price history table stores daily snapshots
- [ ] 30-day trends calculated correctly
- [ ] Sell signal logic working (high price → haul now)
- [ ] Price comparison page displays leaderboard
- [ ] Data updates daily
- [ ] No exceptions in logs

---

## Synthesis Report

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE3B-PRICE-TRACKING-SYNTHESIS.md`
