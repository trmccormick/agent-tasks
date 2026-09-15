# VirtualLedgerService Exchange-Rate Investigation — 100.0 Value

**Status**: DRAFT — Not dispatch-ready  
**Created**: 2026-09-14  
**Last Updated**: 2026-09-14  
**Priority**: LOW (bug investigation, not P0)  
**Type**: INVESTIGATION CANDIDATE  

---

## Finding

`VirtualLedgerService.exchange_rate_to_gcc` returns a hardcoded `100.0` with comment `"Assume 1 USD = 100 GCC or something"` — clearly stale test code that was never cleaned up.

**Location**: `galaxy_game/app/services/financial/virtual_ledger_service.rb:93-95`

```ruby
def self.exchange_rate_to_gcc
  # Assume 1 USD = 100 GCC or something
  100.0
end
```

---

## Scope

### Caller Inventory
- `record_in_situ_savings()` at line 67: `savings_gcc = savings_usd / exchange_rate_to_gcc`
- Any other callers need to be identified via repository-reference audit

### Input/Output Units
- **Input**: USD amount (e.g., $500/kg EAP price)
- **Output**: GCC equivalent (divides input by 100.0)
- **Current behavior**: In-situ savings recorded at 1/100th of their USD value

### Persistence
- No persistence — method returns a constant, not a stored value
- `Financial::ExchangeRate` model (DB-backed) is separate and NOT connected to this service

### Test Expectations
- Need to identify any specs that assert on this value
- Likely stale test assertions that need correction

### Denomination/Scaling Possibility
- Could the 100.0 be intentional (e.g., GCC has different denomination than USD)?
- Unlikely given documented 1:1 peg in `economic_parameters.yml`
- Requires confirmation from Gemini on economic design intent

### Actual Financial Impact
- **Example**: $500/kg EAP price → $500 savings_usd → 5.0 GCC recorded (should be 500 GCC at 1:1)
- **Impact**: In-situ savings are deflated by 100× — AI Manager may reject economically viable in-situ production decisions based on artificially low savings data

### AI Manager/Project-Viability Impact
- Unconfirmed — need to check if any AI services call `record_in_situ_savings()` and use the result for decision-making
- If they do, AI would see artificially deflated savings and potentially reject economically viable decisions

---

## Status Labels

| Aspect | Status |
|--------|--------|
| Current policy | USD = GCC 1:1 (confirmed in `economic_parameters.yml`) |
| Verified implementation | Returns 100.0 — NOT matching policy |
| Classification | Bug / stale test artifact |
| Urgency | LOW — no runtime trigger, affects in-situ savings calculations only |

---

## Do Not Prescribe Yet

- Do NOT change the value to 1.0 until caller inventory, test expectations, and financial/AI impact are fully understood
- This is an investigation task, not a fix task

---

**Status**: Requires Claude review (financial architecture implications) and Gemini review (economic design intent for GCC denomination). Not dispatch-ready.
