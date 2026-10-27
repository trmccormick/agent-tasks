# Closeout handoff: web review session, 2026-10-24 to 2026-10-27

**Agent**: Claude (free web), review role  **Project**: agent-tasks  **Type**: REVIEW
**Written**: 2026-10-27 morning, at `cdcf131` on origin (VERIFIED, fetched). Labels: VERIFIED (a command or file read), REPORTED (Tracy said it, or an agent claimed it), INFERRED (my conclusion).
This continues `2026-10-24-REVIEW-CLOSEOUT.md`, which does not cover anything below.

## How to start the next session

1. Read this file, then `projects/agent-tasks/NEEDS_REVIEW.md` (newest: 2026-10-25 pairing decision) and `projects/galaxy_game/NEEDS_REVIEW.md` (newest: 2026-10-26 paused portal draft).
2. Ask Tracy to run `git pull --rebase && git status -sb`. Other sessions push often (portal wiki work, handoffs). Never rebase with a staged index; commit or ask first.
3. Add the galaxygame repo read-only with `add_repo` if you need it (public).
4. Continue at "Open items" below.

## What landed on main (commits are Tracy's; hashes as on origin)

| Work | Commits |
|---|---|
| 1a GUARDRAILS Rule 12 wording | `c9e010e`, closeout `eb03608` |
| 1b PLANNING_AGENT_SESSION_START wording | `1f2f43d`, closeout `43e8fd2` |
| 1c TASK_TEMPLATE backlog path and stale copies | `2cd19d2`, closeout `74d9953` |
| Review-gate authorization wording (task 2) | draft `05dbd3e`, revised `3e7e3b1`, approved `4c378db`, dispatch fix `8e0dccc`, edit `3cba0fe`, to active `7026a2b`, closeout `cdcf131` |
| WEB_AGENT_SESSION_START L54 and L60 (drafts, not backlog) | `3bc35a8` |
| Pairing-decision entry (agent-tasks NEEDS_REVIEW) | `3938d9e` |
| Paused portal-draft entry (galaxy_game NEEDS_REVIEW) | `c48b057` |
| Earlier: model guide `18b23e5`, incident review `fcb99af`, 2026-10-24 closeout `1caf1e9` | |

`TASK_TEMPLATE.md` is now blob `948c27c77b78a9133e8cf42dd5c4b53f4dd803a5` (VERIFIED).

## Decisions from Tracy (REPORTED)

- **Pairing flow.** A web agent with long context (Perplexity, Gemini; ChatGPT for design) drafts, Qwen fills in what needs the repo, then a repo-capable reviewer (Claude or Grok) reads it from git. REVISE goes back to the drafter and Qwen. Tracy approves. Perplexity has worked well as the mid-level drafter. Model choices rest on context length and what she has seen in testing, not ratings.
- **Approval prompt, Option B.** Confirming a command in an agent's own approval prompt counts as authorization only after a reviewer GO is on record. This replaces the 2026-10-20 decision (a click always counts). It is now in `TASK_TEMPLATE.md`. Commit and push are separate authorizations.
- **Portal and wiki work belongs to ChatGPT** (with Gemini for brainstorming). It is design only; no code. Claude and Grok review once it is committed. The portal draft review (REVISE, small) is paused at low priority and recorded in the galaxy_game NEEDS_REVIEW.
- **Work vs personal.** Work: `wvu-moonshot`, `samvera_hyku`, `samvera_hyrax`, `ccdt` and the five `wvulibraries_*` projects, weekdays about 8:30 to 5. Personal: `galaxy_game`, `galaxy-game-ui`, `galaxy-game-ui-first-attempt`, `eve_dashboard`, off hours. `agent-tasks` is shared. Some wvulibraries repos are public.
- **Tracy has final say.** Open conflicts in design notes are recorded in the wiki for later resolution.

## Open items, in order

1. **Reconcile two backlog tasks with the new gate wording.** `2026-10-19-...STOP-CONDITION-ENHANCEMENT` (Step 4.5, L305, L310, L349) and `2026-10-21-...SESSION-CLOSEOUT-REVIEW-GATE-ENFORCEMENT` (edits SESSION_CLOSEOUT.md and IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md) still use "commit/push/task completion" as one authorization. Read both before dispatching either.
2. **Preplanning-brief section** for `WEB_AGENT_SESSION_START.md`: Qwen extracts verified old text first, then the drafter writes the new wording from it. One concern per file.
3. **`MODEL_SELECTION_GUIDE.md` roles** (add a drafter role; a reviewer needs a passed three-line spot-check) and a `[FILL IN]` check in `REVIEW_AGENT_GUIDE.md`. Then `PLANNING_AGENT_SESSION_START.md` L171-185 and the deprecated table (1b has landed, so this is unblocked).
4. **Portfolio planner.** A cross-project file, two lanes plus shared, one focus per project, parked items with reasons, built by Qwen from the 14 `status.md` files and decided by Tracy. Read `PLANNER_ROLE_BOUNDARIES.md` and `PLANNING_AGENT_WORKFLOW.md` first. Public repo: no secrets, no internal-only details.
5. **Tracy's checks (not mine).** `wvulibraries_authentication/status.md` and `wvulibraries_knapsack/status.md` contain the words password, token, credential (counts only; I did not read the lines). Check for real values. In the game repo, commit `65b0fc6` put research and prompt files at the repo root and added `galaxy_game/app/data/operational/natural_wormhole_anchor_mk1h_data.json`, which no `.rb` file I searched loads.
6. **Still unaddressed from before:** a no-task prompt header (what was "the basic header" in the ChatGPT incident?), the Rule 13a vs dispatch block contradiction, replacing the README example with a pointer, SIMPLE_HANDOFF_TEMPLATE retirement, TASK_TEMPLATE.md L150-152, GUARDRAILS.md L269.

## What to verify, and why (process notes)

- **Compute the expected file hash before the gate.** Apply the edits to a copy at the current base and run `git hash-object`; Tracy compares after Qwen finishes. This caught real errors twice: a typo ("proceeding") that Qwen's pasted diff hid, and a correct result confirmed in one step.
- **Qwen's own reports are unreliable.** Seen this week: a diff pasted that did not match the file; "staged" claimed when only the move was staged; a completion report saying a synthesis was saved when none existed; two stalls after announcing a step. Use a short nudge with the exact command, then a fresh session. For mechanical edits, a short script with `assert count == 1` and a hash check has been more reliable than Qwen.
- **Diff the dispatch block against a known-good one.** Qwen's copy for the review-gate task said drafts to backlog; the real contract is backlog to active. I missed it until the dispatch step.
- **Commits:** path-limited (`git commit -m ... -- <paths>`), both old and new paths for a rename, never `git add .` or `-A`. A rebase will not start with a staged index; a merge would sweep the staged work in. Do not stash.
- **My mistakes this stretch, so they are not repeated:** I briefed Gemini with the opposite of the 2026-10-20 decision; I mislabeled a base hash; I gave two wrong expected counts; I "corrected" a citation that was already right (L55 does mention `[FILL IN]`); I sent a command with a known-bad path. Check each against the repo before sending.
- **Say which window a block goes in** (Qwen chat or terminal). Tracy ran a Qwen message in zsh once.
- **Gemini** returns a file from its file tool, and nested code fences break in chat. Ask for the file inline, wrapped in a four-backtick fence.
- **First Gemini plus Qwen draft took three review rounds**, mostly from my wrong brief and missing repo facts. The reverse order (Qwen extracts verified text first, then the drafter writes) is untested. Log rounds per task as dated evidence in the model guide.

## Environment notes

- My clones are session-local: `/home/claude/trmccormick/agent-tasks` and `/home/claude/trmccormick/galaxygame`. Tracy's clone is `/Users/tracymccormick/Documents/git/agent-tasks`.
- The `agent-tasks` repo is public (anonymous `git ls-remote` worked). The `galaxygame` repo was cloned read-only, shallow.
- Tracy reported the session at 90% of its limit several times (REPORTED; I cannot see the meter). A session pause does not end it; she resumes later in the same session.
