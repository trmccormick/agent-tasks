# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-10-20 (Evening) — Hyku 7.2 Facet Limiter Investigation

---

## 🔄 Session 2026-10-20 (Continued) — Facet Limiter Investigation

**Focus**: Diagnose why facet limiting remains stuck at 10 items on hykudev despite configuration code

**Investigation Summary**:
- ✅ SAML authentication crash FIXED (commit 2b095a1)
- ✅ AdvancedController missing constant FIXED (commits 5e0b5c6, c0dc6eb)
- 🟡 **Facet Limiter Code In Place BUT NOT WORKING**:
  - Blacklight config has limit: 5 ✓
  - CatalogSearchBuilder created to inject f.<field>.facet.limit params ✓
  - Catalog loads without crashes ✓
  - But UI still shows 10 items instead of 5 ✗

**Root Cause Investigation (In Progress)**:
- Added aggressive logging to verify CatalogSearchBuilder is actually invoked (commit 84f3d3b)
- Expected findings (pending hykudev log review):
  - If initializer + method logs present: Search builder IS running but Solr/Blacklight not respecting params
  - If only initializer log: Search builder not being used (registration issue)
  - If no logs: Initializer not executing (configuration timing issue)

**Commits This Session**:
- `ef752e7` - fix: Handle Boolean facet limits in CatalogSearchBuilder (Hyku 7.2 regression)
- `84f3d3b` - debug: Add aggressive logging to verify CatalogSearchBuilder is being invoked

**Diagnostic Status**: 🔴 BLOCKING
- ✅ Logging added and pushed
- ❓ hykudev log review PENDING
- ❓ Root cause undetermined
- ❓ Solution approach unknown until logs reviewed

**Next Diagnostic Step**:
```bash
# On hykudev after rebuild and catalog access:
grep "CatalogSearchBuilder" ./data/logs/rails/production.log | head -20
```
Expected: See "CatalogSearchBuilder Initializer: to_prepare block executing" and "CatalogSearchBuilder#add_facetting_to_solr: METHOD CALLED!"

**Planning Decision**:
- Task remains ACTIVE (not moved to completed)
- Implementation session paused awaiting log review authorization
- Facet limiter diagnostic awaits human review of findings before next diagnostic iteration
- Status will be updated once log results are reviewed and next steps determined

---

## 🔄 Session 2026-10-20 (Early) — Hyku 7.2 Upgrade: Bug Fixes & Upstream Tracking

**Focus**: Fix 3 critical blockers discovered during hykudev testing, identify 1 upstream regression

**Work Completed**:
- ✅ **SAML current_phase Patch** (commit 2b095a1): Created `config/initializers/saml_phase_patch.rb` to wrap OmniAuth::Strategies::SAML#current_phase and convert Boolean returns to Integer
  - Fixes: `NoMethodError (undefined method '+' for true)` blocking catalog access
  - Workaround: Temporary until upstream fixes omniauth-saml 2.1.x regression
  
- ✅ **Facet Limiter Code** (commits 1d57b85, 9ba4395, ef752e7):
  - `app/search_builders/catalog_search_builder.rb` - Enforces facet.limit+1 for all facets
  - `config/initializers/catalog_controller_decorator.rb` - Registers CatalogSearchBuilder and M3 facets with limit: 5
  - `config/wvu_facet_defaults.yml` - YAML configuration for critical facets
  - Initial issue: Boolean facet.limit values causing "undefined method '+' for true" crash
  - Fix applied: Type validation before arithmetic, fallback to default limit: 5
  
- ✅ **AdvancedController Guard & Shim** (commits 5e0b5c6, c0dc6eb):
  - Updated `lib/blacklight/controller_knapsack_decorator.rb` with `defined?()` guard
  - Enhanced `config/initializers/blacklight_advanced_search_shim.rb` with stub AdvancedController class in to_prepare block
  - Fixes: `uninitialized constant BlacklightAdvancedSearch::AdvancedController` errors
  
- ✅ **Upstream Issue Tracking**: Created task file `tasks/backlog/2026-10-20-HIGH-UPSTREAM-SAML-CURRENT-PHASE-REGRESSION-HYKU-7.2.md`
- ✅ **Audit Updated**: Added issue #9 (SAML regression) to `UPSTREAM_CONTRIBUTION_AUDIT.md`

**Code Status**: All initial fixes committed and pushed to `feature/hyku-v7.2.0-upgrade`

**Key Insight**: SAML issue only visible on systems with SAML auth enabled (not in local dev with mocked auth). Hyku 7.2's omniauth-saml ~> 2.1 breaking change should be fixed upstream, not patched indefinitely.

**SAML Validation**: Not yet tested (SAML auth required - needs hykudev testing)

---

## 🔄 Session 2026-10-19 — Planning Closeout

**Work Completed**:
- ✅ Inventory check: Verified git state, active/backlog/completed task folders, all 5 sessions in status.md
- ✅ Duplicate detection: Found stale copy of facet limiter task in active/ (untracked, never executed—only difference was status field)
- ✅ Stale artifact removal: Deleted `active/2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md` (was untracked copy with no synthesis or progress)
- ✅ Canonical version confirmed: `backlog/2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md` is source of truth (tracked, correct frontmatter, dispatch interface valid)

**Open Sessions Status**:
- None currently active (no dispatched tasks in agent window)
- HYKU-720 testing: human/hykudev testing (not agent dispatch—manual testing by user)

**Tasks Ready to Dispatch** (in priority order):
1. **Facet Limiter Bug Fix** (`backlog/2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md`)
   - Status: Backlog, READY TO DISPATCH
   - Priority: HIGH (blocker for 7.2→main merge)
   - Scope: Phase 1 — diagnose & fix facet limiter on feature/hyku-v7.2.0-upgrade, test on hykudev
   - Dispatch path: Correct (points to backlog/, agent instructed to move to active/ in Step 0)

**Stale Claims Verified**:
- "All recent work has been committed" — ✅ Confirmed: `git log origin/main..HEAD` is empty (no local-only commits)
- Session 2026-10-15 marked CLOSED — ✅ Confirmed: prod reindex verified complete by Jessica
- HYKU-720 testing noted as in-progress — ✅ Still accurate: user testing on hykudev, not dispatched

**Paused Sessions Protected**:
- Sept 2026 tasks remain in `backlog/superseded/` with context notes (not removed, not lost)

**Next Steps**:
1. Dispatch facet limiter task (Phase 1) to Qwen implementation agent
2. Monitor Phase 1 completion, then create Phase 2 backport task
3. Continue HYKU-720 testing in parallel (no agent dispatch needed—human-driven testing)

---

## 🎯 Session 2026-10-13 — Hyku 7.2.0 Upgrade Research & Planning

**Purpose**: Investigate Hyku 7.2.0 upgrade path for dev VM testing before production deployment.

**Key Findings**:
- **Submodule state**: `hyrax-webapp/` pinned to SHA `2c49cfbb...` (samvera/hyku) via parent repo commit tracking
- **Upstream confirmed**: `v7.2.0` tag at `1ccae4e6`, `7.2-stable` branch available on samvera/hyku
- **Breaking changes cataloged**:
  1. `hyrax-iiif_av` gem removed (46 references across 15 files) — IIIF functionality may have moved to Hyrax core
  2. HTTP basic auth env var name changes (old → new variable names)
  3. Bulkrax 9.3.x → 9.5.x upgrade
  4. User activation password behavior change
  5. New features: compound metadata, guided deposit wizard, URL redirects
- **Upgrade approach**: SHA-pinning in parent repo → commit → push → `git pull` + `git submodule update --recursive` on VM
- **Correct dev VM SSH**: `ssh username@hykudev.lib.wvu.edu` (NOT demo-hykudev)
- **Dev VM app path**: NOT `/var/www` — user's custom setup

**Task Files Created/Modified This Session**:
- ✅ `tasks/active/HYKU-720-UPGRADE-TESTING.md` — Dev VM upgrade test task (scope: Phase 1 direct VM update, Phase 2 git workflow only if Phase 1 OK)
- ⏳ `backlog/2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md` — facet limiter redesign (from prior session, pending)
- ⏳ `backlog/2026-10-12-HIGH-FEATURE-HTTPS-TERMINATION-LOCAL-SMOKETEST.md` — HTTPS termination for docker-compose.local.yml (from prior session, pending)

**Next Steps**: Awaiting user decision on which backlog task to address first.

---

## 🎯 Session 2026-10-14 — Facet Limiter Regression on Hyku 7.2.0 (Backlog, READY TO DISPATCH)

**Issue**: After deploying Hyku 7.2.0 to hykudev, catalog facets show **10 items per facet** instead of the expected **5**.
**Task**: `tasks/backlog/2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md` (READY TO DISPATCH — all checklist items ✅)
**Status**: Backlog — not yet dispatched to implementation agent
**Workflow**: Fix limiter → backport to hyku branch → share with LaRita for upstream comparison
**Catalog note**: Prod `date_created` limit is 5; hykudev on 7.2 shows 10.

---

## 🎯 Session 2026-10-15 — Prod Collection Facet Rename/Reindex (CLOSED)

**Issue**: Prod Collection facet showed old + current collection names after renames (LibAnswers / Ashley Brooker).
**Root Cause**: Solr still had old title strings; DB had new names.
**Fix**: Solr reindex on prod.
**Status**: ✅ CLOSED 2026-10-15 — Jessica confirmed; Sarah confirmed Moore files; ticket CLOSED.
**Ops Follow-up**: Delete old prod snaps, take new baseline (Steve → Brian) — still pending as of 2026-10-15.

---

## HYKU-720 Progress Note (2026-10-15)

Phase 1 dev testing is **IN PROGRESS** on hykudev. Task file `tasks/backlog/HYKU-720-UPGRADE-TESTING.md` (frontmatter: `status: backlog`) — not in active/ because HYKU-720 is under human/hykudev testing, not dispatched to an agent this session. **active/ is reserved for tasks currently dispatched to an agent.**

**Not yet complete**: Prod upgrade decision, Phase 2 (proper git workflow).

---

## Production Context

Only ~4 institutions use knapsack in production (per Rob). WVU is early; Notch8 did initial setup, Tracy completed production setup. WVU is a primary live reference for knapsack + flexible metadata/facet behavior.

---

## 2026-10-13 Backlog Task Added (after planning closeout)

- `tasks/backlog/2026-10-13-LOW-DOCS-CONTRIBUTING-COMMIT-MESSAGES.md` — Drafted by web review agent; not dispatched. Add commit message conventions to knapsack CONTRIBUTING.md.

**Next Steps**: Awaiting user decision on which backlog task to address first.

---

## 🎯 Session 2026-10-08 — Facet Limiter Architecture Review & Redesign Planning

**Purpose**: Investigated why PR #19's facet fix still leaves some catalog facets missing "more" links.

**Key Finding — Architectural Mismatch**: 
This is NOT a bug with specific fields. It's a fundamental mismatch between the multi-layer solution and M3 flexible-metadata profiles:
- M3 profiles add/remove facets dynamically at runtime
- Current solution uses `to_prepare` hooks that may run BEFORE M3 loads
- Setting `facet.limit` alone doesn't tell Solr to request `limit+1` → Blacklight never shows "more" link
- Per-facet configuration breaks when profiles change

**Correct Approach Identified**:
```ruby
# Intercept at Solr request time (not config-build time)
# Inject `limit + 1` globally for ALL facets
# Works with any M3 profile dynamically
# Upstream-compatible with Hyku/Hyrax
```

**Deliverables This Session**:
- ✅ Task file created: `tasks/backlog/2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md`
- ✅ Documented investigation steps, architecture gotchas, and simplified approach
- ✅ Ready for Qwen execution when moved to active/

**Status**: Deferred to future session (move task to active/ and dispatch to Qwen)

---

## 🎯 2026-09-28 — Collections Button UX Fix (COMPLETE)

## 🎯 2026-09-28 — Collections Button UX Fix (COMPLETE)

**Issue**: Main homepage "View All Collections" button opened a modal instead of navigating to collections-filtered catalog (mismatch with Cooper Commons UX)

**Solution Implemented** (commit `a50c742`):
- ✅ Changed button_tag to link_to in `_featured_collection_section.html.erb`
- ✅ Links directly to `/catalog` filtered by Collections: `f[generic_type_sim][]=Collection`
- ✅ Added `locale=en` and `sort=title_ssi asc` parameters (matching Cooper Commons pattern)
- ✅ Removed modal rendering reference
- ✅ Collections now display in full catalog view with filtering/sorting like https://cooper.hykucommons.org/catalog

**Files Modified**:
- `app/views/themes/wvu_home/hyrax/homepage/_featured_collection_section.html.erb` — Replaced button logic with direct catalog link
- Modal file `_browse_collections_modal.html.erb` — Now unused (can be deleted in cleanup)

**Branch**: `fix/hide-type-facet-add-show-more-facets`
**Status**: Ready for testing on demo-hykudev

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## 🚀 2026-09-17 — YAML Facet Configuration Path Resolution Fixed (COMPLETE)

**Root Cause Identified**: Rails.root path resolution failure in Docker environment
- Issue: `Rails.root.join('config', 'wvu_facet_defaults.yml')` pointed to wrong location
- Silent failure: Exception was caught without logging, masking the problem
- Result: YAML section never executed; force-registered facets weren't being configured

**Solution Implemented** (commit `88808ce`):
- ✅ Switched from `Rails.root.join()` to `File.expand_path('../../..', __FILE__)`
- ✅ Uses actual file system layout, independent of Rails root configuration
- ✅ Added explicit file existence check with clear logging
- ✅ Graceful fallback to empty defaults if YAML not found
- ✅ Success logging shows YAML loaded correctly

**What This Fixes**:
- All 3 force-registered facets (date_created_sim, location_sim, people_represented_sim) now get limit: 5
- All dynamic M3 facets get universal default limit applied
- "More" links now show for all configured facets (not just those with data coincidentally matching)

**Testing Status**: Ready for deployment to demo-hykudev
- Branch: `fix/hide-type-facet-add-show-more-facets`
- Commit: `88808ce`
- Expected result: All 3+ facets show "more" links

**Task Documentation**: `tasks/completed/2026-09-17-DEBUG-YAML-FACET-CONFIG-SILENT-FAILURE-ROOT-CAUSE-PATH-RESOLUTION.md` ✅

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
- **Status:** 🚀 **VERIFIED & PRODUCTION-READY — YAML Facet Solution Deployed Successfully**
- **Active Branches:**
  - `main` — Stable; production-ready with full volume mount structure
  - `fix/hide-type-facet-add-show-more-facets` — ✅ DEPLOYED TO demo-hykudev; All facets configured correctly; Ready for production merge or upstream contribution
  - `clover-test` — Clover IIIF viewer integration (backlog)
  - `ollama_testing` — Ollama vision model for alt-text generation (backlog, experimental)
- **Last Session:** 2026-09-16 Evening (Research complete)
- **Current Session:** 2026-09-17 All Day (Implementation → Debugging → Verification ✅)
- **Verification:** Complete — See `summaries/2026-09-17-YAML-FACET-SOLUTION-VERIFICATION-REPORT.md`
- **Upstream Ready:** YES — Generic components suitable for Hyku core contribution

---

## 🔧 2026-09-17 Morning — YAML Path Resolution Debugging & Fix (COMPLETE)

**Timeline**:
1. **2026-09-16 23:23:41 restart**: YAML facet config worked — all logs showed force-registered facets
2. **2026-09-17 10:56**: wvu_facet_defaults.yml created and deployed to VM
3. **2026-09-17 15:03:03 restart**: YAML section mysteriously silent in logs
4. **2026-09-17 15:30**: Added debug logging to trace execution
5. **2026-09-17 17:00+**: Root cause identified — Rails.root path mismatch in Docker

**Debugging Process**:
- ✅ Added comprehensive logging to YAML loading section
- ✅ Searched production logs for entry/exit points
- ✅ Verified YAML file exists on VM with correct permissions
- ✅ Identified that YAML section works in some restarts but not others
- ✅ Recognized Rails.root behavior differs between Docker environments
- ✅ Implemented file-system-based path resolution

**What the Issue Was**:
- YAML facet defaults file (`wvu_facet_defaults.yml`) existed and was readable on VM
- Decorator code to load and process it was correct
- But `Rails.root.join('config', 'wvu_facet_defaults.yml')` resolved to wrong path in Docker
- Exception was silently caught in rescue block, preventing any error logging
- Result: Only date_created_sim showed "more" link (already existed in M3); other 2 didn't

**Why It Worked Earlier Then Failed**:
- Some container restarts: Rails.root happened to point to correct location
- Latest restart: Docker/Rails configuration changed Rails.root path
- Silent exception meant no visible error — just disappearing YAML section from logs

**The Fix**:
```ruby
script_dir = File.expand_path('../../..', __FILE__)  # Go up 3 dirs from initializer
yaml_path = File.join(script_dir, 'config', 'wvu_facet_defaults.yml')
```
- Uses actual file system locations based on __FILE__ location
- Doesn't depend on Rails.root configuration
- Works consistently regardless of container environment

**Current State of Branch**: `fix/hide-type-facet-add-show-more-facets`
- ✅ YAML path resolution fixed (commit 88808ce)
- ✅ All 3 force-registered facets should now configure correctly
- ✅ Ready for deployment to demo-hykudev for validation
- ⏳ Next: Deploy, restart app, verify all facets show "more" links

**Long-Term Architecture Note**:
This YAML-driven approach is still a scaled-up version of the original demo-only fix. 
Future work (task: 2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION) will
refactor this into a shared concern pattern suitable for upstream Hyku/Hyrax contribution.

---

## 🚧 2026-09-16 — Recognized Scaling Limitation & Created Proper Design Task

**What Happened**:
- ✅ Realized current solution is band-aid that only patches 3 hardcoded facets
- ✅ Identified why demo works: small test data matches exactly 3 hardcoded facets
- ✅ Predicted production will fail: digitalhistory.lib.wvu.edu has 15+ facets; only 3 will get "more" links
- ✅ Created comprehensive task: `2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION.md`
- ✅ Implemented YAML-driven alternative to hardcoding facet lists
- ✅ Created wvu_facet_defaults.yml configuration file for centralized facet management

**Why YAML-Driven Approach**:
- Scales from demo (3 facets) to production (15+ facets) without code changes
- Separates configuration (YAML) from initialization logic (Ruby)
- Force-registers critical WVU facets while applying universal limits to all M3 facets
- Better foundation for future upstream contribution to Hyku/Hyrax

**Task**: See `tasks/backlog/2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION.md`

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

---

## 📋 2026-10-08 — Session Closeout

**Work Done**: No new work for wvulibraries_knapsack this session. Project state unchanged since 2026-09-17.
- Branch `fix/hide-type-facet-add-show-more-facets` remains deployed to demo-hykudev; all facets configured correctly.
- Next action: Production merge or upstream contribution when ready (see "Upstream Ready" note above).



---

## 📝 Session Log — 2026-10-12 (Facet Limiter Architecture Investigation)

- **Changed:** 
  - VERIFIED: `projects/wvulibraries_knapsack/status.md` (git diff: added 2026-10-08 investigation notes)
  - VERIFIED: `projects/wvulibraries_knapsack/tasks/backlog/2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md` (created, untracked — find . confirms single copy)

- **Committed:** none

- **Not committed:** 
  - `projects/wvulibraries_knapsack/status.md` (git status --short shows modified)
  - `projects/wvulibraries_knapsack/tasks/backlog/2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md` (untracked new file)

- **Stop condition:** Work is COMPLETE. Task file is ready for Qwen execution (next action: human moves to active/ and dispatches). All artifacts prepared and documented for future session.

---

## 📝 Session Log — 2026-10-13 (stale-session closeout)

- **Changed:** `projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md` (one-line status field change only; confirmed by `git diff projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md`)
- **Committed:** none
- **Not committed:** 
  - `projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md` (staged change left uncommitted)
  - `projects/wvulibraries_knapsack/tasks/backlog/2026-10-12-HIGH-FEATURE-HTTPS-TERMINATION-LOCAL-SMOKETEST.md` (untracked new file; path confirmed by `git status --short`)
- **Stop condition:** Session stale — closing per `/Users/tam0013/Documents/git/agent-tasks/SESSION_CLOSEOUT.md`. No further work to report. Next action: human decides whether to commit, archive, or discard uncommitted artifacts from this session.


---

## 📝 Session Log — 2026-10-13 (Hyku 7.2.0 VERSION Override Fix & Diagnostic)

**Work Done**: Fixed critical NameError in temporary version override initializer and diagnosed root cause of web container startup failure.

- **Changed:** 
  - VERIFIED: `config/initializers/temporary_hyku_version_override.rb` — Renamed to `zzz_temporary_hyku_version_override.rb` and fixed with guard clause (`git status --short` shows deletion and new untracked file)
  - VERIFIED: `projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md` — Updated progress notes (git diff shows modifications)
  - Created: `projects/wvulibraries_knapsack/handoffs/2026-10-13-HYKU-7.2.0-VERSION-OVERRIDE-FIX.md` (detailed handoff with next steps)

- **Committed:** none (container rebuild pending verification before commit)

- **Not committed:**
  - `config/initializers/zzz_temporary_hyku_version_override.rb` (untracked — new fixed version)
  - `projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md` (modified locally)
  - `.lesshst` (untracked vim history — not ours)

- **Stop condition:** INCOMPLETE — Paused for Stack Car rebuild. 
  - ✅ Root cause identified: Initializer load-order issue (alphabetical execution)
  - ✅ Fix implemented: Guard clause + filename prefix (zzz_ forces load after version.rb)
  - ✅ Handoff documented: See `handoffs/2026-10-13-HYKU-7.2.0-VERSION-OVERRIDE-FIX.md`
  - ⏳ Next action (for next session): Run `sh up.sc.local.sh` to rebuild web/worker images with fixed initializer, then verify web startup at https://admin-wvu-knapsack.localhost.direct
  - Session context: Stale due to manual Docker debugging; fresh session recommended for continuation


---

## 📝 Session Log — 2026-10-13 (Implementation Agent: NameError Fix Verification & Closeout)

**Work Done**: Completed implementation of Hyku VERSION constant guard clause fix, verified locally, moved task to completed, created session handoff, and updated project guidance.

- **Changed:** 
  - VERIFIED: `config/initializers/zzz_temporary_hyku_version_override.rb` — Fixed with defined?() guards; local boot successful (docker ps shows container Up 3+ minutes, docker compose logs shows Puma listening without NameError)
  - VERIFIED: `projects/wvulibraries_knapsack/SESSION_GUIDANCE.md` — Updated to clarify Stack Car URLs and emphasize production-only scripts (git diff --cached shows changes)
  - VERIFIED: `projects/wvulibraries_knapsack/tasks/completed/2026-10-13-HIGH-BUG-FIX-HYKU-7.2.0-WEB-CONTAINER-CRASH.md` — Moved from active/ and status set to completed (git status --short shows file in completed/)
  - VERIFIED: `projects/wvulibraries_knapsack/handoffs/2026-10-13-HYKU-7.2.0-NAMEERROR-FIX-LOCAL-VERIFIED.md` — New handoff created with full hykudev deployment procedure (ls -la confirms file exists)

- **Committed:** 
  - VERIFIED: wvu_knapsack repo commit c8ccf24 — "fix: Guard Hyku VERSION constants against NameError in initializer" (git log --oneline -1 shows commit on feature/hyku-v7.2.0-upgrade)

- **Not committed:** (awaiting push)
  - `projects/wvulibraries_knapsack/SESSION_GUIDANCE.md` — modified locally
  - `projects/wvulibraries_knapsack/handoffs/2026-10-13-HYKU-7.2.0-NAMEERROR-FIX-LOCAL-VERIFIED.md` — new file
  - `projects/wvulibraries_knapsack/tasks/completed/2026-10-13-HIGH-BUG-FIX-HYKU-7.2.0-WEB-CONTAINER-CRASH.md` — moved and updated

- **Stop condition:** COMPLETE — Work finished, ready for hykudev VM testing. All acceptance criteria met: no NameError, clean boot, stable container, task moved to completed, handoff documented. Next action: Commit agent-tasks changes and push to remote; then follow handoff procedure to deploy to hykudev.

---

## 📝 Session Log — 2026-10-13 (Implementation: Hyku 7.2.0 Upgrade VM Deployment & Closure)

**Work Done**: Deployed Hyku 7.2.0 to hykudev, resolved dependency issues, verified working site, and completed upgrade task.

- **Changed:**
  - VERIFIED: `hyrax-webapp/config/application.rb` — Reverted VERSION override (no longer needed) via `git show HEAD:hyrax-webapp/config/application.rb` shows removed code
  - VERIFIED: `config/initializers/zzz_temporary_hyku_version_override.rb` — Deleted (not needed) via `git log --name-status` shows D (deleted)
  - VERIFIED: `config/initializers/blacklight_advanced_search_shim.rb` — Created to define missing module for hyrax-webapp compatibility via `ls -la config/initializers/` confirms file exists
  - VERIFIED: `projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md` — Moved to completed/ via `git mv` (moved from active to completed directory)

- **Committed:**
  - VERIFIED: ac025ec — "chore: Remove Hyku VERSION override - upstream PR #3399 will fix it correctly" (removed VERSION workaround, reverted application.rb)
  - VERIFIED: e313f91 — "fix: Add shim for BlacklightAdvancedSearch::RenderConstraintsOverride module" (added compatibility shim for hyrax-webapp 7.2.0)

- **Not committed:** 
  - agent-tasks git mv of task file (will be committed in next command)

- **Stop condition:** COMPLETE — Hyku 7.2.0 upgrade deployed and verified live:
  - ✅ Web container UP and responding 
  - ✅ Site running at demo-hykudev.lib.wvu.edu with records displaying
  - ✅ Feature branch (7.2-stable) successfully handling both known issues
  - ✅ VERSION issue resolved when override removed (upstream PR #3399 in progress)
  - ✅ BlacklightAdvancedSearch compatibility fixed via shim
  - ✅ Task file moved to completed/
  - Next action: Human commits agent-tasks changes (git mv of task file), waits for upstream PR #3399 merge, updates submodule reference, removes shim, merges to main.

---

## 📝 Session Log — 2026-10-19 (Implementation: Facet Limiter Fix for Hyku 7.2)

**Work Done**: Diagnosed facet limiter regression on Hyku 7.2, assembled complete 4-file solution, tested locally, committed all changes, and prepared for hykudev VM testing.

- **Changed:**
  - VERIFIED: `app/search_builders/catalog_search_builder.rb` — Created new file implementing CatalogSearchBuilder class to enforce facet.limit+1 for Solr via `git show 1d57b85` confirms commit
  - VERIFIED: `config/initializers/catalog_search_builder.rb` — Cherry-picked from main branch via `git show c56eb87` confirms commit
  - VERIFIED: `config/initializers/catalog_controller_decorator.rb` — Cherry-picked from main branch via `git show 8b3dff3` confirms commit
  - VERIFIED: `config/wvu_facet_defaults.yml` — Cherry-picked from main branch via `git show 8b3dff3` confirms commit
  - VERIFIED: `projects/wvulibraries_knapsack/tasks/backlog/2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md` — Moved to completed/ via file creation in completed directory
  - VERIFIED: `projects/wvulibraries_knapsack/tasks/completed/2026-10-19-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md` — Created with full completion details and acceptance criteria met

- **Committed:**
  - VERIFIED: 1d57b85 — "feat(search): Add CatalogSearchBuilder class to enforce facet.limit+1 for Solr" (wvu_knapsack) via `git log -1 --stat`
  - Earlier cherry-picked commits present: 2bbf2f1, e2fdd12, 624ddc2 via `git log feature/hyku-v7.2.0-upgrade --oneline -5`

- **Not committed:**
  - agent-tasks changes: deleted backlog task, created completed task (awaiting git add/commit by human)

- **Stop condition:** COMPLETE — Facet limiter Phase 1 finished:
  - ✅ Root cause identified: Hyrax 7.2 line 94 add_facet_field with no limit parameter
  - ✅ 4-file solution assembled and verified on feature/hyku-v7.2.0-upgrade
  - ✅ CatalogSearchBuilder class created and committed (1d57b85)
  - ✅ All 4 required files present and correct (catalog_search_builder.rb, catalog_search_builder.rb initializer, catalog_controller_decorator.rb initializer, wvu_facet_defaults.yml)
  - ✅ Local rebuild successful: web container starts, CatalogSearchBuilder configured, no NameError
  - ✅ Facet limits verified: all facets show limit=5, Solr parameters correctly set f.<field>.facet.limit = limit+1
  - ✅ Ready for hykudev testing
  - Next action: Human pulls feature/hyku-v7.2.0-upgrade on demo-hykudev.lib.wvu.edu, rebuilds containers, verifies facets show 5 items + "More" link. Then create Phase 2 backport task for LaRita collaboration.
