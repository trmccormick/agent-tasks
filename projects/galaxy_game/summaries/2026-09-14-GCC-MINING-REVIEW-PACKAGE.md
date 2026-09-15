# GCC Mining Review Package — For Claude and Gemini

**Prepared**: 2026-09-14  
**For**: Claude (financial architecture), Gemini (economic design), Tracy (approval)  
**Status**: REVIEW REQUIRED — Not dispatch-ready until both Claude and Gemini review  

---

## 1. Corrected Current/Design Terminology

### GCC Identity
- **GCC is a technically digital/cryptographic, fiat-like virtual ledger currency during the Luna bootstrap.** It is not physical material, commodity inventory, cargo, or a physical extraction output.
- **LDC (Luna Development Corporation) is the initial authorized issuer/mint** for the Luna development program under its UN-mandated Development Corporation role.
- **LDC is an Earth-formed non-profit Development Corporation** operating the Lunar foothold. It does not own Luna or claim territorial sovereignty.

### USD = GCC Scope
- **USD = GCC remains the documented initial Luna/Earth corporate-trade and accounting peg.**
- **It is not universal or permanently guaranteed.** The peg is a bootstrap design anchor, expected to decouple once GCC market volume supports independent floating value.
- **Existing three-phase decoupling/multi-currency research remains exploratory/deferred.** Do not represent it as current simulation behavior or add runtime triggers.

### Virtual-Ledger Role
- DCs and approved aligned corporations can record support, asset use, logistics, docking, refueling, repairs, and other development activity through virtual-ledger obligations/deferred settlement.
- These entries **retain real economic cost** and contribute to deficits, project viability, and AI Manager decisions. They are not free activity or hidden subsidy.
- GCC remains the principal player-facing currency and an AI economic reference, while not every institutional transaction must settle immediately in GCC.

### GCC-Mining Terminology
> **"GCC mining satellite"** remains the established asset/lore name.
>
> **"GCC mining"** means LDC-authorized, processing-hardware-driven GCC issuance/settlement activity. It is not physical extraction, commodity production, or a full proof-of-work gameplay system.

---

## 2. Source-Trace Evidence Summary (Distinguished from Runtime Proof)

### Verified Source-Trace Findings

**Finding**: `recalculate_stats` (base_craft.rb line 372) computes and stores a base-plus-fit mining rate in `current_mining_rate_gcc_per_hour`. The current `mine_gcc` path independently aggregates fitted computer units via MiningUnitAdapter without consuming the stored recalculated rate.

**Evidence**:
- `recalculate_stats` reads: satellite's `base_mining_rate_gcc_per_hour`, each fitted computer's `mining_boost_gcc_per_hour`, GPU rig `processing_boost_gcc_per_hour`
- `mine_gcc` reads: MiningUnitAdapter which queries each fitted unit's `mining_rate_value` or operational_data directly
- No code path connects the two methods — no call from `mine_gcc` to `recalculate_stats`, no read of `current_mining_rate_gcc_per_hour` by `mine_gcc`

**Classification**: Verified source-trace implementation evidence; runtime differential validation pending.

### What Is NOT Yet Proven
- Runtime differential validation (controlled tests with different fits) has not been executed
- Repository-reference audit of `recalculate_stats` consumers is incomplete — may have other consumers not yet discovered
- The 1000 GCC/hour satellite base-rate field status: inconsistent with approved hardware-only capacity direction, but do not claim it is "dead code" without full repository-reference audit

### Time-Model Findings (Source-Established Only)
- `GameSimulationJob` fires every **1 minute** (self-scheduled), initial trigger 10 seconds after Rails boot
- `days_to_simulate = (elapsed_seconds / game_state.seconds_per_game_day).to_i` — whole days only, never fractions
- At default speed=3: `seconds_per_game_day = 60`, so every minute advances by **1 game day**
- The wiki's "6-hour cycle" claim is **incorrect** — actual interval is 60 seconds at default speed
- `0.18` multiplier converts hourly GCC rate to per-operation deposit amount; reversed by mining-log reporting for per-hour display
- `0.18` is NOT proven to be the shared simulation tick duration or coupled to scheduler/game-speed time
- Do not claim a fixed operations-per-game-day count

---

## 3. Revised P0 Task

**Title**: GCC Mining Satellite — Unified Hardware Capacity and Simulation-Loop Integration  
**Location**: `docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-UNIFIED-HARDWARE-CAPACITY-AND-SIMULATION-LOOP-INTEGRATION.md`

### Key Requirements
- One authoritative hardware-derived capacity calculation
- No satellite intrinsic 1000 GCC/hour capacity; zero eligible processors means zero GCC capacity
- No silent unit-rate fallback; missing capacity metadata yields zero plus validation/diagnostic behavior
- Explicitly named/unit-consistent processor rates and rig modifiers
- Integration through existing BaseUnit operation architecture where possible, with exactly one satellite evaluation per elapsed period and no duplicate issuance
- Ordinary `Game#advance_by_days` integration tests without a direct `mine_gcc` call in end-to-end scenarios
- Controlled runtime differential tests across no-processor, baseline processor, altered-fit, and rig cases
- Explicit elapsed-game-time scaling contract; 0.18 cannot be assumed to be a global tick
- Power/battery behavior only according to verified existing energy semantics
- Ledger/account assertions that distinguish pre-seeding, issuance credit, transfers, virtual obligations, and true sinks
- Separation of financial GCC from physical inventory/cargo

### Key Exclusions
- USD/GCC decoupling, foreign exchange, other currencies, monetary-policy redesign
- Blockchain/proof-of-work mechanics
- Full virtual-ledger overhaul
- Exchange-rate architecture consolidation
- Broad craft hierarchy/scheduler refactor unless proven necessary
- Legacy identifier renaming

### Status: Requires Claude and Gemini review; not dispatch-ready.

---

## 4. Architecture Decision Note

**Location**: `docs/new_agent/projects/galaxy_game/architecture/2026-09-14-GCC-MINING-CAPACITY-CALCULATION-DECISION-NOTE.md`

### Two Options Compared

| Criterion | Option A: Shared Capacity | Option B: Mining-Canonical |
|-----------|--------------------------|---------------------------|
| Documentation accuracy | ✅ Resolves discrepancy | ❌ Discrepancy remains |
| Regression risk | Medium | Low |
| Code complexity | Higher | Lower |
| BaseUnit compatibility | Requires extension | No changes needed |
| Data migration | `current_mining_rate_gcc_per_hour` becomes authoritative | May be dead code |
| Recommendation | Preferred if validated | Safe fallback |

### Provisional Recommendation
Option A is preferred if runtime validation confirms it reproduces current mining output correctly. Option B is the safe fallback.

---

## 5. Wiki Pages Changed and Why

### File: `docs/wiki_reorganization/economy/02-currencies-and-accounts.md`

**Changes**:
1. Added Section 1 "GCC Identity" — establishes GCC as fiat-like virtual ledger currency, LDC as authorized issuer, non-profit status
2. Added terminology definition for "GCC mining" — clarifies it is issuance/settlement, not physical extraction
3. Added Section 3 "USD = GCC Scope" — documents peg as bootstrap anchor only, marks Phases 2-3 as exploratory/deferred with status labels
4. Updated "GCC Minting and Distribution" section — replaced "Lunar Mining" with "LDC authorized issuance," added implementation gap callout box
5. Added Section 5 "Virtual Ledger Role" — documents deferred settlement, economic cost retention, player vs. NPC boundaries

**Why**: Aligns wiki with approved GCC identity policy, corrects misleading commodity/extraction language, adds status labels to distinguish current implementation from exploratory design.

### File: `docs/wiki_reorganization/economy/GAPS.md`

**Changes**:
1. Renamed Gap H "Emission Schedule Enforcement" → "GCC Issuance Schedule" — replaces "emission" with "issuance"
2. Added Gap I "GCC Mining recalculate_stats/mine_gcc Disconnection" — documents verified source-trace discrepancy
3. Updated backlog status summary table to include Gap I

**Why**: Terminology correction ("emission" → "issuance") and documentation of the critical implementation gap identified during this session.

---

## 6. Separate Task Candidates and Boundaries

### Candidate A: VirtualLedgerService Exchange-Rate Investigation
**Location**: `2026-09-14-INVESTIGATION-VIRTUAL-LEDGER-EXCHANGE-RATE-100.0.md`  
**Scope**: Caller inventory, input/output units, persistence, test expectations, denomination/scaling possibility, actual financial impact, AI Manager/project-viability impact  
**Exclusion**: Do not prescribe changing the value to 1.0 yet  
**Status**: Requires Claude (financial architecture) and Gemini (economic design intent) review

### Candidate B: Exchange-Rate Architecture Reconciliation
**Location**: `2026-09-14-ARCHITECTURE-EXCHANGE-RATE-RECONCILIATION.md`  
**Scope**: Ownership, callers, persistence, current USD/GCC peg behavior, bond usage, admin UI use, future multi-currency compatibility, migration options, test plan  
**Exclusion**: Not part of P0 scope  
**Status**: Requires Claude and Gemini review

### Candidate C: Satellite Battery Compatibility Investigation
**Location**: `2026-09-14-INVESTIGATION-SATELLITE-BATTERY-COMPATIBILITY.md`  
**Scope**: Determine whether generic satellite blueprint is universal compatibility contract, fallback/placeholder, or temporary base; resolve recommended-fit/whitelist mismatch after evidence  
**Exclusion**: Do not make `recommended_fit` override compatibility validation  
**Status**: Requires Gemini review

---

## 7. Explicit Unanswered Questions for Review

### For Claude (Financial Architecture)
1. **Append-only ledger immutability**: Should GCC ledger entries be append-only/immutably auditable? What are the financial-architecture implications?
2. **Virtual-ledger obligation visibility**: How should virtual-ledger obligations be reported separately from settled GCC transactions?
3. **Deficit threshold policy**: What deficit thresholds and AI Manager intervention rules apply to NPC entities using virtual ledger?
4. **GCC denomination**: Is there a policy reason for GCC to have a different denomination than USD (i.e., is the 100.0 in VirtualLedgerService intentional)?
5. **Exchange-rate ownership**: Who owns exchange-rate infrastructure? Should there be a single source of truth?

### For Gemini (Economic Design)
1. **GCC classification**: Confirm GCC is fiat-style ledger currency, not a material/commodity. Does the wiki need any additional clarification?
2. **USD peg scope**: The 1:1 initial peg is described as "bootstrap price calibration." Is this accurate? Are there any other peg-related docs that need correction?
3. **Issuance mechanism**: LDC is sole issuer via mining satellites + pre-seeding. Is there any other issuance path (e.g., bond creation, NPC earning) that should be documented?
4. **Capacity model**: The design intent is "authorized GCC throughput = ∑ capacity of qualifying active, powered fitted hardware." Does this align with the operational data in `crypto_mining_satellite_data.json`?
5. **Power/battery constraints**: Which power and battery constraints apply to GCC issuance per tick? Is the battery smoothing behavior (eclipse recovery) documented anywhere?
6. **Economy inputs/costs**: What economy inputs/costs are in scope for the P0 task vs. deliberately deferred?
7. **Satellite equipment requirements**: Should `satellite_battery` be part of the generic satellite platform, or should GCC mining satellites have their own blueprint?

### For Tracy (Approval)
1. **P0 dispatch**: Is the revised P0 task ready for dispatch after Claude and Gemini review?
2. **Wiki corrections**: Are the terminology and status-label corrections acceptable?
3. **Task prioritization**: Do the investigation candidates warrant separate backlog tasks, or should some be merged?
4. **Implementation gap resolution**: Should Option A (shared capacity) or Option B (mining-canonical) be preferred for P0?

---

## 8. Gemini Economic Review Addition

**Recorded per Tracy's authorization update**:

- **Fitted processing hardware establishes potential throughput**, while **LDC authorization governs actual GCC credits**.
- **Satellite capacity does not independently authorize currency creation.** The satellite's fitted hardware determines what COULD be mined; LDC's authorized issuance policy determines what IS minted.
- This distinction is critical for the economic model: capacity is a physical/technical constraint, authorization is an economic/policy mechanism.

---

## Commit Summary

**Files Changed**:
1. `docs/wiki_reorganization/economy/02-currencies-and-accounts.md` — GCC identity, USD=GCC scope, virtual-ledger role, mining terminology, implementation gap callout
2. `docs/wiki_reorganization/economy/GAPS.md` — Terminology correction (emission → issuance), new Gap I for recalculate_stats/mine_gcc disconnection

**Files Created**:
1. `tasks/backlog/current/2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-UNIFIED-HARDWARE-CAPACITY-AND-SIMULATION-LOOP-INTEGRATION.md` — P0 task (undispatched)
2. `architecture/2026-09-14-GCC-MINING-CAPACITY-CALCULATION-DECISION-NOTE.md` — Architecture decision note (provisional)
3. `tasks/backlog/current/2026-09-14-INVESTIGATION-VIRTUAL-LEDGER-EXCHANGE-RATE-100.0.md` — VirtualLedgerService investigation (undispatched)
4. `tasks/backlog/current/2026-09-14-ARCHITECTURE-EXCHANGE-RATE-RECONCILIATION.md` — Exchange-rate architecture reconciliation (undispatched)
5. `tasks/backlog/current/2026-09-14-INVESTIGATION-SATELLITE-BATTERY-COMPATIBILITY.md` — Satellite battery compatibility investigation (undispatched)
6. `summaries/2026-09-14-GCC-MINING-REVIEW-PACKAGE.md` — This review package

**Status**: All changes are documentation and task artifacts only. No code, data, configuration, tests, rake tasks, or runtime behavior modified. P0 is NOT dispatch-ready until Claude, Gemini, and Tracy have reviewed it.
