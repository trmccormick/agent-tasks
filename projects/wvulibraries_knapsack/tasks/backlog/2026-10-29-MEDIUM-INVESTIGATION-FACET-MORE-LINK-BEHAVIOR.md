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

### Working Behavior (main branch / production VM)
- Homepage facets show collapsed values in sidebar (default count based on config)
- "More" link → **no modal**, instead redirects to `/catalog/facet/{field_name}?locale=en`  
- Expanded facet page shows ALL available facet values with pagination (Previous/Next)
- Search results page works correctly: 5 values collapsed, all available via expansion

### Bug on hykudev (feature/hyku-v7.2.0-upgrade ONLY)
After deploying the recent changes to hykudev:
- Homepage "More" link now shows a **modal** that ALSO limits to 5 values instead of ALL
- Both collapsed view AND modal show only 5 values — this is the bug
- The original code on main does NOT show the modal; it redirects to `/catalog/facet/` page where all values appear

## Root Cause Hypothesis
The recent deployment changes (facet limiting fix + workaround removal) may have inadvertently changed the facet more link behavior:
- Main branch works correctly — no modal, full expansion via redirect
- Feature/hyku-v7.2.0-upgrade on hykudev introduces modal with limit applied to both collapsed AND expanded views
- `config.default_facet_limit` is being applied where it shouldn't (to facet expansion)

## Investigation Scope

1. **Diff between main and feature/hyku-v7.2.0-upgrade on hykudev**
   - Compare config/initializers changes between the two branches
   - Check if any facet more link / modal config was introduced or removed
   - Verify which files were added/removed during the workaround removal (related_task)

2. **Check what changed that broke the working behavior:**
   - Main branch works correctly — verify which file/state on hykudev differs
   - Likely culprit: changes to `config.default_facet_limit`, Blacklight facet config, or Hyku PR #3369

## Files to Check First

- `hyrax-webapp/config/initializers/blacklight.rb` — facet more link config
- `config/initializers/catalog_controller_decorator.rb` — WVU customizations
- `hyrax-webapp/app/controllers/catalog_controller.rb` — Blacklight 8+ changes
- Any blacklight gem version bumps between main and feature branch

## Acceptance Criteria

- [ ] Root cause identified (which file/config change on hykudev broke the working main branch behavior)
- [ ] Restore original "More" link behavior: no modal, redirects to `/catalog/facet/*` page showing all values
- [ ] Verify fix works on hykudev
- [ ] Confirm not a regression from related_task (facet limiting workaround removal)

## Gotchas

- Main branch and production VM work correctly — this is ONLY reproducible on hykudev with feature/hyku-v7.2.0-upgrade
- The "no modal, redirect to page" behavior is the expected/correct behavior
- The modal behavior introduced on hykudev appears to be an unintended side effect
