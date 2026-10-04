# EVE Dashboard — Project Status

**Last Updated**: 2026-10-03
History moved to `status-archive.md` (historical; may contain claims later corrected). Task files hold the detail; this file keeps recent and pending work only.

## Current state
- Works: containers healthy, /market loads live data (17 sell orders: Neon Blue Mernher 11, Neon Red 6, 0 buys), PRAGMA integrity_check ok.
- Unconfirmed: market bug root cause (database corruption is the unevidenced leading candidate), one unexplained 500 on /market/orders, Tal Beyond sync, in-game order counts.
- Blocked: adding accounts fails with invalid_scope on esi-corporations.read_structures.v1.
- In progress, uncommitted (eve-dashboard, local/improvements): print_exc shim in app/logging_config.py and market-sync logging in app/sync.py; the container runs the first-draft shim.
- Roles: Qwen planning and implementer sessions (local), Claude web read-only reviewer, Tracy approves all commits, pushes and rebuilds.

## Active Tasks
**Ready now**: Phase 2 Part 2 (OAuth validation, manual, no tokens needed).
**After the market bug closes**: Phase 3 -> Phase 3B+4 (parallel) -> 4B -> 5 -> 6

| Task | Status | File | Assigned To | Priority |
|------|--------|------|-------------|----------|
| 🐛 Market Orders Empty Page | 🟠 ACTIVE (PARTIAL, root cause unconfirmed) | 2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md | — | CRITICAL |
| 2: OAuth + ESI | ✅ Part 1 COMPLETE / Part 2 READY | 2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md | — | HIGH |
| 3: Mining + Market | 🔴 BACKLOG | 2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md | — | HIGH |
| 3B: Price Tracking | 🔴 BACKLOG | 2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md | — | HIGH |
| 4: Inventory | 🔴 BACKLOG | 2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md | — | MEDIUM |
| 4B: Logistics | 🔴 BACKLOG | 2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md | — | MEDIUM |
| 5: Efficiency | 🔴 BACKLOG | 2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md | — | MEDIUM |
| 6: Supply Chain | 🔴 BACKLOG | 2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md | — | MEDIUM |

---

## Session Log 2026-10-02 (supersedes earlier sections where they conflict)

### Market Orders Debugging Results
- Market page loads live data: 17 sell orders (Neon Blue Mernher 11, Neon Red 6), 0 buys.
- History of totals: 24 -> 18 -> 17 -> 17 (consistent with orders closing in-game; in-game count check PENDING, Tracy).
- Root cause UNCONFIRMED. Leading candidate: DB corruption cleared by the 2026-10-01 restart (no evidence; not in dashboard.log). Also one unexplained transient 500 on /market/orders on 2026-10-02 while the container showed unhealthy (stats endpoint worked); traceback not captured.
- Verified: PRAGMA integrity_check returned ok; container healthy; /market/stats matches the DB.
- esi.py raises on errors and sync.py catches them, so save_market_orders' delete-before-empty-check only runs after a successful ESI response (low risk; add fetched/saved logging, no behavior change needed).

### Open Items
- In-game order count verification: PENDING (Tracy)
- Slow filters unmeasured (client-side JS render <1ms for 24 rows; server API 7ms)
- Tal Beyond sync status unverified (no market_orders rows found)
- Sync logging regression: sync.py has 3 logger calls vs 17 print_exc, no sync lines in dashboard.log since 2026-10-01
- invalid_scope on esi-corporations.read_structures.v1 blocks adding accounts
- DISPATCH_README.md missing from repo (mentioned in status but file not found on disk)
- Expired-status code path is dead (clear_expired_orders exists but called nowhere)
- ISK-at-risk label misleading for all-sell portfolios
- Margin shows "-" because no buy orders exist to compute spread against

### Corrections to Earlier Sections
- "Blocking Issues: NONE" is wrong — invalid_scope blocks adding accounts.
- Market bug is NOT resolved. Status remains PARTIAL / UNCONFIRMED root cause.

### Next Session Start Order
1. In-game order count check for both characters
2. Tal Beyond account/sync status
3. market.js error handling for a failed /market/orders fetch
4. invalid_scope diagnosis and fix for add-account flow

## Session Log 2026-10-03

- Logging fix in progress (eve-dashboard, branch local/improvements, uncommitted): print_exc shim in app/logging_config.py and market-sync logging in app/sync.py. The container was rebuilt on 2026-10-03 with the FIRST-DRAFT shim; the revised shim (reload guard, sys.exc_info check, named logger) and the market success/failure lines are not deployed yet.
- Deploy facts: only config/ and data/ are bind-mounted; app/ is not, so code edits need a rebuild (Compose Up in VS Code, or docker compose up -d --build). PRAGMA integrity_check returned ok after the rebuild.
- print_exc is also used outside sync.py: alerts.py, agenda.py, chain/kills.py, chain/api.py, chain/tracker.py, wallet_archive.py, main.py (the shim covers these).
- dashboard.log has no entries between 2026-09-10 and 2026-10-01, so it cannot confirm Tal Beyond's recent syncs; use the dashboard card.
- Unexplained 500 on /market/orders (2026-10-02, while the container showed unhealthy): unexamined hypothesis is the sort or row-building step failing on a None value; main.py lines 759-815 have not been reviewed. Check docker logs immediately if it recurs.
- Task file restored to its original text after an overwrite; the planner's five corrections (criteria unchecked, container name, sqlite3 commands, git commit line, counts) are pending.
- agent-tasks commit e5c501c (status log) is pushed. The synthesis report is still uncommitted pending review.