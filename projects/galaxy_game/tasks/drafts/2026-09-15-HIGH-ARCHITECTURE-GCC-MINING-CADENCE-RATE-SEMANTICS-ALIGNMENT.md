# DRAFT ONLY — NOT DISPATCHED

---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER | UNITS
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: GCC Mining Cadence and Rate Semantics Alignment

**Status**: DRAFT — Not dispatch-ready  
**Priority**: HIGH  
**Type**: architecture  
**Created**: 2026-09-15  
**Last Updated**: 2026-09-15  

---

## Context

GCC mining uses three distinct time triggers with inconsistent semantics:
1. **Satellite tick path** (`BaseSatellite#process_tick`) — simulation tick trigger, `time_skipped` parameter not used for scaling
2. **Scheduled job path** (`SatelliteMiningSchedulerJob` → `MineGccJob`) — Sidekiq trigger with `1.hour` scheduler interval and `4.hours` mining interval (interval passed but never consumed)
3. **Mission task path** (`MissionTaskRunnerService.execute_task(action: 'mine_gcc')`) — manual deployment trigger, no time basis

The satellite operational data contains fields named `*_per_hour` (e.g., `base_mining_rate_gcc_per_hour: 1000`, `mining_boost_gcc_per_hour`, `processing_boost_gcc_per_hour`) but these are **not proven payout inputs** — current payout is driven by fitted computer-unit values and multipliers. This makes issuance cadence, rate meaning, and NPC profitability unreliable.

---

## Problem Statement

Multiple mining triggers fire independently without coordinated time semantics:
- The tick path does not scale mining by `time_skipped` (one tick = one mining event regardless of elapsed game time)
- The scheduler passes a four-hour interval that the job does not consume
- Satellite-level fields named `*_per_hour` are not proven payout inputs; current payout is driven by fitted computer-unit values
- No explicit and testable meaning for mining rates or event cadence exists

This makes NPC profitability calculations, investment comparisons, power planning, and market development unreliable.

---

## Evidence

### Time Triggers — Three Independent Paths

**1. Satellite Tick Path**
- **File**: `galaxy_game/app/models/craft/satellite/base_satellite.rb:291`
- **Trigger**: Simulation tick (`process_tick(time_skipped = 1)`)
- **Issue**: `time_skipped` parameter is passed but **not used** for mining scaling. One tick always triggers one mining event regardless of elapsed game time.

**2. Scheduled Job Path**
- **File**: `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb`
- **Trigger**: Sidekiq scheduler runs every `1.hour`; queues `MineGccJob` with `mining_interval: 4.hours`
- **Issue**: The `4.hours` interval is passed as an option hash but **never consumed by MineGccJob** (which ignores it and just calls `mine_gcc` once)

**3. Mission Task Path**
- **File**: `galaxy_game/app/services/mission_task_runner_service.rb:26`
- **Trigger**: Manual deployment action (`'mine_gcc'` task action)
- **Issue**: One-time event with no time basis or cadence

### Rate Fields — Satellite vs Fitted Units

**Satellite-level fields (from `crypto_mining_satellite_data.json`)**:
```json
"operational_properties": {
  "base_mining_rate_gcc_per_hour": 1000
}
```
- **Status**: Dead/unconsumed design data for mining payout
- Only read by `recalculate_stats` (which computes `total_mining_rate = base_mining_rate + computer_boost + rigged_computer_boost`) but this computed value is stored to operational_data, not consumed by `mine_gcc`

**Fitted computer-unit values (actual payout source)**:
- **File**: `galaxy_game/app/models/concerns/cryptocurrency_mining.rb:307` (`MiningUnitAdapter.mine`)
- Each fitted computer's `mining_rate_value` attribute (loaded from its own operational data JSON) is the actual per-unit payout input
- Multiplied by difficulty and efficiency, then enhanced by thermal/processing/direct multipliers

**Other `*_per_hour` fields**:
- `mining_boost_gcc_per_hour` on fitted computers — only read by `recalculate_stats`, not by `mine_gcc`
- `processing_boost_gcc_per_hour` on GPU rigs — only read by `recalculate_stats`, not by `mine_gcc`

### Config-Defined Monetary Policy (Not Enforced)
- **File**: `galaxy_game/config/economic_parameters.yml:253-258`
  ```yaml
  cryptocurrency:
    gcc_mining:
      issuance_model: "capped_deflationary"
      max_supply: 21000000000
      halving_interval_days: 730
      difficulty_scaling: true
      initial_block_reward: 1000
      minimum_block_reward: 1
  ```
- **Status**: Config exists but **none of these values control the mining payout path**. `EconomicConfig` methods are defined and tested but never called from mining code.

---

## Scope

### What This Task Covers
- Establish an explicit and testable meaning for mining rates and event cadence
- Determine which time model applies (game-time / wall-clock-event-driven / deliberate hybrid)
- Ensure the selected time basis is consumed in the mining calculation
- Handle `time_skipped`, elapsed scheduled time, and mission-trigger semantics intentionally
- Clarify satellite-level and fitted-equipment rate field roles; inactive fields are flagged for removal/deprecation
- Ensure AI/NPC profitability calculations can rely on a defined rate

### What This Task Does NOT Cover
- GCC issuance authorization policy (separate task)
- Duplicate credit prevention (separate task)
- Bootstrap USD→GCC conversion correction (separate task)
- Supply controls, halving, difficulty scaling, or block reward enforcement
- General clock redesign unrelated to mining cadence

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1**: The `*_per_hour` field names in satellite operational data do not necessarily mean "real hour" — they could represent game-time hours, simulation ticks, or arbitrary rate units. Do not infer the meaning from field names or comments alone.
- ❌ Wrong: Assume `base_mining_rate_gcc_per_hour: 1000` means 1000 GCC per real-world hour
- ✅ Right: Verify the time basis through test evidence or design documentation before assigning meaning

⚠️ **GOTCHA 2**: The three mining triggers may have different intended responsibilities (e.g., tick = continuous NPC operation, scheduled = periodic batch, mission = one-time deployment). They may not need to produce identical results.
- ❌ Wrong: Force all three paths to use the same cadence without verifying intent
- ✅ Right: Determine if multiple triggers should have explicit non-overlapping responsibilities

⚠️ **GOTCHA 3**: The `recalculate_stats` method computes a `current_mining_rate_gcc_per_hour` from satellite + computer + rig data but this value is never consumed by `mine_gcc`. This could be intentional (pre-computed stats for AI Manager display) or dead code.
- ❌ Wrong: Assume `recalculate_stats` output should feed into `mine_gcc` without verification
- ✅ Right: Verify whether `current_mining_rate_gcc_per_hour` is used by any AI/NPC services before repurposing it

### Unresolved Decisions — [FILL IN]
- **[FILL IN: Time model decision]** — game time / wall-clock-event-driven / deliberate hybrid for NPC-only accelerated buildup?
- **[FILL IN: Which triggers should coexist vs. which should be consolidated]?**
- **[FILL IN: Treatment of satellite-level `*_per_hour` fields]** — remove, deprecate, or repurpose?

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/app/models/craft/satellite/base_satellite.rb` | Satellite tick mining (time_skipped not used) | `process_tick()` line 291 |
| `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb` | Scheduled job (interval passed but unused) | `perform()` line 14 |
| `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` | Mining calculation (rate source) | `mine_gcc()` line 10, `MiningUnitAdapter.mine()` line 307 |

### Reference Files — read but do not edit
| File | Why You Need It |
|------|-----------------|
| `data/json-data/operational_data/crafts/space/satellites/crypto_mining_satellite_data.json` | Satellite operational data (rate fields) |
| `galaxy_game/app/models/craft/base_craft.rb:372` | `recalculate_stats()` — computes but doesn't consume rate |
| `galaxy_game/config/economic_parameters.yml:253-258` | Config-defined monetary policy (not enforced) |
| `galaxy_game/app/services/economic_config.rb` | EconomicConfig methods (defined but not called from mining) |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/drafts/2026-09-15-HIGH-ARCHITECTURE-GCC-MINING-CADENCE-RATE-SEMANTICS-ALIGNMENT.md \
       projects/galaxy_game/tasks/active/2026-09-15-HIGH-ARCHITECTURE-GCC-MINING-CADENCE-RATE-SEMANTICS-ALIGNMENT.md
```

Then open the moved file and change: `status: backlog → status: active`

### Step 1 — Time Model Audit
- Document each trigger's current time basis and scaling behavior
- Verify via tests/specs whether any trigger is intended to scale by elapsed time
- Identify which triggers have explicit non-overlapping responsibilities (if any)
- **[FILL IN: Human decision on time model]** before proceeding

### Step 2 — Rate Semantics Clarification
- Establish explicit meaning for each active mining rate field
- Determine role of satellite-level `*_per_hour` fields vs fitted computer-unit values
- Flag inactive/dead fields for separate removal/deprecation approval

### Step 3 — Fix Implementation
- Apply the selected time model to the mining calculation
- Ensure `time_skipped`, elapsed scheduled time, and mission-trigger semantics are handled intentionally
- Preserve AI/NPC profitability calculation reliability

### Step 4 — Verify
- Run any existing specs related to satellite mining cadence, rate calculations, or NPC profitability
- Expected: defined rate with clear unit and time basis, no new failures

---

## Acceptance Criteria
- [ ] Every active mining rate has a defined unit and time basis
- [ ] Exactly one source of truth determines payout cadence, or multiple triggers have explicit non-overlapping responsibilities
- [ ] The selected time basis is consumed in the mining calculation
- [ ] `time_skipped`, elapsed scheduled time, and mission-trigger semantics are handled intentionally and tested
- [ ] Satellite-level and fitted-equipment rate fields have explicit documented roles
- [ ] AI/NPC profitability calculations can rely on a defined rate
- [ ] No unrelated general clock redesign is introduced unless separately approved
- [ ] **[FILL IN: Human-approved time model applied]**

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Evidence shows the current unscaled behavior is intentional (not a bug)
- Root cause requires architectural decision beyond scope of this task
- Time model decision cannot be determined from existing code or documentation

---

## Dependencies
**Blocked by**: Human decision on time model (game time / wall clock / hybrid)  
**Blocks**: NPC profitability calculations, investment comparisons, power planning  
**Related tasks**: `2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md` (cadence affects duplicate credit timing)

---

## Claude Review Checklist
- [ ] Three time triggers documented with exact behavior?
- [ ] Rate semantics clearly distinguished (satellite vs fitted units)?
- [ ] Time model left open for human decision?
- [ ] Scope boundaries clear (no authorization, no conversion rate, no duplicate credit)?
- [ ] Stop conditions appropriate?

---

**Status**: Requires Claude review and human decision on time model. Not dispatch-ready.
