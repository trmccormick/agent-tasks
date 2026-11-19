# 🚀 ACTION CHECKLIST — Reindex-First Investigation

**Strategy**: Code works locally ✅ → Data/Indexing issue on VM ❌ → Try reindex first

---

## ✅ All Prep Complete

- [x] Code boot error fixed (line 131: `limit == 0`)
- [x] Latest code pushed to origin/feature/hyku-v7.2.0-upgrade (commit 1af5e33)
- [x] Diagnostic script created and ready
- [x] Reindex procedure documented
- [x] Investigation strategy defined
- [x] Decision trees prepared

**You are ready to investigate.**

---

## 🎯 ACTION STEPS (In Order)

### Phase 1: Hykudev Prep (5 min)

```bash
[ ] SSH to hykudev
[ ] cd /root/wvu_knapsack
[ ] git pull origin feature/hyku-v7.2.0-upgrade
[ ] sh down.sh && sh up.sh
[ ] Wait for initialization_app to complete
[ ] Verify no boot errors (should see "Total M3 facets registered: 28")
```

### Phase 2: Collection Check (2 min)

```bash
[ ] Check which collections exist:
      curl -s "http://localhost:8983/solr/admin/collections?action=LIST&wt=json" | jq '.collections'

[ ] Count docs in hydra-production:
      curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'
      Record: __________

[ ] Check if UUID collection exists and has docs:
      curl -s "http://localhost:8983/solr/uuid/select?q=*:*&rows=0" 2>/dev/null | jq '.response.numFound'
      Record: __________

DECISION:
  [ ] hydra-production empty → Proceed to Phase 3 (REINDEX)
  [ ] hydra-production has docs → Run full diagnostics instead
  [ ] UUID has docs, hydra-production empty → Collection mismatch confirmed → REINDEX
```

### Phase 3: Run Reindex (5 min)

```bash
[ ] Execute reindex:
      docker compose exec -T web bundle exec rake solr:index

[ ] Monitor progress:
      docker compose logs web -f | grep -i index

[ ] After completion, verify new doc count:
      curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'
      Record: __________

DECISION:
  [ ] Doc count increased → Data now indexed → Proceed to Phase 4 (TEST)
  [ ] Doc count still 0 → Reindex failed/returned nothing → Run full diagnostics
```

### Phase 4: Test Search Results (2 min)

```bash
[ ] Quick Rails test:
      docker compose exec -T web bundle exec rails runner \
        "results = Blacklight.repository.search({}); \
         puts \"Found: #{results.total}; Returned: #{results.documents.count}\""
      Record: Total: _____, Returned: _____

[ ] Browser test:
      Navigate to: https://demo-hykudev.lib.wvu.edu/catalog?q=*
      [ ] Results display? YES / NO

DECISION:
  [ ] Results display → ✅ REINDEX FIXED IT! Document and add to upgrade procedure
  [ ] Still no results → Problem is not data location → Run full diagnostics
```

### Phase 5: Document Findings (5 min)

```bash
[ ] Update synthesis report:
      /Users/tam0013/Documents/git/agent-tasks/projects/wvu_knapsack/summaries/2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md

[ ] Record:
      [ ] Collections present: _________________
      [ ] Docs before reindex: _________________
      [ ] Docs after reindex: _________________
      [ ] Blacklight results: total _____, returned _____
      [ ] Browser test: YES / NO

[ ] Conclusion:
      [ ] Reindex fixed it → Collection mismatch or schema incompatibility
      [ ] Reindex didn't work → Need deeper investigation (schema or query builder issue)

[ ] Commit findings to agent-tasks
```

---

## 📊 Expected Results

### ✅ If Reindex Works
```
Timeline: ~20 minutes total
Result: Search results display on hykudev
Diagnosis: Solr indexing issue (data in UUID, needs reindex, or old schema incompatible)
Fix: Add `rake solr:index` to production upgrade procedure
Acceptance: AC #1-5 all met ✅
```

### ⚠️ If Reindex Doesn't Work
```
Timeline: Initial 10 min + 25 min diagnostics = 35 min total
Result: Search still doesn't display
Diagnosis: Deeper issue (schema mismatch or query builder)
Next: Run full diagnostic suite
  - schemas/diagnose_search_results.sh
  - Compare fields between local and hykudev
  - Inspect Rails search builder configuration
  - Check Blacklight field mappings
```

---

## 📍 Quick Reference Docs

| Need | Location |
|------|----------|
| **Reindex commands** | `handoffs/2026-11-19-REINDEX-PROCEDURE.md` |
| **Full diagnostics** | `handoffs/2026-11-19-SEARCH-RESULTS-DIAGNOSTIC-PLAN.md` |
| **Strategy overview** | `handoffs/2026-11-19-REINDEX-FIRST-STRATEGY.md` |
| **Diagnostic script** | `wvu_knapsack/diagnose_search_results.sh` |
| **Synthesis report** | `summaries/2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md` |

---

## 🎓 Key Insights

1. **Code works locally** (Stack Car) = Not a code bug
2. **Same code fails on VM** (hykudev) = Data/Indexing issue
3. **Facets load, results don't** = Data is in Solr, but query doesn't find it
4. **LaRita suggests reindex** = Solr indexing is likely culprit
5. **Oct bug was collection mismatch** = UUID vs hydra-production, needs reindex

---

## ✅ Success = When This Is Done

```
[ ] Reindex completed on hykudev
[ ] Doc count before/after recorded
[ ] Search tested in browser
[ ] Result documented (works / doesn't work)
[ ] Synthesis report updated with findings
[ ] Next step identified (add to upgrade procedure OR run diagnostics)
```

**All acceptance criteria will be satisfied once findings are documented.**

---

**Ready to proceed? Start with Phase 1 (Hykudev Prep)** ✅

You've got this! The strategy is sound: Code works → Reindex first → Document → Done.
