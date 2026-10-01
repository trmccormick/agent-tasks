---
status: backlog
priority: CRITICAL
type: bug-fix
system_domain: MARKET_DASHBOARD
mvp_alignment: MARKET_ORDER_TRACKING
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent** (Qwen via GitHub Copilot).

Project: eve-dashboard
Task: /Users/tracymccormick/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-10/2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE:
  cd /Users/tracymccormick/Documents/git/agent-tasks
  git mv projects/eve_dashboard/tasks/backlog/2026-10/2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md \
          projects/eve_dashboard/tasks/active/2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md
  git commit -m "Move market orders bug to active - investigating empty dashboard"

Follow all steps in the Implementation Steps section.
When complete, provide synthesis report using the template at the end of this file.
```

---

## 📋 Problem Statement

**User Impact**: Market Orders page (/market) loads but displays NO orders for any character. UI is functional (filters/buttons work) but data layer is broken.

**Last Known Good State**: Market feature was fully implemented and committed (6101ad7) with all tests passing. Feature worked through entire backend-to-frontend chain.

**Current State**: 
- App running in Docker (docker-compose up -d --build)
- Market page accessible at http://localhost:8765/market
- No errors visible in browser console
- No orders displayed despite characters being synced

**Symptoms**:
- Summary cards show 0 orders, 0 ISK at risk, no margin data
- Filter controls (Active/All/Buy/Sell) show but are inert
- Character dropdown shows characters but selecting doesn't change display
- Manual refresh button doesn't populate data
- API endpoints return empty arrays

---

## 🎯 Acceptance Criteria

- [x] Database contains market orders for at least one character (verify via sqlite3)
- [x] API endpoint GET /market/orders returns non-empty JSON array
- [x] API endpoint GET /market/stats returns correct aggregated data
- [x] Market page displays at least one order in the table
- [x] Filters work (status, character, search)
- [x] All sync logs show market orders being fetched without errors
- [x] Root cause identified and documented

---

## 🏗️ Architecture Overview

Market feature consists of:
1. **Backend** (app/market.py) - Order fetching, profit calculations
2. **Database** (app/db.py) - market_orders table + CRUD functions
3. **Sync** (app/sync.py) - Integrates market sync into character sync workflow
4. **ESI** (app/esi.py) - Character orders wrapper endpoint
5. **API** (app/main.py) - Routes and view logic
6. **Frontend** (app/templates/market.html, app/static/market.js) - UI + AJAX

Data flow: Character synced → market.sync_character_market_orders() called → ESI fetches orders → save_market_orders() stores → UI queries via /market/orders API

---

## ⚠️ Architecture Gotchas

1. **ESI Scope Issue (Most Common)**: Character must have explicitly authorized `esi-markets.read_character_orders.v1` scope. Without it, ESI returns 403 silently in some cases.

2. **Sync Not Running**: Market sync is called during CHARACTER sync (sync.py line 158), not as a standalone. If character sync doesn't complete, market orders never fetched.

3. **Database Schema Mismatch**: If migration didn't run, market_orders table doesn't exist. Query fails silently, returns empty.

4. **Empty Response from ESI**: If character has no active orders, fetch_character_orders() returns [], which is correct but looks like a bug.

5. **API Response Formatting**: If database has data but API doesn't return it, the issue is in _market_orders_view() or the query.

---

## 📍 Implementation Steps

### Step 1: Verify Docker Container is Running
```bash
docker ps | grep eve-dashboard
# Should show running app container
```

### Step 2: Check Docker Logs for Errors
```bash
docker logs $(docker ps -q -f name=eve-dashboard-app) 2>&1 | tail -200 | grep -i "market\|error\|exception"
```
Look for:
- "market" references in sync
- Any "esi-markets" scope errors
- Database errors or exceptions
- "AttributeError" or "TypeError" in market.py

**If you see errors**: Note them and skip to Step 7.

### Step 3: Check Database Schema Exists
```bash
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db ".schema market_orders" | head -20
```
Expected output: SQL CREATE TABLE statement starting with "CREATE TABLE IF NOT EXISTS market_orders"

**If empty or error**: Market_orders table doesn't exist. Schema migration failed. → Go to Step 8.

### Step 4: Check if Database Has Any Market Orders
```bash
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db "SELECT COUNT(*) as order_count FROM market_orders;"
```
Expected: Should show a number > 0

**If 0 or error**: No data has been saved. → Go to Step 5.
**If > 0**: Data exists but API not returning it. → Go to Step 6.

### Step 5: Check Character Sync is Running Market Sync
```bash
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db "SELECT character_id, character_name FROM characters LIMIT 5;"
```

Get a character_id from output, then check logs for that character's sync:
```bash
docker logs $(docker ps -q -f name=eve-dashboard-app) 2>&1 | grep -i "sync.*character" | tail -10
```

Check if sync_character_market_orders is being called:
```bash
docker logs $(docker ps -q -f name=eve-dashboard-app) 2>&1 | grep "market" | tail -20
```

**If no market references**: Sync workflow isn't calling market sync. → Go to Step 8.

### Step 6: Test API Endpoint Directly
```bash
curl -s http://localhost:8765/market/orders | jq . | head -50
```

**If returns `[]`**: API query is returning empty. Check _market_orders_view() logic.
**If returns error or 500**: Exception in route. Check Docker logs.
**If returns valid JSON with orders**: Data exists, frontend issue. → Go to Step 9.

### Step 7: Check ESI Scope Authorization

Verify character has market scope authorized in ESI. This requires checking the character's token in the database:

```bash
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db \
  "SELECT character_id, character_name FROM characters WHERE character_id IN (SELECT DISTINCT character_id FROM tokens) LIMIT 1;"
```

Then manually test ESI endpoint (inside container):
```bash
docker exec eve-dashboard-app-1 python3 -c "
from app import esi, db
char = db.list_characters()[0]
token = db.get_character_token(char['character_id'])
orders = esi.character_orders(char['character_id'], token)
print(f'Character: {char[\"character_name\"]}')
print(f'Orders fetched: {len(orders) if orders else 0}')
if orders:
    print(f'Sample order: {orders[0]}')
"
```

**If error about scope/403**: ESI scope not authorized. User must re-auth with market scope.
**If returns orders**: ESI is working. Problem is save/query. → Go to Step 6.
**If returns empty list**: Character genuinely has no active orders (expected).

### Step 8: Check Database Schema and Sync Integration

Read app/db.py around line 1026 (get_all_market_orders) and line 966 (save_market_orders):
```bash
grep -n "def get_all_market_orders\|def save_market_orders" /Users/tracymccormick/Documents/git/eve-dashboard/app/db.py
```

Read app/sync.py around line 158:
```bash
sed -n '155,165p' /Users/tracymccormick/Documents/git/eve-dashboard/app/sync.py
```

Verify line 158 calls: `market.sync_character_market_orders(char, token)`

**If not present**: Add it. See Section "Fix: Add Missing Market Sync Call" below.

### Step 9: Frontend Issue - Data Exists but Not Displaying

Check app/static/market.js - does loadOrders() function exist and is it being called?

```bash
grep -n "loadOrders\|function\|fetch" /Users/tracymccormick/Documents/git/eve-dashboard/app/static/market.js | head -20
```

Check browser console in /market page for JavaScript errors.

---

## 🔧 Common Fixes

### Fix 1: Add Missing Market Sync Call
If grep of sync.py shows line 158 doesn't have market sync, add it:

File: app/sync.py around line 158 (in sync_character loop)

**Before**:
```python
        omega.sync_character_omega_state(char, token)
        structures.sync_character_structures(char, token)
        mail_mod.sync_character_mail(char, token)
```

**After**:
```python
        omega.sync_character_omega_state(char, token)
        structures.sync_character_structures(char, token)
        mail_mod.sync_character_mail(char, token)
        market.sync_character_market_orders(char, token)
```

Then rebuild: `docker compose up -d --build`

### Fix 2: Database Schema Missing
If market_orders table doesn't exist, manually run schema:

```bash
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db << 'EOF'
CREATE TABLE IF NOT EXISTS market_orders (
    order_id INTEGER PRIMARY KEY,
    character_id INTEGER NOT NULL,
    character_name TEXT,
    type_id INTEGER,
    type_name TEXT,
    location_id INTEGER,
    location_name TEXT,
    system_id INTEGER,
    system_name TEXT,
    is_buy_order BOOLEAN,
    price REAL,
    volume_total INTEGER,
    volume_remaining INTEGER,
    issued_at TEXT,
    expires_at TEXT,
    status TEXT DEFAULT 'active',
    synced_at TEXT DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(order_id, character_id)
);
CREATE INDEX IF NOT EXISTS idx_market_character ON market_orders(character_id);
CREATE INDEX IF NOT EXISTS idx_market_status ON market_orders(status);
EOF
```

Then trigger sync: Visit /dashboard, wait for sync to complete.

### Fix 3: Import Missing
Verify app/main.py imports market module:
```bash
grep "import market" /Users/tracymccormick/Documents/git/eve-dashboard/app/main.py
```

Should show: `from . import db, sso, crypto, sync, detail, homefronts, wealth, fleet, esi, names, market as market_mod`

If not present, add it to the import block around line 24.

### Fix 4: Verify ESI Wrapper Exists
```bash
grep -n "def character_orders" /Users/tracymccormick/Documents/git/eve-dashboard/app/esi.py
```

Should show line ~369. If not, check app/esi.py and ensure function exists.

---

## 📊 Investigation Flowchart

```
1. Docker logs clean? 
   ├─ NO → Errors present, read them, fix those issues first
   └─ YES → Continue

2. Market table exists?
   ├─ NO → Run Fix 2 (schema), rebuild, re-sync
   └─ YES → Continue

3. Database has orders? (COUNT > 0)
   ├─ NO → Sync never ran or ESI failing
   │       └─ Go to Step 5, check if sync_character_market_orders called
   └─ YES → Continue

4. API returns data? (curl /market/orders)
   ├─ NO → Query failing, check _market_orders_view() logic
   └─ YES → Continue

5. Frontend displays data?
   ├─ NO → JavaScript issue, check console errors
   └─ YES → SOLVED
```

---

## 📝 Files to Review

| File | Line Range | Purpose |
|------|-----------|---------|
| app/market.py | 1-50 | fetch_character_orders() function |
| app/market.py | 80-95 | sync_character_market_orders() |
| app/db.py | 119-140 | market_orders table schema |
| app/db.py | 966-1000 | save_market_orders() function |
| app/db.py | 1026-1045 | get_all_market_orders() query |
| app/sync.py | 155-165 | Character sync loop where market sync should be called |
| app/esi.py | 365-375 | character_orders() wrapper |
| app/main.py | 20-30 | imports |
| app/main.py | 759-780 | _market_orders_view() function |
| app/main.py | 782-815 | Market routes (@app.get("/market"), etc.) |
| app/templates/market.html | 1-50 | Template rendering |
| app/static/market.js | 1-50 | AJAX and data loading |

---

## 🎬 Commands Reference (Save for quick use)

```bash
# Check container status
docker ps | grep eve-dashboard

# View all logs (last 200 lines)
docker logs -n 200 $(docker ps -q -f name=eve-dashboard-app)

# Check market table
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db "SELECT COUNT(*) FROM market_orders;"

# List characters
docker exec eve-dashboard-app-1 sqlite3 /app/data/dashboard.db "SELECT character_id, character_name FROM characters LIMIT 5;"

# Test API
curl -s http://localhost:8765/market/orders | jq .
curl -s http://localhost:8765/market/stats | jq .

# Rebuild and restart
docker compose down && docker compose up -d --build

# Enter container shell
docker exec -it eve-dashboard-app-1 /bin/bash

# Check Python syntax
python3 -m py_compile app/market.py app/db.py app/sync.py app/main.py app/esi.py
```

---

## 📊 Synthesis Report Template

**COMPLETE THIS AND RETURN when task is finished.**

```markdown
## Task Completion Report: Market Orders Empty Page Bug

### Issue Diagnosis
**Root Cause Identified**: [e.g., "Character sync not calling market.sync_character_market_orders() at sync.py line 158"]

**Evidence**:
- Docker logs show: [relevant log entries]
- Database state: [e.g., "market_orders table exists with 0 rows"]
- API response: [e.g., "curl /market/orders returns []"]
- Code inspection: [what code was wrong/missing]

### Fix Applied
**Files Modified**: 
- [filename]: [line range], [brief description]

**Commits**:
- [commit hash]: [message]

**Testing Performed**:
- [Step A]: [Result]
- [Step B]: [Result]

### Verification
- [x] Database has market orders (COUNT > 0)
- [x] API endpoint /market/orders returns valid JSON with orders
- [x] /market page displays orders in table
- [x] Filters (status/character) work correctly
- [x] Manual refresh fetches new data
- [x] No errors in browser console
- [x] No errors in Docker logs

### Before/After Screenshots
[If applicable: paste before (empty) and after (with data) screenshots]

### Time Spent
- Diagnosis: [X minutes]
- Fix: [X minutes]
- Testing: [X minutes]
- Total: [X minutes]

### Notes
[Any gotchas, environmental notes, or future improvements]
```
