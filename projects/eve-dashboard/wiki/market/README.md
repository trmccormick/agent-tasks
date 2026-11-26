# Market — Restoration Intent

**Status:** Design intent for restoring the existing Market feature  
**Last reviewed:** 2026-11-26

## Purpose

The Market area provides a consolidated view of the user's characters' current EVE market orders. It is an operational dashboard for monitoring open buy and sell orders, not a market-price research or trading-automation product.

The initial restoration should bring back the existing multi-character open-orders capability from the `local/improvements` implementation while fitting it into the current `local/docker-customizations` application architecture. Existing implementation details are evidence to inspect and reuse where appropriate; they are not automatically authoritative design decisions.

## Initial scope

The first restoration is limited to:

- Fetching and displaying current open market orders for characters connected to this dashboard.
- Showing which character owns each order.
- Showing the order's actual in-game location (and system where useful). Do not assume that every order is in Jita.
- Supporting a character filter and basic order-side filtering/search if the existing implementation supports them cleanly.
- Exposing Market as a user-toggleable dashboard feature through the application's existing settings/preferences pattern.
- Requesting the ESI character-order scope only for characters for which Market is enabled/authorized, using the application's existing OAuth/scope management conventions.
- Handling synchronization and display failures without treating a failed or incomplete ESI response as an authoritative empty order list.

## Explicit boundaries

The initial restoration does **not** include:

- A Market Browser or general item/region market search.
- Best-sell / best-buy comparisons across regions or trade hubs.
- Price history, trend analysis, or sales-performance tracking.
- Mining-ledger or wallet-journal market-sales ingestion.
- Trading automation, order placement, modification, or cancellation.
- A Jita-only view or Jita-only assumptions.

Those ideas may be considered separately later, but they must not be pulled into the open-orders restoration by implication or by copying broad requirements from older planning notes.

## Multi-character and location behavior

Orders are associated with the character that owns them. The view should aggregate orders across the user's eligible characters while retaining character identity on every row and allowing the user to filter by character.

Order location is data, not a global configuration assumption. A character may have orders at different stations/structures and in different systems. Display the location returned/resolved for each order; do not relabel or silently normalize all locations to Jita.

## Data and synchronization principles

- Use the current application's existing ESI client, token handling, database conventions, background synchronization, and feature-preference mechanisms.
- Keep per-character synchronization boundaries clear so one character's ESI failure does not erase or suppress another character's successfully retrieved orders.
- An empty result is meaningful only when the ESI request completed successfully. Distinguish a successful empty response from request failure before changing stored order state.
- Retain enough source identifiers to associate orders with character, order, item type, and location.
- Avoid adding price data dependencies to the basic open-orders page. Any margin or valuation feature must be separately justified and documented.

## Relationship to existing work

The prior `local/improvements` branch contains an earlier Market implementation, including `app/market.py`, `app/static/market.js`, and `app/templates/market.html`. Its `app/prefs.py` also includes a Market feature toggle. These are reference material for the restoration, not a mandate to copy the implementation unchanged.

The existing `agent-tasks` records include:
- An October 2026 Market Orders Empty Page investigation, whose findings include both relevant operational observations and unrelated/uncertain price-margin and expired-order follow-ups.
- A Phase 3 Mining and Market Sales task, which concerns mining-ledger and wallet-journal sales tracking and is not the same feature as open-order monitoring.
- A Phase 3B Price Tracking task, which contains Jita-centric price-history assumptions and is outside this restoration's initial scope.

These older records must be reconciled against current code and this intent before any are reused. Do not treat their old statuses, assumptions, or proposed implementations as current truth.

## Open design questions

The initial code inventory should identify, not decide by guesswork:

1. The current branch's existing feature-toggle and OAuth-scope extension points.
2. The current database schema and whether Market order storage already exists or has migration/bootstrap implications.
3. Whether the existing synchronization scheduler can safely support per-character Market refreshes without changing unrelated sync behavior.
4. Which parts of the prior Market UI are compatible with current routes, template conventions, and front-end structure.
5. Which previously reported Market bugs are still reproducible and directly block open-order retrieval or display.

Resolve these questions through source inspection and focused verification. Record unresolved items for human decision rather than expanding the task.

## Acceptance intent

The initial restoration is successful when a user can enable Market, authorize the required ESI scope for eligible characters, and see their current open buy and sell orders aggregated across characters with character identity and actual order location preserved. A failed ESI request must not be mistaken for a successful empty result, and Market must remain isolated from unrelated features.
