# Qwen Session Handoff — Hyku 7.2.0 Root Cause Investigation
**Date:** 2026-11-05  
**Session Type:** Root cause analysis and code investigation  
**Status:** Ready for Qwen to begin  
**Token Saved:** Switched to local Qwen model (Claude Haiku reserved for expensive operations)

---

## Current State

### Environment
- **hykudev:** v7.1.3 running, web container stable
- **Gem versions:** Fixed (blacklight 7.41.0 + blacklight_advanced_search 7.0.0 via locked Gemfile.lock)
- **Records:** Displaying in Solr (pre-existing data from production)
- **Search:** Empty results despite facets loading (collection mismatch suspected)
- **Snapshot:** Fresh baseline taken (v7.1.3 clean, before 7.2.0 testing)

### What's Been Discovered
1. ✅ **Gem version drift** — Fixed via Gemfile.lock (dev had 8.1.0, prod had 7.0.0)
2. ✅ **Architecture mapped** — Valkyrie indexing writes to one collection, search queries another
3. ✅ **Collection mismatch hypothesis** — Strong evidence but not yet confirmed
4. ⏳ **Root cause of 7.2.0 bug** — Likely in SolrEndpoint or collection naming logic

### Why Search Returns Empty (Working Theory)
```
Indexing Path: Hyrax.index_adapter.save(resource: work) 
  → Uses SolrEndpoint.connection_options['core'] 
  → Writes to collection "???" (likely "uuid" or tenant-based name)

Search Path: CatalogSearchBuilder.build_query 
  → Uses Blacklight.connection_config 
  → Queries from collection "???" (likely "hydra-production")

If collections don't match → facets work (show counts from index), 
but search returns 0 results (queries empty collection)
```

---

## Next Steps (Qwen's Tasks)

### Task 1: Confirm Collection Mismatch (10 min)
**Objective:** Verify that indexing and search use different collections

**Steps:**
1. SSH to hykudev:
   ```bash
   ssh tam0013@hykudev.lib.wvu.edu
   cd /Users/tam0013/Documents/git/wvu_knapsack
   ```

2. Check SolrEndpoint configuration:
   ```bash
   docker compose -f docker-compose.production.yml exec web bin/rails runner \
     "endpoint = Account.first.solr_endpoint; puts endpoint.inspect; puts '---'; puts endpoint.connection_options"
   ```
   **Look for:** Which collection name is stored/used

3. Check Solr collections and doc counts:
   ```bash
   curl -s 'http://localhost:8983/solr/admin/collections?action=LIST' | jq .collections
   ```
   **Look for:** Collection names (likely "uuid" and "hydra-production")

4. Count documents per collection:
   ```bash
   curl -s 'http://localhost:8983/solr/uuid/select?q=*:*&rows=0' | jq .response.numFound
   curl -s 'http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0' | jq .response.numFound
   ```
   **Expected:** uuid has docs (4000+), hydra-production has 0

**Success Criteria:** If uuid has docs and hydra-production is empty → collection mismatch **CONFIRMED**

---

### Task 2: Investigate Code Changes in 7.2.0 (20-30 min)
**Objective:** Find what changed in collection selection logic between 7.1.3 and 7.2.0

**Files to Compare:**
- `hyrax-webapp/app/models/solr_endpoint.rb` (lines 35-47)
  - `connection_options` method: How does it set the 'core' value?
  - `switchable_options` method: Where does collection name come from?
  
- `hyrax-webapp/app/jobs/create_solr_collection_job.rb` (lines 8-114)
  - `perform_for_normal_tenant(account, name)` (line 112)
  - What collection name is passed? How is it determined?
  
- `hyrax-webapp/config/initializers/wings.rb` (Valkyrie config)
  - How is the Solr indexing adapter configured?
  - Does it use a specific collection by default?

**Git Comparison:**
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Compare key files between 7.1.3 and 7.2.0
git log --oneline --all | grep -i "7.1.3\|7.2.0" | head -10
# Find the commit hashes for these versions

git diff 7.1.3-tag 7.2.0-tag -- hyrax-webapp/app/models/solr_endpoint.rb
git diff 7.1.3-tag 7.2.0-tag -- hyrax-webapp/app/jobs/create_solr_collection_job.rb
git diff 7.1.3-tag 7.2.0-tag -- hyrax-webapp/config/initializers/wings.rb
```

**Investigation Questions:**
- Did `create_solr_collection_job.rb` change how collection name is derived?
- Did Valkyrie indexing adapter change its default collection behavior?
- Is there a new environment variable or configuration in 7.2.0?
- Does the account/tenant name change how collection is selected?

**Success Criteria:** Identify at least one code change that explains why collection selection differs

---

### Task 3: Test the Hypothesis Locally (Optional, if needed)
**Objective:** Confirm fix works by testing locally before hykudev

**If hypothesis suggests a fix:**
1. Check out local Stack Car setup
2. Test on 7.2.0 branch with the proposed fix
3. Verify search returns results
4. Report back with success/failure

---

## Files to Read (Before Starting)

**High Priority** (read first):
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-UPGRADE-TESTING-STRATEGY.md` (4 testing lessons)
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md` (current project status)

**Context** (reference):
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/` (active task files if any)
- Conversation summary in current VS Code session (has architecture details)

**Code** (investigate):
- `hyrax-webapp/app/models/solr_endpoint.rb` (SolrEndpoint class)
- `hyrax-webapp/app/jobs/create_solr_collection_job.rb` (Collection creation)
- `hyrax-webapp/config/initializers/wings.rb` (Valkyrie init)

---

## VM Access & Utilities

**SSH to hykudev:**
```bash
ssh tam0013@hykudev.lib.wvu.edu
cd /Users/tam0013/Documents/git/wvu_knapsack
```

**Docker commands on hykudev:**
```bash
# View logs
docker compose -f docker-compose.production.yml logs web -f

# Run Rails command
docker compose -f docker-compose.production.yml exec web bin/rails runner "CODE_HERE"

# Access Rails console
docker compose -f docker-compose.production.yml exec web bin/rails console
```

**Solr queries:**
```bash
# List collections
curl -s 'http://localhost:8983/solr/admin/collections?action=LIST' | jq

# Query a collection
curl -s 'http://localhost:8983/solr/COLLECTION_NAME/select?q=*:*&rows=10'

# Get doc count
curl -s 'http://localhost:8983/solr/COLLECTION_NAME/select?q=*:*&rows=0' | jq .response.numFound
```

---

## Success Criteria for This Session

✅ **Minimal Success:**
- Collection mismatch confirmed (uuid has docs, hydra-production empty)
- At least one code change identified that explains the difference

✅ **Ideal Success:**
- Collection mismatch confirmed
- Root code change isolated and understood
- Proposed fix identified (even if not tested yet)
- Local test run (if feasible)

---

## Related Tasks & History

**Previous Sessions:**
- 2026-10-30: Three code fixes applied (facet blocks, config, limit checks)
- 2026-11-02: Root cause diagnosed as Solr data issue (wrong collection)
- 2026-11-05: Gem version drift fixed, collection mismatch theory strengthened

**Active Investigation:**
- `2026-10-30-CRITICAL-BUG-SEARCH-RESULTS-NOT-DISPLAYING-HYKU-720-UPGRADE.md` (original detailed investigation)
- `2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md` (remediation procedures)

---

## Notes for Qwen

- hykudev is production environment (treat data carefully, no destructive operations without confirmation)
- Snapshot taken before starting (safe to revert if something breaks)
- Fresh Gemfile.lock applied (builds now consistent)
- Focus on **understanding the code change**, not implementing fixes yet
- Report back with findings; may need another session to implement fix

**Time Estimate:** 30-45 min for full investigation

