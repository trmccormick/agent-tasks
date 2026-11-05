---
status: draft
resume: false
priority: HIGH
type: bug-fix
system_domain: hyku
mvp_alignment: hyku-7.2.0-upgrade-blocker
local_worker_safe: true
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [x] Acceptance Criteria are clear and measurable (no ambiguity)
- [x] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [x] Dependencies listed and verified against status.md (all prerequisites exist)
- [x] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

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

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual; if the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: [project_name]
Task: <agent-tasks>/projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md \
         projects/[project]/tasks/active/[FILENAME].md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/[project]/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/[project]/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Local Testing of Hyku 7.2.0 Search Bug — Stack Car Investigation
**Status**: DRAFT  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-11-05  
**Last Updated**: 2026-11-05  

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PENDING REVIEW (REVISE items pending)
- **MVP Alignment**: VALID — directly addresses hyku-7.2.0-upgrade blocker
- **Action Line**: NEEDS MANUAL REVIEW — template conformance must be fixed before dispatch

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires terminal access to run docker commands, Solr queries, Rails runner on Stack Car
**Local attempts before cloud**: N/A — first dispatch for this task
**Supervision Level**: Watched carefully — all agents on first dispatch of a task

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
3. **Project Status**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md`
4. **Previous Testing Strategy**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-UPGRADE-TESTING-STRATEGY.md` (testing lessons learned)
5. **This Task File**: Everything below

---

## Context

The Hyku 7.2.0 upgrade has a search bug where:
- Search returns empty results despite records existing in Solr
- Records are indexed to the wrong Solr collection (Valkyrie indexer writes to UUID-based collection)
- CatalogSearchBuilder reads from a different collection (hydra-production)
- This is NOT 7.2.0-specific — it also affects v7.1.3 if the code path differs

The root cause is a **code-level bug in how SolrEndpoint determines which collection to use**. Both indexing and search paths are misaligned on collection selection. Local testing on Stack Car (safe, disposable environment) will systematically verify, capture diagnostics, and pinpoint the exact file/line responsible for this mismatch.

**Relevant Architecture Docs:**
- `SESSION_CLOSEOUT.md` — closeout procedures
- `REVIEW_AGENT_GUIDE.md` — review criteria and dispositions

---

## Critical Information for This Task

### Credentials (if needed)
| Field | Value | Notes |
|-------|-------|-------|
| Local Docker | Already configured | Run `docker compose ps` from wvu_knapsack root to verify |
| Solr endpoint | http://localhost:8983/solr/ | Local Stack Car only |
| Rails app URL | http://localhost:3000 | Local Stack Car only |

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: Collection mismatch affects BOTH versions, not just 7.2.0**
- ❌ Wrong: Assume 7.1.3 baseline works perfectly on its own
- ✅ Right: v7.1.3 also has the collection issue — but SolrEndpoint may point to a "good enough" collection by default
- Why: The mismatch is in how SolrEndpoint resolves the collection name; defaults can mask it at 7.1.3

⚠️ **GOTCHA 2: Stack Car volumes are disposable**
- ❌ Wrong: Keep old data between version switches
- ✅ Right: `docker compose down --volumes` before each version switch to ensure clean state
- Why: Old Solr collections from previous version will contaminate comparison

⚠️ **GOTCHA 3: Save /tmp test data BEFORE docker compose down**
- ❌ Wrong: Assume /tmp persists across container rebuilds
- ✅ Right: Copy all diagnostic files out of /tmp before any restart; save to project handoffs/ if critical
- Why: Docker volume operations may affect ephemeral container filesystems

⚠️ **GOTCHA 4: No code changes — pure investigation**
- ❌ Wrong: Fix the bug during testing (scope creep)
- ✅ Right: Only run read queries, capture diffs, collect data — report findings
- Why: This task is to INFORM the fix strategy, not to implement it

---

## Problem Statement

When upgrading Hyku from v7.1.3 to v7.2.0, search returns empty results while records remain visible in Solr at document level. The root cause hypothesis is a Valkyrie indexer misrouting documents to a UUID-based Solr collection instead of the standard hydra-production collection.

**Current behavior**: Search page shows "0 results found" despite records existing
**Expected behavior**: Search returns matching documents (same as production at v7.1.3)

---

## Success Criteria

| Phase | Test | Pass Condition |
|-------|------|----------------|
| 0 | Stack Car starts | `docker compose ps` shows web + solr healthy |
| 1.1 | Solr has docs at v7.1.3 | At least one collection with doc count > 0 |
| 1.2 | Search API returns results at v7.1.3 | `numFound > 0` for at least one query |
| 1.3 | Rails connects to correct SolrEndpoint | URL points to a collection with documents |
| 1.4 | Web UI displays results at v7.1.3 | Response contains document data (not empty) |
| 2.x | Same tests on v7.2.0 show bug | Evidence of collection mismatch or search failure |
| 3.x | Root cause identified | Exact file/line causing collection mismatch is named |
| Overall | Fix strategy informed | Findings specify which file to patch and what change needed |

---

## Files Involved

### Primary Files — you will inspect these (read-only, no edits)
| File | Purpose | Key Method/Section |
|---|---|---|
| `hyrax-webapp/app/models/solr_endpoint.rb` | SolrEndpoint model — resolves collection name | `#url`, `#id` |
| `hyrax-webapp/app/models/account.rb` | Account model — belongs_to solr_endpoint | `#solr_endpoint` association |
| `hyrax-webapp/app/jobs/create_solr_collection_job.rb` | Indexing logic | Collection creation/assignment |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `hyrax-webapp/Gemfile` | Verify hyrax/valkyrie/blacklight gem versions |
| `hyrax-webapp/app/models/concerns/solr_query_parameters_extension.rb` | Blacklight query parameter handling (if exists) |

### Migration (if needed)
- [ ] No migration needed — this task is pure investigation only

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/wvulibraries_knapsack/tasks/backlog/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md \
       projects/wvulibraries_knapsack/tasks/active/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md

# Then open the moved file and change: status: backlog → status: active
# Paste the output of both commands in chat before proceeding.
```

### Step 1 — Prerequisites & Safety Setup (Phase 0)

Run from `/Users/tam0013/Documents/git/wvu_knapsack`:

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Verify Stack Car can start
docker compose ps

# If nothing running, start fresh (safe — can restart freely)
sh up.sc.local.sh 2>&1 | tee /tmp/sc-startup.log &

# Wait for containers to be healthy
sleep 30
docker compose ps

# Verify current branch gem versions
grep -A 2 "hyrax" hyrax-webapp/Gemfile
grep -A 2 "valkyrie" hyrax-webapp/Gemfile

# Check which branch we are on
git branch --show-current
```

**Save to handoffs/ the full content of this step's output.**

### Step 2 — v7.1.3 Baseline Testing (Phase 1)

Run ALL commands below, capturing output to /tmp files:

#### Phase 1.1: Verify Records Exist in Solr at v7.1.3

```bash
# List all collections in Solr
curl -s "http://localhost:8983/solr/admin/cores?wt=json" | python3 -c "import sys, json; [print(c) for c in json.load(sys.stdin)['status'].keys()]" > /tmp/solr-coll-list-713.txt

cat /tmp/solr-coll-list-713.txt
```

#### Phase 1.2: Document Counts Per Collection at v7.1.3

```bash
for coll in $(cat /tmp/solr-coll-list-713.txt); do
  count=$(curl -s "http://localhost:8983/solr/$coll/select?q=*:*&rows=0&wt=json" | python3 -c "import sys, json; print(json.load(sys.stdin)['response']['numFound'])")
  echo "$coll: $count docs"
done > /tmp/solr-doc-counts-713.txt

cat /tmp/solr-doc-counts-713.txt
```

**Save criteria**: At least one collection must have doc count > 0. If all are 0, v7.1.3 also has no data (different problem).

#### Phase 1.3: Verify Rails SolrEndpoint Configuration at v7.1.3

```bash
docker compose exec web bin/rails runner "
  acct = Account.first
  puts 'Account: ' + acct.name
  puts 'Solr Endpoint URL: ' + acct.solr_endpoint.url
  puts 'Solr Endpoint ID: ' + acct.solr_endpoint.id
" > /tmp/solr-endpoint-details-713.txt 2>&1

cat /tmp/solr-endpoint-details-713.txt

# Check Rails logs for Solr connection info
docker compose logs web 2>&1 | grep -i "solr\|collection\|endpoint" | tail -30 > /tmp/rails-solr-logs-713.log
```

#### Phase 1.4: Verify Search Returns Results at v7.1.3

```bash
# API-level test (hydra-production collection)
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=10&wt=json" | python3 -c "import sys, json; d=json.load(sys.stdin); print('hydra-production Found:', d['response']['numFound'])" > /tmp/search-hydra-713.txt

# Web UI test
curl -s "http://localhost:3000/catalog?q=test&per_page=5" > /tmp/search-ui-response-713.html 2>&1
grep -i "found\|result\|document\|no_results" /tmp/search-ui-response-713.html | head -10 > /tmp/search-ui-summary-713.txt

cat /tmp/search-hydra-713.txt
cat /tmp/search-ui-summary-713.txt
```

**Save criteria**: Search must return documents (numFound > 0 OR response contains document data). This proves v7.1.3 baseline is functional.

### Step 3 — v7.2.0 Broken Testing (Phase 2)

#### Phase 2.1: Switch Branch to v7.2.0 and Rebuild

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# First, down current services
docker compose down --volumes

# Check out 7.2.0 branch
git checkout feature/hyku-v7.2.0-upgrade

# Verify Gemfile changes
grep -A 2 "hyrax" hyrax-webapp/Gemfile
grep -A 2 "valkyrie" hyrax-webapp/Gemfile

# Start fresh with 7.2.0 gems
sh up.sc.local.sh 2>&1 | tee /tmp/sc-startup-720.log &
sleep 30
docker compose ps
```

#### Phase 2.2: Run EXACT SAME Queries as Phase 1 on v7.2.0

**Do NOT modify the queries — run identical tests to get a controlled comparison.**

```bash
# 2.2.1: List Solr collections (compare with /tmp/solr-coll-list-713.txt)
curl -s "http://localhost:8983/solr/admin/cores?wt=json" | python3 -c "import sys, json; [print(c) for c in json.load(sys.stdin)['status'].keys()]" > /tmp/solr-coll-list-720.txt
cat /tmp/solr-coll-list-720.txt

# 2.2.2: Document counts per collection (compare with /tmp/solr-doc-counts-713.txt)
for coll in $(cat /tmp/solr-coll-list-720.txt); do
  count=$(curl -s "http://localhost:8983/solr/$coll/select?q=*:*&rows=0&wt=json" | python3 -c "import sys, json; print(json.load(sys.stdin)['response']['numFound'])")
  echo "$coll: $count docs"
done > /tmp/solr-doc-counts-720.txt
cat /tmp/solr-doc-counts-720.txt

# 2.2.3: Rails SolrEndpoint for tenant (compare with Phase 1)
docker compose exec web bin/rails runner "
  acct = Account.first
  puts 'Account: ' + acct.name
  puts 'Solr Endpoint URL: ' + acct.solr_endpoint.url
  puts 'Solr Endpoint ID: ' + acct.solr_endpoint.id
" > /tmp/solr-endpoint-details-720.txt 2>&1
cat /tmp/solr-endpoint-details-720.txt

# 2.2.4: Search hydra-production collection (same as Phase 1.3)
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=10&wt=json" | python3 -c "import sys, json; d=json.load(sys.stdin); print('Found:', d['response']['numFound'])" > /tmp/search-hydra-720.txt
cat /tmp/search-hydra-720.txt

# 2.2.5: Web UI search (same as Phase 1.4)
curl -s "http://localhost:3000/catalog?q=test&per_page=5" > /tmp/search-ui-response-720.html 2>&1
grep -i "found\|result\|document\|no_results" /tmp/search-ui-response-720.html | head -10 > /tmp/search-ui-summary-720.txt
cat /tmp/search-ui-summary-720.txt

# 2.2.6: Search ANY collection that has docs (to find where documents actually live)
for coll in $(awk '{print $1}' /tmp/solr-doc-counts-720.txt | grep -v ':'); do
  count=$(grep "$coll" /tmp/solr-doc-counts-720.txt | awk -F': ' '{print $2}')
  if [ "$count" != "0" ]; then
    result=$(curl -s "http://localhost:8983/solr/$coll/select?q=*:*&rows=10&wt=json" | python3 -c "import sys, json; d=json.load(sys.stdin); print('Found:', d['response']['numFound'])")
    echo "Collection $coll ($count docs): $result"
  fi
done > /tmp/search-other-colls-720.txt
cat /tmp/search-other-colls-720.txt
```

#### Phase 2.3: Generate Comparison Summary

```bash
echo "=== COLLECTION LIST DIFF (713 vs 720) ==="
diff -u /tmp/solr-coll-list-713.txt /tmp/solr-coll-list-720.txt || echo "(no diff)"

echo ""
echo "=== DOC COUNTS DIFF (713 vs 720) ==="
diff -u /tmp/solr-doc-counts-713.txt /tmp/solr-doc-counts-720.txt || echo "(no diff)"

echo ""
echo "=== SolrEndpoint DIFF (713 vs 720) ==="
diff -u /tmp/solr-endpoint-details-713.txt /tmp/solr-endpoint-details-720.txt || echo "(no diff)"

echo ""
echo "=== SEARCH COMPARISON ==="
echo "v7.1.3: $(cat /tmp/search-hydra-713.txt 2>/dev/null)"
echo "v7.2.0: $(cat /tmp/search-hydra-720.txt 2>/dev/null)"

echo ""
echo "=== WEB UI COMPARISON ==="
echo "v7.1.3:"; cat /tmp/search-ui-summary-713.txt
echo "v7.2.0:"; cat /tmp/search-ui-summary-720.txt
```

### Step 4 — Root Cause Investigation (Phase 3)

#### Phase 3.1: Code Diff Analysis

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# List all Solr/Valkyrie related changes in this branch
git diff main..feature/hyku-v7.2.0-upgrade --name-only | grep -i "solr\|valkyrie\|index\|collection" > /tmp/solr-changed-files-720.txt
cat /tmp/solr-changed-files-720.txt

# Diff the SolrEndpoint model (key file — this is most likely where collection name is set)
git diff main -- hyrax-webapp/app/models/solr_endpoint.rb > /tmp/diff-solr-endpoint-720.txt 2>/dev/null || echo "solr_endpoint.rb not changed in repo"

# Diff the Account model (where SolrEndpoint belongs_to relationship lives)
git diff main -- hyrax-webapp/app/models/account.rb > /tmp/diff-account-720.txt 2>/dev/null || echo "account.rb not changed in repo"

# Diff create_solr_collection_job (key indexing logic)
git diff main -- hyrax-webapp/app/jobs/create_solr_collection_job.rb > /tmp/diff-create-collection-job-720.txt 2>/dev/null || echo "create_solr_collection_job.rb not changed in repo"
```

#### Phase 3.2: Runtime Valkyrie Adapter Inspection

```bash
docker compose exec web bin/rails runner "
  puts '=== Valkyrie Indexing Adapter ==='
  puts Hyrax.index_adapter.class.name
  puts Hyrax.config.query_index_with_solr_document_class.name
  
  # Show SolrEndpoint resolution for default tenant
  acct = Account.first
  endpoint = acct.solr_endpoint
  puts '=== SolrEndpoint Resolution ==='
  puts 'URL: ' + endpoint.url
  puts 'ID: ' + endpoint.id
  
  # Show Blacklight connection config (search path)
  puts '=== Blacklight Connection Config ==='
  puts JSON.pretty_generate(Blacklight.connection_config)
" > /tmp/valkyrie-blacklight-720.txt 2>&1

cat /tmp/valkyrie-blacklight-720.txt
```

#### Phase 3.3: Capture Solr Query Logs During Search

```bash
# Enable debug logging by triggering a search and watching Rails logs
docker compose logs -f web 2>&1 | grep -i "solr\|q=\|select" | tee /tmp/realtime-solr-queries-720.log &

# Trigger a real search via curl
curl -s "http://localhost:3000/catalog?q=test&per_page=5" > /dev/null 2>&1
sleep 5

# Check what queries were logged during the search
docker compose logs web 2>&1 | grep "q=" | tail -20 > /tmp/search-queries-720.txt
cat /tmp/search-queries-720.txt
```

#### Phase 3.4: Collect Solr Document Samples (Evidence)

```bash
for coll in $(awk '{print $1}' /tmp/solr-doc-counts-720.txt | grep -v ':'); do
  count=$(grep "$coll" /tmp/solr-doc-counts-720.txt | awk -F': ' '{print $2}')
  if [ "$count" != "0" ]; then
    curl -s "http://localhost:8983/solr/$coll/select?q=*:*&rows=1&wt=json" > /tmp/sample-doc-$coll-720.json
    echo "Sample from $coll ($count docs): saved to /tmp/sample-doc-$coll-720.json"
  fi
done

# Identify where documents ARE vs WHERE search queries them FROM
echo "=== COLLECTION MISMATCH ANALYSIS ==="
echo "Documents are in collections:"
awk '{print $1}' /tmp/solr-doc-counts-720.txt | grep -v ': ' | while read coll; do
  count=$(grep "$coll" /tmp/solr-doc-counts-720.txt | awk -F': ' '{print $2}')
  if [ "$count" != "0" ]; then echo "  - $coll ($count docs)"; fi
done

echo ""
echo "Search queries from SolrEndpoint URL:"
grep 'URL:' /tmp/solr-endpoint-details-720.txt 2>/dev/null || echo "N/A"

echo ""
echo "If the collection with documents ≠ the SolrEndpoint URL, MISMATCH CONFIRMED"
```

### Step 5 — Revert to v7.1.3 (Phase 5)

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Revert safely after testing is complete
git checkout main
docker compose down --volumes
sh up.sc.local.sh 2>&1 | tee /tmp/sc-revert-713.log &
sleep 30
docker compose ps

echo "Reverted to v7.1.3 baseline successfully"
```

### Step 6 — Generate Comparison Report (Phase 4)

```bash
cat > /tmp/testing-report-final.txt << 'ENDREPORT'
=== HYKU 7.2.0 SEARCH BUG LOCAL TESTING REPORT ===
Date: 2026-11-05
Environment: Stack Car (local Docker Compose)
Branch tested: feature/hyku-v7.2.0-upgrade

--- PHASE 1: v7.1.3 BASELINE ---
Collections: $(cat /tmp/solr-coll-list-713.txt | tr '\n' ', ')
Doc counts: $(cat /tmp/solr-doc-counts-713.txt)
SolrEndpoint URL: $(grep 'URL:' /tmp/solr-endpoint-details-713.txt 2>/dev/null || echo 'N/A')
Search results (API): $(cat /tmp/search-hydra-713.txt 2>/dev/null || echo 'N/A')
Web UI results: $(cat /tmp/search-ui-summary-713.txt 2>/dev/null || echo 'N/A')

--- PHASE 2: v7.2.0 BROKEN STATE ---
Collections: $(cat /tmp/solr-coll-list-720.txt | tr '\n' ', ')
Doc counts: $(cat /tmp/solr-doc-counts-720.txt)
SolrEndpoint URL: $(grep 'URL:' /tmp/solr-endpoint-details-720.txt 2>/dev/null || echo 'N/A')
Search results (API): $(cat /tmp/search-hydra-720.txt 2>/dev/null || echo 'N/A')
Web UI results: $(cat /tmp/search-ui-summary-720.txt 2>/dev/null || echo 'N/A')

--- PHASE 3: ROOT CAUSE ---
Changed files (Solr/Valkyrie): $(cat /tmp/solr-changed-files-720.txt | tr '\n' ', ')
Adapter config: $(cat /tmp/valkyrie-blacklight-720.txt 2>/dev/null || echo 'N/A')
Search queries during execution: $(cat /tmp/search-queries-720.txt 2>/dev/null || echo 'N/A')

--- COLLECTION MISMATCH ANALYSIS ---
Documents are in: $(awk '{print $1}' /tmp/solr-doc-counts-720.txt | grep -v ': ' | while read c; do count=$(grep "$c" /tmp/solr-doc-counts-720.txt | awk -F': ' '{print $2}'); [ "$count" != "0" ] && echo "$c ($count docs)"; done | tr '\n' ', ')
Search queries: $(grep 'URL:' /tmp/solr-endpoint-details-720.txt 2>/dev/null || echo 'N/A')
MISMATCH: $(if grep -q 'hydra-production' /tmp/solr-endpoint-details-720.txt 2>/dev/null && ! grep -q 'hydra-production.*0 docs' /tmp/solr-doc-counts-720.txt 2>/dev/null; then echo "YES — documents are NOT in the searched collection"; else echo "No mismatch detected or needs verification"; fi)

--- CONCLUSION ---
$(if [ "$(cat /tmp/search-hydra-720.txt 2>/dev/null | grep -o '[0-9]\+$' | tail -1)" = "0" ]; then
  echo "SEARCH BUG CONFIRMED: v7.2.0 returns empty results at API level"
else
  echo "Search behavior requires further investigation"
fi)

--- FILES REFERENCED ---
/tmp/solr-coll-list-713.txt | /tmp/solr-coll-list-720.txt
/tmp/solr-doc-counts-713.txt | /tmp/solr-doc-counts-720.txt
/tmp/solr-endpoint-details-713.txt | /tmp/solr-endpoint-details-720.txt
/tmp/search-hydra-713.txt | /tmp/search-hydra-720.txt
/tmp/valkyrie-blacklight-720.txt
/tmp/diff-solr-endpoint-720.txt
/tmp/diff-account-720.txt
/tmp/diff-create-collection-job-720.txt
ENDREPORT

cp /tmp/testing-report-final.txt /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-7.2.0-SEARCH-BUG-LOCAL-TESTING-RESULTS.md
```

---

## Review Gate

You MAY NOT commit, push, or mark any task step as "complete" until BOTH conditions are met:

1. **Reviewer has issued a GO disposition** — GO/REVISE/STOP per REVIEW_AGENT_GUIDE.md
2. **Human authorization** — explicit human message authorizing commit AND separate authorization for push

**Before requesting review:** Paste your complete evidence in chat (all /tmp outputs + comparison report) and state: *"All implementation and verification complete. Requesting reviewer assessment."*

---

## Synthesis Report Template

Save this as an MD file before starting any work:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Local Testing of Hyku 7.2.0 Search Bug — Stack Car Investigation
**Status**: draft → active (after Step 0) → completed
**Date**: 2026-11-05

### What I'm About to Do
Run a 5-phase testing procedure on local Stack Car to verify the Solr collection mismatch bug between Hyku v7.1.3 and v7.2.0. Phase 0: environment setup; Phase 1: v7.1.3 baseline (records exist, search works); Phase 2: v7.2.0 broken state (same tests show difference); Phase 3: root cause investigation (code diffs + runtime inspection); Phase 4-5: report generation and revert.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| /Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp/Gemfile | Verify gem versions | pending |
| /tmp/solr-coll-list-*.txt | Solr collection names per version | not started |
| /tmp/solr-doc-counts-*.txt | Document counts per collection | not started |
| /tmp/solr-endpoint-details-*.txt | Rails SolrEndpoint config per version | not started |
| /tmp/valkyrie-blacklight-720.txt | Runtime Valkyrie + Blacklight config | not started |
| All diff files under /tmp/* | Code changes between 7.1.3 and 7.2.0 | not started |

### Prerequisites Completed
- ✅ Task file moved to active/ with git mv (find output pasted in chat)
- ✅ YAML status updated from draft → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide and status.md
- ✅ Read this task file and understand all gotchas

### Expected Outcomes
Exact identification of which file/line causes SolrEndpoint to resolve the wrong collection name. Evidence proving: (1) documents exist in some collection, (2) search queries a different collection, (3) the code path that sets the collection name differs between versions.

### Critical Gotchas I Will Avoid
- ❌ Fixing the bug during testing — instead ✅ Only run read queries and collect data
- ❌ Skipping docker compose down --volumes between version switches — instead ✅ Clean state always
- ❌ Assuming /tmp persists across container operations — instead ✅ Copy critical outputs before any restart
```

---

## Planner Self-Validation (Draft Author Checklist)

*Planner self-validation — not sent to agents. For draft author only.*

- [x] YAML frontmatter complete (status: draft, resume, priority, type, system_domain, mvp_alignment, local_worker_safe)
- [x] DRAFT STATUS NOTE filled (says what was verified, what wasn't)
- [x] DRAFT REVIEW CHECKLIST filled (marks items reviewed by planner vs. deferred to review agent)
- [x] Agent Dispatch Interface complete and accurate (no bracketed placeholders except <agent-tasks>)
- [x] Full task body present (all 6 phases, all commands, all success criteria)
- [x] No placeholder text or [FILL IN HERE] markers
- [x] File is in tasks/drafts/ with status: draft
- [x] Acceptance Criteria are clear and measurable
- [x] Task scope is well-bounded (investigate Solr collection mismatch via controlled testing, not fix it)
- [x] Procedures are specific enough for implementation (phase-by-phase with concrete commands)
- [x] All file paths verified to exist locally
- [x] Architecture Gotchas are specific and actionable (4 gotchas identified)
- [x] Review Gate section included per TASK_TEMPLATE.md requirements
- [x] Synthesis Report Template included

---

## Acceptance Criteria

1. ✅ v7.1.3 baseline confirmed: at least one Solr collection has documents, search API returns results
2. ✅ v7.2.0 bug confirmed: same test on 7.2.0 shows empty results or different behavior
3. ✅ Collection mismatch proven: exact evidence that documents are in collection A but search queries collection B
4. ✅ Root cause identified: specific file(s) and code path in hyrax-webapp that causes the mismatch
5. ✅ Comparison report generated at handoffs/2026-11-05-HYKU-7.2.0-SEARCH-BUG-LOCAL-TESTING-RESULTS.md
6. ✅ Stack Car reverted to v7.1.3 baseline after testing complete

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Commit Instructions

> ⚠️ **PRE-COMMIT CHECK**: Before running ANY of the commands below, confirm:
> - [ ] Reviewer has issued GO disposition (not just pending)
> - [ ] Human has explicitly authorized the command I am about to run (commit or push) in a message that names it
> - [ ] Both conditions documented in your completion report
>
> If either condition is unmet, DO NOT proceed. Wait for the missing authorization.

Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]
git add projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: hyku-v7.2.0-upgrade fix strategy development
**Related tasks**: 2026-11-05 HYKU-UPGRADE-TESTING-STRATEGY (handoff document)

---

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
