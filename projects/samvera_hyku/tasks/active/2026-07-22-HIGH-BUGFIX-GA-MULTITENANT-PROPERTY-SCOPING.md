---
status: active
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
date_created: 2026-07-22
date_assigned: 2026-07-22
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: Samvera Hyku
Task: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-PROPERTY-SCOPING.md

PREREQUISITE RESEARCH (already completed, saved for your reference):
1. /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md
2. /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md
3. /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/handoffs/session_handoff_2026-07-07_GA-MULTITENANT-IMPLEMENTATION-READY.md

BRANCH INFO:
- Base branch: main
- Branch name: fix/ga-tenant-property-scoping
- Work directory: /Users/tam0013/Documents/git/hyku

READ FIRST: Task file contains all prerequisites, implementation plan, acceptance criteria, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/summaries/
  Filename pattern: YYYY-MM-DD-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Implement GA Multi-Tenant Property Scoping (Fix #2985 & #2995)

**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-22
**Last Updated**: 2026-07-22
**Reviewer Approved**: 2026-07-07 (Rob, Samvera maintainer)
**GitHub Issues**: [#2985](https://github.com/samvera/hyku/issues/2985) | [#2995](https://github.com/samvera/hyku/issues/2995)
**Production Validation**: [PALNI/PALCI #611](https://github.com/notch8/palni_palci_knapsack/issues/611)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Agent Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section only)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md`
3. **Research Task**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md`
4. **Validation Task**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md`
5. **Implementation Handoff**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/handoffs/session_handoff_2026-07-07_GA-MULTITENANT-IMPLEMENTATION-READY.md`
6. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. All prerequisites contain critical context.

---

## Executive Summary

**The Problem**:
- Multi-tenant Hyku deployments show identical analytics across all tenants
- Root cause: All tenants query the **same GA property ID from ENV** instead of tenant-specific IDs
- Real-world impact: PALNI/PALCI production instance shows 248+ phantom "Collection deleted" entries per tenant, 100% data loss for one tenant
- GitHub issues: #2985 & #2995 (Samvera), PALNI/PALCI #611 (production case)

**The Solution**:
- Modify `Ga4` client to accept tenant-specific `property_id` parameter
- Update analytics controllers to fetch tenant property ID from `current_account.google_analytics_property_id`
- Pass tenant property ID to Ga4 client on initialization
- Fallback to ENV for single-tenant deployments (backward compatible)

**Impact**:
- ✅ Each tenant's GA queries only their own GA property
- ✅ Eliminates cross-tenant data commingling
- ✅ Fixes phantom "Collection deleted" entries caused by unresolvable collection IDs from other tenants
- ✅ Stops GA4 error quota exhaustion from failed Solr lookups

**Reviewer Status**: ✅ APPROVED by Rob (Samvera maintainer, 2026-07-07)

---

## Context: Why This Matters

**Samvera Issues #2985 & #2995**:
- Multi-tenant Hyku instances report identical analytics across all tenants
- Issue #2995 shows phantom "Collection deleted" entries for collections that don't exist in the tenant

**PALNI/PALCI Issue #611** (Real-World Validation):
- Production Hyku Commons with 3+ tenants
- Tenant A: 290 entries in report, only 68 actual collections (81% phantom entries)
- Tenant B: 70 entries in report, only 82 actual collections (100% "Collection deleted")
- Cross-tenant proof: 46 collection IDs overlap between unrelated tenants
- Quote from issue: "This may be an artifact from when GA code was handled at the ENV level instead of specifically per tenant"

**Your Code Fixes This Exact Problem**:
- Move GA property ID from ENV (shared) to Account model (per-tenant)
- Backward compatible: Single-tenant deployments fall back to ENV

---

## Implementation Plan (Reviewer-Approved Scope)

### Fix #1: Use Tenant-Specific GA Property IDs (APPROVED)

**Files to Modify** (4 files, ~40-50 lines total):

#### 1. `app/services/hyrax/analytics/ga4.rb`
Add `property_id` parameter to `initialize` method, modify `property` method to use it.

**Current Code**:
```ruby
def initialize(property_name: nil)
  @property_name = property_name
end

def property
  "properties/#{config.property_id}"
end
```

**Modified Code**:
```ruby
def initialize(property_id: nil, property_name: nil)
  @custom_property_id = property_id
  @property_name = property_name
end

def property
  property_id = @custom_property_id || config.property_id
  "properties/#{property_id}"
end
```

#### 2. `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`
In `index` method: Get tenant property_id from `current_account.google_analytics_property_id`, pass to Ga4 client.

**Modification**:
- Find: `@analytics = Hyrax::Analytics::Ga4.new` (in `index` method)
- Replace with:
```ruby
tenant_property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
@analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)
```

#### 3. `app/controllers/hyrax/admin/analytics/work_reports_controller.rb`
Same as collection_reports_controller.

**Modification**:
- Find: `@analytics = Hyrax::Analytics::Ga4.new` (in `index` method)
- Replace with:
```ruby
tenant_property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
@analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)
```

#### 4. `app/controllers/hyrax/admin/analytics/analytics_controller.rb` (Optional)
Create DRY helper method to avoid duplication across controllers.

**Optional Addition**:
```ruby
private

def initialize_analytics(property_id: nil)
  property_id = property_id || current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
  Hyrax::Analytics::Ga4.new(property_id: property_id)
end
```

Then in both collection and work reports controllers:
```ruby
@analytics = initialize_analytics
```

### Fix #2: Skip Missing Collections (REJECTED by Reviewer)
**Status**: ❌ NOT INCLUDED in this task
- Reviewer decision: Keep "Collection deleted" behavior (preserves visibility into genuine deletions)
- After Fix #1, phantom entries will disappear anyway because GA won't return other tenants' IDs

---

## Testing Strategy

### Multi-Tenant Test Instance Setup
1. Ensure you have 2+ test tenants set up locally (e.g., "tenant-a" and "tenant-b" at localhost)
2. Each tenant should have a distinct Google Analytics property (or mock property ID)
3. Configure each tenant's `google_analytics_property_id` in Account model or via Account settings UI

### Manual Testing Steps
1. **Login to Tenant A**, navigate to Analytics → Collections Report
2. **Record**: Tenant A's collection count and top collections
3. **Login to Tenant B**, navigate to Analytics → Collections Report
4. **Verify**: Tenant B shows different collections than Tenant A (or same collections with different counts)
5. **Verify**: Numbers differ between tenants (not identical)

### RSpec Tests to Add

**Test File 1**: `spec/services/hyrax/analytics/ga4_spec.rb`
- Test that `Ga4.new(property_id: "12345")` stores custom property_id
- Test that `property` method returns custom property_id when provided
- Test that `property` method falls back to config.property_id when custom_property_id is nil

**Test File 2**: `spec/controllers/hyrax/admin/analytics/collection_reports_controller_spec.rb`
- Test that `index` action passes `current_account.google_analytics_property_id` to Ga4.new
- Mock multi-tenant scenario: Tenant A with property_id "111", Tenant B with property_id "222"
- Verify each tenant's controller initializes Ga4 with correct property_id

**Test File 3**: `spec/controllers/hyrax/admin/analytics/work_reports_controller_spec.rb`
- Same as collection_reports_controller tests

### Verification Checklist
- [ ] All 4 files modified (or 3 if skipping optional helper)
- [ ] Code changes compile without errors: `bundle exec rails runner "puts 'OK'"`
- [ ] RSpec tests pass: `bundle exec rspec spec/services/hyrax/analytics/ga4_spec.rb`
- [ ] Manual multi-tenant testing confirms different property IDs are used
- [ ] Fallback to ENV works for single-tenant deployments
- [ ] Backward compatibility: Code doesn't break existing single-tenant Hyku instances

---

## Code Quality & Style Guide

- **Inline Comments**: Explain tenant property_id scoping at each modification point
- **Error Handling**: Use `ENV.fetch` with default (not `.[]`) to provide clear fallback
- **Backward Compatibility**: Always default to ENV value if custom property_id is nil
- **No `!important`**: Follow Hyku style guide (avoid CSS/code hacks)
- **Naming Convention**: Use `property_id` not `propertyId` (Snake_case per Rails standard)

---

## Acceptance Criteria

✓ **Issue #2985**: Multi-tenant analytics emails show DIFFERENT numbers per tenant  
✓ **Issue #2995 (Partial)**: Phantom entries from cross-tenant commingling eliminated  
✓ **Production Case**: PALNI/PALCI Issue #611 scenario prevented in future Hyku instances  
✓ **Code Quality**: Minimal changes (~40-50 lines), backward compatible  
✓ **Tests**: RSpec tests verify tenant isolation  
✓ **Documentation**: Code comments explain tenant property_id scoping  
✓ **No Regressions**: Single-tenant deployments still work with ENV fallback  

---

## Reviewer Feedback & Decisions

**Date**: 2026-07-07  
**Reviewer**: Rob (Samvera maintainer)

| Issue | Decision | Rationale |
|-------|----------|-----------|
| Fix #1 (Tenant property IDs) | ✅ APPROVED | Solves root cause, backward compatible |
| Fix #2 (Skip "Collection deleted") | ❌ REJECTED | Keep behavior to preserve visibility into genuine deletions |

**Note**: After Fix #1 is implemented, phantom entries will naturally disappear because GA won't return other tenants' collection IDs. Fix #2 is unnecessary.

---

## Gotchas & Architecture Notes

### 1. Ga4 Client Initialization
The `Ga4` class is used in multiple places:
- Analytics controllers (3 places: collections, works, optional base)
- Possibly in Jobs or Backgrounds (check codebase)

**Search before modifying**:
```bash
grep -r "Hyrax::Analytics::Ga4.new" app/
```

**Why**: Ensure all GA4 instantiations pass property_id where tenant context is available.

### 2. Account Model `google_analytics_property_id` Column
Verify this column exists:
```bash
grep -r "google_analytics_property_id" db/schema.rb
```

If it doesn't exist, create a migration (but this is unlikely since the field is probably already in Hyku).

**If field missing**, add migration:
```ruby
add_column :accounts, :google_analytics_property_id, :string
```

### 3. Backward Compatibility
Single-tenant deployments that don't have `current_account.google_analytics_property_id` set should:
1. Fetch from ENV (fallback)
2. Still work (no errors)

**Test this explicitly**: Run single-tenant instance without setting the Account field, verify analytics still work.

### 4. Multi-Tenant vs Single-Tenant
- **Multi-tenant**: Each tenant has distinct Account with unique property_id
- **Single-tenant**: Only one Account exists, uses ENV fallback

Hyku can run as either. Your code must support both.

---

## Estimated Work Breakdown

- **Code Implementation**: 1-2 hours
  - Modify 4 files (~40-50 lines)
  - Add inline comments
  - Compile and basic verification

- **RSpec Test Writing**: 1-1.5 hours
  - Write 3 test files
  - Mock multi-tenant scenarios
  - Verify tenant isolation

- **Manual Testing**: 30 minutes - 1 hour
  - Setup 2 test tenants
  - Configure GA properties
  - Test analytics reports

- **Documentation**: 30 minutes
  - Update code comments
  - Prepare PR description

**Total**: 3-4 hours (as originally estimated)

---

## Git Workflow

**Branch Name**: `fix/ga-tenant-property-scoping`  
**Base Branch**: `main`  
**Commit Pattern**: One commit per file (or atomic changes if grouped)

**Example Commits**:
```
1. "refactor: add property_id parameter to Ga4 client"
2. "fix: use tenant property_id in collection_reports_controller"
3. "fix: use tenant property_id in work_reports_controller"
4. "test: add multi-tenant GA4 integration tests"
```

---

## PR Description Template

```markdown
## Issue
Fixes #2985, #2995
Relates to notch8/palni_palci_knapsack#611

## Problem
Multi-tenant Hyku deployments show identical analytics across all tenants. 
All tenants query the same GA property ID from ENV instead of tenant-specific IDs.

Production case (PALNI/PALCI #611): 248+ phantom entries per tenant, 
100% data loss for one tenant due to cross-tenant ID overlap.

## Solution
- Modify Ga4 client to accept tenant-specific `property_id` parameter
- Update analytics controllers to fetch property_id from Account model
- Pass property_id to Ga4 on initialization
- Fallback to ENV for single-tenant deployments (backward compatible)

## Changes
- `app/services/hyrax/analytics/ga4.rb`: Add property_id parameter
- `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`: Pass tenant property_id
- `app/controllers/hyrax/admin/analytics/work_reports_controller.rb`: Pass tenant property_id
- `spec/services/hyrax/analytics/ga4_spec.rb`: Test property_id parameter
- `spec/controllers/hyrax/admin/analytics/*_spec.rb`: Test tenant isolation

## Testing
- RSpec tests verify tenant isolation
- Manual multi-tenant testing confirms different property IDs are queried
- Fallback to ENV verified for single-tenant deployments

## Backward Compatibility
✅ Single-tenant deployments unaffected (ENV fallback)
✅ No breaking changes to Ga4 API (property_id parameter is optional)
```

---

## Reference Documents (Already Completed)

All research, validation, and approval is documented in:

1. **Root Cause Analysis**: `tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md`
2. **Production Validation**: `tasks/active/2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md`
3. **Implementation Handoff**: `handoffs/session_handoff_2026-07-07_GA-MULTITENANT-IMPLEMENTATION-READY.md`

---

## Success Criteria (for Task Completion)

1. ✅ All 4 files modified with tenant property_id scoping
2. ✅ RSpec tests written and passing (3 test files)
3. ✅ Manual multi-tenant testing completed and documented
4. ✅ PR created, linked to issues #2985, #2995, PALNI/PALCI #611
5. ✅ Code review passed (Samvera maintainer approval)
6. ✅ Task file moved to `tasks/completed/`
7. ✅ `status.md` updated with completion note

---

## Contact & Escalation

- **Questions**: Post in task chat (this task is self-contained)
- **Blockers**: Document in task file or chat, do not proceed without resolution
- **Scope Changes**: Do NOT implement, escalate to planning agent
- **Production Issues**: Stop, document, escalate immediately
