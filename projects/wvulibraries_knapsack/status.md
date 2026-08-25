# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-08-25

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## Current Status
- **Status:** 🔄 **IN PROGRESS — Catalog facet limiting HTML validation (CatalogSearchBuilderWrapper testing)**
- **Active Branches:**
  - `main` — Stable; production-ready with full volume mount structure
  - `fix/hide-type-facet-add-show-more-facets` — ✅ DEPLOYED to hykudev (2026-08-19); awaiting QA verification from Jessica
  - `fix/catalog-facet-limiting-solr-level` — 🏗️ IN PROGRESS: Code committed; Qwen testing facet display on HTML page (2026-08-25)
  - `clover-test` — Clover IIIF viewer integration (backlog)
  - `ollama_testing` — Ollama vision model for alt-text generation (backlog, experimental)
- **Last Session:** 2026-08-20 (Investigation complete)
- **Current Session:** 2026-08-25 — Catalog facet limiting validation (Qwen testing task created)
- **Next Handoff:** Qwen (local agent) tests HTML facet display & "More" links

---

## 🔄 2026-08-25 — Catalog Facet Limiting HTML Validation (IN PROGRESS)

**Objective**: Confirm facet limiting works on actual HTML catalog page (not just code analysis).

**Status**: 
- ✅ Code implementation committed and ready
- ✅ Task file created for Qwen: `2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md` (in active/)
- 🟡 **Awaiting Qwen execution**: Test HTML catalog page on local Stack Car

**What Qwen Will Test**:
1. Switch to fix branch, update submodule to Kirk Wang's latest
2. Rebuild Stack Car stack
3. Load https://demo-wvu-knapsack.localhost.direct/catalog?locale=en
4. Verify facets show max 5 items + "More" link
5. Test "More" link functionality

**Acceptance Criteria**:
- Facets display exactly 5 items + "More" link when > 5 exist
- No errors in browser console or Rails logs
- "More" links navigate to full facet list
- All facets (Creator, Subject, Location, etc.) respect limit

**Code Ready for Testing**:
- **CatalogSearchBuilderWrapper**: Adds Solr-level `f.{field_name}.facet.limit` params (17 lines)
- **CatalogControllerDecorator**: Injects wrapper via `search_builder_class` override
- **Initializer**: Ensures decorator applied at correct Rails initialization time (after_initialize)
- **Pattern**: Proven working (HomepageSearchBuilderWrapper works on homepage)

**Synthesis Report Location** (where Qwen will save results):
`/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-08-25-FACET-LIMITING-HTML-TEST-RESULTS.md`

---

## ✅ 2026-08-25 — Pagination Error Root Cause Investigation (COMPLETE)

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
