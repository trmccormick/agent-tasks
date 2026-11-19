# 2026-11-19 Action Plan: Search Results Display Investigation

**Goal**: Diagnose why search results don't display on hykudev (facets + counts OK, but no documents shown) while they display correctly on local Stack Car.

**Current Status**:
- ✅ Latest code pushed to origin/feature/hyku-v7.2.0-upgrade (commit 4df7de7)
- ✅ Diagnostic script created: `/Users/tam0013/Documents/git/wvu_knapsack/diagnose_search_results.sh`
- ⏳ Next: Run diagnostics on hykudev VM and compare with local

---

## Commands to Run on Hykudev

### Prerequisites
```bash
cd /root/wvu_knapsack   # or wherever repo is checked out

# 1. PULL LATEST CODE (if not already done)
git fetch origin
git pull origin feature/hyku-v7.2.0-upgrade

# 2. REBUILD WITH PRODUCTION DOCKER COMPOSE
# Use the standard up/down scripts, NOT raw docker compose commands
sh down.sh
sh up.sh
# Wait for initialization_app to complete, verify no errors
```

### Quick Diagnostics (5 minutes)

**Check 1: Solr Collections & Document Count**
```bash
# From VM root
docker compose exec -T solr solr status | grep -A 2 "hydra"
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'

# Check if UUID collection exists (symptom of previous issue)
curl -s "http://localhost:8983/solr/admin/collections?action=LIST&wt=json" | jq '.collections'
```

**Check 2: Rails Blacklight Search**
```bash
docker compose exec -T web bundle exec rails runner \
  "results = Blacklight.repository.search({}); \
   puts \"Total found: #{results.total}\"; \
   puts \"Documents returned: #{results.documents.count}\"; \
   puts \"First doc ID: #{results.documents.first['id'] if results.documents.any?}\""
```

**Check 3: What URL is the search actually hitting?**
```bash
docker compose exec -T web bundle exec rails runner \
  "config = CatalogController.blacklight_config; \
   puts \"Search Path: #{config.search_path}\"; \
   puts \"Solr Path: #{config.solr_path}\"; \
   puts \"Solr Endpoint: #{config.solr_endpoint_class}\""
```

**Check 4: Verify Git Status**
```bash
docker compose exec -T web bash -c 'cd /app/samvera && git log -1 --oneline && grep -n "limit\." config/initializers/catalog_controller_decorator.rb | head -1'
# Should show commit with fb6b56a or later, and "limit == 0" (not "limit.zero?")
```

---

## Commands to Run on Local (for Comparison)

After running the above on hykudev, run the same commands locally to compare:

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# 1. CHECK LOCAL STATE
git log -1 --oneline
git branch -v
grep -n "limit\." config/initializers/catalog_controller_decorator.rb | head -1

# 2. IF USING STACK CAR, GET RESULTS
sc exec bundle exec rails runner \
  "results = Blacklight.repository.search({}); \
   puts \"Total found: #{results.total}\"; \
   puts \"Documents returned: #{results.documents.count}\"; \
   puts \"First doc ID: #{results.documents.first['id'] if results.documents.any?}\""

# 3. SOLR FIELD COUNT (for Stack Car Solr)
# If Stack Car Solr is running on different port:
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'
```

---

## Expected Findings

### If Search Works Locally but Not on Hykudev:

**Possible Root Causes**:

1. **Solr Collection Mismatch** (from previous Oct session notes)
   - Local Solr has docs in hydra-production: numFound > 0 ✅
   - Hykudev Solr has docs in UUID collection, NOT hydra-production: numFound = 0 ❌
   - **Fix**: Check which collection the tenant's SolrEndpoint points to, verify Valkyrie indexer writes to same collection

2. **Schema Field Mismatch**
   - Local Solr has _sim, _tesim fields (expected for facets)
   - Hykudev Solr missing these fields or using different names
   - **Fix**: Reindex with correct schema

3. **Rails Search Builder Issue**
   - Local: `Blacklight.repository.search({})` returns docs
   - Hykudev: Same call returns empty (query builder not reading correct collection/fields)
   - **Fix**: Check CatalogSearchBuilder, Blacklight configuration

4. **View Layer Issue** (least likely, facets work so HTML rendering works)
   - Solr returns docs, but template doesn't render them
   - **Fix**: Inspect view template, check for CSS hiding results

### If Search Fails on Both:
   - Configuration issue (likely boot error not fully resolved yet)
   - Run diagnostic script to get full error output

---

## Full Diagnostic Script

**Location**: `/Users/tam0013/Documents/git/wvu_knapsack/diagnose_search_results.sh`

**On hykudev**, transfer and run:
```bash
# Transfer from local to hykudev:
scp /Users/tam0013/Documents/git/wvu_knapsack/diagnose_search_results.sh root@hykudev:/root/wvu_knapsack/

# SSH into hykudev:
ssh root@hykudev
cd /root/wvu_knapsack

# Run diagnostics:
bash diagnose_search_results.sh

# View results:
cat diagnostic_results_*.txt
# or specific output:
head -100 diagnostic_results_*.txt
```

**Output File**: Creates `diagnostic_results_[TIMESTAMP].txt` with:
- Phase 1: Solr collections, doc counts, field reachability
- Phase 2: Solr schema (field list comparison)
- Phase 3: Rails Blacklight search response
- Phase 4: CatalogController configuration
- Phase 5: Facet registration details

---

## Decision Tree After Diagnostics

```
START: Run diagnostics on hykudev

├─ Blacklight.repository.search() returns NO docs
│  ├─ Solr hydra-production has docs? NO
│  │  └─ Check if UUID collection exists (previous bug)
│  │     ├─ UUID collection exists, hydra-production empty
│  │     │  └─ ROOT CAUSE: Collection mismatch (Valkyrie indexes to UUID, Rails queries hydra-production)
│  │     │     → Fix: Update SolrEndpoint config OR reindex to hydra-production
│  │     │
│  │     └─ No UUID collection, hydra-production also empty
│  │        └─ ROOT CAUSE: Data not indexed (or indexed to wrong tenant's collection)
│  │           → Fix: Check data import, verify initialize_app indexed records
│  │
│  └─ Solr hydra-production has docs? YES
│     └─ Rails search query not matching Solr fields
│        └─ ROOT CAUSE: Schema field mismatch or query builder issue
│           → Fix: Check field names in Solr vs Rails config, verify CatalogSearchBuilder

├─ Blacklight.repository.search() returns DOCS OK
│  └─ View template not rendering
│     └─ ROOT CAUSE: HTML template issue (unlikely, facets work)
│        → Fix: Check view layer, browser console, CSS

└─ Error on boot (initialize_app fails)
   └─ Code issue blocking application
      → Run with --trace, fix errors, rebuild
```

---

## Summary

**Timeline**:
1. **Now**: Code pushed to origin, synthetic report created, diagnostic script ready
2. **On VM** (5-10 min): Run quick checks to identify root cause
3. **Analysis** (5 min): Compare findings vs local
4. **Fix** (depends on cause): 
   - Collection mismatch → update config/reindex
   - Schema issue → data import or reindex
   - View layer → template fix
5. **Verification** (5 min): Confirm search results display on hykudev

**Key Metric**: After fix, verify both return same doc count:
```
Local: Blacklight.repository.search({}).total
Hykudev: Blacklight.repository.search({}).total
# Both should match
```

---

**Prepared by**: Implementation Agent  
**Date**: 2026-11-19 14:35 UTC  
**Next**: Run VM diagnostics and report findings
