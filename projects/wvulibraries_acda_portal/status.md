# WVU Libraries ACDA Portal — Project Status & Task Tracking
**Last Updated:** 2026-07-15

---

## Project Overview
ACDA Portal — WVU Libraries institutional repository and content management system. Modernizing from Fedora 6 + ActiveFedora/RDF to PostgreSQL + ActiveRecord. Rails 7.0.8.6 on Ruby 3.3.5.

---

## Current Status
- **Status:** ⏳ Waiting for DevOps Approval — Dev VM Maintenance Window
- **Last Session:** 2026-07-17 (branch cleanup complete, good_job planning)
- **Blocker:** Waiting on DevOps to approve dev VM downtime for updates
- **Next Phase:** Once approved, sync dev VM to main, then sidekiq → good_job testing

---

## Immediate Work (This Week/Next)

### Phase 1a: Sidekiq → good_job Migration
**Goal:** Align with Samvera community standards (good_job is faster, simpler)

**Tasks:**
1. Update Gemfile:
   - Remove: `sidekiq`, `sidekiq-cron`, `sidekiq-failures`, `sidekiq-unique-jobs`, `redis`
   - Add: `good_job ~> 3.0`

2. Configure good_job:
   - Create `config/good_job.yml`
   - Update `config/application.rb` (`config.active_job.queue_adapter = :good_job`)
   - Update `config/sidekiq.yml` → remove or replace with good_job config

3. Update docker-compose:
   - Remove `redis` service (good_job doesn't need it)
   - Remove `workers` service (good_job runs in-process or separate pod, not separate container)
   - Simplify job configuration

4. Test:
   - Verify jobs execute
   - Check scheduler still works (cron tasks)
   - Monitor performance

5. Documentation:
   - Update README with good_job setup instructions
   - Document any behavior changes from sidekiq

**Timeline:** 2-3 days (straightforward gem swap)

### Phase 1b: PostgreSQL Migration (Deferred)
Will proceed after good_job is stable in production/staging.

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

### ✅ RESOLVED (2026-07-17)
- **Branch `fix/bundler-git-gem-caching` deleted** — was stale, had regressions
- Contained outdated code:
  - Removed blacklight gem version pins (undoing our recent fixes)
  - Added back deprecated `speedy-af` gem
  - Downgraded Fedora from 7 back to 6
  - Had no production value
- **Resolution**: Deleted both locally and from GitHub
- **Result**: `main` is now the unified, clean branch for all development

---

## Active Tasks

### Immediate (This Week)
1. **Verify dev VM works on main** — Pull latest, test application startup
2. **good_job Migration (Phase 1a)** — Replace sidekiq with good_job
   - Update Gemfile (remove sidekiq, redis; add good_job)
   - Configure good_job in Rails
   - Update docker-compose (remove redis, simplify workers)
   - Test and verify jobs work
3. **Clean up Sidekiq references** — Remove from config, docs, environment files

### Phase 1b: PostgreSQL Migration (Deferred)
- [ ] Create PostgreSQL schema (congressional_records table)
- [ ] Set up data export/import Rake tasks
- [ ] Prepare migration from Fedora
- Starts after good_job is proven stable

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
