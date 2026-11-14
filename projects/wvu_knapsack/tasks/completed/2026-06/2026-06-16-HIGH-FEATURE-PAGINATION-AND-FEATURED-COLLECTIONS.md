---
status: completed
priority: HIGH
type: feature
system_domain: CONTROLLERS
local_worker_safe: true
---

# TASK: Add Catalog Pagination Settings and Increase Featured Collections Limit

**Status**: COMPLETED
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-16
**Last Updated**: 2026-06-16
**Completed By**: GitHub Copilot

---

## Summary
Successfully implemented two catalog configuration features using the Knapsack decorator pattern:
1. Added pagination options to catalog search interface
2. Increased featured collections limit from 6 to 15

Both features deployed using Rails engine decorator pattern to avoid modifying hyrax-webapp engine files directly.

---

## Problem Statement
1. **Pagination**: Catalog search needed configurable page sizes: 6, 12, 24, 48, 96 items per page with default of 12
2. **Featured Collections**: Admin interface limited featured collections to 6; requirement is to increase to 15

---

## Solution Implemented

### File 1: Pagination Configuration
**File**: `app/controllers/catalog_controller_decorator.rb`
**Method**: Blacklight configuration override in decorator module

```ruby
module CatalogControllerDecorator
  CatalogController.configure_blacklight do |config|
    config.advanced_search[:form_facet_partial] = "advanced_search_facets"
    # adjust pagination
    config.per_page = [6, 12, 24, 48, 96]
    config.default_per_page = 12
  end
end
```

**How it works**: Knapsack's engine.rb auto-loads all `*_decorator*.rb` files from app/ and lib/ directories. This decorator is loaded after the engine's CatalogController is initialized, allowing configuration override.

### File 2: Featured Collections Limit
**File**: `config/initializers/featured_collection_decorator.rb` (NEW)
**Method**: Class method override with Rails.application.config.to_prepare

```ruby
Rails.application.config.to_prepare do
  module FeaturedCollectionDecorator
    extend ActiveSupport::Concern
    class_methods do
      def feature_limit
        15
      end
    end
  end
  FeaturedCollection.prepend(FeaturedCollectionDecorator)
end
```

**Why config.to_prepare**: Ensures FeaturedCollection model is loaded before the decorator prepends the module. Without this, NameError: uninitialized constant FeaturedCollection occurs.

**Verification**: Application boots successfully with both configurations active and loaded correctly via engine initialization.

---

## Technical Context

**Architecture**: Knapsack is a Rails engine layered on Hyrax/Hyku. Engine code in `hyrax-webapp/` must NOT be modified directly. Instead, decorators in the knapsack app folder override engine behavior.

**Engine Loading Order**:
1. hyrax-webapp engine initializes
2. Knapsack engine's `lib/hyku_knapsack/engine.rb` auto-loads all `app/**/*_decorator*.rb` and `lib/**/*_decorator*.rb`
3. config/initializers/ are auto-loaded by Rails
4. Both decorator mechanisms work in concert

**Related Code** (engine patterns):
- `hyrax-webapp/app/controllers/catalog_controller.rb` (source, line ~35 for Blacklight config)
- `hyrax-webapp/app/models/featured_collection.rb` (line 11 defines `class_attribute :feature_limit, default: 6`)
- `hyrax-webapp/app/models/featured_work_decorator.rb` (reference example of similar feature_limit override)

---

## Files Modified
- ✅ `app/controllers/catalog_controller_decorator.rb` — Modified (pagination added)
- ✅ `config/initializers/featured_collection_decorator.rb` — Created (featured collections override)

---

## Testing & Validation
1. ✅ Application starts successfully with both decorators loaded
2. ✅ Pagination dropdown shows [6, 12, 24, 48, 96] with default of 12 on search results page
3. ✅ Featured collections admin interface now accepts up to 15 items (verified via FeaturedCollection.feature_limit method returning 15)
4. ✅ Both decorator files follow existing Knapsack patterns (see featured_work_decorator.rb precedent)

---

## Deployment Notes
- No database migrations required
- No environment variable changes needed
- Configuration is Rails-level only
- Changes active on application boot

---

## Known Issues / Context
- **Clover Viewer Feature Flag**: Test branch work includes clover_viewer feature not yet in main. Expected error: "Feature 'clover_viewer' unknown" in hyrax-webapp views — not a blocker for this task. Will be manually added to main after test branch validation complete.
- **Decorator Loading**: Must follow naming convention `*_decorator*.rb` for automatic loading via engine.rb
- **Initializer Placement**: FeaturedCollection override placed in config/initializers/ instead of app/models/ to ensure Rails bootup order (initializers run after models but before prepend needs the model loaded)

---

## Dependencies & Integration
- Blacklight (catalog search framework)
- Hyrax FeaturedCollection model
- Rails.application.config.to_prepare hook
- Knapsack engine decorator auto-loading mechanism

---

## Completion Checklist
- ✅ Code implemented
- ✅ Decorators follow Knapsack patterns
- ✅ No hyrax-webapp engine files modified
- ✅ Application verified to boot with changes
- ✅ Configuration verified active
- ✅ Documentation complete
- ⏳ Code committed to git (pending user approval)
- ⏳ Merged to main branch (pending test branch validation)
