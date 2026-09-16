# DRAFT ONLY — NOT DISPATCHED

---
status: backlog
priority: HIGH
type: architecture
system_domain: FINANCIAL | AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: GCC Issuance Authorization and LDC Recipient Routing

**Status**: DRAFT — Not dispatch-ready  
**Priority**: HIGH  
**Type**: architecture  
**Created**: 2026-09-15  
**Last Updated**: 2026-09-15  

---

## Context

GCC is centrally managed, crypto-inspired, nonphysical virtual currency. The canonical policy states:
- LDC-controlled or explicitly LDC-authorized infrastructure may issue GCC.
- Compute hardware alone is **not** GCC mint authority.
- All newly issued GCC is intended to go to the existing LDC GCC account.

The current codebase does not enforce these rules. Any object with an account and computer units can execute mining behavior via the `CryptocurrencyMining` concern, regardless of LDC authorization status. No explicit LDC authorization guard, facility eligibility rule, or canonical issuer mechanism exists in any mining path.

---

## Problem Statement

The `CryptocurrencyMining#mine_gcc` concern is a generic compute-capable mining infrastructure that can be included by any model. It has no currency-specific issuance policy, no LDC authorization check, and no canonical recipient routing. This means:
- Unauthorized compute-capable craft/satellites/settlements can create net-new GCC.
- Mining recipients vary by entry path (satellite's account, owner's account, LDC's account).
- No auditable identity distinguishes GCC issuance from ordinary transfers (MiningLog is a separate model but Transaction records use `:deposit` type identical to other deposits).

---

## Evidence

### Current Authorization State
- **No authorization guard** in any mining path
  - `CryptocurrencyMining#can_mine_gcc?` only checks `account.present? && mining_units.any?`
  - No LDC check, no ownership verification, no facility eligibility rule
  - File: `galaxy_game/app/models/concerns/cryptocurrency_mining.rb:278`

### Current Recipient Routing (varies by path)
| Path | Recipient | Source |
|------|-----------|--------|
| Satellite tick (`process_tick`) | Owner's GCC account | `base_satellite.rb:319` |
| Scheduled job (`SatelliteMiningSchedulerJob`) | Satellite's own account | `satellite_mining_scheduler_job.rb` → `mine_gcc` |
| Mission task (`MissionTaskRunnerService`) | LDC's account (`accounts[:ldc]`) | `mission_task_runner_service.rb:30` |

### MiningLog Distinction
- **File**: `galaxy_game/app/models/mining_log.rb`
- MiningLog is a separate model/table from Transaction — this provides some audit distinction
- However, the Transaction record for mining deposits uses `transaction_type: :deposit`, identical to other deposits
- No `issuance` discriminator on Transaction records

### Per-Currency Extensibility Requirement
- Galaxy is multi-currency; GCC and USD are initial currencies
- Other Earth currencies and future off-Earth currency types remain possible
- Currency governance, issuance authority, eligibility, recipient routing, supply controls must be currency-specific policy
- Must preserve generic compute/power/thermal/rig capability for possible future currency policies
- Must NOT enable a future permissionless currency now

---

## Scope

### What This Task Covers
- Introduce or identify an appropriate policy/service/domain boundary for GCC-specific issuance rules
- Resolve the existing LDC account through a repository-confirmed mechanism
- Verify eligible GCC issuance infrastructure through an explicit, testable authorization rule
- Route all new GCC issuance to the resolved LDC GCC account
- Preserve generic account deposits, standard transfers, USD handling, and per-currency policy extensibility

### What This Task Does NOT Cover
- Bootstrap USD→GCC conversion correction (separate task)
- Duplicate credit prevention (separate task)
- Mining cadence/rate semantics (separate task)
- Changes to `Financial::Account#deposit` behavior unless evidence demonstrates it is the correct abstraction
- Future cryptocurrency design or permissionless currency support

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1**: The `CryptocurrencyMining` concern is included by `BaseSatellite` and potentially other models. Any authorization mechanism must not break generic compute/power/thermal/rig capability that may be needed for future currency policies.
- ❌ Wrong: Add a global LDC check to `CryptocurrencyMining#mine_gcc` that blocks all non-LDC mining
- ✅ Right: Introduce a GCC-specific issuance policy layer that sits alongside the generic mining infrastructure

⚠️ **GOTCHA 2**: The LDC account is resolved differently in each path (`accounts[:ldc]`, `owner`, `self.account`). A canonical mechanism must be identified and verified before routing.
- ❌ Wrong: Assume `accounts[:ldc]` or `owner` always resolves to the correct LDC account
- ✅ Right: Identify the repository-confirmed LDC account resolution mechanism first

⚠️ **GOTCHA 3**: MiningLog already provides some audit distinction via a separate model/table. The task must determine whether this is sufficient or if additional Transaction-level discrimination is needed.
- ❌ Wrong: Assume MiningLog alone is insufficient without test verification
- ✅ Right: Verify MiningLog's audit adequacy before adding Transaction-level changes

### Unresolved Decisions — [FILL IN]
- **[FILL IN: LDC authorization expression]** — ownership, designated facility role, authorization record, account/faction relationship, or combination?
- **[FILL IN: Canonical LDC account resolution mechanism]** — how to resolve the existing LDC GCC account
- **[FILL IN: Per-currency policy boundary]** — where does GCC-specific policy end and generic infrastructure begin?

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` | Core mining concern (may need GCC-specific policy layer) | `can_mine_gcc?` line 278, `mine_gcc` line 10 |
| `galaxy_game/app/services/mission_task_runner_service.rb` | Mission task path (LDC account resolution) | `execute_task` line 24 |

### Reference Files — read but do not edit
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/app/models/craft/satellite/base_satellite.rb` | Satellite includes CryptocurrencyMining concern |
| `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb` | Scheduled job path |
| `galaxy_game/app/models/financial/account.rb` | Account resolution patterns |
| `galaxy_game/app/models/mining_log.rb` | Existing audit distinction |
| `galaxy_game/app/services/economic_config.rb` | Per-currency policy patterns (may inform design) |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/drafts/2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md \
       projects/galaxy_game/tasks/active/2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md
```

Then open the moved file and change: `status: backlog → status: active`

### Step 1 — LDC Account Resolution
- Identify the repository-confirmed mechanism for resolving the existing LDC GCC account
- Document findings in synthesis report
- **[FILL IN: Human decision on LDC authorization expression]** before proceeding

### Step 2 — Authorization Policy Design
- Design GCC-specific issuance policy layer (not a global restriction)
- Ensure per-currency extensibility is preserved
- Verify MiningLog audit adequacy or design additional Transaction discrimination

### Step 3 — Fix Implementation
- Apply the authorization guard and recipient routing
- Preserve generic compute/power/thermal/rig capability for future currencies
- Do not enable a permissionless-GCC exception

### Step 4 — Verify
- Run any existing specs related to GCC mining, LDC accounts, or MiningLog
- Expected: unauthorized entities cannot create GCC; authorized issuance routes to LDC account

---

## Acceptance Criteria
- [ ] Unauthorized compute-capable craft/satellites/settlements cannot create net-new GCC
- [ ] Authorized GCC issuance is credited only to the existing LDC GCC account
- [ ] Issuer/facility eligibility rule and recipient selection are test-covered
- [ ] Mining/issuance has auditable identity distinct from routine transfers
- [ ] USD and ordinary multi-currency account operations remain unaffected
- [ ] Implementation does not introduce a permissionless-GCC exception
- [ ] Design supports future currency with different issuance policy without enabling it now
- [ ] **[FILL IN: Human-approved authorization expression applied]**

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- LDC account resolution cannot be verified without human input
- Root cause requires architectural decision beyond scope of this task
- Per-currency policy boundary cannot be determined from existing code patterns

---

## Dependencies
**Blocked by**: Human decision on LDC authorization expression and canonical LDC account resolution  
**Blocks**: GCC mining settlement integrity task (authorization affects which paths are valid)  
**Related tasks**: `2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md`

---

## Claude Review Checklist
- [ ] LDC authorization requirement clearly stated?
- [ ] Per-currency extensibility preserved in scope?
- [ ] Implementation approach left open for human decision?
- [ ] Scope boundaries clear (no conversion rate, no duplicate credit, no cadence)?
- [ ] Stop conditions appropriate?

---

**Status**: Requires Claude review and human decision on LDC authorization expression. Not dispatch-ready.
