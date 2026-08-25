---
status: partially-completed
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## Partially Completed by Implementation Agent on 2026-08-25

**Completed**: Root cause CATEGORY identified (upstream Blacklight 7.42.0 bug)
**Not Executed**: Test 2 (Kirk's fix verification), Test 3 (file-level pinpointing)

### What was done:
1. ✅ Confirmed error exists on main branch with zero local changes
2. ✅ Identified root cause: Blacklight 7.42.0 passes nil to `.per()` which becomes `.per(0)`
3. ✅ Determined our facet-limiting changes are NOT the cause
4. 📝 Synthesis report saved at: `projects/wvulibraries_knapsack/summaries/2026-08-25-PAGINATION-ROOT-CAUSE-ANALYSIS.md`

### What remains:
1. Test Kirk's submodule update (Test 2 from task spec)
2. If error persists after Kirk's fix, decide whether to patch upstream or work around

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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-25-HIGH-BUGFIX-PAGINATION-ERROR-ROOT-CAUSE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-25-HIGH-BUGFIX-PAGINATION-ERROR-ROOT-CAUSE.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-25-HIGH-BUGFIX-PAGINATION-ERROR-ROOT-CAUSE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/wvulibraries_knapsack/tasks -name "2026-08-25-HIGH-BUGFIX-PAGINATION-ERROR-ROOT-CAUSE.md"
    Only ONE result should exist in active/. Paste this output before proceeding.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE returning results.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: 2026-08-25-PAGINATION-ROOT-CAUSE-ANALYSIS.md
  Chat is for questions only — never paste full synthesis into chat (post summary + file path instead).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to Qwen.
This is the startup contract — every element is required.

---

# TASK: Determine Root Cause of Kaminari::ZeroPerPageOperation Error

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-08-25
**Last Updated**: 2026-08-25
**Blocks**: Everything else (facet limiting testing, production deployment decision)

---

## The Problem

When accessing catalog locally, a pagination error occurs:
```
Kaminari::ZeroPerPageOperation in Catalog#index
"Current page was incalculable. Perhaps you called .per(0)?"
```

**Critical Question**: Is this error caused by our local facet-limiting changes, or is it a pre-existing upstream bug?

**Why This Matters**: We cannot proceed with facet testing or deployment until we know if our changes broke pagination.

---

## Current State (at Task Creation Time)

- **Branch**: `main` (currently running in Stack Car)
- **Submodule**: Original hyrax-webapp (NOT updated to Kirk's fix)
- **Local Changes**: NONE (main branch is clean)
- **Stack Status**: Running at https://demo-wvu-knapsack.localhost.direct
- **Docker/Stack Car**: `cd /Users/tam0013/Documents/git/wvu_knapsack && sh up.sc.local.sh` (already executed)

---

## What To Test

### Test 1: Does pagination error exist on main branch? (CRITICAL)

**Status**: READY TO TEST (stack already running on main)

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog.json' -k 2>&1 | sed -n '/<h1>/,/<\/h1>/p'
```

**Expected outputs**:
- **If error**: `Kaminari::ZeroPerPageOperation in Catalog#index` → Pagination bug exists in upstream
- **If success**: JSON response with facets → Pagination works on main, our changes broke it

**What to report**: EXACT error message or "No error, JSON returned successfully"

---

### Test 2: If error exists on main, identify if Kirk's submodule update fixes it

**Status**: Conditional (only if Test 1 shows error on main)

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Update submodule to Kirk's fix
git submodule update --remote hyrax-webapp
git add hyrax-webapp

# Rebuild stack with new submodule
sh down.sc.local.sh  # Answer 'y' when prompted
sleep 10
sh up.sc.local.sh
sleep 180  # Wait 3 minutes for startup

# Test again
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog.json' -k 2>&1 | sed -n '/<h1>/,/<\/h1>/p'
```

**Expected outputs**:
- **If error gone**: Kirk's fix solves the pagination issue
- **If error persists**: Pagination bug is deeper, not just routing

---

### Test 3: If Test 1 shows pagination works on main, find which commit broke it

**Status**: Conditional (only if Test 1 shows NO error on main)

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Switch to our branch with facet changes
git checkout fix/hide-type-facet-add-show-more-facets
git submodule update  # Update to Kirk's version

# Rebuild stack
sh down.sc.local.sh
sleep 10
sh up.sc.local.sh
sleep 180

# Test again
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog.json' -k 2>&1 | sed -n '/<h1>/,/<\/h1>/p'
```

**Expected**: Should see Kaminari error again (confirms our changes broke it)

**Next step if pagination broken on our branch**:
```bash
# Identify which local file breaks it by testing in isolation:
# 1. catalog_controller_decorator.rb
# 2. catalog_search_builder_wrapper.rb  
# 3. 999_catalog_controller_decorator.rb initializer
# 4. One of the 5 committed file changes

# Look for .per(0) or pagination-related changes:
git log --oneline fix/hide-type-facet-add-show-more-facets | head -20
git diff main..fix/hide-type-facet-add-show-more-facets -- app/controllers/ config/initializers/ app/search_builders/
```

---

## Architecture Gotchas

1. **Stack Car HTTPS**: Must use `https://demo-wvu-knapsack.localhost.direct` not `http://127.0.0.1:3000`
   - Stack Car auto-redirects HTTP to HTTPS with valid self-signed certs
   - `-k` flag in curl ignores cert validation (required for self-signed)

2. **Pagination Timing**: Kaminari error happens during page calculation, not during facet fetch
   - This error is triggered BEFORE facet limiting code runs
   - Means something is setting `per_page=0` in the request params

3. **Submodule State**: When switching between branches, ALWAYS run `git submodule update`
   - Each branch may have different submodule commit pinned
   - Forgetting this causes false negatives

4. **Docker Startup**: 3-minute wait is minimum for Rails app boot + Solr indexing
   - Don't test before "Listening on http://0.0.0.0:3000" appears in logs
   - Check: `docker logs wvu_knapsack-web-1 | grep -i listening`

---

## Acceptance Criteria

✅ **MUST COMPLETE ALL**:

1. [ ] Confirmed pagination error exists or doesn't exist on `main` branch (Test 1)
   - Clear statement: "YES, error on main" OR "NO, works on main"

2. [ ] If error on main: Tested whether Kirk's submodule update fixes it (Test 2)
   - Result: "Fixed by Kirk's update" OR "Still broken after Kirk's update"

3. [ ] If works on main: Confirmed our branch breaks it (Test 3)
   - Result: "Our branch breaks pagination: YES" with error message

4. [ ] Identified root cause category:
   - [ ] Upstream bug in Hyku (exists on main)
   - [ ] Kirk's fix doesn't address pagination (exists even after submodule update)
   - [ ] Our facet limiting changes broke pagination (only on our branch)
   - [ ] Something else (describe)

5. [ ] If "our changes broke it": Identified which file
   - Narrowed to: decorator, search builder wrapper, or initializer
   - Provided git diff showing the culprit

---

## Synthesis Report Format

Create file: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-08-25-PAGINATION-ROOT-CAUSE-ANALYSIS.md`

```markdown
# Pagination Error Root Cause Analysis

**Date**: 2026-08-25  
**Task**: Find root cause of Kaminari::ZeroPerPageOperation error  

## Test Results

### Test 1: Error on main branch?
[YES/NO]
[error message or "Works fine"]

### Test 2: Kirk's fix resolves it? (if applicable)
[YES/NO/Not applicable]
[outcome]

### Test 3: Our branch breaks it? (if applicable)
[YES/NO/Not applicable]
[error message or "Still works"]

## Root Cause Determination

**Category**: [Upstream bug | Kirk fix insufficient | Our changes | Other]

**Evidence**:
[Specific finding that led to this conclusion]

## Recommendation for Next Step

[What should be done next based on findings]
- If upstream: Report to Hyku/Hyrax maintainers
- If our changes: Remove/fix [filename]
- If Kirk's fix incomplete: Check [specific area]
```

---

## Dependencies & Blocking

**Blocked By**: Nothing (can test immediately)

**Blocks**:
- Facet limiting verification (can't test facets if pagination broken)
- Production deployment decision (won't deploy broken code)
- Submodule update decision (if Kirk's fix needed)

**Related Tasks**:
- `2026-08-20-HIGH-BUGFIX-CATALOG-FACET-LIMITING-TESTING.md` (depends on this)
- `2026-08-21-HIGH-BUGFIX-BISECT-COMMIT-REGRESSION.md` (similar investigation pattern)

---

## Quick Reference

**Working Directory**: `/Users/tam0013/Documents/git/wvu_knapsack`

**Key Branches**:
- `main` - production baseline (test first, should be clean)
- `fix/hide-type-facet-add-show-more-facets` - our changes

**Key Files** (if debugging needed):
- `app/controllers/catalog_controller_decorator.rb` (search_builder_class override, facet config)
- `app/search_builders/catalog_search_builder_wrapper.rb` (Solr param enforcement)
- `config/initializers/999_catalog_controller_decorator.rb` (after_initialize timing)

**Submodule Location**: `hyrax-webapp/`
**Kirk's Fix Commit**: `f0a022c` in samvera/hyku main branch
