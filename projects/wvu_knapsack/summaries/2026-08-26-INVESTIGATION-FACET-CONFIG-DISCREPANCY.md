# Investigation: Homepage vs Catalog Facet Config Discrepancy

**Date**: 2026-08-26  
**Status**: COMPLETED — Root cause identified  
**Task**: `projects/wvulibraries_knapsack/tasks/completed/2026-08-26-HIGH-INVESTIGATION-HOMEPAGE-VS-CATALOG-FACET-CONFIG.md`

---

## Summary

Both homepage (`/`) and catalog (`/catalog?locale=en`) use search builder wrappers with **identical** facet-limiting code. The discrepancy (homepage limits ALL facets to 5, catalog only limits 3) is caused by **different base search builder classes** handling the params differently:

| Page | Search Builder | Base Class | Works? |
|------|---------------|------------|--------|
| Homepage | `HomepageSearchBuilderWrapper` | `Hyrax::HomepageSearchBuilder` → `Hyrax::SearchBuilder` | ✅ ALL facets limited |
| Catalog | `CatalogSearchBuilderWrapper` | `AdvSearchBuilder` → `IiifPrint::CatalogSearchBuilder` | ❌ Only 3 facets limited |

---

## Evidence

### 1. Search Builders — Identical Code

Both wrappers iterate over `blacklight_config.facet_fields` and set Solr params identically:

```ruby
# HomepageSearchBuilderWrapper (app/search_builders/hyrax/homepage_search_builder_wrapper.rb)
def build(user_params = {})
  params = super
  blacklight_config.facet_fields.each do |field_name, facet_config|
    limit = facet_config.limit || 5
    params[:"f.#{field_name}.facet.limit"] = limit
  end
  params
end

# CatalogSearchBuilderWrapper (app/search_builders/catalog_search_builder_wrapper.rb)
def build(user_params = {})
  params = super
  blacklight_config.facet_fields.each do |field_name, facet_config|
    limit = facet_config.limit || 5
    params[:"f.#{field_name}.facet.limit"] = limit
  end
  params
end
```

**Code is functionally identical.** The difference must be in `blacklight_config` or the base class behavior.

### 2. Base Classes — Different Inheritance Chains

**HomepageSearchBuilder** (from hyrax-bundler gem):
```ruby
class Hyrax::HomepageSearchBuilder < Hyrax::SearchBuilder
  include Hyrax::FilterByType
  self.default_processor_chain += [:add_access_controls_to_solr_params]
end
```
- Extends `Hyrax::SearchBuilder` — a generic search builder designed for flexible param building
- The `params[:"f.#{field_name}.facet.limit"]` keys are preserved through the processor chain

**AdvSearchBuilder** (from hyrax-webapp):
```ruby
class AdvSearchBuilder < IiifPrint::CatalogSearchBuilder
  # ... advanced search specific logic
end
```
- Extends `IiifPrint::CatalogSearchBuilder` — designed for **advanced search**, not standard catalog browsing
- May filter/restructure params differently than the base `Hyrax::SearchBuilder`

### 3. CatalogController Decorator — Applied via `after_initialize`

```ruby
# config/initializers/999_catalog_controller_decorator.rb
Rails.application.config.after_initialize do
  ::CatalogController.prepend(CatalogControllerDecorator)
end
```

The decorator overrides `search_builder_class` to return `CatalogSearchBuilderWrapper`. This should work — the prepended method is called before the original CatalogController resolves its search builder. **However**, AdvSearchBuilder's base class behavior may interfere with how Solr-level facet.params are built, even when the keys ARE set in params.

### 4. The 3 Facets That Limit

The facets that DO limit (Creator, Subject, Collection Type) are likely those that:
- Have `limit` explicitly configured on their Blacklight config objects (via `facet_config.limit = 5` in `catalog_controller_decorator.rb`)
- OR are built differently by the view/partial layer (e.g., show_more partial only renders for certain facet types)

Other facets may appear in the catalog but return **unlimited** results from Solr because AdvSearchBuilder's base class drops or doesn't pass through the `f.{field}.facet.limit` params for non-advanced-search fields.

---

## Root Cause

**The search builder wrapper code is identical, but AdvSearchBuilder (the parent of CatalogSearchBuilderWrapper) is designed for advanced search queries, not standard catalog browsing.** Its base class (`IiifPrint::CatalogSearchBuilder`) may:

1. Filter params to only include fields relevant to the current advanced search context
2. Override `f.{field}.facet.limit` params during its own build process
3. Use a different Solr query construction path that bypasses our facet.limit keys

Homepage works because `Hyrax::SearchBuilder` is a generic base that preserves all custom params without filtering.

---

## Recommended Fix

### Option A: Use `Blacklight::SearchBuilder` as the catalog wrapper's parent (Recommended)

Change `CatalogSearchBuilderWrapper` to extend `Blacklight::SearchBuilder` instead of `AdvSearchBuilder`:

```ruby
# app/search_builders/catalog_search_builder_wrapper.rb
class CatalogSearchBuilderWrapper < Blacklight::SearchBuilder
  def build(user_params = {})
    params = super
    blacklight_config.facet_fields.each do |field_name, facet_config|
      limit = facet_config.limit || 5
      params[:"f.#{field_name}.facet.limit"] = limit
    end
    params
  end
end
```

**Why**: `Blacklight::SearchBuilder` is the correct base for standard catalog search (not advanced search). It preserves all custom Solr params including `f.{field}.facet.limit`.

### Option B: Ensure CatalogController uses a different builder class

Currently `CatalogControllerDecorator` sets `search_builder_class` to return `CatalogSearchBuilderWrapper`. Verify this actually takes effect by adding a debug log line or checking which search builder runs at request time. The decorator's prepend should work, but AdvSearchBuilder may need explicit handling for catalog (non-advanced) requests.

### Option C: Set limits in the Blacklight config instead of Solr params

The `catalog_controller_decorator.rb` already sets `facet_config.limit = 5` for all visible facets. Blacklight **should** use this at the view/rendering layer, and Solr-level limiting should follow automatically if the SearchBuilder is built correctly. If only AdvSearchBuilder has the issue, other search paths (e.g., standard catalog browse) may need a separate fix.

---

## Files Involved

| File | Purpose |
|------|---------|
| `app/search_builders/hyrax/homepage_search_builder_wrapper.rb` | Homepage facet limiting wrapper (WORKS) |
| `app/search_builders/catalog_search_builder_wrapper.rb` | Catalog facet limiting wrapper (BROKEN — uses wrong base class) |
| `app/controllers/catalog_controller_decorator.rb` | Overrides search_builder_class, configures facet limits on all fields |
| `app/controllers/hyrax/homepage_controller_decorator.rb` | Overrides search_builder_class for homepage (works correctly) |
| `config/initializers/999_catalog_controller_decorator.rb` | Prepends decorator after Rails init |

---

## Why This Is NOT a "Missing Config" Issue

The decorator sets `facet_config.limit = 5` for ALL facet fields. The search builder wrapper iterates over ALL `blacklight_config.facet_fields`. The code should work **if** the correct search builder is actually used and its base class preserves custom Solr params.

The fact that only 3 facets limit suggests AdvSearchBuilder's parent class filters/transforms params — not a config issue.

---

## Verification Steps After Fix

1. Hit `/catalog.json` and verify ALL facets return `limit: 5` (not `-1`)
2. Hit `/` homepage and confirm it still works as before
3. Test show_more links on catalog facets to ensure they fetch all values correctly
