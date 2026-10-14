---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: SEARCH | DISPLAY
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/backlog/[YYYY-MM-DD]-[PRIORITY]-TYPE-[DESCRIPTIVE-NAME].md
Synthesis Report Required Before Implementation (brief): "Read this task. Summarize your plan in 2 lines and post to chat. Wait for approval."
After approval: implement the task exactly as specified, test locally, report results back to human via chat before committing any changes.

## Instructions for This Session

- Step 0: Move this task file from tasks/backlog/ to tasks/active/. Update its frontmatter status from "backlog" to "in-progress".
- Read the full task file below (especially Implementation Steps, Architecture Gotchas, Acceptance Criteria).
- Do NOT proceed past the synthesis report until approved by human.
```

---

## 2026-10-14-HIGH-BUG-FACET-LIMITER-REGRESSION-HYKU-7.2.0

**Source:** hykudev.lib.wvu.edu after deploying `feature/hyku-v7.2.0-upgrade`  
**Branch:** `feature/hyku-v7.2.0-upgrade`  
**Environment:** hykudev (development VM)  
**Blocked by:** None  

---

### Problem Statement

After deploying the Hyku 7.2.0 upgrade to hykudev, catalog facets are showing **10 items per facet value** instead of the expected **5**. This affects all facets on the filtered results/catalog page — for example, the Date Created facet shows 10 ranges when it should show 5.

This is a regression introduced by the Hyku 7.2.0 upgrade (submodule change from old SHA → v7.2-stable). The `facet.limit` global setting was working correctly before the update but now returns double the expected count.

### Investigation Steps

1. **Compare Blacklight facet.limit config between old and new state:**
   ```bash
   # On hykudev or locally on feature branch:
   git diff main..feature/hyku-v7.2.0-upgrade -- config/initializers/*.rb | grep -i "facet"
   
   # Search for any blacklight facet.limit setting in the knapsack codebase:
   find . -name "*.rb" -o -name "*.yml" -o -name "*.yaml" | xargs grep -l "facet.limit\|blacklight.*facet" 2>/dev/null
   ```

2. **Check hyrax-webapp submodule changes** that may have changed the default:
   ```bash
   cd hyrax-webapp
   git diff <old-sha>..HEAD -- config/initializers/*.rb | grep -i "facet\|blacklight"
   # Also check Gemfile for Blacklight version change
   ```

3. **Check if a knapsack initializer was removed** during the Hyku 7.2.0 migration — the old `config/puma.rb` did not exist on main; similar pattern may have occurred with facet config

4. **Check environment variables** that might override the default:
   ```bash
   # In docker-compose files or .env:
   grep -i "facet" docker-compose*.yml .env* 2>/dev/null
   ```

5. **Verify the correct target value:** Production (digitalhistory.lib.wvu.edu) shows 5 per facet. Confirm what `facet.limit` should be set to — likely `5`.

### Likely Root Causes

- **Hyku default changed** — Hyku/Hyrax may have updated Blacklight gem version between SHAs with a different default `facet.limit` (e.g., 10 instead of 5)
- **Knapsack initializer removed/overwritten** — A custom facet config file that was overriding the default may have been lost in the submodule update
- **Blacklight config structure changed** — Blacklight's config keys may have shifted between versions

### Implementation Steps

1. Find where `facet.limit` is currently set (or not set) on the new branch
2. Compare with what it was before the upgrade (on main or pre-upgrade state)
3. Add or restore the correct `facet.limit` configuration:
   - In a knapsack initializer if one existed before
   - Or in `config/blacklight.yml` / Rails config if that's the pattern
4. Verify the fix: run locally and confirm facets show 5 items per value (not 10)

### Architecture Gotchas

- **Blacklight's `facet.limit` is a global setting** — it applies to all facets on filtered results pages, not just homepage facets
- **The knapsack puma.rb uses clustered mode** (`workers ENV.fetch("WEB_CONCURRENCY", 4)`) which was tested locally but is separate from this issue
- **Do NOT confuse with the facet limiter redesign task** (#11 / `2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md`) — that task targets the "More" link generation. This is specifically about the base limit value itself
- **The Hyku 7.2.0 upgrade also removed `hyrax-iiif_av` gem** and changed HTTP auth env vars — these are documented in the HYKU-720 session handoff files

### Acceptance Criteria

- [ ] Catalog facets show exactly 5 items per facet value (matching production)
- [ ] The setting is applied globally via Blacklight config or initializer
- [ ] The fix persists across docker-compose restarts
- [ ] No side effects on other facet behaviors (filtering, sorting, "More" links remain intact)

### Dependencies & Blocks

- **Blocked by:** None — can investigate locally
- **Blocks:** Full functional validation of Hyku 7.2.0 upgrade on hykudev
- **Related:** 
  - HYKU-720 (parent upgrade): `tasks/backlog/2026-10-13-HIGH-BUG-FIX-HYKU-7.2.0-WEB-CONTAINER-CRASH.md` (completed)
  - Facet limiter redesign: `2026-09-30-HIGH-ARCHITECTURE-SIMPLIFY-FACET-LIMITER.md` (separate issue)
  - Prod duplicate facet ticket: `tasks/active/PRODUCTION-DUPLICATE-COLLECTION-FACET-NAMES.md` (separate issue)

### Session Notes

- The web container crash was fixed separately (VERSION override guard) — confirmed working
- This limiter issue is a secondary regression from the same Hyku 7.2.0 submodule update
- Need to compare what `facet.limit` value was set before vs. after the upgrade
- Production environment still works correctly with 5 items per facet
