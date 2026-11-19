# 2026-11-19 Reindex-First Strategy for Search Results Issue

**Key Insight**: Code works locally, fails on hykudev → Data/Indexing issue, NOT code bug

**Strategy**: Try reindex FIRST before lengthy diagnostics

---

## Why Reindex First Makes Sense

| Evidence | Implies |
|----------|---------|
| ✅ Facets load on hykudev | Solr is running, collections accessible |
| ✅ Facet counts accurate | Data exists in Solr somewhere |
| ❌ Results don't display | Data is there but in wrong collection or schema incompatible |
| ✅ Code works locally | Not a code bug, it's data state |
| ✅ Same data import strategy used | Indexing path likely culprit |

**Previous Issue (Oct 2026)**: Collection mismatch (UUID vs hydra-production)
**Current Issue (Nov 2026)**: Same symptoms suggest same root cause → Likely needs reindex after 7.2.0 upgrade

---

## Fast Path: Reindex First (10 minutes)

### 1. SSH & Prep (2 min)
```bash
ssh root@hykudev
cd /root/wvu_knapsack
git pull origin feature/hyku-v7.2.0-upgrade
sh down.sh && sh up.sh
# Wait for initialization_app
```

### 2. Quick Collection Check (1 min)
```bash
# Check if UUID collection exists (indicator of Oct bug)
curl -s "http://localhost:8983/solr/admin/collections?action=LIST&wt=json" | jq '.collections'

# Count docs
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'
```

**If hydra-production is empty** → Proceed to Step 3 (Reindex)  
**If hydra-production has docs** → Problem is not data location, check schema (run full diagnostics)

### 3. Run Reindex (5 min)
```bash
docker compose exec -T web bundle exec rake solr:index

# Monitor
docker compose logs web -f | grep -i index

# After completion
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'
```

### 4. Test (2 min)
```bash
# Quick Rails test
docker compose exec -T web bundle exec rails runner \
  "results = Blacklight.repository.search({}); \
   puts \"Found: #{results.total}; Returned: #{results.documents.count}\""

# Browse to results page
# https://demo-hykudev.lib.wvu.edu/catalog?q=*
```

---

## Expected Outcomes

### ✅ Scenario A: Reindex Fixes It
```
Before reindex:
  - Solr hydra-production: 0 docs
  - Blacklight search: 0 results
  - Display: Empty ❌

After reindex:
  - Solr hydra-production: N docs
  - Blacklight search: N results
  - Display: Results show ✅
  
→ Root cause confirmed: Indexing issue after 7.2.0 upgrade
→ Fix: Add reindex step to production upgrade procedure
```

### ⚠️ Scenario B: Reindex Doesn't Fix It
```
Before reindex:
  - Solr hydra-production: 0 docs

After reindex:
  - Solr hydra-production: 0 docs (still empty)
  - OR: hydra-production fills but search still returns nothing
  
→ Root cause different: Schema incompatibility or query builder issue
→ Next: Run full diagnostics (compare fields, Blacklight config)
```

### 🔍 Scenario C: UUID Collection Has Data
```
Collections:
  - hydra-production: 0 docs
  - uuid: N docs (non-zero)
  
→ Root cause confirmed: Collection mismatch (Oct bug resurfaced)
→ Fix: Reindex to hydra-production OR update SolrEndpoint config to point to UUID
```

---

## Production Upgrade Procedure (Updated)

After this investigation, the upgrade procedure should include:

```bash
# Standard steps
git pull origin feature/hyku-v7.2.0-upgrade
sh down.sh && sh up.sh
# Wait for initialization_app

# NEW: Add explicit reindex step for 7.2.0 upgrade
docker compose exec -T web bundle exec rake solr:index

# Verify
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'

# Test
# https://demo-hykudev.lib.wvu.edu/catalog?q=*
```

---

## Why This Approach Is Best

1. **Fastest**: 10 minutes vs 25+ minutes for full diagnostics
2. **Most likely**: Oct findings suggest collection/indexing is the issue
3. **De-risking**: If reindex works, confirms root cause immediately
4. **If it fails**: You still have all diagnostics available, now with reindex output as additional evidence
5. **Production ready**: Gives you the exact fix needed for production upgrade

---

## Fallback Path (If Reindex Doesn't Work)

If reindex doesn't solve it, you have:
- ✅ Reindex output/logs
- ✅ Collection info (which collections exist, doc counts)
- ✅ Ready-to-run diagnostic script for deeper investigation
- ✅ Decision tree to analyze schema/builder issues

Then proceed with full diagnostics from `2026-11-19-SEARCH-RESULTS-DIAGNOSTIC-PLAN.md`

---

## Summary

**Recommended Action**:
1. Pull code
2. Rebuild
3. Quick collection check (is data in hydra-production?)
4. **Run reindex**
5. Test search
6. If works → Done! Document as required upgrade step
7. If fails → Run full diagnostics for deeper analysis

**Expected time to resolution**: 10 min if reindex works, 30 min if diagnostic investigation needed

This aligns with your observation: **"Code works locally, fails on VM"** = Data/Indexing issue, not code.
