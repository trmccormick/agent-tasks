---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist

- [x] Agent Dispatch Interface section is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template is provided (copy/paste ready)
- [x] No placeholder text remains
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface

```
You are **Qwen (Investigation Agent)** working on **WVU Libraries Knapsack**.

Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-BISECT-COMMIT-REGRESSION.md

STEP 0 — MOVE TO ACTIVE:
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-BISECT-COMMIT-REGRESSION.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-21-HIGH-BUGFIX-BISECT-COMMIT-REGRESSION.md
  git commit -m "task: move commit bisect investigation to active"

STEP 1 — UNDERSTAND:
  Production (demo-hykudev) works fine at commit 0cc0230
  Local development NOT working - facets show 36+ items, not limited to 5
  Goal: Find which commit broke facet display and/or Wings initialization
  
STEP 2 — BINARY SEARCH:
  Starting from 0cc0230 (known working), incrementally apply commits
  Test each one to find the exact breaking point
  See detailed test steps below

STEP 3 — REPORT:
  Synthesis report with exact commit SHA that broke facets
  Include test results for each commit
```

---

## 📋 TASK: Binary Search Commits to Find Regression

**Objective**: Identify exactly which commit(s) introduced:
1. Facet display bug (36+ items instead of 5)
2. Wings::ModelRegistry deserialization errors (if any)
3. Search results not displaying

**Known Facts**:
- ✅ Production (demo-hykudev): Commit 0cc0230 = working correctly
- ❌ Local dev: Current HEAD = facets not limited, may have Wings issues
- Branch: fix/hide-type-facet-add-show-more-facets

---

## 🧪 Investigation Steps

### STEP 1: Get Commit List

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# See all commits from production (0cc0230) to current HEAD
git log --oneline 0cc0230..HEAD

# Record these in your report
```

**Example output** (may be different):
```
435c378 fix: use after_initialize for decorator timing
952e897 docs: remove outdated to_prepare comment
7971e53 Revert "fix: ensure CatalogControllerDecorator is loaded..."
7cf01c0 Revert "fix: move prepend to initializer..."
ce0ff2d fix: move prepend to initializer...
2b148dd fix: ensure CatalogControllerDecorator is loaded...
60c9463 fix: use CatalogSearchBuilderWrapper pattern...
ba4317b debug: add initialize hook...
```

### STEP 2: Establish Baseline (Known Working)

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Checkout production commit
git checkout 0cc0230

# Restart stack
sh down.sc.local.sh && sleep 5 && sh up.sc.local.sh

# Wait 2-3 minutes for container startup
# Then navigate to: https://demo-wvu-knapsack.localhost.direct/catalog?search_field=all_fields&q=
```

**Test Results at 0cc0230**:
- [ ] Facets visible: YES/NO
- [ ] People Represented showing: [NUMBER] items (expected: any number is baseline)
- [ ] "More" link present: YES/NO
- [ ] Search results display: YES/NO
- [ ] Wings errors in logs: YES/NO

**Document this as baseline.**

### STEP 3: Incremental Testing

For EACH commit after 0cc0230:

```bash
# Get the next commit after current
git log --oneline 0cc0230..HEAD | tail -1  # Gets oldest first
# Or manually from your list, oldest to newest

# Apply next commit
git checkout [COMMIT_SHA]

# Restart stack
sh down.sc.local.sh && sleep 5 && sh up.sc.local.sh

# Wait 2-3 minutes, then test
```

**For each commit, test**:
1. Navigate to catalog: `https://demo-wvu-knapsack.localhost.direct/catalog?search_field=all_fields&q=`
2. Count items in "People Represented" facet (or any facet visible)
3. Check if "More" link present
4. Check if search results display
5. Check Rails logs for Wings errors: `docker logs wvu_knapsack-web-1 2>&1 | grep -i "Wings::ModelRegistry\|DeserializationError" | head -5`

**Record for each commit**:
```
Commit: [SHA]
Message: [commit message]
Facet count (People Represented): [NUMBER or "error"]
"More" link: [YES/NO]
Search results: [YES/NO]
Wings errors: [YES/NO]
Status: [✅ WORKING / ❌ BROKEN - facets not limited / ❌ BROKEN - Wings error / ❌ BROKEN - other]
```

### STEP 4: Identify Culprit Commits

Once you find a broken commit:
- **Note which one broke what** (facets? Wings? Both?)
- **Continue testing remaining commits** to see if any break additional things
- **Record all problematic commits**

Example findings:
- Commit 60c9463: ✅ facets working, no Wings error
- Commit 2b148dd: ❌ facets NOT limited (36+ items), no Wings error
- Commit ce0ff2d: ❌ facets NOT limited, Wings deserialization error
- etc.

---

## 📝 SYNTHESIS REPORT TEMPLATE

```
## Commit Regression Investigation — Binary Search Results

**Date Tested**: [TODAY]
**Branch**: fix/hide-type-facet-add-show-more-facets
**Baseline**: 0cc0230 (production/working)
**Tested By**: Qwen

### BASELINE (0cc0230 — Known Working)
- Facets limited to: [NUMBER] items
- "More" link: [YES/NO]
- Search results display: [YES/NO]
- Wings errors: [YES/NO]

### COMMIT-BY-COMMIT RESULTS

#### Commit: [SHA]
- Message: [commit message]
- Facet count: [NUMBER]
- "More" link: [YES/NO]
- Search results: [YES/NO]
- Wings errors: [YES/NO]
- Status: ✅ WORKING / ❌ BROKEN (describe what broke)

#### Commit: [SHA]
- Message: [commit message]
- Facet count: [NUMBER]
- "More" link: [YES/NO]
- Search results: [YES/NO]
- Wings errors: [YES/NO]
- Status: ✅ WORKING / ❌ BROKEN (describe what broke)

[Continue for all commits...]

### CULPRIT COMMITS IDENTIFIED

**Facet Display Bug**:
- Introduced in: [COMMIT_SHA]
- Message: [commit message]
- Symptom: Facets show [NUMBER] items instead of limited to 5
- Fix approach: [revert? redesign?]

**Wings Deserialization Error**:
- Introduced in: [COMMIT_SHA]
- Message: [commit message]
- Symptom: Wings::ModelRegistry not available
- Fix approach: [revert? redesign?]

**Other Bugs**:
- [if any]

### COMMITS THAT ACTUALLY HELPED (if any)
- [Which commits fixed something?]
- [Which commits were reverts that helped?]

### RECOMMENDATION FOR NEXT STEPS
Based on findings:
1. [Revert commit X and Y]
2. [Keep commit Z because it fixed something]
3. [Need new approach for facet limiting]
4. [Testing order for fixes]

### EVIDENCE
```bash
# Example test results:
Commit 0cc0230: facets show 5, more link yes, results yes, wings no-error
Commit 60c9463: facets show 5, more link yes, results yes, wings no-error
Commit 2b148dd: facets show 36, more link no, results yes, wings no-error
...
```
```

---

## 🎯 Success Criteria

✅ **Report includes**:
1. List of all commits tested (oldest to newest after 0cc0230)
2. Facet count for each commit
3. Exact SHA of commit that broke facet display
4. Exact SHA of commit that broke Wings (if different)
5. Clear recommendation: "Revert X, keep Y, try Z next"

---

## 📌 Important Notes

- **Test in order** (oldest to newest) so you know when it broke
- **Restart stack for each commit** - no shortcuts on that
- **Wait 2-3 minutes** for web container after restart
- **Check both facet count AND search results** - they're separate issues
- **Record Wings errors** even if facets still work - they may be related

---

## ❌ Do NOT

- Do not skip commits - test sequentially
- Do not test multiple commits then restart (isolation is key)
- Do not assume "oh this commit looks harmless, skip it"
- Do not guess - measure exactly (count facet items, check logs)

## ✅ DO

- Do test systematically oldest to newest
- Do restart stack for EACH commit
- Do document everything
- Do provide exact commit SHAs in report
