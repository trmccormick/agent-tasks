# Closeout handoff: web review session, 2026-10-23 to 2026-10-24

**Agent**: Claude (free web) — review role  **Project**: agent-tasks  **Type**: REVIEW
**Written**: 2026-10-24 afternoon. Tracy reported the session at 90% of its limit on the evening of 2026-10-23 (REPORTED, I cannot see the meter); my reply to that message did not reach her, and this handoff is the catch-up. Evidence labels: VERIFIED (I read or ran it this session), REPORTED (pasted by Tracy or stated in a doc, source named), INFERRED (my conclusion). This file adds to `2026-10-22-REVIEW-CLOSEOUT.md` (read it, then `2026-10-21-REVIEW-CLOSEOUT.md`) and updates its task order.

## How to start the next session

1. Read this file, then `2026-10-22-REVIEW-CLOSEOUT.md`, then `projects/agent-tasks/NEEDS_REVIEW.md` (newest entries: 2026-10-22 draft gate, 2026-10-22 Docker rule).
2. Ask Tracy to run `git pull --ff-only && git status -sb`. On 2026-10-24 15:3x the remote was at `0e10716`, eight commits past `1be58fc` (VERIFIED: fetched into my clone): a galaxy-game-ui planning session moved implementation tasks between `drafts/` and `active/` and back (`d1888d9`, `9161b65`), archived the EVE CSS draft, edited the landing and seeds drafts, and added four new drafts (fixture loader, CSS foundation, layout shell, dashboard overview). None of those commits touch the agent-tasks files named here (VERIFIED by file list). The galaxy-game-ui rows in the review table below may be out of date; re-read before relying on them. Then say whether anyone pushed since `0e10716`. Then clone `trmccormick/agent-tasks` read-only into the scratchpad (shallow, `GIT_LFS_SKIP_SMUDGE=1`) and re-read what you rely on.
3. Root `PLANNING_AGENT_WORKFLOW.md` and `TASK_TEMPLATE.md` were read in full this session (VERIFIED). The workflow file is 59 lines and only the stale/overlapping task audit procedure, so nothing in it needs changing for the lifecycle tasks. `TASK_TEMPLATE.md` is 578 lines (at `ec2e23d`). Re-read `TASK_TEMPLATE.md` after 1c is applied, since line numbers will move.
4. Continue at "Where things stand" below.

## What landed (commits are Tracy's; VERIFIED from her pastes)

| Commit | What |
|---|---|
| `861585c` | Draft task 1a: GUARDRAILS Rule 12 lifecycle wording (5 single-line edits) |
| `6bfa574` | Approved 1a into `backlog/` (flat; my first command staged a stale path, Tracy amended her own unpushed commit; final content VERIFIED: `status: backlog`, 3 insertions, 7 deletions) |
| `c5874c6` | Draft task 1b: `PLANNING_AGENT_SESSION_START.md` draft-commit wording (6 single-line edits) |
| `1be58fc` | Approved 1b into `backlog/` (1 insertion, 5 deletions) |
| `7625086` | Draft task 1c: `TASK_TEMPLATE.md` backlog path, flat-backlog substitution, stale-copy wording (3 single-line edits). In `drafts/`, `status: draft`, not yet reviewed |

Neither 1a nor 1b has been dispatched to Qwen as far as I know (INFERRED; Tracy has not said). Nothing is edited in `rules/GUARDRAILS.md`, `PLANNING_AGENT_SESSION_START.md` or `TASK_TEMPLATE.md` yet; the tasks describe the edits.

## Where things stand

- **1a** (`projects/agent-tasks/tasks/backlog/2026-10-23-MEDIUM-DOCUMENTATION-GUARDRAILS-RULE-12-LIFECYCLE-WORDING.md`): approved, in `backlog/`. Independent review: GO by Grok on `861585c`, no changes. I checked Grok's claims against the repo (dispatch block diffed against the template, old strings counted, dry run of all edits and checks on a copy).
- **1b** (`...backlog/2026-10-23-MEDIUM-DOCUMENTATION-PLANNING-SESSION-START-DRAFT-COMMIT-WORDING.md`): approved, in `backlog/`. Grok GO on `c5874c6`; same checks by me.
- **1c** (`...drafts/2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING.md`): pushed, my self-check only (dry run: each old string once, five greps pass, 3 in and 3 out). **Needs the Grok review prompt (template below), then approval.** Approval for a flat backlog: `git mv` to `backlog/`, `status: draft` to `status: backlog`, delete the "DRAFT STATUS NOTE" section; the dispatch block already omits `[SUBFOLDER]/`.
- Grok is available as a reviewer: give it raw GitHub URLs pinned to a commit and the review prompt; it replied in the required format. Its file access is unverified (REPORTED by Tracy only), so check what it quotes.

## Decisions from Tracy, 2026-10-23

1. `agent-tasks` backlog stays flat (no phase subfolders); galaxy_game and other multi-phase projects keep subfolders. The dispatch block's `[SUBFOLDER]/` segment is omitted for a flat backlog; 1c puts that into `TASK_TEMPLATE.md:130`.
2. Grok is the independent reviewer for these small tasks (I wrote them, so I do not review them).

## Review of changes after `3cfc3ca` (read at `ec2e23d`)

| Item | Disposition | Evidence |
|---|---|---|
| Docker rule entry (`90eccc0`, 27 lines) | GO as an entry; placement still open | VERIFIED: well formed with labels. It embeds a machine path (`/Users/tam0013/...`) and uses `docker exec galaxy_game`, while `TASK_TEMPLATE.md:449` uses `docker exec -it web`. INFERRED: put the rule in galaxy-game-ui project docs, not a numbered GUARDRAILS rule, because only that project uses Docker. |
| `SESSION_CLOSEOUT.md` rules 9/10 (`92e3a74`) | REVISE, minor | VERIFIED: new rule 9 overlaps rule 8 directly above it ("do not stage the file as it stands"). Fold into one rule. |
| ChatGPT handoff (`5cef83f`) | GO as a record | VERIFIED: claims nothing unverified. Its closeout disposition is not GO / REVISE / STOP and it has no evidence labels (task 4 scope). |
| Rename to `galaxy-game-ui-first-attempt` (48 files, 100% similarity) | GO on content, REVISE on labeling | VERIFIED: README and `status.md` in the new folder have no archived banner, and the `status.md` header still says "Phase 1 is HIGHEST PRIORITY". Old `galaxy-game-ui/handoffs/...` paths in `2026-10-21-REVIEW-CLOSEOUT.md` and `handoffs/planning/2026-10-21-PLANNING-CLOSEOUT.md` now point at moved files. |
| Stray `galaxy-game-ui/tasks/drafts/NEEDS_REVIEW.md` | REVISE | VERIFIED: a 151-line reuse analysis, not a task; Rule 12 says `drafts/` holds draft tasks only. It says "Delete or archive" for one task, which conflicts with Rule 29. No evidence labels. Ask Tracy where it should live. |
| Three new galaxy-game-ui drafts (2026-10-23, not on the original list) | REVISE | VERIFIED: all have `status: drafts` (template value is `draft`); none has a review-gate section; none contains `git add .`; the dashboard CSS draft is 722 lines. They are the draft-review checklist cases from the 10-22 handoff. |

## Corrections to the 2026-10-22 handoff

- `[handoff]` is at `PLANNING_AGENT_SESSION_START.md:43`, not in Rule 12.
- `{YYYY-MM}` is at `TASK_TEMPLATE.md:17`, not line 10.
- "Rule 7" is item 7 of the session rules in `PLANNING_AGENT_SESSION_START.md:15`, not GUARDRAILS Rule 7.
- So the lifecycle wording task split into three one-file tasks (1a, 1b, 1c) instead of one.

## Revised task order

1. Lifecycle wording: **1a, 1b approved; 1c needs review and approval.** Still dispatchable by Tracy to Qwen in any order (separate files).
2. Stop wording: name the four meanings (`status.md` bullet, task "Stop Conditions — escalate", Step 4.5 review gate, STOP disposition); gate as a hard stop with a copy-ready authorization block, "commit" and "push" separate; draft-review checklist lines (dispatch block contains the review-gate step; no `git add .` / `-A`); fold in Haiku's Task 3 `status.md` examples. **Decide first:** the 2026-10-20 decision says confirming a command in an agent's own approval prompt counts as approval; the new block must say whether that still holds. Runs after 1c (same file, `TASK_TEMPLATE.md`).
3. Closeout ownership and handoff rules, including the `SESSION_CLOSEOUT.md` rules 8/9 overlap above; retention default still to confirm.
4. Dispositions GO / REVISE / STOP and labels VERIFIED / REPORTED / INFERRED everywhere, including a draft-review variant of `REVIEW_REPORT_TEMPLATE.md` and the `REVIEW_AGENT_GUIDE.md` draft-review format.
5. TASK_TEMPLATE contradictions (10-18 HIGH draft in `tasks/drafts/`); also `TASK_TEMPLATE.md:150-152` ("Never leave stale copies"; "Paste this output before committing") inside the dispatch block, which 1c left alone.
6. Lifecycle rule file and Rule 26; `GUARDRAILS.md:269` ("Paste find output") left alone by 1a, decide with this task. Subagents do not commit or push.
7. Guides (planner boundary text, "Timothy", archives, `README.md:660`, `AGENT_NOTES.md`); `PLANNING_AGENT_SESSION_START.md:30`, `:166`, `:239` (older "commit" wording, left out of scope by 1b; Grok judged them mild).
8. Retire `SIMPLE_HANDOFF_TEMPLATE.md` and fix references.
Tasks that share a file run in order. Executor default Qwen.

## Not done yet

- 1c review and approval (above). `status.md` entry for 2026-10-19 to 2026-10-24 (append only; correct the 2026-10-18 entry's stale "Not committed" bullet without editing it).
- NEEDS_REVIEW: add one line to the lifecycle entry's Status (completed task returned to `active/` inside `8d90f0e`); a review note for the items in the table above (Docker placement, first-attempt banner, stray drafts `NEEDS_REVIEW.md`, three drafts' `status: drafts`).
- Knapsack items, Qwen logs, the retention question and the other Questions for Tracy from the 2026-10-22 handoff are unchanged.

## Process notes (what went wrong, so it is not repeated)

- My approve command ran `git add $B $D` after `git mv`; `$D` no longer existed, `git add` aborted, and the commit held the old content. Fix was `git add $B` then `git commit --amend --no-edit` on the unpushed commit. For approvals: stage only the new path, run `git diff --cached --stat` before committing, and do not push until Tracy pastes the check output.
- Test every sed or Edit on a copy before handing commands to Tracy.
- I read only the header of `galaxy-game-ui-first-attempt/status.md` and the first-attempt README; the rest of the galaxy-game-ui changes were checked by file list, status letters and grep, not read.

## Appendix: Grok review prompt template (replace HASH, FILE, TARGET, LINES)

```
You are a REVIEW agent (read-only). Review one draft task file from the public GitHub repo trmccormick/agent-tasks. Do not edit, create, or commit anything, and do not write a replacement task. Return a review only.

Read these at commit HASH (raw URLs, https://raw.githubusercontent.com/trmccormick/agent-tasks/HASH/<path>):
1. Draft under review: projects/agent-tasks/tasks/drafts/FILE
2. File it edits: TARGET (edit targets are lines LINES)
3. Template it must follow: TASK_TEMPLATE.md (dispatch block is lines 132-162)
4. Decisions it implements: projects/agent-tasks/NEEDS_REVIEW.md, entry "Draft-gate commit 442a462" Status line, and the 2026-10-20 lifecycle entry Status line (around line 134)

If you cannot open any of these URLs, say so and stop. Do not guess file contents.

Check, label each finding VERIFIED (you read it; quote the line), REPORTED (stated in a doc, name it) or INFERRED:
A. Each "old:" line appears in the target exactly once, character for character.
B. Each "new:" line matches the decisions in item 4.
C. The grep counts in the checks step are correct for the edited file, and the expected git diff --stat matches the number of single-line edits.
D. The dispatch block matches the template's, with only allowed substitutions; the agent-tasks backlog is flat, so "[SUBFOLDER]/" is omitted from the path lines (say whether you accept that).
E. Sized for a small local Qwen model: one concern, one file, exact old and new text, a grep check, numbered steps, one sentence naming the only stop points, a fixed completion line. Say if anything would make it stop early or guess.
F. The review gate is a hard stop, commit and push authorized separately, no "git add ." or "git add -A".
G. Any edit that contradicts another line in the target or another file; any line the task should change but does not.

Return exactly:
Disposition: GO, REVISE, or STOP
Findings: one line each, with its label and the line numbers or quoted text
Required changes (only if REVISE or STOP): numbered, each naming the exact line and replacement text
Questions for the human: only things you could not verify
Keep it under one page.
```

For 1c use: HASH `7625086`, FILE `2026-10-23-MEDIUM-DOCUMENTATION-TASK-TEMPLATE-BACKLOG-PATH-AND-STALE-COPY-WORDING.md`, TARGET `TASK_TEMPLATE.md`, LINES `17, 130, 421` (the template is also item 3; tell Grok line 130 is outside the dispatch code fence).

## Environment notes

Tracy works from several laptops and commits from the M4 as "Tracy McCormick" and from another machine as "Tracy A McCormick". Deliver whole files with `mv`, never leave copies in Downloads, one request at a time, explicit-path staging, path-limited commits, `git status -sb` after each push. Commit messages end with the Co-Authored-By and Claude-Session lines.
