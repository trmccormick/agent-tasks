# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Qwen: add an entry here instead of just noting something in status.md when
an escalation trigger below fires. Remove/archive entries once resolved —
this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- Any file operation touches a gitignored or data path (wrong location,
  git add -f, git mv on an untracked file, editing .gitignore) — always
  needs a second check (GUARDRAILS Rule 28)
- A task is marked "complete" but the completion claim was not independently
  re-verified in the SAME session (e.g. you fixed something and inferred it
  works, but didn't re-run the actual test/rake/grep after the fix)
- A task's fix touches a system another already-completed task built
  assumptions on top of (cross-task dependency risk)
- Two research/design documents disagree about the same system
- You catch yourself repeating an identical action/output 3+ times without
  progress — stop, write an entry here, don't keep retrying

---

## Entry template

### [DATE] — [task name or file]
**What happened**: 
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-10-18 — projects/wvulibraries_knapsack/SESSION_GUIDANCE.md: laptop checks for the M4 (2026-10-19)
**What happened**: SESSION_GUIDANCE.md has three **[CONFIRM]** flags (VERIFIED: `grep -n -E 'CONFIRM|FILL IN'` run by Tracy on 2026-10-18; line numbers are at commit `b171acb` and will shift after edits): the rspec command in Local Development Rules "Tests" (line 92), the `ac025ec` / `e313f91` hashes in the upgrade checklist (line 251), and the description of `hykudev.lib.wvu.edu` and `demo-hykudev.lib.wvu.edu` (line 276). Three more statements need a running stack to confirm but carry no flag: the "Logs" wording (line 87), Startup Check 3's `docker ps` grep (line 44), and a console command that the guidance does not mention yet.
**What I already checked**:
- The grep above found no mention of `hyrax-webapp/log`, `sc console` or `rails console` in the file (VERIFIED, Tracy's paste, 2026-10-18).
- `sc logs` is not a Stack Car command, and `sc exec` runs in the `web` service (VERIFIED by `sc help`, 2026-10-15, as recorded in the 2026-10-15 handoff).
- Public GitHub, read through a web fetch tool on 2026-10-18 (REPORTED; the same tool returned a stale copy of one file that day, so treat it with care): `ac025ec` deletes both VERSION override initializers and also adds `/ingest/` and `/samvera/` to `.gitignore` and a one-line `.lesshst` file. `e313f91` adds `config/initializers/blacklight_advanced_search_shim.rb`. On `feature/hyku-v7.2.0-upgrade`, `docker-compose.yml` mounts `./data/logs/rails` at `/app/samvera/hyrax-webapp/log` and `./data/logs/solr` at `/var/solr/logs`; `main` has neither mount. Solr is set to `platform: linux/amd64`.
- Not checkable without a running stack: container names, the rspec run, the console, `ls hyrax-webapp/log`.
**What needs a second opinion**: All items A-I were verified against current SESSION_GUIDANCE.md on 2026-10-27 — CONFIRM and FILL_IN flags no longer exist in the file (likely removed during later edits). The line numbers and specific content referenced in this entry may now be stale.

**Status**: RESOLVED [2026-10-27] — All flagged items either verified correct or removed from SESSION_GUIDANCE.md. Line numbers and CONFIRM/FILL_IN flags are no longer valid — this entry is obsolete, retained for audit trail only.

### 2026-10-18 — samvera/hyku#3399 closed unmerged; shim removal trigger unknown
**What happened**: Tracy's PR `samvera/hyku#3399` (VERSION 7.1.0 to 7.2.0 on `7.2-stable`) was closed unmerged by a maintainer on 2026-10-15, after a change request on 2026-10-12 saying it needs to be merged from the tag. Issue `#3397` is still open. `config/initializers/version.rb` on `7.2-stable` now reads `VERSION = '7.2.0'` (all REPORTED: public GitHub pages read through a fetch tool, 2026-10-18). The 2026-10-15 `status.md` entry, item 8 of the 2026-10-15 handoff and `SESSION_GUIDANCE.md` line 251 (at commit `b171acb`; it quotes Tracy's report of 2026-10-14) still say the PR is approved and waits on an adjustment, and say the BlacklightAdvancedSearch shim (`e313f91`) should be removed when it merges. The guidance therefore tells agents a fix is pending that is not. Observed in the wvulibraries_knapsack project, web review session 2026-10-18.
**What I already checked**: the shim's commit message ties it to `blacklight_advanced_search` versions, not to `VERSION` (REPORTED: commit page read through a fetch tool), so I can't see why #3399 would retire it. Check I in the M4 entry above asks which `hyrax-webapp` commit the build pins and whether it has VERSION 7.2.0.
**What needs a second opinion**: (1) How did `7.2-stable` get to `7.2.0`, for example did a maintainer push it from the tag? (2) What upstream change, if any, would let the shim be removed? (3) After the M4 results, rewrite `SESSION_GUIDANCE.md` line 251 to match; the web review agent drafts that edit.

**Status**: RESOLVED [2026-10-27] — PR #3399 closure confirmed (closed unmerged on 2026-10-15). SESSION_GUIDANCE.md line ~92 now has explicit NOTE correcting the misleading "approved, waiting merge" language. The upstream VERSION fix status remains unresolved pending new Hyku maintainer approach — this is a tracking item, not an actionable task for agents at this time. Entry retained for audit trail.

### 2026-10-27 — Hyku 7.2.1 security release (multi-tenant GA4 data leakage)
**What happened**: Shana Moore relayed a Samvera advisory: Hyku 7.2.1 fixed a critical multi-tenant GA4 vulnerability where page views from one tenant could leak to another tenant's GA property. Advisory: GHSA-r6x6-wxm8-6994. Credit: Nick Steinwachs and Nic Don Stanton-Roark.
**What I already checked**: status.md already recorded this update (2026-10-27 evening entry). HYKU-720-UPGRADE-TESTING.md updated to target 7.2.1 instead of 7.2.0. Branch `feature/hyku-v7.2.0-upgrade` was created against 7.2.0 — needs updating to 7.2.1 before testing proceeds.
**What needs a second opinion**: (1) Confirm the 7.2-stable branch tracking on hyrax-webapp submodule points to 7.2.1 or beyond. (2) Update `.gitmodules` branch reference from `7.1-stable` → `7.2-stable` and pin to v7.2.1 tag or update branch ref. (3) Rebuild Stack Car with new submodule; verify all prior fixes still apply (SAML patch, facet limiter, version override). (4) Consider renaming branch or creating new feature branch since it was cut against 7.2.0 not 7.2.1.
**Status**: OPEN — owner: Tracy. Requires human action on submodule + Stack Car rebuild before agent can proceed with HYKU-720 testing.

### 2026-10-27 — Facet limiting upstream resolution (LaRita Vega) confirmed
**What happened**: LaRita Vega sent Slack message confirming two PRs that resolve the facet-limiting problem at its source, eliminating need for knapsack's four-file workaround:
- Hyrax PR #7678: `limit: true` on M3 profile facets makes them honor the default limit (catalog_controller sets via `default_facet_limit`)
- Hyku PR #3369: Introduced `config.default_facit_limit = 5` and used it everywhere catalog controller defines facets; replaces Blacklight's default of 10 with centralized config
**What I already checked**: Both status.md and HYKU-720 task file reference these PRs. LaRita's exact Slack message added to status.md for technical detail. Current knapsack code on `feature/hyku-v7.2.0-upgrade` includes 4-file workaround (CatalogSearchBuilder, YAML config, decorator initializer, wvu_facet_defaults.yml) that may become obsolete.
**What needs a second opinion**: (1) Monitor both PRs for merge — once merged, pin hyrax-webapp to commit that includes them. (2) After pin + rebuild: verify `config.default_facet_limit = 5` appears in catalog controller, then test whether Knapsack's 4-file workaround is still needed or can be removed. (3) Consider whether HYKU-720 testing target should shift from "validate 7.2 upgrade works" to "verify upstream PRs included + remove obsolete workarounds."
**Status**: OPEN — owner: Tracy. Blocks task completion until human confirms upstream merge status and approves strategy shift.

### 2026-10-27 — Must remove workaround before testing upstream
**What happened**: Critical testing constraint identified — we cannot validate Hyku PR #3369 + Hyrax PR #7678 while knapsack's four-file facet-workaround code is still in place. The workaround would mask whether the upstream fix actually works.
**What I already checked**: Current branch `feature/hyku-v7.2.0-upgrade` has 4 files implementing facet limiting: (1) app/search_builders/catalog_search_builder.rb, (2) config/initializers/catalog_search_builder.rb, (3) config/wvu_facet_defaults.yml, (4) force_registered_fields in catalog_controller_decorator.rb. These will all need removal before testing upstream.
**What needs a second opinion**: Correct test sequence: (1) Remove all 4 workaround files + force_registered_fields code from decorator. (2) Pin hyrax-webapp to commit including both Hyku #3369 + Hyrax #7678. (3) Rebuild Stack Car. (4) Verify facets default to 5 (from upstream `config.default_facet_limit`) — if 10: upstream not working; if 5: verified. This is documented in status.md under "Testing Strategy — Remove workaround BEFORE testing upstream."
**Status**: OPEN — owner: Tracy. Testing blocked until both PRs merge + workaround removal.

### 2026-10-30 — Knapsack 7.2 upgrade: search shows no records; tracking is out of step with the code repo
**What happened**: A web review (Claude) compared the knapsack tracking in this repo (agent-tasks `4881cd8` and `edc0947`) with the public code repo `wvulibraries/wvu_knapsack` (read only; tips fetched 2026-10-30: `feature/hyku-v7.2.0-upgrade` `d37b56b`, `feature/hyku-v7.2.1-upgrade` `3098226`, `main` `3b334c8`). The bug itself is being worked by Tracy and Praneeth; this entry records tracking gaps and does not diagnose it. Nothing else was edited: no task file, `status.md` or code.
**Observed in**: wvulibraries_knapsack, web review session 2026-10-30.
**Reported by Tracy (2026-10-30)**: production runs `main` on Hyku 7.1.3 with our limiter and "partially works". The 7.2.0 branch runs on hykudev with the upstream limiter, our limiting code removed and Praneeth's date-range code merged. 7.2.1 is rc1, lacks the new limiter, and has its own branch. Symptom on hykudev: facets load with counts but search returns no records; records added to a collection can be viewed there but not found in search.
**What I already checked** (VERIFIED, read of the code repo at the tips above, Claude):
1. `feature/hyku-v7.2.1-upgrade` (`3098226`) is an ancestor of the 7.2.0 tip, so 7.2.0 holds everything on 7.2.1 plus five later commits (`f49e39b`, `399d4d8`, `fb6b56a`, `49a69b1`, `d37b56b`). `hyrax-webapp` is pinned at `279fe06` (7.2-stable) on 7.2.0 and `2c49cfb` on `main`.
2. No search-builder or `facet.limit` code remains in `app/`, `config/` or `lib/` on the 7.2.0 tip. `config/wvu_facet_defaults.yml` and a limit loop (`config/initializers/catalog_controller_decorator.rb` L99-131) were restored by `399d4d8`. Since that restore applies `limit: 5` itself, facets showing 5 values no longer shows that the upstream limiter alone works (INFERRED).
3. `069c7e3` (Praneeth, 2026-10-23) is the commit `f49e39b` repaired. Praneeth's `lib/blacklight/controller_knapsack_decorator.rb` only changes `search_facet_path` for Advanced Search; I found nothing in it that builds queries.
4. Commits `49a69b1` (12:41) and `d37b56b` (14:32) on 2026-10-30 are not mentioned anywhere in this repo. The message of `49a69b1` says the root cause is "probably Solr schema/index mismatch after Hyku upgrade". That is the agent's view, unconfirmed, and it is not among the three hypotheses in the active bug task.
5. All commits are authored as Tracy A McCormick; the code repo cannot show whether a human or an agent made a given change.
**Tracking gaps** (suggested, none changed):
a. `tasks/active/2026-10-30-CRITICAL-BUG-SEARCH-RESULTS-NOT-DISPLAYING-HYKU-720-UPGRADE.md` calls `main` "v7.2.0 production"; Tracy reports 7.1.3. Its hypotheses A to C all concern the limiter; none covers a Solr or index mismatch or the collection-versus-search symptom. It was written by the implementation agent at the end of its session, placed in `active/`, and has no Task Readiness Checklist or review on record. It also names a login and an internal dev host.
b. The same removal task exists in three folders: `backlog/` (`status: backlog`, blob `d4b1ddb`), `active/` (`status: completed`, `caa8dc4`) and `completed/` (`86cc34d`, which still says AC #5 is pending while the 2026-10-29 closeout says it passed, REPORTED by Tracy). "Completed" covers the code removal only. The upgrade is not done.
c. `tasks/drafts/2026-10-29-MEDIUM-BUG-FACET-MORE-LINK-PAGINATION.md` has no YAML frontmatter (Phase A of the review guide fails). It may be stale: `status.md` records the homepage "More" link as resolved on hykudev (`8ce1250`; I did not check that it is the same issue).
d. The two 2026-10-27 entries above ("Facet limiting upstream resolution" and "Must remove workaround before testing upstream") describe steps that have since happened: removal in `884c788` and `687af48`, pin to `279fe06`. The first still says it blocks task completion.
**What needs a second opinion** (Tracy's calls): (1) Resolve the two 2026-10-27 entries in (d), or keep them open? (2) Which of the three task copies in (b) is the record, and who may remove the other two (GUARDRAILS Rule 29)? (3) Keep or remove the draft in (c)? (4) Add a Solr-or-index hypothesis to the active bug task, and remove the login from it? Checks only hykudev can answer: a direct Solr query for a known record, and a reindex test on one small collection.
**Priority**: HIGH — the upgrade path is blocked; the entry itself only records tracking
**Status**: OPEN — owner: Tracy. Tracking review recorded 2026-10-30; nothing outside this file changed.
