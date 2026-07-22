---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
date_created: 2026-07-22
depends_on: 2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md
---

## ⚡ Minimal Handoff — Copy This Entire Block to Dispatch (After Phase 1 Completion)

```
You are **Implementation Agent** for Samvera Hyku GA Multi-Tenant Fix — PHASE 2 (Upstream PR).

PROJECT: Samvera Hyrax (upstream)  
TASK FILE: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYRAX-PR.md  
GITHUB ISSUES: samvera/hyku#2985 | samvera/hyku#2995 | notch8/palni_palci_knapsack#611  
PHASE: 2 of 2 (Upstream PR to samvera/hyrax for permanent fix)  
DEPENDS ON: Phase 1 completion (Hyku override implementation)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CRITICAL CONTEXT:

Phase 1 implemented a temporary override in Hyku to fix multi-tenant analytics.
Phase 2 pushes the same fix upstream to samvera/hyrax so ALL Hyku instances benefit.

After Phase 2 is merged, Hyku will bump Hyrax version and remove Phase 1 overrides.

STEP 1 — CLONE & SETUP HYRAX REPOSITORY

  cd /Users/tam0013/Documents/git
  git clone https://github.com/samvera/hyrax.git
  cd hyrax
  git checkout main
  git pull
  git checkout -b fix/ga-tenant-property-scoping

  Copy output to chat confirming branch creation.

STEP 2 — READ PREREQUISITES IN THIS ORDER

  1. /Users/tam0013/Documents/git/agent-tasks/README.md (EXECUTOR Role section only)
  2. /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md
  3. The completed Phase 1 synthesis: summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md
  4. Phase 1 task file (completed): tasks/completed/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md
  5. This task file (all sections)

  DO NOT SKIP. All contain critical context.

STEP 3 — EXTRACT & ADAPT PHASE 1 CODE FOR HYRAX

  Phase 1 created overrides in Hyku. Phase 2 applies the same fixes directly to Hyrax.
  
  Changes are the same as documented in original implementation plan:
  - Modify Hyrax::Analytics::Ga4 to accept property_id parameter
  - Update analytics controllers to use tenant property_id
  - Add RSpec tests (in hyrax/spec/ directory)
  
  Use Phase 1 as reference, but target Hyrax files directly (no overrides needed).

STEP 4 — CREATE SYNTHESIS ADDENDUM (Quick Gate)

  Confirm in chat you understand:
  
  ✓ Changes are same as Phase 1, but applied to Hyrax source (not Hyku overrides)
  ✓ Target files: app/services/hyrax/analytics/ga4.rb, app/controllers/hyrax/admin/analytics/*
  ✓ RSpec tests go in hyrax/spec/ directory
  ✓ This PR will be reviewed by Samvera maintainers (Rob already approved scope)
  
  Post: "SYNTHESIS ADDENDUM — Phase 2 upstream PR approach understood. Ready to implement."
  WAIT for approval before proceeding.

STEP 5 — IMPLEMENT IN HYRAX SOURCE

  Modify Hyrax files directly (not creating overrides, applying changes at source).
  See "Implementation Plan" section for exact files and code.

STEP 6 — WRITE RSPEC TESTS

  Tests go in hyrax/spec/ directory (not hyku/spec/).
  Same test scenarios as Phase 1, but in Hyrax test structure.

STEP 7 — COMMIT & CREATE PR

  Commit to fix/ga-tenant-property-scoping branch in Hyrax repo.
  Create PR to samvera/hyrax with provided template.
  Link to issues: samvera/hyku#2985, samvera/hyku#2995, PALNI/PALCI#611

STEP 8 — AFTER PR ACCEPTED

  Once Hyrax PR is merged and new version released:
  - Hyku will bump Hyrax version in Gemfile
  - Phase 1 override files can be removed from Hyku
  - Multi-tenant analytics fixed for all Hyku deployments

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CRITICAL CHECKPOINTS (stop if any fail):
  ✓ Hyrax repository cloned and branch created (Step 1)
  ✓ Prerequisites read (Step 2)
  ✓ Synthesis addendum approved (Step 4)
  ✓ All Hyrax tests pass (Step 6)
  ✓ PR created and linked to issues (Step 7)

QUESTIONS? Post in chat. Blockers? Document and escalate. DO NOT GUESS.
```

---

# TASK: Create Upstream PR to Hyrax for GA Multi-Tenant Fix (Phase 2)

**Status**: BACKLOG (Ready after Phase 1 completion)
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-22
**Phase**: 2 of 2 (Upstream PR to samvera/hyrax)
**GitHub Issues**: [samvera/hyku#2985](https://github.com/samvera/hyku/issues/2985) | [samvera/hyku#2995](https://github.com/samvera/hyku/issues/2995) | [PALNI/PALCI#611](https://github.com/notch8/palni_palci_knapsack/issues/611)
**Depends On**: Phase 1 task completion
**Reviewer Pre-Approved**: Rob (Samvera maintainer, 2026-07-07)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Agent Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section only)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md`
3. **Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md`
4. **Phase 1 Completed**: Read the completed Phase 1 task (reference for code changes)
5. **This Task File**: Everything below

---

## Executive Summary

**Context**: Phase 1 implemented temporary overrides in Hyku to fix multi-tenant analytics immediately. Phase 2 pushes these same fixes **upstream to samvera/hyrax** so all Hyku instances benefit from the permanent fix.

**Scope**: Apply the exact same code changes from Phase 1 directly to Hyrax source files (no overrides needed).

**Benefit**: 
- ✅ Permanent fix in Hyrax (all Hyku deployments get it after version bump)
- ✅ Helps PALNI/PALCI and other multi-tenant Hyku Commons instances
- ✅ Contributes to Samvera community
- ✅ After merge, Phase 1 overrides can be removed from Hyku

**Timeline**: After Phase 2 PR is merged and Hyrax releases new version

---

## Why Phase 2 Matters

### Phase 1 vs Phase 2

| Aspect | Phase 1 (Hyku Override) | Phase 2 (Hyrax Upstream) |
|--------|------------------------|------------------------|
| **Location** | This Hyku instance | samvera/hyrax repository |
| **Timeline** | Immediate (now) | After merge + release |
| **Scope** | This instance only | All Hyku deployments |
| **PALNI/PALCI** | Not helped (yet) | Fixed permanently |
| **Code** | Temporary overrides | Permanent source changes |
| **Cleanup** | Removed after Phase 2 | No cleanup needed |

### Why Both Are Needed

- **Phase 1**: Solves urgent problem TODAY for this Hyku instance
- **Phase 2**: Solves problem PERMANENTLY for entire Samvera community

---

## Implementation Plan

### Target Files (Same as Phase 1, but in Hyrax repository)

1. **`app/services/hyrax/analytics/ga4.rb`**
   - Add `property_id` parameter to `initialize` method
   - Modify `property` method to use custom property_id or fallback

2. **`app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`**
   - Get tenant property_id from `current_account.google_analytics_property_id`
   - Pass to Ga4: `Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)`

3. **`app/controllers/hyrax/admin/analytics/work_reports_controller.rb`**
   - Same as collection_reports_controller

### Code Changes

These are **identical to Phase 1**, applied directly to Hyrax source files.

#### 1. Ga4.rb Modification

```ruby
# app/services/hyrax/analytics/ga4.rb

def initialize(property_id: nil, property_name: nil)
  @custom_property_id = property_id
  @property_name = property_name
end

def property
  property_id = @custom_property_id || config.property_id
  "properties/#{property_id}"
end
```

#### 2. Collection Reports Controller Modification

```ruby
# app/controllers/hyrax/admin/analytics/collection_reports_controller.rb

def index
  tenant_property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
  @analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)
  @all_top_collections = @analytics.top_collections_for_deposit
  # ... rest of original method
end
```

#### 3. Work Reports Controller Modification

Same as collection_reports_controller.

---

## Testing Strategy

### RSpec Tests in Hyrax

Create tests in **hyrax/spec/** directory (Hyrax's test structure):

1. **`spec/services/hyrax/analytics/ga4_spec.rb`**
   - Test property_id parameter acceptance
   - Test fallback to config.property_id
   - Test priority of custom_property_id over ENV

2. **`spec/controllers/hyrax/admin/analytics/collection_reports_controller_spec.rb`**
   - Test tenant isolation with distinct property_ids
   - Test fallback to ENV when Account field is nil

3. **`spec/controllers/hyrax/admin/analytics/work_reports_controller_spec.rb`**
   - Same as collection_reports_controller

### Test Execution

```bash
cd /Users/tam0013/Documents/git/hyrax
bundle exec rspec spec/services/hyrax/analytics/ga4_spec.rb
bundle exec rspec spec/controllers/hyrax/admin/analytics/
```

---

## PR Description Template

```markdown
## Fix: GA Multi-Tenant Property Scoping

### Issues
Fixes samvera/hyku#2985, samvera/hyku#2995
Relates to notch8/palni_palci_knapsack#611

### Problem
Multi-tenant Hyku deployments show identical analytics across all tenants. 
All tenants query the same GA property ID from ENV instead of tenant-specific IDs.

Production case (PALNI/PALCI #611): 248+ phantom entries per tenant, 
100% data loss for one tenant due to cross-tenant ID overlap.

### Solution
- Modify `Ga4` client to accept tenant-specific `property_id` parameter
- Update analytics controllers to fetch property_id from Account model
- Pass property_id to Ga4 on initialization
- Fallback to ENV for single-tenant deployments (backward compatible)

### Changes
- `app/services/hyrax/analytics/ga4.rb`: Add property_id parameter
- `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`: Pass tenant property_id
- `app/controllers/hyrax/admin/analytics/work_reports_controller.rb`: Pass tenant property_id
- `spec/services/hyrax/analytics/ga4_spec.rb`: Test property_id parameter
- `spec/controllers/hyrax/admin/analytics/*_spec.rb`: Test tenant isolation

### Testing
- RSpec tests verify tenant isolation
- Manual multi-tenant testing confirms different property IDs are queried
- Fallback to ENV verified for single-tenant deployments

### Backward Compatibility
✅ Single-tenant deployments unaffected (ENV fallback)
✅ No breaking changes to Ga4 API (property_id parameter is optional)

### Impact
- Fixes multi-tenant analytics isolation across all Hyku instances using this Hyrax version
- Eliminates phantom entries caused by cross-tenant data commingling
- Resolves production issue reported in PALNI/PALCI #611
```

---

## Acceptance Criteria

✓ **All 4 files modified** in Hyrax with tenant property_id scoping  
✓ **RSpec tests pass** (3 test files in hyrax/spec/)  
✓ **Code review approved** by Samvera maintainers  
✓ **PR merged** to samvera/hyrax main branch  
✓ **New Hyrax version released** (Hyrax team responsibility)  
✓ **Hyku can bump version** in Gemfile (separate Hyku task)  

---

## After PR Is Accepted

### For Hyrax Maintainers
1. Merge PR to main
2. Release new Hyrax version
3. Update samvera/hyrax GitHub releases page

### For Hyku Team
1. Create new task: "Bump Hyrax version after GA fix release"
2. Update Gemfile to new Hyrax version
3. Remove Phase 1 override files from Hyku
4. Test in multi-tenant environment
5. Deploy

---

## Estimated Work Breakdown

- **Code Changes**: Same as Phase 1 (~1-2 hours)
- **RSpec Tests**: Same as Phase 1 (~1 hour)
- **PR Creation & Revision**: 30-45 minutes (depends on maintainer feedback)
- **Total**: ~2.5-3 hours

---

## Branch & Git Workflow

**Repository**: samvera/hyrax  
**Branch Name**: `fix/ga-tenant-property-scoping`  
**Base Branch**: `main`  

```bash
cd /Users/tam0013/Documents/git/hyrax
git checkout main
git pull
git checkout -b fix/ga-tenant-property-scoping
```

---

## Gotchas & Architecture Notes

### 1. Hyrax Code Structure May Differ Slightly
Verify exact method signatures in Hyrax before modifying:
```bash
grep -n "def initialize" app/services/hyrax/analytics/ga4.rb
grep -n "def property" app/services/hyrax/analytics/ga4.rb
```

### 2. Test Structure in Hyrax
Tests go in `hyrax/spec/`, not `hyku/spec/`.

### 3. ENV Variable Handling
Use same `ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')` pattern as Phase 1 for consistency.

### 4. Account Model Dependency
Hyrax may not have `current_account.google_analytics_property_id` by default (that's a Hyku extension).
Handle gracefully:
```ruby
property_id = current_account.google_analytics_property_id rescue nil
property_id ||= ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
```

---

## What This PR Enables

After Hyrax merges and releases:
- ✅ All Hyku instances using new Hyrax version get multi-tenant analytics fix
- ✅ PALNI/PALCI gets permanent fix (not just Phase 1 workaround)
- ✅ Phase 1 override code can be removed from Hyku
- ✅ No more cross-tenant data commingling in analytics

---

## Success Criteria (for Task Completion)

1. ✅ All 4 files modified in Hyrax with correct changes
2. ✅ RSpec tests pass (unit + integration in hyrax/spec/)
3. ✅ PR created and linked to samvera/hyku issues
4. ✅ PR reviewed and approved by Samvera maintainers
5. ✅ PR merged to samvera/hyrax main
6. ✅ Task moved to `completed/`

---

## Timeline Dependency

⏳ **This task is BACKLOG** — Only moves to ACTIVE after Phase 1 is complete and approved.

Once Phase 1 is in production:
1. Phase 1 override code is working and tested
2. Phase 2 can proceed with confidence (code is proven, now going upstream)
3. Phase 2 PR can reference Phase 1 as proof-of-concept

---

## Contact & Escalation

- **Samvera Coordination**: Reach out to maintainers (Rob is contact) if questions
- **PR Feedback**: Be responsive to maintainer review feedback
- **Scope Questions**: Escalate to planning agent, do not modify scope
- **Blocker**: Document and escalate

---

## Reference Documents

- **Synthesis Report**: `2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md`
- **Phase 1 Task**: `2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md` (completed)
- **Root Cause**: `2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md`
- **Validation**: `2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md`
- **Hyrax Repo**: https://github.com/samvera/hyrax
