## STATUS SYNTHESIS REPORT

**Task**: Fix Facet Links & Hide Type Facet
**Status**: active
**Date**: 2026-07-07

### What I'm About to Do
Hide the Type facet from search sidebar by deleting it in the decorator.
Add a helper override to fix search result facet links by suffixing _sim to bare property names.
Test both fixes on local instance to verify facets display correctly and drilling works.

### Files I'll Modify
| File | Purpose | Current State |
|---|---|---|
| `app/controllers/catalog_controller_decorator.rb` | Add facet delete line | 1 line addition |
| `app/helpers/hyrax/override_helper_behavior.rb` | Create new helper override | New file (~30 lines) |

### Prerequisites Completed
- ✅ Read wvulibraries_knapsack project README
- ✅ Understand Hyku decorator pattern
- ✅ Know that decorator runs in to_prepare
- ✅ Understand M3 property name vs Solr field suffixes
- ✅ Have test credentials (none needed — local instance)

### Expected Outcomes
1. Type facet no longer visible on /catalog search page
2. Clicking Creator/Contributor/Location on result rows returns matching works
3. URLs show search_field=creator_sim (not search_field=creator)
4. No error messages in browser console or logs

### Critical Gotchas I Will Avoid
- ❌ Modifying hyrax-webapp submodule — instead ✅ create local override
- ❌ Using bare property names — instead ✅ add _sim suffix to field names
- ❌ Placing delete logic in wrong file — instead ✅ use catalog_controller_decorator.rb

---

**SYNTHESIS COMPLETE.** Ready to proceed with both fixes.
