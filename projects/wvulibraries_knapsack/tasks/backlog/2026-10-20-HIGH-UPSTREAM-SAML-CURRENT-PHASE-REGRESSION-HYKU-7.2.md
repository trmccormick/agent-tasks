---
type: upstream-issue-report
priority: HIGH
status: pending-report
created: 2026-10-20
assignee: null
effort: 2-hours
acceptance-criteria:
  - Check Hyku and omniauth-saml upstream repos for existing issue reports
  - If issue exists, update UPSTREAM_CONTRIBUTION_AUDIT.md with link
  - If not reported, file issue on Hyku repo with reproduction steps
  - Document expected upstream fix location (gem update or decorator change)
tags:
  - hyku-7.2-regression
  - saml-authentication
  - temporary-workaround
  - technical-debt
---

# UPSTREAM: SAML current_phase Regression in Hyku 7.2

## Issue Summary

**Status**: Temporary workaround in place (`config/initializers/saml_phase_patch.rb`)
**Severity**: BLOCKING for any tenant using SAML auth on Hyku 7.2
**Scope**: Not WVU-specific — affects all Hyku 7.2 deployments with SAML

Hyku 7.2 with omniauth-saml ~> 2.1 has a breaking regression: `OmniAuth::Strategies::SAML#current_phase` now returns Boolean instead of Integer. The omniauth decorator code still attempts arithmetic on this value (`current_phase + 1`), causing immediate authentication failure.

## Error Details

**Error Message**:
```
NoMethodError (undefined method '+' for true:TrueClass)
```

**Error Location**: `lib/omni_auth/strategies/saml_decorator.rb:32` in `other_phase` method

**Affected Versions**:
- Hyku 7.2.0 (and 7.2-stable branch)
- omniauth-saml ~> 2.1 (tested with 2.1.0, 2.1.2, 2.1.3)

## Reproduction

1. Deploy Hyku 7.2 with SAML authentication enabled
2. Attempt to log in via SAML (e.g., through Okta)
3. Authentication crashes with NoMethodError above
4. Catalog inaccessible — error page on every request

**Note**: Bug is NOT visible in local dev environments that don't trigger the SAML auth flow.

## Temporary Workaround

**File**: `config/initializers/saml_phase_patch.rb`
**Approach**: Monkey-patch `OmniAuth::Strategies::SAML#current_phase` to wrap the method and ensure integer return
**Limitation**: This is a patch, not a fix. Should be removed once upstream issue is resolved.

## Next Steps (For Upstream Report)

### 1. Investigation
- [ ] Check Samvera/Hyku issues for duplicate reports of SAML in 7.2
- [ ] Check omniauth-saml gem changelog/releases between 2.0.x and 2.1.x
- [ ] Determine if issue is in:
  - omniauth-saml gem (regression in 2.1.x)
  - Hyku's integration of omniauth-saml (decorator not updated for new behavior)
  - hyrax-webapp's SAML decorator (old assumption about return type)

### 2. Fix Location (Likely One Of These)
- **Option A**: Update omniauth-saml to revert Boolean return to Integer (if recent change broke existing code)
- **Option B**: Update hyrax-webapp's `lib/omni_auth/strategies/saml_decorator.rb` to handle Boolean return from current_phase
- **Option C**: Update Hyku's integration to normalize the return value before use

### 3. Report To Upstream
- [ ] File issue on samvera/hyku with:
  - Clear reproduction steps
  - Error trace
  - Affected versions
  - Proposed solution (patch example if helpful)
  - Link to this workaround as temporary mitigation

## References

- **Workaround**: `wvu_knapsack/config/initializers/saml_phase_patch.rb`
- **Audit**: `agent-tasks/projects/wvulibraries_knapsack/UPSTREAM_CONTRIBUTION_AUDIT.md` (Issue #9)
- **Hyku Repo**: https://github.com/samvera/hyku
- **omniauth-saml Gem**: https://github.com/omniauth/omniauth-saml

## Tracking

**Created**: 2026-10-20 during Hyku 7.2 upgrade testing on hykudev
**Discovered By**: WVU Knapsack team during feature/hyku-v7.2.0-upgrade branch testing
**Branch**: `feature/hyku-v7.2.0-upgrade`
**Commit**: 2b095a1 (fix: Patch current_phase to return integer for omniauth-saml 7.2 compatibility)

---

## Agent Dispatch Interface

**Goal**: File upstream issue and track expected fix

**Input**:
- Hyku issue tracker access
- omniauth-saml issue/PR history
- This task file for context

**Expected Output**:
- Link to filed upstream issue (or existing duplicate)
- Updated UPSTREAM_CONTRIBUTION_AUDIT.md with issue link
- Task marked as `pending-upstream-fix`

**Acceptance Criteria**:
- ✅ Upstream issue exists or newly filed
- ✅ Issue includes reproduction steps and affected versions
- ✅ UPSTREAM_CONTRIBUTION_AUDIT.md updated with link
- ✅ Task status updated to reflect upstream tracking
