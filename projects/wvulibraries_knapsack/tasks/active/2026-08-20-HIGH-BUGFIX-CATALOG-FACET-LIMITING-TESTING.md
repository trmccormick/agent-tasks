# TASK: Test & Verify Catalog Facet Limiting (CatalogSearchBuilderWrapper)
**Status**: ACTIVE — Ready for Qwen local agent  
**Priority**: HIGH  
**Type**: BUG-FIX  
**Date Created**: 2026-08-20  
**Assigned To**: Qwen (local agent via Copilot)  
**Workspace**: `/Users/tam0013/Documents/git/wvu_knapsack`  

---

## 📋 Minimal Handoff (Paste this to Qwen's initial prompt)

```
You are working on WVU Libraries Knapsack.

TASK: Test catalog facet limiting implementation (CatalogSearchBuilderWrapper).
- Read: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md (section "2026-08-20 — Catalog Facet Limiting")
- Branch: fix/catalog-facet-limiting-solr-level
- Goal: Verify "People Represented" facet shows max 5 items in catalog (not 36)
- Follow: ACCEPTANCE CRITERIA section below

Start by creating a STATUS REPORT confirming your understanding.
```

---

## 🎯 Acceptance Criteria (Definition of Done)

ALL of these must pass for task completion:

1. ✅ **Catalog page loads** on local Stack Car without routing errors
   - URL: `https://admin-wvu-knapsack.localhost.direct/catalog?locale=en`
   - Must show search results and facets panel (left sidebar)

2. ✅ **"People Represented" facet expands and shows LIMITED items**
   - Click facet button to expand
   - Count items displayed: Must be ≤ 5 items (not 36)
   - Verify field name: "People Represented" (not untranslated key)

3. ✅ **"More" link appears when applicable**
   - If > 5 items exist in Solr: "More" link must appear
   - Click "More" link: Must navigate to `/catalog/facet/people_represented_sim` page
   - That page must show ALL available values for the facet

4. ✅ **Other facets also respect the 5-item limit**
   - Test at least 2 other facets: Creator, Subject, Keyword
   - Each must show max 5 items with "More" link if applicable

5. ✅ **Homepage facets still work** (regression test)
   - Navigate to: `https://admin-wvu-knapsack.localhost.direct/?locale=en`
   - Verify homepage facets still show 5 items + "More" link
   - No degradation from catalog changes

6. ✅ **Console logs show search builder wrapper being used**
   - Run: `sc logs web 2>&1 | grep -i "catalogsearch\|facet" | head -10`
   - Look for evidence that CatalogSearchBuilderWrapper build() method executed
   - OR: Add Rails.logger.info to wrapper and check logs after catalog search

7. ✅ **Branch is clean & commits are clear**
   - Branch: `fix/catalog-facet-limiting-solr-level`
   - Commits should be minimal and descriptive
   - No uncommitted changes when complete

---

## 🔧 Technical Background (Context)

**The Problem**: Catalog facets showed unlimited items (e.g., People Represented: 36 items) while homepage showed 5 items with "More" link.

**Root Cause**: 
- Blacklight's `limit: 5` config only controls UI display, not Solr request
- Catalog was sending `facet.limit => unlimited` to Solr, getting all values back
- View displayed only first 5, but Solr was still queried for all

**The Solution** (already implemented):
- Created `CatalogSearchBuilderWrapper` class that extends `AdvSearchBuilder`
- Overrides `build()` method to enforce `facet.limit` from Blacklight config
- Similar to proven `HomepageSearchBuilderWrapper` pattern
- Updated `CatalogController` decorator to inject the wrapper

**Files Changed**:
- ✅ NEW: `app/search_builders/catalog_search_builder_wrapper.rb`
- ✅ MODIFIED: `app/controllers/catalog_controller_decorator.rb` (added search_builder_class override)
- ✅ DELETED: `config/initializers/decorate_search_builders.rb` (non-working approach)

---

## 🚀 Getting Started (Step-by-Step)

### Step 1: Check out the branch
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git fetch origin
git checkout fix/catalog-facet-limiting-solr-level
git log --oneline -5  # Verify commits are there
```

### Step 2: Start Stack Car dev environment
```bash
sh down.sc.local.sh  # Clean stop if running
sh up.sc.local.sh    # Full rebuild + start
```

### Step 3: Wait for web container to be ready
```bash
# Monitor logs (watch for "Listening on http://0.0.0.0:3000")
sc logs web -f

# OR check if ready:
sc logs web 2>&1 | grep "Listening"
```

### Step 4: Test the implementation
**Open in browser**: `https://admin-wvu-knapsack.localhost.direct/catalog?locale=en`
- Username: `samvera`
- Password: `hyku`

---

## 📝 Manual Testing Checklist

Use this checklist while testing:

**Facet Display Check**:
- [ ] Catalog page loads without errors
- [ ] Facet panel visible on left side
- [ ] "People Represented" facet button visible
- [ ] Click to expand "People Represented" facet
- [ ] Items displayed: count them (should be ≤ 5)
- [ ] "More" link appears (if applicable)
- [ ] "More" link navigates to facet detail page

**Creator Facet Check**:
- [ ] Click "Creator" facet to expand
- [ ] Count items (should be ≤ 5)
- [ ] "More" link appears (if applicable)

**Subject Facet Check**:
- [ ] Click "Subject" facet to expand
- [ ] Count items (should be ≤ 5)
- [ ] "More" link appears (if applicable)

**Homepage Regression Check**:
- [ ] Navigate to: `https://admin-wvu-knapsack.localhost.direct/?locale=en`
- [ ] Verify facets still show 5 items + "More" link
- [ ] No errors on homepage

**Browser Console Check**:
- [ ] Open Developer Tools (F12 / Cmd+Option+I)
- [ ] Switch to "Console" tab
- [ ] Look for errors or warnings related to search
- [ ] Should be clean (deprecation warnings OK, JS errors NOT OK)

---

## 🔍 Debugging Guide (If Something Goes Wrong)

### Issue: Catalog page returns 404 Routing Error
**Possible Causes**:
1. Stack Car routing not fully initialized
2. Tenant/multi-tenant setup incomplete

**Debug Steps**:
```bash
# Check if web container is healthy
docker ps | grep web

# Check for errors in web container
sc logs web 2>&1 | grep -i error | head -20

# Try root path first
curl -s 'https://admin-wvu-knapsack.localhost.direct/' -k | head -5

# Then try catalog
curl -s 'https://admin-wvu-knapsack.localhost.direct/catalog' -k | head -5
```

### Issue: Facets show 36 items instead of 5
**Probable Cause**: CatalogSearchBuilderWrapper not being used

**Debug Steps**:
```bash
# Check if wrapper is loaded
grep -r "CatalogSearchBuilderWrapper" /Users/tam0013/Documents/git/wvu_knapsack/app/

# Add debug logging to wrapper build() method
# Edit: app/search_builders/catalog_search_builder_wrapper.rb
# Add at start of build(): Rails.logger.info "[CatalogSearchBuilderWrapper] build() called"

# Check logs for this message
sc logs web 2>&1 | grep "CatalogSearchBuilderWrapper"
```

### Issue: "More" links don't work
**Probable Cause**: Route not configured correctly

**Debug Steps**:
```bash
# Check if facet routes exist
curl -s 'https://admin-wvu-knapsack.localhost.direct/catalog/facet/people_represented_sim' -k | head -20

# Check Rails logs for routing errors
sc logs web 2>&1 | grep -i "routing\|facet" | tail -20
```

---

## 💾 When You're Done

### Success Path:
1. ✅ All acceptance criteria passed
2. ✅ Create synthesis report: `SYNTHESIS-CATALOG-FACET-TESTING.md` (see below for template)
3. ✅ Push results to agent-tasks repo
4. ✅ Update `status.md` if you found bugs or changes needed
5. ✅ Tag issue: move task to `tasks/completed/2026-08/`

### Synthesis Report Template (save to `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/SYNTHESIS-CATALOG-FACET-TESTING.md`):

```markdown
# Synthesis: Catalog Facet Limiting Testing — 2026-08-20

## Summary
[1-2 paragraphs: What was tested, overall result]

## Test Results
- Catalog page load: ✅ PASS / ❌ FAIL
- People Represented facet limiting: ✅ PASS / ❌ FAIL
- More links: ✅ PASS / ❌ FAIL
- Other facets: ✅ PASS / ❌ FAIL
- Homepage regression: ✅ PASS / ❌ FAIL

## Issues Found (if any)
[List any bugs or unexpected behavior]

## Evidence
[Screenshots, console output, curl responses, etc.]

## Recommendation
[Should this be merged? Any fixes needed?]
```

### If Bugs Found:
1. Create new task file in `tasks/active/` with format: `2026-08-20-HIGH-BUGFIX-CATALOG-[ISSUE-DESCRIPTION].md`
2. Include: Steps to reproduce, expected vs actual, suggested fix
3. Assign to self or appropriate agent

---

## 📚 Reference Files

**Project Context**:
- README: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
- Status: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md` (read section "2026-08-20")

**Code to Review** (before testing):
- Wrapper: `/Users/tam0013/Documents/git/wvu_knapsack/app/search_builders/catalog_search_builder_wrapper.rb`
- Decorator: `/Users/tam0013/Documents/git/wvu_knapsack/app/controllers/catalog_controller_decorator.rb`
- Homepage Wrapper (for comparison): `/Users/tam0013/Documents/git/wvu_knapsack/app/search_builders/hyrax/homepage_search_builder_wrapper.rb`

**Related Issues**:
- GitHub: https://github.com/wvulibraries/wvu_knapsack/issues (check for "facet" or "People Represented" issues)
- Previous fix deployed: branch `fix/hide-type-facet-add-show-more-facets` (merged to main)

---

## 🎓 Learning Resources (if needed)

**How Search Builders Work**:
- Blacklight searches use "search builder" classes to construct Solr query parameters
- `build(user_params)` method receives search params, returns modified Solr params
- We intercept this to add `f.field_name.facet.limit` parameters
- Pattern: extend parent class, override `build()`, call `super`, modify params, return

**How Decorators Work** (in Knapsack):
- Rails initializes controller classes
- Our decorator `prepend`s to inject `search_builder_class` method override
- When CatalogController#search_builder_class is called, returns our wrapper class
- All searches then use the wrapper instead of base AdvSearchBuilder

---

**Questions? Blockers?** Update this task file or ask in comments. Good luck! 🚀
