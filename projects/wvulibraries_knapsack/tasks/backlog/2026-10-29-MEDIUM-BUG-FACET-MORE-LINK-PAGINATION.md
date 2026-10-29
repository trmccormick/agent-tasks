# Task: Fix Facet "More" Link Pagination (Showing Limited Results Instead of All)

---
status: backlog
priority: MEDIUM
type: bug
system_domain: CONTROLLERS
created: 2026-10-29
last_updated: 2026-10-29
related_task: 2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md
project: wvulibraries_knapsack

---

## Issue

After deploying feature/hyku-v7.2.0-upgrade to hykudev with facet limiting fix (AC #5), facets correctly show 5 values in the collapsed view on the **search results page**. However, **homepage facets** show a different behavior:

- **Homepage facets**: When clicking the "More" link, the expansion page ALSO limits to 5 values instead of showing all available values
- **Search results facets**: Working correctly (5 values in collapsed view, "More" link behavior correct)

**Expected behavior**: Homepage "More" link → shows ALL facet values  
**Actual behavior**: Homepage "More" link → shows only 5 values (still limited)

**Location of issue**: Homepage facet rendering, NOT search results page

## Discovery Context

- Task: 2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND
- Deployment: feature/hyku-v7.2.0-upgrade on hykudev
- Date discovered: 2026-10-29 (during AC #5 user validation)
- Validator: User testing on hykudev catalog

## Investigation Needed

1. Check if this is a Blacklight configuration issue (facet more link settings)
2. Verify hyrax-webapp PR #3369 handles facet expansion correctly
3. Check if config.default_facet_limit is being applied to the "more" view
4. Confirm if this is a regression from the facet limiting workaround removal or pre-existing

## Acceptance Criteria

- [ ] Root cause identified (Blacklight config, Hyrax bug, WVU decorator issue)
- [ ] "More" link shows all facet values (no limit)
- [ ] Verified on hykudev
- [ ] Confirmed not a regression from AC #5 changes

## Gotchas

- May need to coordinate with Hyku/Hyrax upstream if this is a framework bug
- The facet limiting itself (AC #5) is working correctly—this is specifically about the expansion behavior
- Config.default_facet_limit may need to NOT apply to facet more views (needs investigation)
