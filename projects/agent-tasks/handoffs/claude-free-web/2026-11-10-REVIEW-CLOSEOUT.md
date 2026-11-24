# Review Closeout — 2026-11-10 (claude-free-web), agent-tasks workflow tuning

Session: web review, Tracy (he/him). Claude drafted exact text; Tracy applied, committed (path-limited) and pushed; Claude read back from origin.
Labels: VERIFIED (read from origin), REPORTED (stated by Tracy).

## Landed (VERIFIED on origin main)
- cb35424: notes/AGENT_NOTES.md; Claude (web) and ChatGPT (web) rows now state repo read/write capability.
- d19830e: README.md:693; status.md is "a current-state snapshot", not "the full historical log".

## Design approved by Tracy (REPORTED), for the 2026-11-10 NEEDS_REVIEW entry
Four files, smallest edits, nothing new at root. Remaining, in this order:
1. SESSION_CLOSEOUT.md (drafted and shown to Tracy; NOT confirmed applied):
   a. line 22: replace the REVIEW-[TOPIC]-SESSION.md naming with the same <type> pattern (e.g. 2026-11-07-REVIEW-CLOSEOUT.md).
   b. after line 27: add "A handoff is a record, not a command to end the session" plus the same-day rule (same agent/project/day: update the existing file with a dated Update section; new day: new file; distinct workstream: own file).
2. rules/GUARDRAILS.md (sensitive, last):
   a. Rule 0: scope line saying it describes the VS Code tool set (local/Copilot agents); web agents use what their session provides; authority rules apply regardless.
   b. Rule 26: a write through a GitHub integration counts as a commit; authorized by Tracy's message naming files and action; merges, branch changes and drafts/ -> backlog/ -> active/ moves still need separate explicit confirmation.
Defaults Tracy accepted (DEFAULT, not independently confirmed): drafts/ -> backlog/ stays human-only; same-day handoffs update the existing file.

## Not done
- status.md bloat (agent-tasks header last updated 2026-10-21; galaxy_game ~1,047 lines, wvulibraries_knapsack ~1,087): separate curation pass per PLANNING_AGENT_CLOSEOUT.md line 56, not a rule change.
- The 2026-11-10 NEEDS_REVIEW entry is still OPEN until items 1 and 2 land; then mark RESOLVED with the commit hashes.
- NEEDS_REVIEW.md is ~985 lines against its own "keep small" header; triage not started.
- WEB_AGENT_SESSION_START.md still says the repo is readable from its GitHub URL; the working route is add_repo plus a shallow clone (plain URLs and gh are blocked). One-file fix, not drafted.

## Process notes
- Terminal paste can drop spaces at wrap points; prove with a grep, don't call it a defect.
- Use git --no-pager diff --check; a trailing-whitespace hit in README was real and fixed with a one-line sed.