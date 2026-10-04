# Market Bug Synthesis Report

**Task**: 2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md
**Updated**: 2026-10-03
**Status**: PARTIAL — page loads live data; root cause UNCONFIRMED
**Evidence labels**: [cmd] command output seen, [code] code read, [tracy] observed by Tracy, [agent] reported by an agent session

## 1. Root cause: UNCONFIRMED
- Leading candidate: database corruption cleared by the 2026-10-01 restart. No evidence: nothing from before the restart was logged, and dashboard.log has no sync lines after 2026-10-01 18:57.
- One unexplained 500 on /market/orders on 2026-10-02 while the container showed unhealthy and /market/stats still worked. Traceback not captured. [agent]
- Latent risk reviewed, not shown to have fired: save_market_orders deletes a character's rows before its empty-list check. esi._get raises on HTTP errors (raise_for_status), RateLimited is caught in sync.py, so the delete only runs after a successful ESI response; an empty result then correctly clears rows. [code]

## 2. Current data
- DB: Neon Blue Mernher 11, Neon Red 6, all sell orders, synced 2026-10-02 14:34 UTC. [cmd]
- /market/stats 2026-10-02: orders 17, buy 0, sell 17, isk_at_risk 0, pending_volume 9998073, avg_profit_margin null, expiring_soon 0. Matches the DB. [cmd]
- Count history 24 (18 + 6, 1 buy) -> 18 -> 17 -> 17. [agent] Cause unverified: closing orders is assumed, in-game counts not checked.
- PRAGMA integrity_check returned ok on 2026-10-02 and 2026-10-03; container healthy. [cmd] This does not show the state before the restart.

## 3. Performance and filters
- /market/orders?status=all 7 ms, /market HTML 9 ms. [agent]
- market.js: one fetch on load plus a 60 s poll; filter, search and sort re-render from cached data with no further requests; search has no debounce. [code]
- Tracy: filters work but respond slowly. Cause unknown: server and network ruled out, client render of 24 rows estimated well under 1 ms. [tracy]
- Buy/Sell tab behavior NOT verified. With 0 buy orders, Buy must show an empty table and Sell all 17 rows.

## 4. Margin and ESI
- Margin does not call /markets/{region_id}/orders (character's own buy orders plus db.get_avg_buy_prices). [code] Source table for the fallback not confirmed.
- Margin shows "-" because all current orders are sells with no buy history. Whether wallet_transactions could supply history is open.
- ISK at risk counts buy orders only, so 0 is correct for an all-sell portfolio but the label misleads. Server and client summarize formulas match. [code]
- /market/stats is not used by market.js. [code]

## 5. Acceptance criteria
| Criterion | Status | Evidence |
|---|---|---|
| DB contains orders | PASS | 17 rows [cmd] |
| GET /market/orders non-empty | PASS | curl returned orders 2026-10-02 [agent] |
| GET /market/stats correct | PASS | matches DB [cmd] |
| Market page shows orders | PASS | [tracy] |
| Filters work | PARTIAL | work but slow [tracy]; Buy/Sell unverified |
| Sync logs show market fetch | BLOCKED | no sync lines in dashboard.log since 2026-10-01; logging fix pending |
| Root cause documented | OPEN | unconfirmed |
| In-game counts match | PENDING | Tracy to check |
| No 500 on /market/orders | OPEN | one unexplained 500 |

## 6. Known gaps and follow-ups
- Expired status is dead code: status is always "active" and clear_expired_orders is never called.
- Logging regression: sync.py has 3 logger calls vs 17 traceback.print_exc, and no sync lines reach dashboard.log. Fix in progress (uncommitted).
- Tal Beyond: no market rows and no verified recent sync.
- invalid_scope on esi-corporations.read_structures.v1 blocks adding accounts.
- Review the /market/orders view in main.py (about lines 759-815) for 500 causes.
