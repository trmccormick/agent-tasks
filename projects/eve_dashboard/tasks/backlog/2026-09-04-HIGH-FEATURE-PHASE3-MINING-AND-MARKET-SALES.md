---
status: backlog
priority: HIGH
type: feature
system_domain: EVE_ONLINE_INTEGRATION
mvp_alignment: PLEX_FUND_OPTIMIZATION
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template is provided (copy/paste ready)
- [x] Depends on Phase 2 completion (OAuth integration)
- [x] No placeholder text remains
- [x] All file paths verified

**Task is READY for dispatch (after Phase 2 completes).**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Phase 3 Implementation Agent** for Eve Dashboard Mining & Market Tracking.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md \
         projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/
  Filename pattern: 2026-09-04-PHASE3-MINING-MARKET-SYNTHESIS.md
```

---

# TASK: Phase 3 — Mining Ledger & Market Sales Tracking

**Status**: BACKLOG (blocked on Phase 2 completion)
**Priority**: HIGH
**Type**: feature
**Created**: 2026-09-04
**Depends On**: Phase 2 (OAuth + ESI connectivity working)

---

## Prerequisites — READ FIRST

1. **Project README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Phase 2 Completion**: Phase 2 must be COMPLETED before this starts
3. **This Task File**: Everything below

---

## Handoff from Phase 2

**What was completed in Phase 2:**

✅ OAuth credentials registered and tested
- All 7 user accounts successfully authenticated via EVE Online SSO
- All 11 characters loaded into database via ESI
- Access tokens cached and refreshing correctly
- ESI API connectivity verified

✅ Database initialized
- `characters` table populated with 11 pilots
- `access_tokens` table storing refresh tokens
- WAL mode enabled for concurrent access

✅ Logging operational
- All errors logged to `data/logs/dashboard.log`
- No exceptions during OAuth flow

**Current State:**
- User authenticated and all characters visible
- Flask app running without errors
- ESI data retrieval working
- Ready for mining/market features

**Why Phase 3 is next:**
Phase 2 validated authentication. Phase 3 implements operational dashboards: mining ledger tracking (how much ore mined?), market sales tracking (how much ISK generated?), and daily production totals.

---

## Context

Phase 3 implements the core mining + market sales features. Builds on Phase 2's validated OAuth/ESI connectivity.

**User's Operation**:
- Mining: 4 Orca + Hulks fleet (7 Omega pilots) → all ore/ice reprocessed locally
- Market: Refined materials hauled to Jita for sale (3 support alts handle logistics)
- Goal: Aggregate fleet-wide mining production + sales tracking

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1: Mining Ledger Format**
- ESI returns mining ledger grouped by date + location + ore type
- Must aggregate across all 7 mining pilots to get fleet-wide totals
- ❌ Wrong: Sum all ore values without accounting for pilot who mined it
- ✅ Right: Aggregate per-pilot first, then fleet total

⚠️ **GOTCHA 2: Market Sales Entry Type**
- Wallet journal has multiple entry types: "player_trading", "bounty_prizes", "mission_reward", etc.
- ❌ Wrong: Include all wallet journal entries as "sales"
- ✅ Right: Filter for "player_trading" ref_type ONLY (actual player market sales)

⚠️ **GOTCHA 3: Refined vs Raw Ore Value**
- User sells refined materials (Clear Icicle, Dense Icicle) not raw ore
- ESI mining ledger gives RAW ore quantities
- ❌ Wrong: Show ore values directly as profit
- ✅ Right: Calculate refined material yields using character refining skill

---

## 🔴 REQUIRED: Synthesis Report Template

Before starting, post this to chat (save to summaries folder):

```markdown
## SYNTHESIS REPORT — Phase 3 Mining & Market Sales

**Task**: Phase 3 Implementation
**Status**: backlog → active
**Date**: 2026-09-04

### What I'm About to Do
1. Implement mining_ledger table + sync function
2. Implement market_sales aggregation (wallet journal filtering)
3. Create mining.html dashboard page
4. Create trading.html dashboard page
5. Integrate with Phase 2 ESI data (mining ledger, wallet journal)
6. Test with real character data (all 11 characters)

### Files I'll Create/Modify
| File | Purpose |
|------|---------|
| `app/mining.py` | NEW mining ledger module |
| `app/market.py` | NEW market sales module |
| `app/sync.py` | MODIFY add mining/market sync |
| `app/db.py` | MODIFY add mining_ledger + market_sales tables |
| `templates/mining.html` | NEW mining dashboard page |
| `templates/trading.html` | NEW trading/sales dashboard page |
| `templates/base.html` | MODIFY add navbar links |

### Acceptance Criteria I'm Verifying
- [ ] Mining ledger populated after sync (all 7 pilots)
- [ ] Fleet-wide mining totals calculated correctly
- [ ] Market sales filtered correctly (player_trading only)
- [ ] Mining page displays per-pilot + fleet totals
- [ ] Trading page displays sales by character + date
- [ ] Dashboard navbar shows Mining + Trading links
- [ ] Data matches ESI values (spot check 3-5 transactions)
- [ ] No exceptions in logs
- [ ] Synthesis report saved to summaries/
```

**DO NOT START WORK UNTIL SYNTHESIS IS POSTED.**

---

## Implementation Steps

### Step 0: Move Task File

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md \
       projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md
# Edit: status: backlog → status: active
```

### Step 1: Create Database Tables

**In app/db.py, add**:

```python
def init_db():
    # ... existing code ...
    
    # Mining Ledger Table
    c.execute('''
        CREATE TABLE IF NOT EXISTS mining_ledger (
            id INTEGER PRIMARY KEY,
            character_id INTEGER,
            date DATE,
            ore_type TEXT,
            quantity REAL,
            quantity_m3 REAL,
            location_id INTEGER,
            location_name TEXT,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (character_id) REFERENCES characters(id),
            UNIQUE(character_id, date, ore_type, location_id)
        )
    ''')
    
    # Market Sales Table
    c.execute('''
        CREATE TABLE IF NOT EXISTS market_sales (
            id INTEGER PRIMARY KEY,
            character_id INTEGER,
            date DATE,
            item_type TEXT,
            quantity INTEGER,
            unit_price REAL,
            total_isk REAL,
            transaction_id INTEGER UNIQUE,
            ref_id INTEGER,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (character_id) REFERENCES characters(id)
        )
    ''')
    
    conn.commit()
```

### Step 2: Create Mining Module (app/mining.py)

```python
import httpx
from datetime import datetime, timedelta
from app.config import settings
from app.db import get_db

def fetch_mining_ledger(character_id, access_token):
    """Fetch mining ledger from ESI for one character"""
    headers = {"Authorization": f"Bearer {access_token}"}
    
    # ESI endpoint: /characters/{character_id}/mining
    url = f"https://esi.evetech.net/latest/characters/{character_id}/mining/?datasource=tranquility"
    
    try:
        resp = httpx.get(url, headers=headers, timeout=10)
        if resp.status_code == 200:
            return resp.json()  # List of {date, solar_system_id, ore_type_id, quantity}
        else:
            logger.error(f"Mining ledger fetch failed: {resp.status_code}")
            return []
    except Exception as e:
        logger.exception(f"Mining ledger error for char {character_id}: {e}")
        return []

def sync_mining_for_character(character_id, access_token):
    """Fetch + store mining data for one character"""
    ledger = fetch_mining_ledger(character_id, access_token)
    
    if not ledger:
        return
    
    conn = get_db()
    c = conn.cursor()
    
    for entry in ledger:
        date = entry.get('date')[:10]  # YYYY-MM-DD
        ore_type_id = entry.get('ore_type_id')
        quantity = entry.get('quantity')
        solar_system_id = entry.get('solar_system_id')
        
        # Fetch ore type name from EVE universe (or use cached lookup)
        ore_type_name = get_ore_name(ore_type_id)
        location_name = get_system_name(solar_system_id)
        
        c.execute('''
            INSERT OR REPLACE INTO mining_ledger
            (character_id, date, ore_type, quantity, quantity_m3, location_id, location_name)
            VALUES (?, ?, ?, ?, ?, ?, ?)
        ''', (character_id, date, ore_type_name, quantity, quantity * ore_volume(ore_type_id), 
              solar_system_id, location_name))
    
    conn.commit()
    logger.info(f"Synced {len(ledger)} mining entries for character {character_id}")

def aggregate_fleet_mining(date_range_days=7):
    """Aggregate mining data for all 7 mining pilots"""
    conn = get_db()
    c = conn.cursor()
    
    since_date = (datetime.now() - timedelta(days=date_range_days)).strftime('%Y-%m-%d')
    
    # Fleet totals
    c.execute('''
        SELECT ore_type, SUM(quantity) as total_qty, SUM(quantity_m3) as total_m3
        FROM mining_ledger
        WHERE date >= ? AND character_id IN (SELECT id FROM characters WHERE name LIKE "Neon%")
        GROUP BY ore_type
        ORDER BY total_m3 DESC
    ''', (since_date,))
    
    results = []
    for ore_type, qty, qty_m3 in c.fetchall():
        results.append({
            'ore_type': ore_type,
            'quantity': qty,
            'quantity_m3': qty_m3,
            'estimated_isk': qty_m3 * estimate_ore_price(ore_type)
        })
    
    return results

def get_mining_by_pilot(date_range_days=7):
    """Mining data per pilot"""
    # Similar to aggregate_fleet_mining but GROUP BY character_id instead
    pass
```

### Step 3: Create Market Sales Module (app/market.py)

```python
import httpx
from datetime import datetime, timedelta
from app.config import settings
from app.db import get_db

def fetch_wallet_journal(character_id, access_token):
    """Fetch wallet journal (transaction history) from ESI"""
    headers = {"Authorization": f"Bearer {access_token}"}
    url = f"https://esi.evetech.net/latest/characters/{character_id}/wallet/journal/?datasource=tranquility&page=1"
    
    try:
        resp = httpx.get(url, headers=headers, timeout=10)
        if resp.status_code == 200:
            return resp.json()
        else:
            logger.error(f"Wallet journal fetch failed: {resp.status_code}")
            return []
    except Exception as e:
        logger.exception(f"Wallet journal error for char {character_id}: {e}")
        return []

def sync_market_sales_for_character(character_id, access_token):
    """Extract player_trading entries from wallet journal (market sales only)"""
    journal = fetch_wallet_journal(character_id, access_token)
    
    if not journal:
        return
    
    conn = get_db()
    c = conn.cursor()
    
    for entry in journal:
        ref_type = entry.get('ref_type')  # 'player_trading', 'bounty_prizes', etc.
        
        # ONLY include actual market sales (player_trading)
        if ref_type != 'player_trading':
            continue
        
        date = entry.get('date')[:10]
        amount = entry.get('amount')  # ISK amount
        ref_id = entry.get('ref_id')  # Transaction ID
        
        # Get item details if available
        second_party_id = entry.get('second_party_id')  # Buyer/seller ID
        
        c.execute('''
            INSERT OR REPLACE INTO market_sales
            (character_id, date, item_type, quantity, unit_price, total_isk, ref_id)
            VALUES (?, ?, ?, ?, ?, ?, ?)
        ''', (character_id, date, 'market_sale', 1, amount, amount, ref_id))
    
    conn.commit()
    logger.info(f"Synced market sales for character {character_id}")

def aggregate_fleet_sales(date_range_days=7):
    """Total ISK from sales across all characters"""
    conn = get_db()
    c = conn.cursor()
    
    since_date = (datetime.now() - timedelta(days=date_range_days)).strftime('%Y-%m-%d')
    
    c.execute('''
        SELECT SUM(total_isk) as total_isk, COUNT(*) as num_transactions
        FROM market_sales
        WHERE date >= ?
    ''', (since_date,))
    
    result = c.fetchone()
    return {
        'total_isk': result[0] or 0,
        'num_transactions': result[1] or 0
    }

def get_sales_by_pilot(date_range_days=7):
    """ISK per character from sales"""
    pass
```

### Step 4: Update app/sync.py

Add to `sync_all()`:

```python
def sync_all():
    # ... existing sync code ...
    
    for char in list_characters():
        access_token = get_access_token(char['id'])
        
        # NEW: Sync mining ledger
        from app.mining import sync_mining_for_character
        sync_mining_for_character(char['id'], access_token)
        
        # NEW: Sync market sales
        from app.market import sync_market_sales_for_character
        sync_market_sales_for_character(char['id'], access_token)
```

### Step 5: Create Mining Dashboard (templates/mining.html)

```html
{% extends "base.html" %}

{% block content %}
<div class="mining-dashboard">
    <h2>Fleet Mining Activity</h2>
    
    <!-- Fleet Totals -->
    <div class="fleet-summary">
        <h3>Last 7 Days</h3>
        <p>Total ore mined: {{ fleet_totals.total_m3 }} m³</p>
        <p>Estimated value: {{ fleet_totals.total_isk | format_isk }} ISK</p>
    </div>
    
    <!-- Per-Ore Breakdown -->
    <table>
        <tr>
            <th>Ore Type</th>
            <th>Quantity (m³)</th>
            <th>Est. ISK Value</th>
        </tr>
        {% for ore in fleet_mining %}
            <tr>
                <td>{{ ore.ore_type }}</td>
                <td>{{ ore.quantity_m3 }}</td>
                <td>{{ ore.estimated_isk | format_isk }}</td>
            </tr>
        {% endfor %}
    </table>
    
    <!-- Per-Pilot Breakdown -->
    <h3>Mining by Pilot</h3>
    <table>
        <tr>
            <th>Pilot</th>
            <th>Ore Type</th>
            <th>Quantity</th>
        </tr>
        {% for pilot in pilot_mining %}
            <tr>
                <td>{{ pilot.name }}</td>
                <td>{{ pilot.ore_type }}</td>
                <td>{{ pilot.quantity }}</td>
            </tr>
        {% endfor %}
    </table>
</div>
{% endblock %}
```

### Step 6: Create Trading Dashboard (templates/trading.html)

```html
{% extends "base.html" %}

{% block content %}
<div class="trading-dashboard">
    <h2>Market Sales</h2>
    
    <!-- Fleet Totals -->
    <div class="sales-summary">
        <h3>Last 7 Days</h3>
        <p>Total sales: {{ fleet_sales.total_isk | format_isk }} ISK</p>
        <p>Transactions: {{ fleet_sales.num_transactions }}</p>
    </div>
    
    <!-- Per-Character Sales -->
    <h3>Sales by Pilot</h3>
    <table>
        <tr>
            <th>Pilot</th>
            <th>Total ISK</th>
            <th>Num Transactions</th>
        </tr>
        {% for pilot in pilot_sales %}
            <tr>
                <td>{{ pilot.name }}</td>
                <td>{{ pilot.total_isk | format_isk }}</td>
                <td>{{ pilot.num_transactions }}</td>
            </tr>
        {% endfor %}
    </table>
</div>
{% endblock %}
```

### Step 7: Update templates/base.html

Add navbar links:

```html
<nav>
    <a href="/">Dashboard</a>
    <a href="/wealth">Wealth</a>
    <a href="/assets">Assets</a>
    <a href="/mining">Mining</a>  <!-- NEW -->
    <a href="/trading">Trading</a>  <!-- NEW -->
    <a href="/homefronts">Homefronts</a>
</nav>
```

### Step 8: Add Routes to app/main.py

```python
@app.get("/mining")
async def mining_dashboard():
    from app.mining import aggregate_fleet_mining, get_mining_by_pilot
    fleet_mining = aggregate_fleet_mining()
    pilot_mining = get_mining_by_pilot()
    return templates.TemplateResponse("mining.html", {
        "request": request,
        "fleet_totals": {"total_m3": sum(x['quantity_m3'] for x in fleet_mining), 
                         "total_isk": sum(x['estimated_isk'] for x in fleet_mining)},
        "fleet_mining": fleet_mining,
        "pilot_mining": pilot_mining
    })

@app.get("/trading")
async def trading_dashboard():
    from app.market import aggregate_fleet_sales, get_sales_by_pilot
    fleet_sales = aggregate_fleet_sales()
    pilot_sales = get_sales_by_pilot()
    return templates.TemplateResponse("trading.html", {
        "request": request,
        "fleet_sales": fleet_sales,
        "pilot_sales": pilot_sales
    })
```

### Step 9: Test

```bash
cd /Users/tam0013/Documents/git/eve-dashboard
python3 run.py

# In another terminal
curl http://localhost:8765/mining
curl http://localhost:8765/trading

# Check logs for errors
tail data/logs/dashboard.log
```

---

## Acceptance Criteria

- [ ] Mining ledger table created in database
- [ ] Market sales table created in database
- [ ] Mining sync function retrieves ESI data for all 7 pilots
- [ ] Market sales sync filters for player_trading only
- [ ] Mining page displays fleet + per-pilot totals
- [ ] Trading page displays sales by character
- [ ] Data matches ESI (spot check 5 transactions)
- [ ] No exceptions in logs
- [ ] Navbar links present and functional
- [ ] Synthesis report saved

---

## Synthesis Report Output

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE3-MINING-MARKET-SYNTHESIS.md`

[Include results of all implementation steps]
