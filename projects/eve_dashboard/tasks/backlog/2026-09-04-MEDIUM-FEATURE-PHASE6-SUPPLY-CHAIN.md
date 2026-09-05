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
- [x] Depends on Phase 5 (efficiency data)
- [x] Synthesis report template provided

**READY for dispatch (after Phase 5 completes).**

---

## 🔴 Agent Dispatch Interface

```
You are **Phase 6 Implementation Agent** for Eve Dashboard Supply Chain Analysis.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md

STEP 0 — MOVE TASK FILE:
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md \
         projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md
  Then: status: backlog → status: active

CRITICAL: Save synthesis report to summaries/2026-09-04-PHASE6-SUPPLY-CHAIN-SYNTHESIS.md
```

---

# TASK: Phase 6 — Supply Chain & Profitability Analysis

**Status**: BACKLOG (blocked on Phase 5)
**Priority**: MEDIUM
**Type**: feature
**Depends On**: Phase 5 (efficiency data) + all prior phases

---

## Prerequisites — READ FIRST

1. **Project README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Phase 5 Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE5-EFFICIENCY-SYNTHESIS.md`
3. **This Task File**: Everything below

---

## Handoff from Phase 5

**What was completed in Phase 5:**

✅ Production metrics tracking
- `production_metrics` table stores daily ISK/hour per pilot
- `fleet_metrics` table aggregates fleet totals
- Leaderboard generated: Who's most efficient?
- 7-day moving average calculated

✅ PLEX fund countdown
- `days_to_plex_fund` calculated based on current earnings rate
- Progress % toward PLEX purchase target shown
- Confidence level assessed (moderate/low based on consistency)

✅ Bottleneck detection
- ISK/hour variance detected across pilots
- Uptime tracking shows who's inactive
- Efficiency trends visible

**Current State:**
- Efficiency dashboard operational
- Leaderboard showing pilot performance
- PLEX countdown visible ("X days at current rate")
- Ready for strategic decisions

**Why Phase 6 is next:**
Phases 2-5 are operational ("What are we doing?"). Phase 6 is strategic ("Are we doing the RIGHT thing?"). Determines: Which ore type is most profitable NOW? What's the fee breakdown? Should we refine locally or sell raw ore? Which materials have best margins? Combined phases 2-6 = full operational intelligence dashboard for multi-account mining operation.

---

## Context

Ore profitability ranking (which ore type gives best ISK/hour?). Fee breakdown (broker + market + taxes). Sell-vs-refine decision tool. Strategic mining recommendations.

---

## Implementation Steps

### Step 0: Move Task File

```bash
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md \
       projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md
```

### Step 1: Add Profitability Tables (app/db.py)

```python
c.execute('''
    CREATE TABLE IF NOT EXISTS ore_types (
        item_id INTEGER PRIMARY KEY,
        item_name TEXT,
        mineral_output TEXT,
        base_yield REAL,
        density REAL
    )
''')

c.execute('''
    CREATE TABLE IF NOT EXISTS refining_yields (
        id INTEGER PRIMARY KEY,
        character_id INTEGER,
        ore_id INTEGER,
        refined_item_id INTEGER,
        refined_item_name TEXT,
        yield_percentage REAL,
        FOREIGN KEY (character_id) REFERENCES characters(id)
    )
''')

c.execute('''
    CREATE TABLE IF NOT EXISTS profitability_analysis (
        id INTEGER PRIMARY KEY,
        ore_id INTEGER,
        date DATE,
        ore_price_per_unit REAL,
        refined_material_1_name TEXT,
        refined_material_1_yield REAL,
        refined_material_1_price REAL,
        total_refined_value REAL,
        broker_fees_percent REAL,
        market_fees_percent REAL,
        net_profit_per_unit REAL,
        isk_per_hour_estimate REAL,
        recommendation TEXT,
        FOREIGN KEY (ore_id) REFERENCES ore_types(item_id)
    )
''')
```

### Step 2: Create Supply Chain Module (app/supply_chain.py)

```python
from app.db import get_db

def calculate_refining_yields(character_id, ore_id):
    """Get refined material yields for character's skill level"""
    conn = get_db()
    c = conn.cursor()
    
    # Get character's refining skill
    c.execute('SELECT skill_level FROM refining_efficiency WHERE character_id = ?', (character_id,))
    result = c.fetchone()
    skill_level = result[0] if result else 1
    
    # Base yield for ore
    c.execute('SELECT base_yield, mineral_output FROM ore_types WHERE item_id = ?', (ore_id,))
    result = c.fetchone()
    if not result:
        return []
    
    base_yield, mineral_output = result
    
    # Apply skill efficiency (51% + skill_level%)
    efficiency = 0.51 + (skill_level * 0.02)  # 51% base + 2% per skill level
    actual_yield = base_yield * efficiency
    
    return {
        'efficiency': efficiency,
        'actual_yield': actual_yield
    }

def calculate_ore_profitability(ore_id, date):
    """Profitability of mining this ore type"""
    conn = get_db()
    c = conn.cursor()
    
    # Get ore price
    c.execute('SELECT current_price FROM price_analysis WHERE item_id = ?', (ore_id,))
    result = c.fetchone()
    ore_price = result[0] if result else 0
    
    # Get ore info
    c.execute('SELECT item_name, base_yield FROM ore_types WHERE item_id = ?', (ore_id,))
    result = c.fetchone()
    if not result:
        return None
    
    ore_name, base_yield = result
    
    # Calculate refined value (assume 52% refining efficiency average)
    efficiency = 0.52
    refined_yield = base_yield * efficiency
    
    # Get refined material prices (simplified)
    # In reality, this varies by ore type
    # Clear Icicle example:
    c.execute('SELECT current_price FROM price_analysis WHERE item_name = "Clear Icicle"')
    result = c.fetchone()
    refined_price = result[0] if result else 0
    
    refined_value = refined_yield * refined_price
    
    # Calculate fees (Jita standard)
    broker_fee = 0.005  # 0.5%
    market_fee = 0.01   # 1%
    total_fees = (broker_fee + market_fee)
    
    net_profit = refined_value * (1 - total_fees)
    profit_per_hour = net_profit / 1  # Simplified (1 hour per 1000 m³)
    
    # Determine recommendation
    if profit_per_hour > 100_000_000:
        recommendation = 'mine'
    elif profit_per_hour > 50_000_000:
        recommendation = 'medium'
    else:
        recommendation = 'low'
    
    c.execute('''
        INSERT OR REPLACE INTO profitability_analysis
        (ore_id, date, ore_price_per_unit, refined_material_1_name, refined_material_1_yield,
         refined_material_1_price, total_refined_value, broker_fees_percent, market_fees_percent,
         net_profit_per_unit, isk_per_hour_estimate, recommendation)
        VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
    ''', (ore_id, date, ore_price, 'Clear Icicle', refined_yield, refined_price, refined_value,
          broker_fee * 100, market_fee * 100, net_profit, profit_per_hour, recommendation))
    
    return {
        'ore': ore_name,
        'profitability': profit_per_hour,
        'recommendation': recommendation
    }

def compare_ore_profitability(date):
    """Rank all ore types by profitability"""
    conn = get_db()
    c = conn.cursor()
    
    c.execute('''
        SELECT ore_id, refined_material_1_name, isk_per_hour_estimate, recommendation
        FROM profitability_analysis WHERE date = ?
        ORDER BY isk_per_hour_estimate DESC
    ''', (date,))
    
    return c.fetchall()

def recommend_mining_strategy():
    """Which ore type to focus on NOW"""
    conn = get_db()
    c = conn.cursor()
    
    today = datetime.now().strftime('%Y-%m-%d')
    
    c.execute('''
        SELECT ore_id, refined_material_1_name, isk_per_hour_estimate
        FROM profitability_analysis WHERE date = ? AND recommendation = "mine"
        ORDER BY isk_per_hour_estimate DESC
        LIMIT 1
    ''', (today,))
    
    result = c.fetchone()
    if result:
        return {
            'ore': result[1],
            'isk_per_hour': result[2],
            'reason': 'Highest profitability today'
        }
    
    return {'ore': 'Condensed Ice', 'isk_per_hour': 0, 'reason': 'Default (no data)'}
```

### Step 3: Add Routes

```python
@app.get("/supply-chain")
async def supply_chain_dashboard():
    from app.supply_chain import compare_ore_profitability, recommend_mining_strategy
    today = datetime.now().strftime('%Y-%m-%d')
    ore_ranking = compare_ore_profitability(today)
    strategy = recommend_mining_strategy()
    return templates.TemplateResponse("supply_chain.html", {
        "request": request,
        "ore_ranking": ore_ranking,
        "strategy": strategy
    })
```

### Step 4: Create Supply Chain Template (templates/supply_chain.html)

Shows ore profitability leaderboard, fee breakdown, recommendations.

---

## Acceptance Criteria

- [ ] Ore profitability ranking calculated correctly
- [ ] Refining yield calculations accurate (52% efficiency)
- [ ] Fee breakdown correct (Jita standard)
- [ ] Recommendation logic working
- [ ] Top ore type identified
- [ ] No exceptions in logs

---

## Synthesis Report

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-PHASE6-SUPPLY-CHAIN-SYNTHESIS.md`
