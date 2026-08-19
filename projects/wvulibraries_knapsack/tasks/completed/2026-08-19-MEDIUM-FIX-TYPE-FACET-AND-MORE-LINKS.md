# 2026-08-17 — MEDIUM: Fix Type Facet & "More" Links in Sidebar

**Status**: 🔄 IN PROGRESS  
**Branch**: `fix/facet-links-and-hide-type-facet`  
**Verified by**: Jessica McMillen (via Slack screenshots)

---

## Issues Identified

### Issue 1: Type facet still showing in sidebar
- **Problem**: The "Type" facet (`generic_type_sim`) appears in the left sidebar on search results page and homepage
- **Expected**: Should be hidden/removed per Hyku #3072 workaround (documented in CATALOG_NAVIGATION_STRUCTURE.md)
- **Root Cause**: Line 126 of `catalog_controller.rb` still defines `generic_type_sim` as a visible facet — the decorator was never implemented

### Issue 2: "More" links showing only first 5 results
- **Problem**: Clicking "more" on facet links (Type, Resource Type, Collections) shows limited results modal instead of all available values
- **Jessica's words**: "It only shows the first five collections"
- **Expected**: "More" link should expand to show ALL facet values via paginated modal

---

## Resolution Executed

### File Changed: `hyrax-webapp/app/controllers/catalog_controller.rb`

1. **Commented out** `generic_type_sim` (Type) facet definition — removed from sidebar
2. **Added `show_more: true`** to all 11 facet fields:
   - resource_type_sim, creator_sim, contributor_sim, keyword_sim
   - subject_sim, language_sim, based_near_label_sim, publisher_sim
   - file_format_sim, contributing_library_sim, member_of_collections_ssim

---

## Testing & Verification COMPLETE ✅

**Local Testing (2026-08-19)**:
- ✅ Type facet hidden from homepage and sidebar
- ✅ More links show all values (directly to `/catalog/facet/field_name`)
- ✅ Homepage facets limited to 5 items with functional "More" link
- ✅ No regressions on other facets
- ✅ Rails restart/reload works cleanly (no Zeitwerk NameError)
- ✅ Wings::ModelRegistry guards working properly

**Branch Commits** (8 total):
1. `64950e5` — Hide Type facet + enable show_more on all facets
2. `641e3fd` — Add readable labels to all facet fields
3. `db4c18c` — Guard decorator (Wings error fix)
4. `02829a1` — Improved error guards with begin/rescue
5. `7a42a81` — Apply limit + show_more dynamically
6. `89d1cb7` — HomepageSearchBuilder wrapper (Solr-level limit)
7. `54ba088` — View-level facet limiting (WORKING FIX)
8. `0cc0230` — Remove unused decorator file (Zeitwerk fix)

**VM Deployment** (2026-08-19 20:45):
- ✅ Branch deployed to hykudev
- ✅ Application UP and running
- ✅ All fixes verified stable

**Status**: ✅ **COMPLETE — Deployed to hykudev, ready for Jessica QA verification**
