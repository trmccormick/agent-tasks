# CORRECTED SYNTHESIS — Phase 1 GA Multi-Tenant Override (Actual Architecture)

**Date**: 2026-07-22  
**Agent**: Planning Agent  
**Status**: ✅ CORRECTED & READY FOR IMPLEMENTATION  
**Critical Finding**: Ga4 uses class methods, not instance methods. Implementation approach must change.

---

## Executive Summary

**Discovery**: After examining the actual Hyrax Ga4 implementation, we found:
- Ga4 is a **module with class methods**, not a class with instance methods
- Controllers call `Hyrax::Analytics.daily_events()` (class methods)
- `property_id` is accessed via `Hyrax::Analytics.config.property_id` 
- No `.new()` instantiation happens

**Previous Assumption**: Task file assumed `Ga4.new()` instantiation with instance method overrides (❌ Wrong)

**Corrected Approach**: Two options, both simpler than originally planned:
1. **Option A (Simpler)**: Override controllers to temporarily set `config.property_id` before calling analytics
2. **Option B (Cleaner)**: Prepend module to override just the `property` method in Ga4

**Recommendation**: Option A (controller override) — less intrusive, easier to test, works immediately.

---

## What We Actually Found in Hyrax

### Ga4 Architecture

**File**: `app/services/hyrax/analytics/ga4.rb`

**Key Pattern**:
```ruby
module Hyrax
  module Analytics
    module Ga4  # <-- A MODULE with class methods
      extend ActiveSupport::Concern
      class_methods do
        def config
          @config ||= Config.load_from_yaml
        end

        def property
          "properties/#{config.property_id}"  # <-- Uses config.property_id
        end

        def client
          @client ||= ::Google::Analytics::Data::V1beta::AnalyticsData::Client.new do |conf|
            conf.credentials = config.account_info
          end
        end

        # All other methods are class methods (daily_events, top_events, etc.)
        def daily_events(action, date = default_date_range)
          # ... uses @property and @client
        end
      end
    end
  end
end
```

### Controller Usage

**File**: `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb`

**Key Pattern**:
```ruby
class CollectionReportsController < AnalyticsController
  def index
    @pageviews = Hyrax::Analytics.daily_events('collection-page-view')
    @work_page_views = Hyrax::Analytics.daily_events('work-in-collection-view')
    # ... all class method calls, NO instantiation
  end
end
```

### Dynamic Module Inclusion

**File**: `app/services/hyrax/analytics.rb`

```ruby
module Hyrax
  module Analytics
    def self.provider_parser
      "Hyrax::Analytics::#{Hyrax.config.analytics_provider.to_s.capitalize}".constantize
    end

    include provider_parser  # <-- Dynamically includes Ga4 module
  end
end
```

**Result**: `Hyrax::Analytics.daily_events()` → includes `Hyrax::Analytics::Ga4` methods

---

## Why Original Task File Was Wrong

The task file assumed:
- ❌ `Hyrax::Analytics::Ga4` is a class that can be instantiated with `.new(property_id: X)`
- ❌ Controllers instantiate Ga4 like `@analytics = Hyrax::Analytics::Ga4.new(...)`
- ❌ Instance methods like `initialize` and `property` can be overridden

**Reality**:
- ✅ Ga4 is a module with class methods
- ✅ Controllers call class methods directly: `Hyrax::Analytics.daily_events(...)`
- ✅ No instantiation needed — we override class methods or mutate config

---

## Corrected Implementation Approach

### OPTION A: Controller Override (RECOMMENDED)

Override analytics controllers in Hyku to set tenant-aware `property_id` before calling analytics methods.

**Advantage**: Simpler, no monkey-patching complexity, direct control, easy to test

**Implementation**:

**File 1**: `app/controllers/hyku/admin/analytics/collection_reports_controller.rb`
```ruby
module Hyku
  module Admin
    module Analytics
      class CollectionReportsController < Hyrax::Admin::Analytics::CollectionReportsController
        prepend Hyku::Analytics::TenantAwareAnalytics
      end
    end
  end
end
```

**File 2**: `lib/hyku/analytics/tenant_aware_analytics.rb`
```ruby
module Hyku
  module Analytics
    module TenantAwareAnalytics
      def index
        # Set tenant-aware property_id before calling parent
        with_tenant_analytics { super }
      end

      def show
        with_tenant_analytics { super }
      end

      private

      def with_tenant_analytics
        tenant_property_id = current_account.google_analytics_property_id || 
                             ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
        
        # Temporarily set config
        original_property_id = Hyrax::Analytics.config.property_id
        Hyrax::Analytics.config.property_id = tenant_property_id
        
        begin
          yield
        ensure
          # Restore original
          Hyrax::Analytics.config.property_id = original_property_id
        end
      end
    end
  end
end
```

**File 3**: Similar override for `work_reports_controller.rb`

**Benefits**:
- ✅ No prepend complexity on Ga4 itself
- ✅ Clear what's happening: set property_id for this request
- ✅ Thread-safe (instance variable, not global)
- ✅ Easy to test (mock the config setter)
- ✅ Works immediately after merge

---

### OPTION B: Module Prepend on Ga4 (ALTERNATIVE)

Prepend a module to override the `property` method to be tenant-aware.

**Implementation**:

**File 1**: `lib/hyku/analytics/ga4_tenant_override.rb`
```ruby
module Hyku
  module Analytics
    module Ga4TenantOverride
      def property
        # If we're in a controller with current_account, use tenant property_id
        if defined?(current_account) && current_account
          tenant_property_id = current_account.google_analytics_property_id || config.property_id
          "properties/#{tenant_property_id}"
        else
          # Fallback to original behavior
          super
        end
      end
    end
  end
end
```

**File 2**: `config/initializers/99_ga4_tenant_patch.rb`
```ruby
Rails.configuration.to_prepare do
  unless Hyrax::Analytics::Ga4.ancestors.include?(Hyku::Analytics::Ga4TenantOverride)
    Hyrax::Analytics::Ga4.prepend(Hyku::Analytics::Ga4TenantOverride)
  end
end
```

**Challenges**:
- ⚠️ `current_account` is not available in Ga4 class context
- ⚠️ Would need to pass tenant_property_id as parameter to property method
- ⚠️ More complex than Option A

---

## Corrected Implementation Plan

### RECOMMENDED: Option A (Controller Override)

**Files to Create**:
1. `lib/hyku/analytics/tenant_aware_analytics.rb` — Module with `with_tenant_analytics` helper
2. `app/controllers/hyku/admin/analytics/collection_reports_controller.rb` — Override collection reports
3. `app/controllers/hyku/admin/analytics/work_reports_controller.rb` — Override work reports
4. `spec/lib/hyku/analytics/tenant_aware_analytics_spec.rb` — Unit tests
5. `spec/controllers/hyku/admin/analytics/collection_reports_controller_spec.rb` — Integration test

**Total**: ~5 files, ~100-120 lines of code + tests

### Testing Strategy

**Unit Tests** (`tenant_aware_analytics_spec.rb`):
- Test that `with_tenant_analytics` sets and restores `config.property_id`
- Test that `current_account.google_analytics_property_id` is used when present
- Test that ENV fallback works when account has no property_id

**Integration Tests** (`collection_reports_controller_spec.rb`):
- Create 2 test accounts with distinct property_ids
- Test that collection report uses correct property_id per tenant
- Verify data isolation (no cross-tenant bleed)

**Manual Testing**:
- Local multi-tenant setup with 2+ tenants
- Each tenant has distinct GA property_id in account settings
- Login to each tenant → Analytics → Collection Reports
- Verify different collection counts per tenant (not identical)

---

## Gotchas & Mitigations (Updated)

| Gotcha | Severity | Mitigation |
|--------|----------|-----------|
| current_account not always defined | Medium | Use safe navigation: `current_account&.google_analytics_property_id` |
| Config is mutable, not thread-safe | Medium | Use ensure block to restore original value |
| Ga4 client is cached (@client ||=) | Low | Works fine—cache uses current property_id |
| Controllers not in Hyku yet | High | ✅ We create the overrides, Hyku doesn't have them |
| Single-tenant deployments | High | ✅ Fallback to ENV if current_account nil |

---

## Approval Checklist

Before Qwen codes, confirm:

- ✅ Ga4 is a module with class methods (not a class)
- ✅ Controllers call class methods directly (no instantiation)
- ✅ Option A (controller override) is the recommended approach
- ✅ Implementation uses `with_tenant_analytics` helper to set/restore config
- ✅ Tests cover tenant isolation and fallback cases
- ✅ ENV fallback ensures backward compatibility

---

## Next Steps for Qwen

1. ✅ Review this corrected synthesis
2. ⏳ Update task file with corrected implementation plan
3. ⏳ Proceed with coding (Option A: controller overrides)
4. ⏳ Write unit + integration tests
5. ⏳ Manual multi-tenant testing with 2+ distinct GA properties
6. ⏳ Commit to feature branch

---

## Files Changed

| File | Type | Purpose |
|------|------|---------|
| lib/hyku/analytics/tenant_aware_analytics.rb | NEW | Module with `with_tenant_analytics` helper |
| app/controllers/hyku/admin/analytics/collection_reports_controller.rb | NEW | Override collection reports controller |
| app/controllers/hyku/admin/analytics/work_reports_controller.rb | NEW | Override work reports controller |
| spec/lib/hyku/analytics/tenant_aware_analytics_spec.rb | NEW | Unit tests for helper |
| spec/controllers/hyku/admin/analytics/collection_reports_controller_spec.rb | NEW | Integration tests |
| config/initializers/99_ga4_tenant_patch.rb | DELETE | Not needed in Option A |

---

## Status

**Synthesis**: ✅ CORRECTED & COMPLETE  
**Ready for**: Task file update + implementation  
**Blocking**: None — Qwen can proceed immediately after task file update  

---

**Prepared by**: Planning Agent  
**Date**: 2026-07-22  
**Branch**: fix/ga-tenant-property-scoping
