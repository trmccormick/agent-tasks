---
status: active
resume: false
priority: CRITICAL
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-10-30-CRITICAL-BUG-SEARCH-RESULTS-NOT-DISPLAYING-HYKU-720-UPGRADE.md

STEP 0 — TASK ALREADY IN ACTIVE STATUS (skip git mv):
This task has been moved to active/ by Tracy. Status is already: active
Proceed to Step 1 and begin investigation.

LIFECYCLE: This is continuation work on ongoing Hyku 7.2.0 upgrade debugging.
Previous commits: f49e39b, 399d4d8, fb6b56a (all on feature/hyku-v7.2.0-upgrade)
```

---

## Overview

**Search results are not displaying on hykudev (feature/hyku-v7.2.0-upgrade branch)**

- Symptom: Homepage and facet searches show facet counts but zero result documents
- Example: "Folk Music" facet shows "872" items but search returns empty results pane
- Scope: Issue is ONLY on feature/hyku-v7.2.0-upgrade; production (main/digitalhistory.lib.wvu.edu) works correctly
- Status: initialize_app now completes successfully (after fix fb6b56a), but search results still not displaying on hykudev

---

## Context

### Branch Status
- **main**: v7.2.0 production (https://digitalhistory.lib.wvu.edu) — WORKING correctly
- **feature/hyku-v7.2.0-upgrade**: Active development on hykudev — BROKEN (no search results)
- **hyrax-webapp submodule**: origin/7.2-stable (commit 279fe062) — includes PR #3369 (facet limiting)

### Recent Commits (feature/hyku-v7.2.0-upgrade)
```
fb6b56a (HEAD) fix: Handle boolean limit values in facet configuration check
399d4d8 fix: Restore YAML-driven facet configuration and limit application
f49e39b fix: Restore complete configure_blacklight block in catalog_controller_decorator.rb
bc534ba fix: Restore facet 'more' links to navigate to catalog/facet/{field}
3098226 fix: Use hyrax-webapp 7.2-stable for PR #3369 facet limiting (v7.2.1 tag lacks this PR)
687af48 refactor: Complete surgical trim of decorator — remove search_builder_class reference
884c788 refactor: Remove obsolete facet limiting workaround (upstream PR #3369)
```

### What Changed (Commits 884c788 & 687af48)
These commits removed custom search-building logic:
1. **Deleted**: `app/search_builders/catalog_search_builder.rb` (custom facet.limit+1 injection)
2. **Deleted**: `config/initializers/catalog_search_builder.rb` (registration of custom search builder)
3. **Deleted**: `config/wvu_facet_defaults.yml` (facet defaults)
4. **Removed**: `config.search_builder_class = ::CatalogSearchBuilder` line from initializer
5. **Removed**: YAML-driven facet limit application code

**Rationale**: Assumed Hyku PR #3369 provides equivalent facet limiting globally

### Why Assumption Failed
- PR #3369 provides a global `config.default_facet_limit` setting
- But facets still need EXPLICIT limit configuration for Blacklight to work correctly
- Without it: Solr queries mismatched between facet and result parameters
- Result: Facets load (facet params work) but main search returns empty

### Fixes Applied (Tracy)
1. **fb6b56a**: Fixed NoMethodError by changing `.zero?` to `== 0` (some facet limits are boolean)
2. **399d4d8**: Restored YAML facet configuration + dynamic _sim field limit application
3. **f49e39b**: Restored complete configure_blacklight block structure (fixed premature close)

### Current Blocker
initialize_app now completes successfully (no errors). But **search results still don't display on hykudev**.

This means:
- YAML/facet limit restoration may not be sufficient
- OR it didn't actually get applied
- OR the real issue is something else entirely (search_builder_class removal broke core query logic)

---

## Acceptance Criteria

- [ ] AC1: Verify initialize_app completes on hykudev test run
- [ ] AC2: Pull latest code on hykudev, restart containers (down.sh → up.sh)
- [ ] AC3: Reproduce search results issue OR confirm it's fixed
- [ ] AC4: If still broken: Identify root cause by comparing Solr queries (hykudev vs. production)
- [ ] AC5: Create a fix (OR document why facet limiting restoration doesn't solve it)
- [ ] AC6: Run local testing (car version) to confirm fix works
- [ ] AC7: Document findings and next steps in synthesis report

---

## Step 1 — Test on hykudev

**Environment**: hykudev.lib.wvu.edu (RHEL, feature/hyku-v7.2.0-upgrade branch)

```bash
# SSH to hykudev
ssh tam0013@hykudev.lib.wvu.edu
cd /srv/wvu_knapsack

# Pull latest (should include fb6b56a)
git pull origin feature/hyku-v7.2.0-upgrade
git log --oneline -3  # Verify fb6b56a is present

# Restart containers (full clean restart)
sh down.sc.local.sh
sh up.sc.local.sh

# Monitor initialize_app
docker logs -f wvu_knapsack-initialize_app-1
# Wait for: "Total M3 facets registered: 28" and completion (exit 0)

# Once initialize_app completes and web is healthy:
# Open browser: https://hykudev.lib.wvu.edu/
```

**What to test:**
1. Homepage loads
2. Click a facet (e.g., "Folk Music" or any facet with count > 0)
3. Observe results pane — do result documents appear?
4. If YES: Search results issue is FIXED ✅
5. If NO: Issue persists → proceed to Step 2

---

## Step 2 — Investigate Root Cause (if still broken)

**Goal**: Determine why search results aren't displaying despite facet counts showing.

### Investigation A: Solr Query Comparison

The issue: facets load (facet.limit working) but main query returns 0 documents. This suggests parameter mismatch.

```bash
# On hykudev, open browser network tab (F12 → Network)
# Click a facet with results (e.g., "Folk Music")
# Find the XHR request to Solr

# In Network tab, click the request and examine URL
# Look for these parameters:
#   - q=*:*  (or specific search query)
#   - fq=...  (filter queries)
#   - facet.limit=5  (should be set)
#   - facet.field=...  (facet fields being requested)
#   - rows=...  (number of results requested)

# Compare this URL against production (digitalhistory.lib.wvu.edu) same search
# Key differences to check:
#   - Are facet.limit parameters identical?
#   - Are filter queries (fq) matching between facet and result queries?
#   - Is the main query (q=) correct or empty?
#   - Are row counts different?
```

### Investigation B: Rails Log Analysis

```bash
# On hykudev, tail Rails logs while performing search
docker logs -f wvu_knapsack-web-1 2>&1 | grep -i "search\|solr\|facet"

# Look for:
#   - Any exceptions in search code
#   - Solr query parameters being built
#   - Rails-level errors before Solr query
#   - Comparison: does search_builder or search params look wrong?
```

### Investigation C: Code Diff (main vs. upgrade branch)

```bash
# On local machine, in wvu_knapsack repo
cd /Users/tam0013/Documents/git/wvu_knapsack

# Compare critical search files between branches
git diff main..feature/hyku-v7.2.0-upgrade -- \
  app/controllers/catalog_controller_decorator.rb \
  config/initializers/catalog_controller_decorator.rb \
  app/search_builders/ \
  lib/blacklight/

# Key files to check:
# - app/search_builders/ (custom search builders — were any deleted?)
# - config/initializers/ (search builder registration)
# - lib/blacklight/ (controller decorator, search context)
#
# For each file: determine if removal breaks core search functionality
```

### Investigation D: Verify facet limit application (debugging)

```ruby
# Add temporary debug to config/initializers/catalog_controller_decorator.rb after facet registration loop:

Rails.logger.info("=== FACET CONFIGURATION SUMMARY ===")
config.facet_fields.each do |key, field_config|
  Rails.logger.info("Facet: #{key} | limit: #{field_config.limit.inspect} | show_more: #{field_config.show_more.inspect}")
end
Rails.logger.info("=== END FACET SUMMARY ===")
```

Then restart containers and check logs. Verify facet limits are numeric (5), not nil or boolean.

---

## Step 3 — Root Cause Hypothesis

**Most likely scenarios:**

1. **Hypothesis A: search_builder_class removal broke core query logic**
   - Removed custom CatalogSearchBuilder may have had more than just facet.limit+1 injection
   - Without it: main search query parameters not built correctly
   - Evidence: facets work (facet params correct) but results empty (query params broken)
   - Investigation: Compare deleted CatalogSearchBuilder with upstream Hyrax/Hyku search builders

2. **Hypothesis B: Facet limit application still incomplete**
   - fb6b56a fixed NoMethodError, but facet limits may still not be applied correctly
   - Evidence: Solr facet queries working but result query has wrong parameters
   - Investigation: Debug output should show facet limits are numeric

3. **Hypothesis C: Upstream PR #3369 doesn't replace custom logic exactly**
   - PR #3369 provides `config.default_facet_limit` but facets need individual config
   - Tracy's fix (399d4d8) attempts to apply limits dynamically but may miss edge cases
   - Evidence: Some facets work, others don't (edge case pattern)
   - Investigation: Compare Tracy's restoration code with what was deleted in 884c788

---

## Step 4 — Create Fix or Document Finding

**Depending on root cause from Step 2:**

### If HC A (search_builder_class removal):
- Investigate what custom CatalogSearchBuilder actually did
- Compare with Hyrax/Hyku SearchBuilder to find missing logic
- Restore OR reimplement missing functionality
- OR file issue with Hyku about PR #3369 not being sufficient

### If HC B (facet limit application incomplete):
- Review debug output from Step 2D
- Extend facet limit application to catch edge cases
- Test again

### If HC C (PR #3369 incomplete):
- Keep Tracy's restoration (399d4d8) as a workaround
- Test on both hykudev and local to confirm
- Commit fix with clear explanation of why upstream PR not sufficient

---

## Step 5 — Local Testing (car version)

Once you have a fix working on hykudev:

```bash
# On local machine
cd /Users/tam0013/Documents/git/wvu_knapsack
git checkout feature/hyku-v7.2.0-upgrade
git pull origin feature/hyku-v7.2.0-upgrade  # Get latest fix

# Start local stack (use car version if configured)
./up.sh

# Test search:
# 1. Visit http://localhost:3000/
# 2. Click facets
# 3. Verify results display (not empty)
# 4. Repeat with multiple facets

# Check Rails logs for any errors
docker logs -f wvu_knapsack-web-1 2>&1 | grep -i "search\|error\|facet"
```

If local testing passes → fix is verified ✅

---

## Step 6 — Synthesis Report

Document your findings in a report including:

1. **Problem Statement** (copy from Overview above)
2. **Investigation Steps** (which steps from Step 2 you ran and results)
3. **Root Cause** (which hypothesis was correct)
4. **Solution Applied** (fix code/commits if any)
5. **Testing Results** (hykudev + local, both PASS or both FAIL)
6. **Next Steps** (if issue unresolved, what needs to happen Monday)

Format for synthesis:
```
# Search Results Issue — Investigation Report

## Problem
[symptom, scope, current commits]

## Investigation Method
[which debugging steps were run]

## Root Cause Identified
[which hypothesis, evidence]

## Solution
[fix applied or reason it can't be fixed immediately]

## Test Results
- hykudev: PASS / FAIL
- local: PASS / FAIL
- Evidence: [describe what you see]

## Conclusion
[is issue resolved? blockers for Monday?]
```

---

## Dependencies

- feature/hyku-v7.2.0-upgrade branch must be current
- Commits f49e39b, 399d4d8, fb6b56a must be present locally
- hykudev access (SSH) for live testing
- Local dev environment for car version testing

---

## Architecture Gotchas

1. **Facet vs. Result Query Mismatch**
   - Blacklight sends TWO Solr queries: one for facets, one for results
   - If facet.limit applied to facet query but NOT result query: facets load but results empty
   - Search builders must apply limits consistently to both

2. **Boolean vs. Integer Limit Values**
   - Some Blacklight facets have `limit: true` instead of `limit: 5`
   - Can't call `.zero?` on boolean — must use `== 0` comparison
   - fb6b56a already fixes this, but watch for other boolean handling

3. **PR #3369 Assumptions**
   - Does NOT automatically apply limits to all facets
   - Only provides a global fallback setting
   - Individual facets still need explicit configuration
   - Removing custom search_builder assumes upstream handles it — verify this assumption

4. **M3 Flexible Metadata Facets**
   - 28 facets auto-registered from active FlexibleSchema
   - Must have limits applied dynamically, not in fixed config
   - If limit application fails: all 28 facets could be broken

---

## Files to Check

- [app/controllers/catalog_controller_decorator.rb](app/controllers/catalog_controller_decorator.rb) — facet registration, configure_blacklight block
- [config/initializers/catalog_controller_decorator.rb](config/initializers/catalog_controller_decorator.rb) — M3 facet registration, limit application
- [config/wvu_facet_defaults.yml](config/wvu_facet_defaults.yml) — facet defaults (recreated in 399d4d8)
- hyrax-webapp submodule (origin/7.2-stable) — PR #3369 implementation

---

## Commits Reference

```
fb6b56a: fix: Handle boolean limit values in facet configuration check
  - Changed .zero? to == 0 to handle boolean limit values
  - Fixes NoMethodError: undefined method 'zero?' for true
  - Allows initialize_app to complete

399d4d8: fix: Restore YAML-driven facet configuration and limit application
  - Restored config/wvu_facet_defaults.yml
  - Restored dynamic _sim field limit application
  - Assumes facet limiting not properly provided by upstream PR #3369

f49e39b: fix: Restore complete configure_blacklight block in catalog_controller_decorator.rb
  - Fixed premature closing of configure_blacklight block (line placement)
  - Allows facet configuration to complete without errors

884c788: refactor: Remove obsolete facet limiting workaround (upstream PR #3369)
  - DELETED: app/search_builders/catalog_search_builder.rb
  - DELETED: config/initializers/catalog_search_builder.rb
  - DELETED: config/wvu_facet_defaults.yml
  - REMOVED: config.search_builder_class reference
  - REMOVED: YAML facet limit application code
  - Assumed upstream PR #3369 would replace all this functionality (assumption failed)

687af48: refactor: Complete surgical trim of decorator — remove search_builder_class reference
  - Removed custom search_builder_class registration
  - Removed YAML facet configuration code
  - Assumed Blacklight config alone sufficient (assumption failed)
```

---

## Questions for Qwen

If you get stuck:

1. **Is PR #3369 in hyrax-webapp actually providing equivalent facet limiting?**
   - How does it differ from the removed custom CatalogSearchBuilder?
   - Does it set facet.limit in Solr queries the same way?

2. **Are facets and results using the same search builder?**
   - Do both queries go through the same parameter building logic?
   - Or do they diverge (facet query works, result query broken)?

3. **Was there other logic in CatalogSearchBuilder besides facet.limit+1?**
   - What did add_facetting_to_solr actually do?
   - Is there more than just limit injection?

---

## Previous Work (Tracy's Session)

All commits pushed to feature/hyku-v7.2.0-upgrade on GitHub.
All three fixes (f49e39b, 399d4d8, fb6b56a) are in place.
initialize_app now completes.
Search results still not displaying (reason unknown — needs debugging).
At 99% token usage, handing off to Qwen for investigation.
