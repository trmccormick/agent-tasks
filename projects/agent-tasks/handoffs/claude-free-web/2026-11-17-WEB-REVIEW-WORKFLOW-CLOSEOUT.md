# Review Closeout — 2026-11-17 (claude-free-web), agent-tasks workflow

**Role**: review (web). Follows `2026-11-14-WEB-REVIEW-WORKFLOW-CLOSEOUT.md` (same folder). Session ended at the usage limit; work is paused, not finished.
**Evidence labels**: VERIFIED (checked this session in a clone of origin), REPORTED (Tracy or another agent said so), INFERRED.

## State checked at close (VERIFIED, agent-tasks main `f000efd`, 2026-11-17)

- `templates/DRAFT_REVIEW_PROMPT_TEMPLATE.md` exists (commit `c101a84`, 137 lines). One standard draft-review prompt; one task per review; three-file package (draft task, `TASK_TEMPLATE.md`, `REVIEW_AGENT_GUIDE.md`) for every agent; access blocks A (repo-read), A + write lock, B (upload-only); re-review variant.
- `REVIEW_AGENT_GUIDE.md` line 160 points to the template (commit `61f00bd`).
- Review of the two Hyku 7.2 drafts is saved at `projects/wvu_knapsack/summaries/2026-11-17-REVIEW-HYKU-7.2.0-UPGRADE-VALIDATION-TASKS.md` (`4c837d0`): disposition REVISE for both. It is a single Claude review saved by Haiku, not an independent second opinion.
- Correction commit `f000efd` (authored by Tracy) changed the reviewer line, added the Phase A "provisional" sentence, and changed the Final Recommendation to `backlog/`. The `NEEDS_REVIEW.md` entry now says "a single review rather than an independent second opinion."

## Pending actions

1. **One correction missed.** The report's Evidence Boundary still lacks the sentence "This is a single review, not an independent second opinion." (VERIFIED by diff `ecea527..f000efd`). Add it and commit.
2. **Hyku drafts stay in `drafts/`.** Neither is dispatchable. Mandatory corrections are in the report; the first is Task 2's `docker compose down --volumes` (destroys the data under test). Neither task has the template structure (no Agent Dispatch Interface block: 0 grep matches, VERIFIED). Tracy is not dispatching them yet.
3. **Phase A re-check** of both drafts against the real `TASK_TEMPLATE.md` by a repo-capable reviewer. Claude's Phase A was written without the template.
4. Items needing a local stack or person (not ChatGPT/Claude): confirm Hyku console commands and multi-tenant switching, the real reindex command, and the submodule Gemfile.lock handling.

## Template change Tracy was asked about (not made)

The 2026-11-15 Admin Planetary Monitor chain (ChatGPT drafted, Qwen aligned, Perplexity REVISE, Gemini, Grok GO; replies went back to ChatGPT, who updated the draft directly, then the next reviewer) worked well and faster than Qwen (REPORTED, Tracy). The template treats ChatGPT only as a reviewer with a write lock. Proposed: add a **reviser** role.

| Role | Access | Rule |
|---|---|---|
| Reviewer | read-only or upload-only | Returns a report as text; never edits |
| Reviser (ChatGPT) | read/write git | Applies only the listed corrections to named files; stops and reports if the file differs from the reviewed commit; returns the new commit hash; one commit per reviewer round |
| Human | final say | Directs the commit by naming files; approves the move to `backlog/` |

Update the access table to separate reviewing from revising. Tracy had not yet said yes.

## Corrections to what Claude said earlier this session

- The 2026-10-20 entry already decides the review vocabulary: GO / REVISE / STOP everywhere (so `REVIEW_AGENT_WORKFLOW.md` §4 and `REVIEW_REPORT_TEMPLATE.md` change) and VERIFIED / REPORTED / INFERRED. Apply that decision; the "not for draft reviews" note Claude proposed is superseded.
- Commit approval is largely decided (2026-10-20 lifecycle entry): agents may commit and push when Tracy tells them to; confirming a command in an agent's own approval prompt counts. The rule text may still need updating.
- Claude told Qwen (via a scratch task file) to commit and push. Rule 26 requires Tracy's explicit approval in that chat. Qwen stopped and asked, so no violation occurred (REPORTED).

## NEEDS_REVIEW.md cleanup (Tracy: it must shrink)

`projects/agent-tasks/NEEDS_REVIEW.md`: 1,041 lines, 158 KB, 47 real entries, all OPEN (VERIFIED at `ecea527`). Preliminary sort by title and status line only; bodies not yet read:

| Group | About | Likely action |
|---|---|---|
| Decided by Tracy, doc edits not made (handoff rules, SIMPLE_HANDOFF retirement, review vocabulary, lifecycle validation, duplicate guides, draft-gate commit, pair non-repo drafters) | 7 | Execute, then archive |
| Incident reviews awaiting Claude (Oct 25 to Nov 5) | 9 | Answer once, archive |
| Sign-in refactor cluster (Oct 31, work paused) | 6 | Archive with a note |
| Nov 5 "add to the docs" recommendations | 5 | Merge into one doc task, archive |
| Other | about 20 | Triage one by one |

"Web agent GitHub access capabilities differ" and "Review-gate artifact provisioning" (both 11-05) may be closed by the access tiers and package; confirm by reading. Process: read a group, propose execute / archive / keep per entry, Tracy decides, then a small task moves archived entries to an archive file (no deletes, Rule 29). Start with the decided group.

## Workflow adjustments parked for later (all need Tracy's decision)

1. Retire `SIMPLE_HANDOFF_TEMPLATE.md` (decided 2026-10-20; still live). `rules/GUARDRAILS.md` Rule 13b line 362 also names it and is missing from the entry's reference list.
2. Amend Rule 13b to two routes (direct session; formal task, dispatched by its own Agent Dispatch Interface) and add a lighter reading tier for doc-only edits (GUARDRAILS Rules 0, 12 if touching task files, and 26 plus the named files, instead of about 124 KB of GUARDRAILS + README + SESSION_GUIDANCE).
3. Rewrite the "Agent Handoff Generation Process" section of `README.md` (depends on 1 and 2).
4. Apply the 2026-10-20 vocabulary decision to `REVIEW_AGENT_WORKFLOW.md` and `REVIEW_REPORT_TEMPLATE.md`; add one line to `WEB_AGENT_SESSION_START.md` that the three-file package replaces it for draft reviews.
5. Move the other templates into `templates/` last. `TASK_TEMPLATE.md` has 172 referencing files; if moved, leave a stub at the old path.
6. Size check before drafting a task: the 11-15 task covered a two-commit code fix done directly (16 task commits in two hours). Rule 13b already says small work is a direct session.
7. Record each external reviewer's disposition in the repo (11-15 entry question 3); decide whether a task is re-checked as still needed before the move to `backlog/`.

Sequence 1 to 3 with the draft task about `TASK_TEMPLATE.md` contradictions, which also edits the dispatch block.

## Constraints to carry forward

- The 2026-11-07 "workflow is still experimental" entry authorizes no workflow-document changes and asks whether to run one real task through the flow first (the 11-15 chain may count).
- One prompt to an agent at a time; wait for results. Small, separate tasks. Prompts to Qwen short, pointing to a file.
- Output files in `scratch/`, with a cleanup step. Use `mv`, not `cp`, for downloads. Stage by filename; never `git add .` or `-A`.
- No agent commits or pushes without Tracy's explicit approval in that chat (Rule 26). If Tracy says "commit", that is the approval.
- Reviewers are read-only. Only Tracy moves a task from `drafts/` to `backlog/` (open question: whether a directed agent may).

## Files left uncommitted

None from this session once the handoff itself is committed.
