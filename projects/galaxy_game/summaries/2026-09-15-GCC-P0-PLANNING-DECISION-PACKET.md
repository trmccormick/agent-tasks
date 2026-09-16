# GCC P0 Planning Decision Packet

**Date**: 2026-09-15  
**Agent**: Qwen (Planning Agent)  
**Role**: Read-only planning and human-decision artifact  
**Constraint**: No implementation, modification, staging, committing, or dispatching. No policy decisions. Luna-first only. Phase 2–3 deferred.

---

## Human Decisions Settled This Session

The following design decisions were settled by human stakeholders (Tracy) and form the authoritative record for all subsequent planning:

| # | Decision | Status |
|---|----------|--------|
| 1 | **1 GCC = 1 USD remains fixed** for initial launch and Luna bootstrap phase. Locked stable accounting standard. | SETTLED |
| 2 | **Future uncoupling deferred** pending sustained organic economic maturity (qualitative indicators only, no numeric thresholds). | DEFERRED |
| 3 | **LDC-managed band around parity** is a nonbinding future design reference — not current scope or approved implementation. | DEFERRED |
| 4 | **GCC identity**: centrally managed, crypto-inspired virtual ledger currency using LDC-controlled simulated compute mining for issuance. | SETTLED |
| 5 | **Initial mining infrastructure**: LDC crypto-mining satellites; later LDC-authorized data centers may participate. | SETTLED |
| 6 | **All newly mined GCC flows to the existing LDC account**. LDC distributes via transfers, market liquidity, contracts, services, rewards, and authorized disbursements. | SETTLED |
| 7 | **Accounts support GCC, USD, and future currencies** — no database redesign needed; add new currencies to Financial::Currency table only. | SETTLED |
| 8 | **Physical resource extraction never directly creates GCC**. Produces physical cargo/materials only. | SETTLED |

---

## A. Current-State Contract

### Confirmed Implementation Behavior (Source-Verified)

| Area | What the Code Actually Does | Source Evidence |
|------|---------------------------|-----------------|
| **GCC identity** | GCC is a centrally managed, crypto-inspired virtual ledger currency using LDC-controlled simulated compute mining for issuance. Exists as Financial::Account deposits, Financial::Currency records, LedgerEntry records. Never appears in physical material/cargo/inventory/recipe/commodity schemas. | cryptocurrency_mining.rb:53; integration tests; economy wiki docs |
| **Mining issuance** | `mine_gcc` calculates amount from fitted computer operational data + difficulty/efficiency/thermal/processing/direct-boost modifiers → calls `account.deposit(total_mined, "GCC Mining Operation")` → creates net-new GCC via ledger entry. All newly mined GCC flows to the existing LDC account. No issuer gate in deposit(). | cryptocurrency_mining.rb:10-78 |
| **Craft-stat path** | `recalculate_stats` computes `current_mining_rate_gcc_per_hour` from satellite base rate + fitted computers + GPU rigs. Stored in operational_data. Zero consumers discovered for mining payout or balance mutation. | base_craft.rb:372-389 |
| **Satellite base-rate** | `base_mining_rate_gcc_per_hour: 1000` in satellite operational data is read by recalculate_stats but NEVER consumed by mine_gcc. Dead/unconsumed design data for mining output. | crypto_mining_satellite_data.json; MiningUnitAdapter.extract_mining_rate fallback chain |
| **Time cadence** | speed=3 → 60 real seconds/game day (game_state.rb case statement). Construction uses /86400 real-world days. Missions use EPOCH/86400. Mining rake uses independent game_days loop counter. | game_state.rb:49-60; mega_project.rb:32; ai_manager_controller.rb:178; gcc_mining_sat.rake:22 |
| **ExchangeRateService** | In-memory hash, default 1:1 (matches bootstrap peg), active for NPC trade/market pricing via NpcPriceCalculator. Lost on restart. | exchange_rate_service.rb |
| **ExchangeRate model** | PostgreSQL DB, default 1.0 (matches bootstrap peg), active for bonds via Financial::Bond repayment conversion. | financial/exchange_rate.rb |
| **Two systems disconnected** | Zero synchronization routes between ExchangeRateService and ExchangeRate model. Rate changes via one are invisible to the other. | Source-trace of all callers |
| **VirtualLedgerService.exchange_rate_to_gcc** | Production-active at hardcoded 100.0. Called from record_in_situ_savings(). Converts 100 USD → 1 GCC (undervalued by 2 orders vs. 1:1 bootstrap peg). **This is a confirmed correctness defect.** | virtual_ledger_service.rb:93-96, line 67 |
| **GCC as centrally managed currency** | CONFIRMED — GCC is a centrally managed, crypto-inspired virtual ledger currency using LDC-controlled simulated compute mining for issuance. Exists only as ledger entries, never physical material. | Full source-trace across all schemas |

### Established Design Intent (From DUAL_ECONOMY_INTENT.md + Economy Wiki)

| Area | Settled Bootstrap-Phase Design | Implementation Gap |
|------|-------------------------------|-------------------|
| **GCC identity** | Centrally managed, crypto-inspired virtual ledger currency using LDC-controlled simulated compute mining for issuance. 8-decimal precision. System currency. LDC is the GCC mint. | ✅ Matches implementation (ledger-only) |
| **LDC as sole issuer** | LDC generates new GCC through crypto mining satellites. All mined GCC flows to existing LDC account. Compute capacity alone does NOT grant authority to issue GCC. | ⚠️ Policy intent documented but NOT enforced by code — no issuer gate in deposit() |
| **USD:GCC peg** | **1 GCC = 1 USD remains fixed for initial launch and Luna bootstrap phase.** Locked stable accounting standard. Dynamic exchange-rate policy deferred beyond current P0. | ⚠️ ExchangeRateService defaults 1:1 (matches); VirtualLedgerService returns 100.0 (contradicts — confirmed defect) |
| **Mining as controlled issuance** | Simulated compute mining, not physical extraction. LDC centrally controls supply. Crypto-mining satellites and authorized data centers are issuance infrastructure. | ✅ mine_gcc creates net-new GCC via deposit (consistent with "controlled issuance") |
| **Physical ≠ GCC** | Physical resource extraction produces physical cargo/materials; never directly creates GCC. Compute capacity alone does not authorize currency creation. | ✅ Confirmed — GCC absent from all physical schemas |
| **Multi-currency accounts** | Financial::Account holds multiple currencies. GCC and USD supported now; future currencies extensible via Financial::Currency table only. No database redesign needed. | ✅ Implemented (Financial::Account supports per-currency balances) |
| **Future peg separation** | Deferred pending sustained organic economic maturity (qualitative indicators only). LDC-managed band around parity is a nonbinding future design reference. | ❌ Not implemented — not in scope

### Contradictions, Missing Enforcement Points, Unresolved Runtime Facts

| # | Issue | Type | Severity |
|---|-------|------|----------|
| 1 | VirtualLedgerService.exchange_rate_to_gcc = 100.0 contradicts 1:1 bootstrap peg | **Contradiction** | HIGH — in-situ savings undervalued by 2 orders of magnitude |
| 2 | Two exchange-rate systems (in-memory vs DB) with zero synchronization | **Missing enforcement** | MEDIUM — rate changes via one invisible to the other |
| 3 | mine_gcc has no issuer gate — any account reference can deposit GCC | **Missing enforcement** | MEDIUM — "LDC sole issuer" is policy, not code-enforced |
| 4 | recalculate_stats and mine_gcc are disconnected; current_mining_rate_gcc_per_hour has zero consumers for mining | **Unresolved runtime fact** | LOW — documented as P0 scope item |
| 5 | Satellite base_mining_rate_gcc_per_hour: 1000 is dead data for mining output | **Unresolved runtime fact** | LOW — dead/unconsumed design data |
| 6 | "Per hour" source fields have no demonstrated runtime time basis | **Unresolved runtime fact** | LOW — naming convention without verified time-basis |

---

## B. Time-Domain Decision Packet

### Observed Timing Sources

| System | Timing Source | Uses seconds_per_game_day? | Independent Timer? |
|--------|--------------|--------------------------|-------------------|
| **Production simulation** | GameSimulationJob (self-scheduled every 1 minute) → `days_to_simulate = (elapsed_seconds / game_state.seconds_per_game_day).to_i` | YES | Job interval hardcoded at 1 minute; does NOT derive from seconds_per_game_day |
| **Construction** | MegaProject.days_remaining: `(deadline - Time.current).to_i / 86400.0` | NO | Real-world days via EPOCH/86400 |
| **Missions** | Mission.average('EXTRACT(EPOCH FROM (updated_at - created_at))/86400') | NO | Real-world days via EPOCH/86400 |
| **Mining** | Rake loop: `game_days.times { ... }` with ENV['GAME_DAYS'] counter; Sidekiq jobs call mine_gcc() directly | NO | Independent cron-driven (MineGccJob has fatal nil-receiver bug, never fired) |
| **Logistics** | TerraSim idle body simulation: `rand < 0.1` probability, simulates 1 day per idle body | NO | Randomized, not game-time driven |
| **Markets** | NpcPriceCalculator via Financial::ExchangeRateService — stateless lookup, not time-driven | N/A | N/A |
| **Finance** | Bond repayment: synchronous ExchangeRate.get_rate() lookup | N/A | N/A |

### Systems That Must Share Game-Time for Reliable Autonomous Luna NPC Buildup

For AI Manager cost/throughput comparison to be meaningful across systems, the following must share a consistent game-time basis:
- **Production simulation** (already uses seconds_per_game_day)
- **Mining** (currently independent rake loop — needs alignment)
- **Construction** (uses real-world days — may need game-day conversion for AI comparison)
- **Missions** (uses real-world days — may need game-day conversion for AI comparison)

### Three Bounded Options for Construction/Mission/Mining Timing Semantics

#### Option 1: Unified Game-Time (All Systems Use seconds_per_game_day)

| Criterion | Detail |
|-----------|--------|
| **Intended time basis** | All systems derive elapsed game days from `game_state.seconds_per_game_day` |
| **Effect at speed=3** | 60 real seconds = 1 game day for ALL systems (production, construction, missions, mining) |
| **Data-unit convention** | Game days as the universal time unit; real-world EPOCH only for external reporting |
| **NPC/AI consequences** | AI Manager cost/throughput comparison becomes consistent across all systems; NPC decisions scale uniformly with game speed |
| **Test requirements** | Verify construction/mission timers produce correct game-day counts at each speed setting (1-5); verify mining rake uses game_state for elapsed calculation |
| **P0 blocker level** | MEDIUM — affects AI Manager cost comparison accuracy but not core GCC issuance |
| **Implementation scope** | Modify MegaProject.days_remaining and Mission timeline logic to use game_state.seconds_per_game_day; modify mining rake to read game_state for elapsed calculation |

#### Option 2: Dual-Time (Production/Game-Time + Construction/Mission/Real-World)

| Criterion | Detail |
|-----------|--------|
| **Intended time basis** | Production uses game-time; construction/missions retain real-world EPOCH/86400; mining rake independent |
| **Effect at speed=3** | Production: 1 game day per 60 real seconds. Construction/missions: 1 day per 86400 real seconds (unchanged). Mining: independent loop. |
| **Data-unit convention** | Game days for production; real-world days for construction/missions; arbitrary for mining |
| **NPC/AI consequences** | AI Manager cost comparison across systems requires explicit time-unit conversion; NPC decisions may appear inconsistent at different speeds |
| **Test requirements** | Verify dual-time behavior is intentional and documented; verify no regression in construction/mission timelines |
| **P0 blocker level** | LOW — preserves existing behavior; does not resolve cross-system timing inconsistency |
| **Implementation scope** | None — status quo. Document the dual-time model explicitly. |

#### Option 3: Hybrid (Production + Mining Use Game-Time; Construction/Missions Retain Real-World)

| Criterion | Detail |
|-----------|--------|
| **Intended time basis** | Production and mining use game_state.seconds_per_game_day; construction/missions retain real-world EPOCH/86400 |
| **Effect at speed=3** | Production: 1 game day per 60 real seconds. Mining: aligned with production. Construction/missions: 1 day per 86400 real seconds (unchanged). |
| **Data-unit convention** | Game days for production/mining; real-world days for construction/missions |
| **NPC/AI consequences** | AI Manager cost comparison between mining and production is consistent; comparison with construction/missions requires conversion |
| **Test requirements** | Verify mining rake uses game_state for elapsed calculation; verify no regression in construction/mission timelines |
| **P0 blocker level** | MEDIUM — aligns mining with production (critical for GCC P0); leaves construction/missions as separate concern |
| **Implementation scope** | Modify mining rake to read game_state for elapsed calculation; modify MegaProject/Mission only if AI Manager requires unified comparison |

---

## B. Future-Reference Peg Note (Deferred)

### Locked Bootstrap Rule

**1 GCC = 1 USD** — This is the locked stable accounting standard for initial launch and throughout the Luna bootstrap phase. Every active USD↔GCC conversion path during this phase must honor that 1:1 relationship in the correct direction.

### Future Uncoupling (Deferred)

Future uncoupling from the 1:1 peg is deferred beyond current Luna P0. It is not triggered by elapsed time, total issued supply alone, one transaction, or one player/NPC account.

Future separation becomes plausible only after sustained off-Earth economic maturity, including qualitative indicators:
- Sustained GCC-denominated trade
- Meaningful non-LDC GCC circulation
- Multiple active market/settlement nodes
- Meaningful local production, services, and trade
- Local price influence from off-Earth supply, demand, transport, shortages, and production conditions
- Sufficient market history/liquidity
- Reliable multi-currency accounting and conversion behavior

### LDC-Managed Band (Nonbinding Future Reference)

A narrow LDC-managed band around parity is a nonbinding future design reference, not a current rule or approved implementation. Exact thresholds, band width, triggers, intervention policy, and dynamic-rate mechanics are deferred.

---

## C. Controlled GCC Mining Decision Packet

| Criterion | Detail |
|-----------|--------|
| **Intended time basis** | Production and mining use game_state.seconds_per_game_day; construction/missions retain real-world EPOCH/86400 |
| **Effect at speed=3** | Production: 1 game day per 60 real seconds. Mining: aligned with production. Construction/missions: 1 day per 86400 real seconds (unchanged). |
| **Data-unit convention** | Game days for production/mining; real-world days for construction/missions |
| **NPC/AI consequences** | AI Manager cost comparison between mining and production is consistent; comparison with construction/missions requires conversion |
| **Test requirements** | Verify mining rake uses game_state for elapsed calculation; verify no regression in construction/mission timelines |
| **P0 blocker level** | MEDIUM — aligns mining with production (critical for GCC P0); leaves construction/missions as separate concern |
| **Implementation scope** | Modify mining rake to read game_state for elapsed calculation; modify MegaProject/Mission only if AI Manager requires unified comparison |

---

## C. Controlled GCC Mining Decision Packet

### Minimum P0 Invariants (From Established Design Intent)

1. Only LDC-controlled or explicitly LDC-authorized mining infrastructure can perform issuance mining
2. Eligible infrastructure: initial crypto-mining satellites + later authorized mining data centers
3. Mining capacity alone does NOT confer mint authority
4. All new GCC is credited to the existing LDC account
5. Later player/NPC/market circulation is an explicit transfer of existing GCC (not re-issuance)
6. Resource extraction cannot call or be interpreted as GCC issuance
7. Issuance is distinguishable/auditable from ordinary transfers

### Three Bounded Implementation Approaches

#### Approach 1: Ownership-Gate on account.deposit() for GCC Currency

| Criterion | Detail |
|-----------|--------|
| **Required authorization/ownership/account context** | Add a currency-specific guard in Financial::Account#deposit: if currency == 'GCC' and entry_type == :issuance, verify the from_account belongs to an LDC-authorized entity. Check ownership chain against LDC account or authorized_mining_infrastructure list. |
| **Treatment of satellite vs. data-center mining** | Both satellite and future data-center mining call the same deposit path; gate checks entity authorization, not hardware type |
| **Impact on NPCs/players/markets/Luna physical extraction** | NPCs cannot create GCC via deposit; players cannot bypass LDC authority; markets unaffected (GCC circulation is transfer, not issuance); Luna physical extraction unchanged (no GCC creation from resource extraction) |
| **Relationship to disconnected craft-stat path** | No impact — gate is on deposit(), not on recalculate_stats or current_mining_rate_gcc_per_hour |
| **Tests/evidence required** | Verify LDC-authorized satellite can deposit GCC; verify non-LDC entity cannot deposit GCC as issuance; verify existing mine_gcc behavior unchanged for LDC satellites |
| **P0 blocker level** | MEDIUM — enforces "LDC sole issuer" policy in code; does not block mining functionality |
| **Non-goals** | Does not design a public blockchain, proof-of-work system, halving schedule, or multi-entity mint governance |

#### Approach 2: Dedicated Issuance Method on LDC Account

| Criterion | Detail |
|-----------|--------|
| **Required authorization/ownership/account context** | Create `Financial::Account#issue_gcc(amount, reason)` method that only LDC-owned accounts can call. mine_gcc calls this instead of account.deposit(). Ordinary transfers continue via account.deposit(). |
| **Treatment of satellite vs. data-center mining** | Both call the same issuance method; authorization is on the account (LDC-owned), not the hardware |
| **Impact on NPCs/players/markets/Luna physical extraction** | NPCs cannot issue GCC (no LDC-owned account); players cannot bypass; markets unaffected; Luna physical extraction unchanged |
| **Relationship to disconnected craft-stat path** | No impact — issuance method is separate from recalculate_stats |
| **Tests/evidence required** | Verify LDC account can call issue_gcc; verify non-LDC accounts raise error on issue_gcc; verify mine_gcc calls issue_gcc for new GCC creation |
| **P0 blocker level** | MEDIUM — clean separation of issuance vs. transfer; requires mine_gcc modification |
| **Non-goals** | Does not design a multi-entity mint, treasury, or market-maker structure |

#### Approach 3: MiningLog Entry-Type Enforcement + Audit Trail

| Criterion | Detail |
|-----------|--------|
| **Required authorization/ownership/account context** | No new gate on deposit(). Instead, enforce that all GCC issuance must have a corresponding MiningLog entry with entry_type :mining_issuance. Add audit query: any LedgerEntry with currency='GCC' and no MiningLog is suspicious. |
| **Treatment of satellite vs. data-center mining** | Both create MiningLog entries; enforcement is via audit, not prevention |
| **Impact on NPCs/players/markets/Luna physical extraction** | Does not prevent unauthorized issuance (audit-only); markets unaffected; Luna physical extraction unchanged |
| **Relationship to disconnected craft-stat path** | No impact — MiningLog is created by mine_gcc, separate from recalculate_stats |
| **Tests/evidence required** | Verify mine_gcc creates MiningLog with correct entry_type; verify audit query detects entries without MiningLog |
| **P0 blocker level** | LOW — does not prevent unauthorized issuance but makes it detectable; preserves existing behavior |
| **Non-goals** | Does not enforce authorization at deposit time; does not redesign ledger architecture |

---

## D. Exchange-Rate Decision Packet

### Approved P0 Bootstrap Peg: USD:GCC = 1:1

---

### Non-Dispatched Task Draft: Align In-Situ Savings Conversion with 1:1 Bootstrap Peg

> **STATUS**: DRAFT ONLY — NOT DISPATCHED, NOT CREATED AS A TASK FILE
> This draft is held for human approval before any implementation work.

#### Problem Statement
The production-active in-situ savings USD→GCC conversion contradicts the locked 1 GCC = 1 USD bootstrap peg. VirtualLedgerService.exchange_rate_to_gcc currently applies a rate of 100.0, making 100 USD yield only 1 GCC — undervalued by 2 orders of magnitude.

#### Confirmed Actual Behavior
- **Method**: `VirtualLedgerService#exchange_rate_to_gcc` returns hardcoded `100.0`
- **Caller**: `record_in_situ_savings()` calls this method during NPC economic simulation
- **Effect**: 100 USD → 1 GCC (should be 100 USD → 100 GCC)
- **Code location**: virtual_ledger_service.rb:93-96, line 67
- **Comment in code**: "Assume 1 USD = 100 GCC or something" — stale test artifact never cleaned up

#### Required Behavior
During bootstrap phase (1 GCC = 1 USD locked):
- `exchange_rate_to_gcc` must return a value that produces 1:1 conversion
- 100 USD must yield 100 GCC
- Direction matters: USD→GCC and GCC→USD both honor 1:1

#### Affected Conceptual Systems
- VirtualLedgerService (conversion method)
- record_in_situ_savings() (caller)
- Any other code calling exchange_rate_to_gcc (if discovered)

#### Explicit Non-Goals
- No dynamic rate system
- No peg-separation system
- No full exchange-system consolidation (ExchangeRateService ↔ ExchangeRate model sync deferred)
- No bond redesign
- No new currency schema
- No monetary-policy redesign

#### Acceptance Criteria
1. Correct rate direction and 1:1 value for USD↔GCC conversion
2. Coverage for representative amounts (1, 10, 100, 1000 USD) and decimal precision
3. Preservation of GCC/USD currency identity (no schema changes)
4. No regression to NPC market pricing or other identified conversion paths
5. Evidence that the corrected savings result is used consistently in downstream calculations

#### Dependencies
- None for the fixed bootstrap policy
- Note: wider rate-service unification (ExchangeRateService ↔ ExchangeRate model) remains deferred

#### Luna Impact
- Fixes in-situ savings accuracy and cross-currency cost/accounting reliability
- Does NOT independently fix all market/bond synchronization (that requires separate exchange-rate unification work)

---

### Bounded Options for Single Authoritative P0 Conversion Source

#### Option 1: Fix VirtualLedgerService to Delegate to ExchangeRateService

| Criterion | Detail |
|-----------|--------|
| **Affected economic operations** | In-situ savings recording (record_in_situ_savings()); any other code calling exchange_rate_to_gcc |
| **Persistence/synchronization assumptions** | ExchangeRateService remains in-memory; rate set to 1.0 at seed time; no DB persistence needed for P0 |
| **Migration/compatibility risks** | In-situ savings recorded with 100.0 rate will be corrected to 1.0 — historical entries remain at old values (no retroactive fix needed) |
| **Tests/evidence required** | Verify record_in_situ_savings() produces correct GCC amount at 1:1; verify no other callers of exchange_rate_to_gcc exist or are affected |
| **P0 blocker level** | HIGH — 100.0 directly contradicts 1:1 peg and affects NPC economic calculations |
| **Non-goals** | Does not synchronize ExchangeRateService with ExchangeRate model; does not design dynamic exchange-rate system |

#### Option 2: Fix VirtualLedgerService to Return Hardcoded 1.0

| Criterion | Detail |
|-----------|--------|
| **Affected economic operations** | Same as Option 1 |
| **Persistence/synchronization assumptions** | No external dependency; method returns literal 1.0 |
| **Migration/compatibility risks** | Same as Option 1 — historical entries remain at old values |
| **Tests/evidence required** | Verify return value is 1.0; verify record_in_situ_savings() produces correct GCC amount |
| **P0 blocker level** | HIGH — same correction needed as Option 1 |
| **Non-goals** | Does not integrate with ExchangeRateService or ExchangeRate model; does not support future rate changes |

#### Option 3: Unified Single Source (ExchangeRateService for All Conversions)

| Criterion | Detail |
|-----------|--------|
| **Affected economic operations** | In-situ savings, NPC trade/market pricing, cost comparison/reporting — all use ExchangeRateService |
| **Persistence/synchronization assumptions** | ExchangeRateService remains in-memory; rate set to 1.0 at seed time; VirtualLedgerService delegates to it |
| **Migration/compatibility risks** | ExchangeRate model (DB) remains for bonds only; no synchronization needed between systems for P0 |
| **Tests/evidence required** | Verify all conversion paths use ExchangeRateService; verify 1:1 rate is set at seed time; verify in-situ savings correct |
| **P0 blocker level** | HIGH — resolves both the 100.0 bug AND the dual-system inconsistency for P0 scope |
| **Non-goals** | Does not synchronize ExchangeRateService with ExchangeRate model; does not design dynamic exchange-rate system |

---

## E. Multi-Currency Account Contract

### Minimum Account/Transaction Behavior for GCC and USD (P0)

| Aspect | P0 Requirement | Future Extensibility |
|--------|---------------|---------------------|
| **Deposits** | account.deposit(amount, currency, description) — creates LedgerEntry with currency field | Currency field is extensible; no schema change needed for new currencies |
| **Transfers** | account.transfer(to_account, amount, currency) — moves existing GCC/USD between accounts | Same method works for any currency in Financial::Currency table |
| **Conversion** | Financial::ExchangeRateService.convert(amount, from_currency, to_currency) — stateless lookup | ExchangeRateService can be extended with new currency pairs without account changes |
| **Balance display** | Financial::Account.balance(currency_id) — returns per-currency balance | Per-currency balance is already supported by Financial::Account design |
| **Precision** | GCC: 8 decimals; USD: 2 decimals — stored as decimal in database, formatted at display layer | New currencies can define precision in Financial::Currency table |
| **Currency identity** | Financial::Currency records with symbol, is_system_currency, issuer fields | Extensible — new currencies added as new Currency records |

### P0-Required vs. Future Extensibility

| Area | P0-Required | Future Extensibility |
|------|------------|---------------------|
| GCC and USD accounts | ✅ Financial::Account supports per-currency balances | Additional currencies: add to Financial::Currency table, no account schema change |
| Per-currency balance queries | ✅ balance(currency_id) already works | Same method for any currency |
| Exchange rate lookup | ✅ ExchangeRateService.convert() already works | Add new currency pairs to rates hash or DB |
| LedgerEntry currency field | ✅ Already stores currency reference | Extensible by design |
| **Database schema** | ✅ No redesign needed — current schema supports multi-currency | Future currencies: no migration required |

---

## F. Documentation Decision Inventory

### Known Remaining Terminology Issues (Not Yet Corrected)

| # | Path/Section | Exact Phrase | Classification | Suggested Neutral Wording |
|---|-------------|-------------|---------------|--------------------------|
| 1 | `04-bonds-and-financing.md` §3.2 | "GCC Mining Bonds" / "collateral: crypto_mining_satellite_01" | **Wording-only** — could conflate GCC with physical commodity collateral | "Bonds denominated in GCC, secured by the satellite asset itself (not GCC as collateral)" |
| 2 | `03-market-and-pricing.md` §1 | "GCC supply backed by Luna's productive capacity" | **Wording-only** — "backed by" implies commodity backing (gold standard) | "GCC supply grows through authorized LDC issuance, anchored to Luna's productive capacity as the economy's growth indicator" |
| 3 | `02-currencies-and-accounts.md` §5 (Virtual Ledger) | No deferred marker on virtual-ledger section | **Missing scope marker** — readers may mistake bootstrap-only mechanism for full design | Add: "**DEFERRED**: Full virtual-ledger redesign out of scope. Future ledger-architecture task will address obligation visibility, deficit thresholds." |
| 4 | `02-currencies-and-accounts.md` §161 | "ExchangeRateService adjusts rate based on GCC supply vs. demand" | **Incorrect current-code claim** — no such logic exists in the service code (it's a static hash with default 1:1) | Remove or mark as exploratory/deferred design |

### Terminology Guidance (Settled Record)

When documenting or discussing GCC, use terminology consistent with the settled record:

- **GCC mining** means simulated compute-based issuance, not physical mineral extraction
- **Crypto-mining satellites** and authorized data centers are issuance infrastructure
- All newly mined GCC flows to the LDC account
- Physical extraction produces materials/cargo — never directly creates GCC
- The USD:GCC peg remains 1:1 for the bootstrap period
- GCC is a centrally managed, crypto-inspired virtual ledger currency using LDC-controlled simulated compute mining for issuance

### Already-Corrected Items
- `GAPS.md` Gap H: "Emission Schedule Enforcement" → "GCC Issuance Schedule" — ✅ Committed (`0e4f67be`)

---

## G. Dependency-Ordered Implementation Plan

### P0 Must-Have Work

#### Task 1: Fix VirtualLedgerService.exchange_rate_to_gcc (P0 HIGH)

| Criterion | Detail |
|-----------|--------|
| **Problem** | Returns hardcoded 100.0, contradicting 1:1 bootstrap peg; in-situ savings undervalued by 2 orders of magnitude |
| **Affected systems** | VirtualLedgerService, record_in_situ_savings(), any code calling exchange_rate_to_gcc |
| **Dependencies on human decisions** | Option selection (delegate to ExchangeRateService vs. hardcoded 1.0) |
| **Expected behavior** | Returns 1.0 (or delegates to ExchangeRateService which returns 1.0 at seed time) |
| **Acceptance criteria** | record_in_situ_savings(100 USD) produces 100 GCC (not 1); no regression in other economic operations |
| **Evidence/tests required** | Unit test for exchange_rate_to_gcc; integration test for record_in_situ_savings at 1:1 |
| **Luna blocker classification** | BLOCKER — NPC economic calculations are incorrect without this fix |
| **Explicit non-goals** | Does not synchronize ExchangeRateService with ExchangeRate model; does not design dynamic exchange-rate system |

#### Task 2: Enforce LDC Issuance Authority for GCC Mining (P0 MEDIUM)

| Criterion | Detail |
|-----------|--------|
| **Problem** | "LDC sole issuer" is policy intent but not code-enforced; any account reference can deposit GCC |
| **Affected systems** | Financial::Account#deposit, CryptocurrencyMining#mine_gcc, mine_gcc callers |
| **Dependencies on human decisions** | Approach selection (ownership-gate vs. dedicated issuance method vs. audit-only) |
| **Expected behavior** | Only LDC-owned or LDC-authorized accounts can create GCC via mining; non-LDC entities cannot deposit GCC as issuance |
| **Acceptance criteria** | LDC satellite mine_gcc works unchanged; non-LDC entity cannot deposit GCC as issuance; existing tests pass |
| **Evidence/tests required** | Verify LDC authorization check in mine_gcc or deposit path; verify existing mining behavior preserved for LDC satellites |
| **Luna blocker classification** | NOT BLOCKER — does not block NPC economic simulation but enforces design intent |
| **Explicit non-goals** | Does not design public blockchain, proof-of-work, halving schedule, or multi-entity mint governance |

### P0 Optional Cleanup

#### Task 3: Document Dual-Time Model (P0 LOW)

| Criterion | Detail |
|-----------|--------|
| **Problem** | Production uses game-time; construction/missions use real-world days; mining uses independent loop — no unified documentation |
| **Affected systems** | Documentation only |
| **Dependencies on human decisions** | Option selection from Section B (unified, dual-time, or hybrid) |
| **Expected behavior** | Clear documentation of each system's time basis and conversion requirements for AI Manager cost comparison |
| **Acceptance criteria** | Wiki section documents all timing sources; AI Manager cost comparison guidance included |
| **Evidence/tests required** | None — documentation only |
| **Luna blocker classification** | NOT BLOCKER |
| **Explicit non-goals** | Does not change any timing behavior; does not implement unified time model |

### Post-P0 / Deferred Work

#### Task 4: Synchronize ExchangeRateService with ExchangeRate Model (Deferred)

| Criterion | Detail |
|-----------|--------|
| **Problem** | Two disconnected exchange-rate systems; rate changes via one invisible to the other |
| **Affected systems** | ExchangeRateService, ExchangeRate model, all conversion callers |
| **Dependencies on human decisions** | Policy decision on single source of truth for exchange rates |
| **Expected behavior** | Rate changes propagate between in-memory service and DB model |
| **Acceptance criteria** | Rate set via either system is visible to both; no stale rate reads |
| **Evidence/tests required** | Integration test verifying rate sync across systems |
| **Luna blocker classification** | NOT BLOCKER for P0 — deferred to future task |
| **Explicit non-goals** | Does not design dynamic exchange-rate system; does not redesign financial architecture |

#### Task 5: Align Mining Rake with Game-Time (Deferred)

| Criterion | Detail |
|-----------|--------|
| **Problem** | Mining rake uses independent game_days loop counter, not game_state.seconds_per_game_day |
| **Affected systems** | gcc_mining_sat.rake, mining timing semantics |
| **Dependencies on human decisions** | Option selection from Section B (unified, dual-time, or hybrid) |
| **Expected behavior** | Mining rake reads game_state for elapsed calculation; aligns with production simulation |
| **Acceptance criteria** | Mining output scales correctly with game speed; consistent with production timing |
| **Evidence/tests required** | Multi-speed test verifying mining output at speeds 1-5 |
| **Luna blocker classification** | NOT BLOCKER for P0 — deferred unless AI Manager requires unified comparison |
| **Explicit non-goals** | Does not change rake task behavior without human decision; does not redesign mining loop |

---

## End Summary

### Work Completed This Update

| Section | Change | Status |
|---------|--------|--------|
| **Human Decisions Settled** (NEW) | 8 settled decisions recorded as authoritative record | ✅ ADDED |
| **Section A — Current-State Contract** | GCC identity terminology updated to "centrally managed, crypto-inspired virtual ledger currency using LDC-controlled simulated compute mining for issuance"; VirtualLedgerService 100.0 labeled confirmed defect; bootstrap peg language strengthened | ✅ UPDATED |
| **Section B — Future-Reference Peg Note** (NEW) | Locked 1:1 rule; qualitative maturity indicators; LDC-managed band as nonbinding reference | ✅ ADDED |
| **Section D — Exchange-Rate Decision Packet** | Non-dispatched task draft added for VirtualLedgerService 100.0 defect with problem statement, actual/required behavior, acceptance criteria, non-goals, dependencies, Luna impact | ✅ ADDED |
| **Section F — Terminology Guidance** (NEW subsection) | 6-item guidance list reflecting settled record; preserves existing 4-item inventory intact | ✅ ADDED |

### Files Modified

- `projects/galaxy_game/summaries/2026-09-15-GCC-P0-PLANNING-DECISION-PACKET.md` — planning decision packet (this file only)

### Sections Added

1. **Human Decisions Settled This Session** — 8 authoritative decisions from Tracy's policy guidance
2. **Future-Reference Peg Note** (end of Section B) — locked bootstrap rule, deferred uncoupling indicators, nonbinding band reference
3. **Non-Dispatched Task Draft** (Section D) — VirtualLedgerService 100.0 defect alignment task
4. **Terminology Guidance** (Section F) — settled-record terminology recommendations

### Confirmation: No Implementation Work Performed

- ✅ No source code modified
- ✅ No tests modified
- ✅ No blueprints modified
- ✅ No operational data modified
- ✅ No wiki pages modified
- ✅ No configuration files modified
- ✅ No task state/lifecycle files altered
- ✅ No implementation work dispatched
- ✅ No task files created, moved, or closed
- ✅ Planning session remains active

### Remaining Open Human Decisions

| # | Decision Required | By Whom | Priority |
|---|------------------|---------|----------|
| 1 | **Exchange-rate fix approach**: Delegate VirtualLedgerService to ExchangeRateService, or hardcoded 1.0? | Tracy | HIGH |
| 2 | **LDC issuance enforcement approach**: Ownership-gate on deposit, dedicated issuance method, or audit-only? | Claude (financial architecture) + Tracy | MEDIUM |
| 3 | **Time-model option**: Unified game-time, dual-time, or hybrid for construction/missions/mining? | Tracy + Gemini | MEDIUM |
| 4 | **Wiki terminology corrections**: Approve suggested neutral wording for 4 remaining conflicts? | Tracy | LOW |
| 5 | **Non-dispatched task draft approval**: Approve VirtualLedgerService alignment task scope before dispatch? | Tracy | HIGH |

### Explicit Statement

**No code, wiki, documentation (beyond this planning artifact), operational data, or tracked-task changes were made.** This is a read-only planning and human-decision artifact only. No files were staged, committed, reset, formatted, or branched. No work was dispatched. Gemini's economic-design conclusions and Tracy's policy decisions remain unanswered. Phase 2–3 monetary questions are deferred. Mars-specific implementation is deferred. Player UI/lore work is deferred.
