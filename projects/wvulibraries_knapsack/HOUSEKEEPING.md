# Housekeeping tracker (wvulibraries_knapsack + agent-tasks)

**Status: DRAFT, proposed 2026-10-19, not yet approved by Tracy.** Nothing here authorizes deleting or moving anything.

**Purpose**: Track recurring housekeeping issues observed in this project. Agents have been:
1. Using `cp` to duplicate task files, leaving stale untracked copies with outdated frontmatter
2. Running diagnostic/exploratory commands and leaving output files uncommitted
3. Creating temporary branches/files without cleanup steps in task documentation

This file breaks cleanup into small items per session (max 10 files per slice) so it never becomes a big-bang cleanup.

## Observed Issues This Session

**Issue 1: Stale Task File Copy (2026-10-19)**
- Agent moved or copied `2026-10-14-HIGH-BUG-FACET-LIMITER-DEFAULTS-TO-10-ON-7.2.md` from backlog/ to active/
- Left untracked copy in active/ with only status field changed
- No synthesis report, no work evidence, dispatch interface pointing to wrong path
- Root cause: Used `cp` instead of git workflow; frontmatter not fully updated
- Fix: Verified canonical version in backlog/, removed duplicate
- Prevention: Add "cleanup steps" section to task template

**Issue 2: Temporary Command Output Not Cleaned (pattern)**
- Agents run `grep`, `diff`, `ls` commands for diagnostics
- Output sometimes left in shell history or temp files
- No tracking of what was created vs. what should be committed

## Standing Rules

1. **All files agents create must be intentional.** Untracked files are suspect:
   - Task files: MUST be tracked with `git add` before dispatch
   - Diagnostic output: Should be in a commit message or handoff, not left loose
   - Temporary files: MUST have explicit cleanup step in task

2. **Use git mv, never cp.** When moving task files between folders:
   - Always use `git mv` (not `cp` then `rm`)
   - Update all frontmatter (status, dispatch interface paths, etc.)
   - Verify with `git status` before committing

3. **Document cleanup in task files.** Every task that creates temporary files must include:
   - "Cleanup Steps" section specifying what to remove
   - Clear "do NOT commit" guidance for scratch files
   - Optional: output that should go to handoff, not git

4. **One housekeeping slice per session:** At most one item from this tracker per planning session closeout, max 10 files affected.

5. **Keep:** The newest handoff per agent, and anything an open task references. Archive everything else after 30 days.

## Items

Status: `todo`, `in progress`, `done`

| ID | Issue | Evidence | First Step (Read-Only) | Prevention | Status |
|---|---|---|---|---|---|
| K1 | Task file duplication via `cp` | Untracked 2026-10-14 facet limiter in active/ (227/228 lines identical to backlog version, only status field differed) | Review git history for similar patterns: `git log --all --grep="cp" --oneline \| head -20` | Add "git workflow (git mv, not cp)" rule to TASK_TEMPLATE.md dispatch section | todo |
| K2 | Stale task with outdated frontmatter | Same file: dispatch interface still pointed to backlog/ despite being in active/ | Check active/ folder for any task files with mismatched status field vs. folder location | Add validation: planning agent must verify status matches folder (active→active, backlog→backlog) in readiness checklist | in progress |
| K3 | Untracked temporary files from diagnostics | Pattern in session history: agents run `grep`, `diff`, `ls` and leave output loose | Search workspace for untracked `.txt`, `.log`, `.diff` files: `git status --short \| grep "^??" \| head -20` | Task template: add "Cleanup Steps" section (required field, filled or marked N/A) | todo |
| K4 | Lack of cleanup instructions in tasks | Agents diagnostic work often has no documented cleanup | Sample 3 recent task files in backlog/ and check for cleanup steps | Add "Cleanup Steps (required)" to TASK_TEMPLATE.md as mandatory section | todo |
| K5 | Handoff files not organized by agent folder | Before 2026-10-19 fix, knapsack had old handoffs in root of handoffs/, new ones need to live in agent-specific folders | Current state: `handoffs/` has both unorganized files and new `haiku-copilot/` subfolder | Document naming convention (`claude-web`, `qwen-planning`, `haiku-copilot`, etc.) in handoffs folder README | todo |
| K6 | Stale commits accumulating before session start | Sessions sometimes start with 1-2 local-only commits not pushed | Check `git log origin/main..HEAD` at session start | Add verification step to PLANNING_AGENT_CLOSEOUT.md: confirm no local-only commits before declaring closeout complete | todo |

## How Planning Agent Uses This (New Proposal Step)

At the **end of each closeout**, before final commit:

1. Read this file. Pick the first `todo` item with no open dependency.
2. Do its read-only first step (no changes yet).
3. Propose findings + recommended action in the Session closeout summary.
4. Wait for Tracy's approval. Act only on approved items.
5. Record the slice in Session log below and update item status.

## Session Log

| Date | Item | What Was Done | Approval | Commit |
|---|---|---|---|---|
| 2026-10-19 | K2 (in progress) | Identified stale task with mismatched status/folder; removed untracked duplicate; verified canonical version; updated status.md and created handoff | Tracy approved after verification work | 7540d03, 98066ed |

## Related Documentation

- **Source pattern**: `projects/galaxy_game/HOUSEKEEPING.md`
- **Enforcement points**: 
  - Task template (TASK_TEMPLATE.md): add cleanup steps + git mv guidance
  - Readiness checklist: add status/folder validation
  - Planning closeout workflow: add housekeeping slice step
  - Session closeout: add cleanup summary section

---

**Next planning session**: Check item K1 (task file duplication patterns), propose any files to archive or move, wait for approval.
