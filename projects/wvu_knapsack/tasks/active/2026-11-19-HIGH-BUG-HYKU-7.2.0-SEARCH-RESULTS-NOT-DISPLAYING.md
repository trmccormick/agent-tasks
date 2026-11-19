---
status: active
resume: false
priority: HIGH
type: bug-investigation
system_domain: hyku-7.2.0-upgrade
mvp_alignment: hyku-7.2.0-upgrade-blocker
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md
       git ls-files --error-unmatch projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md
     If the source is not tracked, stage the source first:
       git add projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

  2. Move the task:
       git mv projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md \
              projects/wvu_knapsack/tasks/active/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/wvu_knapsack/tasks/active/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md
       test ! -e projects/wvu_knapsack/tasks/backlog/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md && test -f projects/wvu_knapsack/tasks/active/2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record all Step 0 outputs in your synthesis report before proceeding.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/wvu_knapsack/README.md
  3. <agent-tasks>/projects/wvu_knapsack/SESSION_GUIDANCE.md
  4. The task file (this file)

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/wvu_knapsack/summaries/
  Filename pattern: 2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md
  Update the report: after Step 0, after diagnosis, before proposing fixes.
  In chat, provide only the report path and status; do not paste raw output.

Closeout: follow projects/wvu_knapsack/SESSION_CLOSEOUT.md
```

---

## 🟡 INVESTIGATION OBJECTIVE

**Goal**: Determine why search results don't display on hykudev (production mode 7.2.0 upgrade) while they display correctly on local dev (Stack Car 7.2.0).

**Key Finding**: Fresh local dev + import + upgrade = ✅ works. Hykudev production data + upgrade = ❌ no results (but facets have counts, so data/Solr accessible).

**Root Cause Unknown** — investigate systematically to identify whether the issue is:
- Solr state (indexing, collection configuration, data format)
- Schema incompatibility (field mismatch between 7.1.3 and 7.2.0)
- Display template issue (view layer regression)
- Query builder mismatch (Rails search not using correct Solr fields)

---

## 🔴 CRITICAL FACTS (Read Carefully)

1. **This is NOT a code bug** — same codebase (feature/hyku-v7.2.0-upgrade branch) works locally but fails on hykudev
2. **This is NOT the field_config.zero? error** — that was fixed (commit fb6b56a) and is no longer blocking
3. **Data exists** — facet counts prove Solr has records
4. **Services healthy** — web, worker, Solr all running, initialize_app succeeded
5. **Local works** — Stack Car dev environment displays results correctly with same code/data import strategy

---

## 🟡 SYMPTOM COMPARISON

| Environment | Facets | Counts | Results Display | Notes |
|---|---|---|---|---|
| Local dev (Stack Car) | ✅ Yes | ✅ Yes (accurate) | ✅ Yes | 5 test records, full 7.2.0 upgrade verified |
| Hykudev (Production mode) | ✅ Yes | ✅ Yes (non-zero) | ❌ No | Services healthy, initialization succeeded |

---

## Architecture Gotchas

### Solr Collection Issues (From Oct-Nov Sessions)
- **Previous symptom**: UUID collection pre-existed on hykudev; Valkyrie indexed to UUID, Rails queried hydra-production (empty)
- **Current**: Check if UUID collection still exists or if collections are correct
- **Why**: 7.2.0 uses Valkyrie indexer; Solr collection configuration changed; pre-existing data can cause divergence

### Production vs Dev Differences
- **Local**: Stack Car with Traefik, fresh DB/Solr init from seed
- **Hykudev**: Production docker-compose.production.yml, 2+ years of production data, production Solr config
- **Field schemas may differ** between Stack Car defaults and production config

### No Automatic Re-Index During Upgrade
- Scripts don't trigger Solr re-indexing
- Local works because fresh import = fresh indexing
- Hykudev may have stale Solr index from pre-upgrade (7.1.3 field format)

---

## 🟢 INVESTIGATION CHECKLIST

### Phase 1: Solr State Verification

**Task 1.1 — Solr Collection Status**
Run from hykudev:
```bash
docker compose exec -T solr solr status
# Report: Total collections, collection names, core status
# Look for: UUID collections (symptom of previous issue), hydra-production status
```

**Task 1.2 — Solr Document Count**
Run from hykudev:
```bash
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*" | jq '.response.numFound'
# Report: Total documents in hydra-production
# Compare: Local Solr doc count (if you can run same query locally)
```

**Task 1.3 — Verify Solr Reachability**
Run from hykudev web container:
```bash
docker compose exec web curl -s "http://solr:8983/solr/hydra-production/select?q=*:*&rows=1" | jq '.response.docs[0]'
# Report: First document structure, field names, field values
# Look for: _tesim, _sim, _dts fields (expected in 7.2.0 schema)
```

### Phase 2: Schema Compatibility Check

**Task 2.1 — Compare Field Schemas**
Run from hykudev:
```bash
curl -s "http://localhost:8983/solr/hydra-production/schema/fields" | jq '.fields | map(.name) | sort' > /tmp/hykudev-fields.json
# (If you have local Solr running) Run same curl locally and compare
# Report: Field list, missing/extra fields between local and hykudev
# Focus: _sim and _tesim fields used by facets and display
```

**Task 2.2 — Raw Solr Query Test**
Run from hykudev:
```bash
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=5&wt=json" | jq '.response.docs'
# Report: Do documents return? What fields are present?
# If docs return, schema is OK; issue is view layer or Rails search builder
```

### Phase 3: Rails Search Layer

**Task 3.1 — Rails Search Query**
Run from hykudev web container:
```bash
docker compose exec web bundle exec rails console
> results = Blacklight.repository.search({})
> puts results.inspect
# Report: Does Rails search return results? What is the response?
# If YES: data exists, issue is view/template layer
# If NO: issue is query builder or Solr adapter
```

**Task 3.2 — CatalogController Config**
Run from hykudev:
```bash
grep -n "search_builder_class\|solr_path" app/controllers/catalog_controller.rb config/initializers/catalog_controller_decorator.rb
# Report: Which search builder is configured?
# Compare: Local config (should match)
```

### Phase 4: Browser & View Layer

**Task 4.1 — Browser Console Check**
Access: `https://demo-hykudev.lib.wvu.edu/catalog?q=*`
- Open browser console (F12 → Console tab)
- Look for JavaScript errors
- Report: Any errors? Request failures?

**Task 4.2 — Page Source Inspection**
Right-click → View Page Source on the search results page
- Search for `<div class="document"` or similar result container
- Report: Is the HTML markup generated but hidden? Or completely absent?

**Task 4.3 — View Template Check**
Run from hykudev:
```bash
grep -r "document" hyrax-webapp/app/views/catalog/ | head -20
# Report: Which view template renders results?
# Check: app/views/catalog/index.html.erb or similar
```

---

## ✅ ACCEPTANCE CRITERIA

- [ ] **AC #1**: Root cause identified and documented (Solr state, schema, template, or query builder)
- [ ] **AC #2**: Evidence collected (command output, field schemas, Solr docs, Rails search results)
- [ ] **AC #3**: Diagnosis explains why local works but hykudev fails
- [ ] **AC #4**: Clear next step identified (what needs to be fixed)
- [ ] **AC #5**: Synthesis report saved to summaries/ folder with all findings

---

## 🛑 STOP CONDITIONS (Escalate Immediately)

1. **If Solr is unreachable** from web container → container networking issue (escalate infrastructure)
2. **If hydra-production collection doesn't exist** → Solr state corrupted (escalate to restart/recover)
3. **If Rails search works but view layer broken** → requires template debugging (escalate to code review)
4. **If multiple issues found** → report each separately; don't attempt fixes, just diagnose

---

## 📋 SYNTHESIS REPORT TEMPLATE

Create file: `<agent-tasks>/projects/wvu_knapsack/summaries/2026-11-19-INVESTIGATION-SEARCH-RESULTS-DISPLAY.md`

```markdown
# 2026-11-19 Investigation: Search Results Display Issue

**Status**: In Progress  
**Investigator**: [Your name]  
**Task**: 2026-11-19-HIGH-BUG-HYKU-7.2.0-SEARCH-RESULTS-NOT-DISPLAYING.md

## Step 0 Completion
[Paste git mv output, status verification]

## Diagnosis Phase

### Solr State
- Collections: [output]
- Doc count: [number]
- Status: [healthy/warning/error]

### Field Schema
- Fields match local: [yes/no]
- Missing fields: [list]
- Status: [compatible/mismatch]

### Rails Search
- Blacklight.repository.search response: [yes/no/error]
- Status: [works/broken]

### View Layer
- Browser console errors: [none/list]
- HTML markup generated: [yes/no/partially]
- Status: [renders/missing]

## ROOT CAUSE DIAGNOSIS
[One paragraph: what is the actual issue?]

## NEXT STEPS
[What needs to be fixed based on diagnosis]

## EVIDENCE
[Paste key command outputs, truncate if >50 lines]
```

---

## Related Files & Context

- **Wiki**: `/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/HYKU_7.2.0_UPGRADE_STATUS.md`
- **Build Guide**: `/Users/tam0013/Documents/git/wvu_knapsack/HYKU_BUILD_GUIDE.md`
- **Previous Issue** (Oct 2026): UUID collection divergence — see tasks/completed/ for details
- **Code Branch**: `feature/hyku-v7.2.0-upgrade` (all 4 fixes pushed, fb6b56a included)

---

**Created**: 2026-11-19 1:40 PM  
**Status**: Backlog — Ready for dispatch to Qwen  
**Blocking**: 7.2.0 production upgrade readiness
