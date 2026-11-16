# GCC P0 Verification Evidence Packet

**Date**: 2026-09-15  
**Agent**: Perplexity (read-only verification)  
**Scope**: Five claims — no code/doc/task/wiki modifications  

---

## Claim 1: Simulation Cadence

### Result: CONFIRMED

### Source
- **File**: `galaxy_game/app/models/game_state.rb`
- **Method**: `seconds_per_game_day` (lines 49-60)
- **Caller**: `update_time!` at line 35; `GameSimulationJob#perform` at line 19

### Governing Logic
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

At speed=3 (default): **`seconds_per_game_day = 60`** (1 real minute = 1 game day).

### Scope
- `GameState#speed` is a database column with validation: `numericality: { greater_than: 0, less_than_or_equal_to: 5 }` (game_state.rb line ~8)
- Default speed=3 set in `set_defaults` callback (game_state.rb line ~24)
- No admin UI exists — raw DB column only
- The "86400/game_speed" formula does NOT exist anywhere in the codebase

### Stale Comment Found
- **File**: `galaxy_game/spec/integration/game_loop_integration_spec.rb` line 28
- **Comment**: `# If speed=3 and seconds_per_game_day = 86400/3 = 28800, we need 28800+ seconds elapsed`
- **Status**: INCORRECT — this comment was based on a wrong assumption. The actual method returns 60, not 28800.

### Test Evidence
- `GameSimulationJob#perform` (game_simulation_job.rb line 19):
  ```ruby
  days_to_simulate = (elapsed_seconds / game_state.seconds_per_game_day).to_i
  ```
  Uses integer division — whole days only, never fractions.
- Job self-schedules via `GameSimulationJob.perform_in(1.minute)` (line 42)
- Initial trigger: 10 seconds after Rails boot (documented in prior session; source-trace confirmed)

### Limitations
- No unit test directly asserts `seconds_per_game_day` returns 60 at speed=3
- The stale comment in the integration spec is misleading but does not affect runtime behavior

---

## Claim 2: Mining-Rate Disconnection

### Result: CONFIRMED

### Source
- **recalculate_stats**: `galaxy_game/app/models/craft/base_craft.rb` lines ~372-389
- **mine_gcc**: `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` line 10
- **MiningUnitAdapter**: `cryptocurrency_mining.rb` lines 293-325 (inner class)

### recalculate_stats Behavior
```ruby
def recalculate_stats
  base_mining_rate = operational_data.dig('operational_properties', 'base_mining_rate_gcc_per_hour') || 0
  computer_units = base_units.select { |u| u.unit_type.include?('computer') }
  computer_boost = computer_units.sum { |u| u.operational_data.dig('operational_properties', 'mining_boost_gcc_per_hour').to_f }
  gpu_rigs = rigs.select { |r| r.rig_type == 'gpu_coprocessor_rig' }
  gpu_boost = gpu_rigs.sum { |r| r.operational_data.dig('operational_properties', 'processing_boost_gcc_per_hour').to_f }
  rigged_computer_boost = computer_units.count * gpu_boost
  total_mining_rate = base_mining_rate + computer_boost + rigged_computer_boost
  operational_data['operational_properties']['current_mining_rate_gcc_per_hour'] = total_mining_rate.round(2)
  save!
end
```

**Stores result in**: `operational_data['operational_properties']['current_mining_rate_gcc_per_hour']`

### mine_gcc Behavior (What It Actually Reads)
```ruby
def mine_gcc
  # ... power checks ...
  total_mined = 0
  mining_units.each do |unit|
    base_amount = unit.mine(mining_difficulty, unit_efficiency)
    enhanced_amount = apply_mining_effects(base_amount)
    total_mined += enhanced_amount
  end
  # ... ledger write: account.deposit(total_mined, "GCC Mining Operation") ...
end
```

`mining_units` returns `MiningUnitAdapter` instances. Each adapter's `mine()` method reads from the **individual unit's** operational data via fallback chain:
```ruby
def extract_mining_rate
  return 45.0 unless @unit.respond_to?(:operational_data)
  @unit.operational_data&.dig('mining', 'base_rate_gcc_per_hour') || 
  @unit.operational_data&.dig('operational_properties', 'base_mining_rate_gcc_per_hour') ||
  @unit.operational_data&.dig('performance', 'mining_power') ||
  @unit.operational_data&.dig('mining', 'gcc_per_hour') || 45.0
end
```

### Caller/Search Summary
- **recalculate_stats callers**: Called when units are added/removed from a craft (HasUnits concern). No other consumers discovered in source-trace.
- **mine_gcc callers**: Manual rake tasks, Sidekiq jobs (MineGccJob — has fatal nil-receiver bug, never successfully fired), SatelliteMiningSchedulerJob (active hourly queuer with broken deduplication).
- **No code path connects the two methods**: No call from `mine_gcc` to `recalculate_stats`, no read of `current_mining_rate_gcc_per_hour` by `mine_gcc`.

### Does base_mining_rate_gcc_per_hour Affect GCC Ledger Issuance?
**NO.** The satellite-level `base_mining_rate_gcc_per_hour: 1000` field is read by `recalculate_stats` but NEVER consumed by `mine_gcc`. It is dead/unconsumed design data for mining output.

### Limitations
- Repository-reference audit of all `recalculate_stats` consumers is incomplete — may have other consumers not yet discovered
- Runtime differential validation (controlled tests with different fits) has not been executed

---

## Claim 3: Exchange-Rate Systems

### Result: CONFIRMED — Two Disconnected Systems

### System A: Financial::ExchangeRateService (In-Memory)
- **File**: `galaxy_game/app/services/financial/exchange_rate_service.rb`
- **Storage**: In-memory hash `@rates` — lost on restart
- **Default**: 1:1 (`get_rate` returns `@rates[key] || 1.0`)
- **Class**: `ExchangeRateService` (inside `Financial` module)
- **Key methods**: `convert(amount, from, to)`, `get_rate(from, to)`, `set_rate(from, to, rate)`

**Callers**:
| Caller | File:Line | Usage |
|--------|-----------|-------|
| `NpcPriceCalculator` | (referenced in wiki docs as required consumer) | Pricing conversions |
| `VirtualLedgerService` | (indirectly — see below) | Not used; uses its own hardcoded rate |
| `system_intelligence_service.rb:88` | `exchange_service = Financial::ExchangeRateService.new` | AI Manager intelligence |
| `economic_stress_test.rake:285` | Test harness | Stress testing |
| `cislunar_infrastructure.rake` | (referenced in wiki docs) | Seed-time setup |

### System B: Financial::ExchangeRate Model (PostgreSQL DB)
- **File**: `galaxy_game/app/models/financial/exchange_rate.rb`
- **Storage**: PostgreSQL DB via ApplicationRecord
- **Default**: 1.0 (`get_rate` returns `rate_record&.rate || 1.0`)
- **Class**: `Financial::ExchangeRate < ApplicationRecord`
- **Key methods**: `self.get_rate(from_symbol, to_symbol)`, `self.set_rate(from_symbol, to_symbol, rate)`

**Callers**:
| Caller | File:Line | Usage |
|--------|-----------|-------|
| `cislunar_infrastructure.rake:65-66` | `Financial::ExchangeRate.set_rate('GCC', 'USD', 1.0)` | Seed-time setup |
| Wiki docs (02-currencies-and-accounts.md:271, 387; 04-bonds-and-financing.md:168) | Referenced in documentation | Bond repayment conversion |

### Synchronization/Conversion Integration
**NONE.** The two systems are completely disconnected:
- `ExchangeRateService` uses an in-memory hash; `ExchangeRate` model uses the database
- Calling `ExchangeRate.set_rate()` does NOT update `ExchangeRateService`'s rates, and vice versa
- This is a design gap, not an intentional separation

### VirtualLedgerService.exchange_rate_to_gcc Classification
- **File**: `galaxy_game/app/services/financial/virtual_ledger_service.rb` lines 93-96
- **Code**:
  ```ruby
  def self.exchange_rate_to_gcc
    # Assume 1 USD = 100 GCC or something
    100.0
  end
  ```
- **Classification**: **STALE TEST CODE BUG** — The comment "Assume 1 USD = 100 GCC or something" is unmistakably stub/test code that was never cleaned up. It returns a hardcoded `100.0` instead of delegating to either exchange-rate system.
- **Impact**: `record_in_situ_savings()` (line 67) calls `savings_usd / exchange_rate_to_gcc`, meaning in-situ savings are recorded at **1/100th** of their USD value in GCC.

### Limitations
- `NpcPriceCalculator` usage of ExchangeRateService is documented in wiki but not traced to specific source lines in this verification pass
- The wiki claims `ExchangeRateService` "adjusts rate based on GCC supply vs. demand" (02-currencies-and-accounts.md:161) — no such logic exists in the service code

---

## Claim 4: GCC Classification

### Result: PARTIAL — Code supports "ledger currency" but does NOT enforce "LDC sole issuer"

### Evidence Supporting Fiat-Style Ledger Currency
| Finding | Source |
|---------|--------|
| GCC is deposited via `account.deposit(total_mined, "GCC Mining Operation")` | cryptocurrency_mining.rb:53 |
| GCC balance tracked in `self.funds += total_mined` | cryptocurrency_mining.rb:54-55 |
| MiningLog records `amount_mined`, `currency: 'GCC'` | cryptocurrency_mining.rb:60+ |
| No cargo/inventory/material model for GCC anywhere | Source-trace confirmed — GCC never appears as physical material |
| Financial::Account, Financial::Currency models exist | Economy wiki docs confirm GCC/USD are Currency records |

### Evidence Against "LDC Sole Issuer" Enforcement
| Finding | Source |
|---------|--------|
| `account.deposit()` is a generic ledger method — no issuer check | No code path validates that only LDC can create GCC |
| Integration tests deposit GCC directly: `ldc_gcc_account.depit(initial_gcc_amount, ...)` | Multiple integration-test files (gcc_mining_sat.rb:382, 465; exchange_rate_integration.rb:372) |
| AI bootstrap rake deposits: `@ldc_gcc_account.deposit(initial_gcc_funding, "AI Bootstrap Initial GCC Fund")` | ai_manager_sol_test.rake:212 |
| No mint/issuance gate exists in code | Source-trace confirmed — any code with an account reference can deposit GCC |

### Assessment
- **"GCC is fiat-style virtual ledger currency"**: **CONFIRMED** by source-trace. GCC exists only as ledger entries (Financial::Account deposits), never as physical material/cargo/inventory.
- **"LDC is sole issuer"**: **NOT ENFORCED in code**. The classification is a policy/design intent documented in wiki, but the code has no mint gate or issuer validation. Any account can receive GCC deposits.

### Limitations
- This verification is source-trace only; runtime behavior (who actually calls deposit) would require execution tracing
- "Sole issuer" may be enforced at a higher architectural level (e.g., game initialization, admin-only rake tasks) not visible in source alone

---

## Claim 5: Terminology Inventory

### Result: PARTIAL — Several conflicts identified

| Path/Page | Phrase | Conflict Type | Suggested Neutral Wording |
|-----------|--------|---------------|--------------------------|
| `04-bonds-and-financing.md` §3.2 | "GCC Mining Bonds" / "collateral: crypto_mining_satellite_01" | Conflates GCC with physical commodity collateral | "Bonds denominated in GCC, secured by the satellite asset itself (not GCC as collateral)" |
| `GAPS.md` Gap H (renamed) | Previously "Emission Schedule Enforcement" | "Emission" reads as physical gas emission | Already corrected to "GCC Issuance Schedule" ✅ |
| `03-market-and-pricing.md` | "GCC supply backed by Luna's productive capacity" | "Backed by" implies commodity backing (gold standard) | "GCC supply grows through authorized LDC issuance, anchored to Luna's productive capacity as the economy's growth indicator" |
| `02-currencies-and-accounts.md` §5 Virtual Ledger | No deferred marker on virtual-ledger section | Readers may mistake bootstrap-only mechanism for full design | Add: "**DEFERRED**: Full virtual-ledger redesign out of scope. Future ledger-architecture task will address obligation visibility, deficit thresholds." |
| `02-currencies-and-accounts.md` §1 | "GCC is a fiat-style virtual ledger currency" | Accurate but may need clarification that "fiat-style" means LDC-authorized issuance, not cryptographic proof-of-work | Add: "Issuance is authorized by LDC via mining satellites; 'mining' is in-universe terminology for authorized currency creation, not physical extraction." |
| `GAPS.md` Gap I | Accurate source-trace finding | No conflict — correctly documents the disconnection | ✅ No change needed |
| Wiki docs referencing "ExchangeRateService adjusts rate based on GCC supply vs. demand" (02-currencies-and-accounts.md:161) | Implies dynamic pricing mechanism exists | No such logic exists in the service code; it's a static hash with default 1:1 | Remove or mark as exploratory/deferred design |

### Limitations
- This inventory covers economy wiki pages only (`docs/wiki_reorganization/economy/`). Other docs (architecture intent files, phase alignment reports) may contain additional terminology issues not scanned here.
- "Suggested neutral wording" is advisory — no edits applied per read-only constraint.

---

## Summary Table

| Claim | Result | Key Finding |
|-------|--------|-------------|
| 1. Simulation cadence (speed=3 = 60s/game day) | **CONFIRMED** | `game_state.rb` case statement; no "86400/game_speed" formula exists in code |
| 2. Mining-rate disconnection | **CONFIRMED** | `recalculate_stats` and `mine_gcc` are two independent methods with no data flow between them |
| 3. Two exchange-rate systems | **CONFIRMED** | ExchangeRateService (in-memory, default 1:1) + ExchangeRate model (DB, default 1.0); zero synchronization |
| 4. GCC = fiat ledger currency; LDC sole issuer | **PARTIAL** | Fiat ledger confirmed; "LDC sole issuer" is policy intent, not code-enforced |
| 5. Terminology conflicts | **PARTIAL** | 3 remaining wiki conflicts identified (bonds collateral language, "backed by", dynamic pricing claim); 1 already corrected ("emission" → "issuance") |

---

## Limitations of This Verification

1. **Source-trace only**: No runtime execution tracing was performed
2. **Repository-reference audit incomplete**: Some methods (e.g., `recalculate_stats` consumers) may have undiscovered callers
3. **Integration tests not executed**: Stale comments in test files (game_loop_integration_spec.rb:28) were identified but not corrected
4. **Higher-level enforcement unknown**: "LDC sole issuer" policy may be enforced at game initialization or admin level not visible in source alone
