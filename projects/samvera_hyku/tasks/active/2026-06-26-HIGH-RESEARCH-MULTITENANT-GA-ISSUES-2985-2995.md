# Task: Research & Propose Fix for Multi-Tenant GA Issues #2985 & #2995

**Status**: RESEARCH COMPLETE — READY FOR SYNTHESIS REVIEW  
**Priority**: HIGH  
**Assignee**: Product Owner / Working Group  
**Date Created**: 2026-06-26  
**Reporter**: WVU Knapsack (external contributor research)  
**Related Issues**: 
- [#2985 — Analytics summary emails include cross-tenant Google Analytics graph data](https://github.com/samvera/hyku/issues/2985)
- [#2995 — Analytics Collections Report shows phantom 'Collection deleted' entries](https://github.com/samvera/hyku/issues/2995)

---

## Problem Summary

In multi-tenant Hyku deployments, analytics reporting is broken:

**Issue #2985**: Repository managers receive analytics summary emails showing **identical numbers across all tenants**
- Example: Demo instance shows "2 File Downloads, 14 Work Views" for every tenant
- Same behavior in production instance (different numbers, but still identical across tenants)
- Affects: Analytics summary emails, graphs, work/collection analytics dashboards

**Issue #2995**: Collection analytics reports display **phantom "Collection deleted" entries**
- Entries appear even in repositories where no collections were deleted
- Same phantom entries repeated across multiple tenants
- Suggests related underlying cause to #2985

---

## Root Cause Analysis

Both issues stem from a **single architectural problem**: Report generation uses a **shared GA property ID** for all tenants, ignoring tenant-specific property IDs stored in `AccountSettings`.

### Technical Details

**Issue #2985 — Identical Analytics**

File: `samvera/hyrax/app/services/hyrax/analytics/ga4.rb`

```ruby
def property
  @property ||= "properties/#{config.property_id}"  # Always uses ENV, never tenant-specific
end
```

Controllers instantiate GA with no parameters:
```ruby
@analytics = Hyrax::Analytics::Ga4.new  # Uses shared ENV property_id for ALL tenants
```

**Why it breaks**:
- Each tenant should have their own GA property (per Hyku documentation)
- `AccountSettings` stores `google_analytics_property_id` per-tenant ✓
- But controllers NEVER PASS it to GA queries ✗
- Result: All tenants query the shared `GOOGLE_ANALYTICS_PROPERTY_ID` ENV var ✗

### Issue #2995 — Phantom "Collection deleted" Entries

File: `samvera/hyrax/app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`

```ruby
def export_data
  csv_row = CSV.generate do |csv|
    @all_top_collections.each do |collection|
      document = begin
                   ::SolrDocument.find(collection[0])
                 rescue
                   "Collection deleted"  # <-- PHANTOM ENTRY INSERTED HERE
                 end
end
```

**Why it breaks**:
- When GA returns a collection ID that doesn't exist in Solr, rescue catches error
- Inserts the string `"Collection deleted"` as a phantom entry
- In multi-tenant with shared GA property: collection IDs from OTHER TENANTS fail lookup
- Result: Phantom entries appear in current tenant's report

---

## Proposed Solution

Both issues fixed by ensuring **tenant-specific GA property IDs are used in all queries**.

### Fix #1: Use Tenant Property IDs in GA Queries (Fixes #2985)

**File**: `samvera/hyrax/app/services/hyrax/analytics/ga4.rb`

```ruby
class Ga4
  def initialize(property_id: nil)
    @custom_property_id = property_id
  end

  def property
    property_id = @custom_property_id || config.property_id
    "properties/#{property_id}"
  end
end
```

**File**: `samvera/hyrax/app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`

```ruby
def index
  tenant_property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
  @analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)
  @all_top_collections = @analytics.top_collections_for_deposit
  # ... rest of method
end
```

Do the same in:
- `samvera/hyrax/app/controllers/hyrax/admin/analytics/work_reports_controller.rb`
- Any other controllers that instantiate `Hyrax::Analytics::Ga4`

### Fix #2: NOT RECOMMENDED (REJECTED BY REVIEWER)

**Original proposal**: Skip missing collections instead of inserting "Collection deleted"

**Reviewer feedback** (Rob, 2026-07-07): "Fix 2 will fail if a connection actually is deleted, a thing that can happen. So I wouldn't do that one."

**Rationale**: Skipping missing collections breaks visibility into genuinely deleted collections that still have historical GA data. Once Fix #1 is applied, remaining "Collection deleted" entries will be legitimate deletions, not cross-tenant phantom entries.

**Decision**: Keep the current "Collection deleted" rescue clause as-is. After Fix #1 (using tenant-specific GA properties), phantom entries will be eliminated, and remaining "Collection deleted" entries will represent actual deletions to preserve in analytics records.

---

## Implementation Plan

### Phase 1 — Core GA Property Scoping (HIGH PRIORITY - APPROVED)
- [ ] Modify `Hyrax::Analytics::Ga4` to accept optional `property_id` parameter
- [ ] Update `CollectionReportsController` to pass tenant property_id
- [ ] Update `WorkReportsController` to pass tenant property_id
- [ ] Update `AnalyticsController` base class if needed
- [ ] Test: Verify different tenants get different analytics

**Note**: Once Phase 1 is complete, phantom "Collection deleted" entries will be eliminated because GA will only return collection IDs from the correct tenant's GA property. No Phase 2 modifications needed (keeping the "Collection deleted" behavior as-is allows visibility into genuinely deleted collections).

### Phase 2 — Robustness (MEDIUM PRIORITY)
- [ ] Add explicit account/tenant scoping to `Hyrax::Analytics::Ga4::Base` query class
- [ ] Create DRY `initialize_analytics` helper in base controller
- [ ] Add comprehensive RSpec tests for multi-tenant property isolation
- [ ] Document the pattern in Hyku/Hyrax contributor guide

### Phase 3 — Long-Term Improvements (POST-RELEASE)
- [ ] Track collection deletion events in GA4 with collection metadata
- [ ] Query GA directly for collection titles instead of Solr resolution

---

## Files to Modify

**Hyrax Core** (Phase 1):
- `app/services/hyrax/analytics/ga4.rb` — Add property_id parameter
- `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb` — Pass tenant property_id to Ga4
- `app/controllers/hyrax/admin/analytics/work_reports_controller.rb` — Pass tenant property_id to Ga4
- `app/controllers/hyrax/admin/analytics/analytics_controller.rb` — Optional: Add initialize_analytics helper

**Hyrax Core** (Phase 2):
- `app/services/hyrax/analytics/ga4/base.rb` — Optional: Add account scoping helper

**Testing**:
- `spec/services/hyrax/analytics/ga4_spec.rb` — Test property_id parameter
- `spec/controllers/hyrax/admin/analytics/collection_reports_controller_spec.rb` — Test tenant isolation
- `spec/controllers/hyrax/admin/analytics/work_reports_controller_spec.rb` — Test tenant isolation

---

## Testing Strategy

**Multi-Tenant Test Instance Setup**:
1. Create 2+ tenants (e.g., "Demo Tenant A", "Demo Tenant B")
2. Each tenant creates its own GA property with distinct Measurement ID
3. Generate distinct analytics for each tenant (different collections, works, file downloads)
4. Wait for GA data to populate (24 hours or use dev environment with backdated events)

**Test Cases**:
- [ ] Tenant A's collection report shows only Tenant A's collections/analytics
- [ ] Tenant B's collection report shows only Tenant B's collections/analytics
- [ ] Numbers differ between tenants (not identical)
- [ ] No cross-tenant phantom "Collection deleted" entries appear (genuine deletions may remain)
- [ ] Analytics email (if implemented) shows tenant-specific data

---

## Acceptance Criteria

✓ **Issue #2985**: Multi-tenant analytics emails show DIFFERENT numbers per tenant  
✓ **Issue #2995 (Partial)**: Phantom entries from cross-tenant data commingling eliminated (genuine "Collection deleted" entries from actual deletions are preserved)  
✓ **Code Quality**: Minimal changes (~40-50 lines), DRY implementation, backward compatible  
✓ **Tests**: Multi-tenant test instance verified, RSpec tests added  
✓ **Documentation**: Code comments explain tenant property_id scoping pattern  

---

## Context & References

- **Samvera Hyku GA4 Integration Documentation**: https://samvera.atlassian.net/wiki/spaces/hyku/pages/3185147970/Google+Analytics+4+GA4+Integration
- **GitHub Issue #2985**: https://github.com/samvera/hyku/issues/2985
- **GitHub Issue #2995**: https://github.com/samvera/hyku/issues/2995
- **Research Date**: 2026-06-26
- **Research Location**: WVU Knapsack (external contributor request for Samvera working group)

---

## Notes

- **Backward Compatibility**: Changes should not break single-tenant deployments or existing GA integrations
- **Severity**: Critical for multi-tenant Hyku deployments; affects data integrity and tenant isolation
- **Estimated Work**: 1-2 hours implementation (Phase 1 only) + 2 hours testing = 3-4 hours total
- **Blocker Status**: Blocks any multi-tenant Hyku 7 release until resolved
- **Reviewer Feedback** (Rob, 2026-07-07): Fix #1 approved; Fix #2 rejected to preserve visibility into genuinely deleted collections
