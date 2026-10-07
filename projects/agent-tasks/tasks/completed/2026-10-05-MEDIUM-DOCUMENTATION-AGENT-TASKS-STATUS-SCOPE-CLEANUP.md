---
status: completed
priority: MEDIUM
type: documentation
system_domain: AGENT_WORKFLOW
mvp_alignment: CORE_OPERATIONS
local_worker_safe: true
cross_project_impact: false
---

> **Draft authored by Claude (review tier) on 2026-10-05 from the GitHub `main` tip (`34a1377`) only.** Claude could not see Tracy's local working tree. Every line number, hash, and section name below comes from the remote copy and MUST be re-verified against the local file before use. Sections marked `[FILL IN]` are Qwen's to complete from terminal evidence.

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] Scope and Out-of-Scope lists reviewed by Tracy
- [ ] Synthesis (edit plan) requirement understood: no edits until Tracy approves the plan
- [ ] Acceptance Criteria are measurable
- [ ] Tracy has confirmed dispatch for this task

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: agent-tasks
Task: /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks/backlog/2026-10-05-MEDIUM-DOCUMENTATION-AGENT-TASKS-STATUS-SCOPE-CLEANUP.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  This file is new and untracked: create tasks/active/ if it does not exist, then
    mv projects/agent-tasks/tasks/backlog/2026-10-05-MEDIUM-DOCUMENTATION-AGENT-TASKS-STATUS-SCOPE-CLEANUP.md \
       projects/agent-tasks/tasks/active/2026-10-05-MEDIUM-DOCUMENTATION-AGENT-TASKS-STATUS-SCOPE-CLEANUP.md
    git add projects/agent-tasks/tasks/active/2026-10-05-MEDIUM-DOCUMENTATION-AGENT-TASKS-STATUS-SCOPE-CLEANUP.md
  If the file is already tracked, use git mv instead.
  Then open the moved file and change: status: backlog → status: active
  Paste the output of these commands and of the find check below in chat before proceeding.

LIFECYCLE: backlog → active → completed
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks -name "2026-10-05-MEDIUM-DOCUMENTATION-AGENT-TASKS-STATUS-SCOPE-CLEANUP.md"
    Only ONE result should exist.

READ FIRST (after Step 0): the task file below contains scope, steps, and stop conditions.

CRITICAL: Save the synthesis (edit plan) as an MD file in the agent-tasks repo before any edit:
  /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/summaries/2026-10-05-SYNTHESIS-AGENT-TASKS-STATUS-SCOPE-CLEANUP.md
  Post only the path and a 3-line summary in chat, then WAIT for Tracy's approval.
```

---

# TASK: Remove other projects' content from `projects/agent-tasks/status.md`

**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-10-05
**Authority**: Tracy approved dispatch of cleanup work in session on 2026-10-05.

## Context

`projects/agent-tasks/status.md` is meant to track work on the agent-tasks repo itself: governance rules, templates, routing docs, and shared workflow. Review on 2026-10-05 found it also carries Galaxy Game and EVE dashboard material, most of which is already tracked in those projects' own status files.

Remote-copy observations (verify locally, line numbers will differ):

- Header line and "In Flight" section list the TransitEngine topology pass (Galaxy Game).
- A "Galaxy Game Task System Audit — DELIVERED" section lists GCC mining and asset-generation task paths, a `lunar_production` search result, and `methane.json` pricing (Galaxy Game).
- Same-day session logs mention Galaxy Game summaries, backlog tasks, and `PERPLEXITY_SESSION_START_GALAXY_GAME.md`.
- "Recent Git Activity" is a repo-wide commit table. Its market-bug and ESI rows belong to `eve_dashboard`. Commit `88dfdd9` ("history moved verbatim to status-archive.md") purged `projects/eve_dashboard/status.md`, not this file.
- Galaxy Game's own `status.md` already has TransitEngine sections and a "GCC Mining Work — HELD" section.

The agent-tasks status file also cites commits (`bbce10af`, `1d4da71`, `f11f720`, `e7d98f4`, `7dfc77e`) that were not on the GitHub remote at review time.

## Problem Statement

Project-specific work recorded in the agent-tasks status file duplicates other status files and obscures what is actually pending for the shared repo. The goal is scope correctness only, not a redesign of the status format.

## Scope

In scope:
1. Read-only baseline check (Step 1).
2. Identify blocks in `projects/agent-tasks/status.md` that belong to another project (Step 2).
3. Remove those blocks from `projects/agent-tasks/status.md` after Tracy approves the plan (Step 4), preserving their text verbatim in the synthesis file.
4. Make the "Recent Git Activity" table agent-tasks-only.

## Out of Scope — DO NOT

- Edit `projects/galaxy_game/status.md`, `projects/eve_dashboard/status.md`, or any other file besides `projects/agent-tasks/status.md`. Galaxy Game's status file has uncommitted local edits; leave them alone.
- Add the removed content to another project's status file. If an item is not already tracked in its owner project, STOP and report it (see Stop Conditions).
- Restructure, reorder, summarize, or collapse the remaining session logs. That is a separate discussion Tracy wants to have after this cleanup.
- Edit `rules/GUARDRAILS.md`, `TASK_TEMPLATE.md`, routing docs, or any MAG task file.
- Stage anything beyond this task file's move, commit, push, or move this task to `completed/` (Rule 26; Tracy approves each).
- Touch gitignored paths (Rule 28) or run git commands inside Docker.

## Steps

**Step 1 — Baseline (read-only).** From the repo root on the host (not through the `docs/new_agent` symlink), paste raw output, not a summary, for:

```
git status --porcelain=v1
git branch --show-current
git log --oneline origin/main..HEAD
git diff --stat -- projects/agent-tasks/status.md
git diff --stat --cached -- projects/agent-tasks/status.md
git cat-file -t 1d4da71; git cat-file -t e7d98f4; git cat-file -t bbce10af
```

Record whether `projects/agent-tasks/status.md` has uncommitted changes. If it does, edits in Step 4 are applied on top of them without reverting anything.

**Step 2 — Classify.** Read `projects/agent-tasks/status.md` in full. For each section, bullet, and table row, classify as AGENT-TASKS (keep), GALAXY GAME, EVE DASHBOARD, or OTHER PROJECT (remove). For the commit table, classify each row with `git show --stat <hash>` (touches only `rules/`, templates, root docs, or `projects/agent-tasks/` = keep). `[FILL IN]` classification table in the synthesis file: item, location (local line range), classification, evidence.

**Step 3 — Owner check.** For every item classified as another project's, confirm it is already tracked in that project's status file (grep with the matching phrase) and record the file and line. `[FILL IN]` evidence column.

**Step 4 — Synthesis and approval gate.** Save the synthesis file (path in the Dispatch Interface) containing: Step 1 output, the classification table, the owner-check evidence, a verbatim copy of every block to be removed, and the exact proposed edits. Post path plus a 3-line summary in chat. WAIT for Tracy's approval. Do not edit before it.

**Step 5 — Apply (only after approval).** Remove only the approved blocks from `projects/agent-tasks/status.md`. Update its `Last Updated` header to describe this cleanup only. Do not stage or commit.

**Step 6 — Verify and report.** Re-run and paste:

```
git diff --stat
git status --porcelain=v1
grep -n -i 'galaxy\|GCC\|TransitEngine\|methane\|lunar_production\|market bug\|ESI' projects/agent-tasks/status.md
```

Every remaining grep hit must be justified in the report as agent-tasks-scoped, or removed. Add a short Completion Report section to this task file. Leave the task in `active/` and wait for Tracy's decision on commit and on moving it to `completed/`.

## Gotchas

- The remote copy of the status file may differ from the local one. Trust the local file; report any differences from the line numbers above, do not "fix" them.
- `status-archive.md` exists only under `projects/eve_dashboard/`. There is no agent-tasks archive. Do not create one.
- Do not infer ownership from which file a task path points at alone; check what project the item is about.
- Rule 30: the synthesis file goes in the agent-tasks repo `summaries/`, never a code repo.

## Acceptance Criteria

1. Step 1 raw output is pasted in chat and in the synthesis file.
2. The synthesis file contains a classification for every section and table row, and a verbatim copy of each removed block.
3. No edit happens before Tracy's approval of the plan.
4. After Step 5, `git status --porcelain=v1` shows only `projects/agent-tasks/status.md` modified, plus the new synthesis file and this task file's move.
5. Every removed item has owner-status evidence recorded, or was reported under Stop Conditions and left in place.
6. The commit table contains only agent-tasks-scoped commits.
7. No commit, push, or other-file edit occurred.

## Stop Conditions

Stop and report in chat, without improvising, if:
- Any item to be removed is NOT already tracked in its owner project's status file.
- `projects/agent-tasks/status.md` has uncommitted changes that overlap blocks slated for removal.
- Classification of any item is ambiguous (e.g., governance work that touched a project path).
- The local file differs materially from the remote-copy description above.
- Two copies of this task file exist in different lifecycle folders (MAG-1 duplicate protocol).

## Completion Report

```
b4368e5 2026-10-07 status: remove Galaxy Game and stale state content from agent-tasks status

 projects/agent-tasks/status.md | 31 +------------------------------
 1 file changed, 1 insertion(+), 30 deletions(-)
```

Committed and pushed as `b4368e5` (2026-10-07). The commit removed the Galaxy Game Task System Audit section (including methane/lunar_production lines), Recent Git Activity table, Working tree line from Project Health, Remaining Working Tree State line from MAG session log, TransitEngine Topology In Flight subsection, Phase Folder Verification closure, and updated the Last Updated header to remove stale claims.
