# GCC Economy Wiki — Lockdown Plan

**Date**: 2026-09-15  
**Prepared by**: Qwen (Planning Agent)  
**For**: Tracy + Gemini review before applying  

---

## Prerequisite: Cadence Contradiction Resolved ✅

**Source of truth**: `galaxy_game/app/models/game_state.rb` lines 49-60

```ruby
def seconds_per_game_day
  case speed
  when 1 then 300  # 5 min = 1 day
  when 2 then 120  # 2 min = 1 day
  when 3 then 60   # 1 min = 1 day
  when 4 then 30   # 30 sec = 1 day
  when 5 then 10   # 10 sec = 1 day
  else 60
  end
end
```

**Actual values at default speed=3: `seconds_per_game_day = 60` (1 real minute = 1 game day).**

The "86400/game_speed" formula does NOT exist in the codebase. It only appears in one stale comment (`game_loop_integration_spec.rb` line 28) that was based on an incorrect assumption. The closing handoff's claim of 60 seconds is correct.

**Wiki correction needed**: Any page claiming "8 hours per game day" or "86400/game_speed" must be corrected to the actual values above.

---

## Pages/Sections to Lock Down as Canonical

### Page: `02-currencies-and-accounts.md`
**Status**: Already corrected today (`0e4f67be`). Ready to lock down.

| Section | Status | Content to Lock |
|---------|--------|-----------------|
| GCC Identity | LOCK | "GCC is a fiat-style virtual ledger currency, not a material/commodity." LDC as authorized issuer. Non-profit UN-mandated Development Corporation status. |
| USD = GCC Scope | LOCK | "1:1 initial peg is a bootstrap design anchor, expected to decouple once GCC market volume supports independent floating value." Phases 2-3 marked exploratory/deferred. |
| Virtual Ledger Role | LOCK (with deferred marker) | Deferred settlement mechanism documented. But mark as "bootstrap-only — full redesign deferred to future ledger-architecture task." |
| GCC Minting and Distribution | LOCK | LDC authorized issuance via mining satellites + pre-seeding as second source. Implementation gap callout retained. |

### Page: `03-market-and-pricing.md`
**Status**: Already correct per prior audit. Ready to lock down.

| Section | Status | Content to Lock |
|---------|--------|-----------------|
| LDC as sole issuer | LOCK | "LDC is the sole issuer of GCC." |
| 1:1 USD peg as value-anchor | LOCK | Described as bootstrap price calibration, not conversion mechanism. |
| GCC supply backing language | DEFERRED FIX | "GCC supply backed by Luna's productive capacity" — change to "GCC supply grows through authorized LDC issuance (mining satellites), anchored to Luna's productive capacity as the economy's growth indicator." |

### Page: `04-bonds-and-financing.md`
**Status**: Needs correction before locking.

| Section | Status | Content to Lock |
|---------|--------|-----------------|
| §3.2 "GCC Mining Bonds" | NEEDS CORRECTION | Clarify bonds are **denominated in** GCC, not about mining a commodity. Collateral is the satellite asset itself, not GCC. |

### Page: `GAPS.md`
**Status**: Already corrected today (`0e4f67be`). Ready to lock down with deferred markers.

| Section | Status | Content to Lock |
|---------|--------|-----------------|
| Gap H (renamed from "Emission Schedule") | LOCK | "GCC Issuance Schedule" — terminology corrected. |
| Gap I (new) | LOCK | GCC mining recalculate_stats/mine_gcc disconnection documented as verified source-trace finding. |

---

## Pages/Sections to Mark Explicitly Deferred

### `02-currencies-and-accounts.md` — Virtual Ledger Section
**Action**: Add deferred marker at top of section:
> **DEFERRED**: Full virtual-ledger redesign is out of scope for the GCC mining P0. Current implementation supports bootstrap-only deferred settlement. Future ledger-architecture task will address obligation visibility, deficit thresholds, and AI Manager intervention rules.

### `03-market-and-pricing.md` — "GCC Supply Backed By" Language
**Action**: Change to:
> "GCC supply grows through authorized LDC issuance (mining satellites), anchored to Luna's productive capacity as the economy's growth indicator."

### `04-bonds-and-financing.md` — §3.2 "GCC Mining Bonds"
**Action**: Rewrite collateral language:
> "Long-term financing secured by mining satellite asset; creates recurring GCC demand sink."
> → "Long-term financing denominated in GCC, secured by the satellite asset itself (not GCC as collateral); creates recurring GCC demand sink."

### `05-launch-and-operational-fees.md`
**Status**: No changes needed. Already correct.

### `06-contracts-and-players.md`
**Status**: No changes needed. Already correct.

### `07-npc-economy-lifecycle.md`
**Status**: No changes needed. Already correct.

---

## New Sections to Add (Canonical)

### In `02-currencies-and-accounts.md`: GCC Mining Capacity Section
Add after "GCC Minting and Distribution":

> **GCC Mining Capacity**
> 
> Fitted processing hardware establishes **potential throughput**. LDC authorization governs **actual GCC credits issued**. Satellite capacity does NOT independently authorize currency creation.
> 
> - Zero eligible processors means zero GCC capacity.
> - No silent unit-rate fallback; missing capacity metadata yields zero plus validation/diagnostic behavior.
> - The `mine_gcc` method (cryptocurrency_mining.rb) is the single canonical calculation, aggregating fitted computer units via MiningUnitAdapter.
> - If `current_mining_rate_gcc_per_hour` is displayed as a stat, it must call the same method — not maintain a second parallel implementation.
> 
> **Time Model**: Game days advance at `seconds_per_game_day` intervals based on GameState speed (see `game_state.rb`). At default speed=3: 60 real seconds = 1 game day. The simulation tick interval is NOT tied to the `0.18` per-operation conversion constant used in the mining formula.

### In `GAPS.md`: Add Deferred Section
Add after Gap I:

> **Gap J — Deferred: Exchange-Rate Architecture Reconciliation**
> Two disconnected exchange-rate systems exist (in-memory ExchangeRateService vs. DB-backed ExchangeRate model). This is a design gap, not a policy conflict. Resolution deferred to future ledger-architecture task.
> 
> **Gap K — Deferred: Virtual Ledger Full Redesign**
> VirtualLedgerService.exchange_rate_to_gcc returns hardcoded 100.0 (test artifact). Full virtual-ledger obligation visibility, deficit thresholds, and AI Manager intervention rules deferred to dedicated future task.

---

## Summary Table

| Page/Section | Action | Reason |
|-------------|--------|--------|
| `02-currencies-and-accounts.md` §1 GCC Identity | LOCK ✅ | Already corrected today |
| `02-currencies-and-accounts.md` §3 USD=GCC Scope | LOCK ✅ | Already corrected today |
| `02-currencies-and-accounts.md` §5 Virtual Ledger | LOCK + DEFERRED MARKER | Bootstrap-only; full redesign deferred |
| `02-currencies-and-accounts.md` New: GCC Mining Capacity | ADD (LOCK) | Canonical capacity model documented |
| `03-market-and-pricing.md` LDC sole issuer | LOCK ✅ | Already correct |
| `03-market-and-pricing.md` "GCC supply backed by" | CORRECT | Change to "grows through authorized LDC issuance" |
| `04-bonds-and-financing.md` §3.2 GCC Mining Bonds | CORRECT | Clarify denominated in GCC, not about mining commodity |
| `GAPS.md` Gap H (renamed) | LOCK ✅ | Already corrected today |
| `GAPS.md` Gap I (new) | LOCK ✅ | Already corrected today |
| `GAPS.md` Gap J + K (new deferred) | ADD DEFERRED MARKERS | Exchange-rate + virtual-ledger deferred |
| `05-07` pages | NO CHANGE ✅ | Already correct |

---

## What Stays Explicitly Deferred (Not in This Wiki Lockdown)

These are NOT part of the wiki lockdown — they should be marked as exploratory/deferred elsewhere:

1. **Virtual ledger full redesign** — obligation visibility, deficit thresholds, AI Manager intervention rules
2. **Exchange-rate architecture reconciliation** — two disconnected systems need consolidation
3. **Player fitting system** — not yet implemented
4. **Wormhole-stabilization satellites** — exploratory design
5. **Server-farm GCC hosting** — exploratory design
6. **USD/GCC decoupling phases 2-3** — already marked exploratory/deferred in wiki

---

## Cadence Correction (Applies to All Pages)

Any page claiming "8 hours per game day" or "86400/game_speed" must be corrected:

| Speed | seconds_per_game_day | Real time per game day |
|-------|---------------------|----------------------|
| 1 | 300 | 5 minutes |
| 2 | 120 | 2 minutes |
| 3 (default) | 60 | 1 minute |
| 4 | 30 | 30 seconds |
| 5 | 10 | 10 seconds |

---

**Status**: AWAITING Tracy + Gemini review before applying. No changes applied yet.
