# DRAFT ONLY — NOT DISPATCHED

---
status: backlog
priority: HIGH
type: bug-fix
system_domain: FINANCIAL | AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: GCC Mining Settlement Integrity — Duplicate-Credit Prevention

**Status**: DRAFT — Not dispatch-ready  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-09-15  
**Last Updated**: 2026-09-15  

---

## Context

GCC mining produces new currency through three distinct entry paths:
1. **Satellite tick path** (`BaseSatellite#process_tick`) — simulation tick trigger
2. **Scheduled job path** (`SatelliteMiningSchedulerJob` → `MineGccJob`) — Sidekiq trigger
3. **Mission task path** (`MissionTaskRunnerService.execute_task(action: 'mine_gcc')`) — manual deployment trigger

Each path has different deposit recipient behavior, and the satellite tick path may produce duplicate credits.

---

## Problem Statement

Evidence shows that `CryptocurrencyMining#mine_gcc` deposits mined GCC to **the satellite's own account** (`self.account.deposit`). However, `BaseSatellite#process_tick` independently looks up the **owner's** GCC account and deposits there as well — after `mine_gcc` has already run. This means a single mining event can produce **two separate GCC credits** from the same mined amount.

Additionally, each entry path deposits to a different recipient:
- Satellite tick → owner's GCC account (in addition to satellite's own account via mine_gcc)
- Scheduled job → satellite's own account
- Mission task → LDC's account (`accounts[:ldc]`)

This creates inconsistent monetary credit outcomes for the same mining event depending on which trigger fires.

---

## Evidence

### Source Code — Satellite Tick Path
- **File**: `galaxy_game/app/models/craft/satellite/base_satellite.rb:291`
- **Method**: `process_tick(time_skipped = 1)`
  ```ruby
  mined_amount = mine_gcc if respond_to?(:mine_gcc)
  # ...
  if mined_amount && mined_amount > 0
    owner_gcc_account = Account.find_or_create_for_entity_and_currency(
      accountable_entity: owner,
      currency: Currency.find_by(symbol: 'GCC')
    )
    owner_gcc_account.deposit(mined_amount, "Satellite mining tick")
  end
  ```

### Source Code — Mining Concern
- **File**: `galaxy_game/app/models/concerns/cryptocurrency_mining.rb:10`
- **Method**: `mine_gcc` deposits to `self.account.deposit(total_mined, "GCC Mining Operation")`

### Source Code — Scheduled Job Path
- **File**: `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb`
- Queues `MineGccJob.perform_later(satellite.id, ...)` which calls `satellite.mine_gcc`
- Deposits to satellite's own account (via mine_gcc)

### Source Code — Mission Task Path
- **File**: `galaxy_game/app/services/mission_task_runner_service.rb`
- Calls `accounts[:ldc].deposit(initial_gcc, "Initial GCC mining from #{satellite.name}")`
- Deposits to LDC's account (NOT satellite's or owner's)

### Key Observations
- `mine_gcc` deposits to `self.account` (satellite's own account)
- `process_tick` then deposits the same amount to `owner`'s account — **potential duplicate credit**
- No evidence of deduplication logic in any path
- The `time_skipped` parameter is passed but **not used** for scaling

---

## Scope

### What This Task Covers
- Identify and document all duplicate-credit scenarios across the three mining entry paths
- Determine whether the satellite tick path's dual deposit (mine_gcc → owner) is intentional or a bug
- Establish that each mining event produces exactly one monetary credit outcome
- Ensure MiningLog records correspond to the credited transaction

### What This Task Does NOT Cover
- GCC issuance authorization policy (separate task)
- Mining cadence/rate semantics (separate task)
- LDC recipient routing (separate task)
- Changes to `Financial::Account#deposit` behavior unless evidence requires it

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1**: The satellite tick path may intentionally deposit to both the satellite's account AND the owner's account. This could be a design where the satellite "earns" GCC and then "transfers" it to its owner.
- ❌ Wrong: Assume duplicate credit is always a bug without test verification
- ✅ Right: Verify via tests/specs whether dual deposit is intentional before fixing

⚠️ **GOTCHA 2**: The mission task path deposits to `accounts[:ldc]` which is passed as a parameter — there is no verification that `:ldc` refers to a valid LDC account.
- ❌ Wrong: Assume accounts[:ldc] always exists and is valid
- ✅ Right: Verify the LDC account resolution in synthesis report

### Unresolved Decisions — [FILL IN]
- **[FILL IN: After GCC issuance-policy alignment, which current mining entry points remain valid issuance initiators, which must be removed or redirected, and which may only calculate/log mining output without independently settling a ledger credit?]** — requires test/spec verification + issuance-policy alignment (Task 3)

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/app/models/craft/satellite/base_satellite.rb` | Satellite tick mining + deposit | `process_tick()` line 291 |
| `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` | Core mining logic | `mine_gcc()` line 10 |

### Reference Files — read but do not edit
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb` | Scheduled job path |
| `galaxy_game/app/jobs/mine_gcc_job.rb` | Job performer |
| `galaxy_game/app/services/mission_task_runner_service.rb` | Mission task path |
| `galaxy_game/app/models/financial/account.rb` | Account#deposit behavior |
| `galaxy_game/app/models/mining_log.rb` | MiningLog audit record |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/drafts/2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md \
       projects/galaxy_game/tasks/active/2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md
```

Then open the moved file and change: `status: backlog → status: active`

### Step 1 — Duplicate Credit Audit
- Trace all three mining entry paths for deposit recipients
- Verify via tests/specs whether dual deposit in process_tick is intentional
- Document findings in synthesis report

### Step 2 — Fix Implementation
- **[FILL IN: Human decision on canonical recipient per path]**
- Ensure exactly one monetary credit per mining event
- Preserve MiningLog audit trail integrity

### Step 3 — Verify
- Run any existing specs related to satellite mining, GCC deposits, or MiningLog
- Expected: single credit per mining event, no new failures

---

## Acceptance Criteria
- [ ] All three mining entry paths documented with exact deposit recipients
- [ ] Duplicate credit scenario confirmed or ruled out via test evidence
- [ ] Each mining event produces exactly one monetary credit outcome
- [ ] MiningLog records correspond to the credited transaction
- [ ] **[FILL IN: Human-approved recipient routing applied]**

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Evidence shows dual deposit is intentional design (not a bug)
- Root cause requires architectural decision beyond scope of this task
- LDC account resolution cannot be verified without human input

---

## Dependencies
**Blocked by**: Human decision on canonical recipient per path  
**Blocks**: GCC issuance authorization task (recipient routing depends on integrity fix)  
**Related tasks**: `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-FITTING-DRIVEN-OUTPUT-GAMELOOP-INTEGRATION.md`

---

## Claude Review Checklist
- [ ] Three entry paths documented with exact recipients?
- [ ] Duplicate credit scenario clearly described?
- [ ] Implementation approach left open for human decision?
- [ ] Scope boundaries clear (no authorization, no cadence)?
- [ ] Stop conditions appropriate?

---

**Status**: Requires Claude review and human decision on recipient routing. Not dispatch-ready.
