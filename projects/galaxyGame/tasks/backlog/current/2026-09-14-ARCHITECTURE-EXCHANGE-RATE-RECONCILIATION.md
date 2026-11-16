# Exchange-Rate Architecture Reconciliation — In-Memory vs. DB Systems

**Status**: DRAFT — Not dispatch-ready  
**Created**: 2026-09-14  
**Last Updated**: 2026-09-14  
**Priority**: LOW (architecture gap, not urgent)  
**Type**: ARCHITECTURE TASK CANDIDATE  

---

## Finding

Two separate exchange-rate systems exist but are NOT connected:

### System A: `Financial::ExchangeRateService` (in-memory)
- **Location**: `galaxy_game/app/services/financial/exchange_rate_service.rb`
- **Type**: In-memory hash store
- **Persistence**: Ephemeral (process lifetime only)
- **Usage**: General-purpose rate lookups via `convert(amount, from, to)`
- **Default behavior**: Returns 1:1 for all conversions when no rate is set

### System B: `Financial::ExchangeRate` model (DB-backed)
- **Location**: `galaxy_game/app/models/financial/exchange_rate.rb`
- **Type**: PostgreSQL database model
- **Persistence**: Persistent (survives restarts)
- **Usage**: Bonds only (via `BondRepayment` currency conversion)
- **Methods**: `get_rate` and `set_rate` class methods using first_or_initialize pattern

**Gap**: Setting a rate via one system does NOT update the other. No code connects them.

---

## Scope

### Ownership
- Who owns each system? Is there a designated owner for exchange-rate infrastructure?
- Should there be a single source of truth, or are separate systems intentional?

### Callers
- `ExchangeRateService`: General-purpose rate lookups (no automatic phase-transition logic)
- `ExchangeRate` model: Bond repayment currency conversion only
- Need repository-reference audit to identify all callers of each system

### Persistence
- In-memory: Lost on restart — requires re-seeding
- DB-backed: Survives restarts — persistent state

### Current USD/GCC Peg Behavior
- Documented peg: 1:1 in `economic_parameters.yml`
- `ExchangeRateService`: Returns 1:1 by default (no rate set)
- `ExchangeRate` model: First-or-initialize pattern — may or may not have a stored rate
- Both systems effectively return 1:1 currently, but for different reasons

### Bond Usage
- Bonds use `Financial::ExchangeRate` model for repayment currency conversion
- If the DB model has no stored rate, what happens during bond repayment?
- Need to verify bond repayment behavior with current data

### Admin UI Use
- Is there an admin UI for setting exchange rates?
- If so, which system does it update?
- Does the admin UI update both systems or just one?

### Future Multi-Currency Compatibility
- If multi-currency is planned, both systems need to support multiple currencies
- In-memory service would need re-seeding on restart — is this acceptable?
- DB model provides persistence but may need schema changes for multi-currency

### Migration Options
1. **Unify**: Replace both with a single system (DB-backed preferred for persistence)
2. **Sync**: Add sync mechanism so setting rate in one updates the other
3. **Separate**: Document that they serve different purposes and keep them separate
4. **Deprecate**: If one is unused, deprecate it

### Test Plan
- Verify bond repayment behavior with current data
- Test rate setting via both systems
- Verify no cross-system synchronization exists
- Test restart behavior (in-memory loss vs. DB persistence)

---

## Status Labels

| Aspect | Status |
|--------|--------|
| Current policy | USD = GCC 1:1 (no dynamic rates in use) |
| Verified implementation | Two independent systems, neither actively used for GCC valuation |
| Classification | Architecture-reconciliation candidate — deferred pending multi-currency requirements |
| Urgency | LOW — no active usage, no immediate impact |

---

## Relationship to P0

- This is NOT part of P0 scope
- P0 excludes exchange-rate architecture consolidation
- This should be addressed separately when multi-currency or dynamic-rate features are planned

---

**Status**: Requires Claude review (financial architecture implications) and Gemini review (economic design intent for multi-currency). Not dispatch-ready.
