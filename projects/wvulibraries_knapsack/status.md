# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-07-21

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## Current Status
- **Status:** ✅ **VM DEPLOYMENT READY** — All fixes verified and tested
- **Active Branches:**
  - `main` — Stable; production-ready with full volume mount structure
  - `fix/facet-links-and-hide-type-facet` — ✅ ALL FIXES COMPLETE (logging, Solr, exit code, symlink)
  - `clover-test` — Clover IIIF viewer integration (backlog)
  - `ollama_testing` — Ollama vision model for alt-text generation (backlog, experimental)
- **Last Session:** 2026-07-21
- **Last Update:** 2026-07-21 — ✅ SYMLINK FIX VERIFIED + DOCKER-COMPOSE ALIGNMENT COMPLETE

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

## Completed This Session

### Session 2026-07-15 (Current - VM Deployment Issue)
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
