# GCC Mining Satellite — Unified Hardware Capacity and Simulation-Loop Integration

**Status**: DRAFT — Requires Gemini review + Tracy approval; Claude answers applied (see below)  
**Created**: 2026-09-14  
**Last Updated**: 2026-09-15  
**Priority**: P0 (highest)  
**Type**: HIGH-FEATURE / ARCHITECTURE  

---

## Problem Statement

The GCC mining satellite is the first deployable asset in the gameplay loop (GCC → fund Luna precursor → deploy Venus skimmer). It is currently **decoupled from the game tick loop**: craft inherit `ApplicationRecord`, not `Units::BaseUnit`, so `Game#advance_by_days` → `process_units` never reaches satellites. Mining only occurs via manual `mine_gcc()` calls (rake/Sidekiq), not through simulation.

Additionally, there is a verified source-trace discrepancy: `recalculate_stats` computes and stores a base-plus-fit mining rate in `current_mining_rate_gcc_per_hour`, but the current `mine_gcc` path independently aggregates fitted computer units via MiningUnitAdapter without consuming the stored recalculated rate. These are two disconnected code paths.

---

## Terminology Definition

> **"GCC mining satellite"** remains the established asset/lore name.
>
> **"GCC mining"** means LDC-authorized, processing-hardware-driven GCC issuance/settlement activity. It is not physical extraction, commodity production, or a full proof-of-work gameplay system.
>
> All references to "mining output" in this task mean "authorized GCC issuance per tick."

---

## Legacy Identifiers Preserved (No Renaming in P0)

- `CryptocurrencyMining` concern module — keep name
- `mine_gcc` method — keep name
- `MiningLog` model — keep name
- `MiningUnitAdapter` class — keep name
- `crypto_mining_satellite_data.json` filename — keep name
- `MineGccJob` job class — keep name

---

## Scope — What P0 Does

### 1. One Authoritative Hardware-Derived Capacity Calculation

- Fitted processing hardware establishes **potential** throughput.
- LDC authorization governs **actual** GCC credits issued.
- Satellite capacity does NOT independently authorize currency creation.
- Zero eligible processors means zero GCC capacity.
- No silent unit-rate fallback; missing capacity metadata yields zero plus validation/diagnostic behavior.
- Explicitly named/unit-consistent processor rates and rig modifiers.

### 2. Simulation-Loop Integration

- Integration through the existing BaseUnit operation architecture where possible.
- Exactly one satellite evaluation per elapsed period — no duplicate issuance.
- Ordinary `Game#advance_by_days` integration tests without a direct `mine_gcc` call in end-to-end scenarios.
- Controlled runtime differential tests across:
  - No-processor configuration
  - Baseline processor configuration
  - Altered-fit configuration
  - Rig-augmented configuration

### 3. Time-Model Contract

- Explicit elapsed-game-time scaling contract.
- `0.18` cannot be assumed to be a global tick — it is a per-operation conversion factor within the mining formula, not proven to be the shared simulation tick duration or coupled to scheduler/game-speed time.
- Do not claim a fixed operations-per-game-day count.

### 4. Power/Battery Behavior

- Power/battery behavior only according to verified existing energy semantics.
- No assumptions about battery discharge rates beyond what `satellite_battery_data.json` documents (capacity: 500.0 kWh, max_discharge: 150.0 kW).

### 5. Ledger/Account Assertions

- Auditable pre-seeding requirement using the existing supported financial mechanism (not immutable ledger entry — flag append-only/immutability as a Claude financial-architecture question).
- Distinguish pre-seeding, issuance credit, transfers, virtual obligations, and true sinks.
- Separation of financial GCC from physical inventory/cargo.

---

## Scope — What P0 Excludes

- USD/GCC decoupling, foreign exchange, other currencies, and monetary-policy redesign
- Blockchain/proof-of-work mechanics
- Full virtual-ledger overhaul
- Exchange-rate architecture consolidation (separate task candidate)
- Broad craft hierarchy/scheduler refactor unless proven necessary
- Legacy identifier renaming

---

## Acceptance Criteria

1. **Ordinary `Game#advance_by_days` operation**: GCC mining occurs automatically through the tick loop — no manual `mine_gcc` call in end-to-end tests
2. **Tested configurations**: Valid-fit, no-hardware (zero computers), and changed-fit scenarios
3. **Verified power/battery behavior**: Mines when powered; falls back to battery during grid outage; returns 0 when battery depleted
4. **Ledger-vs-inventory separation**: Mining deposits distinguishable from pre-seeded reserves, transfers, virtual-ledger obligations
5. **Auditable pre-seeding**: Pre-seeded reserves tracked via existing financial mechanism (append-only/immutability is a Claude question)
6. **Repeatable multi-period results**: Advancing by N days produces N× the output of 1 day (deterministic, time-proportional) — holds at any speed setting
7. **Virtual-ledger visibility**: Virtual-ledger obligations remain visible and separately reportable where current models support them (automatic deficit/default flags replaced with this requirement; separate deferred AI/economy task candidate for deficit thresholds, enforcement, and AI Manager intervention rules)

---

## Critical Gotcha — Fitting vs. Flat-Rate Resolution

> **`recalculate_stats` exists and computes fitting-driven rates, but `mine_gcc` does NOT read its output.** Actual mining capacity is computed independently via MiningUnitAdapter from each fitted computer's operational data. The satellite-level `base_mining_rate_gcc_per_hour: 1000` is dead/unconsumed design data for mining output. This was confirmed by source-read: two independent methods with no data flow between them.
>
> **Status**: Verified source-trace implementation evidence; runtime differential validation pending.

---

## Virtual-Ledger Obligations Requirement

- Virtual-ledger obligations remain visible and separately reportable where current models support them.
- Automatic deficit/default flags are replaced with this visibility requirement.
- Separate deferred AI/economy task candidate for deficit thresholds, enforcement, and AI Manager intervention rules.

---

## Claude Review Answers (Applied — No Longer Dispatch Gates)

> **Tracy's direction**: Q1-Q3 removed as P0 dispatch gates. They contradict the task's own out-of-scope list (which already excludes virtual-ledger redesign and exchange-rate policy). All three deferred to a dedicated future ledger-architecture task.

### Q4 — Capacity Unification: RESOLVED ✅
Make the existing `mine_gcc`/`MiningUnitAdapter` chain the **single canonical calculation**. If `current_mining_rate_gcc_per_hour` is kept as a displayed stat, it must call the same method — not maintain a second parallel implementation.

### Q5 — Satellite Base-Rate Field: RESOLVED ✅
Deprecate/remove `base_mining_rate_gcc_per_hour: 1000` rather than repurpose. It is dead/unconsumed design data for mining output.

### Q6 — Time-Model Contract: RESOLVED ✅
Document `0.18` as a per-operation unit-conversion constant, explicitly **not** tied to the simulation tick interval, with a code comment.

---

## Gemini Review Questions (Required Before Dispatch)

1. **GCC classification**: Confirm GCC is fiat-style ledger currency, not a material/commodity. Does the wiki need any additional clarification?
2. **USD peg scope**: The 1:1 initial peg is described as "bootstrap price calibration." Is this accurate? Are there any other peg-related docs that need correction?
3. **Issuance mechanism**: LDC is sole issuer via mining satellites + pre-seeding. Is there any other issuance path (e.g., bond creation, NPC earning) that should be documented?
4. **Capacity model**: The design intent is "authorized GCC throughput = ∑ capacity of qualifying active, powered fitted hardware." Does this align with the operational data in `crypto_mining_satellite_data.json`?
5. **Power/battery constraints**: Which power and battery constraints apply to GCC issuance per tick? Is the battery smoothing behavior (eclipse recovery) documented anywhere?
6. **Economy inputs/costs**: What economy inputs/costs are in scope for the P0 task vs. deliberately deferred?

---

## Files Involved

- `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` — mine_gcc method
- `galaxy_game/app/models/craft/base_craft.rb` — recalculate_stats method
- `galaxy_game/app/models/concerns/has_units.rb` — unit management
- `data/json-data/operational_data/craft/satellites/crypto_mining_satellite_data.json` — satellite operational data
- `data/json-data/blueprints/crafts/space/satellites/generic_satellite_bp.json` — compatible_units whitelist
- `data/json-data/operational_data/units/energy/satellite_battery_data.json` — battery specs
- `galaxy_game/app/models/game.rb` — advance_by_days method
- `galaxy_game/app/jobs/game_simulation_job.rb` — simulation scheduling

---

**Status**: Requires Claude and Gemini review; not dispatch-ready.  
**Review package**: See `2026-09-14-GCC-MINING-REVIEW-PACKAGE.md` (separate file).
