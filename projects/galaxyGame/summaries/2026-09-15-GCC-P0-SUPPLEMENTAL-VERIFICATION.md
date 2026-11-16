# GCC P0 Supplemental Verification Evidence

**Date**: 2026-09-15  
**Agent**: Perplexity (read-only repository verifier)  
**Scope**: Supplement `2026-09-15-GCC-P0-VERIFICATION-EVIDENCE-PACKET.md`  
**Constraint**: Read-only. No code/doc/task/wiki changes. No staging, committing, resetting, formatting, branch creation, or work dispatching.

---

## Section 1: Cadence Scope and Stale 28800 Reference

### Result: CONFIRMED — speed=3 = 60 real seconds/game day; 28800 is comment-only stale artifact

### Governing Configuration/Logic
- **File**: `galaxy_game/app/models/game_state.rb`
- **Method**: `seconds_per_game_day` (lines 49-60)
- **Field**: `speed` — database column, validated: `numericality: { greater_than: 0, less_than_or_equal_to: 5 }`
- **Default**: Set in `set_defaults` callback (line ~24): `self.speed ||= 3`

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

### Runtime Loops — Timing Source Enumeration

| Loop Category | Timing Source | Uses `seconds_per_game_day`? | Separate Timer? |
|---------------|--------------|-----------------------------|-----------------|
| **Production simulation** | `GameSimulationJob#perform` (game_simulation_job.rb:19) | YES — `days_to_simulate = (elapsed_seconds / game_state.seconds_per_game_day).to_i` | NO |
| **Mining** | Manual rake (`gcc_mining_sat.rake`) + Sidekiq jobs (MineGccJob, SatelliteMiningSchedulerJob) | NO — rake uses `game_days.times` loop with manual iteration; jobs call `mine_gcc()` directly | YES — rake has its own `ENV['GAME_DAYS']` counter; jobs are cron-driven independently |
| **Construction** | `MegaProject#days_remaining` (mega_project.rb:32) | NO — uses `(deadline - Time.current).to_i / 86400.0` (real-world days) | YES |
| **Missions** | `Mission.average('EXTRACT(EPOCH FROM (updated_at - created_at))/86400')` (ai_manager_controller.rb:178) | NO — uses real-world EPOCH/86400 for timeline stats | YES |
| **Upkeep** | Not found as a distinct loop in source-trace | N/A | N/A |
| **Logistics** | TerraSim idle body simulation (game_simulation_job.rb:32-40) | NO — uses `rand < 0.1` probability, simulates 1 day per idle body | YES |
| **Markets** | `NpcPriceCalculator` via `Financial::ExchangeRateService` | NO — pricing is stateless lookup, not time-driven | N/A |
| **Finance** | Bond repayment via `Financial::ExchangeRate.get_rate()` (cislunar_infrastructure.rake:65-66) | NO — rate lookup is synchronous, not time-driven | N/A |
| **Scheduled/background jobs** | `GameSimulationJob.perform_in(1.minute)` (game_simulation_job.rb:42) | YES — job itself uses `seconds_per_game_day` for day calculation | Job interval is hardcoded 1 minute; does NOT derive from `seconds_per_game_day` |

### Search Results for Timing Keywords
| Pattern | Matches Found | Context |
|---------|--------------|---------|
| `86400` | 7 matches | ai_manager_controller.rb:178 (Mission timeline avg), mega_project.rb:32 (days_remaining), station_cost_benefit_analyzer.rb:484-486 (construction/critical/optimal days), game_loop_integration_spec.rb:28 (stale comment) |
| `28800` | 1 match | game_loop_integration_spec.rb:28 ONLY — stale comment |
| `game_speed` | 0 matches in code | Only appears in documentation/handoff files |
| `speed=3` | 0 matches as literal | Only appears in documentation/handoff files |
| `tick` | 2 matches | digital_twins_controller.rb:35,184 — `run_simulation_tick` for Digital Twin sandbox only (isolated testing) |
| `interval` | 0 matches | Not found as a timing constant |
| `elapsed time` / `elapsed_seconds` | 2 matches | game_state.rb:35, game_simulation_job.rb:19 — both use `seconds_per_game_day` |
| `game day` / `game_days` | Multiple | Integration tests (exchange_rate_integration.rb, gcc_mining_sat_original2.rb) and rake tasks use `game_days` as a loop counter, NOT derived from `seconds_per_game_day` |
| `game hour` | 0 matches | Not found |
| `simulation time` | 0 matches | Not found |
| `time_scale` | 1 match | terra_sim/planet_update_service.rb:8 — `@time_scale = celestial_body.time_scale || 1` (TerraSim planet-specific adjustment, NOT game simulation) |

### game_loop_integration_spec.rb:28 — Exact Classification

**File**: `galaxy_game/spec/integration/game_loop_integration_spec.rb` line 28  
**Content**: `# If speed=3 and seconds_per_game_day = 86400/3 = 28800, we need 28800+ seconds elapsed`

**Classification**: **COMMENT-ONLY stale artifact** — not executable test expectation, not fixture/calculation dependency, not runtime-affecting configuration.

**Evidence**:
- Line is inside a `#` comment block (lines 25-28)
- The actual code on line 30 sets `last_updated_at: 1.hour.ago` — this produces 3600 seconds elapsed
- With `seconds_per_game_day = 60`, 3600/60 = 60 game days simulated (not 1 as the comment assumes)
- The test does NOT assert any specific day count; it creates a satellite and runs integration tests
- Line 172 logs `game_state.seconds_per_game_day` at runtime — this would reveal the actual value if the test output were examined

**What the evidence proves**: The comment reflects an incorrect assumption about how game days work. It does not affect test behavior because it is a comment, not executable code.

**What remains unproven**: Whether any other integration tests or rake tasks carry the same incorrect assumption in comments or documentation.

---

## Section 2: Mining Time Basis and Full Output Trace

### Result: CONFIRMED — complete runtime disconnection proven for direct paths; indirect consumers of `recalculate_stats`/`current_mining_rate_gcc_per_hour` not fully audited

### Rate Field Classification

| Field | Actual Payout Input | Stats-Only | AI/Planning Input | Display-Only | Test-Only | Fallback-Only | Unconsumed |
|-------|-------------------|------------|-------------------|-------------|-----------|--------------|------------|
| `base_mining_rate_gcc_per_hour` (satellite operational data) | ❌ | ✅ recalculate_stats only | ❓ unknown | ❓ unknown | ❌ | ❌ | ✅ for mine_gcc |
| `current_mining_rate_gcc_per_hour` (recalculate_stats output) | ❌ | ✅ stored by recalculate_stats | ❓ unknown | ✅ likely display | ❌ | ❌ | ✅ for mine_gcc |
| Individual unit `mining_rate_value` / `operational_data.dig('mining', 'base_rate_gcc_per_hour')` etc. | ✅ MiningUnitAdapter.mine() | ❌ | ❌ | ❌ | ❌ | ✅ (45.0 fallback) | ❌ |
| `MiningUnitAdapter.mine(difficulty, efficiency)` | ✅ mine_gcc consumes via mining_units.each | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `mine_gcc` total_mined | ✅ account.deposit() + self.funds += | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| 45.0 fallback in MiningUnitAdapter.extract_mining_rate | ✅ Used when no operational_data mining rate found | ❌ | ❌ | ❌ | ❌ | ✅ default | ❌ |

### "Per Hour" Meaning at Runtime

| Value | Per-Hour Basis | Evidence |
|-------|---------------|----------|
| `base_mining_rate_gcc_per_hour` (satellite operational data) | **Unknown** — field name says "per hour" but no code traces its time basis | Dead/unconsumed for mining output |
| `current_mining_rate_gcc_per_hour` (recalculate_stats output) | **Unknown** — same naming convention, same uncertainty | Dead/unconsumed for mining output |
| Individual unit operational_data mining rates | **Unknown** — field names say "per hour" but no time-basis documentation | Active in MiningUnitAdapter |
| `mine_gcc` total_mined output | **Per-operation** (not per-hour) — the 0.18 multiplier converts hourly rate to per-operation deposit | cryptocurrency_mining.rb:53-54 |

### Exact Payout Formula in Code Terms

```ruby
# mining_units = MiningUnitAdapter instances for each fitted computer unit
total_mined = 0
mining_units.each do |unit|
  # Step 1: Extract base rate from unit operational data (fallback chain)
  base_rate = if unit.respond_to?(:mining_rate_value)
    unit.mining_rate_value
  else
    unit.operational_data&.dig('mining', 'base_rate_gcc_per_hour') ||
    unit.operational_data&.dig('operational_properties', 'base_mining_rate_gcc_per_hour') ||
    unit.operational_data&.dig('performance', 'mining_power') ||
    unit.operational_data&.dig('mining', 'gcc_per_hour') ||
    45.0  # fallback default
  end
  
  # Step 2: Apply difficulty and efficiency
  base_amount = base_rate * difficulty * efficiency_multiplier
  
  # Step 3: Apply thermal multiplier (from radiator modules)
  thermal_multiplier = calculate_thermal_efficiency_boost  # e.g., 1.0 + (heat_dissipation_kw / 10.0) * 0.02
  enhanced_amount = base_amount * thermal_multiplier
  
  # Step 4: Apply processing multiplier (from GPU rigs)
  processing_multiplier = calculate_processing_boost
  enhanced_amount *= processing_multiplier
  
  # Step 5: Apply direct mining boost (from specialized rigs)
  direct_boost = calculate_direct_mining_boost
  enhanced_amount += direct_boost
  
  total_mined += enhanced_amount
end

# Step 6: Per-operation conversion (0.18 multiplier applied within unit.mine())
# The 0.18 converts hourly rate to per-operation deposit amount
# NOT tied to simulation tick interval or game_speed

# Step 7: Ledger write
account.deposit(total_mined, "GCC Mining Operation")
self.funds += total_mined if respond_to?(:funds)
```

### One Mining Event — End-to-End Trace

1. **Source operational data**: Each fitted computer unit's `operational_data` (JSONB column on base_units table)
2. **Adapter/calculation**: `MiningUnitAdapter.mine(difficulty, efficiency)` reads from unit's operational_data via fallback chain; applies difficulty × efficiency
3. **mine_gcc**: Iterates all mining_units, applies thermal/processing/direct multipliers, sums total_mined
4. **Final state mutation**: 
   - `account.deposit(total_mined, "GCC Mining Operation")` — creates LedgerEntry
   - `self.funds += total_mined` — updates satellite's funds column
   - `MiningLog.create!` with operational_details hash

### recalculate_stats / current_mining_rate_gcc_per_hour — Consumer Trace

| Target | Direct Consumers Found | Indirect Consumers Checked |
|--------|----------------------|---------------------------|
| `recalculate_stats` method | HasUnits concern (called on unit add/remove) | MiningUnitAdapter: NO; mine_gcc: NO; AI Manager: NO; Market: NO; UI: NO; Jobs: NO; Callbacks: HasUnits only |
| `current_mining_rate_gcc_per_hour` field | None discovered in source-trace | Same as above — no code reads this field |

### Complete Runtime Disconnection — Proven or Only Direct?

**Direct disconnection is PROVEN**: No call from `mine_gcc` to `recalculate_stats`, no read of `current_mining_rate_gcc_per_hour` by `mine_gcc`. Two independent methods with zero data flow between them.

**Complete runtime disconnection is PARTIALLY proven**: A full repository-reference audit of all `recalculate_stats` consumers is incomplete — there may be undiscovered indirect consumers (e.g., serialized output, admin UI display, AI Manager planning) not found in this source-trace pass. However, for **mining payout and ledger/balance mutation**, complete disconnection IS proven: mining never reads recalculate_stats output.

---

## Section 3: Mining Accounting and LDC Issuance Reachability

### Result: PARTIAL — mine_gcc creates GCC via ledger deposit (not transfer); no issuer gate exists in code; low-level lack of guard confirmed but demonstrable production-path bypass not proven without runtime tracing

### mine_gcc Terminal Effect

**What it does**: Creates GCC via ledger deposit + balance mutation. Combines both:
1. `account.deposit(total_mined, "GCC Mining Operation")` — creates a LedgerEntry record (ledger creation)
2. `self.funds += total_mined` — mutates the satellite's funds column (balance change)

**NOT**: A transfer of existing GCC from another account. This is net-new GCC creation via deposit.

### Credited Account/Entity, Offset/Debit, Transaction Type

| Field | Value |
|-------|-------|
| **Credited account** | `self.account` (the satellite's Financial::Account) |
| **Offset/debit/source** | None — no corresponding debit entry found in mine_gcc |
| **Entry type** | `"GCC Mining Operation"` (description string, not typed enum) |
| **Authorization context** | None — no issuer check, permission gate, or LDC validation |

### All Non-Test Callers of Financial::Account#deposit

| Caller | File:Line | Category | Context |
|--------|-----------|----------|---------|
| `CryptocurrencyMining#mine_gcc` | cryptocurrency_mining.rb:53 | Mining | Satellite mining operation |
| Integration test: `ldc_gcc_account.deposit(initial_gcc_amount, ...)` | gcc_mining_sat.rb:382 | Test | Setup seed funding |
| Integration test: `ldc_gcc_account.deposit(mined_amount_this_cycle, ...)` | gcc_mining_sat.rb:465 | Test | Mining cycle deposit |
| Integration test: `ldc_gcc_account.depit(cost_in_gcc * 2, ...)` | gcc_mining_sat.rb:501 | Test | Purchase funding |
| Integration test variants (exchange_rate_integration.rb:372, 438; gcc_mining_sat_original.rb:382,465,501; gcc_mining_sat_original2.rb:343,396; gcc_mining_sat_integration_production.rb:175,222; gcc_mining_sat_integration_production_2.rb:196,243; gcc_mining_sat_integration_simplified.rb:265,434) | Multiple | Test | Various test scenarios |
| `ai_manager_sol_test.rake` | ai_manager_sol_test.rake:212 | Seed/bootstrap | AI bootstrap initial GCC fund |

**Production callers**: Only `CryptocurrencyMining#mine_gcc` (cryptocurrency_mining.rb:53) is a production-path caller of account.deposit for GCC.

### Can Ordinary Production Player/NPC Economic Behavior Invoke GCC-Increasing Deposit Without LDC Authorization?

**Low-level lack of guard: CONFIRMED.** The `account.deposit()` method has no issuer validation, no LDC-owned account check, no permission gate, and no transaction-type constraint that restricts GCC creation to a specific entity. Any code with a reference to an account can call `.deposit()`.

**Demonstrable production-path bypass: NOT PROVEN.** Only one production caller (`mine_gcc`) deposits GCC. Whether this constitutes a "bypass" depends on whether LDC authorization is enforced at a higher architectural level (e.g., game initialization, admin-only rake tasks, or satellite deployment gates) not visible in source alone. The code itself does not prevent arbitrary GCC creation via deposit.

---

## Section 4: Exchange-Rate Caller and Liveness Matrix

### Result: CONFIRMED — two disconnected systems; VirtualLedgerService.exchange_rate_to_gcc = 100.0 is STALE TEST CODE BUG (production-active but incorrect)

### Definitions, Defaults, Reads, Writes, Persistence

| System | Definition File | Default | Persistence | Update Mechanism |
|--------|----------------|---------|-------------|-----------------|
| `ExchangeRateService` | `galaxy_game/app/services/financial/exchange_rate_service.rb` | 1:1 (`get_rate` returns `@rates[key] || 1.0`) | In-memory hash — LOST on restart | `set_rate(from, to, rate)` updates `@rates` hash |
| `ExchangeRate` model | `galaxy_game/app/models/financial/exchange_rate.rb` | 1.0 (`get_rate` returns `rate_record&.rate || 1.0`) | PostgreSQL DB (ApplicationRecord) | `set_rate(from_symbol, to_symbol, rate)` writes to DB via `first_or_initialize` |
| `VirtualLedgerService.exchange_rate_to_gcc` | `galaxy_game/app/services/financial/virtual_ledger_service.rb:93-96` | Hardcoded 100.0 | None — method returns literal | Never updated; stub code |

### Rate Direction and Currency-Pair Assumptions

| System | Direction | Pair |
|--------|-----------|------|
| ExchangeRateService | Bidirectional (any from/to) | Any currency pair, default 1:1 |
| ExchangeRate model | Bidirectional (any from/to) | Any currency pair via DB records, default 1.0 |
| VirtualLedgerService.exchange_rate_to_gcc | To GCC only | USD → GCC (assumes 1 USD = 100 GCC) |

### Caller Matrix

| Use Case | ExchangeRateService | ExchangeRate model | VirtualLedgerService.exchange_rate_to_gcc |
|----------|-------------------|-------------------|------------------------------------------|
| Player trade | Indirect use (via NpcPriceCalculator) | No use found | No use found |
| NPC trade | Active use (NpcPriceCalculator pricing) | No use found | No use found |
| Market pricing | Active use (base_price_for, price_for) | No use found | No use found |
| Import/export | Indirect use (via base_price_for) | No use found | No use found |
| Construction cost comparison | Indirect use (via base_price_for) | No use found | No use found |
| Mining/rewards | No use found | No use found | **Active use** — record_in_situ_savings() |
| Savings | No use found | No use found | **Active use** — record_in_situ_savings() |
| Bonds/financing | No use found | Active use (bond repayment conversion) | No use found |
| Account valuation | Indirect use (via base_price_for) | No use found | No use found |
| UI/display | No use found | No use found | No use found |
| Tests/fixtures | Active use (exchange_rate_integration.rb, resource_acquisition_2.rb) | Active use (cislunar_infrastructure.rake:65-66) | N/A |

### Synchronization Between ExchangeRateService and ExchangeRate

**NONE FOUND.** Checked all possible routes:
- Shared configuration: Not found
- Callback: Not found
- Cache: Not found
- Job: Not found
- Controller: Not found
- Background refresh: Not found
- Database adapter: Not found
- Seed script: Each system seeded independently (rake sets ExchangeRate via DB; ExchangeRateService initialized with empty hash)

### VirtualLedgerService.exchange_rate_to_gcc = 100.0 — Reachability Classification

**Classification**: **PRODUCTION-ACTIVE but INCORRECT** — the method is called from `record_in_situ_savings()` which is invoked during production in-situ savings recording. It is NOT test-only, seed-only, or dead/unreachable.

**Concrete numerical trace of 100 USD through record_in_situ_savings()**:
```ruby
# record_in_situ_savings(producer:, resource:, amount:, eap_price:)
savings_usd = amount * eap_price  # e.g., 100 USD
savings_gcc = savings_usd / exchange_rate_to_gcc  # 100 / 100.0 = 1.0 GCC
# LedgerEntry created with amount: 1.0 (GCC), entry_type: :in_situ_savings
```

**Impact**: In-situ savings are recorded at **1/100th** of their USD value in GCC. If the approved design peg is USD:GCC = 1:1, this is a bug that systematically undervalues in-situ production savings by two orders of magnitude.

---

## Section 5: GCC Physical-Schema and Terminology Cross-Check

### Result: PARTIAL — GCC confirmed as ledger/account unit only (not physical material); 3 remaining wiki terminology conflicts identified

### GCC Representation Search Results

| Schema Category | GCC Found? | Evidence |
|----------------|------------|----------|
| Physical material/item/cargo/resource | **NO** | No blueprint, operational data, inventory, cargo manifest, resource, recipe, extraction output, production output, trade good, market commodity, mission reward, or station/craft data references GCC as a physical entity |
| Ledger/account unit only | **YES** | Financial::Account deposits (cryptocurrency_mining.rb:53), Financial::Currency records (GCC/USD), LedgerEntry records |
| Test fixture | **YES** | Multiple integration tests use `ldc_gcc_account.deposit()` for setup |
| Documentation text | **YES** | Economy wiki docs, handoffs, architecture notes |

### Remaining Terminology Conflicts (Not Yet Corrected)

| Path/Section | Exact Phrase | Category | Suggested Neutral Wording |
|-------------|-------------|----------|--------------------------|
| `04-bonds-and-financing.md` §3.2 | "GCC Mining Bonds" / "collateral: crypto_mining_satellite_01" | Conflates GCC with physical commodity collateral | "Bonds denominated in GCC, secured by the satellite asset itself (not GCC as collateral)" |
| `03-market-and-pricing.md` §1 | "GCC supply backed by Luna's productive capacity" | Implies commodity backing (gold standard) | "GCC supply grows through authorized LDC issuance, anchored to Luna's productive capacity as the economy's growth indicator" |
| `02-currencies-and-accounts.md` §5 (Virtual Ledger) | No deferred marker on virtual-ledger section | Readers may mistake bootstrap-only mechanism for full design | Add: "**DEFERRED**: Full virtual-ledger redesign out of scope. Future ledger-architecture task will address obligation visibility, deficit thresholds." |
| `02-currencies-and-accounts.md` §161 | "ExchangeRateService adjusts rate based on GCC supply vs. demand" | Implies dynamic pricing mechanism exists; no such logic in code | Remove or mark as exploratory/deferred design |

### Already-Corrected Items

| Path/Section | Correction | Status |
|-------------|-----------|--------|
| `GAPS.md` Gap H | "Emission Schedule Enforcement" → "GCC Issuance Schedule" | ✅ Committed (`0e4f67be`) |
| `02-currencies-and-accounts.md` §1 | GCC identity clarified as fiat-style virtual ledger currency | ✅ Committed (`0e4f67be`) |
| `02-currencies-and-accounts.md` §3 | USD=GCC scope documented as bootstrap anchor only | ✅ Committed (`0e4f67be`) |

---

## End Summary

### Claims Now Fully Verified
1. **Simulation cadence**: speed=3 = 60 real seconds/game day — CONFIRMED by `game_state.rb` case statement
2. **Mining-rate disconnection**: recalculate_stats and mine_gcc are two independent methods with zero data flow — DIRECT DISCONNECTION PROVEN; complete runtime disconnection PARTIALLY proven (mining payout/balance mutation fully disconnected)
3. **Two exchange-rate systems**: ExchangeRateService (in-memory, default 1:1) + ExchangeRate model (DB, default 1.0) — CONFIRMED disconnected with zero synchronization
4. **GCC as fiat ledger currency**: CONFIRMED — exists only as Financial::Account deposits; "LDC sole issuer" is policy intent, NOT code-enforced

### Claims Still Only Partially Supported
- **"LDC is sole issuer"**: Policy/design intent documented in wiki but not enforced by any code gate. Any account reference can deposit GCC.
- **Wiki terminology conflicts**: 3 remaining conflicts identified but not yet corrected (bonds collateral language, "backed by" phrasing, virtual ledger deferred marker)

### Claims Contradicted
- None contradicted. The prior verification packet's findings are confirmed and supplemented.

### Unverified Runtime Paths
- Full repository-reference audit of all `recalculate_stats` consumers (may have undiscovered indirect consumers beyond mining payout)
- Whether LDC authorization is enforced at a higher architectural level (game initialization, admin-only gates) not visible in source alone
- Whether any other integration tests or rake tasks carry the same 28800 incorrect assumption in comments

### P0 Blockers Specifically Affecting

| Area | Blocker Status | Evidence |
|------|---------------|----------|
| **1. Physical Luna logistics/construction** | NOT BLOCKED | GCC is ledger-only; no physical material conflicts found |
| **2. NPC mining income** | NOT BLOCKED — but 0.18 time-basis unproven | Mining payout formula traced; "per hour" basis of source fields unknown; 0.18 not proven to be tied to simulation tick |
| **3. NPC market settlement** | NOT BLOCKED — but exchange-rate disconnect is a design gap | Two disconnected systems (in-memory vs DB) mean rate changes via one are invisible to the other |
| **4. AI Manager GCC-valued cost comparison** | NOT BLOCKED — but VirtualLedgerService 100.0 bug affects in-situ savings valuation | record_in_situ_savings() records at 1/100th USD value; may affect AI Manager cost-benefit analysis |

### Explicit Statement

**No code, wiki, documentation, operational data, or tracked-task changes were made.** This is a read-only report artifact only. No files were staged, committed, reset, formatted, or branched. No work was dispatched. Gemini's review conclusions and Tracy's policy decisions remain unanswered. Deferred Phase 2-3 questions remain open.
