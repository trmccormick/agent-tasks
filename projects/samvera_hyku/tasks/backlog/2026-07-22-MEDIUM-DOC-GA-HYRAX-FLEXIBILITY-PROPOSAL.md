---
status: backlog
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
date_created: 2026-07-22
depends_on: 2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md
---

## ⚡ Minimal Handoff — Copy This Entire Block to Dispatch (After Phase 1 Completion)

```
You are **Documentation Agent** for Samvera Hyrax GA Flexibility Proposal.

PROJECT: Samvera Hyrax (propose optional feature)  
TASK FILE: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/backlog/2026-07-22-MEDIUM-DOC-GA-MULTITENANT-HYRAX-PROPOSAL.md  
GITHUB ISSUES: samvera/hyku#2985 | samvera/hyku#2995 | notch8/palni_palci_knapsack#611  
PHASE: 2 of 2 (Discussion/proposal, not implementation)  
DEPENDS ON: Phase 1 completion (Hyku override working and tested)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CRITICAL CONTEXT:

Phase 1 implemented a permanent override in Hyku to fix multi-tenant analytics.
Phase 2 creates a DISCUSSION ISSUE in Hyrax proposing optional property_id parameter.
This is NOT an implementation task — it's a proposal to Samvera maintainers.

STEP 1 — UNDERSTAND THE CONTEXT

  Phase 1 successfully solved the problem with Hyku-specific overrides.
  Phase 2 is asking: "Would Hyrax benefit from an optional property_id parameter?"
  
  This is optional — if Hyrax maintainers say no, that's fine.
  Multi-tenant GA support stays in Hyku (where it belongs).

STEP 2 — READ PREREQUISITES

  1. /Users/tam0013/Documents/git/agent-tasks/README.md (STRATEGIST or DOCUMENTATION role)
  2. /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md
  3. The completed Phase 1 task (for reference)
  4. This task file (all sections)

STEP 3 — CREATE ISSUE IN SAMVERA/HYRAX

  Go to: https://github.com/samvera/hyrax/issues
  Create new issue with title: "Feature Request: Optional property_id parameter for Ga4 service"
  
  Use the issue template in this task file (see below).

STEP 4 — DOCUMENT THE PROPOSAL

  Issue should:
  ✓ Explain use case (multi-tenant Hyku deployments need per-tenant GA property IDs)
  ✓ Show code example of proposed change (optional parameter)
  ✓ Link to Hyku issues #2985, #2995, PALNI/PALCI #611
  ✓ Explain that Hyku has already solved this with overrides
  ✓ Ask for Hyrax maintainer feedback

STEP 5 — CLOSE TASK

  After issue is created:
  ✓ Post issue link to chat
  ✓ Document in task completion summary
  ✓ Move task to completed/

  NOTE: Do NOT wait for Hyrax response. This is a proposal, not a blocker.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

EXPECTED OUTCOME:
  ✓ Issue created in samvera/hyrax
  ✓ Hyrax maintainers can decide if they want to accept this feature
  ✓ If yes: reduces override complexity in Hyku (optional)
  ✓ If no: Hyku keeps override (perfectly fine)

NO RUSHING. This is a discussion, not urgent.
```

---

# TASK: Create Hyrax Feature Proposal — Optional property_id Parameter (Phase 2)

**Status**: BACKLOG (Ready after Phase 1 completion)
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-07-22
**Phase**: 2 of 2 (Discussion/proposal with Hyrax maintainers)
**GitHub Issues**: [samvera/hyku#2985](https://github.com/samvera/hyku/issues/2985) | [samvera/hyku#2995](https://github.com/samvera/hyku/issues/2995) | [PALNI/PALCI#611](https://github.com/notch8/palni_palci_knapsack/issues/611)
**Depends On**: Phase 1 task completion
**Type of Task**: Feature proposal/discussion (not implementation)

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

- **Phase 1**: Solves urgent prob**permanent overrides in Hyku** to fix multi-tenant analytics. This is the correct solution because multi-tenancy is a Hyku feature, not a Hyrax feature.

**Phase 2 Purpose**: Create a **discussion/proposal issue** in samvera/hyrax asking if Hyrax would benefit from an optional `property_id` parameter for flexibility.

**Important Note**: This is **NOT** a blocker or urgent. Hyku's override solution works perfectly fine as-is. This is just asking: "Would Hyrax maintainers find an optional parameter useful?" If they say no, we keep the Hyku override (which is correct anyway).

**Timeline**: No rush — this is a discussion for the Samvera community.
Create GitHub Issue in samvera/hyrax

**Go to**: https://github.com/samvera/hyrax/issues/new

**Title**: `Feature Request: Optional property_id parameter for Ga4 analytics service`

**Issue Body** (use template below):Modification

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
```markdown
## Context: Multi-Tenant Hyku GA4 Analytics

We're working on a Hyku feature to support multi-tenant deployments with per-tenant Google Analytics properties. Currently, Hyku instances that support multiple tenants have a problem: all tenants share the same GA4 property ID (from ENV), leading to cross-tenant data commingling.

**Related Issues**:
- samvera/hyku#2985 — Multi-tenant analytics show identical data
- samvera/hyku#2995 — Phantom collection entries in analytics
- notch8/palni_palci_knapsack#611 — Production case: 248+ phantom entries, 46 cross-tenant ID overlap

## Proposed Solution

We'd like to propose adding an optional `property_id` parameter to `Hyrax::Analytics::Ga4.initialize()` for flexibility. This would allow multi-tenant deployments (like Hyku instances) to pass tenant-specific GA property IDs.

**Proposed Code Change**:

```ruby
# Before (single shared property_id from ENV):
def initialize(property_name: nil)
  @property_name = property_name
end

# After (optional property_id parameter):
def initialize(property_id: nil, property_name: nil)
  @custom_property_id = property_id
  @property_name = property_name
end

def property
  property_id = @custom_property_id || config.property_id
  "properties/#{property_id}"
end
```

**Backward Compatible**: ✅ Yes — existing code continues to work, new parameter is optional

## Why This Matters

- Multi-tenant Hyku instances need per-tenant GA isolation
- Current workaround: Hyku creates monkey-patch overrides (works, but not ideal)
- Proposed change: Make Ga4 more flexible, so overrides are simpler/cleaner

## What Hyku Is Doing

Hyku has already implemented a working solution using Hyku-specific overrides in the GA service. This proposal is just asking: "Would Hyrax benefit from this optional parameter for future flexibility?"

## No Urgency

This is a discussion, not a blocker. Hyku's override-based solution works perfectly. We're just exploring if this would be useful for Hyrax or other multi-tenant use cases.

## Questions for Maintainers

1. Would this be a welcome addition to Hyrax?
2. Are there other use cases where optional property_id flexibility would help?
3. If not, no problem — Hyku will keep its override solution (which is correct for multi-tenancy).

Thanks for considering!
```

---

## After Issue Is Created

1. **Post issue link** to chat with task summary
2. **Do NOT wait** for Hyrax response (this is background discussion)
3. **Move task to completed/** (proposal is done, waiting for community feedback is ongoing)
4. **Hyku keeps override** as permanent solution regardless of Hyrax response

---

## Possible Outcomes

### If Hyrax Maintainers Accept
- ✅ Hyrax adds optional `property_id` parameter in future version
- ✅ Hyku can simplify override (no longer needed)
- ✅ Community gets more flexible GA4 support

### If Hyrax Maintainers Say No
- ✅ Hyku keeps override (perfectly valid for multi-tenant layer)
- ✅ Override is clearly documented as "Hyku multi-tenant extension"
- ✅ No changes needed, system already works

### Either Way
- ✅ Hyku's multi-tenant GA fix is working and stable
- ✅ Contribution opportunity explored with Samvera
- ✅ No time wasted on unnecessary coordination