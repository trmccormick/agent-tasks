---
status: draft
resume: false
priority: HIGH
type: testing
system_domain: DEPLOYMENT | OPERATIONS | OTHER
mvp_alignment: SPEC_HEALTH | OTHER
local_worker_safe: true
---

# Task: Test 7.2.1 Upgrade Locally with Data Persistence

**Project:** WVU Knapsack  
**Type:** Testing / Validation  
**Priority:** High  
**Created:** 2026-11-17  
**Last Updated:** 2026-11-18  

---

## DRAFT STATUS NOTE

This task is in draft status and awaiting review approval. It has NOT yet been approved for dispatch.

**Current State:** In `tasks/drafts/` committed to git  
**Next Step:** Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

---

## DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Testing Agent**.

Project: WVU Knapsack
Task: <agent-tasks>/projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/test_7.2.1_upgrade_locally_with_data_persistence.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/test_7.2.1_upgrade_locally_with_data_persistence.md
       git ls-files --error-unmatch projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/test_7.2.1_upgrade_locally_with_data_persistence.md
     If the source is not tracked, stage the source first:
       git add projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/test_7.2.1_upgrade_locally_with_data_persistence.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/test_7.2.1_upgrade_locally_with_data_persistence.md \
              projects/wvu_knapsack/tasks/active/test_7.2.1_upgrade_locally_with_data_persistence.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/wvu_knapsack/tasks/active/test_7.2.1_upgrade_locally_with_data_persistence.md
       test ! -e projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/test_7.2.1_upgrade_locally_with_data_persistence.md && test -f projects/wvu_knapsack/tasks/active/test_7.2.1_upgrade_locally_with_data_persistence.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/wvu_knapsack/tasks -name "test_7.2.1_upgrade_locally_with_data_persistence.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/wvu_knapsack/README.md
  3. <agent-tasks>/projects/wvu_knapsack/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/wvu_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-TESTING-7.2.1-DATA-PERSISTENCE.md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## Task Readiness Checklist (Human — before dispatching to implementation)

- [ ] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**This task requires human (Tracy) authorization before dispatch.**

---

## Objective

Verify that switching to the Hyku 7.2.1 upgrade branch locally **preserves imported data** and keeps it queryable in the UI. This task focuses exclusively on data persistence testing with minimal smoke tests for search/facet queries.

> **Version note:** This task targets Hyku 7.2.1 (not 7.2.0). Version 7.2.1 includes critical GA4 security fixes that 7.2.0 lacks. All references to "7.2.0" in prior drafts have been updated to 7.2.1.

## Context

**Current State:**
- Local has imported test data (visible in screenshot from earlier session)
- 7.2.1 upgrade branch has not been tested with real data persistence
- Goal is to validate data safety before promoting to hykudev/prod

**Why This Matters:**
- Hyku 7.2.1 has schema/model changes (Flexible metadata framework)
- Database migrations must run correctly
- Solr index must be rebuilt or reindexed
- Data must remain queryable after branch switch

## Scope (Revised per Review Finding F7)

**IN SCOPE:**
- Data persistence testing during local branch switch
- Minimal smoke tests: search queries, facet queries
- Documenting any errors encountered

**OUT OF SCOPE (removed per F7):**
- Replicating procedure on hykudev — do NOT access hykudev or prod VMs; report findings only
- Planning production update timeline
- Investigating gem conflicts during build (that is Task 1's job)

## Dependencies and Prerequisites

- **Prerequisite:** Human (Tracy) authorization before dispatch
- **Depends on:** Investigation findings from `investigate_gemfile_lock_hykudev_reboots_7.2.1.md` — specifically, the lockfile approach recommended in Task 1's deliverables
- **Related task:** Run Task 1 first to understand gem conflicts before attempting this test

## Detailed Test Procedure

### Phase 1: Baseline Documentation

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# 1. Record current state (7.1.3/main)
git branch -v
git log --oneline -1

# 2. Pre-switch database backup (MANDATORY per Review Finding F2)
# This is critical: once 7.2 migrations run against the persisted DB,
# 7.1.3 code may not run against the migrated schema.
docker compose exec db pg_dump -U postgres knapsack_production > /tmp/knapsack_pre_7.2.1_backup.sql
# Verify backup was created
ls -lh /tmp/knapsack_pre_7.2.1_backup.sql

# 3. Record baseline item count using rails runner (per F5: prefer one-liners over interactive console)
docker compose exec web rails runner "puts Item.count" > /tmp/baseline_item_count.txt 2>&1
cat /tmp/baseline_item_count.txt

# 4. Sample baseline data (tenant-aware — switch to tenant first per F5)
# TODO: verify model name 'Item' exists in this Hyku/knapsack instance
# If the model has a different name, replace 'Item' with the correct model class
docker compose exec web rails runner "puts Tenant.current&.name || 'no tenant'; puts Item.count" > /tmp/baseline_tenant_info.txt 2>&1

# 5. Record baseline Solr count
# TODO: verify Blacklight.repository.search works in this Hyku instance
docker compose exec web rails runner "puts Blacklight.repository.search({q: '*:*'}).fetch(:response).fetch(:numFound)" > /tmp/baseline_solr_count.txt 2>&1

# 6. Test UI visibility (manual step)
# - Navigate to http://localhost:3000/my/works
# - Screenshot or describe what's visible
# - Note any items you can see
```

### Phase 2: Switch to 7.2.1 Branch

```bash
# 7. Stash any uncommitted changes
git stash

# 8. Update submodule (per F3: these changes are intentional and temporary)
git submodule update --init --recursive
# NOTE: The submodule working tree may show modifications during this process.
# These changes are temporary and MUST NEVER be committed to the submodule.
# At the end of testing, verify: git -C hyrax-webapp status (must show clean)

# 9. Rebuild containers (per F1: NEVER use --volumes flag — it destroys data volumes)
docker compose down --remove-orphans
# WARNING: NEVER use -v or --volumes flags with docker compose down.
# This destroys named volumes including Postgres and Solr data.
docker compose build --no-cache 2>&1 | tee /tmp/7.2.1_build.log
sh up.sc.local.sh
```

### Phase 3: Monitor Container Startup

```bash
# 10. Watch web container logs
docker compose logs -f web > /tmp/7.2.1_web_startup.log 2>&1 &

# 11. Wait for "Listening on" message (define success: no errors in logs within 5 minutes of "Listening on")
# If errors occur during boot, check:
# - Are gems missing/conflicting? (look for NameError, MissingConstantError)
# - Are migrations failing?
# - Are initializers crashing?

# 12. Stop log capture once "Listening on" appears or after 5 minutes
kill %1 2>/dev/null
```

### Phase 4: Data Persistence Check

```bash
# 13. Verify items still exist (per F5: use rails runner one-liners, not interactive console)
# TODO: verify model name 'Item' exists in this Hyku/knapsack instance
ITEM_COUNT=$(docker compose exec web rails runner "puts Item.count" 2>&1 | tail -1)
echo "Post-switch item count: $ITEM_COUNT"
echo "$ITEM_COUNT" > /tmp/post_switch_item_count.txt

# COMPARE with baseline — should be EXACTLY the same (not "same or close")
BASELINE=$(cat /tmp/baseline_item_count.txt)
if [ "$ITEM_COUNT" = "$BASELINE" ]; then
  echo "PASS: Item count matches baseline ($BASELINE)"
else
  echo "FAIL: Item count differs — baseline=$BASELINE, post-switch=$ITEM_COUNT"
fi

# 14. Verify tenant context (per F5: Hyku is multi-tenant)
# TODO: verify the correct tenant-switching command for this Hyku instance
# Common patterns: Tenant.current, CurrentTenant.set('tenant_name'), switch!('tenant_name')
docker compose exec web rails runner "puts 'Tenant: ' + (Tenant.current&.name || 'none')" > /tmp/post_switch_tenant.txt 2>&1

# 15. Verify Solr count (exact match required)
SOLR_COUNT=$(docker compose exec web rails_runner "puts Blacklight.repository.search({q: '*:*'}).fetch(:response).fetch(:numFound)" 2>&1 | tail -1)
echo "Post-switch Solr count: $SOLR_COUNT"
echo "$SOLR_COUNT" > /tmp/post_switch_solr_count.txt

BASELINE_SOLR=$(cat /tmp/baseline_solr_count.txt)
if [ "$SOLR_COUNT" = "$BASELINE_SOLR" ]; then
  echo "PASS: Solr count matches baseline ($BASELINE_SOLR)"
else
  echo "FAIL: Solr count differs — baseline=$BASELINE_SOLR, post-switch=$SOLR_COUNT"
fi
```

### Phase 5: UI Smoke Tests (Minimal)

```bash
# 16. Access UI and run minimal smoke tests
# - Navigate to http://localhost:3000/my/works
#   PASS: Items are visible
#   FAIL: Page does not load or shows errors
# - Try search box (search for a known item title)
#   PASS: Search returns expected results
#   FAIL: Search returns no results or errors
# - Try one facet filter
#   PASS: Facet filters work
#   FAIL: Facet filters show errors

# 17. Check for rendering errors
docker compose logs web 2>&1 | grep -i "ActionView::MissingTemplate" > /tmp/missing_template_errors.txt || true
if [ -s /tmp/missing_template_errors.txt ]; then
  echo "FAIL: ActionView::MissingTemplate errors found"
else
  echo "PASS: No MissingTemplate errors"
fi
```

### Phase 6: Solr Reindexing (if needed)

```bash
# 18. If Solr is empty but items exist in Postgres, attempt reindexing
# TODO: verify the correct reindex command for this Hyku instance
# The command varies by adapter (ActiveFedora vs Valkyrie); confirm with stack owner
# Common patterns:
#   - Hyrax::Indexer.index_remote_objects  (Valkyrie)
#   - Rake task: hyrax:index:reindex_all  (ActiveFedora)
# Do NOT assume either exists without verification.

# 19. Verify Solr populated after reindex
SOLR_COUNT_AFTER=$(docker compose exec web rails runner "puts Blacklight.repository.search({q: '*:*'}).fetch(:response).fetch(:numFound)" 2>&1 | tail -1)
echo "Post-reindex Solr count: $SOLR_COUNT_AFTER"
```

### Phase 7: Cleanup and Documentation

```bash
# 20. Verify submodule is clean (per F3)
git -C hyrax-webapp status
# Must show no uncommitted changes in hyrax-webapp/

# 21. Document findings
# Write results to /tmp/7.2.1_test_results.md with:
# - Baseline counts vs post-switch counts
# - UI smoke test pass/fail results
# - Any errors encountered
# - Whether backup Gemfile.lock was needed (if applicable)

# 22. Cleanup temporary files
rm -f /tmp/baseline_*.txt /tmp/post_switch_*.txt /tmp/7.2.1_*.log /tmp/missing_template_errors.txt
```

## Success Criteria (Measurable)

✅ **Task passes if ALL of the following are true:**
1. Container builds successfully (or uses backup Gemfile.lock approach from Task 1 to stabilize)
2. Web container boots without crashing (no NameError, uninitialized constant within 5 minutes of "Listening on")
3. Post-switch item count EXACTLY equals baseline item count (not "same or close" — exact match required)
4. Items are visible in UI (/my/works page loads without errors)
5. Search smoke test passes (at least one known item found via search)
6. Facet smoke test passes (at least one facet filter works)
7. No ActionView::MissingTemplate errors in server logs

❌ **Task fails if ANY of the following occur:**
1. Container fails to build with unresolvable gem conflicts
2. Web container crashes on startup (NameError, uninitialized constant)
3. Post-switch item count differs from baseline (data loss)
4. Items exist but Solr is empty AND reindexing fails
5. UI shows "MissingTemplate" errors for key partials
6. Search or facet smoke tests fail

## Expected Outcomes

### If Successful
- Document: "7.2.1 upgrade branch preserves data and UI functionality"
- Provide exact counts and evidence in test results file

### If Partial Success (some errors but recoverable)
- Document specific errors and workarounds
- Note if backup Gemfile.lock was required
- Identify which fixes from investigation task are still needed

### If Unsuccessful
- Document exact failure point (build, boot, data loss, UI rendering)
- Share logs with investigation task to correlate with gem conflicts
- Plan recovery steps

## Branches to Test

**Primary:** `feature/hyku-v7.2.1-upgrade`
- Includes GA4 security fix
- Includes PR #3369 facet limiting
- More fixes than 7.2.0
- **Recommended to test first**

**Secondary:** `feature/hyku-v7.2.0-upgrade`
- Initial 7.2.0 attempt
- Useful to understand what's improved in 7.2.1
- Only test if 7.2.1 issues warrant comparison

## Rollback Plan (Revised per Review Finding F2)

If testing is catastrophic:

```bash
# 1. Stop containers
docker compose down

# 2. Restore database from pre-switch backup (MANDATORY — see Phase 1 step 2)
docker compose exec -i db pg_dump -U postgres knapsack_production < /tmp/knapsack_pre_7.2.1_backup.sql

# 3. Reset to main branch
git checkout main
git submodule update --init --recursive

# 4. Rebuild clean
docker compose build --no-cache
sh up.sc.local.sh
```

**Data should persist** because Postgres volume is not removed (we used `--remove-orphans` without `--volumes`).

**Schema migration caveat:** Once 7.2 migrations run against the persisted DB, 7.1.3 code may not run against the migrated schema. This is why the pre-switch `pg_dump` backup is mandatory. If the database schema has been altered by 7.2 migrations and the rollback fails, you may need to restore from an earlier database snapshot.

## Submodule Modification Note (per Review Finding F3)

During this testing, you will modify the `hyrax-webapp` submodule working tree:
- **Phase 2:** You may temporarily place a Gemfile.lock inside `hyrax-webapp/` during build testing

**CRITICAL:** These changes are intentional and temporary. They MUST NEVER be committed to the submodule. At the end of testing:
```bash
# Verify submodule working tree is clean before any commit
git -C hyrax-webapp status
# Must show no uncommitted changes in hyrax-webapp/
```

## Resources

- **Test plan details:** `/Users/tam0013/.copilot/session-state/4e01e37b-32bd-427f-b787-c7a4cd5ff972/files/TEST_PLAN_7.2.0_DATA_PERSISTENCE.md`
- **Gemfile.lock backup:** `/Users/tam0013/.copilot/session-state/4e01e37b-32bd-427f-b787-c7a4cd5ff972/files/Gemfile.lock.backup` (checksum: `7b4ed53d782f8f0103bcd2537d0d6052`)
- **Wiki docs:** `/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/` (testing_best_practices, local_stack_car_environment_mismatch, etc.)
- **Investigation task:** `investigate_gemfile_lock_hykudev_reboots_7.2.1.md` (run first to understand gem conflicts)

## Important Notes

- **Do NOT access hykudev or prod VMs.** Report findings only — do not attempt remote testing.
- **Data is precious:** Use care during branch switches; have rollback plan ready.
- **Document everything:** Even partial success/failure helps de-risk hykudev testing.
- **If Solr empties:** This is recoverable with reindexing — don't panic.
- **If UI errors:** Capture full error messages — may be guide-able with wiki fixes.
- **Report findings:** Even negative results (data loss, build failures) are valuable for planning.
