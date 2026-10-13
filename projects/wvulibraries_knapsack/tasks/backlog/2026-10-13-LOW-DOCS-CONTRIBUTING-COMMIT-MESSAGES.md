---
status: backlog
priority: LOW
type: DOCS
project: wvulibraries_knapsack
# [FILL IN] Match the remaining header fields (for example `resume`) to TASK_TEMPLATE.md.
---

# Add a "Commit messages" subsection to wvu_knapsack CONTRIBUTING.md

Drafted by the web review agent on 2026-10-13 from pasted and fetched evidence. Not dispatched. Leave in `backlog/` until Tracy assigns it.

## Context

The Hyku 7.2.0 upgrade session (branch `feature/hyku-v7.2.0-upgrade` in `wvu_knapsack`) produced commits such as `fix: Guard Hyku VERSION constants`, `chore: Remove override` and `fix: Add shim`. The pattern is not documented. `NEEDS_REVIEW.md` (entry "Commit message conventions not specified", 2026-10-13) first said no CONTRIBUTING rules existed. That is wrong: `CONTRIBUTING.md` exists in the knapsack repo root. Its "Git Workflow" section says only "Make changes and commit with clear messages" and has no prefixes, no upstream references and no multi-commit pattern (REPORTED: Tracy pasted the file from GitHub; branch not confirmed).

## Problem Statement

Commit messages in the knapsack repo follow an informal convention that is written down nowhere, so future sessions will not follow it consistently.

## The text to add

Add this subsection exactly as written, directly after step 2 of "Git Workflow" (the step that says to make changes and commit with clear messages). Change nothing else in the file.

```markdown
### Commit messages

- Start with a type prefix: `fix:`, `chore:`, `docs:`, `feat:`, `test:`.
- One logical change per commit. A fix that needs several steps (for example a VERSION override: guard, shim, remove override) is several commits, each saying what it guards or restores.
- A temporary workaround for an upstream bug names the upstream issue or PR in the commit message and in a code comment (example: `samvera/hyku#3397`), along with the condition for removing it.
- A submodule pointer bump is its own commit: `chore: Bump Hyku submodule to <version>`.
```

The `fix:` and `chore:` prefixes and the one-fix-per-commit pattern come from the observed commits. The `docs:`, `feat:` and `test:` prefixes and the upstream-reference rule are proposals Tracy approved on 2026-10-13.

## Gotchas

- The edit happens in the **`wvu_knapsack` repo**, not in `agent-tasks`. Tracy has no local copy of that repo on the web-review machine; the implementation machine does. [FILL IN: confirm the local path, which SESSION_GUIDANCE lists as `/Users/tam0013/Documents/git/wvu_knapsack/`.]
- The knapsack repo has two relevant branches. Confirm the current branch before editing. Do not switch branches.
- Never edit anything inside `hyrax-webapp/`.
- Only the human stages, commits and pushes (GUARDRAILS Rule 26). Do not use `git add -A`, `git add .` or `git commit -a`.

## Acceptance Criteria

- `git diff` in the knapsack repo shows only added lines in `CONTRIBUTING.md`, and those lines are the subsection above.
- The diff output is pasted into chat before anything is staged.

## Stop Conditions

Stop and report, without editing, if:
- the working tree is dirty, or the branch is not the one Tracy named;
- "Git Workflow" is missing, or does not contain the numbered steps "1. Create a feature branch" and "2. Make changes and commit with clear messages";
- a "Commit messages" subsection already exists.

## Files Involved

[FILL IN] by an agent that can read the repo. Expected: `CONTRIBUTING.md` in the knapsack repo root, and nothing else.

## After the edit

Follow the Task Completion Workflow in the agent-tasks `README.md` for moving this file to `completed/` and updating `status.md`. Also update the `NEEDS_REVIEW.md` entry for commit conventions: correct the "what I already checked" line (CONTRIBUTING.md exists) and mark it RESOLVED with the date.
