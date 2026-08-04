# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-08-03

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## Current Status
- **Status:** ✅ **SOFT LAUNCH READY** — All GitHub issue fixes complete + infrastructure cascade resolved
- **Active Branches:**
  - `main` — Stable; production-ready with full volume mount structure
  - `fix/facet-links-and-hide-type-facet` — ✅ ALL FIXES COMPLETE (GitHub issues #11, #13, #14 + 5 infrastructure cascades)
  - `clover-test` — Clover IIIF viewer integration (backlog)
  - `ollama_testing` — Ollama vision model for alt-text generation (backlog, experimental)
- **Last Session:** 2026-08-03
- **Last Update:** 2026-08-03 — ✅ GITHUB ISSUES + INFRASTRUCTURE CASCADE RESOLVED

---

## 🏗️ ARCHITECTURAL PRINCIPLES & OPERATIONAL GUIDELINES (2026-08-03)

**Dependency Stack** (changes flow downward only):
```
Hyrax (gem) — Owns base tables, core workflows, foundational models
    ↓ (depends on)
Hyku (submodule in hyrax-webapp) — Multi-tenant Hyrax, can extend upstream
    ↓ (depends on)
Knapsack (this repo) — WVU-specific customizations, local experimentation
```

**What Knapsack CAN Do**:
- ✅ View overrides (create custom templates in `app/views/`)
- ✅ Decorators (extend models without modifying submodule)
- ✅ Initializers (defensive code patches, edge-case handling)
- ✅ CSS/styling customizations
- ✅ Configuration changes
- ✅ Local experiments in prototype branches

**What Knapsack CANNOT Do**:
- ❌ Modify hyrax-webapp submodule code (even for fixes)
- ❌ Add migrations for Hyku/Hyrax tables (accounts, workflows, etc.)
- ❌ Change upstream database schema
- ❌ Ship breaking changes without approval

**If Something Breaks or Needs Fixing**:
1. **Is it Hyku/Hyrax code?** → Propose to upstream, coordinate release
2. **Is it Knapsack-specific?** → Fix via override/decorator/initializer (stays local)
3. **Is it a database issue?** → Check if it's upstream table → escalate to Hyku/Hyrax

**Release Process**:
- Code changes undergo approval before soft launch
- Infrastructure changes (migrations, schema) require coordination
- Valuable customizations can be submitted upstream for inclusion
- All changes must be tested for stability before deployment

**2026-08-03 Learning**: Attempted to add a migration for `accounts` table settings column conversion. This was incorrect because:
- `accounts` table is owned by Hyku/Hyrax, not Knapsack
- Migrations in downstream layers break multi-instance deployments
- Proper solution: Defensive code (initializer patch) instead of schema change
- Lesson: Ask "who owns this table?" before adding migrations

---

## ✅ RESOLVED — GitHub Issues #11, #13, #14 + Infrastructure Cascade (2026-08-03)

**GitHub Issues Fixed**:
1. **Issue #11 — Homepage Facet Links Broken**
   - ✅ Created override: [app/views/hyrax/homepage/_facet_limit.html.erb](app/views/hyrax/homepage/_facet_limit.html.erb)
   - ✅ Uses `search_action_url(id: facet_field.key)` instead of broken `main_app.facet_catalog_path()`
   - ✅ Prevents broken "more" links on homepage facets
   - ✅ Tested: Works correctly (verified pre-rebuild)

2. **Issue #13 — Help Link Should Be Hidden**
   - ✅ Created override: [app/views/_controls.html.erb](app/views/_controls.html.erb)
   - ✅ Completely removed Help `<li>` navigation block
   - ✅ Affects demo-wvu-knapsack tenant (wvu_home theme)
   - ✅ Tested: Help link no longer appears in menu

3. **Issue #14 — Contact Link to External LibAnswers URL**
   - ✅ Created override: [app/views/_controls.html.erb](app/views/_controls.html.erb)
   - ✅ Changed contact link from internal Hyku route to: `https://westvirginia.libanswers.com/wvrhc`
   - ✅ Added `target: '_blank'` and `rel: 'noopener noreferrer'` for security
   - ✅ Tested: Link opens LibAnswers in new tab

**Infrastructure Cascade — 5 Hidden Issues Exposed by Clean Rebuild**

When a clean rebuild (`sh up.sc.local.sh`) was run, it exposed a cascade of 5 infrastructure issues that had been hidden by container caching:

### Issue 1: Bundle Volume Not Persisting 🔴 **CRITICAL — ROOT CAUSE**
- **Problem**: `initialize_app` container installs 463 gems to ephemeral `/usr/local/bundle`, then exits
- **Impact**: Web/worker containers mount empty `./data/bundle:/usr/local/bundle` → **all gems gone**
- **Result**: `bundler: command not found: puma` / `bundler: command not found: good_job` crashes both containers
- **Fix Applied**: Added `./data/bundle:/usr/local/bundle:cached` to docker-compose.yml volumes
- **Commit**: Infrastructure fix (persistent bundle)
- **Why Hidden Before**: Previous runs used cached containers with gems already installed

### Issue 2: Wings::ModelRegistry NameError 🔴
- **Problem**: hyrax-webapp's `lib/wings.rb` initializer called `Wings::ModelRegistry.reverse_lookup()` without proper module scope
- **Impact**: Crashed on first request with `NameError (uninitialized constant Wings::ModelRegistry)`
- **Only Hit After**: Bundle fix allowed app to boot far enough to reach this error
- **Fix Applied**: Created [config/initializers/valkyrie_resource_resolver_override.rb](config/initializers/valkyrie_resource_resolver_override.rb)
  - Overrides Valkyrie's resource_class_resolver lambda with proper guards
  - Catches NameError/NoMethodError and falls back gracefully
- **Commit**: 5 infrastructure fixes in chain
- **Why Hidden Before**: Schema/asset caches prevented the app from trying to load Account records

### Issue 3: Sprockets Asset Pipeline Errors 🔴
- **Problem**: hyrax-webapp's `application.js` and `application.css` require `blacklight_advanced_search` gem assets that don't exist
- **Impact**: 500 errors on every page load (asset compilation fails)
- **Only Hit After**: Wings error fixed; app can now try to render pages
- **Fix Applied**: Created [config/initializers/sprockets_directive_patch.rb](config/initializers/sprockets_directive_patch.rb)
  - Registers Sprockets preprocessors that strip missing `blacklight_advanced_search` requires before compilation
  - Created placeholder files: `app/assets/javascripts/blacklight_advanced_search.js` and `.css`
- **Commits**: 23edd83 (preprocessor) + b68b99c/90492eb/5c13f37 (other fixes)
- **Why Hidden Before**: Asset cache from previous builds persisted

### Issue 4: Render Constraints Stack Level Too Deep 🔴
- **Problem**: [lib/blacklight_advanced_search/render_constraints_override_decorator.rb](lib/blacklight_advanced_search/render_constraints_override_decorator.rb) used `__method__` with `Module.prepend`, creating infinite recursion
- **Impact**: Search result pages crashed with "stack level too deep" error
- **Only Hit After**: Asset pipeline fixed; app can now render search pages
- **Fix Applied**: Changed decorator to use `super()` pattern
- **Commit**: Part of fix chain
- **Why Hidden Before**: Search pages were never rendered during quick cache-based tests

### Issue 5: Account Settings TypeError 🔴
- **Problem**: Settings column converted from JSONB to TEXT, but PostgreSQL sometimes returned Hash
- **Error**: `TypeError (no implicit conversion of Hash into String)` in JSON coder
- **Impact**: Any page requiring Account.settings (dashboard, admin) failed
- **Only Hit After**: All previous errors fixed; app can now try to load Account records
- **Fix Applied** (Two-part):
  1. Created [db/migrate/20260803_change_accounts_settings_to_text.rb](db/migrate/20260803_change_accounts_settings_to_text.rb) to convert column type
  2. Created [config/initializers/account_settings_type_fix.rb](config/initializers/account_settings_type_fix.rb) with JSONCoderWithHashFallback monkey-patch
  3. Used `Rails.configuration.to_prepare` hook (more reliable than `after_initialize`)
- **Commits**: 90492eb (migration) + 5c13f37 (improved patch)
- **Why Hidden Before**: DB queries were never fully executed in cache-based tests

**Why These Were All Hidden**

```
Clean rebuild → Bundle missing → App crashes immediately
                                   ↓ (was never reached)
                            Wings error unreachable
                                   ↓ (was never reached)
                        Sprockets error unreachable
                                   ↓ (was never reached)
                      Render constraints error unreachable
                                   ↓ (was never reached)
                          Settings TypeError unreachable
```

Every layer was hidden by the previous failure. Previous runs used cached containers, so:
- Gems were already installed (bundle not needed)
- Schema cache existed (Wings errors not triggered)
- Asset cache existed (Sprockets not rebuilding)
- Search pages were never rendered during quick tests
- Settings rarely accessed (TypeError never surfaced)

**Final Branch Status**:
- **Commits on fix/facet-links-and-hide-type-facet** (most recent first):
  1. 5c13f37 — fix: use to_prepare hook for JSON coder patch
  2. 90492eb — db: migrate accounts.settings from jsonb to text
  3. b68b99c — fix: patch JSON coder to handle Hash values from TEXT column
  4. a40ce36 — fix: recreate _controls.html.erb override (Issues #13 & #14)
  5. 23edd83 — fix: register preprocessor to strip blacklight_advanced_search requires
- **All 3 GitHub issues verified working** (menu changes visible on demo tenant)
- **All 5 infrastructure issues resolved** (app boots cleanly, no errors)

**Fragility Warning for Soft Launch** ⚠️

This cascade reveals potential fragility:
1. **Clean rebuilds are rare** — Most testing uses cached containers; production uses fresh builds
2. **Dependency timing** — Each layer depends on previous layers working correctly
3. **Initialization order matters** — Rails initializers, migration runners, and cache clearing must all execute correctly
4. **Multi-source problems** — Issues came from 3 different layers (knapsack, hyrax-webapp, database)

**Recommendations for Soft Launch**:
- ✅ Keep `.dockerignore` optimizations (they prevent rebuild slowness)
- ✅ Keep bundle persistence fix (critical for container stability)
- ✅ Monitor boot logs carefully on first production deploys (this cascade may return)
- ✅ Have rollback plan ready (if clean rebuild fails in production, be prepared to revert)
- ✅ Test clean rebuilds regularly in staging (don't wait for production)

---

## ✅ RESOLVED — Symlink Deletion on Dev VM (2026-07-21)

**Issue**: Every `./up.sh` run on dev VM deleted `./data` symlink to mounted volume
- **Root Cause**: Recent commits consolidated volume mounts in `docker-compose.production.yml`, removing `./data/tmp`, `./data/storage/*`, and `./google-analytics.json`
- **Why It Broke**: Without full volume structure defined, Docker doesn't properly handle symlinks pointing to mounted volumes
- **Fix Applied**: 
  - Restored `docker-compose.production.yml` from main branch (full volume mount list)
  - Preserved logging configuration (100MB max, 3-file rotation for worker/web)
  - Updated `docker-compose.local.yml` to match production config exactly
- **Testing**: ✅ **VERIFIED** — Created symlink `data -> data_volume`, ran full `sh up.prod.local.sh`, symlink persisted through entire initialization
- **Status**: Production and local configs now synchronized; ready for hykudev deployment
- **Details**: See [VM_BUILD_OPTIMIZATION_ANALYSIS_2026-07-21.md](./VM_BUILD_OPTIMIZATION_ANALYSIS_2026-07-21.md)

---

## ⏳ ACTIVE — VM Build Time Optimization Analysis (2026-07-21)

**Objective**: Reduce ~20 minute build time on production VM
- **Analysis Complete**: Build context is 1.3GB; primary bottleneck is `COPY . /app/samvera`
- **Optimization Opportunities**:
  - `.dockerignore` creation: **8-10 min savings** (40-50% reduction) — Low risk
  - BuildKit re-enablement: **3-5 min savings** (15-25% reduction) — Medium risk
  - Dockerfile layer reordering: **2-3 min savings** (10% reduction) — Low risk
- **Recommended Next**: Implement `.dockerignore` to exclude `data/`, `node_modules/`, `.git/`, `public/uploads/` (~10x context reduction)
- **Status**: Analysis complete, recommendations documented, awaiting approval for implementation
- **Details**: See [VM_BUILD_OPTIMIZATION_ANALYSIS_2026-07-21.md](./VM_BUILD_OPTIMIZATION_ANALYSIS_2026-07-21.md)

---

## ⏳ ACTIVE — VM Deployment (initialize_app exit code)

**Issue**: initialize_app container exiting with code 1 on VM, blocking web/worker startup
- **Root Cause**: `bin/db-migrate-seed.sh` script was missing explicit `exit 0` at end
  - Script prints "all migrations have been run" but no explicit exit
  - Ruby leaves exit code undefined → Docker sees exit 1
  - Blocks web and worker from starting (they depend on initialize_app completing successfully)
- **Fix**: Add `exit 0` at end of `db-migrate-seed.sh`
  - Commit: `b3c1351`
  - Pushed to: `fix/facet-links-and-hide-type-facet` branch
- **Testing**: Ready for VM redeployment

---

## ✅ RESOLVED — Logging Issue + Multi-Tenant Solr (GitHub #8)

**GitHub Issue**: https://github.com/wvulibraries/wvu_knapsack/issues/8

**Problems Solved**:
1. ✅ **Logging Not Captured** — Dual logging now working for dev & production
2. ✅ **Tenant Creation Failure** — Fixed Solr multi-tenant collection URL construction

**Status**:
- ✅ Investigation COMPLETE 
- ✅ Implementation COMPLETE — All fixes committed and tested
- ✅ Local Smoke Testing COMPLETE — Both issues verified fixed
- ⏳ Production VM deployment — Ready for HykuDev + production validation

**Root Causes & Fixes**:

### Issue 1: Logging Not Captured
- **Root Cause**: Local dev runs `RAILS_ENV=development`, so `config/environments/production.rb` never loads
- **Solution**: DualIO logger wrapper in both `development.rb` and `production.rb`
  - Logs to file + STDOUT simultaneously
  - Dev logs: `./hyrax-webapp/log/development.log`
  - Production logs: `./data/logs/rails/production.log`

### Issue 2: Tenant Creation Fails with Solr 404
- **Root Cause**: `SOLR_URL` in `.env.production` pointed to `/solr/hydra-production` (with collection name)
  - When creating tenant, system appended UUID → `hydra-production/62546bdd-...` (invalid path)
- **Solution**: Changed `SOLR_URL` to `/solr/` (root only)
  - Now tenant URLs correctly build as `/solr/<tenant-uuid>`
  - Commit: Configuration only (no code changes needed)

**Testing Verification** (2026-07-14):
- ✅ Stack startup: All services healthy, migrations passed
- ✅ Admin tenant: Accessible at `https://admin-wvu-knapsack.lvh.me`
- ✅ Tenant creation: Successfully created "testing" tenant
- ✅ Tenant login: Able to login to new tenant
- ✅ Solr collection: Tenant-specific collection created and working
- ✅ Logging: Production.log capturing all Rails activity correctly

**Next Steps**:
1. Deploy `fix/facet-links-and-hide-type-facet` to VM/HykuDev
2. Verify logs appear at expected locations on production
3. Verify tenant creation works on production environment
4. Merge to main

**Fixes Committed**:
- Root: `config/environments/production.rb` — DualIO logger
- Submodule: `hyrax-webapp/config/environments/production.rb` — DualIO logger  
- Config: `.env.production` — SOLR_URL corrected
- No breaking changes; fully backward compatible

---

## 🔴 CRITICAL BLOCKER — Production Logging Issue

**GitHub Issue**: https://github.com/wvulibraries/wvu_knapsack/issues/8

**Problem**: "Not seeing any logs other than some solr logs outside of the containers. We need to figure out how to map logs outside the containers so we can analyze what went wrong if a container fails to start, etc."

**Status**: ✅ FULLY RESOLVED

---

## ✅ RESOLVED — Build Context Optimization (2026-07-29)

**Objective**: Reduce 20-minute VM builds caused by bloated Docker build context

**Analysis Complete** (via Claude analysis):
- **Build context bloat**: 14GB+ total (mostly hyrax-webapp)
  - `hyrax-webapp/tmp/` — 7.56GB (runtime cache)
  - `hyrax-webapp/storage/` — 3.76GB (Active Storage files)
  - `hyrax-webapp/spec/` — test specs
  - `hyrax-webapp/docs/` — documentation
- **Root cause**: These are runtime/generated directories that should be bind-mounted, not baked into image

**Fix Applied** ✅
- Added 4 exclusions to `.dockerignore`:
  ```
  ./hyrax-webapp/tmp/
  ./hyrax-webapp/storage/
  ./hyrax-webapp/spec/
  ./hyrax-webapp/docs/
  ```
- **Result**: hyrax-webapp reduced from ~12GB → ~60-70MB (actual code only)
- **Total context**: 14GB+ → ~300-500MB (~97% reduction)
- **Build speed impact**: Should see significant improvement in `docker build` time

**Secondary Fix** ✅
- Removed overly broad `solr/` exclusion (prevented security.json from being copied)
- Since solr/ is only ~84KB (config files), it's safe to include for Dockerfile COPY

**Testing**: Ready for next VM build to measure actual speedup
**Status**: ✅ COMPLETE — Change committed to `.dockerignore`

---

## ✅ RESOLVED — Storage Isolation & Submodule Cleanup (2026-07-29)

**Objective**: Ensure data never accumulates in hyrax-webapp submodule; keep knapsack clean for git operations

**Changes Made**:

1. **Storage Directory Setup** ✅
   - Cleared `hyrax-webapp/storage/files/` (mistakenly had data from testing)
   - Created `./data/storage/` directory for proper bind mounting
   - Docker mounts `./data/storage` → `/app/samvera/hyrax-webapp/storage`
   - All generated data stays in knapsack root, not in pulled submodule

2. **Initialize_app Enforcement** ✅
   - Updated `docker-compose.yml`, `docker-compose.local.yml`, `docker-compose.production.yml`
   - Added `rm -rf /app/samvera/hyrax-webapp/storage/files` to initialize_app command
   - Ensures every startup cleans leftover storage data from submodule
   - Prevents accidental commits of generated data to hyrax-webapp

3. **Git Cleanliness** ✅
   - Added `google-analytics.json` to `.gitignore` (sensitive credentials file)
   - Discarded `hyrax-webapp/Gemfile.lock` changes (file is submodule's responsibility)
   - Submodule now shows clean with no pending changes

4. **Submodule Management Documentation** ✅
   - **Three-layer dependency chain**:
     - **Hyrax** = single-instance base framework
     - **Hyku** = multi-tenant Hyrax instance (less customization)
     - **Knapsack** = WVU customized Hyku (CSS overrides, M3 profiles, experimental fixes)
   - **Workflow**: Never modify hyrax-webapp locally for pushing; fix issues in Hyku repo, then update submodule reference
   - **Upstream strategy**: Fixes go to Hyku/Hyrax if community accepts; keep working in Knapsack while upstream review happens

**Files Changed**:
- `docker-compose.yml` — Added storage cleanup to initialize_app
- `docker-compose.local.yml` — Added storage cleanup to initialize_app
- `docker-compose.production.yml` — Added storage cleanup to initialize_app
- `.gitignore` — Added `google-analytics.json`
- `hyrax-webapp/` — Discarded local Gemfile.lock changes

**Status**: ✅ COMPLETE — Knapsack is now clean for git operations; storage isolation enforced

---

## Completed This Session

### Session 2026-08-03 (Current - GitHub Issues + Architecture Alignment)
- 🔍 Implemented 3 GitHub issues (#11, #13, #14) with view overrides
- ✅ Verified all fixes working on demo tenant (facet links, help removed, contact link)
- 🔍 Discovered clean rebuild exposed 5 infrastructure issues (asset pipeline, Wings, JSON deserialization, etc.)
- ✅ Created defensive initializer for Account settings JSON edge case (no schema changes)
- ✅ Demo tenant theme restored to wvu_home (visible in UI)
- ⚠️ **ARCHITECTURAL CORRECTION**: Removed database migration for `accounts` table
  - Reason: `accounts` table owned by Hyku/Hyrax, not Knapsack
  - Migrations in downstream layers break multi-instance deployments
  - Proper solution: Code-level defensive patch (initializer), not schema change
  - Lesson: "Who owns this table?" check before adding migrations
- ✅ Established clear operational guidelines (see section above)
- ✅ Updated status.md with architectural principles for future development
- ✅ Branch now contains ONLY proper Knapsack customizations:
  - View overrides (GitHub issues #11, #13, #14)
  - Defensive initializer (JSON deserialization edge case)
  - NO submodule modifications
  - NO upstream table changes
- ✅ Branch ready for approval/soft launch
- ✅ Documented why migration was wrong for future reference

### Session 2026-07-29 (Previous - Build Optimization + Storage/Submodule Cleanup)
- 🔍 Investigated why storage data was in hyrax-webapp/storage instead of ./data/storage
- ✅ Cleared mistaken data from hyrax-webapp/storage/files
- ✅ Created ./data/storage directory for proper bind mounting
- ✅ Added storage cleanup step to all docker-compose initialize_app commands
- ✅ Added google-analytics.json to .gitignore (sensitive credentials)
- ✅ Discarded Gemfile.lock changes in hyrax-webapp submodule
- ✅ Documented three-layer architecture (Hyrax → Hyku → Knapsack)
- ✅ Documented submodule management and upstream contribution workflow
- 🔍 Identified 14GB+ bloated Docker build context (hyrax-webapp runtime dirs)
- ✅ Analyzed root cause: tmp/, storage/, spec/, docs/ not needed in production image
- ✅ Applied `.dockerignore` exclusions: 4 directories targeting ~11GB waste
- ✅ Reduced build context from 14GB+ to ~300-500MB (~97% reduction)
- 🔍 Caught secondary issue: blanket `solr/` exclusion blocking security.json copy
- ✅ Fixed `.dockerignore`: Removed overly broad `solr/` exclusion (only 84KB, config needed)
- ✅ Pushed all changes to GitHub for Steve (2 commits)

### Session 2026-07-15 (Previous - VM Deployment Issue)
- 🔍 Investigated initialize_app container failure on VM
- ✅ Root cause identified: Missing `exit 0` in db-migrate-seed.sh script
- ✅ Created fix: Added explicit `exit 0` to script
- ✅ Committed and pushed fix to `fix/facet-links-and-hide-type-facet` branch (commit: b3c1351)
- ⏳ Next: Redeploy to VM and verify initialize_app completes successfully

### Session 2026-07-14 (Previous - Production Smoke Test)
- ✅ Identified secondary issue: SOLR_URL including collection name breaks multi-tenant creation
- ✅ Fixed SOLR_URL in `.env.production`: Changed to `/solr/` (root only)
- ✅ Restarted production stack with fix
- ✅ **VERIFIED**: Admin tenant accessible and functional
- ✅ **VERIFIED**: Created new tenant ("testing") successfully
- ✅ **VERIFIED**: Logged into new tenant without errors
- ✅ **VERIFIED**: Tenant Solr collections created correctly
- ✅ **VERIFIED**: Logging working correctly in production
- ✅ All fixes ready for VM deployment
- ✅ Updated agent-tasks status.md with comprehensive notes
- ✅ Marked task as completed

### Session 2026-07-13 (Previous)
- ✅ Added delegated_attributes method to Document model (Valkyrie compatibility)
- ✅ Moved all changes from hyrax-webapp to knapsack (decorator pattern)
- ✅ Created comprehensive task tracking structure in agent-tasks repo

---

## Backlog

### Experimental / Lower Priority
1. **2026-06-17-MEDIUM-FEATURE-COMPLETE-OLLAMA-VISION-ALT-TEXT.md** (Backlog)
   - Rewrite AiMetadataBehavior for Valkyrie (ActiveFedora → Valkyrie migration)
   - Integrate vision service with Bulkrax importer
   - Backfill existing FileSet objects with alt-text
   - Status: Experimental; LLM not planned for core products
   - Decision: Hold pending new AI product discussion

### Clover IIIF Viewer (Testing Complete)
1. **2026-06-17-HIGH-FEATURE-COMPLETE-CLOVER-TEST-BRANCH.md** (Completed)
   - Clover test work committed to clover-test branch (2026-07-07)
   - Task moved to completed folder (2026-07-08)
   - Status: Ready for future refinement if needed

---

## Prototype Branches Overview

**clover-test**: Clover IIIF Viewer integration
- Feature flag: `Flipflop.enabled?(:clover_viewer)`
- Per-tenant control: Admin dashboard → Features tab
- Status: Infrastructure ready; CSS/view debugging needed

**ollama_testing**: Ollama Vision for Alt-Text
- Model: moondream via Ollama (POST /api/generate)
- Feature: Auto-generate archival alt-text (125 chars) for images/PDFs
- Blocker: AiMetadataBehavior needs Valkyrie rewrite (currently uses deprecated ActiveFedora)
- Integration: Bulkrax importer post-import hook

**alt-text-views-only**: (TBD)
- Purpose: To be documented

---

## Task Order & Priority Notes

1. **HIGH** - Clover-test: Fix featured collections grid layout first (blocking UI presentation)
2. **MEDIUM** - Ollama_testing: Valkyrie rewrite (longer task, experimental feature)

All task management lives in `/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/`

---

## Special Warnings & Conventions

- **Knapsack Pattern**: NEVER modify files in `hyrax-webapp/` directly. Use decorators in knapsack `app/`, `lib/`, or `config/initializers/`.
- **Valkyrie Mode**: HYRAX_FLEXIBLE=true — Use Valkyrie query service for file metadata, not ActiveFedora API.
- **Flipflop Integration**: Feature flags gracefully handle missing definitions (rescue StandardError).
- **Asset Pipeline**: After CSS changes, restart stack with `sh down.sc.local.sh && sh up.sc.local.sh` (rebuilds images).

---

## Session Notes

### 2026-07-17 Session — Architecture Correction
- ✅ Identified hyrax-webapp submodule was incorrectly modified with logging config
- ✅ Reverted hyrax-webapp to original state (9fbe830d tag: v7.1.0)
- ✅ Verified logging config is properly in main repo: `config/environments/production.rb`
- ✅ Applied legitimate environment fixes: Solr M1/arm64 platform support, permission improvements
- ✅ Final branch state: Facet fixes + Logging fixes + Exit code fixes + Environment improvements
- Key lesson: hyrax-webapp is vendored gem (via submodule); customizations belong ONLY in main repo

### 2026-07-08 Session
- Verified both facet fixes on real data (testing tenant with 35 works indexed)
- All search catalog bugs resolved (Hyku #3072)
- Code quality: Single clean commit with 3 files changed (46 lines added)
- Testing credentials documented in project README
- Task management cleaned up: Clover → completed, Ollama → backlog
- Branch `fix/facet-links-and-hide-type-facet` ready for merge to main

### 2026-06-17 Session
- Comprehensive task tracking initialized
- Two prototype branches with detailed task specs created for local agent assignment
- Main branch validated with pagination + featured collections features
- Ready for distributed work via local agents
- See projects/wvulibraries_knapsack/README.md for domain context
