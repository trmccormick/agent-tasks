# Housekeeping tracker (galaxy_game + agent-tasks)

**Status: DRAFT, proposed 2026-10-18, not yet approved by Tracy.** Nothing here authorizes deleting or moving anything. Save as `projects/galaxy_game/HOUSEKEEPING.md`.

Purpose: agent sessions leave handoffs, logs and scratch files behind, and nobody can tell later what is still needed. This file breaks the cleanup into small items that can be done a few at a time, one slice per session, so it never becomes a big-bang cleanup.

Counts below are VERIFIED from origin on 2026-10-18 unless marked REPORTED. Re-check a count before acting on it.

## Standing rules

1. **Tracked files are recoverable.** Anything committed stays in git history, and Tracy also has Time Machine. Removing or archiving a tracked file loses nothing. **Untracked files are the only ones at risk:** copy them out first (for example to `~/agent-out/`), then remove.
2. **When unsure, archive, do not delete.** Use the existing `projects/galaxy_game/archive/` (confirm its conventions in item H2 before adding anything).
3. **Read-only inventory first, then one proposal table, then Tracy approves.** The planning agent proposes; it never deletes on its own. Same table format as `PLANNING_AGENT_CLOSEOUT.md`.
4. **One slice per session:** at most one item below, or at most 10 files, whichever is smaller. Record it in the Session log. Stop there.
5. **Where scratch goes:** scripts and logs inside a repo go only in `temp-files/` (gitignored, see H1). Output files Tracy must upload go only in `~/agent-out/`, never the home folder root, and are cleared once the work they served is committed.
6. **Keep:** the newest handoff per agent, and anything an open task references. Everything else is archive-eligible after 30 days.

## Items

Status: `todo`, `in progress`, `done`. Each item should become its own small task file in `tasks/drafts/` when it is picked up.

| ID | Item | Evidence | First step (read-only unless noted) | Status |
|---|---|---|---|---|
| H1 | Add `temp-files/` to `.gitignore` in agent-tasks and galaxyGame | `temp-files/` shows as untracked in agent-tasks, not ignored | One-line change per repo; Tracy approves | todo |
| H2 | Confirm what `projects/galaxy_game/archive/`, `status_archive/` and `deprecated/` are for | all three exist at the project root | Read their contents and any README; report conventions | todo |
| H3 | Write the retention rule and add a "Housekeeping slice" step (new section 7) to `PLANNING_AGENT_CLOSEOUT.md`: read this file, pick the next item, propose, wait, record in the Session log | closeout doc is 56 lines, sections 0 to 6 | Draft the section text; ChatGPT and Tracy review before it is committed | todo |
| H4 | Add a "Files I created this session and how to dispose of them" line to `SESSION_CLOSEOUT.md`, so the agent that made scratch says what is safe to remove | agents currently leave this unstated | Draft one short section; review before commit | todo |
| H5 | galaxyGame root: six scratch files added in `d7894ca3` | `2026-10-10-status.md` (terminal paste), two Qwen chat logs (85 KB), three `tools/diag_*` scripts | Tracy's suggestions: delete the paste; copy the two logs to agent-tasks handoffs then `git rm` in galaxyGame; keep the diag scripts until C3b is closed | todo |
| H6 | Handoff duplicates | 42 file names exist in two places inside `galaxy_game/handoffs` (351 KB of extra copies; sizes matched in the 10 sampled, contents NOT compared) | Qwen compares contents of all 42 pairs and reports which are byte-identical | todo |
| H7 | Archive old handoffs, one month per session | 147 of 168 dated handoffs are older than 30 days (Jun 49, Jul 35, Aug 34, Sep 41, Oct 9) | Propose one month's table; keep newest per agent and anything an open task references | todo |
| H8 | Archive summaries | 246 files in `galaxy_game/summaries` (Jul 90, Aug 60, Sep 40, Oct 37) | Same rule as H7, keeping anything an open or recent task references | todo |
| H9 | galaxyGame root inventory | 74 root files: 13 `test_*.rb`, 3 `debug_*.rb`, committed `__pycache__`, logs and diffs (`full_run_order.txt` 863 KB, `diff_files.txt` and `filtered_diff.txt` 114 KB each, `fix.log`, `grinder_startup.log`, `monitor_gitlog.txt`) | Qwen classifies each: referenced by anything, code, doc or scratch. Then batches of at most 10 | todo |
| H10 | Normalize handoff folder names to simple slugs (for example `claude-web`, `qwen-planning`, `perplexity`) and fix docs that mention the old names | current names include spaces and parentheses (`claude(free web)`, `claude(hakiu copilot)`, `grok` and `grok(free web)`, `various`); the planning docs say `claude-web` | Do after H6 and H7 so fewer files move | todo |
| H11 | agent-tasks root: three proposal-style docs | `PHASE_1_REVIEW_EXTRACT.md`, `TASK_FILE_AS_EXECUTION_CONTRACT_PROPOSAL.md`, `TASK_FILE_AUTHORITY_AND_MULTI_AGENT_ROUTING_PROPOSAL.md` | Check whether anything references them, then propose `docs/` or archive | todo |
| H12 | Home folder (Tracy's machine only, not in git) | 15 files from this workflow were moved to `~/agent-out/2026-10-archive/` on 2026-10-18; about 12 older files from Oct 6 to 11 remain in `~` (REPORTED) | Tracy reviews the older files and decides | todo |

## How the planning agent uses this (proposed wording for H3)

At the end of the closeout proposal, add one "Housekeeping slice" row group:
1. Read this file. Pick the first `todo` item that has no open dependency.
2. Do its read-only first step. Propose at most 10 files in the normal proposal table.
3. Wait for Tracy's approval. Act only on approved rows.
4. Record the slice in the Session log below and set the item to `in progress` or `done`.

## Session log

| Date | Item | What was done | Commit |
|---|---|---|---|
| 2026-10-18 | (tracker created) | Inventory of both repos and the home folder; 15 workflow output files moved from `~` to `~/agent-out/2026-10-archive/` | |
