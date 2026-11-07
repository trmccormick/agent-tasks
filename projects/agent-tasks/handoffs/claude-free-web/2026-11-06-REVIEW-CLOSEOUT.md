# Closeout handoff: web review session, 2026-11-06

**Agent**: Claude (free web), review role  **Project**: agent-tasks  **Type**: REVIEW
**Written**: 2026-11-06 about 18:45, with origin at `5a23192` (VERIFIED, `git ls-remote` at 18:39). Labels: VERIFIED (a command or a read of origin), REPORTED (Tracy said it, or an agent claimed it), INFERRED (my conclusion).
This continues `2026-11-05-REVIEW-CLOSEOUT.md` (untracked in Tracy's clone as of 18:37; commit it with this file if it is still untracked). Its open items 4 to 9 are carried forward below; its items 1 and 2 are done.

## How to start the next session

1. Read this file, then the headings of `projects/agent-tasks/NEEDS_REVIEW.md` from the 2026-11-04 entry onward (`grep -nE '^#{2,3} ' ... | awk -F: '$1>=600'`). Today's addenda moved every line number after L386, so go by heading text, not by the line numbers in older notes.
2. Ask Tracy to run `git pull --ff-only && git status -sb`. Other sessions push often.
3. Before using any text from a plan file, search origin for its marker phrase. Do not trust a file's own status.
4. Draft exact text; Tracy applies, commits and pushes; read it back from origin. Never write to the repo.

## What landed on main today (all VERIFIED by read-back from origin)

| Work | Commit |
|---|---|
| `REVIEW_AGENT_GUIDE.md`: new section "Review Package (Required Before Review)"; one pass with one complete list (template and content together, a template FAIL still blocks GO); the placeholder check as a command with one expected hit; each PASS quotes its evidence; `Package commit` line in the disposition format; REVISE re-checks only the listed items and a second failed re-check stops the task for Tracy; item 6 added to the dispatch-prompt block | `ddadc92` |
| `TASK_TEMPLATE.md` L130: `[project_name]` added to the fill list; placeholders are filled in the file at drafting and only `<agent-tasks>` is substituted at dispatch | `bbce866` |
| `projects/agent-tasks/NEEDS_REVIEW.md`: five addenda dated 2026-11-06, each directly after its own entry (10-31 dispatch-path, 10-31 template completion, 11-03 alternating review, 11-05 review timing, 11-05 review-gate artifacts) | `5a23192` |

Before my first edit, Tracy's pull brought in `f535980` and the commits before it from other sessions (REPORTED from the pull output, not read): three Galaxy Game architecture drafts under `projects/galaxy_game/tasks/drafts/`, 12 lines added to `projects/galaxy_game/status.md`, and `projects/galaxy-game-ui/handoffs/chatgpt-free-web/2026-11-06-SMALL-UI-FOLLOW-UP.md`. `d7e9233` is an ancestor of HEAD (VERIFIED).

## Decisions from Tracy (REPORTED)

- **Review package is required for every reviewer** (option (a) on the 11-05 review-gate entry): the task file, `TASK_TEMPLATE.md` and the guide are attached or pasted with the commit they came from; if one is missing the reviewer asks for it. A web agent that needs data from the project machine asks for it; Qwen gathers it and pastes raw output into the package.
- Basis he gave: a Copilot review on 2026-11-05 could review but had no GitHub access, while Claude, Grok and ChatGPT can review against GitHub.
- He applied and pushed the five addenda, which mark the two 10-31 entries (dispatch-path, template completion) RESOLVED. The original `Status` lines were left as written; each addendum says it is the current status.

## Open items, in order

1. **P2, `PLANNING_AGENT_SESSION_START.md` Task Creation Workflow** (about L35, not read): the pairing step (web agent drafts with `[FILL IN]`; Qwen fills facts and runs the mechanical checks, pasting raw output; then the independent reviewer). Add to the same pass: the review-timing wording from the 11-05 review-timing entry ("the human reviews only after a web agent GO"), with the exception "unless the re-check cap is reached"; and check whether this file, or any handoff template that ships review prompts, carries item 6 of the dispatch fragment (the guide says to add the fragment there as well; not checked).
2. **Guide leftovers** (`REVIEW_AGENT_GUIDE.md`, my edit did not touch them): line 7 says Copilot and Qwen are not used for review, line 101 lists Office 365 Copilot as a review agent, and Copilot reviewed on 2026-11-05 (REPORTED). Tracy's call on the wording; Qwen's role stays "gathers local data for the package". "Last Updated" on line 3 still says 2026-10-06. The guide's GO outcome does not say the human reviews next (11-05 review-timing entry, question 2).
3. **P3, slim tasks**: partly covered by the MAG-6 paragraph and READ FIRST item 4. Decide whether a "repeat-once" line (a fact told twice goes into `SESSION_GUIDANCE.md`) is still needed and where.
4. **Consolidate the 2026-11-05 planning-family entries** into one short section: file creation, status.md responsibilities, planning scope, prompt lifecycle, plus the handoff-vs-draft-task entry. Then the web-agent GitHub access entry: record ChatGPT's read/write capability and the Copilot no-GitHub detail, both with the REPORTED label.
5. **Rule 26 and Rule 13b**: another Claude session proposed a scope paragraph for Rule 26 (text is in the 2026-11-03 NEEDS_REVIEW entries). Recommended split: one scope sentence in Rule 26 (gates `git commit` and `git push` only; staging allowed) plus a separate "proceed when told" rule, candidate home Rule 13b item 5. Also: a cross-pointer between Rule 13b and the "When a task file is not needed" block in `WEB_AGENT_SESSION_START.md`; the `SIMPLE_HANDOFF_TEMPLATE.md` reference in Rule 13b item 3 and in the `DIRECT_SESSION_TEMPLATE.md` table.
6. **Entry statuses (Tracy's call)**: the 2026-11-04 design-intent entry's own `Status` line still says OPEN (my 11-05 addendum says RESOLVED except that projects' guidance must name its intent source); the 2026-11-01 host-command entry could move to RESOLVED; the 11-03 alternating-review entry stays OPEN until questions 1, 2 and 5 are decided; keep the 10-31 overhead and Qwen-persistence entries OPEN. The 10-31 end-to-end entry was not touched today.
7. **Galaxy Game UI guidance (on hold until the UI reopens)**: the file was restructured on origin (section 2); recheck whether the pointer to `projects/galaxy_game/SESSION_GUIDANCE.md` section 9 and the three auth facts (`Current.user`, `authenticated?`, `role_admin?`) are present; add the design-intent line once the EVE/original blend is decided; `projects/galaxy-game-ui/status.md` still lacks `2e10b1d` and `f07953b`.
8. **Not read**: the three "Align draft task structure with required dispatch-interface order" commits (`139ef63`, `269efd1`, `a407fca`; they may bear on the `TASK_TEMPLATE.md` dispatch block), the three 2026-11-05 Galaxy Game drafts, the 2026-11-06 ChatGPT follow-up, the Solr credential rotation entry, and the other 2026-11-05 entries beyond their headings. Which files changed between `d7e9233` and `cc4e09a` was not checked; run `git diff --stat d7e9233 cc4e09a` before relying on the guide, template or rules text.

## Open decisions for Tracy

- Alternating Grok and Claude reviews as the recommended pattern (11-03 entry, questions 1, 2 and 5); blocker, major, minor and cosmetic labels; whether reviewers must be read-only.
- Sign-in task in `projects/galaxy-game-ui/tasks/backlog/` (rewritten in `64bc6e1`): move to `completed/` as superseded, or keep.
- `64bc6e1` removed 59 lines from `projects/galaxy_game/status.md` and set "Last Updated" back to 2026-10-26: intended?
- The "dispatch sequencing conflict" claim in the sign-in task (the block forbids reading the task before the move; the readiness checklist needs it read first): still not judged. Step 0 still comes first.
- Solr credential rotation confirmation; the four knapsack decisions; whether Qwen applying one bounded edit with no saved task is an approved path (open since 2026-10-24; the direct-edit lane may now cover it).

## What to verify, and why (process notes)

- **Pasted terminal output drops or adds spaces at wrapped lines** (`agent-tasksclone`, `PhaseB`, `linked copyit` appeared in pastes but not in the files). Today a grep for the spaced form settled each one before staging. Do not call a spacing problem a defect from a paste.
- **Stage only after a check that must succeed.** The pattern that worked today: one command that greps for the new strings, checks `git diff --quiet HEAD origin/main -- <file>` (file unmoved on origin) and the expected count, and only then runs `git add` and `git diff --cached --stat`. A failing check stages nothing.
- **Give an edit as a script that asserts it matches once** (`python3` with an assert on the old text, or on the line count of the file read), and have Tracy paste `git diff` before staging. The diff stat alone only shows counts.
- **Read each push back from origin** (`git fetch origin main && git ls-remote origin main`, plus a grep count in the file there). The push output alone is REPORTED.
- **Addenda go directly after their own entry**, after its Status line and before any `---`; entries with no `---` get none.
- **Expected diff counts**: existing blank lines are reused, so an addendum adds its paragraph and one blank line.
- Earlier lessons still apply: empty output is not a pass; fetch with an explicit refspec and trust `git ls-remote`; do not pipe an exit-status check; check that `head` did not cut output; ask which prompt was used; ask what a review request wants before investigating; path-limited commits (`git commit -m ... -- <paths>`), commit and push as separate steps.

## Environment notes

- **This session could not read the repo.** WebFetch refused the GitHub and raw URLs ("not in any prior search or fetch result") and a search did not surface the repo, so everything above came from Tracy's pasted output, not from my own reads of origin. `WEB_AGENT_SESSION_START.md` says the web agent reads origin directly; that did not hold here.
- Tracy's clone is `/Users/tracymccormick/Documents/git/agent-tasks`; he works from more than one machine, so pull before any local check.
- This file belongs at `projects/agent-tasks/handoffs/claude-free-web/2026-11-06-REVIEW-CLOSEOUT.md`; Tracy commits it.
