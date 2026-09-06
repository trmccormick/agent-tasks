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
- [x] Depends on Phase 4 (inventory data)
- [x] Synthesis report template provided

**READY for dispatch (after Phase 4 completes).**

---

## 🔴 Agent Dispatch Interface

```
You are **Phase 4.5 Implementation Agent** for Eve Dashboard Logistics Optimization.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md

STEP 0 — MOVE TASK FILE:
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md \
         projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md
  Then: status: backlog → status: active

CRITICAL: Save synthesis report to summaries/2026-09-04-PHASE4B-LOGISTICS-SYNTHESIS.md
```

---

# TASK: Phase 4.5 — Logistics Optimization & Hauling Scheduler

**Status**: BACKLOG (blocked on Phase 4)
**Priority**: HIGH
**Type**: feature
**Depends On**: Phase 4 (inventory) + Phase 3B (prices)

---

## Prerequisites — READ FIRST

1. **Project README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Phase 4 Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE4-INVENTORY-SYNTHESIS.md`
3. **This Task File**: Everything below

---

## Handoff from Phase 4

**What was completed in Phase 4:**

✅ Asset inventory tracking
- ESI `/characters/{id}/assets/` data fetched for all pilots
- `inventory` table populated with ore/refined materials per location
- Fleet totals aggregated across 7 mining pilots
- Storage capacity usage calculated

✅ Inventory valuation
- Each item valued at current prices (from Phase 3B)
- Total fleet ISK value calculated
- Per-location breakdown visible

**Current State:**
- Real-time inventory visible: "We have 150k units waiting to refine"
- Storage alerts working: "Station is 95% full"
- Fleet inventory totals showing
- Ready for hauling logic

**Why Phase 4.5 is next:**
Phases 3B + 4 answer: "What do we have and when should we sell?" Phase 4.5 answers: "HOW do we sell it efficiently?" Determines minimum batch sizes, hauler assignments (which of 3 support alts?), route costs, and haul profitability. Optimizes logistics workflow.

---

## Context

Determines WHAT to haul, WHEN to haul, and WHERE (Jita vs staging). User mines ore locally, refines with Neon Red, then 3 support alts haul refined materials to market. This phase optimizes that hauling workflow.

---

## Implementation Steps

### Step 0: Move Task File

```bash
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md \
       projects/eve_dashboard/tasks/active/2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md
```

### Step 1: Add Logistics Tables (app/db.py)

```python
c.execute('''
    CREATE TABLE IF NOT EXISTS haul_recommendations (
        id INTEGER PRIMARY KEY,
        generated_at DATETIME,
        item_id INTEGER,
        quantity_ready REAL,
        quantity_recommended REAL,
        recommended_hauler_id INTEGER,
        destination TEXT,
        estimated_haul_time_hours REAL,
        estimated_cost_isk REAL,
        estimated_profit_isk REAL,
        profit_per_hour REAL,
        priority TEXT,
        reason TEXT,
        FOREIGN KEY (recommended_hauler_id) REFERENCES characters(id)
    )
''')

c.execute('''
    CREATE TABLE IF NOT EXISTS haul_history (
        id INTEGER PRIMARY KEY,
        hauler_id INTEGER,
        source_location TEXT,
        destination TEXT,
        item_id INTEGER,
        quantity REAL,
        haul_duration_minutes INTEGER,
        haul_cost_isk REAL,
        sale_price_isk REAL,
        profit_isk REAL,
        completed_at DATETIME,
        FOREIGN KEY (hauler_id) REFERENCES characters(id)
    )
''')
```

### Step 2: Create Logistics Module (app/logistics.py)

```python
from app.db import get_db

def calculate_minimum_haul_batch(item_id):
    """Calculate smallest economical haul size"""
    price = get_current_price(item_id)
    volume_per_unit = get_item_volume(item_id)
    haul_cost_estimate = 5_000_000  # 5M ISK estimate
    
    # Break-even: haul_cost / (price × margin)
    margin = 0.95  # 5% fees
    min_units = haul_cost_estimate / (price * margin)
    min_volume = min_units * volume_per_unit
    
    return {
        'min_units': min_units,
        'min_volume': min_volume,
        'min_isk_value': min_units * price
    }

def recommend_haul_now():
    """Decide: haul now or wait"""
    conn = get_db()
    c = conn.cursor()
    
    # Check storage capacity
    c.execute('SELECT SUM(quantity * volume) FROM inventory')
    storage_used = c.fetchone()[0] or 0
    max_storage = 1_000_000  # m³ estimate
    
    if storage_used > max_storage * 0.9:
        return {'action': 'haul_now', 'reason': 'storage_full', 'priority': 'critical'}
    
    # Check price vs average
    c.execute('''
        SELECT current_price, avg_30_day FROM price_analysis
        WHERE current_price > avg_30_day * 1.05
    ''')
    
    if c.fetchone():
        return {'action': 'haul_now', 'reason': 'high_price', 'priority': 'high'}
    
    return {'action': 'wait', 'reason': 'low_volume_or_price', 'priority': 'low'}

def generate_haul_recommendations():
    """Create prioritized haul list"""
    conn = get_db()
    c = conn.cursor()
    
    # Get refined materials ready to sell
    c.execute('''
        SELECT item_id, item_name, SUM(quantity) as qty
        FROM inventory
        WHERE item_group = "refined" AND location_name LIKE "Mining%"
        GROUP BY item_id
        ORDER BY isk_value_at_current_price DESC
    ''')
    
    recommendations = []
    for item_id, item_name, qty in c.fetchall():
        min_batch = calculate_minimum_haul_batch(item_id)
        
        if qty > min_batch['min_volume']:
            rec = {
                'item_id': item_id,
                'item_name': item_name,
                'quantity_ready': qty,
                'quantity_recommended': min_batch['min_volume'],
                'destination': 'jita',
                'profit_isk': qty * get_current_price(item_id) * 0.95,
                'priority': 'high' if qty > min_batch['min_volume'] * 2 else 'medium'
            }
            recommendations.append(rec)
    
    return recommendations
```

### Step 3: Add Routes

```python
@app.get("/logistics")
async def logistics_dashboard():
    from app.logistics import generate_haul_recommendations
    recs = generate_haul_recommendations()
    return templates.TemplateResponse("logistics.html", {
        "request": request,
        "recommendations": recs
    })
```

### Step 4: Create Logistics Template (templates/logistics.html)

Shows recommended hauls, hauler status, route analysis.

---

## Acceptance Criteria

- [ ] Haul recommendations generated correctly
- [ ] Prioritization logic accurate (critical/high/medium/low)
- [ ] Minimum batch size calculated correctly
- [ ] Hauler assignments working
- [ ] Cost estimates reasonable
- [ ] Dashboard displays recommendations
- [ ] No exceptions in logs

---

## Synthesis Report

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE4B-LOGISTICS-SYNTHESIS.md`
