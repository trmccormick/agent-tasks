# Closeout handoff: web review session, 2026-10-21

**Agent**: Claude (free web) — review role  **Project**: agent-tasks  **Type**: REVIEW
**Written**: 2026-10-21 evening, at Tracy's request to pause; she may not continue until 2026-10-22 evening. Evidence labels: VERIFIED (I read or ran it this session), REPORTED (pasted by Tracy or stated in a doc, source named), INFERRED (my conclusion). This file replaces the "next work" list in `2026-10-20-REVIEW-CLOSEOUT.md`; read that one for the decisions and commit history up to `c00a3c2`.

## How to start the next session

1. Read this file, then `projects/agent-tasks/NEEDS_REVIEW.md` (Status lines record the decisions).
2. Ask Tracy to run `git fetch && git status -sb` and say whether anyone pushed since `498df94`. Then clone `trmccormick/agent-tasks` read-only into the scratchpad and re-read what you rely on.
3. Before writing any task file, read: Haiku's drafted Task 3 (`2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md`, named in `handoffs/haiku-copilot/2026-10-20-PLANNING-CLOSEOUT.md`); the Task 2 closeout incident handoff in `projects/galaxy-game-ui/handoffs/` (`implementation-agent/2026-10-21-IMPLEMENTATION-TASK2-CLOSEOUT.md`); `projects/galaxy-game-ui/NEEDS_REVIEW.md` (Dispatch Agent proposal, Grok assessment); the Qwen handoffs there.
4. Then settle the task order and sizes (below) and write the first small task files.

## What landed this session (commits are Tracy's; VERIFIED from her pastes)

| Commit | What |
|---|---|
| `cbfd092` | Closeout handoff for 2026-10-19/20 (moved from the wrong project folder) |
| `8f01943` | Review artifact `summaries/2026-10-21-REVIEW-NEW-WORKFLOW-TASKS.md`: both new backlog tasks REVISE |
| `498df94` | Status line of the handoff-rules entry: handoff ownership decided |

## Decisions and direction from Tracy, 2026-10-21

1. **Who writes handoffs:** review agents (web agents in reviewer mode) and planning agents, so the next session has the information. Implementation agents write no handoff file: they put findings and closeout in the task file and add a short summary at the end of `status.md`; the commit plus the task file must hold what a reviewer needs. A `summaries/` report stays valid for research and other cases but is not required every time. Recorded in `498df94`.
2. **Retention** is still unanswered. Default on the table: stated once in `PLANNING_AGENT_CLOSEOUT.md`, keep the newest handoff per agent and anything an open task or NEEDS_REVIEW entry points to, archive-eligible after 30 days with Tracy's approval, a project `HOUSEKEEPING.md` may override. Fold in the 2026-10-13 "Artifact categorization" entry (session records kept; working drafts archived with approval; permanent history never deleted).
3. **Copilot and Haiku's NEEDS_REVIEW entries were left for Claude to work, not for them to build tasks from.** The two 2026-10-21 backlog tasks are input only; my task files replace them. Ask Tracy before touching those two files (Rule 29: "superseded by" note or move to `archive/`, no delete).
4. **Planner boundary:** a planning agent diagnoses, writes task files and recommends; it does not execute fixes and does not dispatch work. A separate role might manage subtasks later; not designed yet.
5. **Qwen subagents:** a Qwen planner can run subagents (REPORTED: Tracy, 2026-10-21; Copilot's write-up, not checked by me). Copilot recommends treating this as an implementation capability, not a workflow role, and doing stability work before any Dispatch Agent design. I agree. Add one line to the lifecycle task: subagents do not commit or push; only the implementation agent Tracy told to commit may.
6. **Task size:** Qwen has trouble with large task files and often stops and needs a nudge to continue (REPORTED: Tracy). Keep tasks small: one concern, one or two files, exact line, old text and new text, a `grep` check, a numbered step list, one sentence naming the only places to stop, and a fixed last step that prints one completion line. Ask Tracy where the nudges usually happen (after reading, mid-edit, or before closeout) if still unknown. Planned total: about ten small tasks.
7. **Stop wording works in two directions:** Qwen stops when it should continue, and (Task 2 closeout incident, REPORTED by Copilot) implementation agents treat the review gate as a precondition instead of a hard stop. Wording needs one list of the only stopping points, the review gate as a hard stop, and "continue" everywhere else. Copilot ranks this first; I now agree, once I have read Task 3.

## Proposed task order (confirm after reading Task 3)

1. Stop conditions (Haiku's Task 3 may cover it).
2. Closeout ownership and handoff rules (decision 1; `SESSION_CLOSEOUT.md`, `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md`, make the `TASK_TEMPLATE.md` synthesis report optional, retention once decided).
3. Dispositions GO / REVISE / STOP and evidence labels VERIFIED / REPORTED / INFERRED everywhere (`REVIEW_AGENT_WORKFLOW.md`, `REVIEW_REPORT_TEMPLATE.md`, `WEB_AGENT_SESSION_START.md:106`, `REVIEW_AGENT_GUIDE.md`, governance plan).
4. TASK_TEMPLATE contradictions (10-18 HIGH entry; starting point: the unsent draft in `tasks/drafts/`).
5. Lifecycle rule and Rule 26 (`rules/TASK_FILE_LIFECYCLE_VALIDATION.md`, `GUARDRAILS.md:242`, the subagent line).
6. Guides: planner boundary text in `docs/PLANNER_ROLE_BOUNDARIES.md` plus a pointer in `PLANNING_AGENT_SESSION_START.md`; remove "Timothy" and model names; archive root `PLANNER_ROLE_BOUNDARIES.md`, both `MODEL_SELECTION_GUIDE.md` and `rules/TRIAGE_GUIDE.md`; `REVIEW_AGENT_GUIDE.md:121-139` config block; `README.md:660`; the two `AGENT_NOTES.md` entries.
7. Retire `SIMPLE_HANDOFF_TEMPLATE.md` and fix its live references (after task 6 for `README.md`).
Tasks sharing a file must run in order. Renaming old `-SESSION.md` files stays paused. Executor default: Qwen.

## Not done yet

- Task files: none written. Review of the two 2026-10-21 backlog tasks is done (REVISE); they are not to be dispatched.
- Evidence to read in galaxy-game-ui (not yet read; only the file list is VERIFIED): `DASHBOARD-COMMAND-CENTER` is in both `tasks/active/` and `tasks/backlog/` at 717 lines (possible stray copy, INFERRED); `tasks/backlog/gemini-code-1792533726580.md` looks like a scratch file; implementation agents wrote handoffs in two folders there, one named `qwen(implementation)`. Do not touch another project's files without Tracy's say-so; ask whether findings go into the agent-tasks NEEDS_REVIEW.
- A NEEDS_REVIEW entry for the dispatch redesign (deferred) with Copilot's three-phase proposal, after the stability items.
- `status.md` entry for 2026-10-19 to 2026-10-21 (append only; correct the 2026-10-18 entry's stale "Not committed" bullet and its "2026-10-14 and 2026-10-15 entries" wording without editing it).
- Knapsack: M4 entry amendment (checks F, G answered; I partly; A to D need a running stack; H is Tracy's), closeout-overlap entry (re-check references to `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md`), `SESSION_GUIDANCE.md` edits (`:92`, `:251`, `:276`, `:87`, `:44`, `:147`; Hyku flags `:36`, `:59`, `:75`, `:77`, `:78`).
- Questions for Tracy: who approved removing the stray copies in the lifecycle incident; repeated sections and the old "Last Updated" date in review docs; whether to remove the stray `~/agent-out/2026-10-20-web-review/NEEDS_REVIEW.md`; whether leftover `-SESSION.md` wording (`SESSION_CLOSEOUT.md:22`, `:118`, `SYSTEM_INTENT.md`, `README.md:737`) stays paused; Haiku's open questions (dispatch Task 3, apply the review gate to other projects, model-budget phase).

## Corrections to things I said earlier

- I called the commit for the handoff Status line "decision 3"; it is decision 4 in my numbering (the handoff-required question).
- I ranked dispositions and evidence labels as the first task; Copilot's write-up and Tracy's Qwen observations argue for stop conditions first.

## Environment notes

Tracy works from several laptops. Nobody else is working on the repo tonight (REPORTED: Tracy, 2026-10-21 22:41). Deliver whole files with `mv`, never leave copies in Downloads, one request at a time, explicit-path staging, path-limited commits, `git status -sb` after each push. Commit messages end with the Co-Authored-By and Claude-Session lines.
