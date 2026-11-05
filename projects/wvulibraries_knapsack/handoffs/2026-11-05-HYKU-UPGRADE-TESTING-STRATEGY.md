# Hyku Upgrade Testing Strategy & Learnings
**Date:** 2026-11-05  
**Status:** Testing framework clarified; gem version management critical path identified  
**Next Session:** After hykudev snapshot complete

---

## Quick Summary

Testing Hyku version upgrades (7.2.0, 7.3, etc.) requires:
1. **Gem version pinning** (Gemfile.lock or version constraints)
2. **Local baseline testing** (Stack Car) before VM troubleshooting
3. **Collection isolation** (Solr architecture understanding)
4. **Fresh snapshots** between major test cycles

---

## Lesson 1: Gem Version Drift Blocks Everything

### The Problem
- **Cause**: Gemfile.lock not committed to repo
- **Effect**: Different build times resolve gems independently → incompatible versions
- **Example**: Dev built `blacklight_advanced_search 8.1.0`, prod had `7.0.0`
  - v8.x removed `RenderConstraintsOverride` constant
  - Hyrax v7.1.3 controller still references it (class load time)
  - Result: NameError crashes web container before any code runs

### The Fix
```ruby
# Option A: Commit Gemfile.lock (locks all versions)
git add Gemfile.lock && git commit -m "lock: Pin gem versions for reproducible builds"

# Option B: Pin critical gems in Gemfile (after submodule)
eval_gemfile 'hyrax-webapp/Gemfile'
gem 'blacklight', '~> 7.41.0'
gem 'blacklight_advanced_search', '~> 7.0.0'
```

### Why This Matters
- **Blocking**: Gem resolution happens at container build time, before any Rails code runs
- **Silent failure**: Different gems = different constants/methods available
- **Hard to diagnose**: Looks like a code bug when it's a dependency mismatch
- **Multi-env issue**: Prod, dev, hykudev can all have different gem versions

### Testing Gem Changes
- Always test locally with Stack Car after updating Gemfile
- Run `bundle install` to verify resolution
- Check for removed constants/methods in gem change logs

---

## Lesson 2: Debug Locally FIRST, Then VM

### The Pattern That Failed
1. ❌ Upgrade hykudev to new version
2. ❌ See error, try to fix on hykudev VM
3. ❌ Spend hours debugging in production environment
4. ❌ Cannot easily revert, cannot compare with baseline

### The Pattern That Works
1. ✅ Upgrade locally with Stack Car
2. ✅ Verify it works locally (or find issue quickly in controlled environment)
3. ✅ If it works locally but fails on VM, it's an **infrastructure issue** (docker config, mount points, env vars)
4. ✅ If it fails locally, it's a **code issue** (fix, test locally, then deploy to VM)

### Why This Matters
- **Local testing is fast**: No SSH delays, easy to restart containers, easy to inspect files
- **VM is production**: hykudev is a production environment with persistent data; mistakes are costly
- **Reference baseline**: Jr dev confirmed local setup works → use that as "known good"
- **Isolation**: Local + VM failure → infrastructure issue; VM only failure → local issue

### Applying This to 7.2.0
- **Hypothesis**: 7.2.0 breaks search (empty results)
- **Test locally first**: `cd /path/to/knapsack && docker compose up -d --build` on 7.2.0 branch
- **If it works locally**: Search expected to return results; if VM search fails, investigate hykudev config
- **If it fails locally**: Code issue; fix in branch, test locally, then merge to hykudev

---

## Lesson 3: Solr Collection Mismatch Is a CODE Issue

### The Observation
- Records visible in Solr (queries show doc count > 0)
- Search results empty (queries return 0 docs)
- **Data is NOT lost**, just indexed to wrong collection

### The Architecture
```
Indexing Path:
  Hyrax.index_adapter.save(resource: work)
    → Valkyrie::Indexing::Solr::IndexingAdapter
    → SolrEndpoint.connection_options['core'] (determines collection name)
    → Solr client indexes to that collection

Search Path:
  CatalogSearchBuilder.build_query
    → Blacklight.connection_config['url']
    → Solr client queries from that collection
```

### If Collections Don't Match
- **Result**: Indexing writes to "uuid" collection, search queries "hydra-production"
- **Symptom**: Facets load (Solr has docs), but results empty (query collection is empty)
- **Cause**: SolrEndpoint logic changed between 7.1.3 and 7.2.0
- **Fix**: Investigate code diff in SolrEndpoint/Valkyrie path; likely `create_solr_collection_job.rb` or how tenant name is used

### How to Confirm Collection Mismatch
```bash
# 1. Check what collection hykudev tenant uses
docker compose -f docker-compose.production.yml exec web bin/rails runner \
  "puts Account.first.solr_endpoint.inspect"
# Shows: #<SolrEndpoint ... collection:"???" ...>

# 2. Check Solr doc counts per collection
curl 'http://localhost:8983/solr/uuid/select?q=*:*&rows=0' | jq .response.numFound
curl 'http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0' | jq .response.numFound

# 3. If uuid has docs and hydra-production is empty → collection mismatch confirmed
```

### Why This Matters
- **Data safety**: Docs aren't lost, wrong collection is an easy fix
- **Code vs data**: This is NOT a data problem, it's a code logic problem
- **Testing strategy**: Don't clear Solr; fix the code that selects collection
- **Upgrade impact**: Any upgrade changing SolrEndpoint logic must be tested carefully

---

## Lesson 4: Fresh Snapshots Between Test Cycles

### Snapshot Purpose
- **Checkpoint**: Known-good baseline to compare against
- **Insurance**: Can revert failed test without full rebuild
- **Comparison**: Multiple snapshots let you compare "working" vs "broken"

### Snapshot Checklist
Before major version testing (7.2.0, 7.3, etc.):
- [ ] Running version (e.g., 7.1.3) is verified stable
- [ ] All gem versions correct (Gemfile.lock or constraints applied)
- [ ] Records displaying in search (data + code both working)
- [ ] Facet counts loading correctly
- [ ] Take snapshot with clear label: `2026-11-05-v7.1.3-clean-baseline`

### Using Snapshots
```bash
# Before upgrading
snapshot create "v7.1.3-clean-baseline"

# Test upgrade (7.2.0 branch)
# If it fails badly
snapshot restore "v7.1.3-clean-baseline"

# To compare two versions
snapshot list
# Apply diff: git diff main feature/hyku-v7.2.0-upgrade
```

---

## Next Session Preparation

### Before Starting 7.2.0 Testing
- [ ] hykudev snapshot complete (v7.1.3 clean baseline)
- [ ] SolrEndpoint collection configuration confirmed for current version
- [ ] Solr doc counts verified (both collections, known-good baseline)
- [ ] Gemfile.lock strategy decided (commit it or pin in Gemfile)

### Investigation Checklist for 7.2.0
1. **Local test first**
   - `git checkout feature/hyku-v7.2.0-upgrade`
   - `docker compose up -d --build`
   - Test search locally
   
2. **If local fails, root cause is code**
   - Use git diff to compare SolrEndpoint, Valkyrie indexing, collection creation
   - Narrow to specific commit that breaks search
   
3. **If local works but hykudev fails, root cause is infrastructure**
   - Compare docker config (prod vs local)
   - Check env vars (SOLR_COLLECTION_NAME, etc.)
   - Verify Solr collections exist and are accessible

4. **If both work but search is empty, root cause is collection mismatch**
   - Confirm which collection tenant endpoint points to
   - Confirm which collection docs are indexed to
   - Find code that changed between versions
   - Fix the collection selection logic

---

## Critical Files for Next Session

**Code**:
- `hyrax-webapp/app/models/solr_endpoint.rb` (lines 35-47) — How collection is selected
- `hyrax-webapp/app/jobs/create_solr_collection_job.rb` (lines 99-114) — Which collection name is created
- `hyrax-webapp/config/initializers/wings.rb` (Valkyrie configuration)

**Config**:
- `Gemfile.lock` (or Gemfile with gem version pins)
- `.env.production` (SOLR_COLLECTION_NAME, SOLR_CONFIGSET_NAME)
- `docker-compose.production.yml` (Solr service config)

**Task Files**:
- `2026-10-30-CRITICAL-BUG-SEARCH-RESULTS-NOT-DISPLAYING-HYKU-720-UPGRADE.md` (investigation procedures)
- `2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md` (remediation procedures)

---

## Key Takeaways

| Issue | Root Cause | Prevention |
|-------|-----------|-----------|
| Web crash on startup | Gem version mismatch | Commit Gemfile.lock or pin versions |
| Empty search results | Collection mismatch (code logic) | Test locally first; compare SolrEndpoint between versions |
| Hard to diagnose failures | Debugging on VM without baseline | Use Stack Car local testing; VMs are for final validation |
| Lost snapshots / unable to revert | Forgot to checkpoint | Fresh snapshot before each major test cycle |

