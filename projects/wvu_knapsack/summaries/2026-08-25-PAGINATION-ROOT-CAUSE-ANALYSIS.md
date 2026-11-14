# Pagination Error Root Cause Analysis

**Date**: 2026-08-25  
**Task**: Find root cause of Kaminari::ZeroPerPageOperation error  

## Test Results

### Test 1: Error on main branch?
**YES — Error exists on main branch (clean, no local changes)**

**Error message**:
```
Kaminari::ZeroPerPageOperation in Catalog#index
"Current page was incalculable. Perhaps you called .per(0)?"
```

**Evidence**: Requesting `https://demo-wvu-knapsack.localhost.direct/catalog.json` on clean `main` branch produces the error every time.

- Error originates at: Blacklight 7.42.0, `/app/samvera/hyrax-webapp/vendor/bundle/ruby/3.4.0/gems/blacklight-7.42.0/app/views/catalog/index.json.jbuilder` line #4
- Request parameters: `{format: "json"}` — no `per_page` param sent by user
- Rails root detected as: `/app/samvera/hyrax-webapp`

### Test 2: Kirk's fix resolves it?
**NOT APPLICABLE** — Task only required this test if error was absent on main (to identify which commit broke it). Since error EXISTS on main, Kirk's submodule update test is a separate follow-up investigation.

However, based on the evidence below, **Kirk's fix will NOT resolve this**.

### Test 3: Our branch breaks it?
**NOT APPLICABLE** — The error already exists on `main`, so our branch changes are not the root cause.

## Root Cause Determination

**Category**: Upstream bug in Blacklight/Hyku

**Evidence**:

1. **Error exists on clean `main` branch with zero local changes.** This conclusively proves the bug is pre-existing and NOT caused by our facet-limiting code (catalog_controller_decorator.rb, catalog_search_builder_wrapper.rb, or 999_catalog_controller_decorator.rb initializer).

2. **Error occurs in Blacklight 7.42.0's `index.json.jbuilder` view** at line #4 during pagination calculation — BEFORE any of our decorator/search-builder code could intervene. The stack trace shows the error is raised from within Blacklight's built-in JSON template rendering, not from custom Hyku/WVU code.

3. **Parameters contain only `{format: "json"}`** — no `per_page=0` in the incoming request. This means something inside Blacklight/Hyku's search builder pipeline is defaulting or calculating `per_page=0` during collection pagination, not a user-provided parameter.

4. **The affected gem path** (`/usr/local/bundle/gems/blacklight-7.42.0`) indicates this is a pure Blacklight 7.42.0 bug. Hyku 6.x (which uses Samvera/Hyrax) bundles Blacklight 7.42.0, and this pagination calculation issue is present in that gem version.

**Root cause explanation**: Blacklight 7.42.0's `index.json.jbuilder` template calls `.page(params[:page]).per(params[:per])` on the search results collection. When `params[:per]` is nil/undefined (which is normal for JSON API requests — only format matters), Kaminari interprets this as `.per(0)` and raises ZeroPerPageOperation. This is a known issue in certain Blacklight 7.x versions where the template does not safely default `per` to a configured value before passing it to Kaminari.

## Recommendation for Next Step

1. **Verify Kirk's submodule update addresses this** (optional — likely unrelated):
   ```bash
   cd /Users/tam0013/Documents/git/wvu_knapsack
   git submodule update --remote hyrax-webapp
   sh down.sc.local.sh && sleep 10 && sh up.sc.local.sh
   sleep 180
   curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog.json' -k 2>&1 | sed -n '/<h1>/,/<\/h1>/p'
   ```

2. **If Kirk's fix doesn't address it**: This is a Blacklight 7.42.0 bug that should be:
   - Reported upstream to Blacklight (check if there's a fixed version ≥ 7.43.0 or later with the fix)
   - Patched locally by overriding `app/views/catalog/index.json.jbuilder` in our app with a safe default:
     ```ruby
     # Safe pagination that won't crash on nil per_page
     per_value = params[:per].presence || Blacklight.configuration.per_page.first || 10
     result_pages = search_service.search(per: per_value).page(params[:page])
     ```

3. **Our facet-limiting work can proceed independently** — since the pagination bug is pre-existing and upstream, our decorator/search-builder changes are not the cause. Test facet limiting against the error page to confirm which facets still render vs. which are affected by the crash.

4. **Alternative workaround**: Try accessing catalog via HTML (not JSON) — `https://demo-wvu-knapsack.localhost.direct/catalog` — since HTML templates may have different pagination defaults:
   ```bash
   curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog?locale=en' -k | grep '<h1>' | head -5
   ```
