# Investigation: Facet "More" Link Behavior — Limited Modal + Redirect to Separate Page

---
status: backlog
priority: MEDIUM
type: bug/investigation
system_domain: CONTROLLERS
created: 2026-10-29
last_updated: 2026-10-29
related_task: 2026-10-29-MEDIUM-BUG-FACET-MORE-LINK-PAGINATION.md
project: wvulibraries_knapsack

---

## Issue Summary

Two related issues observed after deploying feature/hyku-v7.2.0-upgrade to hykudev:

### 1. Homepage Facet "More" Link Shows Limited Results (Still Limited)
- **Expected**: "More" link expands to show ALL facet values
- **Actual**: Shows only 5 values (same limit as collapsed view)
- **Location**: Homepage facets, NOT search results page

### 2. Recent Modal Change Redirects to Separate Page Instead of Modal
- The modal behavior has changed — no longer shows in-place expansion
- Now redirects to `/catalog/facet/location_sim?locale=en` 
- This needs verified: is the redirect intentional (Blacklight 8+)? Or regression?
- Original expected behavior was a modal or in-page expansion

## Investigation Scope

1. **Check main branch vs hykudev** — What config changed between:
   - Main branch / production VM (what we currently have deployed)
   - Feature/hyku-v7.2.0-upgrade on hykudev (testing)

2. **Verify catalog/facet URL redirect behavior**:
   - `https://digitalhistory.lib.wvu.edu/catalog/facet/location_sim?locale=en`
   - Is this the intended Blacklight 8+ behavior?
   - Confirmed with Jessica (catalog team) on expected behavior

3. **Config changes to check**:
   - `config.default_facet_limit = 5` — does this apply to "more" views?
   - `limit: true` on facet fields in hyrax-webapp PR #3369
   - Any Blacklight facet expansion config that was removed/changed

4. **Determine if this is**:
   - A Blacklight upgrade behavior change (intentional)
   - A Hyku/Hyrax framework bug
   - A WVU decorator issue
   - A regression from facet limiting workaround removal

## Files to Check First

- `hyrax-webapp/config/initializers/blacklight.rb` — facet more link config
- `config/initializers/catalog_controller_decorator.rb` — WVU customizations
- `hyrax-webapp/app/controllers/catalog_controller.rb` — Blacklight 8+ changes
- Any blacklight gem version bumps between main and feature branch

## Acceptance Criteria

- [ ] Confirmed whether redirect to `/catalog/facet/*` is intentional (with Jessica/catalog team)
- [ ] If not intended: root cause identified + fix implemented
- [ ] If intended but broken ("More" limited in expanded view): fix applied
- [ ] Both behaviors working correctly on hykudev

## Gotchas

- May need to coordinate with catalog team (Jessica) on intended behavior change
- Blacklight 8+ may have changed facet expansion from modal to page-based — check gem changelog
- The "limited in expanded view" bug is separate from the modal→redirect behavior change
