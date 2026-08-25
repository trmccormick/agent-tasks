---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies clear (BLOCKED by GoodJob debug task)

**Task is READY TO DISPATCH once GoodJob task completes.**

**BLOCKER**: Do NOT start this task until GoodJob queue is resolved and indexing completes.

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Qwen (Implementation Agent)** working on **WVU Libraries Knapsack**.

Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-TEST-CATALOG-FACET-LIMITING.md

PREREQUISITE: GoodJob queue must be resolved (no more Wings::ModelRegistry errors).

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE:
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-TEST-CATALOG-FACET-LIMITING.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-21-HIGH-BUGFIX-TEST-CATALOG-FACET-LIMITING.md
  git commit -m "task: move catalog facet limiting test to active dispatch"

STEP 1 — READ PROJECT CONTEXT:
  cat /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md

STEP 2 — UNDERSTAND THE SOLUTION:
  Read /Users/tam0013/Documents/git/wvu_knapsack/app/search_builders/catalog_search_builder_wrapper.rb
  Read /Users/tam0013/Documents/git/wvu_knapsack/config/initializers/load_catalog_controller_decorator.rb
  Read /Users/tam0013/Documents/git/wvu_knapsack/app/controllers/catalog_controller_decorator.rb

STEP 3 — FOLLOW TEST STEPS:
  1. Restart stack (code already loaded, just need fresh container state)
  2. Navigate to catalog search
  3. Verify facets show max 5 items + "More" link
  4. Verify search results display
  5. Test multiple facets (not just People Represented)

STEP 4 — REPORT RESULTS:
  Create SYNTHESIS REPORT using template at end of this file.
  If all passing → Ready for production deployment
  If any failing → Debug and report blockers
```

---

## 📋 TASK: Test & Verify Catalog Facet Limiting Implementation

**Objective**: Verify that catalog search facets are properly limited to 5 items with "More" link, matching homepage behavior.

**Context**:
- Branch: `fix/hide-type-facet-add-show-more-facets`
- Commits: ce0ff2d (latest - prepend fix), 2b148dd (initializer), 60c9463 (wrapper)
- Implementation: CatalogSearchBuilderWrapper + decorator with search_builder_class override
- Production: Currently at older commit without these fixes

**Success = All facets limited to 5 items + "More" link + search results display correctly**

---

## 🧪 Test Procedure

### STEP 1: Restart Stack with Latest Code

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Restart Stack Car (already on this branch with changes)
sh down.sc.local.sh && sleep 5 && sh up.sc.local.sh

# Wait for web container to be ready (2-3 minutes typically)
# Verify by checking logs:
sc logs web 2>&1 | tail -20
# Should see: "Listening on [IP]:3000"
```

### STEP 2: Navigate to Catalog Search

Open browser to: `https://demo-wvu-knapsack.localhost.direct/catalog?search_field=all_fields&q=`

**What you should see**:
- Left sidebar with facet fields
- Main content area with search results (may be empty if no query)
- Facet list showing counts

### STEP 3: Test Facet Display — People Represented

1. **Locate facet**: Look for "People Represented" in left sidebar
2. **Click to expand**: Click the facet name/button to expand the list
3. **Count items**: How many items show in the expanded list?
   - **Expected**: Exactly 5 items (or fewer if fewer than 5 exist in Solr)
   - **If you see 36+ items**: ❌ FAIL — facet limiting not working
4. **Check "More" link**: If more than 5 items exist in Solr
   - Must show "More..." link at bottom of facet list
   - Click it: Should navigate to `/catalog/facet/people_represented_sim`
   - That page should show ALL available values

**Document results**:
- How many items showing? [e.g., "5", "36+", etc.]
- Is "More" link present? [Yes/No]
- Can you click it? [Yes/No]

### STEP 4: Test Other Facets (Regression Test)

Pick at least 2 more facets from the sidebar and repeat STEP 3:

**Recommended facets to test**:
- Creator
- Subject
- Keyword
- Location (based_near_label_sim)

**Each should**:
- Show max 5 items
- Have "More" link if > 5 values exist
- Be clickable

### STEP 5: Test Search Results Display

1. Enter a search term: `q=test` in the search box
2. Run the search
3. **Check results**:
   - Results should display in main content area
   - Each result should show title, metadata
   - Pagination controls should work (if multiple pages)

**Document**: 
- Do search results appear? [Yes/No]
- Are they clickable? [Yes/No]

### STEP 6: Homepage Regression Test

Navigate to: `https://demo-wvu-knapsack.localhost.direct/?locale=en`

**Check**:
- Homepage facets still show 5 items + "More" link
- No degradation from our catalog changes
- Homepage still works correctly

---

## 🎯 Acceptance Criteria (ALL must pass)

- [ ] **People Represented facet**: Shows max 5 items (NOT 36+)
- [ ] **"More" link present**: When > 5 items exist
- [ ] **Other facets limited**: Creator, Subject, Keyword all show max 5
- [ ] **Search results display**: Results appear in main content area
- [ ] **Homepage facets work**: Homepage facets still show 5 items + "More"
- [ ] **No errors in logs**: `sc logs web 2>&1 | grep -i error` shows no catalog/facet errors
- [ ] **Git status clean**: All code changes committed, branch is clean

---

## 📝 SYNTHESIS REPORT TEMPLATE (COPY & PASTE)

```
## Catalog Facet Limiting Test Results

**Date Tested**: [TODAY]
**Branch**: fix/hide-type-facet-add-show-more-facets
**Tested By**: Qwen

### TEST RESULTS

#### People Represented Facet
- Items showing: [NUMBER]
- "More" link present: [YES/NO]
- "More" link clickable: [YES/NO]
- Status: [✅ PASS / ❌ FAIL]

#### Other Facets (Creator, Subject, Keyword)
- Creator: [NUMBER] items, "More" link: [YES/NO] → [✅ PASS / ❌ FAIL]
- Subject: [NUMBER] items, "More" link: [YES/NO] → [✅ PASS / ❌ FAIL]
- Keyword: [NUMBER] items, "More" link: [YES/NO] → [✅ PASS / ❌ FAIL]

#### Search Results Display
- Results appearing: [YES/NO]
- Results clickable: [YES/NO]
- Status: [✅ PASS / ❌ FAIL]

#### Homepage Regression
- Homepage facets still working: [YES/NO]
- Status: [✅ PASS / ❌ FAIL]

#### Rails Logs
```bash
# Any errors?
[PASTE OUTPUT]
```

### OVERALL RESULT
- [ ] ✅ ALL TESTS PASSING — Ready for production deployment
- [ ] ⚠️ PARTIAL FAILURE — Specific items failing (list above)
- [ ] ❌ CRITICAL FAILURE — Facets not limited, need debugging

### IF FAILING
Describe what's broken:
- Which facet? [People Represented / Other]
- What shows instead? [36 items / error / etc.]
- Error messages: [PASTE FROM LOGS]

### NEXT STEPS
- [ ] Proceed to production deployment
- [ ] Investigate and iterate (provide findings)
```

---

## 🏗️ Technical Background

**The Problem**: 
- Catalog facets showed unlimited items (e.g., People Represented: 36+ items)
- Homepage showed 5 items with "More" link
- Inconsistent UX, poor performance with high-cardinality facets

**Root Cause**:
- Blacklight's `limit: 5` config only controls UI display, NOT Solr request
- Catalog was sending `facet.limit => unlimited` to Solr
- View displayed first 5, but Solr queried all 36+

**The Solution**:
1. **CatalogSearchBuilderWrapper** (`app/search_builders/catalog_search_builder_wrapper.rb`)
   - Extends AdvSearchBuilder
   - Overrides `build()` method
   - Enforces `f.{field_name}.facet.limit` from Blacklight config
   - Limits Solr request, not just UI display

2. **CatalogControllerDecorator** (`app/controllers/catalog_controller_decorator.rb`)
   - Overrides `search_builder_class` to return CatalogSearchBuilderWrapper
   - Configured facet fields with limit: 5, show_more: true
   - Removed Type facet (not needed for WVU theme)

3. **Initializer** (`config/initializers/load_catalog_controller_decorator.rb`)
   - Loads decorator in to_prepare block
   - Ensures decorator applied when CatalogController is defined
   - Timing is critical for proper initialization

---

## 🚀 After Testing

**If ALL tests pass**:
1. Push branch to origin
2. Notify user: "Ready for production deployment"
3. Create handoff note for deployment agent

**If ANY test fails**:
1. Do NOT push branch
2. Investigate specific failure
3. Create detailed bug report with:
   - What's broken
   - Expected vs. actual
   - Rails logs showing error
   - Recommendation for fix

---

## ❌ Do NOT

- Do not run `sh up.sc.local.sh` multiple times in quick succession
- Do not test without waiting for stack to be fully ready
- Do not assume "More" link presence without checking
- Do not skip homepage regression test

## ✅ DO

- Do wait 2-3 minutes after `sh up.sc.local.sh` for web container startup
- Do count facet items exactly (not guess)
- Do test multiple facets, not just People Represented
- Do check Rails logs for any errors
