# 2026-11-19 Search Results Display Issue — Investigation Ready

**Status**: ✅ Ready for VM diagnostics  
**Investigator**: Implementation Agent  
**Task**: 2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

---

## Summary

The 7.2.0 upgrade on hykudev shows a **critical display bug**: facets load correctly with accurate counts, but search result documents don't display, while the same code works perfectly locally on Stack Car.

**Key Facts**:
- ✅ **Facets display** (28 M3 facets registered, counts accurate)
- ✅ **Data exists** (Solr contains records, proven by facet counts)
- ❌ **Documents don't display** (search results empty on hykudev)
- ✅ **Local Stack Car works** (same codebase, same data import strategy)

**Root Cause**: Unknown — likely one of:
1. **Solr collection mismatch** (Valkyrie indexes to UUID collection, Rails queries hydra-production)
2. **Schema field mismatch** (field names differ between local and production)
3. **Rails search builder issue** (CatalogSearchBuilder not requesting correct fields)
4. **View layer bug** (HTML template not rendering results even if Solr returns them)

---

## What's Been Done

### ✅ Code Fix Verified
- Boot error fixed locally: `config/initializers/catalog_controller_decorator.rb` line 131
  - Changed from: `field_config.limit.zero?` (fails with boolean values)
  - Changed to: `field_config.limit == 0` ✅
- Latest code **pushed to origin/feature/hyku-v7.2.0-upgrade** (commit 1af5e33)

### ✅ Diagnostic Tools Ready
1. **Diagnostic script** created: `/Users/tam0013/Documents/git/wvu_knapsack/diagnose_search_results.sh`
   - 5 phases of investigation (Solr state, schema, Rails search, view layer, facets)
   - Runs all 13 diagnostic commands and produces single output file
   - Already committed and pushed

2. **Diagnostic plan** created: `/Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/handoffs/2026-11-19-SEARCH-RESULTS-DIAGNOSTIC-PLAN.md`
   - Commands to run on hykudev
   - Commands to run on local for comparison
   - Decision tree for root cause analysis

3. **Synthesis report** created: `/Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/summaries/2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md`
   - Tracks investigation progress
   - Documents all findings
   - Maps to acceptance criteria

---

## How to Proceed

### Step 1: Prepare Hykudev (5 minutes)

```bash
# SSH into hykudev
ssh root@hykudev

# Navigate to repo
cd /root/wvu_knapsack   # or wherever it's located

# Pull latest code (includes boot fix + diagnostic script)
git fetch origin
git pull origin feature/hyku-v7.2.0-upgrade

# Rebuild with production docker compose (use standard scripts, not raw docker commands)
sh down.sh
sh up.sh
# Wait for initialization_app to complete (~2-3 minutes)
# Verify no boot errors (should see "Total M3 facets registered: 28" message)
```

### Step 2: Run Diagnostics (5 minutes)

```bash
# Still SSH'd into hykudev
bash diagnose_search_results.sh

# Outputs results to: diagnostic_results_[TIMESTAMP].txt
# Example: diagnostic_results_1700453000.txt
```

### Step 3: Gather Comparison Data on Local (5 minutes)

```bash
# On your local machine
cd /Users/tam0013/Documents/git/wvu_knapsack

# If using Stack Car, run same tests
sc exec bundle exec rails runner \
  "results = Blacklight.repository.search({}); \
   puts 'Total found: #{results.total}'; \
   puts 'Documents returned: #{results.documents.count}'"

# Check Solr directly
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'

# Verify config
sc exec bundle exec rails runner \
  "config = CatalogController.blacklight_config; \
   puts \"Search Path: #{config.search_path}\"; \
   puts \"Solr Path: #{config.solr_path}\""
```

### Step 4: Compare Findings

**Hykudev Results vs Local Results**:

| Item | Local | Hykudev | Match? |
|------|-------|---------|--------|
| Blacklight total found | ? | ? | ✅/❌ |
| Blacklight docs returned | ? | ? | ✅/❌ |
| Solr hydra-production doc count | ? | ? | ✅/❌ |
| Search Path | ? | ? | ✅/❌ |
| Solr Path | ? | ? | ✅/❌ |

**Root Cause Indicators**:
- If Solr counts differ → data/collection issue
- If Blacklight counts differ but Solr matches → Rails query builder issue
- If Blacklight works but HTML empty → view layer issue

### Step 5: Run Full Diagnostic Script (Optional, More Detailed)

```bash
# On hykudev, run the comprehensive diagnostic
bash diagnose_search_results.sh

# Transfer results back to local for analysis
scp root@hykudev:/root/wvu_knapsack/diagnostic_results_*.txt \
    /Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/summaries/

# View results
cat /Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/summaries/diagnostic_results_*.txt
```

---

## Critical Questions to Answer

Based on diagnostic results, determine root cause:

### Question 1: Does Solr have the data?
```bash
# On hykudev:
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'

# If numFound > 0 → Data in Solr ✅
# If numFound = 0 → Check if UUID collection has data (previous bug) ⚠️
```

### Question 2: Does Rails see the data?
```bash
# On hykudev:
docker compose exec -T web bundle exec rails runner \
  "results = Blacklight.repository.search({}); \
   puts results.total"

# If > 0 → Rails can query Solr ✅
# If = 0 → Rails not reading same collection ❌
```

### Question 3: Does the view render HTML?
```bash
# In browser:
# 1. Navigate to: https://demo-hykudev.lib.wvu.edu/catalog?q=*
# 2. Right-click → View Page Source
# 3. Search for: <div class="document"
# If found → HTML generated but hidden ⚠️
# If not found → View layer bug ❌
```

---

## Decision Guide

**If all diagnostics show mismatches**:
→ Likely **collection mismatch** (previous Oct bug resurfaced)
→ Check: `Account.first.solr_endpoint.inspect` to see which collection tenant uses
→ Solution: Reindex to hydra-production OR update SolrEndpoint config

**If Solr has data but Rails returns empty**:
→ Likely **query builder issue**
→ Check: CatalogSearchBuilder configuration, Blacklight field mappings
→ Solution: Debug Rails search query, verify field names match Solr schema

**If Rails returns data but HTML empty**:
→ Likely **view layer bug**
→ Check: Browser console (F12) for JavaScript errors
→ Solution: Inspect catalog/index view template, check CSS display properties

**If everything matches local but hykudev still empty**:
→ Likely **data import or tenancy issue**
→ Check: Which tenant's data was imported, verify Blacklight searches correct tenant
→ Solution: Check tenant configuration, re-import data if necessary

---

## Files & References

| File | Purpose |
|------|---------|
| `diagnose_search_results.sh` | Comprehensive diagnostic script (all 5 phases) |
| `2026-11-19-SEARCH-RESULTS-DIAGNOSTIC-PLAN.md` | Detailed plan with VM commands and decision tree |
| `2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md` | Synthesis report (tracks findings) |
| `grok-session.md` | Previous session notes (boot error history) |
| `HYKU_BUILD_GUIDE.md` | Reference for Stack Car and production setup |

**Locations**:
- Diagnostic script: `/Users/tam0013/Documents/git/wvu_knapsack/diagnose_search_results.sh`
- Plans & reports: `/Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/handoffs/`
- Synthesis: `/Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/summaries/`

---

## Expected Timeline

| Step | Duration | Action |
|------|----------|--------|
| **Hykudev prep** | 5 min | Pull code, rebuild |
| **Quick tests** | 5 min | Run 3-4 quick checks |
| **Local tests** | 5 min | Run same tests locally |
| **Analysis** | 5 min | Compare findings |
| **Root cause** | Immediate | Decision tree → diagnosis |
| **Fix** | Varies | Depends on root cause |

**Total diagnostic time: ~20 minutes**

---

## Success Criteria

✅ **Task Complete When**:
1. Root cause identified and documented in synthesis report
2. Evidence collected (command output from both hykudev and local)
3. Diagnosis explains why local works but hykudev fails
4. Next step identified (what code change or data fix needed)

---

**Prepared by**: Implementation Agent  
**Date**: 2026-11-19 14:35 UTC  
**Status**: Ready for VM investigation  
**Next**: Run hykudev diagnostics and report findings
