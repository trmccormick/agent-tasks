# Session Handoff — 2026-07-07
## Samvera Hyku GA Multi-Tenant Issues #2985 & #2995 — APPROVED FOR IMPLEMENTATION

**Date**: 2026-07-07  
**Previous Work**: 2026-06-26 to 2026-07-07 (Research, Validation, Testing)  
**Status**: ✅ REVIEWER APPROVED — Ready for Implementation Phase  
**Reviewer**: Rob (Samvera)  
**GitHub Issues**: [#2985](https://github.com/samvera/hyku/issues/2985) | [#2995](https://github.com/samvera/hyku/issues/2995)  
**Related**: [PALNI/PALCI #611](https://github.com/notch8/palni_palci_knapsack/issues/611) (production validation)

---

## 1. What Was Accomplished

### ✅ Root Cause Analysis Complete
- **Issue**: Multi-tenant Hyku deployments show identical analytics across all tenants
- **Phantom "Collection deleted" entries**: 248+ in PALNI/PALCI production (81% of one tenant's report)
- **Root Cause**: All tenants query the **same GA property ID from ENV** instead of tenant-specific IDs
- **Code Location**: `app/services/hyrax/analytics/ga4.rb` — `property` method uses shared `config.property_id`

### ✅ Production Validation
- **PALNI/PALCI Issue #611**: Real-world case with 248+ phantom entries, 46 cross-tenant ID overlap
- **Cross-tenant proof**: IDs from unrelated tenants appearing in each other's reports
- **Quote from issue**: "This may be an artifact from when GA code was handled at the ENV level instead of specifically per tenant"

### ✅ WVU Knapsack Testing
- **Test Setup**: Demo (test-only) vs Main (production) tenants
- **Test Action**: Deleted 1 collection from demo (2026-06-29)
- **Result**: Main tenant's "7 deleted collections" count remained unchanged (no increase)
- **Conclusion**: WVU's specific "deleted collections" are likely genuine deletions or Valkyrie migration artifacts, NOT cross-tenant bleed (unlike PALNI/PALCI case)
- **Validation**: Architectural fix still needed and will prevent issues

### ✅ Reviewer Approved Implementation Plan
- **Fix #1 - APPROVED**: Use tenant-specific GA property IDs in GA queries
- **Fix #2 - REJECTED**: Don't skip missing collections (preserves visibility into genuine deletions)
- **Estimated Work**: Phase 1 only (~3-4 hours total)

---

## 2. Implementation Plan (Phase 1 Only)

### Files to Modify

**Hyrax Core**:
1. `app/services/hyrax/analytics/ga4.rb`
   - Add `property_id` parameter to `initialize` method
   - Modify `property` method to use custom property_id or fall back to ENV

2. `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`
   - In `index` method: Get tenant property_id from `current_account.google_analytics_property_id`
   - Pass to Ga4 client: `@analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)`

3. `app/controllers/hyrax/admin/analytics/work_reports_controller.rb`
   - Same as collection_reports_controller

4. `app/controllers/hyrax/admin/analytics/analytics_controller.rb` (optional)
   - Create DRY helper method `initialize_analytics` to avoid code duplication

### Testing Strategy

**Multi-Tenant Test Instance**:
1. Setup 2+ tenants (e.g., "Tenant A", "Tenant B")
2. Each tenant creates own GA property with distinct Measurement ID (in Hyku account settings)
3. Generate distinct analytics for each tenant
4. Verify: Each tenant's report shows only their own collections/analytics
5. Verify: Numbers differ between tenants (not identical across all tenants)

**RSpec Tests to Add**:
- `spec/services/hyrax/analytics/ga4_spec.rb` — Test property_id parameter acceptance
- `spec/controllers/hyrax/admin/analytics/collection_reports_controller_spec.rb` — Test tenant isolation
- `spec/controllers/hyrax/admin/analytics/work_reports_controller_spec.rb` — Test tenant isolation

---

## 3. Key Implementation Details

### Code Change: Ga4.rb

```ruby
# app/services/hyrax/analytics/ga4.rb
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

### Code Change: Collection Reports Controller

```ruby
# app/controllers/hyrax/admin/analytics/collection_reports_controller.rb
def index
  tenant_property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
  @analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)
  @all_top_collections = @analytics.top_collections_for_deposit
  # ... rest of method
end
```

### Important Notes

- **NO CHANGES** to collection export "Collection deleted" behavior (Fix #2 rejected)
- **After Fix #1**: Phantom entries will disappear because GA won't return other tenants' collection IDs
- **Genuine deletions**: Still show as "Collection deleted" (preserves historical analytics for deleted collections)
- **Backward compatible**: Single-tenant deployments unaffected (ENV fallback still works)

---

## 4. Acceptance Criteria

✓ **Issue #2985**: Multi-tenant analytics emails show DIFFERENT numbers per tenant  
✓ **Issue #2995 (Partial)**: Phantom entries from cross-tenant commingling eliminated  
✓ **Code Quality**: Minimal changes (~40-50 lines), backward compatible  
✓ **Tests**: RSpec tests verify tenant isolation  
✓ **Documentation**: Code comments explain tenant property_id scoping

---

## 5. Next Steps for Implementation Session

1. **Create Feature Branch**: `fix/ga-tenant-property-scoping` from main
2. **Implement Fix #1** in Hyrax:
   - Modify 4 files (ga4.rb, 2 controllers, optional base controller)
   - Add inline comments explaining tenant scoping
3. **Write RSpec Tests**: 3 test files with multi-tenant scenarios
4. **Test on GA-Enabled Instance**:
   - Setup 2 test tenants with distinct GA properties
   - Verify tenant isolation
5. **Create PR**: Link to issues #2985, #2995, and reference PALNI/PALCI #611
6. **Code Review**: Await Samvera maintainer approval

---

## 6. Context & References

### Documentation Files
- **Main Task**: `projects/samvera_hyku/tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md`
- **Validation**: `projects/samvera_hyku/tasks/active/2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md`
- **GitHub Issues**: #2985, #2995, PALNI/PALCI #611
- **Reviewer Feedback**: Rob approved Fix #1 (2026-07-07)

### Testing Data
- **WVU Knapsack Test**: Demo deletion experiment (2026-06-29 to 2026-06-30)
  - Result: No cross-tenant event bleed in this instance
  - Conclusion: Multiple root causes can create "deleted collections", but architectural fix applies to all

### Hyrax Code Locations
- Main GA4 service: `samvera/hyrax/app/services/hyrax/analytics/ga4.rb`
- Collection reports controller: `samvera/hyrax/app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`
- Work reports controller: `samvera/hyrax/app/controllers/hyrax/admin/analytics/work_reports_controller.rb`

---

## 7. Handoff Summary

**Ready for**: Developer or agent to pick up implementation  
**Duration**: ~3-4 hours (Phase 1 implementation + testing)  
**Complexity**: LOW — Minimal code changes, well-understood root cause  
**Blocker**: None — All approvals obtained  
**Urgency**: HIGH — Affects production Hyku Commons deployments  

**To Start**: Check out the main task file and follow the implementation plan above.
