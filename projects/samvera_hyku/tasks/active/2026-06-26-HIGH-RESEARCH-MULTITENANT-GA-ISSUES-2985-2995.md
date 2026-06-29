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

### Fix #2: Remove Phantom Collection Entries (Fixes #2995)

**File**: `samvera/hyrax/app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`

```ruby
def export_data
  csv_row = CSV.generate do |csv|
    csv << ["Name", "ID", "View of Works In Collection", "Downloads of Works In Collection", "Collection Page Views"]
    @all_top_collections.each do |collection|
      document = begin
                   ::SolrDocument.find(collection[0])
                 rescue
                   next  # Skip instead of inserting "Collection deleted"
                 end
      csv << [document['title_tesim']&.first, collection[0], view_count, download_count, page_view_count]
    end
  end
end
```

**Note**: Fix #1 should prevent most phantom entries (GA won't return other tenants' collection IDs). Fix #2 handles edge cases where a collection is truly deleted.

---

## Implementation Plan

### Phase 1 — Core GA Property Scoping (HIGH PRIORITY)
- [ ] Modify `Hyrax::Analytics::Ga4` to accept optional `property_id` parameter
- [ ] Update `CollectionReportsController` to pass tenant property_id
- [ ] Update `WorkReportsController` to pass tenant property_id
- [ ] Update `AnalyticsController` base class if needed
- [ ] Test: Verify different tenants get different analytics

### Phase 2 — Data Quality Cleanup (HIGH PRIORITY)
- [ ] Modify collection report export to skip missing collections
- [ ] Test: Verify no phantom entries in collection reports

### Phase 3 — Robustness (MEDIUM PRIORITY)
- [ ] Add explicit account/tenant scoping to `Hyrax::Analytics::Ga4::Base` query class
- [ ] Create DRY `initialize_analytics` helper in base controller
- [ ] Add comprehensive RSpec tests for multi-tenant property isolation
- [ ] Document the pattern in Hyku/Hyrax contributor guide

### Phase 4 — Long-Term Improvements (POST-RELEASE)
- [ ] Track collection deletion events in GA4 with collection metadata
- [ ] Query GA directly for collection titles instead of Solr resolution

---

## Files to Modify

**Hyrax Core**:
- `app/services/hyrax/analytics/ga4.rb` — Add property_id parameter
- `app/services/hyrax/analytics/ga4/base.rb` — Optional: Add account scoping helper
- `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb` — Pass tenant property_id, skip missing collections
- `app/controllers/hyrax/admin/analytics/work_reports_controller.rb` — Pass tenant property_id
- `app/controllers/hyrax/admin/analytics/analytics_controller.rb` — Optional: Add initialize_analytics helper

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
- [ ] No "Collection deleted" entries appear in either tenant
- [ ] Analytics email (if implemented) shows tenant-specific data

---

## Acceptance Criteria

✓ **Issue #2985**: Multi-tenant analytics emails show DIFFERENT numbers per tenant  
✓ **Issue #2995**: No phantom "Collection deleted" entries in collection reports  
✓ **Code Quality**: Minimal changes, DRY implementation, backward compatible  
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
- **Estimated Work**: 2-3 hours implementation + 2 hours testing = 4-5 hours total
- **Blocker Status**: Blocks any multi-tenant Hyku 7 release until resolved
