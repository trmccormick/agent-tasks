# Review Closeout — 2026-11-14 (claude-free-web), agent-tasks workflow

**Role**: review (web). Follows `2026-11-12-WEB-REVIEW-WORKFLOW-CLOSEOUT.md` (same folder). The galaxy_game wiki work is in `projects/galaxy_game/handoffs/claude-free-web/2026-11-14-WEB-REVIEW-WIKI-CLEANUP-CLOSEOUT.md`.
**Evidence labels**: VERIFIED, REPORTED, INFERRED.

## State checked at close (VERIFIED, agent-tasks main `2208c47`, 2026-11-14)

- The NEEDS_REVIEW correction addendum is NOT yet on main.
- `projects/agent-tasks/NEEDS_REVIEW.md` still has merge-conflict leftovers (`<<<<<<< HEAD` line 540, `=======` line 542, from `e6a7af0`).
- `projects/galaxy_game/CHATGPT_TO_CLAUDE.md` has no Claude response yet.
- Not read this session: the 11-13 changes to `PLANNING_LOCAL.md` (Step 0 restructure) and the session-guidance and task-spec updates in `2208c47`, `6ff60fa`, `8ca4030`; the 7 UI draft tasks.

## Pending actions (text ready; Tracy commits)

1. **Correction addendum** (final wording, role-based; append at the end of the 2026-11-11 entry "Small focused direct-session prompts work well for Qwen" in `projects/agent-tasks/NEEDS_REVIEW.md`):

```
**Correction (2026-11-12, Tracy; recorded by Claude)**: Point 1 of the addendum above is wrong where it says to have the executor read this entry first. NEEDS_REVIEW.md is for planning and research agents (whichever model fills the role, local or web). It is not for implementation agents deployed on work tasks, because it can hold stale entries and is about 150 KB. Implementation-agent startup is only the template order: GUARDRAILS, project README, SESSION_GUIDANCE. The planning agent reads this file before drafting an implementation prompt. A model acting in an implementation role follows implementation startup, whichever model it is.
```
   Commit: `git commit -m "NEEDS_REVIEW (agent-tasks): correct executor startup note" -- projects/agent-tasks/NEEDS_REVIEW.md`
2. **Reply in the channel**, beneath ChatGPT's request in `projects/galaxy_game/CHATGPT_TO_CLAUDE.md` (ChatGPT's handoff `cb97d44` notes none appeared). Summary to use: genuine open items are Rails 8 / API direction, portal item 4, and the Eden anchor-mass threshold (backend entries ON HOLD); answered or obsolete are the 08-30 loop task (in tasks/completed/2026-08), the 08-01 naming research task (completed; the naming rule is still not in the wiki), and portal draft items 1-3; overlaps are cross-referenced and intentional; highest priority next item (tentative) is the anchor-mass threshold; Tracy decisions are listed in the galaxy_game handoff. Process note accepted: the header and duplicate-bullet edits in `54c7579` were Tracy-approved exceptions. Commit 6f285c6 does not exist in galaxyGame but does exist in agent-tasks (UI NEEDS_REVIEW addenda).
3. **Merge-conflict leftovers**: proposed fix (needs Tracy's OK; outside the file's append-only rule): keep the line 541 paragraph (the one that adds ', to the "Task-drafting workflow overhead" entry above'), delete the two marker lines and the identical duplicate paragraph at line 543.
4. **Header addition proposal** for the galaxy_game NEEDS_REVIEW editing rules: owner-approved exceptions are named in the commit message and in an addendum.
5. **Optional addendum** recording today's workflow findings (below).

## Workflow findings for the tuning entries (REPORTED or observed)

- Narrow prompts beat repeated nudging. Qwen stalled (announce a plan, end the turn) on every multi-edit page this week; single-edit prompts and prompts that give the exact old and new strings did not. Startup reading outside the workspace also triggered stalls.
- Scripts with asserts, tested against the linter on a clone first, are the best fit for tables of small exact substitutions.
- A different executor may appear (commit `ae1ad8d` was authored as Copilot); it asks questions that tools could answer. Prompts should say "check with tools; do not ask" and give full paths for files outside the workspace.
- Terminal pastes mislead (soft-wrap drops spaces); verify from the file or a byte comparison on origin/main.
- ChatGPT edited a frozen doc outside `docs/wiki`; anything outside the wiki is frozen and not to be edited (revert `5c9028f` in galaxyGame).

## Files left uncommitted

None from Claude in this repo.
