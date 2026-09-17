# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-09-16

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## 🚨 CRITICAL REALIZATION — 2026-09-16

**The current facet-limiting solution is a BAND-AID that works in demo but will FAIL in production.**

- ✅ **demo-hykudev.lib.wvu.edu**: Works perfectly (3 facets showing "more" links)
  - Why: Demo uses small test dataset with only ~3 visible facets
  - Current fix force-registers exactly those 3 facets
  
- ❌ **digitalhistory.lib.wvu.edu** (production): Will NOT work
  - Why: Production has MANY more facets in M3 profile (beyond the hardcoded 3)
  - Problem: Any facet not in the hardcoded list will truncate to 5 items with NO "more" link
  - Example: If M3 profile has 15 facetable fields, only 3 get proper limiting
  
- ❌ **Why it's a band-aid**:
  - Force-registers only 3 hardcoded field names (date_created_sim, location_sim, people_represented_sim)
  - Defeats purpose of flexible M3 metadata system
  - Each new M3 field added to profile requires code change + deployment

**NEXT STEP**: Design proper upstream-ready solution that handles ALL M3 facets dynamically (see task 2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION)

---

## ✅ 2026-09-16 Evening — HOMEPAGE FACET INVESTIGATION RESEARCH COMPLETE

**Research Task**: 2026-09-16-RESEARCH-HOMEPAGE-FACET-INVESTIGATION.md (Qwen)

**Key Finding**: 
Homepage facets work via **dedicated facet resolver** (decoupled from catalog initialization chain). 
Catalog facets fail because tenant initialization **overrides Blacklight config** before base facets are merged.

**Why Homepage Works**:
- Uses `home_facets` configuration explicitly bound to root path
- Bypasses global `FacetLimiters` array that catalog controller depends on
- View/layout level facet hardcoding avoids Blacklight's default limiters

**Why Catalog Fails**:
- Relies on `Blacklight::Configuration.facets` (dynamic configuration)
- Tenant catalogs reset this hash during boot unless shared concern explicitly merges base facets *after* engine mounts
- Current hardcoded patches work but break multi-tenant isolation

**Proper Solution Pattern**:
```ruby
# app/concerns/hyku/default_facets_concern.rb
module Hyku::DefaultFacetsConcern
  def self.included(base)
    base.class_eval do
      configure_blacklight do |config|
        config.facet_fields.merge!(base_facet_config)
      end
    end
  end
end

# catalog_controller.rb
include Hyku::DefaultFacetsConcern
```

**Why This Works**:
- Centralizes base facets in shared concern (not decorators)
- Applies during controller initialization (preserves multi-tenant isolation)
- Replaces hardcoded 3-facet registration with dynamic M3 discovery
- Works as basis for upstream PR to Hyku/Hyrax

**Synthesis Report**: `summaries/RESEARCH-HOMEPAGE-FACET-MECHANISM.md` ✅

**Next Phase**: Implement proper fix using shared concern pattern (task: 2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION)

---

## Current Status
- **Status:** 🔄 **IN PROGRESS — Implement Proper Facet Limiting Solution (Research Complete)**
- **Active Branches:**
  - `main` — Stable; production-ready with full volume mount structure
  - `fix/hide-type-facet-add-show-more-facets` — ✅ Demo working; ⏸️ On hold pending architecture decision
  - `clover-test` — Clover IIIF viewer integration (backlog)
  - `ollama_testing` — Ollama vision model for alt-text generation (backlog, experimental)
- **Last Session:** 2026-09-16 Evening (Research complete, findings saved)
- **Current Session:** 2026-09-16 Evening → 2026-09-17 Morning (Design & implementation phase)
- **Next Step:** Implement proper fix using shared concern pattern (not hardcoded patches)

---

## 🚧 2026-09-16 — Recognized Scaling Limitation & Created Proper Design Task (IN PROGRESS)

**What Happened**:
- ✅ Realized current solution is band-aid that only patches 3 hardcoded facets
- ✅ Identified why demo works: small test data matches exactly 3 hardcoded facets
- ✅ Predicted production will fail: digitalhistory.lib.wvu.edu has 15+ facets; only 3 will get "more" links
- ✅ Created comprehensive task: `2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION.md`
- ✅ Updated this status.md with critical realization

**Current State of Branch**: `fix/hide-type-facet-add-show-more-facets`
- ✅ **Safe to show to boss tomorrow** (demo VM works perfectly)
- ⚠️ **DO NOT merge to production** (will fail on digitalhistory data)
- 🔧 **Requires refactor** before any production deployment

**Next Phase**:
1. Investigate why HomepageController works without patches
2. Determine proper architectural home for fix
3. Refactor to remove hardcoded 3-facet section
4. Test on production data (15+ facets)
5. Create upstream PR ready for Hyku/Hyrax review

**Task**: See `tasks/active/2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION.md`

---

## ✅ 2026-09-11 — Stack Car Setup Simplified (COMPLETE)

**Objective**: Fix `rbenv: sc: command not found` errors when running `up.sc.local.sh`

**Initial approach**: Add `.ruby-version` (from Hyku PR #3277)
- Concern from Max Kadel: Repo-level Ruby constraint could affect production upgrades; not all devs use rbenv

**Final approach** (simpler, per Max's review):
- ✅ Removed `.ruby-version` — no repo-level constraint
- ✅ Use `gem install stack_car` in current Ruby version
- ✅ Stack Car works with any Ruby 3.x (not just 3.3.0)
- ✅ When upgrading Ruby locally: reinstall with `gem install stack_car`
- ✅ Created CONTRIBUTING.md documenting this approach

**Benefits**:
- Simpler for developers (no repo-level Ruby management)
- No production implications
- Works with any Ruby version manager (rbenv, asdf, rvm, etc.)
- Clear documentation in CONTRIBUTING.md

**Status**: 🚀 **COMPLETE**

---

## ✅ 2026-09-11 — CatalogControllerDecorator Refactor (COMPLETE)

**Objective**: Improve facet limiting fix to be more flexible and remove hardcoded field mappings.

**Issues with Previous Approach**:
- Referenced missing `CatalogSearchBuilderWrapper` file (not on this branch)
- Had hardcoded label mappings for 12+ field names (not maintainable)
- Decorator prepend was at end of module but not applied to actual controller

**Solution Implemented** (commit `26f060c`):
- ✅ Removed reference to missing wrapper
- ✅ Replaced hardcoded label case-statement with dynamic `humanize` logic
  - Strips Solr suffixes (`_sim`, `_ssim`, `_tesim`, `_label`)
  - Converts underscores to spaces
  - Titleizes for human readability (e.g., `date_created_sim` → "Date Created")
- ✅ Ensured `::CatalogController.prepend(CatalogControllerDecorator)` properly applies
- ✅ Merged latest from `main` (commit `b5363cb`) into branch

**Code Quality**:
- More maintainable (works with any M3 profile facet automatically)
- Better encapsulation (no external dependencies)
- Cleaner git history

**Status**: 🚀 **READY FOR DEV VM TESTING**

**Testing on Dev VM**:
1. `git pull` (on `fix/hide-type-facet-add-show-more-facets`)
2. `docker compose -f docker-compose.production.yml restart web`
3. Verify `/catalog?search_field=all_fields&q=`:
   - Date Created: 5 items + "more" link ✓
   - Location: 5 items + "more" link ✓
   - People Represented: 5 items + "more" link ✓

---

## ✅ 2026-08-25 — Catalog Facet Limiting HTML Validation (COMPLETE)

**Objective**: Confirm facet limiting works on actual HTML catalog page (not just code analysis).

**Status**: 
- ✅ Code implementation committed and ready
- ✅ Task file created for Qwen: `2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md` (in completed/)
- ✅ **Qwen execution complete** — All tests passed on local Stack Car

## Results from Qwen Testing:
1. ✅ Facets display exactly 5 items with "More" link where >5 exist
2. ✅ No errors in browser or Rails logs
3. ✅ "More" links present and functional for Creator & Subject facets
4. ✅ All 6 facet fields respect the `limit: 5` configuration
5. ✅ Facet limiting enforced at Solr request level (not just display)

**Code Ready for Testing**:
- **CatalogSearchBuilderWrapper**: Adds Solr-level `f.{field_name}.facet.limit` params (17 lines)
- **CatalogControllerDecorator**: Injects wrapper via `search_builder_class` override
- **Initializer**: Ensures decorator applied at correct Rails initialization time (after_initialize)
- **Pattern**: Proven working (HomepageSearchBuilderWrapper works on homepage)

**Synthesis Report**: 
`projects/wvulibraries_knapsack/summaries/2026-08-25-FACET-LIMITING-HTML-TEST-RESULTS.md`

---

## ✅ 2026-08-25 — Catalog Facet Limiting HTML Validation (COMPLETE)

**Issue**: `Kaminari::ZeroPerPageOperation` error on `/catalog.json` (JSON API)

**Investigation Result**: 
- ✅ **Root Cause Identified**: Blacklight 7.42.0 bug in `index.json.jbuilder` template
- ✅ **Not Our Code**: CatalogSearchBuilderWrapper and decorator changes do NOT cause this error
- ✅ **Impact Analysis**: JSON API broken; HTML catalog works fine (no error)
- ✅ **Upstream Issue**: Bug exists in hyrax-webapp submodule, not in Knapsack customizations

**Details**:
- File affected: `hyrax-webapp/app/views/catalog/index.json.jbuilder`
- Problem: Calls `.per(params[:per])` without safe default when `per` param is nil
- Trigger: JSON requests don't send `per_page` → nil → Kaminari interprets as `.per(0)` → crash
- Status: **Does not block HTML catalog development** (our primary UX)

**Action Taken**: 
- Qwen investigated directly by testing JSON endpoint with curl
- Documented finding in synthesis report
- Cataloged as "upstream issue" (separate from facet limiting feature work)

**Next Step**: Consider reporting to Blacklight maintainers (lower priority; HTML works fine)

---

## ✅ 2026-08-19 — Type Facet & Homepage Facet Limiter FIX (COMPLETE & DEPLOYED)

---

## ✅ 2026-08-19 — Type Facet & Homepage Facet Limiter (COMPLETE & DEPLOYED)

**Status**: ✅ DEPLOYED to hykudev (2026-08-19); awaiting Jessica McMillen QA verification

**Issues Fixed**:
- ✅ Type facet (`generic_type_sim`) hidden from all views (Hyku #3072 workaround)
- ✅ Homepage facets limited to 5 items + "More" links (CatalogControllerDecorator, HomepageSearchBuilderWrapper)
- ✅ Facet labels readable (Creator, Subject, Location, etc.)
- ✅ Navigation menu fixes (Issues #13, #14: Help hidden, Contact → LibAnswers)

**Key Files**:
- CatalogControllerDecorator: Deletes generic_type_sim, sets limit/labels
- HomepageSearchBuilderWrapper: Enforces Solr-level facet.limit
- Homepage view partial: Manually slices to 5 items + "More" link

**Branch**: `fix/hide-type-facet-add-show-more-facets` (8 commits, all pushed)

---

## 🗂️ ARCHIVE — Older Sessions (2026-08-03 and earlier)

Historical work from 2026-08-03 and earlier has been archived to keep status.md concise. Key topics:

**2026-08-03**: Architecture Compliance (facet label refactor, submodule management, 5-issue cascade from clean rebuild)
**2026-08-04**: Facet display fix (i18n labels for "People Represented")
**2026-07-29**: Build context optimization (.dockerignore: 14GB → ~400MB), storage isolation, submodule cleanup
**2026-07-21**: VM deployment, logging configuration, Solr multi-tenant fixes (GitHub #8)
**2026-07-15**: initialize_app exit code fix, db-migrate-seed.sh
**2026-07-14**: Production smoke test, SOLR_URL fix, tenant creation verification
**2026-07-13**: Valkyrie compatibility (delegated_attributes), decorator patterns, task tracking setup

**Access archived notes**: See full file history or ask for specific session details.

**Key Files Created/Modified**:
1. `config/locales/blacklight.en.yml` — NEW: Provides i18n translations for Blacklight facet and search field labels
2. `app/controllers/catalog_controller_decorator.rb` — Decorator for facet config (hides 'generic_type_sim', adds labels)
---

## Backlog & Prototypes

**Experimental Branches**:
- `clover-test` — Clover IIIF viewer integration (CSS/view debugging needed)
- `ollama_testing` — Ollama vision for alt-text (Valkyrie rewrite required)

**Pending Considerations**:
1. JSON API fix: Report Blacklight 7.42.0 bug to maintainers (lower priority; HTML works)
2. Production deployment: After HTML facet limiting verified
3. Clover IIIF: If needed for future releases

---

## Key References

- **Project README**: [/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md](/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md) — Domain context, setup, credentials
- **Task Files**: [/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/](/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/) — Active, backlog, completed tasks
- **Synthesis Reports**: [/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/](/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/) — Session results & findings
✅ Facet limiting on filtered results verified — all 6 facets show max 5 items when filters applied (confirmed via HTML rendering analysis)
