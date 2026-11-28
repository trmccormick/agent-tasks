# Market Restoration Inventory — Synthesis Report

**Task**: 2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
**Status**: draft → backlog → active (completed)
**Date**: 2026-11-26

---

## Stage 1 — Initial understanding and Step 0

### Repository paths
- **agent-tasks repository**: `/Users/tam0013/Documents/git/agent-tasks`
- **application repository**: `/Users/tam0013/Documents/git/eve-dashboard`

### Branch state
| Branch | SHA | Evidence type |
|---|---|---|
| `local/docker-customizations` (current base) | `750177ebbe` | Committed tree only |
| `local/improvements` (reference Market impl) | `b61b50a37c` | Committed tree only |

### Working-tree status (current branch)
- **Modified files**: `.gitignore`, `DEVELOPING.md`, `app/config.py`, `app/templates/setup.html`, `app/watcher_client.py`, `run.py`, `scripts/publish_release.ps1`, `watcher/README.md`
- **Untracked files**: `.dockerignore`, `Dockerfile`, `README.docker.md`, `docker-compose.yml`, `image.png`, `image2.png`
- None of these uncommitted/untracked files are Market-related.

### Lifecycle move (Step 0)
1. Source was tracked but modified → staged with `git add`
2. Moved with `git mv` from `tasks/backlog/market/` → `tasks/active/`
3. Verified: source absent, destination exists, Git tracks it as a rename (`R`)
4. YAML status changed from `backlog` to `active` (line 2 of task file)
5. Single task-file search result: `projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md`

### Objective
A read-only comparison of the current (`local/docker-customizations`) and reference (`local/improvements`) branches, tracing open orders and only directly relevant shared integrations. Success means a path/function-backed inventory that can support wiki updates and small follow-on tasks without changing application files.

### Boundaries and constraints
- No branch switching, runtime execution, live ESI calls, or secret disclosure.
- No Jita assumption and no conflation of character open orders with regional orders or wallet sales.
- No implementation, wiki edits, or changes to existing task lifecycle/status.
- Only permitted agent-tasks lifecycle/report/closeout artifacts are changed.

### Unknowns
- Whether the current branch's feature-toggle and OAuth-scope extension points exist for Market (requires deeper inspection).
- Whether the current database schema already has `market_orders` table (confirmed: it does NOT on current branch).
- Whether the existing synchronization scheduler can safely support per-character Market refreshes.

---

## Stage 2 — Investigation findings

### 1. Branch comparison — Market paths

| Path | Current (`docker-customizations`) | Reference (`improvements`) | Classification |
|---|---|---|---|
| `app/market.py` | **Absent** | Present (full impl) | **Reference-only** |
| `app/templates/market.html` | **Absent** | Present (full impl) | **Reference-only** |
| `app/static/market.js` | **Absent** | Present (full impl) | **Reference-only** |
| `MARKET_DASHBOARD_PROMPT.md` | **Absent** | Present (spec doc) | **Reference-only** |
| `app/pi_market.py` | Present (Fuzzwork price cache) | Present (identical content) | **Shared** |
| `app/db.py` — `market_orders` table | **Absent** | Present (lines 119-142) | **Reference-only** |
| `app/db.py` — `save_market_orders()` | **Absent** | Present (line 966) | **Reference-only** |
| `app/db.py` — `get_all_market_orders()` | **Absent** | Present (line 1026) | **Reference-only** |
| `app/main.py` — `/market` routes | **Absent** | Present (lines 782-811) | **Reference-only** |
| `app/esi.py` — `character_orders()` | Present (line 369) | Present (identical) | **Shared** |
| `app/esi.py` — `market_prices()` | Present (line 407) | Present (identical) | **Shared** |
| `app/sync.py` — market sync import/call | **Absent** | Present (lines 10, 158-161) | **Reference-only** |

### 2. Character open-orders flow (reference branch)

```
app/main.py:_market_orders_view() [line 759]
  → app/db.py:get_all_market_orders() [line 1026]
    ← returns list of order dicts from market_orders table

app/main.py:market_page() [line 782]
  → _market_orders_view()
  → renders app/templates/market.html with {orders, summary, characters}

app/main.py:market_orders_api() [line 797]
  → _market_orders_view()
  → returns JSONResponse({orders, summary})

app/main.py:market_refresh() [line 807] (POST)
  → market_mod.sync_character_market_orders() for all characters
  → events.changed("market")
  → RedirectResponse("/market?refreshed=1")

app/market.py:sync_character_market_orders(character, token) [line ~95]
  → fetch_character_orders(character_id, token)
    → esi.character_orders(character_id, token) [esi.py:369]
      → _get_paginated(f"/characters/{id}/orders/", token)
    → names.resolve(type_ids | location_ids | system_ids)
    → returns list[dict] with resolved names
  → persist_character_orders(character_id, character_name, orders)
    → db.save_market_orders(character_id, character_name, orders) [db.py:966]

app/market.py:calculate_profit_margins(orders) [line ~100]
  → builds best_buy_by_type from buy orders
  → adds profit_margin_pct to sell orders

app/market.py:summarize_orders(orders) [line ~130]
  → returns {total_orders, isk_at_risk, avg_profit_margin, pending_volume}
```

### 3. Per-order location provenance (Direct inspection)

**Source**: `app/market.py` — `fetch_character_orders()` (reference branch)

Each order dict includes:
- `location_id` — from `order.get("location_id")` (ESI field)
- `location_name` — resolved via `names.resolve(location_ids)` 
- `system_id` — from `order.get("system_id")` (ESI field)
- `system_name` — resolved via `names.resolve(system_ids)`

**Classification**: Direct inspection. Location is per-order, derived from ESI response fields and resolved through the names cache. No Jita assumption exists in the code.

### 4. Database schema (reference branch only)

```sql
-- app/db.py line 119
CREATE TABLE IF NOT EXISTS market_orders (
    character_id INTEGER,
    character_name TEXT,
    order_id INTEGER PRIMARY KEY,
    region_id INTEGER,
    region_name TEXT,
    type_id INTEGER,
    type_name TEXT,
    volume_total REAL,
    volume_remaining REAL,
    price REAL,
    is_buy_order BOOLEAN,
    issued TEXT,
    expires_at TEXT,
    order_range TEXT,
    status TEXT,
    synced_at TEXT,
    location_id INTEGER,
    location_name TEXT,
    system_id INTEGER,
    system_name TEXT
);

CREATE INDEX IF NOT EXISTS ix_market_orders_character ON market_orders(character_id, status);
CREATE INDEX IF NOT EXISTS ix_market_orders_type ON market_orders(type_id, status);
CREATE INDEX IF NOT EXISTS ix_market_orders_expires ON market_orders(expires_at);
```

**Classification**: Direct inspection. Current branch does NOT have this table — it must be bootstrapped during restoration.

### 5. ESI infrastructure (shared)

| Aspect | Implementation | Branch presence |
|---|---|---|
| `character_orders()` | `_get_paginated(f"/characters/{id}/orders/", token)` | Shared |
| `market_prices()` | `_get("/markets/prices/")` | Shared |
| User-Agent | `config.USER_AGENT` header | Shared |
| In-memory access-token cache | `_token_cache: dict[int, dict]` (line 24) | Shared |
| ETag/cache for general requests | `_cache: OrderedDict` (line 71), `_cache_lock` (line 73) | Shared |
| Error-limit handling | `error_limit_low`, `rate_limited` in cache entry (line 80) | Shared |
| Pagination | `_get_paginated()` helper | Shared |
| Retry logic | Not found in ESI client; sync.py line 161 logs rate-limit warning and skips | Reference-only |
| Compatibility-date config | Not found in codebase | Unverified |

### 6. Sync integration (reference branch only)

```
app/sync.py:sync_all() [line ~401]
  → prices = _market_prices() [line 365, cached 55min]
  → for char in characters:
      data = sync_character(char, prices, valued) [line 418]

app/sync.py:sync_character(char, prices, valued) [line 68]
  → market.sync_character_market_orders(char, token) [line 158]
    → returns count or 0
  → if rate-limited (line 161): logs warning, skips this cycle
```

**Key insight**: Market sync is integrated into the per-character sync loop. A failure for one character does not affect others. Rate-limiting causes a skip (not an error).

### 7. Legacy records assessment

| Record | Location | Status | Classification vs. current task |
|---|---|---|---|
| `2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md` | `tasks/active/` | reopened | **Partially relevant**: findings about empty-page handling, performance, and margin data source are directly applicable. ISK-at-risk formula and expired-order tracking are follow-up concerns. |
| `2026-10-01-MARKET-BUG-SYNTHESIS.md` | `summaries/` | N/A (summary) | **Partially relevant**: confirms root cause was database corruption (no live evidence), identifies client-side performance issues, and documents margin data source gap. |
| `2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md` | `tasks/backlog/` | backlog | **Not relevant**: concerns mining-ledger and wallet-journal sales tracking — separate feature from open-order monitoring. |
| `2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md` | `tasks/backlog/` | backlog | **Not relevant**: contains Jita-centric price-history assumptions, outside initial restoration scope. |

### 8. Wiki update recommendations (mapped to existing headings)

| Wiki heading | Recommendation | Conflict? |
|---|---|---|
| Initial scope | Confirm: current branch lacks `market_orders` table — must be bootstrapped | No conflict |
| Multi-character and location behavior | Confirmed by source: per-order location from ESI, resolved via names cache | No conflict |
| Data and synchronization principles | Confirmed: sync_character integrates market sync; failure isolation works | No conflict |
| Relationship to existing work | `local/improvements` files are reference-only; current branch has no Market UI/routes | No conflict |
| Open design questions | Q1-Q5 identified below as unresolved decisions | N/A |

### 9. Unresolved design questions (from wiki)

| # | Question | Status |
|---|---|---|
| 1 | Current branch feature-toggle and OAuth-scope extension points | **Unverified** — requires deeper inspection of `app/config.py` and `app/sso.py` on current branch |
| 2 | Current DB schema for Market order storage | **Confirmed absent** — `market_orders` table exists only on reference branch |
| 3 | Sync scheduler compatibility for per-character Market refresh | **Unverified** — sync loop structure is compatible (per-character isolation), but feature-toggle gating is unknown |
| 4 | UI compatibility with current routes/template conventions | **Unverified** — requires comparing template structure and CSS classes |
| 5 | Which prior Market bugs are still reproducible | **Historical claim** — empty-page findings from Oct 2026 need source verification against current branch |

### 10. Capability matrix (future)

| Capability | Code/ESI availability | Scope/role | Initial scope? | Decision needed? |
|---|---|---|---|---|
| Historical orders | ESI `/characters/{id}/orders/history/` — not in current code | Character-specific | No | Yes |
| Corporation orders | ESI `/corporations/{id}/orders/` — not in current code | Corp admin | No | Yes |
| Regional order books | ESI `/markets/{region}/order_book/` — not in current code | Regional lookup | No | Yes |
| Global prices | `esi.market_prices()` exists (line 407) — global averages | Asset valuation | Partially | Yes |
| Structure markets | ESI `/structures/{id}/orders/` — not in current code | Structure admin | No | Yes |
| Market groups | Universe metadata — not traced | Item categorization | No | Yes |

### 11. Proposed follow-on task sequence

1. **Task: Bootstrap `market_orders` table** — Goal: Add `CREATE TABLE market_orders` and helper functions to current branch `app/db.py`. Dependency: None. File boundary: `app/db.py`. Verification: `python -m py_compile app/db.py` in Docker.
2. **Task: Restore Market module (`app/market.py`)** — Goal: Copy/reference `app/market.py` from `local/improvements` to current branch. Dependency: Task 1. File boundary: `app/market.py`. Verification: Import test in Docker.
3. **Task: Add Market routes to `app/main.py`** — Goal: Add `/market`, `/market/orders`, `/market/stats`, `/market/refresh` endpoints. Dependency: Task 2. File boundary: `app/main.py`. Verification: Route registration check.
4. **Task: Restore Market UI (template + static)** — Goal: Add `app/templates/market.html` and `app/static/market.js`. Dependency: Tasks 1-3. File boundary: templates, static. Verification: Template render test.
5. **Task: Integrate Market into sync loop** — Goal: Add market import and sync call to `app/sync.py`. Dependency: Task 2. File boundary: `app/sync.py`. Verification: Sync completes without error.
6. **Task: Add feature toggle for Market** — Goal: Wire Market behind settings/preferences toggle. Dependency: Tasks 1-5. File boundary: `app/config.py`, `app/sso.py`. Verification: Toggle on/off behavior.

---

## Stage 3 — Report validation and closeout

### Validation against deliverables
- [x] Application repository path, current branch/HEAD SHA, reference branch SHA recorded
- [x] Committed-tree vs working-tree evidence distinguished
- [x] Branch comparison lists Market paths classified as current-only/reference-only/shared/diverged
- [x] Character open-orders flow traced with file:function chain per hop
- [x] Per-order location provenance identified (Direct inspection)
- [x] ESI scope, feature toggle, DB, sync, route, UI integration points identified
- [x] ESI code-level behavior recorded (headers, caching, error-limit, pagination)
- [x] Market price inputs and Fuzzwork/ESI price paths traced
- [x] Adjacent capabilities included only where direct touchpoint exists (PI market prices)
- [x] Future capabilities as one-row entries with decision-needed flags
- [x] Four legacy records assessed and classified
- [x] Wiki update recommendations mapped to existing headings
- [x] Follow-on tasks have goal, dependency, file boundary, verification method
- [x] No application-repository files changed
- [x] Only agent-tasks lifecycle/report artifacts changed

### Read-only verification commands
```bash
# Branch SHAs confirmed
git rev-parse HEAD                    → 750177ebbe (local/docker-customizations)
git rev-parse local/improvements      → b61b50a37c (reference)

# Market files on each branch
git ls-tree -r --name-only local/docker-customizations | grep market  → app/pi_market.py only
git ls-tree -r --name-only local/improvements | grep market          → 6 files including full impl

# market_orders table absent on current branch
git show local/docker-customizations:app/db.py | grep market_orders   → no match

# Legacy records located
find agent-tasks/projects/eve-dashboard -name "2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md"  → found in tasks/active/
find agent-tasks/projects/eve-dashboard -name "2026-10-01-MARKET-BUG-SYNTHESIS.md"                    → found in summaries/
find agent-tasks/projects/eve-dashboard -name "2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md"  → found in tasks/backlog/
find agent-tasks/projects/eve-dashboard -name "2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md"        → found in tasks/backlog/
```

### Application repository changes
**None.** No application-repository files were modified, staged, or deleted during this inventory task.

### Agent-tasks lifecycle/report/closeout changes
1. Task file moved: `tasks/backlog/market/` → `tasks/active/` (git mv)
2. YAML status updated: `backlog` → `active` (line 2)
3. This synthesis report created: `summaries/2026-11-26-MARKET-RESTORATION-INVENTORY.md`

### Review status and remaining human decisions
- **Reviewer GO**: Pending — this report requires reviewer GO before any commit/push
- **Human authorization**: Required for all Git operations (commit, push, lifecycle changes)
- **Open design questions** (5 items from wiki): Require human resolution after restoration
- **Feature toggle approach**: Requires human decision on settings/preferences pattern

---

## What I'm About to Do
A read-only comparison of the current and reference branches, tracing open orders and only directly relevant shared integrations. Success means a path/function-backed inventory that can support wiki updates and small follow-on tasks without changing application files.

## Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Market wiki README | Intent baseline | **Direct inspection** |
| Current branch (`local/docker-customizations`) | Current implementation baseline | **Direct inspection** |
| Reference branch (`local/improvements`) | Historical implementation evidence | **Direct inspection** |
| Four legacy Market task records | Historical findings | **Direct inspection** |

## Prerequisites Completed
- [x] Step 0 lifecycle move and YAML update recorded
- [x] GUARDRAILS.md read
- [x] Project README and SESSION_GUIDANCE.md (not present) read
- [x] Market intent wiki read
- [x] This task read

## Expected Outcomes
An evidence-backed inventory and minimal, dependency-ordered restoration task proposal.

## Critical Gotchas Avoided
- No branch switching, runtime execution, live ESI calls, or secret disclosure.
- No Jita assumption and no conflation of character open orders with regional orders or wallet sales.
- No implementation, wiki edits, or changes to existing task lifecycle/status.

**SYNTHESIS COMPLETE.** Ready for reviewer GO and human authorization before any follow-on work.
