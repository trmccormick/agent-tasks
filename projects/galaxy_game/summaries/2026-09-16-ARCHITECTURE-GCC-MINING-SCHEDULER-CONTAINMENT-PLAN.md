# GCC Mining Scheduler Containment — Architecture Plan
**Created**: 2026-09-16
**Agent Session**: Planning Agent
**Task**: `2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md`

---

## STATUS SYNTHESIS REPORT

This planning task produces a containment plan and decision matrix for GCC mining. No implementation changes are made. The report below is evidence-based from current repository state.

### Key Findings Summary
1. **Double-credit condition is CONFIRMED LIVE** — GameSimulationJob → Game#advance_by_days → process_free_crafts → craft.process_tick → mine_gcc (satellite deposit) + owner deposit executes in production when a power-positive satellite exists.
2. **Scheduler job path is BROKEN before mutation** — MineGccJob receives `satellite.id` (Integer) and calls `Integer#mine_gcc`, raising NoMethodError. Zero GCC minted via this path.
3. **Two disconnected rate calculations exist** — `recalculate_stats` vs MiningUnitAdapter with no data flow between them.

---

## Step 1 — Scheduler Containment Analysis

### Option A: Hard Disable (Early Return)
- **File**: `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb:38`
- **Change**: Add `return` at top of `#perform` or set `mining_job_queued?` to always return true
- **Rollback**: 1-line revert (remove early return)
- **Risk if containment fails**: Scheduler continues queuing jobs that fail harmlessly (nil-receiver). Low risk.
- **Repository alignment**: Consistent with temporary disable pattern used in other services

### Option B: Feature-Flag Gate
- **Files**: 
  - `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb` — read flag at top of perform
  - `config/economic_parameters.yml` or `config/application.yml` — add `mining_scheduler_enabled: false`
- **Rollback**: Change config value from `false` to `true` (1-line)
- **Risk if containment fails**: Scheduler activates uncontrolled minting path. Medium risk until recipient policy is settled.
- **Repository alignment**: `economic_parameters.yml` already exists with mining-related config (lines 253-258); pattern exists

### Option C: Sidekiq Queue Disable
- **Files**: 
  - Sidekiq cron/queue configuration (location to be verified)
  - `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb:4` — change `queue_as :scheduling` to a disabled queue name
- **Rollback**: Restore queue name + Sidekiq config (1-2 lines)
- **Risk if containment fails**: Jobs still process if Sidekiq worker is running on that queue. Depends on Sidekiq configuration.
- **Repository alignment**: Requires verifying Sidekiq cron setup; not confirmed in current codebase

### Recommendation: Option A (Hard Disable)
**Rationale**: Lowest risk, simplest rollback, no config dependency. The scheduler path is already broken and wastes Sidekiq capacity — disabling it eliminates waste without introducing new surface area. Once canonical trigger/recipient are settled, the scheduler can be re-enabled or replaced with a proper implementation.

---

## Step 2 — Upstream Liveness Trace (MANDATORY EVIDENCE)

### Caller Chain: GameSimulationJob → GCC Mining

```
GameSimulationJob#perform (galaxy_game/app/jobs/game_simulation_job.rb:7-50)
  │
  ├─ Line 13: game_state = GameState.first_or_create
  ├─ Line 16: return unless game_state.running          ← conditional gate
  ├─ Line 21: days_to_simulate = (elapsed_seconds / game_state.seconds_per_game_day).to_i
  ├─ Line 27: game = Game.new(game_state: game_state)
  └─ Line 28: game.advance_by_days(days_to_simulate)    ← LIVE production call
        │
        └─ Game#advance_by_days (galaxy_game/app/models/game.rb:47-60)
              │
              ├─ Line 52: process_settlements(days)      ← calls settlement/craft.process_tick
              ├─ Line 53: process_manufacturing_jobs(days)
              └─ Line 54: process_free_crafts(days)      ← calls craft.process_tick for undocked crafts
                    │
                    └─ Game#process_free_crafts (galaxy_game/app/models/game.rb:82-89)
                          │
                          └─ Line 87: craft.process_tick(time_skipped)
                                │
                                └─ BaseSatellite#process_tick (galaxy_game/app/models/craft/satellite/base_satellite.rb:291-320)
                                      │
                                      ├─ Line 304: mined_amount = mine_gcc          ← deposits to satellite.account
                                      ├─ Line 315: owner_gcc_account.deposit(mined_amount, "Satellite mining tick")  ← deposits to owner.account
                                      └─ RESULT: Two sequential positive GCC deposits from same mined_amount
```

### Classification: **CONFIRMED LIVE**

The double-credit condition executes in the production game loop when:
1. `GameState.running` is true
2. A deployed, undocked BaseSatellite with power-positive balance exists
3. The satellite responds to `mine_gcc` (all do via CryptocurrencyMining concern)

### GameService Status
`GameService#process_free_crafts` and `GameService#process_settlements` exist at `galaxy_game/app/services/game_service.rb:18,58,35` but are **NOT called by GameSimulationJob**. They appear to be parallel implementations that may be used elsewhere or are legacy. The production path goes through `Game#advance_by_days`.

### GameSimulation Job Path
`GameSimulationJob` does NOT call `process_units` or `Units::BaseUnit.operate`. That path is **UNRESOLVED** — no current caller identified in production code.

---

## Step 3 — Trigger Ownership Decision Matrix

| Candidate | Current Status | Activation Condition | Financial Side Effects | Overlap With Other Paths | Recommendation |
|---|---|---|---|---|---|
| `process_tick` (tick-driven) | TICK-CAPABLE — dual-credit path confirmed when invoked; direct caller: Game#advance_by_days → process_free_crafts → craft.process_tick (galaxy_game/app/models/game.rb:54,87) | Power-positive tick or sufficient battery | Two deposits per event: satellite.account (mine_gcc line 62), owner.account (process_tick line 315) | YES — primary overlap zone | **KEEP but FIX** — This is the confirmed live path. Must resolve dual-credit before any other trigger is enabled. Single canonical deposit per mining event required. |
| `SatelliteMiningSchedulerJob` → `MineGccJob` | BROKEN — Integer#mine_gcc NoMethodError (galaxy_game/app/jobs/mine_gcc_job.rb:7) | Hourly Sidekiq schedule; self-schedules at line 41 of scheduler job. Do not state production execution confirmed. | None reached (fails before mutation) | POTENTIAL if receiver fixed without idempotency | **DISABLE** — Hard disable via early return (Option A, Step 1). Do not fix receiver until canonical trigger/recipient are settled. |
| `GameSimulationJob` → `process_units` → `Units::BaseUnit.operate` | UNVERIFIED caller chain | Every 1 minute real time (Sidekiq) | UNVERIFIED | POTENTIAL overlap if operate triggers mining | **NO ACTION** — No current caller of process_units identified in production. Mark as unresolved; do not disable without evidence. |

---

## Step 4 — Credit Authority and Recipient Policy Framework

### 4a. All Current Mutation Targets

| Target | How Reached | Amount Source | Transaction Type | Locking |
|---|---|---|---|---|
| `satellite.account` (mine_gcc) | CryptocurrencyMining#mine_gcc line 62: `account.deposit(total_mined, "GCC Mining Operation")` | MiningUnitAdapter calculation per unit (lines 48-53): `unit.mine(mining_difficulty, unit_efficiency)` + apply_mining_effects | :deposit (Financial::Account) | with_lock (Account#deposit line 52-60) |
| `owner.account` (process_tick) | BaseSatellite#process_tick line 315: `owner_gcc_account.deposit(mined_amount, "Satellite mining tick")` | Same `mined_amount` returned from mine_gcc (line 304) | :deposit (Financial::Account) | with_lock (Account#deposit line 52-60) |
| LDC account | MissionTaskRunnerService — only path routing to LDC (per flow-map audit). Not called by mining code. | N/A | N/A | N/A |

### 4b. Decision Matrix: Recipient Options

| Option | Required Code Changes (without implementing) | Risk if Implemented Without Human Approval |
|---|---|---|
| **Satellite-only** | Remove owner deposit from BaseSatellite#process_tick line 315; update any code expecting owner to receive mining credits | May break settlement economics if owner GCC is consumed elsewhere |
| **Owner-only** | Change mine_gcc deposit target from self.account to owner account (CryptocurrencyMining#mine_gcc line 62); remove process_tick owner deposit line 315 | Breaks satellite's own funds tracking; may break satellite-level economy |
| **LDC-only** | Route both deposits through LDC account; add authorization guard (requires Task 3 resolution) | Requires upstream authorization decision; blocks all mining until resolved |
| **Conditional routing** | Add recipient policy check before each deposit; requires human decision on per-path rules | Most flexible but highest implementation complexity |

### 4c. Human Decision Required
- **Who**: Product/architecture owner
- **Evidence needed**: Game design intent for GCC ownership (satellite entity vs player/faction owner)
- **Decision unlocks**: All downstream mining implementation tasks

---

## Step 5 — Rate/Cadence/Idempotency Framework

### 5a. Rate Authority — Two Disconnected Calculations

| Calculation | File | Lines | Formula | Used By |
|---|---|---|---|---|
| `recalculate_stats` → `current_mining_rate_gcc_per_hour` | `galaxy_game/app/models/craft/base_craft.rb` | 372-395 | base + computer_boost + rigged_computer_boost | Persisted on BaseCraft; purpose unclear (no confirmed callers) |
| MiningUnitAdapter rate | `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` | 293-330 | Per-unit: `unit.mine(mining_difficulty, unit_efficiency)` + thermal/processing/direct boosts | Active in mine_gcc (lines 48-53); authoritative for live mining |

**Recommendation**: Document the disconnect. MiningUnitAdapter is the active calculation; recalculate_stats output is dead data unless a confirmed caller exists. Reconciliation required after canonical trigger is established.

### 5b. Cadence Alignment

| Cadence | Source | Interval | Game-Time Equivalent |
|---|---|---|---|
| Production game loop | GameSimulationJob | Every 1 minute real time | N game days (depends on GameState.seconds_per_game_day) |
| Scheduler job | SatelliteMiningSchedulerJob | Every 1 hour wall-clock | mining_interval: 4.hours (passed but ignored by MineGccJob) |

**Overlap risk**: If both paths were functional, they could produce overlapping credits for the same elapsed interval. The scheduler's 4-hour interval does not align with game-time tick cadence.

### 5c. Idempotency Options

| Option | Repository Pattern Alignment | Complexity | Recommendation |
|---|---|---|---|
| Last-mined timestamp check | MiningLog model exists (galaxy_game/app/models/mining_log.rb); could add `last_mined_at` to satellite | Low | **RECOMMENDED** — Minimal change, works with both paths once scheduler is fixed |
| Event-key uniqueness | Requires establishing mining-event identity first; key fields unknown until canonical trigger settled | Medium | Defer until Step 3 decisions made |
| Transaction-level lock | Financial::Account#deposit already uses with_lock; scope would need to be event-level, not deposit-level | High | Overkill if last-mined timestamp suffices |
| No idempotency acceptable | Only if mining events are inherently non-overlapping (requires cadence alignment first) | N/A | Not yet justifiable without cadence resolution |

---

## Step 6 — Portfolio Placement and Existing-Work Preflight

### Related Tasks Inventory

#### Active Tasks
| File | Status | Classification | Notes |
|---|---|---|---|
| `2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md` | active | **Independent** | Material sourcing, not mining/financial |
| `2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md` | active (this task) | — | This task |
| `2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md` | active | **Independent** | Asset generation, not mining/financial |

#### Backlog/Current Tasks (GCC Mining Related)
| File | Status | Classification | Notes |
|---|---|---|---|
| `2026-09-03-MEDIUM-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY.md` | backlog | **Upstream evidence/dependency** | Power/battery affects mining trigger conditions |
| `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-FITTING-DRIVEN-OUTPUT-GAMELOOP-INTEGRATION.md` | backlog | **Downstream/blocked** | Requires canonical trigger settled first |
| `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-UNIFIED-HARDWARE-CAPACITY-AND-SIMULATION-LOOP-INTEGRATION.md` | backlog | **Downstream/blocked** | Requires canonical trigger settled first |
| `2026-09-14-INVESTIGATION-SATELLITE-BATTERY-COMPATIBILITY.md` | backlog | **Upstream evidence/dependency** | Battery compatibility affects mining power logic |
| `2026-09-14-ARCHITECTURE-EXCHANGE-RATE-RECONCILIATION.md` | backlog | **Independent** | Exchange rate, not mining issuance |
| `2026-09-14-INVESTIGATION-VIRTUAL-LEDGER-EXCHANGE-RATE-100.0.md` | backlog | **Independent** | VirtualLedgerService, not mining code path |

#### Draft Tasks (GCC Mining Related)
| File | Status | Classification | Notes |
|---|---|---|---|
| `2026-09-15-HIGH-BUG-FIX-BOOTSTRAP-USD-GCC-CONVERSION-CORRECTION.md` | draft | **Independent** (but upstream for NPC economy) | USD→GCC conversion in VirtualLedgerService; does not touch mining code |
| `2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md` | draft | **Downstream/blocked** | Directly addresses dual-credit; blocked by recipient policy decision |
| `2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md` | draft | **Upstream evidence/dependency** | Required before any mining implementation; resolves human decision gates |
| `2026-09-15-HIGH-ARCHITECTURE-GCC-MINING-CADENCE-RATE-SEMANTICS-ALIGNMENT.md` | draft | **Downstream/blocked** | Requires canonical trigger settled first |

### Task Dependency Graph
```
Task 3 (Issuance Auth + LDC Routing) [DRAFT]
    └──→ Task 2 (Duplicate Credit Prevention) [DRAFT]
            └──→ Tasks: Fitting-Driven-Output, Unified-Hardware-Capacity [BACKLOG]

Task 4 (Cadence/Rate Alignment) [DRAFT]
    └──→ All downstream mining tasks

Power/Battery Research [BACKLOG] ──┐
Battery Compatibility [BACKLOG] ───┤──→ Upstream evidence for mining trigger conditions

USD/GCC Conversion [DRAFT] ─────────┤──→ Independent (NPC economy, not mining)
Exchange Rate Reconciliation [BACKLOG] ─┘
```

### Recommended Global Deployment Order
1. **This task** (containment plan) — produces candidate options, confirms dual-credit is live, identifies missing evidence
2. **GCC issuance-recipient + authorization decision task** (new — supersedes draft Task 3) — produces implementation contract resolving 7 items
3. **Task 2 draft** (Duplicate Credit Prevention) — blocked by Step 2 output
4. **Containment decision** (after Step 2) — choose among Options A/B/C once scheduler role is settled
5. **Task 4 draft** (Cadence/Rate Alignment) — conditional: should occur after recipient contract and duplicate-credit boundaries are established, unless an independent preflight proves it cannot alter recipient, mint, rate-authority, or cadence behavior.

---

## Step 7 — Containment Recommendation and Next-Task Proposal

### 7a. Containment Status: No containment recommendation yet

**Reason**: The scheduler's operational activation has not been verified (no cron/boot trigger confirmed), and its intended future role relative to tick-driven mining is unresolved. Without these two pieces of evidence, recommending a specific containment option would be premature.

**Candidate options remain** (from Step 1):
- **Option A — Hard disable**: Early return in `satellite_mining_scheduler_job.rb` (~line 10). Rollback: 1-line revert. Risk if activated without recipient policy: low (job fails before mutation).
- **Option B — Feature-flag gate**: Config flag read at top of `#perform`. Rollback: config value change. Risk if activated without recipient policy: medium.
- **Option C — Sidekiq queue disable**: Queue name change + Sidekiq config update. Rollback: 1-2 lines. Risk depends on Sidekiq configuration.

**What is needed before a containment recommendation can be made**:
1. Confirmation of whether `SatelliteMiningSchedulerJob` is actually enqueued in any environment (cron/boot trigger verification).
2. Human decision on the scheduler's intended future role: superseded by tick-driven mining, or retained as an alternative trigger path.
3. Resolution of canonical recipient policy (see Step 7d) — containment option choice depends on whether the scheduler is part of the future architecture.

**Dual-credit via process_tick remains confirmed live** and is the immediate financial-integrity concern regardless of scheduler status.

### 7b. Human Decisions Required (Prioritized)

| # | Decision | Evidence Needed | Unlocks |
|---|---|---|---|
| 1 | **Canonical GCC recipient per mining path** — satellite, owner, LDC, or conditional? | Game design intent for GCC ownership model | All downstream mining implementation |
| 7c. What this task does NOT decide
- Which specific code changes implement the recipient policy
- Whether dual-deposit is intentional (requires product/design review)
- Mining hardware simulation parameters
- Currency/gameplay economics redesign
- Whether to keep or replace the scheduler architecture

### 7d. Smallest Recommended Next Task: GCC Issuance-Recipient and Authorization Decision (Human-Gated Implementation Contract)

**Task type**: Architecture decision producing an implementation contract (not documentation-only).

**Purpose**: Establish a binding, actionable specification that downstream implementation tasks reference — not merely record a label in DECISIONS.md.

**Must resolve the following seven items:**

1. **Canonical recipient for satellite-mining issuance** — Which account receives GCC from mining: `self.account` (satellite), `owner_gcc_account`, LDC, or conditional routing?
2. **Dual-deposit intent** — Is the two-deposit pattern in `BaseSatellite#process_tick` (satellite + owner) ever intentional? If not, establish an explicit one-event/one-credit invariant.
3. **Scope of recipient policy** — Does the recipient rule apply to satellite-mined GCC only, or to all newly-mined GCC across all craft types and paths?
4. **LDC role** — Is LDC the canonical issuer (recipient), directional intent only, or conditional policy based on authorization?
5. **Canonical account-resolution mechanism** — If LDC is selected, how is the LDC account resolved at runtime (entity lookup, facility role, auth record, combination)?
6. **Issuer/authorization enforcement boundary** — Where in the call chain is issuance authorized? Before `mine_gcc`, inside `Account#deposit`, or at a new service layer?
7. **Consequences for existing paths** — What happens to: (a) existing `self.account` and `owner account` deposits, (b) the broken `MineGccJob` path, (c) later rate/cadence work that depends on mining semantics?

**Deliverable**: A one-page implementation contract with exact file paths, method signatures, and decision rationale — sufficient for an executor to implement without further clarification.

**Classification of existing drafts**: This task must reconcile with `2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md` (draft). The planning output must determine whether the existing draft is retained and revised, used as the basis for this task, or formally superseded. Task 2 (duplicate credit prevention) remains downstream/blocked on this task's output.

---

## No-Change Confirmation
- **No repository files modified** (this is planning-only)
- **No Git state changed**
- **No implementation performed**
- **All recommendations evidence-qualified** (not assumptions)

---

## Files Searched
- `galaxy_game/app/jobs/game_simulation_job.rb` — production game loop entry point
- `galaxy_game/app/models/game.rb:47-95` — Game#advance_by_days, process_settlements, process_free_crafts
- `galaxy_game/app/services/game_service.rb:18,35,58` — parallel implementations (not called by GameSimulationJob)
- `galaxy_game/app/models/craft/satellite/base_satellite.rb:291-320` — BaseSatellite#process_tick with dual-deposit
- `galaxy_game/app/models/concerns/cryptocurrency_mining.rb:10-95,293-330` — mine_gcc + MiningUnitAdapter
- `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb:1-45` — self-scheduling scheduler (broken)
- `galaxy_game/app/jobs/mine_gcc_job.rb:6-8` — failing job (Integer#mine_gcc)
- `galaxy_game/app/models/craft/base_craft.rb:372-395` — recalculate_stats (dead rate calculation)
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `agent-tasks/projects/galaxy_game/tasks/active/` — 3 active tasks
- `agent-tasks/projects/galaxy_game/tasks/backlog/current/` — 25 backlog tasks
- `agent-tasks/projects/galaxy_game/tasks/drafts/` — 11 draft tasks

---

## Handoff Summary
HANDOFF SUMMARY: Containment = no recommendation yet (scheduler activation unverified, future role unresolved; all 3 options remain candidates) | TRIGGER MATRIX: process_tick = keep+fix (confirmed live dual-credit), scheduler = candidate pending activation evidence, process_units = not established as a satellite trigger; retain only for read-only reconciliation of historical assumptions | HUMAN DECISIONS: GCC issuance-recipient + authorization implementation contract required (7 items) before any containment or mining implementation | NEXT ACTION: Human-gated GCC issuance-recipient and authorization decision task producing an implementation contract (not documentation-only)
