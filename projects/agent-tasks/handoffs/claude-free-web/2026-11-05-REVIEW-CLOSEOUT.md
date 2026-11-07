# Closeout handoff: web review session, 2026-11-05

**Agent**: Claude (free web), review role  **Project**: agent-tasks  **Type**: REVIEW
**Written**: 2026-11-05 about 16:25, with origin at `d7e9233` (VERIFIED, fetched). Labels: VERIFIED (a command or a read of origin), REPORTED (Tracy said it, or an agent claimed it), INFERRED (my conclusion).
This continues `2026-10-31-REVIEW-CLOSEOUT.md` (on origin since `0156245`). Its open items 3 to 8 are unchanged unless listed below.

## How to start the next session

1. Read this file, then `projects/agent-tasks/NEEDS_REVIEW.md` from the 2026-11-04 entry onward (about L606).
2. Ask Tracy to run `git pull --ff-only && git status -sb`. Other sessions push often.
3. Before using any text from a plan file, search origin for its marker phrase. Do not trust a file's own status.
4. Draft exact text; Tracy applies, commits and pushes; read it back from origin. Never write to the repo.

## What landed on main today (all VERIFIED by read-back from origin)

| Work | Commit |
|---|---|
| `rules/GUARDRAILS.md`: paragraph "Design intent has one home per project" at the end (MAG-6 addendum) | `dc1ef40` |
| `projects/galaxy-game-ui/SESSION_GUIDANCE.md`: two bullets under `## 5. Critical rules` (run commands in the container `galaxy_game`; the folder/container name collision) | `72baff5` |
| `projects/agent-tasks/NEEDS_REVIEW.md`: addendum to the 2026-11-04 design-intent entry (L636) | `33e7f6f` |
| 2026-10-31 closeout and the 2026-11-01 plan saved to the repo | `0156245` |
| `rules/GUARDRAILS.md` Rule 1: one line for projects whose container is not `web` (E3) | `3f6e844` |
| `REVIEW_AGENT_GUIDE.md` Phase A: accepts the earlier or the current READ FIRST in the dispatch block | `b866687` |
| `TASK_TEMPLATE.md` dispatch block: READ FIRST lists `rules/GUARDRAILS.md`, the project `README.md`, the project `SESSION_GUIDANCE.md` (if any), then the task file | `d7e9233` |

Plan edits status: E1b, E1, E2, E3 applied. **E4 withdrawn**: `DIRECT_SESSION_TEMPLATE.md` already lists the three startup files and Rule 13b says to use it; the task dispatch block was the real gap and `d7e9233` closes it. The 2026-11-01 plan file still says "Nothing below was applied"; that is out of date.

## Decisions from Tracy (REPORTED)

- Wikis for intended behavior: recommended, not required. If a task needs intent the wiki lacks, the agent drafts the wording as unconfirmed, Tracy approves it, and the task points to it.
- Every direct prompt and every task dispatch should name the same three files (GUARDRAILS, project README, project SESSION_GUIDANCE).
- A directed commit (Tracy says commit) complies with Rule 26; ChatGPT now has GitHub read/write when Tracy directs it (REPORTED, 2026-11-05).

## Open items, in order

1. **P1, `REVIEW_AGENT_GUIDE.md`** (blob `b866687` version): one pass for template and content, one complete list (change the Phase A "do not proceed to content assessment" line near L71 and the REVISE "before re-review" line near L79); each PASS quotes its evidence; after REVISE the reviewer re-checks only the listed items; a second failed re-check stops the task and Tracy decides on the direct lane. Placeholder check as a command: `grep -nE '\[(project_name|project|SUBFOLDER|FILENAME|FILL IN)\]' <task file>`; expected: one line at the head of the block (the "One allowed substitution" sentence). Also the 2026-11-03 alternating-review entry and the 2026-11-05 review-timing entry (L664) want a round cap.
2. **`TASK_TEMPLATE.md` L130** ("One allowed substitution"): add `[project_name]` to the fill list and say the placeholders are filled in the file at drafting, with only `<agent-tasks>` substituted at dispatch. This is the rest of P4. Once it lands, the template-completion and dispatch-path 10-31 entries can be marked RESOLVED.
3. **P2**, `PLANNING_AGENT_SESSION_START.md` Task Creation Workflow (about L35, not read this session): the pairing step (web agent drafts with `[FILL IN]`; Qwen fills facts and runs the mechanical checks, pasting raw output; then the independent reviewer).
4. **P3**, slim tasks: partly covered by the MAG-6 paragraph and READ FIRST item 4. Decide whether a "repeat-once" line (a fact told twice goes into `SESSION_GUIDANCE.md`) is still needed and where.
5. **Consolidate the 2026-11-05 planning-family entries** into one short section: file creation (L698), status.md responsibilities (L735), planning scope (L765), prompt lifecycle (L796), plus the handoff-vs-draft-task entry (L637). Then the web-agent GitHub access entry (L827): record ChatGPT's read/write capability with the REPORTED label ("when Tracy directs it").
6. **Rule 26 and Rule 13b**: another Claude session proposed a scope paragraph for Rule 26 (text is in the 2026-11-03 NEEDS_REVIEW entries). Recommended split: one scope sentence in Rule 26 (gates `git commit` and `git push` only; staging allowed) plus a separate "proceed when told" rule, candidate home Rule 13b item 5. Also: a cross-pointer between Rule 13b and the "When a task file is not needed" block in `WEB_AGENT_SESSION_START.md`; the `SIMPLE_HANDOFF_TEMPLATE.md` reference in Rule 13b item 3 and in the `DIRECT_SESSION_TEMPLATE.md` table.
7. **Entry statuses (Tracy's call)**: the 2026-11-04 design-intent entry's own `Status` line still says OPEN (my addendum says RESOLVED except that projects' guidance must name its intent source); the 2026-11-01 host-command entry could move to RESOLVED now that E1, E2 and E3 are applied; keep the 10-31 overhead and Qwen-persistence entries OPEN.
8. **Galaxy Game UI guidance (on hold until the UI reopens)**: the file was restructured on origin (section 2); recheck whether the pointer to `projects/galaxy_game/SESSION_GUIDANCE.md` section 9 and the three auth facts (`Current.user`, `authenticated?`, `role_admin?`) are present; add the design-intent line once the EVE/original blend is decided; `projects/galaxy-game-ui/status.md` still lacks `2e10b1d` and `f07953b`.
9. **Not read**: the Solr credential rotation entry and the other 2026-11-05 entries beyond their headings.

## Open decisions for Tracy

- Sign-in task in `projects/galaxy-game-ui/tasks/backlog/` (rewritten in `64bc6e1`): move to `completed/` as superseded, or keep.
- `64bc6e1` removed 59 lines from `projects/galaxy_game/status.md` and set "Last Updated" back to 2026-10-26: intended?
- The "dispatch sequencing conflict" claim in the sign-in task (the block forbids reading the task before the move; the readiness checklist needs it read first): still not judged. `d7e9233` does not change it; Step 0 still comes first.
- Solr credential rotation confirmation; the four knapsack decisions; whether Qwen applying one bounded edit with no saved task is an approved path (open since 2026-10-24; the direct-edit lane may now cover it).

## What to verify, and why (process notes)

- **Pasted terminal output drops spaces at wrapped lines** (`Thenames`, `conflictswith` appeared in pasted diffs but not in the files). Do not call a missing space a defect from a paste; have Tracy grep the file for the suspect string, or read it back from origin.
- **Empty output is not a pass.** An empty `git diff` meant the edit had not been saved. Ask for a grep that must find the line.
- **Give an edit as a script that asserts it matches once** (`python3` with `assert s.count(old) == 1`), and have Tracy paste the diff before committing.
- **Check the blob on origin before giving an edit**, and again if the pull is refused (two files had moved under Tracy's local edits today). `git stash`, `git pull --ff-only`, `git stash pop` merged cleanly.
- **Expected diff counts**: existing blank lines are reused, so an insert between two blank-separated blocks counts the paragraph only (my expected 3 was really 1 for the NEEDS_REVIEW addendum). A stray blank line shows as an extra insertion.
- **Addenda go directly after their own entry**, not at the end of the file, or they read as updates to the last entry.
- Earlier lessons still apply: fetch with an explicit refspec and trust `git ls-remote`; do not pipe an exit-status check; check that `head` did not cut output; ask which prompt was used; ask what a review request wants before investigating; path-limited commits (`git commit -m ... -- <paths>`), commit and push as separate steps.

## Environment notes

- Clones are session-local: `/home/claude/trmccormick/agent-tasks` (shallow; fetch only) and `/home/claude/trmccormick/galaxy-game-ui` (public, read-only, shallow). Tracy's clone is `/Users/tracymccormick/Documents/git/agent-tasks`; he works from more than one machine, so pull before any local check.
- This file belongs at `projects/agent-tasks/handoffs/claude-free-web/2026-11-05-REVIEW-CLOSEOUT.md`; Tracy commits it.
