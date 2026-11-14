---
status: backlog
priority: MEDIUM
type: feature
system_domain: EVE_ONLINE_INTEGRATION
mvp_alignment: PLEX_FUND_OPTIMIZATION
local_worker_safe: true
---

## 🔴 Task Readiness Checklist

- [x] Dispatch Interface complete
- [x] Depends on Phase 4.5 (logistics)
- [x] Synthesis report template provided

**READY for dispatch (after Phase 4.5 completes).**

---

## 🔴 Agent Dispatch Interface

```
You are **Phase 5 Implementation Agent** for Eve Dashboard Production Efficiency.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md

STEP 0 — MOVE TASK FILE:
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md \
         projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md
  Then: status: backlog → status: active

CRITICAL: Save synthesis report to summaries/2026-09-04-PHASE5-EFFICIENCY-SYNTHESIS.md
```

---

# TASK: Phase 5 — Production Efficiency Metrics

**Status**: BACKLOG (blocked on Phase 4.5)
**Priority**: MEDIUM
**Type**: feature
**Depends On**: Phase 3 (mining data) + Phase 4.5 (logistics costs)

---

## Prerequisites — READ FIRST

1. **Project README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Phase 4.5 Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE4B-LOGISTICS-SYNTHESIS.md`
3. **This Task File**: Everything below

---

## Handoff from Phase 4.5

**What was completed in Phase 4.5:**

✅ Logistics optimization
- `haul_recommendations` table generates prioritized haul list
- Minimum batch sizes calculated per item type
- Hauler assignments optimized (load balancing across 3 support alts)
- Cost estimates: broker fees, market fees, route expenses
- Profit-per-hour calculated per haul

✅ Hauling workflow
- Critical/High/Medium/Low priority hauls generated
- Storage-full alerts trigger immediate hauling
- High-price alerts trigger hauling when profitable
- Historical tracking in `haul_history` table

**Current State:**
- Haul recommendations dashboard operational
- Logistics costs quantified
- Hauler assignments working
- Ready for efficiency metrics

**Why Phase 5 is next:**
Phases 2-4.5 optimize individual activities (mining, pricing, inventory, hauling). Phase 5 shows the big picture: ISK/hour per pilot and fleet-wide, PLEX fund countdown (days until 3 accounts upgraded?), and bottleneck detection. Answers: "Is the operation efficient? Who's underperforming?"

---

## Context

Calculate ISK/hour per pilot and fleet-wide. Show PLEX countdown (days until fund ready at current rate). Detect bottlenecks limiting fleet output.

---

## Implementation Steps

### Step 0: Move Task File

```bash
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md \
       projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md
```

### Step 1: Add Efficiency Tables (app/db.py)

```python
c.execute('''
    CREATE TABLE IF NOT EXISTS production_metrics (
        id INTEGER PRIMARY KEY,
        character_id INTEGER,
        date DATE,
        total_ore_mined REAL,
        total_isk_generated REAL,
        sessions_count INTEGER,
        isk_per_hour REAL,
        uptime_percent REAL,
        FOREIGN KEY (character_id) REFERENCES characters(id)
    )
''')

c.execute('''
    CREATE TABLE IF NOT EXISTS fleet_metrics (
        id INTEGER PRIMARY KEY,
        date DATE,
        fleet_isk_total REAL,
        fleet_isk_per_hour REAL,
        mining_uptime_percent REAL,
        plex_fund_progress_percent REAL,
        days_to_plex_fund REAL
    )
''')
```

### Step 2: Create Efficiency Module (app/efficiency.py)

```python
from datetime import datetime, timedelta
from app.db import get_db

def calculate_daily_metrics(character_id, date):
    """ISK/hour for one character, one day"""
    conn = get_db()
    c = conn.cursor()
    
    # Get mining for this character this day
    c.execute('''
        SELECT SUM(quantity) as total_qty FROM mining_ledger
        WHERE character_id = ? AND date = ?
    ''', (character_id, date))
    
    result = c.fetchone()
    total_qty = result[0] if result else 0
    
    # Get market sales for this character this day
    c.execute('''
        SELECT SUM(total_isk) as total_isk FROM market_sales
        WHERE character_id = ? AND date = ?
    ''', (character_id, date))
    
    result = c.fetchone()
    total_isk = result[0] if result else 0
    
    # Estimate: 8 hours mining + hauling per day
    isk_per_hour = total_isk / 8 if total_isk > 0 else 0
    
    c.execute('''
        INSERT OR REPLACE INTO production_metrics
        (character_id, date, total_ore_mined, total_isk_generated, isk_per_hour)
        VALUES (?, ?, ?, ?, ?)
    ''', (character_id, date, total_qty, total_isk, isk_per_hour))
    
    conn.commit()

def calculate_fleet_metrics(date):
    """Fleet totals for one day"""
    conn = get_db()
    c = conn.cursor()
    
    # Sum all character metrics
    c.execute('''
        SELECT SUM(total_isk_generated), SUM(isk_per_hour) FROM production_metrics
        WHERE date = ?
    ''', (date,))
    
    result = c.fetchone()
    total_isk, avg_isk_per_hour = result if result else (0, 0)
    
    # Project PLEX fund
    plex_cost_isk = 2_500_000_000  # ~2.5B ISK for 1 PLEX
    daily_isk = total_isk
    days_to_plex = plex_cost_isk / daily_isk if daily_isk > 0 else 999
    progress = min(100, (daily_isk / plex_cost_isk) * 100)
    
    c.execute('''
        INSERT OR REPLACE INTO fleet_metrics
        (date, fleet_isk_total, fleet_isk_per_hour, plex_fund_progress_percent, days_to_plex_fund)
        VALUES (?, ?, ?, ?, ?)
    ''', (date, total_isk, avg_isk_per_hour, progress, days_to_plex))
    
    conn.commit()

def get_leaderboard(date):
    """ISK/hour ranking by pilot"""
    conn = get_db()
    c = conn.cursor()
    
    c.execute('''
        SELECT c.name, pm.isk_per_hour FROM production_metrics pm
        JOIN characters c ON pm.character_id = c.id
        WHERE pm.date = ?
        ORDER BY pm.isk_per_hour DESC
    ''', (date,))
    
    return c.fetchall()

def project_plex_timeline():
    """Calculate days until PLEX fund ready"""
    conn = get_db()
    c = conn.cursor()
    
    # 7-day average
    cutoff = (datetime.now() - timedelta(days=7)).strftime('%Y-%m-%d')
    c.execute('''
        SELECT AVG(fleet_isk_total), AVG(days_to_plex_fund)
        FROM fleet_metrics WHERE date >= ?
    ''', (cutoff,))
    
    result = c.fetchone()
    avg_daily_isk, avg_days = result if result else (0, 999)
    
    return {
        'avg_daily_isk': avg_daily_isk,
        'days_to_plex': avg_days,
        'confidence': 'moderate' if avg_daily_isk > 100_000_000 else 'low'
    }
```

### Step 3: Add Routes

```python
@app.get("/efficiency")
async def efficiency_dashboard():
    from app.efficiency import get_leaderboard, project_plex_timeline
    today = datetime.now().strftime('%Y-%m-%d')
    leaderboard = get_leaderboard(today)
    timeline = project_plex_timeline()
    return templates.TemplateResponse("efficiency.html", {
        "request": request,
        "leaderboard": leaderboard,
        "plex_timeline": timeline
    })
```

### Step 4: Create Efficiency Template (templates/efficiency.html)

Shows leaderboard, PLEX countdown, trend chart.

---

## Acceptance Criteria

- [ ] Daily metrics calculated per character
- [ ] Fleet totals aggregated correctly
- [ ] Leaderboard ranked by ISK/hour
- [ ] PLEX countdown calculated
- [ ] Trend chart shows 7-day progression
- [ ] No exceptions in logs

---

## Synthesis Report

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE5-EFFICIENCY-SYNTHESIS.md`
