# Economic Documentation Audit & Wiki Completion Plan

**Date**: 2026-09-09  
**Author**: Planning Agent  
**Status**: Complete — deliverables in `docs/wiki_reorganization/economy/`

---

## Executive Summary

Conducted comprehensive audit of Galaxy Game economic/financial documentation. Found that the wiki reorganization has **Phase 1–4 discovery artifacts** but **zero actual economy reference pages written**. All economic content still lives in `docs/architecture/economy/`. Produced 4 new wiki pages and identified 8 doc/code contradictions plus 8 missing topics.

---

## Section 1: Current Wiki Structure

The `docs/wiki_reorganization/` directory contains Phase 1–4 discovery artifacts from a documentation archaeology scan (started 2026-07-16):

| Path | Contents | Status |
|------|----------|--------|
| `README.md` | Phase 1 Discovery — 368+ files cataloged | ✅ Complete |
| `analysis/` | ARCHITECTURE_RECONSTRUCTION, CONFLICT_REPORT, CORE_CONCEPT_MAP, TERMINOLOGY_MAP | ✅ Complete |
| `inventory/` | DOCUMENT_AUTHORITY_MAP, DOCUMENT_INVENTORY | ✅ Complete |
| `proposals/` | PROPOSED_DOCUMENTATION_STRUCTURE (13-folder proposal) | ✅ Complete |
| `phase2_alignment/` | WIKI_2_STRUCTURE_PROPOSAL, DEVELOPMENT_PHASE_MAPPING, ARCHITECTURE_GAPS | ✅ Complete |
| `phase3_alignment/` | PHASE3_CANONICAL_ALIGNMENT_REPORT, RESOLVED_CONFLICTS, MISSING_DOCUMENTATION_REPORT (17 files) | ✅ Complete |
| `phase4/` | WIKI_SITE_MAP, MISSING_WIKI_PAGES, CANONICAL_DOCUMENT_INDEX, DOCUMENT_RELOCATION_PLAN (9 files) | ✅ Complete |

**Key finding**: Phase 4 site map defines an Economy section with pages (ECONOMY_OVERVIEW, CURRENCY, MARKETS, TRADING, NPC_ECONOMY, PLAYER_ECONOMY, CONTRACTS, SUPPLY_AND_DEMAND, IMPORT_EXPORT, PRICING, ECONOMIC_PHILOSOPHY) — **none written yet**. All economic content still in `docs/architecture/economy/`.

---

## Section 2: Proposed Additions & Mapping

### New Pages Created (4 total)

| Page | Location | Source |
|------|----------|--------|
| `CURRENCIES_AND_ACCOUNTS.md` | NEW — core reference | Code: Currency, Account models + CURRENCY_AND_EXCHANGE.md + financial_system.md |
| `BONDS_AND_FINANCING.md` | NEW — missing entirely | Code: Bond model + integration test patterns |
| `LAUNCH_PAYMENT_MODEL.md` | NEW — missing entirely | Code: LaunchPaymentService + integration test |
| `ECONOMY_OVERVIEW.md` | NEW — entry point | Synthesis of all economy docs |

### Old → New Mapping

**Move (canonical, no changes):**
- `docs/architecture/economy/PRICE_DISCOVERY_LIFECYCLE.md` → `wiki_reorganization/economy/PRICE_DISCOVERY_LIFECYCLE.md`
- `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` → `wiki_reorganization/economy/FISCAL_POLICY_AND_FEES.md`

**Merge (combine into single page):**
- `CURRENCY_AND_EXCHANGE.md` + `financial_system.md` → `economy/CURRENCIES_AND_ACCOUNTS.md` ✅ done
- `VIRTUAL_LEDGER_FLOWS.md` + `LEDGERS.md` → `economy/VIRTUAL_LEDGER_AND_SETTLEMENT.md` (pending)

**Deprecate:**
- `gcc_coupling_status.md` — ephemeral status tracker
- `economic_baseline.md` — specific numbers belong in config docs
- `ISRU_PRICING_MODEL.md` — manufacturing domain, not economy
- `PLAYER_CONTRACT_SYSTEM.md` — player-facing, belongs in player wiki
- `MARKET_OPERATIONS.md` — overlaps with price discovery; merge into ECONOMY_OVERVIEW

---

## Section 3: Deliverables (4 New Wiki Pages)

### Page 1: `economy/CURRENCIES_AND_ACCOUNTS.md` ✅

**Covers:**
- `Financial::Currency` model (symbol, issuer, precision, system_currencies scope)
- `Financial::Account` model (polymorphic accountable, overdraft permission, optimistic locking)
- GCC/USD peg phases: Hard Peg → Soft Peg/Managed Float → Full Float
- Uncoupling triggers and EAP conversion formula
- Currency stability measures from GUARDRAILS.md (overdraft limits, exchange bands, minting limits, reserves)
- Debt & overdraft controls (NPC inter-debt normalcy, player debt ceiling 200%)
- GCC as numeraire — explicit statement: "GCC is a currency, not a material"

### Page 2: `economy/BONDS_AND_FINANCING.md` ✅

**Covers:**
- `Financial::Bond` model (issuer, holder, currency, status enum, repayments)
- Bond lifecycle: issued → paid / defaulted
- 4 bond types: Launch Service Bonds, GCC Mining Bonds, Inter-DC Bonds, USD Capital Bonds
- GCC mining satellite example (manifest financing: $1,857,986.22, 180 days, 5%)
- Bond repayment flow with currency conversion
- Supply-side impact on economic system (GCC demand sinks)
- Proposed bond schema for `data/json-data/schemas/gcc_mining_bond_v1.json`

### Page 3: `economy/LAUNCH_PAYMENT_MODEL.md` ✅

**Covers:**
- Full payment flow: mass calculation → launch cost → payment processing → bond creation
- Configuration structure (pricing + payment methods)
- Mass calculation details (base craft + units + modules + rigs hierarchy)
- Blueprint lookup fallback chain
- GCC mining satellite integration test example with cost breakdown
- Edge cases and error handling (insufficient funds, bond maturity, default)
- Relationship to economic system (GCC sink, USD source)

### Page 4: `economy/ECONOMY_OVERVIEW.md` ✅

**Covers:**
- Dual-currency, NPC-first system architecture with 3 monetary layers (GCC, USD, Virtual Ledger)
- Core design principles (5 principles)
- Complete documentation map linking all economy wiki pages
- Key models & services table
- Economic flow diagram (ASCII art)
- Configuration sources table

---

## Section 4: Inconsistencies & Gaps

### Ledger System — Implementation Status (CORRECTED)

**Previous claim was wrong**: `Financial::LedgerEntry` and `Financial::LedgerManager` **do exist** in the codebase. My audit failed to verify them before making the incorrect claim that they were absent.

#### What is Implemented

| Component | Status | Details |
|-----------|--------|---------|
| `Financial::LedgerEntry` model | ✅ **Implemented** | `from_account` (optional), `to_account` (required), `currency` (optional), `item` (optional), `entry_type` enum (`currency_transfer`, `goods_transfer`), scopes: `npc_to_npc`, `off_market` |
| `Financial::LedgerManager.reconcile_npc_debts` | ⚠️ **Skeletal** | Finds debtor accounts, defines two reconciliation paths (asset/goods swap, USD-to-GCC liquidation) — both are placeholder logic with comments, no actual implementation |
| `Financial::VirtualLedgerService.record_transfer` | ✅ **Implemented** | Creates LedgerEntry records for NPC-to-NPC transfers; resolves currency/item; distinguishes currency vs goods transfer types |
| `Financial::VirtualLedgerService.off_market_volume` | ✅ **Implemented** | Queries off-market volume via `LedgerEntry.off_market` scope |
| `Financial::VirtualLedgerService.corporate_transfers` | ✅ **Implemented** | Queries inter-corporation transfers grouped by currency |
| `Financial::Account.can_overdraft?` | ✅ **Implemented** | NPCs and Colonies can have negative balances; players cannot |
| `Financial::Account.transfer_funds` | ✅ **Implemented** | Uses optimistic locking; enforces overdraft permission |

#### What is Partially Implemented / Proposed

| Component | Status | Details |
|-----------|--------|---------|
| NPC debt reconciliation (LedgerManager) | ⚠️ **Skeletal** | `reconcile_npc_debts` has two planned paths but neither is implemented: (1) Asset/goods swap — commented placeholder; (2) USD-to-GCC liquidation (`settle_with_usd`) — empty method body with only a comment |
| Virtual ledger clearing policy | 📋 **Proposed** | LEDGERS.md describes virtual ledger as "NPC accounting" but no formal clearing/settlement policy exists in code |
| LedgerEntry `npc_to_npc` scope | ⚠️ **Stub** | Returns `all` — not yet filtered by NPC status of accountable entities |
| LedgerEntry `off_market` scope | ✅ **Implemented** | Filters by entry_type (currency_transfer or goods_transfer) |

#### What LEDGERS.md Gets Wrong

LEDGERS.md describes the virtual ledger as if it's a separate accounting layer from Account balances. In reality:
- **Virtual ledger IS negative Account balances** — NPCs can go negative via `can_overdraft?`
- **LedgerEntry is an audit trail** — records created by VirtualLedgerService for off-market activity tracking, not a separate balance system
- **Reconciliation is proposed, not implemented** — LedgerManager.reconcile_npc_debts is a skeleton with no working logic

### Contradictions Between Docs and Code (7 items)

| # | Issue | Where | Fix |
|---|-------|-------|-----|
| 1 | `ExchangeRateService` vs `ExchangeRate` naming | PRICE_DISCOVERY_LIFECYCLE.md | Update to static methods on ExchangeRate |
| 2 | GCC minting described as revenue conversion, not direct mining | LEDGERS.md | Align with satellite mining mechanism |
| 3 | Fiscal fee values inconsistent (0.5%, 0.3%, 3.37% vs 0.5%, 10%) | Authority map vs FISCAL_POLICY doc | Verify current values |
| 4 | Bond model missing maturity_days and interest_rate fields | Bond model file | Check migrations; document if exists |
| 5 | GCC described as having production output but no material file | GCC_MINTING_AND_PRESEEDING.md | Explicitly document GCC excluded from materials |
| 6 | Inter-DC interest exemption not enforced in code | GUARDRAILS.md §8 | Add validation or mark as "unenforced" |
| 7 | LDC stabilization reserves (25%) not implemented | GUARDRAILS.md §8 | Document as "policy not yet enforced" |

### Missing Topics in New Wiki (8 items)

| Priority | Topic | Where It Should Go |
|----------|-------|-------------------|
| P0 | Exchange rate mechanism (how rates change) | `economy/EXCHANGE_RATES.md` — needs own page |
| P0 | Economy overview | ✅ ECONOMY_OVERVIEW.md done |
| P1 | NPC price calculator mechanics (EAP, buy/sell spreads) | Dedicated page or expanded section |
| P1 | Market operations (NPC order posting/updating) | `economy/MARKET_OPERATIONS.md` |
| P2 | Player contract system integration | `economy/PLAYER_CONTRACTS.md` or player wiki |
| P1 | GCC material gap implications | Add to CURRENCIES_AND_ACCOUNTS.md |
| P2 | ISRU pricing model placement | Move to 05_MANUFACTURING/ if kept |
| P1 | Dual economy intent (Earth vs space philosophy) | `economy/DUAL_ECONOMY_INTENT.md` |

### Structural Gaps

| Issue | Recommendation |
|-------|----------------|
| Economy section has no README | ECONOMY_OVERVIEW.md serves as this ✅ |
| Phase 4 site map lists pages that don't exist | 3 of 4 priority pages written; remaining fill incrementally |
| gcc_coupling_status.md is status tracker, not reference | Deprecate — move to ephemeral tracking or delete |
| ISRU_PRICING_MODEL.md straddles economy/manufacturing | Move to manufacturing section |

### Immediate Action Items (5)

1. **Update LEDGERS.md** — Distinguish implemented ledger record structure from skeletal NPC reconciliation; stop describing virtual ledger as separate from Account balances
2. **Fix exchange rate service name** — Update ExchangeRateService → ExchangeRate in all docs
3. **Create GCC material gap note** — Explicitly document GCC excluded from material system
4. **Verify Bond model fields** — Check if maturity_days, interest_rate, due_at exist on migration
5. **Add economy/README.md** — ECONOMY_OVERVIEW.md can serve as this ✅

---

## Files Produced in This Session

| File | Path |
|------|------|
| GCC Minting Architecture (draft) | `docs/architecture/economy/GCC_MINTING_AND_PRESEEDING.md` |
| Currencies & Accounts | `docs/wiki_reorganization/economy/CURRENCIES_AND_ACCOUNTS.md` |
| Bonds & Financing | `docs/wiki_reorganization/economy/BONDS_AND_FINANCING.md` |
| Launch Payment Model | `docs/wiki_reorganization/economy/LAUNCH_PAYMENT_MODEL.md` |
| Economy Overview | `docs/wiki_reorganization/economy/ECONOMY_OVERVIEW.md` |

---

*All deliverables are draft status pending human review and commit.*
