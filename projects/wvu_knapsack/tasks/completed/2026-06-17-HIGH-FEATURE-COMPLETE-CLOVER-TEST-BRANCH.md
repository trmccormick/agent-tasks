---
status: active
priority: HIGH
type: feature
system_domain: CONTROLLERS
local_worker_safe: true
---

# TASK: Complete Clover IIIF Viewer Integration for clover-test Branch

**Status**: ACTIVE
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-17
**Last Updated**: 2026-06-17
**Branch**: clover-test
**Assigned To**: (pending - assign to local Qwen3.5-27B)

---

## Summary
Complete the Clover IIIF Viewer integration on the clover-test branch. The core decorator infrastructure is in place; this task focuses on final CSS/view fixes and testing per-tenant feature flag activation via Flipflop.

**CONTEXT**: This work prototypes per-tenant feature flag patterns and decorator architecture that may be valuable for Hyrax community discussion, separate from LLM integration decisions. Code should reflect clean architectural patterns and clear design rationale.

---

## Context

**What is done (main branch)**:
- Pagination settings: `config.per_page = [6, 12, 24, 48, 96]`, default `12`
- Featured collections limit increased: 6 → 15
- Both use knapsack decorator pattern (no engine modifications)

**What needs work (clover-test branch)**:
1. **CSS/View Fixes**: Featured collections display grid layout broken (single column vs 2×3 grid)
   - SCSS rules exist: `display: flex; flex-wrap: wrap; gap: 2rem;` in `wvu_home_theme.scss`
   - Issue: May be asset recompile or CSS selector conflict
   - Reference: `app/assets/stylesheets/wvu_overrides/wvu_home_theme.scss` (lines 152-220)

2. **Clover Viewer Infrastructure**: Ready for activation
   - Files: `config/initializers/work_show_presenter_clover_decorator.rb`
   - Files: `config/initializers/valkyrie_resource_resolver.rb`
   - Files: `app/models/document.rb` (delegated_attributes fix)
   - Feature flag gracefully defaults to `false` when not defined

3. **Testing**: Per-tenant Flipflop activation
   - Must verify clover viewer toggles on/off per tenant via admin dashboard
   - Must verify no errors when feature flag is undefined

---

## Problem Statement

1. **Featured Collections Layout**: After increasing limit to 15, landing page shows single-column layout instead of 3-per-row grid
   - Expected: 2 rows × 3 columns (6 displayed by default, up to 15 available)
   - Actual: Single column vertical list, no tile/card styling

2. **Clover Viewer Toggle**: Feature flag ready but not yet tested across tenants
   - Need to verify Flipflop admin interface allows per-tenant enable/disable
   - Need to verify no crashes when feature is disabled

---

## Solution Approach

### Part 1: Fix Featured Collections Grid Layout
1. Verify CSS is loaded: Check browser DevTools → Inspect `explore-collections-container`
2. Check if `display: flex` is applied in computed styles
3. If CSS not loading:
   - Run `docker-compose exec web bash -c "bundle exec rails assets:precompile"`
   - Restart web container
4. If CSS loading but not working:
   - Check for CSS specificity conflicts (inspect parent/child selectors)
   - Verify `wvu_home_theme.scss` is included in asset pipeline
   - Check `app/assets/stylesheets/hyku_knapsack/application.scss` imports
5. Debug in Rails console if needed:
   - `FeaturedCollection.feature_limit` should return `15`
   - `@featured_collection_list.featured_collections.size` should show actual count

### Part 2: Test Clover Viewer Feature Flag
1. Access admin dashboard → Features tab
2. Find or create `clover_viewer` feature flag entry
3. Toggle on/off and verify:
   - No errors in web logs
   - Work show page loads without crashing
   - Clover viewer partial renders/hides based on flag
4. Test on multiple tenants if available

### Part 3: Document Findings
- Record any CSS path changes needed
- Document Flipflop behavior with undefined features
- Note any Valkyrie-specific edge cases

---

## Files to Reference

**Featured Collections**:
- `app/assets/stylesheets/wvu_overrides/wvu_home_theme.scss` (lines 152-220)
- `app/views/themes/wvu_home/hyrax/homepage/_featured_collection_section.html.erb`
- `app/views/themes/wvu_home/hyrax/homepage/_explore_collections.html.erb`
- `config/initializers/featured_collection_decorator.rb`

**Clover Viewer**:
- `config/initializers/work_show_presenter_clover_decorator.rb`
- `config/initializers/valkyrie_resource_resolver.rb`
- `app/models/document.rb`
- `hyrax-webapp/config/features.rb` (check if clover_viewer needs to be formally defined)

**Architecture Context**:
- Knapsack pattern: decorators in knapsack, engine untouched
- Valkyrie flexible mode: no ActiveFedora delegation
- Flipflop: feature flags per-tenant via admin dashboard

---

## Testing & Validation

1. ✅ Stack Car dev environment running (`sh up.sc.local.sh`)
2. ⏳ Featured collections grid displays 2×3 layout on landing page
3. ⏳ Clover viewer can be toggled on/off without errors
4. ⏳ Work show page loads with clover_viewer? method returning expected boolean
5. ⏳ No console errors when feature is undefined

---

## Deployment Notes

- Changes remain on clover-test branch until validated
- When ready for production merge to main:
  - Merge featured collections fixes (if needed beyond main)
  - Keep clover viewer decorators for future tenant feature flag usage
  - No database migrations required
  - No new dependencies added

---

## Known Issues / Context

- **Valkyrie Flexible Mode**: FeaturedCollection uses class attribute, override via decorator (working)
- **Flipflop Exception Handling**: Uses broad `StandardError` rescue (safer than specific exception classes)
- **Wings::ModelRegistry**: Conditionally handled in valkyrie_resource_resolver.rb for hybrid AF/Valkyrie environments
- **Featured Collections Validation**: Lines 16, 26, 32 in hyrax-webapp/app/models/featured_collection.rb use feature_limit

---

## Completion Checklist

- ⏳ Featured collections grid layout restored
- ⏳ CSS precompiled and assets verified
- ⏳ Clover viewer feature flag tested and working
- ⏳ Per-tenant toggle tested (if multiple tenants available)
- ⏳ No errors in web logs
- ⏳ All validations passing
- ⏳ Documentation updated
- ⏳ Ready for merge to main or hold for later

---

## Dependencies & Integration

- Blacklight catalog controller
- Hyrax FeaturedCollection model
- Flipflop gem (feature flags)
- Hyku::WorkShowPresenter (clover_viewer? method)
- Valkyrie resource resolver (Wings bridge)
- CSS asset pipeline (Rails 7.2)

---

## Agent Assignment

**Assigned To**: [Qwen3.5-27B local (primary)]
**Why This Agent**: Local model ideal for CSS/view debugging, Rails asset pipeline work, and interactive testing
**Supervision Level**: watched carefully (first deployment of clover feature)

> If local Qwen attempts fail or test reveals blocking issues:
> - Escalate specific blocker to Claude Haiku 0.33x
> - Document exact error for code review
> - Do NOT proceed with production merge until CSS issue resolved
