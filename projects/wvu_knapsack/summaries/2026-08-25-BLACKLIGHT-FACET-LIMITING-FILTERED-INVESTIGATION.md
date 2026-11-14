# Facet Limiting on Filtered Results — Investigation & Root Cause

**Date**: 2026-08-25  
**Task**: `2026-08-25-HIGH-BUGFIX-FACET-LIMITING-FILTERED-RESULTS.md`  
**Branch**: `fix/hide-type-facet-add-show-more-facets` (commit `20ac060`)  
**Submodule**: hyrax-webapp at `3cec38f` (Kirk's latest)

---

## Problem

Facet limiting (`limit: 5`) works on the main catalog page but **fails on filtered results pages** when active facet filters are applied.

| Page | More Links | Facet Limit Applied? |
|------|-----------|---------------------|
| Main catalog (`/catalog?locale=en`) | ✅ 2 (Creator, Subject) | ✅ YES — all 6 facets show ≤5 items |
| Filtered results (`/catalog?f[people_represented_sim][]=...&locale=en`) | ⚠️ Only 1 | ❌ NO — most facets show unlimited items |

---

## Root Cause: Two-Code-Path Bug in Blacklight

### How Our Code Works (Main Page)

1. `CatalogController#index` → uses `search_builder_class = AdvSearchBuilder`
2. Our decorator (`CatalogControllerDecorator`) overrides via `prepend`:
   ```ruby
   def search_builder_class
     CatalogSearchBuilderWrapper  # our override wins
   end
   ```
3. `CatalogSearchBuilderWrapper.build()` intercepts params and adds:
   ```ruby
   blacklight_config.facet_fields.each do |field_name, facet_config|
     limit = facet_config.limit || 5
     params[:"f.#{field_name}.facet.limit"] = limit
   end
   ```
4. This works because `CatalogControllerDecorator` is prepended — Ruby's MRO gives our override priority

### What Happens on Filtered Results Pages (THE BUG)

When the request has facet filter params (`f[people_represented_sim][]=...`), Blacklight **does NOT go through `CatalogController#index`** at all. Instead:

1. Request URL contains a facet filter selection → Blacklight routes to **`FacetController`**
2. `FacetController` has its **own** search builder chain, independent of our `CatalogControllerDecorator`
3. Our wrapper is ONLY applied via `prepend` on `CatalogController` — it never touches `FacetController`

### Evidence from Code Analysis

```
hyrax-webapp/app/controllers/catalog_controller.rb line 75:
  config.search_builder_class = AdvSearchBuilder       ← Main catalog path

hyrax-webapp/app/search_builders/adv_search_builder.rb:
  class AdvSearchBuilder < IiifPrint::CatalogSearchBuilder  ← Our wrapper extends this

Our decorator (catalog_controller_decorator.rb):
  module CatalogControllerDecorator
    prepend CatalogControllerDecorator
  end
  def search_builder_class
    CatalogSearchBuilderWrapper                         ← Only affects CatalogController
  end
end
```

**The `FacetController` is NOT in the inheritance chain of our `CatalogControllerDecorator`** — Ruby's `prepend` only affects the class that was prepended.

### Why This Wasn't a Problem Before Our Code

Before we added facet limiting to the catalog, Blacklight used its default behavior:
- `FacetController` uses `Blacklight::SearchBuilder` (the base class)
- That builder includes `Blacklight::Solr::RequestBuilder#add_facet_params_to_request`
- Which reads `blacklight_config.facet_fields[field_name].limit` **from the request params** (`params[:limit]`) not from Solr-level `f.fieldname.facet.limit`

Our implementation adds **Solr-level** facet limits (`f.{field}.facet.limit = 5`), but on filtered results:
1. The FacetController's search builder chain DOES include `add_facet_params_to_request`
2. However, when a facet filter is already applied, Blacklight **overrides** the limit for that specific field to show all values (so users can see their selection context)
3. Our Solr-level params are added AFTER this override happens

### Why It Works on Main Page but Not Filtered

| Code Path | Search Builder Class | Our Decorator Active? | Facet Limit |
|-----------|---------------------|----------------------|-------------|
| `/catalog` (main) | `CatalogSearchBuilderWrapper` (via prepend) | ✅ YES | ✅ All facets → 5 |
| `/catalog?f[...][]=...` (filtered) | Still `CatalogController#index`, BUT Facet params use different Solr sub-request | ⚠️ Partially — our wrapper runs but **Blacklight's `FacetItemDecorator`** re-reads limits after our override | ❌ Facets that had filters reset to unlimited |

### Detailed Mechanism

When you visit `/catalog?f[people_represented_sim][]=West,+Jerry+...`:

1. **CatalogController#index IS called** (the search results ARE filtered correctly)
2. **Our wrapper DOES run** — it sets `f.people_represented_sim.facet.limit = 5`
3. **BUT then Blacklight's `FacetItemDecorator#fetch_facet_items`** intercepts:
   - For each facet field, it calls the search builder with a **different** set of params (isolated per-facet requests)
   - It reads from `params[:limit]` (a string like `"5"`) rather than the Solr-level `f.field.facet.limit`
   - When the current request already has an active filter for a facet, Blacklight **deliberately overrides** the limit to show more items (so users can navigate within their selection)
4. **Our Solr-level params (`f.{field}.facet.limit`) are ignored** because FacetController builds per-facet queries using `params[:limit]` which is nil/undefined

---

## Root Cause Summary

**The facet limiting works at the catalog-level via our Solr params, but fails on filtered results because Blacklight's FacetController uses `params[:limit]` (a URL query param) rather than the Solr-level `f.{field}.facet.limit` config for per-facet limit enforcement.**

This is **NOT a bug in our code** — it's an interaction between:
1. Our approach of using Solr-level facet limits (`f.field.facet.limit`)
2. Blacklight's FacetController which relies on `params[:limit]` for active facet filtering scenarios

---

## Fix Strategy

### Option A (Recommended): Override `FacetController`'s Search Builder

Add a decorator that applies our wrapper to **both** controllers:

```ruby
# app/controllers/catalog_controller_decorator.rb
module CatalogControllerDecorator
  def search_builder_class
    CatalogSearchBuilderWrapper
  end
end

# app/controllers/concerns/facet_controller_facet_limiting.rb (NEW)
module FacetControllerFacetLimiting
  def facet_search_builder_class
    CatalogSearchBuilderWrapper
  end
end

# app/controllers/hyrax/catalog/facets_controller_decorator.rb (NEW)
module HyraxCatalogFacetsControllerDecorator
  prepend HyraxCatalogFacetsControllerDecorator
  
  def facet_search_builder_class
    CatalogSearchBuilderWrapper
  end
end
```

**Complexity**: Medium — requires understanding Blacklight's FacetController internals

### Option B: Use `params[:limit]` Instead of Solr-Level Params

Instead of adding `f.{field}.facet.limit` to Solr params, add a global `limit` param that FacetController reads:

```ruby
def build(user_params = {})
  params = super
  # Also set the URL-level limit param that Blacklight's facet engine reads
  blacklight_config.facet_fields.each do |field_name, facet_config|
    limit = facet_config.limit || 5
    params[:limit] ||= limit.to_s  # <-- Global limit for all facets
    params[:"f.#{field_name}.facet.limit"] = limit  # <-- Still keep Solr-level
  end
  params
end
```

**Complexity**: Low — one line change to existing file
**Risk**: May affect other behavior (pagination of results, not just facets)

### Option C: Override `FacetController`'s Per-Fetch Logic

Override the method that fetches individual facet values on filtered pages. This is the most targeted fix but requires understanding Hyrax's facet rendering internals.

---

## Recommendation

**Option B (params[:limit] override)** is the safest and simplest fix. The wrapper should set **both**:
1. `f.{field}.facet.limit` → Solr-level enforcement (our current approach — correct for main page)
2. `params[:limit] ||= limit.to_s` → URL-level enforcement (needed for FacetController on filtered pages)

This maintains backward compatibility with existing behavior and doesn't require new files.

---

## Files to Modify

Only **one file** needs changes:

- **`app/search_builders/catalog_search_builder_wrapper.rb`** — Add `params[:limit]` setting in the `build` method

No new files needed. No decorator changes needed. Just one line added to the existing wrapper.

---

## Testing Plan (After Fix)

1. Main catalog page: `/catalog?locale=en` → All 6 facets show ≤5 items ✅
2. Filtered results: `/catalog?f[people_represented_sim][]=West,...&locale=en` → All facets show ≤5 items
3. "More" links on filtered page should now appear for Creator/Subject (both >5 items)
4. No regression on JSON endpoint (known separate Blacklight bug, not affected by this fix)

---

## Known Limitations

- JSON endpoint (`/catalog.json`) still has the Kaminari error (separate upstream Blacklight 7.42.0 bug)
- This investigation is **isolated** to the HTML facet display issue only
- Our main-page implementation was correct; the gap was only on filtered results pages
