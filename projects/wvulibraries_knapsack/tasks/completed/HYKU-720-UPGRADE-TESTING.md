---
status: active
priority: HIGH
type: upgrade-testing
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/HYKU-720-UPGRADE-TESTING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/HYKU-720-UPGRADE-TESTING.md \
         projects/wvulibraries_knapsack/tasks/active/HYKU-720-UPGRADE-TESTING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "HYKU-720-UPGRADE-TESTING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Local Hyku 7.2.0 Submodule Update & Validation Test
**Status**: BACKLOG
**Priority**: HIGH
**Type**: upgrade-testing
**Created**: 2026-10-12
**Last Updated**: 2026-10-12
**Scope**: Local Stack Car validation only — NO dev/prod deployment in this task
**Next step after approval**: Update `.gitmodules` branch reference, run Stack Car build + test locally

---

## Background & Context

### Current State
- **Submodule**: `hyrax-webapp/` is on `7.1-stable` branch (per `.gitmodules`)
- **Upstream available**: `7.2-stable` branch and `v7.2.0` tag confirmed at commit `1ccae4e6`
- **Local Stack Car**: Working correctly with Traefik wildcard HTTPS on `*.localhost.direct`

### Why This Test Matters (Scope: Local Validation Only)
Before upgrading the dev VM, we need to validate locally that:
1. Submodule update + full Docker rebuild completes without errors
2. IIIF functionality still works after `hyrax-iiif_av` gem is removed from Hyku core
3. Bulkrax 9.5.x doesn't break import/export workflows
4. All RSpec specs pass with the new base

**This task covers Stack Car local validation only.** Dev/prod deployment is a separate follow-up.

### Risk Assessment: LOW
- Rails already at 7.2.0 in `hyrax-webapp/Gemfile` — no gem version changes needed
- Decorator pattern isolates most knapsack customizations from upstream changes
- Easy rollback: revert `.gitmodules` to `7.1-stable` and rebuild

---

## What to Verify During Local Test

### 1. `hyrax-iiif_av` Gem Removed ⚠️
**Known impact**: 46 references across 15 files in `hyrax-webapp/`. Most are in Hyku's own code — the key question: does functionality move to Hyrax core?
- Watch for errors during rebuild at: `Gemfile` line 56, `config/routes.rb`, `config/initializers/hyrax.rb`
- Knapsack decorator to verify: `app/presenters/concerns/hyrax/iiif_av/displays_content_decorator.rb` loads without error

### 2. Bulkrax 9.5.x Compatibility
Knapsack uses Bulkrax ~> 9.3.0. Watch for import/export errors during local test.

### 3. HTTP Basic Auth Changes (if applicable)
Low priority — only matters if your dev/prod `.env` uses basic auth env vars.

### 4. New Features (evaluate later, not now)
Compound metadata, guided deposit wizard, URL redirects, new themes — note for future consideration, don't test yet.

---

## Test Steps (Local Only — Stack Car)

```bash
# STEP A — Update submodule branch reference from 7.1-stable to 7.2-stable
cd /Users/tam0013/Documents/git/wvu_knapsack
# Edit .gitmodules: change "branch = 7.1-stable" to "branch = 7.2-stable"

# STEP B — Update the submodule working tree
git submodule update --recursive
git log --oneline -1 hyrax-webapp  # verify we're at v7.2.0 / tip of 7.2-stable

# STEP C — Full rebuild via Stack Car (builds web + worker from scratch)
sh up.sc.local.sh

# STEP D — Watch initialization, wait for "Listening on https://..."
sc logs web -f

# STEP E — Run migrations inside container
sc exec bundle exec rails db:migrate

# STEP F — Precompile assets
sc exec bundle exec rake assets:precompile

# STEP G — Verify application accessible
open https://admin-wvu-knapsack.localhost.direct
```

---

## Testing Strategy

### Automated Tests
1. **RSpec Test Suite**: Run full suite locally, then on dev
   - Focus on specs referencing `Hyrax::IiifAv` (27+ test lines across 4 spec files)
   - Verify IIIF manifest presenter tests pass after gem removal
   
2. **Feature/Capability Tests** (manual):
   | Feature | How to Test | Expected Result |
   |---------|-------------|-----------------|
   | User Authentication | Sign in/out as admin, regular user, superadmin | No changes expected |
   | IIIF Viewer | Upload audio/video work, view on detail page | Media plays, thumbnails load |
   | M3 Metadata Profiles | Create/edit work with all M3 fields | All fields render correctly |
   | Search/Facets | Various search queries, facet drill-down | Same results as before 7.2.0 |
   | Bulkrax Import | Test CSV/JSON import workflows | Imports complete successfully |
   | Work Types | Create works of each type | Metadata forms render correctly |
   | Collections | Add/remove works from collections | Permissions propagate correctly |
   | File Upload | Upload single/multiple files | Derivatives generated, thumbnails created |

### Regression Testing Checklist
- [ ] Homepage loads without errors
- [ ] Admin dashboard accessible
- [ ] Tenant catalogs work (`*.localhost.direct` or prod URLs)
- [ ] IIIF viewer works for all media types (image, audio, video, PDF)
- [ ] Facets display "more" links correctly (verify prior fix still works)
- [ ] M3 metadata profiles render all fields
- [ ] Work decorators preserve WVU customizations
- [ ] Bulkrax import/export workflows functional
- [ ] Email notifications sent/received
- [ ] Search works across all work types
- [ ] File derivatives generated correctly (derivatives, thumbnails)
- [ ] Solr search returns expected results
- [ ] Fedora storage working (objects creatable/readable/updatable/deletable)

---

## Docker Image Rebuild Requirements

### Images to Rebuild
| Image | Base | Purpose | Location |
|-------|------|---------|----------|
| `ghcr.io/wvulibraries/wvu_knapsack/web` | Hyku 7.2.0 base | Web application | Built from `Dockerfile` |
| `ghcr.io/wvulibraries/wvu_knapsack/worker` | Same as web | Background jobs (GoodJob) | Built from same Dockerfile |
| `ghcr.io/wvulibraries/wvu_knapsack/solr` | solr:8.11.2 + custom config | Search indexing | Built with SolrCloud setup |

### Build Commands (local validation)
```bash
# From wvu_knapsack root
docker compose -f docker-compose.production.yml build web worker solr

# Push to GHCR after validation
docker push ghcr.io/wvulibraries/wvu_knapsack/web:7.2.0
docker push ghcr.io/wvulibraries/wvu_knapsack/worker:7.2.0
docker push ghcr.io/wvulibraries/wvu_knapsack/solr:7.2.0
```

---

## Acceptance Criteria

### Technical Completion (Local)
- [ ] `hyrax-webapp` submodule updated to 7.2-stable tip (verified via `git log --oneline -1 hyrax-webapp`)
- [ ] `.gitmodules` branch reference updated from `7.1-stable` to `7.2-stable`
- [ ] Stack Car full rebuild completes without bundler/Docker errors (`sh up.sc.local.sh`)
- [ ] Database migrations complete without errors (`sc exec bundle exec rails db:migrate`)
- [ ] Assets precompile without warnings/errors (`sc exec bundle exec rake assets:precompile`)
- [ ] Full RSpec test suite passes locally (`sc exec bundle exec rspec`)
- [ ] Admin panel accessible at `https://admin-wvu-knapsack.localhost.direct`

### Functional Validation (Local)
- [ ] Application loads without errors on HTTPS localhost URL
- [ ] IIIF viewer works for audio/video (media plays, thumbnails load)
- [ ] Bulkrax import/export workflows tested and functional locally
- [ ] No `NameError` or `NoMethodError` in logs related to knapsack decorators

### Documentation Updated
- [ ] `status.md` updated with local test result date

---

## Architecture Gotchas (Knapsack-Specific)

### 1. IIIF Functionality Deeply Integrated
The `hyrax-iiif_av` gem is NOT a simple dependency — it's deeply integrated:
- Controllers: `Hyrax::IiifAv::ControllerBehavior` in `works_controller_behavior.rb`
- Presenters: `Hyrax::IiifAv::DisplaysContent`, `DisplaysIiifAv` mixins
- Initializers: Configuration in `config/initializers/hyrax.rb` line 277-285
- Routes: Engine mount at `config/routes.rb` line 14
- Specs: 4 spec files with IIIF-specific test logic

**Verification Required**: After removing the gem, all these integrations must still function via Hyrax core. Check that:
- `app/presenters/concerns/hyrax/iiif_av/displays_content_decorator.rb` (knapsack decorator) is compatible with new Hyrax core behavior
- `app/controllers/concerns/hyku/works_controller_behavior.rb` includes correct IIIF controller concern

### 2. Knapsack Decorators Preserve Customizations
Knapsack's `*_decorator.rb` files prepend modules to override Hyku/Hyrax behavior. Since these use the decorator pattern (Rails autoload + `to_prepare` blocks), they should mostly survive the upgrade unless:
- Parent class method signatures changed
- Module names/namespace paths changed in Hyrax core

**Action**: After upgrade, verify all knapsack decorators load without `NameError` or `NoMethodError`.

### 3. Gemfile.lock Must Be Regenerated
After updating the submodule and removing/updating gem references:
```bash
cd hyrax-webapp
bundle install
git add Gemfile Gemfile.lock
```

### 4. M3 Metadata Profiles Are Runtime-Configured
The flexible M3 (Marc Metadata Profiles) system means static configuration solutions won't work. Any migration that affects metadata field definitions must be validated against each active M3 profile in production, not just dev/test.

### 5. SolrCloud Configuration May Need Updates
SolrCore configuration files in `solr/` may reference schema changes. Verify:
- M3 facet fields still indexed correctly
- New Hyku 7.2.0 solr fields (if any) added to schema
- Collection configurations compatible with upgraded Solr

---

## Blocked By / Dependencies

### Blocks: Production Deployment
This upgrade must complete before any production deployment. All other tasks are secondary until this is validated.

### Dependencies:
1. **HTTPS Termination Task**: Should be completed first — new environment needs HTTPS-capable smoke testing for IIIF validation
2. **Upstream Hyku 7.2.0 Release Notes**: Final release notes should be reviewed for any last-minute changes
3. **Bulkrax 9.5 Migration Guide**: Check Bulkrax changelog for breaking API changes between 9.3 and 9.5
4. **Hyrax Core IIIF Changes**: Confirm `hyrax-iiif_av` functionality mapping to Hyrax core

### Prerequisites:
1. Access to dev server (`demo-hykudev.lib.wvu.edu`) SSH credentials
2. Access to prod server (`digitalhistory.lib.wvu.edu`) SSH credentials (Phase 3 only)
3. Production database backup strategy confirmed
4. Maintenance window scheduled for production deployment

---

## Rollback Plan

If upgrade fails or introduces blocking issues:

```bash
# On affected server
cd /var/www/wvu_knapsack

# 1. Revert submodule to previous version
git checkout 7.1-stable hyrax-webapp
git submodule update --recursive

# 2. Restore previous Docker images (tagged before upgrade)
docker compose -f docker-compose.production.yml pull wvu-knapsack/web:7.1-stable
docker compose -f docker-compose.production.yml pull wvu-knapsack/worker:7.1-stable

# 3. Restart with previous version
docker compose -f docker-compose.production.yml up -d web worker

# 4. Verify rollback successful
curl -I https://<tenant>-hyku.lib.wvu.edu
```

**Database migrations are additive** — they do not need to be rolled back since:
- New tables don't break old code
- New columns have defaults or allow NULL
- Data is preserved in reverse direction

---

## Implementation Notes for Agent

### Key Files to Modify
1. `/Users/tam0013/Documents/git/wvu_knapsack/.gitmodules` — Update submodule branch reference
2. `/Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp/Gemfile` — Remove `hyrax-iiif_av` gem line
3. `/Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp/Gemfile.lock` — Regenerate after bundle install
4. Knapsack decorator files in `app/` and `lib/` that reference `Hyrax::IiifAv` — Update module paths if needed

### Commands to Run (local validation)
```bash
# Update submodule
git submodule update --remote hyrax-webapp

# Regenerate Gemfile.lock
cd hyrax-webapp && bundle install && cd ..

# Rebuild Docker images
docker compose -f docker-compose.production.yml build web worker solr

# Test locally with Stack Car
sh up.sc.local.sh  # full rebuild
sc exec bundle exec rails db:migrate
sc exec bundle exec rspec  # run specs
```

### Verification Points
1. **Gemfile**: Confirm `hyrax-iiif_av` line removed, no bundler errors on install
2. **Initializers**: Check all `Hyrax::IiifAv` references resolved to Hyrax core
3. **Controllers/Presenters**: Verify decorator pre-pends work with new module paths
4. **RSpec**: All specs pass, especially IIIF-related specs
5. **Docker**: Images build without errors
6. **Smoke Test**: Application starts and responds on localhost:3000 (HTTP) and https://*.localhost.direct (HTTPS)

---

## Estimated Complexity: MODERATE

- **Low Risk Factors**: Minor version bump, Rails unchanged, decorator pattern isolation
- **Moderate Risk Factors**: IIIF gem removal requires careful migration, Bulkrax API changes
- **High Risk Factors**: Database migrations, production deployment timing
- **Estimated Effort**: 2-3 days for validation (local + dev), 0.5 day for production deployment
