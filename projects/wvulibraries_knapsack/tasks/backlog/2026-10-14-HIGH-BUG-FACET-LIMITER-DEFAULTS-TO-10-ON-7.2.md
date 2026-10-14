---
status: active
resume: false
priority: HIGH
type: bug-fix
system_domain: SEARCH | DISPLAY
mvp_alignment: BLOCKER
local_worker_safe: true
---

## 🟢 Task Readiness Checklist (Human — READY TO DISPATCH)

- [x] Agent Dispatch Interface section below is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template is provided
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY TO DISPATCH.**

---

## 🟢 Agent Dispatch Interface (Copy this EXACTLY to send to Qwen)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-10-14-HIGH-BUG-CHERRY-PICK-FACET-LIMITER-FIX-TO-7.2.md

Synthesis Report Required Before Implementation (brief): "Read this task. Summarize your plan in 2 lines and post to chat. Wait for approval."
After approval: implement the task exactly as specified, test locally, report results back to human via chat before committing any changes.

## Instructions for This Session

- Step 0: Do NOT move or update this task file frontmatter yet.
- Read the full task file below (especially Implementation Steps, Architecture Gotchas, Acceptance Criteria).
- Do NOT proceed past the synthesis report until approved by human.
```

---

## 2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2

**Source:** hykudev.lib.wvu.edu after deploying `feature/hyku-v7.2.0-upgrade`  
**Branch:** `feature/hyku-v7.2.0-upgrade`  
**Environment:** hykudev (development VM)  
**Blocked by:** None  
**Workflow:** Fix our limiter → backport to hyku branch → share with LaRita for upstream comparison

---

### Problem Statement

After deploying Hyku 7.2.0 upgrade to hykudev, catalog facets show **10 items per facet** instead of the expected **5**. The 7.2 branch already has a facet limiter implementation applied, but it's not respecting the configured limit and is reverting to Blacklight's default of 10.

**Expected behavior (production on main):** Facets show 5 items + "More" link when applicable  
**Actual behavior (hykudev on 7.2):** Facets show 10 items (Blacklight default) with no pagination

The gap: Production has Tracy's `limit+1` Solr signaling working correctly. hykudev's limiter is applying a limit, but either (a) it's not being set correctly, or (b) it's missing the `+1` increment needed for "More" link detection.

### Three-Phase Workflow

**Phase 1: Diagnose & Fix our limiter** (on feature/hyku-v7.2.0-upgrade)
- Identify why 7.2 branch shows 10 facets instead of 5
- Apply/correct the facet limiter configuration to ensure `limit+1` for Solr
- Test on hykudev

**Phase 2: Backport to hyku branch** (create new compare branch)
- Once fixed on 7.2 and verified, create a branch off samvera/hyku
- Backport our solution to show LaRita our approach
- This allows her to compare with her upstream work

**Phase 3: Collaborate with LaRita**
- Share the hyku branch with LaRita for comparison
- Discuss which approach (ours vs. her backport) is better for upstream contribution
- Decide on final solution for hyrax/hyku

### Root Cause (To Be Diagnosed)

**Hypothesis:** The facet limiter on 7.2 branch is missing the Solr `limit+1` signal that Tracy's fix provides.

- **What we know:** 7.2 branch has facet limiting code applied
- **What's broken:** It's defaulting to 10 instead of respecting the configured 5
- **Likely issue:** The initializer either (1) isn't setting `limit+1` for Solr, or (2) isn't running at all

### What Needs to Happen

**File:** `config/initializers/search_builder_facet_limits.rb` (or equivalent on 7.2)  
**Goal:** Ensure facet.limit is set to `configured_limit + 1` when requesting from Solr

**Why `+1` matters:**
- Without the `+1`: Solr receives `facet.limit=5`, returns exactly 5, Blacklight doesn't know if there are more
- With the `+1`: Solr receives `facet.limit=6`, returns up to 6, Blacklight can detect and show "More" link

### Implementation Steps

**PHASE 1: Fix & Verify (Primary dispatch scope)**

**Step 0:** Create a synthesis report with your plan (2 lines max) and post to chat. Wait for approval before proceeding.

**Step 1:** Ensure you're on the 7.2 branch locally and diagnose current facet limiter configuration
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git checkout feature/hyku-v7.2.0-upgrade

# Check if search_builder_facet_limits.rb exists on 7.2:
ls -la config/initializers/search_builder_facet_limits.rb 2>&1
cat config/initializers/search_builder_facet_limits.rb 2>/dev/null || echo "FILE NOT FOUND"
```

**Step 2:** Compare with main branch to see the correct working configuration
```bash
# Stash any local changes, check main:
git stash
git checkout main
echo "=== MAIN BRANCH ===" 
cat config/initializers/search_builder_facet_limits.rb 2>/dev/null || echo "NOT ON MAIN EITHER"

# Search for any facet limit configuration on main:
grep -r "facet.*limit" config/ app/controllers/ --include="*.rb" 2>/dev/null | grep -v "hyrax-webapp" | head -20
```

**Step 3:** Return to 7.2 branch and apply the correct fix
```bash
git checkout feature/hyku-v7.2.0-upgrade
git stash pop 2>/dev/null || echo "No stash"

# Based on Step 2 findings:
# If search_builder_facet_limits.rb exists on main but not 7.2: cherry-pick it
# If it exists on both: compare and fix the 7.2 version to match main
# If missing from both: manually create it to set facet.limit = limit + 1
```

**Step 4:** Create/update the facet limiter initializer on 7.2 to ensure it sets `limit+1` for Solr
- The initializer should set `params[:"f.#{field_name}.facet.limit"] = (configured_limit + 1).to_i`
- This ensures Solr receives 6 (when limit=5) so Blacklight can detect if there are more items

**Step 5:** Test the fix on hykudev (which is already running 7.2 branch)
```bash
# Push the fix to the wvu_knapsack repo (or inform that changes are ready to deploy)
# If docker-compose is running locally:
docker-compose -f docker-compose.yml rebuild web
docker-compose up -d web

# Once hykudev is rebuilt with the fix:
# Visit http://hykudev.lib.wvu.edu/catalog
# Inspect any facet (Date Created, Creator, etc.)
# Confirm: Shows 5 items (not 10) + "More" link when additional values exist
```

**Step 6:** Commit the fix to feature/hyku-v7.2.0-upgrade
```bash
git add config/initializers/search_builder_facet_limits.rb
git commit -m "fix: Ensure facet limiter sets limit+1 for Solr on 7.2 branch

Fixes facet limit regression on hykudev where facets were showing 10 items
instead of configured 5. The initializer now correctly sends limit+1 to Solr
so Blacklight can detect and display the More link when additional facet
values are available beyond the display limit."
```

---

**PHASE 2: Backport to hyku branch** (after Phase 1 verified)

Once Phase 1 is fixed and verified on hykudev:
1. Create a new branch off `samvera/hyku`:
   ```bash
   cd /path/to/hyku
   git checkout v7.2-stable
   git pull origin
   git checkout -b feature/wvu-facet-limiter-backport
   ```
2. Backport the facet limiter fix from wvu_knapsack to this hyku branch
3. Test locally
4. Document the changes for LaRita's review

---

**PHASE 3: Collaborate with LaRita** (after Phase 2)

1. Share the `feature/wvu-facet-limiter-backport` branch with LaRita
2. Discuss approaches: which is better for upstream contribution to Hyrax/Hyku?
3. Decide on final solution path (her backport vs. ours vs. hybrid)

### Acceptance Criteria (PHASE 1)

- [ ] Root cause identified: Why is 7.2 branch showing 10 facets instead of 5?
- [ ] Facet limiter configuration corrected on 7.2 branch (missing `limit+1` added or fixed)
- [ ] `config/initializers/search_builder_facet_limits.rb` exists and correctly sets `facet.limit = limit + 1` for Solr requests
- [ ] Local test on hykudev: catalog facets show exactly 5 items (not 10)
- [ ] "More" link appears when there are more than 5 facet values available
- [ ] Changes are committed to feature/hyku-v7.2.0-upgrade (but NOT pushed until human approval)

**Phase 2 & 3 (backport + LaRita collaboration) will be separate follow-up tasks after Phase 1 is verified.**

### Architecture Gotchas

- **Two different scenarios:** 
  - If initializer is MISSING on 7.2: Cherry-pick or manually add it from main
  - If initializer EXISTS but is broken: Diagnose and fix the configuration
- **The `+1` is critical:** Without it, Solr can't tell if there are more items, so "More" link won't show
- **Timing:** This is blocking 7.2 → main merge until resolved
- **Related:** Don't confuse with the facet redesign task. This is the core limiter fix for correct count/pagination.

### Dependencies & Blocks

- **Blocked by:** None
- **Blocks:** 
  - Full validation of Hyku 7.2.0 upgrade (can't call 7.2 ready for production without this fix)
  - Merging 7.2 branch to main
- **Related:**
  - HYKU-720 (parent upgrade): Hyku 7.2.0 submodule update + VERSION override
  - Facet redesign (separate): `2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md`

### Session Notes

- **7.2 branch status:** Has facet limiting code, but showing 10 items instead of 5 (default)
- **Production (main) status:** Working correctly with 5 items + "More" link (Tracy's fix applied)
- **Diagnosis needed:** Why is 7.2's limiter not working? Missing `limit+1` signal to Solr, or missing initializer entirely?
- **Tracy's reference fix:** Commit `e42517c` on main—use as template if needed
- **LaRita coordination:** Once our fix is verified on hykudev, backport to hyku branch for upstream comparison
- **Workflow:** Phase 1 (fix + verify) → Phase 2 (backport to hyku) → Phase 3 (collaborate with LaRita on upstream approach)
- **Next blocker:** Once Phase 1 is fixed and verified on hykudev, unblocks 7.2 → main merge consideration
