---
status: active
priority: HIGH
type: testing
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/wvulibraries_knapsack/tasks -name "2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md"
    Only ONE result should exist in active/. Paste this output before proceeding.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE returning results.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: 2026-08-25-FACET-LIMITING-HTML-TEST-RESULTS.md
  Chat is for questions only — never paste full synthesis into chat (post summary + file path instead).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to Qwen.
This is the startup contract — every element is required.

---

# TASK: Test Facet Limiting on HTML Catalog Page

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-08-25
**Depends On**: 2026-08-25-HIGH-BUGFIX-PAGINATION-ERROR-ROOT-CAUSE (COMPLETE ✅)
**Blocks**: Production deployment decision, facet UX validation

---

## Background: Why This Task Exists

Previous investigation (Qwen's pagination task) confirmed:
- ✅ Pagination error is upstream Blacklight bug, NOT our code
- ✅ Our facet-limiting code is cleared of blame
- ✅ JSON API endpoint broken upstream (don't test there)
- 🎯 **Now test facet limiting on HTML** (the actual user-facing page)

**This task validates**: Does our facet-limiting solution work on the real HTML catalog page?

---

## Current State (at Task Creation)

- **Branch**: Currently on `main` (clean baseline)
- **Stack Status**: Still running from previous testing
- **Working Directory**: `/Users/tam0013/Documents/git/wvu_knapsack`
- **URL**: https://demo-wvu-knapsack.localhost.direct/catalog (use this, NOT .json)

---

## What To Test

### Step 1: Switch to facet-limiting branch

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Switch to our branch with facet changes
git checkout fix/hide-type-facet-add-show-more-facets

# Update submodule to Kirk's latest version (includes routing fix + other updates)
git submodule update --remote hyrax-webapp

# Verify both changes are in place
git log --oneline -3
git status hyrax-webapp
```

**Expected output**:
- HEAD on `fix/hide-type-facet-add-show-more-facets` branch
- hyrax-webapp submodule at latest commit (3cec38f or newer)
- 5 local files modified/new (decorator, search builders, partial, guard, initializer)

**Report**: Paste the outputs of `git log` and `git status hyrax-webapp`

---

### Step 2: Rebuild stack with new code

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Full rebuild (gets new submodule code + local changes)
sh down.sc.local.sh  # Answer 'y' when prompted
sleep 10
sh up.sc.local.sh

# Wait for web container to be ready
sleep 180  # 3 minutes for Rails boot + Solr indexing

# Verify startup
docker logs wvu_knapsack-web-1 2>&1 | grep -i "listening\|ready" | tail -3
```

**Expected**: See "Listening on http://0.0.0.0:3000" or similar startup message

**Report**: Confirm stack is ready to serve

---

### Step 3: Test facet display on HTML catalog page

**Open in browser OR curl**:
```bash
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog?locale=en' -k 2>&1 | \
  grep -A20 'facet-counts\|class="facet\|card-body"' | head -80
```

**OR** visit in browser: https://demo-wvu-knapsack.localhost.direct/catalog?locale=en

**What to look for**:
1. **Facet count**: How many facet items appear for each facet?
   - Expected: 5 items per facet (Creator, Subject, Location, People Represented, etc.)
   - Actual: [Count what you see]

2. **"More" link present?**
   - Expected: Each facet should have a "More" link after the 5th item
   - Actual: [YES/NO]

3. **Facets that should be limited**: Check at least these:
   - `creator_sim` (Creator) → should show 5 creators
   - `people_represented_sim` (People Represented) → should show 5 people
   - `subject_sim` (Subject) → should show 5 subjects

**Report**: For EACH facet:
```
- [Facet Name]: Shows [X] items, "More" link [PRESENT/MISSING]
```

---

### Step 4: Test "More" link functionality (if present)

**If "More" links are present**:
1. Click "More" on one facet (e.g., Creator)
2. Expected: Opens modal/page showing full facet list
3. Expected: Can see all creators (beyond the first 5)

**Report**:
```
- "More" link clicks: [YES/NO]
- Modal opens: [YES/NO]
- Full facet list visible: [YES/NO]
- Example: Saw facet items 1-5 initially, clicked More, now see 6-36+ creators
```

---

### Step 5: Verify facet configuration is applied

```bash
# Check that our decorator/search builder code is loaded
docker logs wvu_knapsack-web-1 2>&1 | grep -i "facet\|limit" | head -20
```

**Report**: Any relevant log messages about facet limiting?

---

## Architecture Gotchas

1. **Use HTML endpoint, NOT JSON**:
   - ✅ `/catalog?locale=en` (HTML works, facets display)
   - ❌ `/catalog.json` (JSON broken upstream, don't test here)

2. **"More" link URL**: Should route to `/facet/{field_name}` in catalog controller
   - Kirk's fix ensures homepage facet "More" links route to catalog (not homepage)
   - Check that both homepage AND catalog "More" links work correctly

3. **Facet limiting happens in TWO places**:
   - UI-level: Blacklight config `limit: 5` (display)
   - Solr-level: CatalogSearchBuilderWrapper adds `f.{field}.facet.limit=5` params
   - Both must work for complete solution

4. **Submodule timing**: May need to restart web container twice if submodule changes aren't picked up
   - If facet limiters still missing after rebuild: `docker restart wvu_knapsack-web-1`

---

## Acceptance Criteria

✅ **ALL must be met for task completion**:

1. [ ] Switched to `fix/hide-type-facet-add-show-more-facets` branch
   - Confirmed with `git log` output showing correct branch

2. [ ] Submodule updated to latest main
   - Confirmed with `git status hyrax-webapp` showing new commit

3. [ ] Stack rebuilt and running
   - Verified with docker logs showing "Listening"

4. [ ] Facet display working on `/catalog` page
   - All checked facets show 5 items + "More" link
   - Example: Creator shows 5 creators, not 36+

5. [ ] "More" links functional
   - Clicking opens modal/page with full facet list
   - OR if not present: clearly documented why

6. [ ] No errors in web logs
   - No Kaminari, Wings, Valkyrie errors
   - No 500 status codes

---

## Synthesis Report Format

Create file: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-08-25-FACET-LIMITING-HTML-TEST-RESULTS.md`

```markdown
# Facet Limiting HTML Catalog Test Results

**Date**: 2026-08-25  
**Branch**: fix/hide-type-facet-add-show-more-facets  
**Submodule**: [latest commit SHA]  

## Test Summary

### Facet Display (Step 3)
- Creator facet: [X items] "More" link [PRESENT/MISSING]
- Subject facet: [X items] "More" link [PRESENT/MISSING]
- People Represented facet: [X items] "More" link [PRESENT/MISSING]
- [Additional facets tested]

### "More" Link Functionality (Step 4)
- "More" link clicks work: [YES/NO]
- Modal/page opens: [YES/NO]
- Full list visible: [YES/NO]

### Error Status (Step 6)
- Web errors: [NONE / list any]
- Kaminari errors: [NONE / present]
- Wings errors: [NONE / present]

## Verdict

**Facet Limiting Implementation**: [✅ WORKING / ❌ BROKEN / ⚠️ PARTIAL]

**Details**: [Specific findings about what works and what doesn't]

## Recommendation

[What should be done next?]
- If working: Ready for production testing
- If broken: Which component needs adjustment?
- If partial: Which facets/links have issues?
```

---

## Dependencies & Blocking

**Depends On**: 2026-08-25-HIGH-BUGFIX-PAGINATION-ERROR-ROOT-CAUSE ✅ (COMPLETE)

**Blocked By**: Nothing (can test immediately)

**Blocks**:
- Production deployment decision
- Facet UX validation
- Documentation of facet limiting behavior

---

## Quick Reference

**Working Directory**: `/Users/tam0013/Documents/git/wvu_knapsack`

**Key Branches**:
- `main` (clean baseline)
- `fix/hide-type-facet-add-show-more-facets` (our changes)

**Key Files** (if debugging needed):
- `app/controllers/catalog_controller_decorator.rb` (facet config + search_builder_class override)
- `app/search_builders/catalog_search_builder_wrapper.rb` (Solr param enforcement)
- `config/initializers/999_catalog_controller_decorator.rb` (after_initialize timing)
- `app/views/wvu_home/hyrax/catalog/_facet_limit.html.erb` (facet display partial)

**Stack Car URLs**:
- Homepage: https://demo-wvu-knapsack.localhost.direct/?locale=en
- Catalog HTML: https://demo-wvu-knapsack.localhost.direct/catalog?locale=en (✅ TEST THIS)
- Catalog JSON: https://demo-wvu-knapsack.localhost.direct/catalog.json (❌ BROKEN upstream, don't test)

**Kirk Wang's Fix**: Ensures homepage facet "More" links route to `/facet/{field}` in catalog (not homepage)
- Commit: f0a022c in samvera/hyku
- Already included in latest submodule version
