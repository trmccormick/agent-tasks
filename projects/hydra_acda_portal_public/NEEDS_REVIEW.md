# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.

---

## Entry template

### [DATE] — [task name or file]
**What happened**: 
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-11-19 — Bulkrax PR #1293: non-hyrax path docs and commits

**What happened**: LaRita Robinson announced PR [#1293](https://github.com/samvera/bulkrax/pull/1293) targeting bulkrax improvements specifically for the non-hyrax usage path (which acdap uses). The PR adds:
- Specs for the non-hyrax path in Bulkrax
- Documentation at https://github.com/samvera/bulkrax/wiki/Using-Bulkrax-without-Hyrax  
- Commits that were only on knapsack's branch to make it easier to update to Bulkrax 10

**What I already checked**:
- Knapsack project (`wvu_knapsack`) already has bulkrax forced to 10.0.1 via `bundler.d/bulkrax.rb` (commit e5d383d)
- Current hyrax-webapp/Gemfile.lock still shows bulkrax 10.0.0 (knapsack override forces 10.0.1 at install time)
- acdap project does NOT have this NEEDS_REVIEW file yet — creating it now
- The PR is NOT merged yet but "coming" per LaRita's message

**What needs a second opinion**: 
1. When PR #1293 merges, should we pin to the post-merge commit instead of 10.0.1?
2. Do acdap's bulkrax settings need review after the non-hyrax path documentation is reviewed?
3. The wiki doc at `Using-Bulkrax-without-Hyrax` may affect our configuration choices

**Status**: OPEN — tracking incoming PR for acdap project planning. Awaiting merge of #1293 before action.
