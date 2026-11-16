# DRAFT ONLY — NOT DISPATCHED

---
status: backlog
priority: HIGH
type: bug-fix
system_domain: FINANCIAL
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Bootstrap USD→GCC Conversion Correction (100.0 → 1:1)

**Status**: DRAFT — Not dispatch-ready  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-09-15  
**Last Updated**: 2026-09-15  

---

## Context

GCC and USD are the initial currencies in a multi-currency system. The documented bootstrap peg is **1 GCC = 1 USD**. However, `VirtualLedgerService.exchange_rate_to_gcc` returns a hardcoded `100.0` with the comment `"Assume 1 USD = 100 GCC or something"` — clearly stale test code that was never cleaned up.

This means in-situ production savings are recorded at **1/100th** of their intended USD-equivalent GCC value, which can cause AI Manager to reject economically viable production decisions based on artificially deflated savings data.

---

## Problem Statement

`VirtualLedgerService.record_in_situ_savings()` divides USD savings by `exchange_rate_to_gcc` (100.0), producing a GCC amount that is 100× smaller than the bootstrap peg intends.

**Example**: $500/kg EAP price → `savings_usd = 500` → `savings_gcc = 5.0` (should be `500` at 1:1).

---

## Evidence

### Source Code
- **File**: `galaxy_game/app/services/financial/virtual_ledger_service.rb:65-96`
- **Method**: `self.record_in_situ_savings(producer:, resource:, amount:, eap_price:)`
  ```ruby
  savings_usd = amount * eap_price
  savings_gcc = savings_usd / exchange_rate_to_gcc
  ```
- **Method**: `self.exchange_rate_to_gcc` at line 93:
  ```ruby
  def self.exchange_rate_to_gcc
    # Assume 1 USD = 100 GCC or something
    100.0
  end
  ```

### Policy Reference
- **File**: `galaxy_game/config/economic_parameters.yml` — documents bootstrap peg (1:1)
- **Status.md** notes: "VirtualLedgerService.exchange_rate_to_gcc returns hardcoded 100.0 (test artifact, never cleaned up)"

### Caller Inventory
- `record_in_situ_savings()` at line 67 is the proven caller
- Any other callers of `exchange_rate_to_gcc` must be identified via repository-reference audit before dispatch

---

## Scope

### What This Task Covers
- Identify all callers of `VirtualLedgerService.exchange_rate_to_gcc`
- Determine the correct fix approach for the bootstrap peg:
  - Option A: Change `exchange_rate_to_gcc` to return `1.0` (explicit bootstrap rate)
  - Option B: Use `ExchangeRateService` or another dynamic rate mechanism
  - **[FILL IN: Human decision on implementation approach]**
- Fix the conversion so that USD savings are recorded at the correct GCC equivalent

### What This Task Does NOT Cover
- Exchange rate dynamics, market pricing, or Phase 2/3 monetary policy
- Other currency pairs beyond USD→GCC
- Changes to `Financial::ExchangeRate` model (DB-backed) unless evidence shows it is connected
- General financial service refactoring

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1**: The `Financial::ExchangeRate` model exists in the database but is NOT connected to `VirtualLedgerService.exchange_rate_to_gcc`. Do not assume they are related.
- ❌ Wrong: Modify `Financial::ExchangeRate` table/model
- ✅ Right: Fix `VirtualLedgerService.exchange_rate_to_gcc` directly or wire it to the model if evidence supports it

⚠️ **GOTCHA 2**: Other code may depend on the current 100.0 value (intentionally or not). A full caller inventory is required before changing the return value.
- ❌ Wrong: Change 100.0 → 1.0 without verifying all callers
- ✅ Right: Audit all callers first, then fix with regression verification

### Unresolved Decisions — [FILL IN]
- **[FILL IN: Human decision on implementation approach]** — explicit bootstrap 1.0 vs ExchangeRateService vs other
- **[FILL IN: Are there other callers of exchange_rate_to_gcc that need separate treatment?]**

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/app/services/financial/virtual_ledger_service.rb` | USD→GCC conversion | `record_in_situ_savings()` line 65, `exchange_rate_to_gcc` line 93 |

### Reference Files — read but do not edit
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/config/economic_parameters.yml` | Bootstrap peg documentation |
| `galaxy_game/app/services/financial/exchange_rate_service.rb` | Existing exchange rate service (may or may not be relevant) |
| `galaxy_game/app/models/financial/exchange_rate.rb` | DB-backed ExchangeRate model (verify if connected) |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/drafts/2026-09-15-HIGH-BUG-FIX-BOOTSTRAP-USD-GCC-CONVERSION-CORRECTION.md \
       projects/galaxy_game/tasks/active/2026-09-15-HIGH-BUG-FIX-BOOTSTRAP-USD-GCC-CONVERSION-CORRECTION.md
```

Then open the moved file and change: `status: backlog → status: active`

### Step 1 — Caller Inventory
- Run repository-reference audit for all callers of `VirtualLedgerService.exchange_rate_to_gcc`
- Document findings in synthesis report
- **[FILL IN: Human decision on implementation approach]** before proceeding

### Step 2 — Fix Implementation
- Apply the chosen fix (implementation approach TBD by human)
- Ensure no regression in other financial operations

### Step 3 — Verify
- Run any existing specs related to `VirtualLedgerService` or `record_in_situ_savings`
- Expected: correct GCC amount at 1:1 peg, no new failures

---

## Acceptance Criteria
- [ ] All callers of `exchange_rate_to_gcc` identified and documented
- [ ] USD savings recorded at correct GCC equivalent (1:1 bootstrap peg)
- [ ] No regression in other financial operations
- [ ] **[FILL IN: Human-approved implementation approach applied]**

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Evidence shows the 100.0 value is intentional (not a test artifact)
- Root cause requires architectural decision beyond scope of this task
- Other currency pairs are affected and need coordinated treatment

---

## Dependencies
**Blocked by**: Human decision on implementation approach (explicit 1.0 vs ExchangeRateService)  
**Blocks**: GCC mining settlement integrity task (conversion correctness affects NPC profitability)  
**Related tasks**: `2026-09-14-INVESTIGATION-VIRTUAL-LEDGER-EXCHANGE-RATE-100.0.md` (investigation draft)

---

## Claude Review Checklist
- [ ] Bootstrap peg documented correctly (1:1)?
- [ ] Caller inventory requirement stated?
- [ ] Implementation approach left open for human decision?
- [ ] Scope boundaries clear (no monetary policy, no other currencies)?
- [ ] Stop conditions appropriate?

---

**Status**: Requires Claude review and human decision on implementation approach. Not dispatch-ready.
