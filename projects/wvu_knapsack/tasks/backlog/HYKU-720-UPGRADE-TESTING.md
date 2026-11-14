---
status: backlog
priority: HIGH
type: upgrade-testing
system_domain: SEARCH | DISPLAY
mvp_alignment: BLOCKER
local_worker_safe: true
---

## 📋 Task Revision (2026-10-27)

**Revised for**: Hyku 7.2.1 testing with upstream PR #3369 + Hyrax #7678 integration
**Branch**: `feature/hyku-v7.2.1-upgrade` (new — split from `feature/hyku-v7.2.0-upgrade`)
**Scope**: Remove knapsack's facet-workaround code, verify upstream solution works on clean baseline
**Not in scope**: Dev/prod deployment, IIIF/Bulkrax testing, date range work (separate task)
**Prerequisite**: Both Hyku #3369 + Hyrax #7678 must be merged before testing can proceed

---

# TASK: Hyku 7.2.1 Upgrade — Validate Upstream Facet Limiting Fix
**Status**: BACKLOG
**Priority**: HIGH
**Type**: upgrade-testing
**Created**: 2026-10-12 (original) | **Revised**: 2026-10-27
**Scope**: Local Stack Car validation on `feature/hyku-v7.2.1-upgrade` branch
**Branch**: `feature/hyku-v7.2.1-upgrade` (NEW — split from `feature/hyku-v7.2.0-upgrade`; old branch preserved)
**Next step after approval**: Remove facet-workaround code → verify upstream PRs merged → pin hyrax-webapp → rebuild → test

---

## Background & Context

### Current State
- **Submodule**: `hyrax-webapp/` on `7.2-stable` branch ✅ (already updated)
- **Current target**: Hyku 7.2.1 (released 2026-10-27) — CRITICAL security fix for multi-tenant GA4 data leakage
- **Upstream PRs addressing facet limiting**:
  - Hyku #3369: Introduced `config.default_facet_limit = 5`, used everywhere catalog controller defines facets
  - Hyrax #7678: Added `limit: true` on M3 profile facets to honor the default limit
- **Knapsack's workaround**: Four-file implementation (CatalogSearchBuilder + YAML config + decorator initializer) that injects `f.<field>.facet.limit` params at query time

### 🔐 SECURITY UPDATE (2026-10-27): Hyku 7.2.1 Released

**Critical Security Fix**: Multi-tenant GA4 data leakage vulnerability fixed in 7.2.1
- **Advisory**: https://github.com/samvera/hyku/security/advisories/GHSA-r6x6-wxm8-6994
- **Issue**: Hyku 7.2.0 with Google Analytics 4 could send one tenant's page views to another tenant's GA property
- **Resolution**: Upgrade to 7.2.1 recommended (CRITICAL for WVU's multi-tenant deployment)
- **Release**: https://github.com/samvera/hyku/releases/tag/v7.2.1
- **Credit**: Nick Steinwachs and Nic Don Stanton-Roark (Samvera community)

### Why This Task Is Different from HYKU-720 Original

The original task focused on "update submodule + rebuild." That's now done. This task addresses the **critical testing constraint**: we cannot validate upstream PR #3369 + Hyrax #7678 while our workaround code is in place — it masks whether the upstream fix actually works.

**Testing Strategy**: Remove workaround → pin hyrax-webapp to commit with both upstream PRs → rebuild → verify facets default to 5 (not 10)

### Current Behavior (Before Fix)
- **Static/non-flexible facets**: Show limit=5 ✅ (correct via Blacklight/default config)
- **Flexible facets (M3 profile-driven)**: Default to Blacklight's 10 ❌ — on BOTH homepage AND catalog view
- This is the exact problem upstream PRs #3369 + Hyrax #7678 solve

---

## Implementation Steps

### STEP 1 — Remove knapsack's facet-workaround code

Remove these 4 files and their references. This is REQUIRED before testing upstream (our code masks whether the fix works):

| File | Action | Commit Message |
|------|--------|---------------|
| `app/search_builders/catalog_search_builder.rb` | **DELETE** (git rm) | Remove CatalogSearchBuilder class |
| `config/initializers/catalog_search_builder.rb` | **DELETE** (git rm) | Remove catalog_search_builder initializer |
| `config/wvu_facet_defaults.yml` | **DELETE** (git rm) | Remove wvu_facet_defaults config |
| `config/initializers/catalog_controller_decorator.rb` lines ~95-120 | **REMOVE**: delete the `yaml_path`, `force_registered_fields`, and force-registration block that imports from wvu_facet_defaults.yml | Remove force_registered_fields logic from decorator |

**Before removing**, verify these files don't have other critical logic beyond facet limiting. If they serve other purposes, extract only the facet-workaround portions and keep the rest.

**Rollback**: `git checkout feature/hyku-v7.2.0-upgrade -- app/search_builders/catalog_search_builder.rb config/initializers/catalog_search_builder.rb config/wvu_facet_defaults.yml` (restores files from old branch).

### STEP 2 — Verify upstream PRs are merged

Check both PRs on GitHub for merge status:
```bash
# Hyku #3369 — should show "Merged" with `config.default_facet_limit = 5`
# Hyrax #7678 — should show "Merged" with `limit: true` on M3 facets

git ls-remote origin refs/pull/3369/head  # if empty, PR not merged
git ls-remote origin refs/pull/7678/head  # if empty, PR not merged
```

If **both merged**: proceed to STEP 3.
If **either NOT merged**: block implementation; wait for merge or use branch tip as temporary source. Document which PR is blocking.

### STEP 3 — Pin hyrax-webapp to correct commit

Check what the `7.2-stable` branch currently resolves to:
```bash
git log --oneline hyrax-webapp -1  # current pinned commit
```

Verify this commit includes both upstream PRs. If it doesn't (PRs merged after current pin):
```bash
git submodule update --remote hyrax-webapp
git log --oneline hyrax-webapp -3  # verify new tip includes both PRs
```

### STEP 4 — Rebuild \u0026 Test on Clean Baseline

```bash
# Full rebuild (no cached layers)
docker compose down --remove-orphans && sleep 5 && sh up.sc.local.sh

# Watch logs through boot
sc logs web -f

# Run migrations + assets
sc exec bundle exec rails db:migrate
sc exec bundle exec rake assets:precompile
```

### STEP 5 — Verify Facet Limiting Works (Core Test)

**Critical test**: With workaround REMOVED and upstream PRs in place, all facets should show limit=5:

| Location | Test | Expected |
|----------|------|----------|
| **Homepage** — Flexible facets (People Represented, Location) | Check facet list item count | Exactly 5 items + "More" link |
| **Catalog view** — Flexible facets | Check facet list item count | Exactly 5 items + "More" link |
| **Homepage** — Static/non-flexible facets | Check unchanged behavior | Still exactly 5 items ✅ |
| **Catalog view** — Static/non-flexible facets | Check unchanged behavior | Still exactly 5 items ✅ |

If any flexible facet shows 10 → upstream PR not effective or hyrax-webapp doesn't include merged PRs. Block deployment.
If all show 5 → upstream fix verified. Proceed to regression testing below.

---

## What to Test (Post-Facet Verification)

Only run these AFTER Step 5 confirms facets work correctly. Consolidated from prior task sections — cleaned for 7.2.1 scope:

### Manual Regression Tests
| Feature | How to Test | Expected Result |
|---------|-------------|-----------------|
| Homepage loads | Open https://admin-wvu-knapsack.localhost.direct | No errors |
| Admin dashboard | Sign in as admin | Accessible, no NameError |
| SAML authentication | Sign in via SAML (if enabled) | Works (saml_phase_patch.rb active) |
| Search results | Various search queries | Results display correctly |
| Facet drill-down | Click facet values | Drill-in works, limit applied |
| IIIF viewer | Upload audio/video work, view on detail page | Media plays, thumbnails load |
| Bulkrax import | Test CSV/JSON import workflows | Imports complete successfully |
| M3 profiles | Create/edit work with all M3 fields | All fields render correctly |
| Collections | Add/remove works from collections | Permissions propagate correctly |
| Work Types | Create works of each type | Metadata forms render correctly |
| File Upload | Upload single/multiple files | Derivatives generated, thumbnails created |

### Automated Tests
Run `sc exec bundle exec rspec spec/` after Step 5 pass. Focus areas:
- IIIF manifest presenter specs (prior hyrax-iiif_av removal)
- SAML auth flow specs (verify saml_phase_patch.rb doesn't break anything)
- M3 metadata profile specs

### What to NOT test yet (defer to follow-up tasks)
- Compound metadata features
- Guided deposit wizard
- URL redirect changes
- New theme rendering
- Date range limiting (Praneeth's separate work on Collections vs Works)
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

## ⚠️ CRITICAL UPDATE: Facet Limiting Work (Upstream PRs)

**As of 2026-10-27**: LaRita Vega (Hyku maintainer) has submitted upstream PRs addressing facet limiting on Hyku 7.2:

1. **Hyrax PR #7678** - Facet Limiting via M3 Profiles
   - Adds `limit: true` configuration to honor default facet limits for M3 profile facets
   - Status: See https://github.com/samvera/hyrax/pull/7678

2. **Hyku PR #3369** - Default Facet Limit Configuration
   - Introduces `config.default_facet_limit = 5` to override Blacklight's default of 10
   - Applied to all facets defined in CatalogController
   - Status: See https://github.com/samvera/hyku/pull/3369

**Knapsack Status**:
- ✅ Current feature branch has workarounds (CatalogSearchBuilder, YAML config) to enforce limit: 5
- ❓ When upstream PRs are merged into hyrax-webapp 7.2-stable, these workarounds may be superseded
- **ACTION**: After merging Praneeth's date range work, verify whether upstream PRs are included in pinned hyrax-webapp commit
  - If YES: Consider removing workaround code in favor of upstream solution
  - If NO: Keep workarounds as temporary patches (update comments to reference upstream PR status)

**Testing During 7.2 Validation**:
- Verify facets display with limit: 5 (before "more »" link shows)
- Verify no conflicts between workaround code and upstream configuration
- Document whether upstream PRs solve the issue completely

**Praneeth's Date Range Work** (blocking):
- Currently merging date range limiting logic to feature branch
- Issue: homepage searches Works only (1885–2014), but catalog searches Works + Collections (1775–2024 from archival spans)
- Awaiting merge completion before final 7.2 validation

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
