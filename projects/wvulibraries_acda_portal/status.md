# WVU Libraries ACDA Portal — Project Status & Task Tracking
**Last Updated:** 2026-07-15

---

## Project Overview
ACDA Portal — WVU Libraries institutional repository and content management system. Modernizing from Fedora 6 + ActiveFedora/RDF to PostgreSQL + ActiveRecord. Rails 7.0.8.6 on Ruby 3.3.5.

---

## Current Status
- **Status:** ✅ Application Running Locally — Dependency Cleanup Phase
- **Last Session:** 2026-07-15 (gem fixes + docker-compose optimization)
- **Next Phase:** Branch alignment + Modernization execution (PostgreSQL migration)

---

## Today's Work (2026-07-15)

### ✅ Completed
1. **Fixed gem version constraints** (blocking application startup):
   - `blacklight_advanced_search` changed from `~> 7.4` (broken) to `~> 7.0` 
     - Root cause: `~> 7.4` means ">= 7.4 and < 8.0" but only 7.0.0 exists in 7.x series
     - Fix: Changed to `~> 7.0` to match 7.0.x releases
   
   - `blacklight_range_limit` pinned to `~> 8.0`
     - Root cause: Version 9.x renamed helper module from `RangeLimitHelper` to `BlacklightRangeLimit::RangeLimitHelper`
     - Fix: Pinned to `~> 8.0` to avoid breaking changes in 9.x

2. **Fixed docker-compose race condition**:
   - Removed base `app` service from auto-startup (set `profiles: ['build-only']`)
   - Moved `bundle install` to `web` service only (runs once at startup)
   - `workers` service now waits for `web` to start, skips duplicate `bundle install`
   - Eliminates gem install race condition when both containers run `bundle install` simultaneously

3. **Cleaned up temporary artifacts**:
   - Added `rm -rf ./hydra/tmp` to cleanup-dev.sh to handle corrupted Rails asset cache

4. **Fedora 6 → 7 Upgrade** ✅ **COMPLETE**:
   - ✅ Drop-in replacement done (docker-compose using `fcrepo/fcrepo:7-tomcat10`)
   - ✅ Tested locally, verified working
   - 🔄 **Pending:** Staging environment validation before production deployment
   - Note: Fedora 7 resolves Tomcat 9 security vulnerability

5. **Application Status**:
   - ✅ Application running locally at localhost:3000
   - ✅ Landing page rendering correctly
   - ✅ Gem dependencies resolved and installed
   - ✅ Containers orchestrated correctly (no stray sleeper containers)
   - ✅ Using Fedora 7 (Tomcat 10)

---

## Critical Issue: Branch Misalignment

### Problem
- **Local branch** (`main` or current working branch): Has today's fixes (Gemfile updated, docker-compose optimized)
- **Dev VM branch**: Different branch, cannot auto-merge
- **Blocker**: Cannot proceed with modernization Phase 1 setup until branches are aligned

### Action Required
1. **Audit branches**: Determine what's on dev VM vs. local
2. **Reconcile differences**: 
   - Pull all fixes into unified branch
   - OR rebase dev VM branch onto main with today's fixes
3. **Test unified branch**: Verify application runs on dev VM with aligned code
4. **Then proceed**: With modernization Phase 1 tasks

### Risks if Not Fixed
- Dev VM tests use old Gemfile (might fail or give false results)
- Modernization work bifurcates (different code paths)
- Merge conflicts later (expensive to resolve)

---

## Active Tasks
### Immediate (This Week)
1. **Branch Alignment** — Reconcile dev VM branch with main
2. **Fedora 7 Staging Validation** — Drop-in replacement done locally, needs staging environment testing (before production)
3. **Remove speedyAF gem** — Unsupported, tested locally, safe to remove

### Phase 1: Modernization Setup (1-2 weeks, starts after branch alignment)
- [ ] Create PostgreSQL schema (congressional_records table)
- [ ] Set up good_job configuration
- [ ] Prepare data export/import Rake tasks
- [ ] Update docker-compose for PG (remove Fedora, redis)

### Phase 2: Data Migration (2-3 weeks)
- [ ] Export records from Fedora
- [ ] Import files to ActiveStorage
- [ ] Reindex Solr
- [ ] Verify data integrity

### Phase 3: Model Refactoring (2-3 weeks)
- [ ] Create CongressionalRecord AR model
- [ ] Simplify indexing
- [ ] Update controllers/views/jobs

### Phases 4-6: Jobs → Testing → Cutover (1-2 weeks each)

---

## Backlog
- See MODERNIZATION.md for full 8-12 week detailed plan
- Phases 4-6: Job migration, comprehensive testing, production cutover
- Rails 8 upgrade: Deferred to Phase B (separate project, after PG migration stable)

---

## Task Order & Priority Notes
- All task management lives in `/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/tasks/`
- Follow agent workflow rules as documented in agent-tasks repo
- **BLOCKING ISSUE:** Branch alignment must be resolved before Phase 1 begins

---

## Special Warnings & Conventions
- Follow all agent workflow and commit protocols as documented in projects/wvulibraries_acda_portal/README.md
- **Branch Alignment Needed:** Dev VM on different branch; must reconcile before proceeding
- Keep Gemfile.lock and Gemfile changes synced across all branches

---

## Session Notes (2026-07-15)
1. Application successfully running locally after gem fixes
2. docker-compose orchestration cleaned up and optimized
3. Branch misalignment identified as critical blocker
4. Next session should focus on:
   - Resolving branch misalignment
   - Validating Fedora 6 → 7 upgrade on staging
   - Beginning Phase 1 setup once branches aligned
5. See MODERNIZATION.md for complete modernization plan (8-12 weeks)
6. See this document for immediate action items (this week)
