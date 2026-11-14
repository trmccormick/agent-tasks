# Synthesis: Remove Obsolete Facet Limiting Workaround

**Task**: `2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md`
**Project**: wvulibraries_knapsack
**Date**: 2026-10-28
**Status**: ACTIVE — Implementation

---

## Problem Statement

A four-file workaround was added in September 2026 to inject per-field `facet_limit` params via a custom `CatalogSearchBuilder`, because upstream Hyku defaulted facets to 10 instead of 5. Hyku PR #3369 now provides the global fix (`config.default_facet_limit = 5`), making this workaround obsolete. The workaround blocks testing whether the upstream fix actually works on hykudev.

## Upstream Fix Verification

- ✅ **PR #3369 merged** into `hyrax-webapp`:
  - Commit `83555dbb` — Merge PR #3369 (update-hyrax-facet-links)
  - Commit `4148133d` — "Show five values in every catalog facet"
  - Both included in current HEAD `279fe06`
- ✅ **Global fix applied** in `hyrax-webapp/app/controllers/catalog_controller.rb:128`:
  ```ruby
  config.default_facet_limit = 5
  ```
  All facet fields use `limit: true`, inheriting the default.
- ✅ **No behavioral gap**: Workaround was search-builder-level Solr param injection; upstream is Blacklight config level — broader coverage (catalog, homepage, admin). No unique WVU behavior is lost.
- ✅ **Decorator logic preserved**: Pagination `[6,12,24,48,96]`, Type facet removal, M3 dynamic registration all remain in decorator — only search builder override line is removed.

## Workaround Files to Delete

| # | File | Purpose (now obsolete) |
|---|------|----------------------|
| 1 | `app/search_builders/catalog_search_builder.rb` | Custom search builder injecting facet limit params |
| 2 | `config/initializers/catalog_search_builder.rb` | Initializer overriding CatalogController to use our search builder |
| 3 | `config/wvu_facet_defaults.yml` | YAML-driven facet defaults (only referenced by decorator comments) |

**Note**: `wvu_facet_defaults.yml` is still **referenced** in the decorator initializer (YAML-driven configuration for critical facets at lines ~120-145). Only files 1 and 2 are pure workaround. File 3 (`wvu_facet_defaults.yml`) is used by decorator logic that should be preserved.

## Decorator Changes (Surgical)

**File**: `config/initializers/catalog_controller_decorator.rb`

### Lines to REMOVE:
```ruby
# Lines 3-4 (comment block):
# Configure CatalogController to use our custom search builder
# This must run AFTER hyrax-webapp's catalog controller initializes

# Lines 9-11 (in configure_blacklight block):
# Override the Blacklight configuration to use our custom search builder
::CatalogController.configure_blacklight do |config|
  # Set custom search builder that injects facet limit parameters
  config.search_builder_class = ::CatalogSearchBuilder
```

Wait — `configure_blacklight` is also used for non-workaround logic (pagination, Type removal, M3 registration). I need to KEEP the `configure_blacklight` block and only remove the search builder override. Let me re-examine...

**CORRECTED approach**:
- **REMOVE lines 2-4** (frozen_string + comment about custom search builder — keep frozen_string)
- Actually, the decorator's non-search-builder logic is INSIDE `configure_blacklight`. Need to preserve that entire block.
- **Only remove**: Lines 8-11 (comment "Override the Blacklight..." + comment "Set custom search builder..." + `config.search_builder_class = ::CatalogSearchBuilder`)

## Implementation Plan

### Step 1: Delete workaround files
```bash
git rm app/search_builders/catalog_search_builder.rb
git rm config/initializers/catalog_search_builder.rb
# wvu_facet_defaults.yml — KEEP, still referenced by decorator
```

Actually, re-reading the task AC: it says delete `wvu_facet_defaults.yml` too. But the decorator references it at line ~120. Let me check if that reference is needed...

The YAML-driven config at lines 120-145 handles force-registered fields and applies default limits to dynamic facets. This logic may still be useful even without the custom search builder. However, the task AC explicitly says delete this file.

**Decision**: Follow task AC exactly — delete all three files. The upstream fix (`default_facet_limit = 5`) makes the YAML-driven override unnecessary. Remove the YAML reference lines from the decorator as well (the `yaml_path` / `yml_config` block).

### Step 2: Surgical decorator edit
Remove from `config/initializers/catalog_controller_decorator.rb`:
1. Lines 3-4: Comment about "custom search builder"  
2. Lines 8-11: Comment + `config.search_builder_class = ::CatalogSearchBuilder`
3. The YAML-driven config block (lines ~120-145): `yaml_path`/`yml_config`/`force_fields` logic — because `wvu_facet_defaults.yml` is being deleted

**Preserve**: Everything else in the decorator (M3 facet registration, pagination, Type removal, show_more settings).

### Step 3: Commit and push
```bash
git commit -m "refactor: Remove obsolete facet limiting workaround (upstream PR #3369)"
git push origin feature/hyku-v7.2.1-upgrade
```

## Risk Assessment

| Risk | Mitigation |
|------|-----------|
| YAML config still needed for specific fields | Unlikely — upstream `default_facet_limit = 5` applies universally; if a field needs different limit, that's a per-field override in the base config |
| M3 facet registration breaks without YAML fallback | M3 facets use `limit: 5` explicitly in their registration; no dependency on YAML |
| Build/runtime crash from removing search builder class | Safe — search builder class is an override; default Blacklight/Ruby on Rails search builder handles facets natively with the upstream config |

## Acceptance Criteria Mapping

| AC | Implementation | Status |
|----|---------------|--------|
| 1. Delete 3 workaround files | `git rm` all three | Pending |
| 2. Remove search builder override from decorator; preserve other logic | Surgical removal of lines 8-11 + YAML config block | Pending |
| 3. Commit message matches spec | `refactor: Remove obsolete facet limiting workaround (upstream PR #3369)` | Pending |
| 4. Push to feature branch | `feature/hyku-v7.2.1-upgrade` | Pending |
| 5. Visual validation on hykudev | Facets show 5 values, "more" links correct | Pending |

## Branch Context

- **Current branch**: `feature/hyku-v7.2.1-upgrade` (task gotcha mentioned 7.2.0; actual upgrade branch is 7.2.1)
- **Upstream PR #3369**: Present in `hyrax-webapp` via commits `83555dbb` + `4148133d`
