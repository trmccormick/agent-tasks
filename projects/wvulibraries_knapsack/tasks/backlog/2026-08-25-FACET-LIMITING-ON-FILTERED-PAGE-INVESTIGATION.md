---
status: backlog
priority: MEDIUM
type: investigation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
requires_tenant_build: false
tags: [facet-limiting, blacklight, filtered-page, more-links]

# FACET-LIMITING ON FILTERED PAGE — WHY NO MORE LINKS?

## Problem
On the main catalog page (`/catalog?locale=en`), facets show "More" links correctly (2-4 per page).
On filtered pages (`/catalog?f[people_represented_sim][]=West...`), facets show 0 "More" links.

## What We Know (from investigation)

### 1. Our wrapper config IS applied
- `CatalogSearchBuilderWrapper` overrides `search_builder_class` 
- It sets `params["f.field.facet.limit"] = 5` for ALL facet fields
- Our decorator sets `facet_config.limit = 5` for all facets in `blacklight_config`

### 2. Blacklight's limit mechanism (DISCOVERED)
The `_facet_limit.html.erb` template renders "More" links based on:
```erb
<% unless paginator.last_page? || params[:action] == "facet" %>
  <li class="more_facets_link">...</li>
<% end %>
```

The `paginator.last_page?` check depends on what Blacklight's `FacetController` reads:
```ruby
# In blacklight-7.42.0/lib/blacklight/solr/search_builder_behavior.rb
def facet_limit_for(facet_field)
  facet = blacklight_config.facet_fields[facet_field]
  return if facet.blank?
  if facet.limit
    facet.limit == true ? blacklight_config.default_facet_limit : facet.limit
  end
end
```

**Key finding:** `facet_limit_for` reads from `blacklight_config.facet_fields[field].limit`, NOT from Solr response params. Our config (`limit = 5`) IS correct here.

### 3. The likely explanation
When a filter is applied, facet counts are recalculated for the filtered dataset. If Creator has exactly 5 total items in the filtered results, and limit is 5:
- `paginator.current_count = 5` (items shown)
- `paginator.total_item_count = 5` (total available)
- `paginator.last_page? = true` → **no "More" link**

This would be **expected behavior, not a bug.**

### 4. What we tested
- Tested `params[:limit] ||= limit.to_s` fallback — partial improvement observed (0 → 1 more link), but this controls pagination not facets
- Debug logging was never deployed (never rebuilt after adding logger)
- No Solr query inspection possible without rebuild + verbose logging

## Why We're Not Fixing This Now

The `params[:limit]` approach was incorrect — it controls search result pagination, NOT facet display limits. Any fix needs to:
1. First verify if the filtered page truly has ≤5 items per facet (expected behavior) OR
2. If there ARE more items, find why Blacklight's `facet_limit_for` isn't returning 5

## Recommended Investigation Path

1. Check actual Solr response for a filtered search — does each facet show total_count vs used_items?
2. Add debug logging to `facet_paginator_class.new()` call in the running container
3. Or manually add `Rails.logger.warn` to `_facet_limit.html.erb` template to see what values are evaluated

## What Was Accomplished (Current State)

- ✅ Confirmed Kaminari::ZeroPerPageOperation on `/catalog.json` is upstream Blacklight 7.42.0 bug
- ✅ Confirmed facet limiting works correctly (5 items per facet) on main catalog page
- ✅ Created `CatalogSearchBuilderWrapper` that enforces `f.field.facet.limit = 5` for all fields
- ✅ Configured all facet fields with `limit: 5` and `show_more: true` in `CatalogControllerDecorator`
- ⚠️ Filtered page behavior is unexplained — likely expected behavior, not bug

## Files Modified (in wvu_knapsack)

1. `app/search_builders/catalog_search_builder_wrapper.rb` - Enforces facet limits for all fields
2. `app/controllers/catalog_controller_decorator.rb` - Sets limit:5, show_more:true on all facets + labels
3. `config/initializers/999_catalog_controller_decorator.rb` - Applies decorator via after_initialize

## Next Person to Pick This Up

Should verify the "expected behavior" hypothesis first before writing code:
```bash
# Check if filtered page actually has >5 items for Creator/Subject facets
docker exec wvu_knapsack-web-1 ruby -r ./config/environment << 'RUBY'
  solr = Hyrax::Solr.connection
  resp = solr.get('/select', params: {
    q: '*:*',
    fq: ['people_represented_sim:"West, Jerry, 1938-2024"'],
    facet: 'true',
    'f.creator_sim.facet.limit': '-1',
    'f.subject_sim.facet.limit': '-1',
    rows: 0
  })
  puts resp.to_h['facets']['facetFields'].inspect
RUBY
```
