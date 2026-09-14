# GCC/USD Decoupling & Exchange-Rate Evidence Inventory

**Date**: 2026-09-14  
**Prepared by**: Qwen (Planning Agent) — read-only evidence audit  
**Status**: EVIDENCE ONLY — no changes to code, wiki, config, or task files  
**Scope**: USD/GCC parity, decoupling, floating rates, multi-currency exchange, monetary transition  

---

## Executive Summary

The repository contains **substantial infrastructure for multi-currency exchange** that is partially implemented, partially dormant, and partially documented in wiki pages. The confirmed Luna-MVP policy (USD = GCC fixed 1:1, no automatic decoupling) does NOT contradict any existing code — the exchange-rate system exists as a general-purpose service with a hardcoded default of 1:1. However, there are **three areas of ambiguity** that need Tracy review:

1. `VirtualLedgerService.exchange_rate_to_gcc` returns a hardcoded `100.0` (not 1.0) — this is likely stale test code, not production logic
2. The wiki documents three peg phases (Hard → Soft → Managed Float → Full Float) but no implementation exists for phase transitions
3. Multiple integration tests simulate exchange-rate fluctuation scenarios that are exploratory/deferred

---

## 1. Documentation/Wiki Evidence

### A. Economy Wiki (`docs/wiki_reorganization/economy/`)

| File | Section | Classification | Details |
|------|---------|---------------|---------|
| `02-currencies-and-accounts.md` §3 | GCC/USD Peg & Exchange Phases | **EXPLORATORY/DEFERRED DESIGN** | Documents three phases: (1) Hard Peg 1:1, (2) Soft Peg ±10%, (3) Full Float market-driven. States "Uncoupling Triggers" include "sufficient GCC supply," "market depth," and "AI Manager monitors for volatility." **No implementation exists.** This is design documentation only. |
| `02-currencies-and-accounts.md` §4 | Currency Stability Measures | **EXPLORATORY/DEFERRED DESIGN** | NPC overdraft limit, player debt ceiling, interest rate floor — documented but not implemented as automated mechanisms. |
| `03-market-and-pricing.md` §1 | GCC/USD Peg — The Market Primer | **EXPLORATORY/DEFERRED DESIGN** | Same three-phase progression documented with implementation notes: "Financial::ExchangeRateService defaults to 1:1. The rate hash `{ ["USD", "GCC"] => 1.0 }` is set at seed time." Also documents EAP conversion formula: `EAP_gcc = EAP_usd / exchange_rate(USD → GCC)`. |
| `03-market-and-pricing.md` §1 | Bond Risk Note | **EXPLORATORY/DEFERRED DESIGN** | "LDC can issue USD-denominated bonds for major capital projects. If GCC depreciates against USD after bond issuance, the debt burden increases in GCC terms." This is a design note, not implemented logic. |
| `04-bonds-and-financing.md` §3.1 | Launch Service Bonds | **CONFIRMED CURRENT** | Bond model exists (`Financial::Bond`). `total_repaid()` accepts an exchange_rate_service parameter for currency conversion. Implementation confirmed in code (see Section 2 below). |
| `GAPS.md` Gap D + H | Hybrid GCC Supply Model / Emission Schedule | **EXPLORATORY/DEFERRED DESIGN** | Documents "1,000,000 GCC/cycle daily from LDC mining satellites" and "halving schedule: TBD." No implementation. The "emission" terminology is problematic (see planning report). |
| `AUDIT-ECONOMY-DOCS.md` Step 5 | Cross-reference updates | **HISTORICAL** | Documents which files need path updates after wiki consolidation. Not relevant to GCC/USD policy. |

### B. Legacy Economy Docs (`docs/architecture/economy/`)

| File | Classification | Details |
|------|---------------|---------|
| `CURRENCY_AND_EXCHANGE.md` | **HISTORICAL/SUPERSEDED** | Superseded by `02-currencies-and-accounts.md`. Contains same three-phase peg documentation. |
| `GCC_MINTING_AND_PRESEEDING.md` | **EXPLORATORY/DEFERRED DESIGN** | Documents mining satellite deployment, halving schedule, supply cap — all TBD. No implementation. |
| `gcc_coupling_status.md` | **DYNAMIC STATE / REFERENCE** | Per-system exchange rate tracking. Dynamic data file, not architecture doc. |

### C. Summaries/Handoffs

| Source | Classification | Details |
|--------|---------------|---------|
| `2026-09-14-GCC-ECONOMIC-CLASSIFICATION-PLANNING-REPORT.md` | **CONFIRMED** | Confirms GCC is fiat-style ledger currency. Documents four wiki wording conflicts (A-D). |
| Perplexity handoff (`2026-09-14-PERPLEXITY-HANDOFF.md`) | **CONFIRMED** | Documents USD peg as "bootstrap price calibration." No automatic decoupling trigger. |

---

## 2. Code/Data Model Evidence

### A. Exchange Rate Service — IMPLEMENTED (general-purpose, default 1:1)

**File**: `galaxy_game/app/services/financial/exchange_rate_service.rb`

| Method | Status | Details |
|--------|--------|---------|
| `initialize(rates = {})` | **IMPLEMENTED** | Accepts rate hash. Default empty → all conversions return 1:1. |
| `convert(amount, from, to)` | **IMPLEMENTED** | Returns `amount * rate`. If no rate found, returns `amount` (implicit 1:1). |
| `get_rate(from, to)` | **IMPLEMENTED** | Looks up `[from, to]` key in `@rates`. Default fallback: `1.0`. |
| `set_rate(from, to, rate)` | **IMPLEMENTED** | Sets/updates a rate in the hash. |
| `value_of(item, quantity, target_currency)` | **IMPLEMENTED** | Delegates to `convert`. |
| `base_price_for(entity_id, target_currency)` | **IMPLEMENTED** | Looks up item/blueprint value, converts to target currency. Fallback: 100 GCC. |
| `market_price_for()` | **DORMANT** | Returns `nil` (TODO). Not implemented. |
| `price_for()` | **PARTIAL** | Falls back to `base_price_for` if no market price. |

**Key finding**: The service is a general-purpose in-memory rate store. It does NOT persist rates to the database. There is NO automatic phase-transition logic. Rates are set externally (e.g., at seed time or via admin API).

### B. Exchange Rate Model — IMPLEMENTED (database-backed)

**File**: `galaxy_game/app/models/financial/exchange_rate.rb`

| Method | Status | Details |
|--------|--------|---------|
| `ExchangeRate` model | **IMPLEMENTED** | ApplicationRecord with `from_currency`, `to_currency`, `rate`. Validated uniqueness on from/to pair. |
| `get_rate(from_symbol, to_symbol)` | **IMPLEMENTED** | DB lookup via scope. Default fallback: `1.0`. |
| `set_rate(from_symbol, to_symbol, rate)` | **IMPLEMENTED** | `first_or_initialize` pattern — creates or updates DB record. |

**Key finding**: This is a separate persistence layer from `ExchangeRateService`. The service uses an in-memory hash; the model uses the database. They are NOT connected — calling `ExchangeRate.set_rate()` does NOT update `ExchangeRateService`'s rates, and vice versa. **This is a potential bug or design gap.**

### C. Currency Model — IMPLEMENTED

**File**: `galaxy_game/app/models/financial/currency.rb`

| Field | Status | Details |
|-------|--------|---------|
| `symbol` | **IMPLEMENTED** | 2-5 uppercase chars (e.g., "GCC", "USD"). Validated uniqueness. |
| `is_system_currency` | **IMPLEMENTED** | Boolean flag for pre-defined system currencies. |
| `precision` | **IMPLEMENTED** | 0-8 decimal places. |
| `issuer` | **IMPLEMENTED** | Polymorphic association (who mints this currency). |

### D. Virtual Ledger Service — PARTIAL EXCHANGE RATE

**File**: `galaxy_game/app/services/financial/virtual_ledger_service.rb`

| Method | Status | Details |
|--------|--------|---------|
| `exchange_rate_to_gcc` | **STALE/HARDCODED** | Returns `100.0` with comment "Assume 1 USD = 100 GCC or something." This is clearly test/stub code, not production logic. |
| `record_in_situ_savings()` | **USES STALE RATE** | Calls `exchange_rate_to_gcc` (returns 100.0) to convert USD savings to GCC. This means in-situ savings are recorded at 1/100th of their USD value in GCC — likely a bug. |

### E. Bond Model — IMPLEMENTED with Exchange Rate Support

**File**: `galaxy_game/app/models/financial/bond.rb`

| Method | Status | Details |
|--------|--------|---------|
| `total_repaid(exchange_rate_service = nil)` | **IMPLEMENTED** | If exchange_rate_service is passed, converts repayment amounts using it. Otherwise returns raw sum. |
| `paid_off?(exchange_rate_service = nil)` | **IMPLEMENTED** | Delegates to `total_repaid`. |

### F. GCC Mining — `mine_gcc` Method

**File**: `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` (line 10)

| Aspect | Current Implementation |
|--------|----------------------|
| **Formula** | `total_mined = ∑(unit.mine(difficulty, efficiency) × thermal_multiplier × processing_multiplier + direct_boost)` per mining unit |
| **Field reads** | `operational_data.dig('thermal_effects', 'heat_dissipation_kw')`, `operational_data.dig('processing_effects', 'boost_multiplier')`, `operational_data.dig('mining_effects', 'boost_gcc_per_hour')` |
| **Thermal boost** | 2% efficiency per 10kW heat dissipation: `1.0 + (heat_dissipation / 10.0) * 0.02` |
| **Processing boost** | Multiplier from `operational_data['processing_effects']['boost_multiplier']` |
| **Direct boost** | `operational_data.dig('mining_effects', 'boost_gcc_per_hour') * 0.18` (hourly → test period) |
| **Power check** | `has_sufficient_power?` → if insufficient, tries battery → if battery insufficient, returns 0 |
| **Battery behavior** | `consume_battery(power_needed)` — drains battery to cover power shortfall |
| **Ledger write** | `account.deposit(total_mined, "GCC Mining Operation")` + `self.funds += total_mined` |
| **Mining log** | Creates `MiningLog` record with operational details (power used, efficiency factors, unit count) |
| **Units** | GCC per call (not per-hour; the 0.18 multiplier converts hourly rate to "test period" rate) |
| **Fallback** | Returns `0` if: no account, no mining units, or insufficient power with no battery |

### G. Power/Energy Management

| File | Method | Status |
|------|--------|--------|
| `energy_management.rb` | `has_sufficient_power?` | IMPLEMENTED — checks power generation vs. consumption |
| `orbital_depot.rb` | `has_sufficient_power?` | IMPLEMENTED (separate implementation for depot) |
| `cryptocurrency_mining.rb` | `power_required_for_mining` | EXISTS at line 259 — need to read full method |

### H. Mining Unit Adapter

**File**: `cryptocurrency_mining.rb` (line 293)

| Class | Status | Details |
|-------|--------|---------|
| `MiningUnitAdapter` | **IMPLEMENTED** | Wraps `Units::Computer` units for mining. `mining_units` method selects base_units where `unit_type` includes "computer" and wraps each in adapter. |

---

## 3. Configuration Evidence

### A. Economic Parameters

**File**: `galaxy_game/config/economic_parameters.yml`

| Setting | Value | Classification |
|---------|-------|---------------|
| `usd_to_gcc_peg` | `1.0` | **CONFIRMED CURRENT LUNA MVP** — hardcoded in config |

### B. Integration Tests (Exchange Rate Scenarios)

| File | Classification | Details |
|------|---------------|---------|
| `exchange_rate_integration.rb` | **EXPLORATORY/DEFERRED** | Simulates exchange rate fluctuation scenarios. Includes bond USD→GCC conversion at depreciated rates. Not production code. |
| `exchange_rate_integration_2.rb` | **EXPLORATORY/DEFERRED** | Tests core ExchangeRateService with simulated depreciation (1:1 → 1.3 GCC/USD). Simulates bond repayment risk. Not production code. |
| `resource_acquisition_2.rb` | **EXPLORATORY/DEFERRED** | Uses ExchangeRateService for resource acquisition pricing. Sets rate to 1.0 explicitly. Not production code. |
| `gcc_mining_sat_original2.rb` | **HISTORICAL** | Early GCC mining test. Sets exchange rate to 1.0. Historical artifact. |
| `gcc_mining_sat_integration_simplified.rb` | **HISTORICAL** | Simplified integration test. Hardcoded `exchange_rate = 1.0`. Historical artifact. |

---

## 4. Admin/Controller Evidence

| File | Finding | Classification |
|------|---------|---------------|
| `admin/resources_controller.rb:26` | `@gcc_exchange_rate = 1.0` hardcoded | **CONFIRMED CURRENT** — admin UI uses 1:1 |

---

## 5. Conflict Analysis

### Conflict A: Two Separate Exchange Rate Systems

**Issue**: `ExchangeRateService` (in-memory hash) and `ExchangeRate` model (database) are NOT connected.

| System | Storage | Persistence | Used By |
|--------|---------|-------------|---------|
| `Financial::ExchangeRateService` | In-memory hash `@rates` | NO — lost on restart | `NpcPriceCalculator`, `VirtualLedgerService`, integration tests |
| `Financial::ExchangeRate` model | PostgreSQL DB | YES | Bond repayment conversion (optional parameter) |

**Impact**: If rates are set via the service, bonds won't see them. If rates are set via the model, the service won't see them. **This is a design gap, not a policy conflict.**

### Conflict B: VirtualLedgerService Uses Stale Rate

**Issue**: `VirtualLedgerService.exchange_rate_to_gcc` returns `100.0` with comment "Assume 1 USD = 100 GCC or something."

**Impact**: In-situ savings are recorded at 1/100th of their USD value in GCC. This is almost certainly a bug from test code that was never cleaned up. **This needs Tracy review before P0.**

### Conflict C: Wiki Documents Phase Transitions, Code Does Not

**Issue**: Wiki documents three peg phases (Hard → Soft → Managed Float → Full Float) with "uncoupling triggers," but no implementation exists for any phase transition. The service defaults to 1:1 and stays there unless set externally.

**Impact**: This is **exploratory/deferred design**, not a conflict. The wiki documents intended future behavior; the code implements current behavior (fixed 1:1). No automatic decoupling trigger exists.

### Conflict D: "Emission" Terminology in GAPS.md

**Issue**: Gap D uses "Hybrid GCC Supply Model" and Gap H uses "Emission Schedule Enforcement." In a space-industrial context, "emission" reads as gas/chemical emission, not currency issuance.

**Impact**: This is a terminology issue (noted in the planning report). Needs correction to "issuance/minting."

---

## 6. Classification Summary

| Finding | Classification |
|---------|---------------|
| `ExchangeRateService` general-purpose service with default 1:1 | **IMPLEMENTED but dormant** — works for multi-currency, defaults to fixed peg |
| `ExchangeRate` DB model | **IMPLEMENTED but partially connected** — used by bonds, not by service layer |
| `Currency` model (GCC, USD) | **CONFIRMED CURRENT** — both currencies exist as system currencies |
| `VirtualLedgerService.exchange_rate_to_gcc` returns 100.0 | **BUG / STUB CODE** — needs cleanup |
| Wiki three-phase peg documentation | **EXPLORATORY/DEFERRED DESIGN** — no implementation |
| Integration tests with fluctuating rates | **EXPLORATORY/DEFERRED** — test scenarios, not production |
| `economic_parameters.yml` peg = 1.0 | **CONFIRMED CURRENT LUNA MVP** |
| Admin controller hardcoded 1:1 | **CONFIRMED CURRENT** |
| Bond model with exchange rate support | **IMPLEMENTED** — optional parameter for currency conversion |
| GCC mining via `CryptocurrencyMining` concern | **CONFIRMED CURRENT** — fitting-driven, power-constrained, ledger-deposited |

---

## 7. Open Questions for Tracy

1. **VirtualLedgerService stale rate**: Should `exchange_rate_to_gcc` be fixed to return `1.0` (matching the peg) or should it delegate to `ExchangeRateService`? This affects in-situ savings calculations.

2. **Two exchange rate systems**: Should `ExchangeRateService` and `ExchangeRate` model be unified, or is the separation intentional (in-memory for pricing, DB for bond records)?

3. **GCC mining units**: The `mine_gcc` method uses a `0.18` multiplier to convert hourly rates to "test period" rates. What does this multiplier represent? Is it per-tick, per-day, or something else?

4. **Power cost of mining**: Does GCC issuance have a separate economic cost (GCC deducted for power), or is power purely a simulation constraint (throughput reduction) with no direct GCC cost?

5. **Halving schedule**: GAPS.md mentions "halving schedule: TBD." Is this still TBD, or has it been decided elsewhere?

6. **Wiki phase transitions**: Should the three-phase peg documentation be marked as "deferred" in the wiki to prevent confusion, or kept as aspirational design?

---

## 8. P0 Task Impact Assessment

The P0 task (`2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-FITTING-DRIVEN-OUTPUT-GAMELOOP-INTEGRATION.md`) is **compatible with the confirmed Luna-bootstrap policy**:

- ✅ The exchange-rate service defaults to 1:1 — no automatic decoupling
- ✅ GCC mining deposits to `account.deposit()` — ledger-based, not physical inventory
- ✅ Fitting-driven output is already implemented in `mine_gcc` — the task is about wiring it into the tick loop, not building from scratch
- ✅ No new currencies or exchange mechanisms are needed for P0

**P0 does NOT need to be revised for policy alignment.** The only potential impact is:
- If `VirtualLedgerService.exchange_rate_to_gcc` bug (100.0) affects GCC mining ledger entries, that should be fixed as part of P0 or as a separate low-priority fix.

---

## 9. Recommended Actions (For Tracy's Approval)

| Action | Priority | Owner |
|--------|----------|-------|
| Fix `VirtualLedgerService.exchange_rate_to_gcc` to return 1.0 or delegate to ExchangeRateService | LOW — bug fix | P0 or separate task |
| Mark wiki three-phase peg as "deferred" to prevent confusion | LOW — documentation | Wiki maintenance |
| Clarify GCC mining unit rate (what does 0.18 multiplier represent?) | MEDIUM — design question | Tracy + Gemini |
| Decide whether to unify ExchangeRateService and ExchangeRate model | MEDIUM — architecture decision | Tracy + implementation agent |
| Confirm halving schedule status (TBD or decided?) | LOW — design question | Gemini |

---

**Report complete. No code, wiki, config, or task files have been modified.**
