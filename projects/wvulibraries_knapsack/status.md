# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-07-09

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## Current Status
- **Status:** 🔴 **CRITICAL PRODUCTION ISSUE** — Logging not captured outside containers
- **Active Branches:**
  - `main` — Stable; pagination + featured collections features deployed
  - `fix/facet-links-and-hide-type-facet` — ✅ COMPLETED (ready for production); logging fixes pending
  - `clover-test` — Clover IIIF viewer integration (completed testing, committed 2026-07-07)
  - `ollama_testing` — Ollama vision model for alt-text generation (backlog, experimental)
  - `alt-text-views-only` — (TBD)
- **Last Session:** 2026-07-09
- **Last Update:** 2026-07-09 — CRITICAL: Production logging blocker created (GitHub issue #8)

---

## 🔴 CRITICAL BLOCKER — Production Logging Issue

**GitHub Issue**: https://github.com/wvulibraries/wvu_knapsack/issues/8

**Problem**: "Not seeing any logs other than some solr logs outside of the containers. We need to figure out how to map logs outside the containers so we can analyze what went wrong if a container fails to start, etc."

**Impact**:
- Cannot debug production failures
- No diagnostics when containers fail to start
- Blocks production operations

**Status**:
- ✅ Investigation COMPLETE — Root cause identified (synthesis report done)
- ⏳ Implementation ACTIVE — Qwen implementing dual logging fix
- ⏳ Testing pending — HykuDev validation
- ⏳ Deployment pending — Production rollout

**Active Task**: `2026-07-09-CRITICAL-BUGFIX-PRODUCTION-LOGGING-NOT-CAPTURED.md`
- Assigned to: Qwen (Implementation Agent)
- Synthesis report: `/summaries/2026-07-09-SYNTHESIS-LOGGING-HYKUDEV-INVESTIGATION.md`
- Implementation task: Dual logging (STDOUT + file) in production.rb + Docker config

---

## Active Tasks

### 🔴 CRITICAL — Production Logging Fix
**Task**: `2026-07-09-CRITICAL-BUGFIX-PRODUCTION-LOGGING-NOT-CAPTURED.md`
- Root cause: `RAILS_LOG_TO_STDOUT=true` sends logs only to STDOUT, bypassing volume mount
- Solution Status: ✅ IMPLEMENTED
  - ✅ Fix 1: RAILS_LOG_TO_STDOUT correctly set on production
  - ✅ Fix 2: ./data/logs/rails/ directory has correct permissions
  - ✅ Fix 3: Dual logging implemented (broadcast to file + STDOUT)
  - ✅ Fix 4: Docker log rotation configured (100m per file, keep 3)
- Branch: `fix/facet-links-and-hide-type-facet`
- Commit: `7426102`
- Status: ✅ READY FOR PRODUCTION DEPLOYMENT
- Next: Deploy to HykuDev, then production; verify logs appear in `/data/logs/rails/production.log`

---

## Completed This Session

### Session 2026-07-09 (Current)
- ✅ CRITICAL: DevOps opened GitHub issue #8 for production logging
- ✅ Investigated logging configuration across all environments
- ✅ Root cause identified: STDOUT overrides file-based logging
- ✅ Created synthesis report with detailed findings and 4 proposed fixes
- ✅ Moved investigation task to completed
- ✅ Created CRITICAL implementation task for Qwen
- ✅ Fix 1: Verified RAILS_LOG_TO_STDOUT=true set correctly
- ✅ Fix 2: Verified ./data/logs/rails/ permissions correct on production
- ✅ Fix 3: Implemented dual logging in config/environments/production.rb
- ✅ Fix 4: Added Docker log rotation to docker-compose.production.yml
- ✅ Syntax validation: production.rb and docker-compose.production.yml both valid
- ✅ Changes committed and pushed to fix/facet-links-and-hide-type-facet branch

### Session 2026-07-08 (Previous)
- ✅ Tested facet fixes on testing tenant with real data (35 works)
- ✅ Verified Fix #1: Type facet hidden from search sidebar (`generic_type_sim` deleted)
- ✅ Verified Fix #2: Facet links use correct format (`f[creator_sim][]` URLs)
- ✅ Created initializer to load helper override (`hyrax_helper_decorator.rb`)
- ✅ Combined commits into single clean commit: "fix: Hide Type facet and fix facet links with helper override (Hyku #3072)"
- ✅ Updated project README with testing tenant credentials (samvera/hyku)
- ✅ Moved Ollama task to backlog (experimental, not prioritized)
- ✅ Moved Clover test to completed (testing work committed 2026-07-07)

### Session 2026-06-17 (Previous)
- ✅ Fixed pagination settings in catalog (per_page=[6,12,24,48,96], default=12)
- ✅ Increased featured collections limit (6 → 15)
- ✅ Resolved clover_viewer feature flag error handling (Valkyrie mode)
- ✅ Fixed Wings::ModelRegistry error in flexible mode
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
