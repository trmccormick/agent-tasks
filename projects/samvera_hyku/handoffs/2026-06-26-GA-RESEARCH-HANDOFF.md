# Handoff: GA Issues #2985 & #2995 Research Complete

**Date**: 2026-06-26  
**Assigned To**: Samvera Product Owner / Working Group  
**Status**: Research Complete — Ready for Implementation Planning

---

## Executive Summary

Multi-tenant Hyku installations show **two critical analytics bugs** affecting data integrity and tenant isolation:

1. **#2985**: Analytics emails show identical numbers across all tenants
2. **#2995**: Collection reports contain phantom "Collection deleted" entries

**Root Cause**: Single architectural issue — all tenants query the same GA property instead of tenant-specific properties.

**Fix Complexity**: LOW — Requires changes to 4-5 files, ~50 lines of code modifications total.

**Estimated Work**: 4-5 hours (implementation + testing)

---

## What's Documented

**Main Task File**: `projects/samvera_hyku/tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md`

**Validation Document**: `projects/samvera_hyku/tasks/active/2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md`

Main documents contain:
- Detailed root cause analysis with exact file paths and code snippets
- Line-by-line fixes for both issues
- Implementation plan (4 phases)
- Testing strategy
- Acceptance criteria
- **PRODUCTION VALIDATION**: Real-world case showing 248+ phantom entries at scale

---

## Key Findings

### Why It's Broken

**File**: `samvera/hyrax/app/services/hyrax/analytics/ga4.rb`

```ruby
def property
  @property ||= "properties/#{config.property_id}"  # Always uses ENV, ignores tenant-specific ID
end
```

**Controllers**:
```ruby
@analytics = Hyrax::Analytics::Ga4.new  # No property_id parameter passed
```

**Result**: All tenants query `GOOGLE_ANALYTICS_PROPERTY_ID` from ENV, not their own GA properties.

### Why "Collection deleted" Appears


### PRODUCTION VALIDATION: PALNI/PALCI Issue #611

Real-world Hyku Commons instance showing the exact same bugs at scale:

**Tenant A (AU Archives)**:
- Actual collections: 68
- Report shows: 290 entries (248 phantom "Collection deleted")

**Tenant B (Alpa)**:
- Actual collections: 82  
- Report shows: 70 entries (ALL phantom "Collection deleted", 0 real collections)

**Cross-tenant proof**: 46 collection IDs overlap between two unrelated tenants
- Confirms data commingling across GA properties
- Validates root cause: Shared GA property ID pulling data from multiple tenants
- **Impact**: GA4 error quota exhaustion, analytics dashboard goes down every 15 minutes

**URGENT**: This is a production-breaking bug affecting existing Hyku Commons deployments.

1. **Review** — Product owner reviews findings, VALIDATION from real-world case, & proposed fixes
2. **Prioritize** — Working group prioritizes this as BLOCKER for multi-tenant Hyku 7 release
3. **Approve** — Working group approves implementation plan
4. **Assign** — Task assigned to developer for implementation
5. **PR** — Create pull request with fixes for Hyku repo
6. **Test** — Multi-tenant test instance verification using PALNI/PALCI test data if available
7. **Merge** — Merge to main branch
8. **Backport** — Consider backport to earlier Hyku versions (issue affecting production today)

## The Fix (TL;DR)

1. **Modify GA4 client** to accept optional `property_id` parameter
2. **Pass tenant property_id** from AccountSettings when creating GA queries
3. **Skip missing collections** instead of inserting phantom entries

See task file for complete code changes.

---

## Next Steps

1. **Review** — Product owner reviews findings & proposed fixes
2. **Approve** — Working group approves implementation plan  
3. **Assign** — Task assigned to developer for implementation
4. **PR** — Create pull request with fixes for Hyku repo
5. **Test** — Multi-tenant test instance verification
6. **Merge** — Merge to main branch

---

## Contact

For questions on this research: See WVU Knapsack project notes or refer to the full task file.

---

## Files Modified

- `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md` (NEW)
- `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/status.md` (UPDATED)
