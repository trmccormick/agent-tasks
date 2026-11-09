## STATUS SYNTHESIS REPORT

**Task**: Local Testing of Hyku 7.2.0 Search Bug — Stack Car Investigation  
**Status**: active (Step 0 done); Steps 1-7 not started  
**Date**: 2026-11-09

---

### What I'm About to Do

Investigate (read-only) why Hyku 7.2.0 search returns no results, by (1) reading the collection-resolution code at the 7.1.3 and 7.2.0 submodule SHAs, then (2) running identical seeded captures on both versions in Stack Car, then (3) comparing which collection holds documents against which collection the app's search path reads. No code is changed. Goal: determine if the cause is code-level (SolrEndpoint / Account / CreateSolrCollectionJob mismatch) or infrastructure-level, using controlled identical runs at both versions as evidence.

---

### Files I'll Reference

| File | Purpose | Status |
|---|---|---|
| hyrax-webapp/app/models/solr_endpoint.rb | Collection/core resolution, switch!, connection_options | pending |
| hyrax-webapp/app/models/account.rb | Account to SolrEndpoint relationship, build_solr_endpoint | pending |
| hyrax-webapp/app/jobs/create_solr_collection_job.rb | Collection creation, single-tenant endpoint assignment | pending |
| $HOME/hyku-720-testing/ (scratch) | Captured evidence, helper scripts, diff outputs | not started |

---

### Prerequisites Completed

| Item | Completed | Notes |
|---|---|---|
| Task file moved to active/ with git mv | ✅ Done | Step 0 completed; status: backlog → active |
| Read GUARDRAILS.md | ✅ Done | Rules 0-10 reviewed |
| Read project README.md | ✅ Done | Architecture, Stack Car workflow, submodule handling verified |
| Read project status.md | ✅ Done | Gem drift fixed (blacklight_advanced_search pinned); code-level collection mismatch proposed as the cause (hypothesis, not verified); v7.1.3 baseline restored, snapshot taken |
| Read testing-strategy handoff | ✅ Done | Four lessons: gem pinning critical, local-first debugging essential, collection mismatch is code issue (not data), fresh snapshots required |
| Working tree clean, refs recorded | pending | Step 1 will record BASE_SHA, TARGET_SHA, and verify both differ and match expected values |
| BASE_SHA verified as 7.1.x Hyku | pending | Step 1 will confirm BASE_SHA=2c49cfbbd72466c3b8c382b43a49e0b56503b832 (Hyku v7.1.3 tag) |

---

### Expected Outcomes

For **each version** (7.1.3 baseline and 7.2.0 target), document with captured file evidence:
1. Which Solr collection(s) hold documents (from `counts.tsv`)
2. Which collection/core the app resolves (from `endpoint_collection`, `endpoint_connection_options`, `blacklight_connection_config`, `valkyrie_solr_index_url`)
3. What the app's search path actually sees (from `catalog_search_path_numFound`, `hyrax_solrservice_numFound` vs `metadata_store_record_count`)
4. Whether web UI search returned results (from `catalog-summary.txt`)

**Primary conclusion**: Mismatch CONFIRMED or NOT CONFIRMED, with exact file/line names deciding indexing and search collection selection.

---

### Critical Gotchas I Will Avoid

| # | Gotcha | Wrong Approach ❌ | Right Approach ✅ |
|---|--------|------------------|-------------------|
| 1 | Mismatch may affect BOTH versions, not just 7.2.0 | Assume 7.1.3 baseline is "correct" because search returns results | At each version record WHICH collection holds documents and WHICH collection search queries. Defaults can mask a bug at 7.1.3. |
| 2 | `docker compose down --volumes` wipes database AND Solr | Expect records to survive version switch; compare seeded 7.1.3 to empty 7.2.0 run | Run down --volumes before each switch, then seed SAME data SAME way on each version. Contamination ruins comparison. |
| 3 | `hyrax-webapp` is a submodule; `git checkout` alone does NOT switch Hyku | Assume `git checkout <branch>` runs 7.2.0 code | Run `git checkout <ref> && git submodule update --init --recursive`, then verify `git submodule status hyrax-webapp` SHA differs between runs. |
| 4 | Do not assume `main` is v7.1.3 | Treat main as known baseline | Step 1 verifies what main pins before use as baseline. If not 7.1.x Hyku, STOP and ask. |
| 5 | Scratch files live on the HOST | Write to `/tmp` (cleared on reboot) or leave files in home root | Write only under `$HOME/hyku-720-testing`; copy final evidence to handoffs/ before cleanup. |
| 6 | `rails runner` does not run `switch!` for you | Read `Blacklight.connection_config` in bare runner and treat as what web process uses | Call `account.switch!` first (provided capture.sh does); treat runner output as "what app resolves for this account". |
| 7 | No code changes — pure investigation | Edit app, submodule, config or .env files | Read, query, diff and record only. Report findings; fix strategy is separate task. |

---

### Work Plan (Steps 0-7)

| Step | Phase | Description | Status |
|---|---|---|---|
| 0 | Bootstrap | Move task file to active/ and update status | done |
| 1 | Safety setup, refs and helper scripts | Record start state, define BASE/TARGET refs, capture SHAs, create helper scripts (wait_stack.sh, capture.sh, compare.sh) | pending |
| 2 | Static code analysis | Diff code between submodule SHAs; grep for collection/core/tenancy decisions in both versions; answer 5 architecture questions | pending |
| 3 | Baseline (7.1.3) | Clean Stack Car, checkout BASE_REF, submodule update, start, seed test data, run capture.sh 713 | pending |
| 4 | v7.2.0 | Clean Stack Car, checkout TARGET_REF, submodule update, start, seed same data, run capture.sh 720 | pending |
| 5 | Comparison and root cause | Run compare.sh; state for EACH version: collection(s) with docs, collection app resolves, file/line names each decision | pending |
| 6 | Revert to starting state | docker compose down, restore original ref and submodule SHA, verify working tree clean, restart Stack Car if needed | pending |
| 7 | Report, evidence copy and cleanup | Write findings into handoff markdown, remove $SCRATCH folder (only after report complete and non-empty) | pending |

---

### Acceptance Criteria

- [ ] 1. `BASE_SHA` and `TARGET_SHA` recorded and differ, and each matches its expected value in Step 1 (or the human confirmed a different SHA in chat)
- [ ] 2. Step 2 questions 1-5 answered with file and line citations from both SHAs
- [ ] 3. The exact seed command is recorded and the same seeded record count is shown at both versions
- [ ] 4. `capture.sh` completed for 713 and 720 with all output files present (collections, counts, endpoint, catalog, log)
- [ ] 5. For BOTH versions the report states, quoting captured files: the collection(s) holding documents, the collection/core the app resolves, and the app search path's numFound
- [ ] 6. Mismatch status stated explicitly as CONFIRMED or NOT CONFIRMED with the deciding file(s)/line(s) named — or a stated reason none can be named
- [ ] 7. Report saved at `handoffs/2026-11-05-HYKU-7.2.0-SEARCH-BUG-LOCAL-TESTING-RESULTS.md`, with no `FILL IN` text left
- [ ] 8. Parent repo and submodule restored to the recorded starting ref/SHA (diff against `start-submodule-status.txt` clean), working tree clean, scratch folder removed
- [ ] 9. No application, submodule, config or `.env` file was modified

---

### Context: Problem & Hypothesis

**Documented Symptom** (from 2026-11-05 status.md):
- Records visible in Solr (doc counts > 0)
- Search results empty (queries return 0 docs)
- Reported in the handoff: data appears intact but indexed to a different collection than search reads (treat as reported, not verified)

**Hypothesis Tested** (from testing-strategy handoff):
- Valkyrie indexing writes to one collection (e.g., UUID)
- Blacklight search reads from different collection (e.g., hydra-production)
- Hypothesis (NOT assumed true): the cause is code-level, in how SolrEndpoint / Account / CreateSolrCollectionJob resolve the collection. "Mismatch NOT CONFIRMED" is an acceptable outcome
- Both versions may have mismatch; 7.1.3 may just have defaults masking it

**Environment** (from handoff Lesson 2):
- Testing locally (Stack Car) first, before VM
- Gem versions critical (Gemfile.lock pinned)
- Collection isolation requires understanding Solr architecture

**Testing Strategy** (from handoff Lesson 3):
- Controlled identical runs at both versions (same seed, same data)
- Compare which collection has docs vs which search path queries
- Narrow to exact code file/line deciding each path

---

### Stop Conditions — Escalate to User If:

- The working tree is dirty at the start (do not stash or discard)
- `BASE_SHA` equals `TARGET_SHA`, or the baseline SHA is not a 7.1.x Hyku
- After `git submodule update`, the submodule SHA does not equal the expected SHA
- Stack Car cannot be started or does not become healthy after two clean attempts
- No documented seed method exists, or seeding leaves every collection at 0 documents
- The mismatch is not reproducible after two full clean runs (down --volumes, checkout, submodule update, seed)
- A primary file listed for inspection does not exist at the recorded SHA
- An architectural decision is required about which collection name is canonical
- The root cause lies outside the three primary files and would expand scope
- Any step would require writing or modifying application code (this task is investigation only)

---

### Review Gate

You MAY NOT commit, push, or mark any task step as "complete" until BOTH conditions are met:

1. **Reviewer has issued a GO disposition** — GO/REVISE/STOP per REVIEW_AGENT_GUIDE.md
2. **Human authorization** — explicit human message authorizing commit AND separate authorization for push

**Before requesting review:** Paste your complete evidence in chat (the report plus the key captured files) and state: *"All implementation and verification complete. Requesting reviewer assessment."*

---

### Summary

**Ready to start Steps 0-7 of the local testing plan.** All prerequisites read; context understood from 2026-11-05 diagnostic session (gem drift fixed; a code-level collection mismatch is the working hypothesis, not yet verified). Will execute identical controlled runs at 7.1.3 baseline and 7.2.0 target, capturing Solr collections, doc counts, endpoint resolutions, and search results. Evidence will determine if mismatch is confirmed and which file/line decides collection selection for indexing vs search.

**Next immediate action:** Step 0 is already complete. Do NOT repeat it (do not run git mv again). Proceed with Step 1 (safety setup, refs, helper scripts) only when the human says to start.
