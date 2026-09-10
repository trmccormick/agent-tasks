# Research: SettlementFees vs TransactionFee — Fee Mechanism History

**Date:** 2026-09-08  
**Researcher:** Planning Agent (Qwen)  
**Purpose:** Determine why SettlementFees disappeared, whether TransactionFee replaced it, and what's actually live for fee calculation

---

## Executive Summary

**Neither fee mechanism is live.** Two separate attempts at per-settlement/market fees were made, neither of which reached production:

1. **`Market::TransactionFee`** (Jan 12, 2026) — Added to main but has **zero callers**. Model + table exist; nothing calls it.
2. **`SettlementFees` concern** (Aug 10, 2026) — Added to `market-fee-hold` branch but **never merged to main**. Lives only on that branch.

The RSpec run from Sept 5 that showed `SettlementFees#apply_default_fees!` in logs was running against a **different database/state** (likely the Docker container with uncommitted changes or a different branch). The live codebase has neither mechanism wired up.

---

## Timeline

### Jan 12, 2026 — TransactionFee Created (on main)
- **Commit:** `c60d658f` / `a12aec40` — "Implement NPC Insurance Corporations and Player Contract Security System"
- **File:** `galaxy_game/app/models/market/transaction_fee.rb`
- **Migration:** `20250214213326_create_market_transaction_fees.rb` → table `market_transaction_fees` exists in schema
- **Model structure:** `fee_type` (percentage/fixed), `percentage`, `fixed_amount`
- **Comment in code:** `"# If you want a separate model"` — suggests this was a draft/proposal, not finalized
- **Callers:** **ZERO** — grep across `app/` finds only the class definition itself

### Aug 10, 2026 — SettlementFees Created (NOT on main)
- **Commit:** `7db7566c` — "feat: per-location market fee management for AI Manager"
- **Branch:** `market-fee-hold` only — never merged to main
- **File:** `galaxy_game/app/models/concerns/settlement_fees.rb` (120 lines)
- **Pattern:** Per-settlement fees stored in `operational_data['fees']` JSONB
- **Methods:** `broker_fee_type/value`, `transaction_fee_type/value`, `order_duration_min/max`, `apply_default_fees!`
- **Included by:** Both `BaseSettlement` and `OrbitalSettlement` (on that branch)
- **Also added:** `LogisticsCoordinator#set_location_fees/get_location_fees/apply_default_fees`, `UniversalDockingService#calculate_docking_fees/process_docking_fees`
- **Tests:** 30 examples in `per_location_fees_spec.rb` — all passing

### Why SettlementFees Never Reached Main
The branch name `market-fee-hold` suggests it was intentionally held. The commit message says "per-location market fee management for AI Manager" — this was the more complete design (per-settlement, per-location fees with broker + transaction types). TransactionFee was a simpler draft that never got wired up.

### Sept 5 RSpec Logs Showing SettlementFees
The RSpec run from Sept 5 showed `SettlementFees#apply_default_fees!` in logs. This is **not** from the main branch codebase — it must have been running against:
- The `market-fee-hold` branch directly, OR
- A Docker container with uncommitted changes from that branch

The live `main` branch has neither mechanism wired up.

---

## Current State of Both Mechanisms

### Market::TransactionFee (on main, but dead)
| Aspect | Status |
|--------|--------|
| Model file | ✅ Exists at `app/models/market/transaction_fee.rb` |
| Database table | ✅ `market_transaction_fees` in schema |
| Migration | ✅ `20250214213326_create_market_transaction_fees.rb` |
| **Live callers** | ❌ **ZERO** — never instantiated or called |
| Comment | `"# If you want a separate model"` — draft/proposal language |

### SettlementFees (on market-fee-hold branch only)
| Aspect | Status |
|--------|--------|
| Concern file | ✅ Exists on `market-fee-hold` branch only |
| Included by models | ✅ BaseSettlement + OrbitalSettlement (on that branch) |
| Storage pattern | ✅ `operational_data['fees']` JSONB (consistent with existing patterns) |
| **Merged to main** | ❌ **Never** — branch name "hold" suggests intentional hold |
| Tests | ✅ 30 examples passing on that branch |

---

## TransitFeeService — Third Fee Mechanism?

A separate `TransitFeeService` was also found (attached in handoff):
- Charges GCC per transit ton (10 GCC/ton)
- Distributes dividends to founding corporations
- Uses in-memory state (`system[:transit_fees_enabled]`) — no database table
- **Status:** Likely draft/proposal — no callers confirmed

---

## What This Means for Gemini Phase 2

### Good News
The `SettlementFees` design on `market-fee-hold` is the **more complete** mechanism:
- Per-settlement, per-location fees (not global)
- Broker fee + transaction fee types (percentage/fixed)
- Order duration constraints
- Stored in `operational_data['fees']` (consistent with existing patterns)
- 30 passing tests

### Bad News
Neither mechanism is live. Gemini Phase 2's "Phase 1 — Parity Fix" claim about `SettlementFees` was based on the RSpec logs from Sept 5, which were running against a different state. **There is no parity bug** — there's a gap: neither fee mechanism is wired up in production code.

### Recommendation
The `market-fee-hold` branch has the better design. If Gemini Phase 2 needs to work with fees:
1. Merge `market-fee-hold` into main (or cherry-pick `7db7566c`)
2. Wire `SettlementFees` into the pricing pipeline (currently only included by settlement models, not called by pricing services)
3. Decide whether `Market::TransactionFee` is needed at all (it's a simpler model with no callers)

---

## Key Code References

| Component | Location | Branch |
|-----------|----------|--------|
| SettlementFees concern | `app/models/concerns/settlement_fees.rb` | market-fee-hold only |
| Market::TransactionFee | `app/models/market/transaction_fee.rb` | main |
| TransitFeeService | `app/services/ai_manager/transit_fee_service.rb` | main (likely draft) |
| Migration | `db/migrate/20250214213326_create_market_transaction_fees.rb` | main |
| Schema table | `market_transaction_fees` in `schema.rb` | main |

---

**Status:** Research complete. Neither fee mechanism is live in production code. The `market-fee-hold` branch has the more complete design but was never merged.
