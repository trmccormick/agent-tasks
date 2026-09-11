---
status: backlog
priority: HIGH
type: bugfix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
requires_tenant_build: false
tags: [facet-limiting, blacklight, m3-flexible-metadata, method-interception, blocker]
updated: 2026-09-11

# FACET-LIMITING ON FILTERED PAGE — WHY NO MORE LINKS?

## 🔴 BLOCKER DISCOVERED (2026-09-11)

**Main Catalog Page Still Shows 100+ Items** — Type facet deletion works ✅ but facet limiting not applied

### What Happened Today
1. ✅ Confirmed decorator loads and applies to CatalogController
2. ✅ Confirmed Type facet is hidden (generic_type_sim deleted successfully)
3. ❌ **Discovered**: M3 facet limiting is NOT working on catalog page
   - Date Created shows 100+ items (should show 5)
   - Location shows 100+ items (should show 5)
   - People Represented shows 100+ items (should show 5)

### Root Cause Analysis
Boot-time Blacklight config (limit: 5) IS being applied:
- Type facet deletion works (proves config is loaded)
- Facet config has limit: 5 set correctly

BUT runtime facet slicing via method wrapping is NOT working:
- Attempted `prepend(CatalogControllerDecorator)` — decorator loads but method not intercepted
- Attempted `define_method(:search_results)` — not called during searches
- Attempted `define_method(:index)` — not called during searches
- Attempted `alias_method` — not applied

**Hypothesis**: CatalogController or its `search_results` method is loaded AFTER our decorator runs, or Hyrax/Rails resets method lookups after our patch.

### Technical Details
- Branch: `fix/hide-type-facet-add-show-more-facets` (commit 08df7e9)
- Search action: `CatalogController#index` is called ✅
- Method wrapper: search_results method exists but our intercepts don't fire
- Solr limits: Not being enforced at query time (f.field.facet.limit params untested)

### 5 Approaches to Try Next (Prioritized)

**1. PRIORITY 1: Solr-Level Facet Limiting**
- File: `config/initializers/search_builder_facet_limits.rb`
- Test: Add debug logging to verify Solr params include `f.<fieldname>.facet.limit=5`
- If works: Eliminates need for controller/view slicing
- Why this might work: Facet counts limited at query time, before Blacklight processes response

**2. PRIORITY 2: Use method_missing**
- Ruby will call method_missing when search_results is invoked
- Bypasses need for direct method replacement
```ruby
def method_missing(method_name, *args, &block)
  if method_name == :search_results
    # Slice facets, then call super
  end
  super
end
```

**3. PRIORITY 3: Verify Blacklight's Real Entry Point**
- search_results might not be called directly
- Investigate Blacklight::SearchContext where search_results is defined
- May need to patch a different method entirely

**4. PRIORITY 4: Hook into after_initialize**
- Instead of decorating at load time, patch in Rails.application.config.after_initialize
- Ensures patching happens AFTER all gems/submodules loaded

**5. PRIORITY 5: Override View Partial (Nuclear Option)**
- If method wrapping impossible due to Rails/Hyrax architecture
- Override facet display partial to slice items in template
- Less elegant but guaranteed to work

## Problem (Original from 2026-08-25)
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

## Testing Commands (2026-09-11 Session)

```bash
# Deploy and test Solr-level limits first
git pull
docker compose -f docker-compose.production.yml restart web
sleep 5

# Trigger search that should show limited facets
curl "https://hykudev.lib.wvu.edu/catalog?search_field=all_fields&q=test" -k >/dev/null 2>&1
sleep 1

# Check for facet slicing logs
docker compose -f docker-compose.production.yml logs web | grep -i "slicing\|entering\|facet.limit"

# Verify visually
# https://hykudev.lib.wvu.edu/catalog?search_field=all_fields&q=
# Look for Date Created, Location, People Represented facets - should show 5 items + "more" link
```

## Key Findings (Current)

**What Works ✅**
- Type facet hiding: generic_type_sim deleted successfully
- Boot-time Blacklight config: Applied correctly
- M3 facet registration: date_created_sim and people_represented_sim registered with limit: 5
- Decorator module loading: File loads and prints debug messages

**What Doesn't Work ❌**
- Response-level facet slicing: Method wrappers not intercepting search_results
- Solr param injection: Untested (search_builder approach)

**Acceptance Criteria for Fix**
- [ ] Date Created shows exactly 5 items + "more" link on main catalog page
- [ ] Location shows exactly 5 items + "more" link on main catalog page
- [ ] People Represented shows exactly 5 items + "more" link on main catalog page
- [ ] Type facet remains hidden
- [ ] Filtered page behavior confirmed (5 items or fewer = no "more" link is expected)
- [ ] No debug logging in production code
- [ ] Code merged to main branch
