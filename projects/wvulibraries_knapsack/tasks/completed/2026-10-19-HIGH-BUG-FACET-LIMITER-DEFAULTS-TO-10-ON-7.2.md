---
status: completed
priority: HIGH
type: bug-fix
system_domain: SEARCH | DISPLAY
mvp_alignment: BLOCKER
local_worker_safe: true
completed_date: 2026-10-19
---

## ✅ COMPLETED — Facet Limiter Fix (Phase 1)

**Completed:** 2026-10-19  
**Branch:** `feature/hyku-v7.2.0-upgrade`  
**Phase:** 1 (Diagnose & Fix)

---

## Progress / Outcome Summary

### Problem
Hyku 7.2 branch on hykudev showed 10 facet items instead of expected 5, with no "More" link. Root cause: `FlexibleCatalogBehavior.load_flexible_schema` (line 94) calls `add_facet_field(name, label: label)` with NO limit parameter, causing Blacklight to default to 10.

### Solution Assembled (4-File Architecture)
All files successfully created/cherry-picked and committed to `feature/hyku-v7.2.0-upgrade`:

1. **app/search_builders/catalog_search_builder.rb** ✅
   - Created from commit 2e35fbf on fix/hide-type-facet-add-show-more-facets branch
   - Extends AdvSearchBuilder, overrides add_facetting_to_solr
   - Sets `f.<field>.facet.limit = limit+1` for all facets
   - Enables Blacklight to detect "More" link when values exist

2. **config/initializers/catalog_search_builder.rb** ✅
   - Cherry-picked (commit c56eb87)
   - Registers CatalogSearchBuilder as the search builder class
   - Uses to_prepare block for Rails initialization

3. **config/initializers/catalog_controller_decorator.rb** ✅
   - Cherry-picked (commit 8b3dff3)
   - Registers facets with limit: 5
   - Force-registers critical WVU facets from YAML
   - Two-pass registration ensuring all _sim fields get limit: 5

4. **config/wvu_facet_defaults.yml** ✅
   - Cherry-picked (commit 8b3dff3)
   - Centralized configuration with defaults and force-registered fields
   - Single source of truth for facet limits

### Verification & Testing

**Local Rebuild:** ✅
```
docker compose down --remove-orphans && sleep 5 && sh up.sc.local.sh
Web server started: "Listening on http://0.0.0.0:3000"
CatalogSearchBuilder configured: "CatalogSearchBuilder: Configured CatalogController to use ::CatalogSearchBuilder"
No NameError or startup errors
```

**Facet Limits Verified:** ✅
- All static facets show limit=5
- All flexible facets receive limit: 5
- No nil limits on _sim fields
- Solr parameters correctly set `f.<field>.facet.limit = (limit + 1).to_i`

### Commits
- **1d57b85** — `feat(search): Add CatalogSearchBuilder class to enforce facet.limit+1 for Solr`
  - Pushed to origin/feature/hyku-v7.2.0-upgrade

- Earlier commits (cherry-picked):
  - **2bbf2f1** — `fix(facets): Apply working facet limiter from main to 7.2 branch`
  - **e2fdd12** — `fix(facets): simplify flexible facet limit logic to avoid timing issues`
  - **624ddc2** — `fix(facets): Override flexible/M3 facet limit from default 10 to 5 on Hyku 7.2`

---

## Acceptance Criteria — PHASE 1

- [x] Root cause identified: Hyrax 7.2 line 94 add_facet_field with no limit parameter
- [x] Facet limiter configuration corrected: 4-file solution assembled
- [x] `app/search_builders/catalog_search_builder.rb` exists and sets `facet.limit = limit + 1`
- [x] Local verification: No NameError, clean boot, stable container
- [x] Changes committed to feature/hyku-v7.2.0-upgrade
- [x] Ready for hykudev VM testing (user to verify: 5 items/facet + "More" link)

---

## Architecture Summary

### Two-Layer Facet Limiting System

**Blacklight Layer (Display):**
- `field_config.limit = 5` determines how many items user sees

**Solr Layer (Server Request):**
- `f.<field>.facet.limit = 6` tells Solr to return 6 values
- Without +1: Solr returns exactly 5, Blacklight can't detect if more exist
- With +1: Solr returns up to 6, Blacklight shows "More" link when 6th value exists

### How It Works
1. CatalogSearchBuilder intercepts add_facetting_to_solr before Solr request
2. Applies limit+1 to every facet uniformly
3. Ensures both static AND flexible facets get the same treatment
4. Backward compatible: static facets maintain limit: 5, flexible facets gain limit: 5

---

## PHASE 2 & 3 Status

**Phase 2 (Backport to hyku branch):** Not started (pending Phase 1 verification on hykudev)
**Phase 3 (LaRita collaboration):** Not started (depends on Phase 2)

These will be separate follow-up tasks after hykudev testing confirms fix works.

---

## Next Action

Human to:
1. Pull feature/hyku-v7.2.0-upgrade on demo-hykudev.lib.wvu.edu
2. Rebuild containers
3. Test facets: verify 5 items/facet + "More" link for facets with 6+ values
4. Confirm both static and flexible facets working correctly
5. After verification, create Phase 2 backport task for LaRita collaboration

---
## 🔄 SUPERSEDED by Hyku PR #3369

**Superseded:** 2026-10-27  
**Reason:** Upstream PR #3369 (LaRita Vega) implements  everywhere in catalog controller — same fix, no local workaround needed.  
**Action Taken:** Workaround code (4-file architecture) should be removed after upstream merged and verified.  
**Related:** HYKU-720 task now documents workaround removal steps.
---
## SUPERSEDED by Hyku PR #3369

Superseded: 2026-10-27
Reason: Upstream PR #3369 (LaRita Vega) implements default_facet_limit = 5 everywhere in catalog controller - same fix, no local workaround needed.
Action Taken: Workaround code (4-file architecture) should be removed after upstream merged and verified.
Related: HYKU-720 task now documents workaround removal steps.
