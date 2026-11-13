# Review Closeout — 2026-11-12 evening (claude-free-web), agent-tasks workflow

**Role**: review (web). Follows `2026-11-12-WEB-REVIEW-CLOSEOUT.md` (same folder). The galaxy_game wiki work from the same session is in `projects/galaxy_game/handoffs/claude-free-web/2026-11-12-WEB-REVIEW-WIKI-CLEANUP-CLOSEOUT.md`.
**Evidence labels**: VERIFIED, REPORTED (Tracy or another agent), INFERRED.

## Workflow findings (REPORTED by Tracy unless marked)

- **Roles are not tied to models.** Planning, research and implementation are roles; any model (local or web) can fill one. Qwen is the local model Tracy currently runs. ChatGPT (GitHub read/write, no shell) can act as an implementation agent for documentation work that does not need the application or the linter running, and can also act as a planning agent.
- **Both Ollama hosts and both Qwen instances are interchangeable and Tracy's.** Do not describe one instance as a particular agent's.
- **NEEDS_REVIEW.md is for planning and research agents, not for implementation agents deployed on work tasks.** It can hold stale entries and is about 150 KB (VERIFIED: `projects/agent-tasks/NEEDS_REVIEW.md` 152 KB). Implementation startup is the DIRECT_SESSION_TEMPLATE.md order: GUARDRAILS, project README, SESSION_GUIDANCE. VERIFIED sizes: GUARDRAILS 51 KB, SESSION_GUIDANCE 11 KB, README 6 KB.
- **Focused prompts work; large startup reading stalls.** A fresh implementation session stopped right after announcing its plan when the prompt had it read the 152 KB file (INFERRED cause). Sessions with only the three startup files and one page rarely stall (REPORTED, Tracy, matches ChatGPT's prompts). Qwen sometimes announces a plan and ends its turn; a short continuation nudge fixes it.
- **Executor reports are summaries.** Require the diff or the file, and verify from the file or a script, not from pasted terminal output (soft-wrap drops spaces).
- **Stale sessions exist.** An old VS Code session in agent-tasks produced an out-of-date report that was misread as new twice. Ask which session and machine a report came from before acting.

## Pending actions (not yet committed; text was given to Tracy in chat)

1. **Correction addendum** at the end of the 2026-11-11 entry "Small focused direct-session prompts work well for Qwen" in `projects/agent-tasks/NEEDS_REVIEW.md`. It corrects point 1 of my addendum `981cb33` (which wrongly had executors read the entry first). Wording to use: NEEDS_REVIEW.md is for planning and research agents (whichever model fills the role); not for implementation agents; implementation startup is GUARDRAILS, project README, SESSION_GUIDANCE; the planning agent reads the file before drafting an implementation prompt; optionally add "A model acting in an implementation role follows implementation startup, whichever model it is." Commit with `git commit -m "NEEDS_REVIEW (agent-tasks): correct executor startup note" -- projects/agent-tasks/NEEDS_REVIEW.md`.
2. **Reply in the channel** under ChatGPT's request in `projects/galaxy_game/CHATGPT_TO_CLAUDE.md` (the five-point audit answer; text given in chat). ChatGPT's handoff `cb97d44` notes no reply had appeared.
3. **Merge-conflict leftovers (VERIFIED on main)**: `projects/agent-tasks/NEEDS_REVIEW.md` still has `<<<<<<< HEAD` (line 540) and `=======` (line 542) from commit `e6a7af0` (2026-11-03), with two paragraphs "Update 2026-10-31 (evening, Claude web review)" identical except the first adds ', to the "Task-drafting workflow overhead" entry above'. No other `.md` file has markers. Proposed fix (needs Tracy's OK, outside the file's append-only rule): keep line 541, delete the two marker lines and the duplicate paragraph.
4. **Header addition proposal** for the galaxy_game NEEDS_REVIEW editing rules: owner-approved exceptions are named in the commit message and in an addendum.
5. **Further NEEDS_REVIEW addendum** (optional, offered, not written): today's findings above (startup file size, retyped-text edits, summaries vs diffs).

## Open questions this session raised (for the existing workflow-tuning entries)

- Whether `DIRECT_SESSION_TEMPLATE.md` or SESSION_GUIDANCE should say: implementation prompts include only three startup files, one page, a stop point; the planner reads NEEDS_REVIEW, the implementer does not.
- Whether `projects/agent-tasks/NEEDS_REVIEW.md` (995+ lines, about 45 entries) needs its own triage and Editing rules (the galaxy_game and UI files have them).

## Files left uncommitted

None from Claude in this repo. Items 1 and 2 above are text for Tracy to commit.
