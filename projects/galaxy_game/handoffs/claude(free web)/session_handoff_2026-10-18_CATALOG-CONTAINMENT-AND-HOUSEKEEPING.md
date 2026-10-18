# Session handoff 2026-10-18 — catalog_image containment tasks and housekeeping plan

Agent: Claude (web). Replaces the stale 2026-10-17 draft handoff, which was never committed.
Paste or attach at the start of the next review session. **Nothing here authorizes dispatch, commit or push.** "Commit" from Tracy is the approval.

Labels: VERIFIED = a command or a read of origin this session. REPORTED = Tracy, his pasted terminal output, or an agent, not re-checked.

## State on origin

agent-tasks `c4ddb4e` (VERIFIED by fetch). galaxyGame last checked at `acc2678`: docs-only commits since `d7894ca3`. The controller is untouched; the `start_with?` guard is still at line 45 of `catalog_controller.rb`.

The traversal finding is recorded in C3b (`f79cdd0`). The fix is split into three small drafts in `projects/galaxy_game/tasks/drafts/` (Dispatch paths corrected in `7e27328`):

- **A** core real-path containment. ChatGPT reviewed it; no A-specific changes needed.
- **B** symlink cases. ChatGPT review merged and pushed (`771e84e`, md5 `8d97db96d1abddb2a458913219193e97`).
- **C** HTTP verification, report only. ChatGPT found the right issues but returned a description, not text. **Waiting:** send him "paste the full corrected sections as ONE code block in chat", then merge and verify against origin the way B was done.
- The parent `2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md` stays in `tasks/drafts/` until the children are settled, then moves to `backlog/superseded/`.

Template-level findings (VERIFIED, `TASK_TEMPLATE.md`): the synthesis section says to post in chat (lines 241, 280) but also says never paste it in chat (123, 243); the synthesis timing conflicts with Step 0 going first (241 vs 102/107); the `find` command uses `agent-tasks/projects/...` while `git mv` is repo-relative (115); and the body lists the checklist before the Dispatch Interface while the header comments say Dispatch goes first (A/B/C follow the comments). Fix once at template level, then re-substitute the blocks in A/B/C. Do not edit the verbatim blocks in individual tasks.

Housekeeping tracker committed: `projects/galaxy_game/HOUSEKEEPING.md` (`c4ddb4e`), twelve small items H1 to H12, one slice per session. First suggested slice: H1, add `temp-files/` to `.gitignore` in both repos.

## Open decisions for Tracy

- Approve A, B, C for dispatch, and which first (C is read-only and could run first for severity evidence).
- Whether `tasks/drafts/` is the permanent home for drafts (it contradicts the template's `backlog/<subfolder>/` Step 0 shape).
- H5 scratch from `d7894ca3`: delete the terminal paste; copy the two Qwen logs to agent-tasks then `git rm` in galaxyGame; keep the three `tools/diag_*` scripts until C3b closes. Qwen's inventory said one log was "untracked"; it is tracked on origin.
- The older C2 copy and anything under `backlog/asset-ui/` are ChatGPT's lane. Leave them.

## Temporary files to remove

Rule from now on: every handoff ends with this list. Each entry: path, who created it, when it is safe to remove.

| Path | Created by | Safe to remove |
|---|---|---|
| `~/agent-out/2026-10-archive/` (15 files: `at-state.md`, `status-wt-diff.md`, `status.md.stale-worktree-copy`, `status-restore.md`, `pathcheck.md`, `c3b-state.md`, `c3b-note-diff.md`, `draft-edit-diff.md`, `draft-review-qwen.md`, `draft-edit-diff-2.md`, `draft-commit2.md`, `c-fix.md`, `split-edit-diff.md`, `split-review-qwen.md`, `scratch-inventory.md`) | Claude-drafted commands/prompts, Oct 13 to 17 | Whenever Tracy wants. All the work they served is committed. REPORTED: Tracy ran the move to this folder; if they still appear in `~`, move them with `mv -n`. |
| Downloads copies of the parent draft, `task-B-sections.md`, `split-report.md`, the stale handoff, `HOUSEKEEPING.md` | Claude `cp` commands and ChatGPT outputs | Already moved to the Trash by Tracy (REPORTED). |
| Older files in `~` dated Oct 6 to 11 (`closeout-review.md`, `impl-*`, `midpoint-check.txt`, `task-copies.md`, `stash-check.txt`, others) | earlier sessions, not this one | Tracy decides (tracker item H12). |
| agent-tasks untracked: `handoffs/claude(free web)/HANDOFF-2026-10-11-...md`, `summaries/2026-10-06-SPECS-ORIGINAL-ON-GUARDED.txt` (1.2 MB), `summaries/2026-10-11-C3B-TASK-VERIFICATION-REPORT.md`, `summaries/2026-10-13-A2-VERIFICATION-B1-B2-COMPLETION-CLAIMS.md`, `reconciliation-summary-2026-10-11.md` (repo root), `temp-files/` | other sessions | Not ours. Leave until the planning agent proposes a sweep. Copy untracked files elsewhere before removing; tracked files are always recoverable from git (plus Time Machine). |
| This session's sandbox files | Claude | Nothing persists. |

## How to work with Tracy (confirmed this session)

- One prompt to an agent at a time; wait for results. Agents write output files to `~/agent-out/`, never the home root, for Tracy to upload.
- Qwen handles short prompts that point to a file in the repo; keep prompts short. Verify his diffs: he twice overstated his own work.
- ChatGPT (free tier) takes pasted text, not whole files, within usage caps. Paste only a task's specific sections. Ask for the result as one code block in chat.
- Files go into repos with `mv`, not `cp`. Several small tasks beat one large one.
- Commit route is flexible: Claude gives the command, Tracy runs it, or Qwen does. Say when something is ready to commit.
- Check origin (`git fetch`, recent commits) before analyzing any local snapshot or upload.

## Mistakes to not repeat

- Read Rule 26 too strictly; it requires explicit human approval, not "human types the command".
- Gave `cp` commands and home-folder outputs without a cleanup step, adding clutter while planning the cleanup.
- Suggested folding the controller fix into the catalog-index task, whose scope excludes the controller.
- Gave a commit command before it was asked, and guessed where files were.
