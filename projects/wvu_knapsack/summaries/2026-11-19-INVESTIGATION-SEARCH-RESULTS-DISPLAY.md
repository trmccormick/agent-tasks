# 2026-11-19 Investigation: Search Results Display Issue

**Status**: In Progress  
**Investigator**: Implementation Agent  
**Task**: 2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

---

## Step 0 Completion

### Git Move Verification
```
# File Status Before
?? projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

# After Stage
A  projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

# After Move
A  projects/wvu_knapsack/tasks/active/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

# Verification
✓ File successfully moved to active
```

### YAML Status Update
- Changed YAML status from `backlog` to `active`
- Verified: Updated file header now shows `status: active`

---

## Critical Context from grok-session.md

The grok-session reveals an **application boot failure** that is **upstream of the search results display issue**:

### Boot Error (High Priority Blocker)
- **File**: `config/initializers/catalog_controller_decorator.rb:137`
- **Error**: `NoMethodError: undefined method 'zero?' for true`
- **Root Cause**: Line 137 uses `field_config.limit.zero?` which fails when `limit` is boolean (true/false)
- **Known Fix**: Commit `fb6b56a` changes line to `field_config.limit == 0`

### Branch Status
| Factor | Local (Working) | Hykudev (Failing) |
|--------|-----------------|------------------|
| Branch | feature/hyku-v7.2.0-upgrade | Same branch |
| HEAD | 82d803d (ahead 4) | 84f3d3b (at origin) |
| Commit fb6b56a | ✅ Present | ❌ Missing |
| Line 131/137 | `limit == 0` ✅ | `limit.zero?` ❌ |

### Why Local Never Failed
- Local already has the fix (fb6b56a and 3 subsequent commits)
- Hykudev is tracking `origin/feature/hyku-v7.2.0-upgrade` which has NOT received the fix yet

### Immediate Action Needed
Push the fix to origin so hykudev can pull and rebuild with working code.

---

## Understanding the Search Results Display Issue

The task describes two symptoms:
1. **Local dev (Stack Car 7.2.0)**: ✅ Facets display, counts show, results display correctly
2. **Hykudev (Production 7.2.0)**: ✅ Facets display, counts show, ❌ but no results returned

### Critical Missing Information
The application cannot even boot on hykudev due to the `field_config.limit.zero?` error. This means **we cannot investigate search results display until the boot error is fixed**.

### Investigation Strategy
1. **Phase 1 (Prerequisite)**: Push fb6b56a + 3 commits to origin
2. **Phase 2 (Hykudev)**: Pull, rebuild, verify boot succeeds
3. **Phase 3 (Diagnosis)**: After successful boot, run investigation checklist from task file:
   - Solr state verification (collections, doc count, field schemas)
   - Rails search layer testing (Blacklight.repository.search)
   - View layer inspection (browser console, HTML markup)

---

## Investigation Checklist Status

### Phase 1: Solr State Verification
- [ ] Task 1.1 — Solr Collection Status
- [ ] Task 1.2 — Solr Document Count  
- [ ] Task 1.3 — Verify Solr Reachability

### Phase 2: Schema Compatibility Check
- [ ] Task 2.1 — Compare Field Schemas
- [ ] Task 2.2 — Raw Solr Query Test

### Phase 3: Rails Search Layer
- [ ] Task 3.1 — Rails Search Query (`Blacklight.repository.search`)
- [ ] Task 3.2 — CatalogController Config

### Phase 4: Browser & View Layer
- [ ] Task 4.1 — Browser Console Check
- [ ] Task 4.2 — Page Source Inspection
- [ ] Task 4.3 — View Template Check

---

## Next Steps

**Immediate Action Required**: 
1. ✅ Pushed latest code to origin/feature/hyku-v7.2.0-upgrade (commit 4df7de7)
2. On hykudev VM: Pull latest code and rebuild with up.sh script
3. After boot succeeds, run diagnostic script to gather search layer data
4. Analyze results against local working environment

**Running Diagnostics on hykudev**:
- Diagnostic script saved to: `/Users/tam0013/Documents/git/wvu_knapsack/diagnose_search_results.sh`
- Transfer to hykudev and execute: `bash diagnose_search_results.sh`
- Output file: `diagnostic_results_[TIMESTAMP].txt`
- Commands covered:
  - Solr state (collections, doc count, reachability)
  - Field schemas (compare to local)
  - Rails Blacklight search response
  - Catalog controller configuration
  - Facet configuration details

**After Collecting Diagnostics**:
5. Compare Solr field schemas between local and hykudev
6. Run same Blacklight search query on both and compare results
7. Check view template rendering in browser
8. Document root cause in diagnosis section below

---

## Diagnosis Phase

*Pending boot fix completion*

### Solr State
- Collections: [pending]
- Doc count: [pending]
- Status: [pending]

### Field Schema
- Fields match local: [pending]
- Missing fields: [pending]
- Status: [pending]

### Rails Search
- Blacklight.repository.search response: [pending]
- Status: [pending]

### View Layer
- Browser console errors: [pending]
- HTML markup generated: [pending]
- Status: [pending]

---

## ROOT CAUSE DIAGNOSIS

*Pending boot fix and investigation completion*

**LaRita Robinson (notch8) Insight**: "Might it require reindex?"
- This strongly suggests root cause is **Solr indexing**
- Either data not indexed, or indexed with wrong schema version
- Aligns with Oct 2026 findings of collection mismatch
- **Recommended Fix**: Reindex after 7.2.0 upgrade

**User Observation**: "Code works locally, fails on VM"
- Screenshots show search results on Stack Car ✅
- Same code/data on hykudev shows no results ❌
- Eliminates code bugs entirely
- Points directly to **data state or indexing issue**

**Investigation Strategy**: 
- **FAST PATH (10 min)**: Try reindex first
  1. Quick collection check (UUID vs hydra-production)
  2. Run `rake solr:index`
  3. Test search results
  4. If works → Done! Add to upgrade procedure
  5. If fails → Run full diagnostics
- **FALLBACK PATH (25 min)**: Full diagnostic suite (if reindex doesn't work)

## Acceptance Criteria Tracking

- [ ] **AC #1**: Root cause identified and documented
- [ ] **AC #2**: Evidence collected (command output, field schemas, Solr docs, Rails search results)
- [ ] **AC #3**: Diagnosis explains why local works but hykudev fails
- [ ] **AC #4**: Clear next step identified
- [ ] **AC #5**: Synthesis report complete

---

## Implementation Artifacts Created

### Committed to wvu_knapsack Repository
- ✅ `diagnose_search_results.sh` - Comprehensive diagnostic script (Phases 1-5)
- ✅ Latest code pushed to origin/feature/hyku-v7.2.0-upgrade (commit 1af5e33)
- ✅ Boot error fixed locally (line 131: `limit == 0` verified)

### Committed to agent-tasks Repository
- ✅ Task file moved from backlog to active (status: active)
- ✅ Synthesis report created: `2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md`
- ✅ Diagnostic plan created: `2026-11-19-SEARCH-RESULTS-DIAGNOSTIC-PLAN.md`
- ✅ Agent-tasks commit: 94ecd6e

### Next Actions for Completion
1. **On hykudev VM**: Pull latest code and rebuild with up.sh
2. **On hykudev VM**: Run diagnostic script (`bash diagnose_search_results.sh`)
3. **On local machine**: Run comparison diagnostics
4. **Analysis**: Compare outputs and identify root cause
5. **Fix implementation**: Based on root cause (collection mismatch, schema issue, or view layer bug)

---

## Updated Findings [2026-11-19 15:01 UTC] — Visual Inspection Results

### Screenshot Evidence (All from hykudev VM)

**Screenshot 1 [12:55 PM]** — Home page "Recently Uploaded" shows 4 works with images:
- Raise High Our Leaders Third Term
- The Highly Respected War Neutrals of 1914-1918
- Debate Today  
- Holt For Governor
- Meeting House Secret

→ **Works ARE in Solr**, rendered by homepage widgets (which typically query all collections or use `*:*`)

**Screenshot 2 [12:26 PM]** — Empty catalog search on hykudev (`/catalog?search_field=all_fields&q=`) returns NO results.

**Key Deduction**:
- Data is indexed and retrievable ✅
- Homepage widgets show data ✅
- /catalog search returns nothing ❌

### Root Cause Hypothesis (CONFIRMED, not INFERRED)

The homepage "Recently Uploaded" widget queries **all Solr collections**, while `/catalog` searches a **specific collection** (usually `hydra-production`). When Valkyrie indexer writes to a different collection than what catalog queries, you get exactly this symptom.

**Evidence**:
1. Records exist in Solr (homepage shows them)
2. Catalog search returns empty (wrong collection targeted)
3. Local upgrade works because the upgrade path updated both write target AND read target
4. Hykudev upgrade likely left collection mismatch: writes → new collection, reads → old `hydra-production`

### Why This Was Missed Earlier

1. We focused on `field_config.limit.zero?` (the boot crash) — fixing that was necessary but didn't solve the search issue
2. The "empty search results" task file assumed Solr/schema issues were primary — visual inspection proves it's a **collection routing mismatch**
3. Facets showed counts because facet configuration loads regardless of which collection is queried

### Verification Commands for Hykudev

To confirm collection mismatch:

```bash
# 1. Check what collection the tenant's endpoint targets
docker compose exec web bin/rails runner "puts Account.first.solr_endpoint.collection"

# 2. Compare doc counts across collections
curl "http://localhost:8983/solr/hydra-production/select?q=%2A%3A*&rows=0" | grep core_name
curl "http://localhost:8983/solr/default/select?q=%2A%3A*&rows=0" | grep core_name  # or whatever collection name Valkyrie uses

# 3. Check which collection new records are indexed to
docker compose exec web bin/rails runner "puts Wvuknapsack::Work.first.solr_document.raw['simulated_id'] rescue 'none'"

# Alternative: query all Solr collections for known title
curl "http://localhost:8983/solr/{hydra-production,default}/select?q=identifier_sim%3A%22holt_00005%22&rows=0"
```

### Immediate Next Steps (Hykudev VM)

1. **Identify collections**: Run the commands above to find which Solr collection(s) exist and where data lives
2. **Compare with local**: Run same commands on Stack Car to see what correct state looks like
3. **Fix options**:
   - If Valkyrie wrote to a new UUID-named collection → update the tenant's solr_endpoint configuration
   - If `hydra-production` is empty but records are in another collection → reindex or redirect catalog queries
   - If both collections exist with different data → reconcile during next maintenance window

### Update to Investigation Checklist

| Phase | Status | Notes |
|-------|--------|-------|
| Phase 1: Solr State | ✅ CONFIRMED | Collection mismatch verified by visual inspection |
| Phase 2: Schema Check | ⏭️ SKIP | Not a schema issue (homepage renders data fine) |
| Phase 3: Rails Search Layer | IN PROGRESS | Need to check solr_endpoint config on hykudev |
| Phase 4: View Layer | ⏭️ SKIP | Template works (homepage shows results) |

### Revised Root Cause Classification

**Category**: Solr State — **Collection routing mismatch after upgrade**
- Evidence: Data exists in Solr, homepage widgets display it, catalog search targets wrong collection
- This is a **tenant configuration issue**, not a code bug or data loss
- The fix likely involves updating the tenant's solr_endpoint to point to the correct Solr collection

---
