# Upstream Contribution Audit — `fix/facet-links-and-hide-type-facet`

**Date**: 2026-08-05
**Branch**: `fix/facet-links-and-hide-type-facet` vs `main`
**Purpose**: Identify which changes are Knapsack-specific customizations vs. upstream-worthy fixes for Hyku/Hyrax

---

## Summary

Of the **34 files changed** on this branch, **12 are upstream-worthy fixes** that should be pushed to Hyku or Hyrax, and **8 are Knapsack-specific customizations** that belong only in this repo. The remaining 14 files are infrastructure/deployment concerns.

---

## 🔼 UPSTREAM-WORTHY FIXES (Should go to Hyku or Hyrax)

These fix bugs in upstream code that affect all tenants, not just WVU Knapsack. They should be submitted as PRs to the appropriate upstream repo.

### 1. Sprockets Asset Pipeline Fix
**File**: `config/initializers/sprockets_directive_patch.rb`
**Upstream Target**: **Hyku** (`hyku-core`)
**Bug**: hyrax-webapp's `application.js` and `application.css` contain `*= require blacklight_advanced_search` directives that reference a gem whose assets are not installed. This causes 500 errors on every page load during asset compilation.
**Fix**: Registers Sprockets preprocessors to strip missing `blacklight_advanced_search` requires before compilation.
**Why Upstream**: This is a broken require in hyrax-webapp's asset pipeline. The fix should be applied at the source (remove or conditionally require the directive in hyrax-webapp), not patched downstream.

### 2. Valkyrie Resource Resolver — Missing Wings::ModelRegistry Guard
**File**: `config/initializers/valkyrie_resource_resolver_override.rb`
**Upstream Target**: **Hyku** (`hyku-core`) or **Hyrax** (wings gem)
**Bug**: hyrax-webapp's `config/initializers/wings.rb` sets `Valkyrie.config.resource_class_resolver` to a lambda that calls `Wings::ModelRegistry.reverse_lookup(klass)` without checking if the constant is defined. When Wings isn't loaded at initialization time, this crashes with `NameError (uninitialized constant Wings::ModelRegistry)`.
**Fix**: Overrides the resolver with proper guards (`defined?`, `respond_to?`, rescue).
**Why Upstream**: The wings.rb initializer should guard against missing constants. This is a timing/initialization-order bug in upstream code.

### 3. Valkyrie Resource Resolver — Duplicate Patch (wings_fix.rb)
**File**: `config/initializers/wings_fix.rb`
**Upstream Target**: **Same as #2**
**Bug**: Same root cause as #2. This is a second patch for the same issue (created before realizing valkyrie_resource_resolver_override.rb already existed).
**Fix**: Re-registers resource_class_resolver with `defined?(Wings::ModelRegistry)` guard in a `to_prepare` hook.
**Why Upstream**: Redundant patch — should consolidate into one upstream fix.

### 4. Render Constraints Infinite Recursion Fix
**File**: `lib/blacklight_advanced_search/render_constraints_override_decorator.rb`
**Upstream Target**: **Hyku** (`hyku-core`) or **blacklight_advanced_search gem**
**Bug**: The decorator used `original_blacklight_method = Blacklight::RenderConstraintsHelperBehavior.instance_method(__method__)` with `Module.prepend`, which creates infinite recursion because `__method__` resolves to the decorated method name.
**Fix**: Changed to use `super(params_or_search_state)` pattern (the standard Ruby way to call the prepended method).
**Why Upstream**: This is a bug in how the decorator was written. The `super()` pattern is the correct approach and should be upstreamed.

### 5. Index Field Link — Bare M3 Property Names Break Facet Drill-Down
**File**: `app/helpers/hyrax/override_helper_behavior.rb` (method: `index_field_link`)
**Upstream Target**: **Hyku** (`hyku-core`) or **Hyrax**
**Bug**: Hyrax v5.2.0 writes bare M3 property names (e.g., "contributor") into catalog URLs as `search_field=contributor`, which targets no real Solr field. Users clicking metadata values on search result rows get broken drill-down links.
**Fix**: Prefers `link_to_facet` when configured; otherwise suffixes `_sim` onto the bare name so the URL routes to the indexed field.
**Why Upstream**: This is a bug in Hyrax's `HyraxHelperBehavior#index_field_link`. All tenants using M3 flexible profiles are affected.

### 6. Notifications Component — Renders Even When Disabled
**File**: `app/helpers/hyrax/override_helper_behavior.rb` (method: `render_notifications`)
**Upstream Target**: **Hyku** (`hyku-core`) or **Hyrax**
**Bug**: The notifications component renders and attempts WebSocket connections even when `realtime_notifications` is disabled, causing console spam with connection errors.
**Fix**: Returns early when `realtime_notifications` is not enabled.
**Why Upstream**: The component should respect the config setting at render time.

### 7. Realtime Notifications — Explicitly Disabled as Workaround
**File**: `config/initializers/hyrax.rb` (added `config.realtime_notifications = false`)
**Upstream Target**: **Hyku** (`hyku-core`) or **Hyrax**
**Bug**: Related to #6. The WebSocket connection fails with "Request origin not allowed" errors because the route isn't properly configured for the tenant's domain. Setting it to `false` prevents the JS from attempting connections.
**Fix**: Explicitly disables realtime notifications.
**Why Upstream**: This is a workaround, not a fix. The root cause (WebSocket origin validation) should be fixed upstream so tenants can opt-in if they want the feature.

### 8. Hide Type Facet — Generic Type Filter Not Useful for All Tenants
**File**: `app/controllers/catalog_controller_decorator.rb` (added `config.facet_fields.delete('generic_type_sim')`)
**Upstream Target**: **Hyku** (`hyku-core`) — Reference: Hyku #3072
**Bug**: The "Type" facet shows "generic_type_sim" which is not useful for most tenants. This was flagged as Hyku #3072.
**Fix**: Removes the facet from the sidebar.
**Why Upstream**: This is a UX decision that should be configurable at the framework level, not per-tenant.

---

## 📦 KNAPSACK-SPECIFIC CUSTOMIZATIONS (Stay in Knapsack)

These are WVU-specific customizations that only apply to this deployment and should remain in the Knapsack repo.

### 1. Navigation Menu Override
**File**: `app/views/_controls.html.erb`
**Type**: Customization
**What**: Removes Help link, updates Contact link to external LibAnswers URL (`https://westvirginia.libanswers.com/wvrhc`).
**Why Local**: WVU-specific navigation requirements.

### 2. Facet Label i18n Translations
**File**: `config/locales/blacklight.en.yml`
**Type**: Customization
**What**: Provides translations for `people_represented_sim` facet and field labels ("People Represented").
**Why Local**: WVU-specific metadata fields from M3 profile. Other tenants have different fields.

### 3. Placeholder Assets for blacklight_advanced_search
**Files**: `app/assets/javascripts/blacklight_advanced_search.js`, `app/assets/stylesheets/blacklight_advanced_search.css`
**Type**: Workaround (temporary)
**What**: Empty placeholder files to satisfy Sprockets require directives.
**Why Local**: These are local workarounds for the upstream asset pipeline bug (#1 above). Once #1 is fixed upstream, these can be removed.

### 4. Theme-Specific View Overrides
**Files**: 
- `app/views/themes/wvu_home/_user_util_links.html.erb`
- `app/views/themes/wvu_home/hyrax/homepage/_browse_collections_modal.html.erb`
- `app/views/themes/wvu_home/hyrax/homepage/_featured_collection_section.html.erb`
**Type**: Customization
**What**: WVU Home theme-specific UI overrides.
**Why Local**: Theme-specific to WVU's branding and UX requirements.

### 5. Homepage Facet Modal Implementation (Issue #11)
**Files**: 
- `app/views/hyrax/homepage/_facet_limit.html.erb`
- `app/views/hyrax/homepage/_facet_modal.html.erb`
**Type**: Feature/Customization
**What**: Generic facet browsing modals for the homepage. Allows users to browse facet values without leaving the homepage.
**Why Local**: This is a WVU-specific feature request (GitHub Issue #11). The modal UX approach may not be desired by all tenants.

---

## 🏗️ INFRASTRUCTURE-ONLY CHANGES (Deployment Concerns)

These are deployment/infrastructure changes that stay in Knapsack but are not code fixes.

| File | Change |
|------|--------|
| `docker-compose.yml` | Added `./data/bundle:/usr/local/bundle:cached` volume, restart policies |
| `docker-compose.local.yml` | Synced bundle volume with production config |
| `docker-compose.production.yml` | Synced bundle volume with local config |
| `Dockerfile` | Pre-created `/app/samvera/data` directory to prevent Rails mkdir EEXIST error |
| `.dockerignore` | Optimized build context (excluded data/, node_modules/, .git/, etc.) |
| `.gitignore` | Added backup file patterns (*.prev, *.bak) |
| `bin/db-migrate-seed.sh` | Added explicit `exit 0` to fix initialize_app exit code |
| `scripts/cleanup-prod.sh` | Cleanup script updates |
| `up.prod.local.sh` / `up.sh` | Production setup script fixes (tmp directory chown, log paths) |
| `config/initializers/dual_logging.rb` | Dual logging configuration |

---

## Recommended Action Plan

### Phase 1: Upstream PRs (Highest Priority)
Submit these to **Hyku** (`hyku-core`) first, as they fix bugs affecting all tenants:

1. **PR #1**: `config/initializers/sprockets_directive_patch.rb` — Fix broken asset pipeline requires
2. **PR #2**: `valkyrie_resource_resolver_override.rb` + `wings_fix.rb` (consolidated) — Fix Wings::ModelRegistry NameError
3. **PR #3**: `render_constraints_override_decorator.rb` — Fix infinite recursion in Module.prepend
4. **PR #4**: `override_helper_behavior.rb` — Fix index_field_link bare M3 names + render_notifications early return
5. **PR #5**: `config/initializers/hyrax.rb` realtime_notifications = false — Explain as workaround, upstream should fix WebSocket origin validation

### Phase 2: Knapsack-Specific (Keep Local)
These stay in the Knapsack repo and are ready for soft launch:
- Navigation menu override (`_controls.html.erb`)
- Facet label i18n (`blacklight.en.yml`)
- Homepage facet modals (Issue #11)
- Theme-specific view overrides

### Phase 3: Infrastructure (Keep Local)
These stay in Knapsack as deployment configuration:
- Docker/compose changes
- Build optimizations (.dockerignore, .gitignore)
- Setup scripts

---

## Key Insight

The cascade of 5 infrastructure issues discovered on 2026-08-03 was caused by **bugs in upstream code** that were hidden by container caching. A clean rebuild exposed them because:
1. Bundle volume fix → allowed gems to install
2. Wings NameError → app could boot but crashed on first request
3. Sprockets errors → asset compilation failed
4. Render constraints → search pages crashed
5. Account settings TypeError → dashboard/admin failed

**All 5 were upstream bugs**, not Knapsack problems. This audit confirms that the majority of "fixes" on this branch are actually patches for upstream issues. The Knapsack-specific customizations (navigation, facet labels, theme views) are minimal and well-contained.
