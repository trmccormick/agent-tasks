# Hyku 7.2.0 Local Testing — Synthesis Report

**Session Date**: 2026-11-09  
**Task**: `2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md`  
**Priority**: HIGH | **Type**: bug-fix  
**MVP Alignment**: hyku-7.2.0-upgrade-blocker  

---

## Prerequisites Completed

| # | Prerequisite | Status |
|---|-------------|--------|
| 1 | Read `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` | ✅ Done |
| 2 | Read `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md` | ✅ Done |
| 3 | Read `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md` | ⏳ Not yet done |
| 4 | Read `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-UPGRADE-TESTING-STRATEGY.md` | ⏳ Not yet done |
| 5 | Read full task file (this synthesis) | ✅ Done |

---

## Task Summary

### What This Task Is
Local testing to investigate why Hyku 7.2.0 shows "0 results" on catalog search even though records appear to exist in Solr. Testing whether the cause is a **code-level mismatch** (SolrEndpoint / Account / CreateSolrCollectionJob resolving collection names differently between indexing and search paths) versus other causes.

### What Is NOT This Task
- NOT a Gemfile.lock drift fix (the earlier `blacklight_advanced_search` v8.x pinning issue is resolved separately)
- NOT a production deployment task
- NOT a hykudev VM investigation

---

## Key Facts Recorded

### Environment Configuration
| Item | Value |
|------|-------|
| App repo | `/Users/tam0013/Documents/git/wvu_knapsack` |
| Solr (local) | http://localhost:8983/solr |
| Rails app (local) | http://localhost:3000 |
| Scratch folder | `$HOME/hyku-720-testing` |
| Compose service | `web` (confirm with `docker compose ps --services`) |

### Architecture Gotchas
1. **GOTCHA 1 — Baseline may be "correct" by accident**: The 7.1.3 search working may just mean indexing and search both point at the same default collection. Must record WHICH collection holds documents vs. WHICH collection search queries at EACH version.
2. **GOTCHA 2 — `docker compose down --volumes` wipes data**: Seed SAME data SAME way on each version before comparing.
3. **GOTCHA 3 — Submodule requires `git submodule update`**: Changing the Hyku version via git checkout alone does NOT switch the submodule.

### Provisional File/Line References (PROVISIONAL — must be re-verified against actual SHAs recorded in Step 1)
- `hyrax-webapp/` is a **git submodule** pointing to `samvera/hyku`, branch `7.2-stable`
- `SolrEndpoint#switch!` sets Valkyrie indexing adapter, Blacklight connection config, and Hyrax SolrService from `connection_options`
- `connection_options` sets `base_options['core'] ||= switchable_options['collection']`
- `CreateSolrCollectionJob#perform` branches on `Hyku.single_tenant?`
- **DO NOT look for**: `solr_query_parameters_extension.rb` — does not exist at this SHA

---

## Work Plan (from Task File Steps)

### Step 1: Verify Environment & Record Baseline SHAs
- Confirm local stack is running via Stack Car
- Record current Hyku submodule SHA and version
- Record Solr collections and their document counts

### Step 2: Investigate Solr Collection Resolution
- Trace how indexing resolves collection name (SolrEndpoint, CreateSolrCollectionJob)
- Trace how search resolves collection name (Blacklight connection_config)
- Determine if both paths agree on the same collection

### Step 3: Test at Both Versions (7.1.3 and 7.2.0)
- Run `down --volumes`, seed data, verify indexing writes to correct collection
- Switch versions with `git submodule update`, seed SAME data, compare results
- Record WHICH collection has documents vs. WHICH collection search queries

### Step 4: Document Findings & Determine Root Cause
- Conclude whether the issue is code-level (collection mismatch) or other cause
- Provide actionable remediation path

---

## Acceptance Criteria

| # | Criterion | Status |
|---|-----------|--------|
| 1 | Both Hyku 7.1.3 and 7.2.0 tested with SAME seeded data | Not started |
| 2 | Solr collection used for indexing recorded at each version | Not started |
| 3 | Solr collection used for search recorded at each version | Not started |
| 4 | Root cause documented (code mismatch or other) | Not started |
| 5 | Synthesis report saved (this file) | ✅ Done (template complete) |

---

## Files Referenced in This Task

| File | Purpose |
|------|---------|
| `tasks/active/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md` | This task file |
| `status.md` | Project progress tracking |
| `handoffs/2026-11-05-HYKU-UPGRADE-TESTING-STRATEGY.md` | Previous testing strategy (context) |
| `GUARDRAILS.md` | Operational rules (Rule 1 Docker, Rule 3 RSpec, etc.) |
| `SESSION_GUIDANCE.md` | Project-specific paths and rules |
| `README.md` | Domain context for wvulibraries_knapsack |

---

## Blockers / Dependencies

- **Docker/Stack Car**: Local stack must be running (`sh up.sc.local.sh`)
- **DevOps access**: hykudev VM — agents have no access, only Tracy/DevOps
- **Previous testing strategy handoff**: Must be read for context before starting work

---

## Notes

- Neither "Solr data issue" nor "code bug" framing is assumed true — evidence determines which
- Baseline comparison at 7.1.3 is essential even if it appears to "work" (GOTCHA 1)
- Scratch outputs go to `$HOME/hyku-720-testing`; cleaned up in Step 7
