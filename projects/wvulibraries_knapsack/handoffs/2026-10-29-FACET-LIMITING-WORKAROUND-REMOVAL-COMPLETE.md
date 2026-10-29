# Handoff: Facet Limiting Workaround Removal — Task Complete ✅

**Task**: 2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md  
**Status**: COMPLETED  
**Date**: 2026-10-29  
**AC #5 Validation**: PASS ✅ (All facets showing exactly 5 values on hykudev)

---

## What Was Done

### 1. Branch Discovery & Strategy
- Identified that `feature/hyku-v7.2.1-upgrade` (attempted v7.2.1 upgrade) had workaround removal commits
- Verified that v7.2.1 tag lacks PR #3369 (facet limiting), but origin/7.2-stable has it
- Decision: Use 7.2-stable (has PR #3369) + bring cleanup work to 7.2.0-upgrade branch

### 2. Code Cleanup (Commits ac6e001, 884c788, 687af48)
- **Removed 3 files entirely**:
  - `app/search_builders/catalog_search_builder.rb` (custom search builder with facet.limit+1 injection)
  - `config/initializers/catalog_search_builder.rb` (override to use ::CatalogSearchBuilder)
  - `config/wvu_facet_defaults.yml` (YAML facet configuration)
  
- **Trimmed decorator** (`config/initializers/catalog_controller_decorator.rb`):
  - Removed search_builder_class override
  - Preserved pagination config, generic_type_sim removal, M3 facet registration

### 3. Branch Consolidation
- Rebased `feature/hyku-v7.2.0-upgrade` onto `feature/hyku-v7.2.1-upgrade`
- Brought cleanup commits to 7.2.0-upgrade branch
- Verified submodule: 279fe062 (origin/7.2-stable) with branch tracking enabled
- Pushed to origin with force

### 4. Deployment to hykudev
- Checked out `feature/hyku-v7.2.0-upgrade` on hykudev
- Cleaned submodule (restored from git, reinited with `--init --recursive`)
- Removed `hyrax-webapp/Gemfile.lock` (prevents initialize_app permission issues)
- Ran `sh down.sh` and `sh up.sh`
- Containers started successfully

### 5. Validation (AC #5 PASS)
✅ **User verified on hykudev**: All catalog facets defaulting to 5 values  
- Static facets (Creator, Subject): 5 values
- M3/profile facets (People Represented, Location): 5 values
- "More" links present when >5 results exist
- No crashes, no errors

---

## Root Cause Resolution

**The Problem**: M3 facets were showing 10 (Blacklight default) instead of 5 (desired limit).

**Why It Happened**: 
- Hyrax::FlexibleCatalogBehavior auto-registers M3 facets via `add_facet_field(name, label:)` 
- Without limit parameter, they inherit Blacklight's hardcoded default of 10
- WVU's workaround tried to inject Solr params per-field at query time (not a clean solution)

**Upstream Fix (PR #3369 in 7.2-stable)**:
- Sets `config.default_facet_limit = 5` globally in Hyrax::Blacklight
- All facets use `limit: true`, inheriting the default
- Works for auto-registered M3 facets AND static facets
- Covers all contexts (catalog, homepage, admin)

**Why Removal Works Now**:
- 7.2-stable submodule has PR #3369 merged
- Upstream solution is superior (global, cleaner, broader coverage)
- WVU workaround is obsolete

---

## What Changed

| File | Status | Notes |
|------|--------|-------|
| `app/search_builders/catalog_search_builder.rb` | ✅ DELETED | Workaround search builder |
| `config/initializers/catalog_search_builder.rb` | ✅ DELETED | Override initializer |
| `config/wvu_facet_defaults.yml` | ✅ DELETED | YAML facet config |
| `config/initializers/catalog_controller_decorator.rb` | ✅ TRIMMED | Removed search_builder override; kept pagination, type facet removal, M3 registration |
| `hyrax-webapp` submodule | ✅ VERIFIED | 279fe062 (origin/7.2-stable) |

---

## Deployment Information

**Branch**: feature/hyku-v7.2.0-upgrade  
**Latest Commit**: 3098226 (fix: Use hyrax-webapp 7.2-stable for PR #3369 facet limiting)  
**Submodule SHA**: 279fe062 (origin/7.2-stable)  
**Deployed Environment**: hykudev  
**Deployment Date**: 2026-10-29  
**Status**: RUNNING, VALIDATED

---

## Next Steps

None. This task is complete. If future versions of Hyku (v7.2.2+) include both PR #3369 AND the GA4 security fix (GHSA-r6x6-wxm8-6994), you can upgrade the submodule to that version.

For now:
- **Production (main)**: Still on 7.1.3 (https://digitalhistory.lib.wvu.edu/)
- **Testing (feature/hyku-v7.2.0-upgrade)**: Deployed to hykudev, AC #5 PASSING

---

## Verification Checklist

- [x] Workaround files deleted
- [x] Decorator trimmed (search_builder override removed, other logic preserved)
- [x] Submodule at 7.2-stable (has PR #3369)
- [x] Code pushed to origin
- [x] Deployed to hykudev
- [x] Containers running
- [x] AC #5 VALIDATED: Facets show 5 values

**TASK STATUS: ✅ COMPLETE**
