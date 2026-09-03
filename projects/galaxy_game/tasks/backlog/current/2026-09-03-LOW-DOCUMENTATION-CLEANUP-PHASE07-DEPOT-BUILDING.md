---
status: backlog
priority: LOW
type: documentation
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-09-03
last_updated: 2026-09-03
---

## 🔴 Agent Dispatch Interface

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-03-LOW-DOCUMENTATION-CLEANUP-PHASE07-DEPOT-BUILDING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-09-03-LOW-DOCUMENTATION-CLEANUP-PHASE07-DEPOT-BUILDING.md \
         projects/galaxy_game/tasks/active/2026-09-03-LOW-DOCUMENTATION-CLEANUP-PHASE07-DEPOT-BUILDING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-03-LOW-DOCUMENTATION-CLEANUP-PHASE07-DEPOT-BUILDING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-03-CLEANUP-phase07-depot-building.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Cleanup sweep — backlog/phase07-depot-building/ (19 task files)
**Status**: BACKLOG
**Priority**: LOW
**Type**: documentation
**Created**: 2026-09-03
**Last Updated**: 2026-09-03

---

## Context

Part of a systematic backlog cleanup initiated 2026-09-03 after discovering the
Lookup Service Caching task had a stale duplicate (completed work left with
`status: active` in `completed/`, plus a recreated copy in `backlog/current/`).
This task covers the `backlog/phase07-depot-building/` folder specifically.

## Problem Statement

Task files in `backlog/phase07-depot-building/` may have:
1. **Duplicates** — same filename exists in another folder (active/, completed/, other backlog subfolder)
2. **Status mismatches** — YAML `status:` field doesn't match the folder location (e.g. `status: active` in backlog/)
3. **Missing YAML frontmatter** — no `status:` field at all (pre-template files)
4. **Completed work** — task was actually done but never moved to completed/

## Implementation Steps

### Step 0 — Move task file to active/ (standard)

### Step 1 — Inventory
List all .md files in `backlog/phase07-depot-building/` (excluding README.md).
Record count. Compare against expected: 19 files.

### Step 2 — Check for duplicates
For each file, run:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "FILENAME.md"
```
If more than 1 result → DUPLICATE. Record which copies exist.

### Step 3 — Check status headers
For each file, extract the YAML `status:` field:
```bash
grep -m1 "^status:" FILENAME.md
```
Flag any file where:
- `status:` is `active` or `completed` (should be `backlog` in this folder)
- `status:` field is missing entirely (pre-template file)

### Step 4 — Check for completed work
For files flagged as `status: completed` or with completion reports filled in,
verify whether the work was actually done in the galaxyGame codebase (git log,
file existence). If done → move to `completed/2026-09/` with correct status.

### Step 5 — Fix issues
- Duplicates: keep the canonical copy (usually the one in the correct lifecycle folder), remove the stale one with `git rm`
- Status mismatches: correct the YAML `status:` field to match the folder
- Missing frontmatter: add proper YAML block (status, priority, type, created, last_updated)
- Completed work: `git mv` to `completed/2026-09/` + update status to `completed`

### Step 6 — Verify
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "*.md" -path "*/phase07-depot-building/*" | wc -l
```
Confirm count matches expected (minus any moved to completed/).

### Step 7 — Commit
```bash
git add [specific files changed]
git commit -m "chore: cleanup sweep backlog/phase07-depot-building/ — [summary of fixes]"
```

## Acceptance Criteria
- [ ] All files in `backlog/phase07-depot-building/` inventoried
- [ ] No duplicates remain (find returns exactly 1 result per filename)
- [ ] All YAML status fields match folder location
- [ ] All files have valid YAML frontmatter
- [ ] Completed work moved to completed/ with correct status
- [ ] Commit made with specific file list

## Stop Conditions
- More than 5 duplicates found (suggests systemic issue, escalate)
- A file's content is ambiguous (can't tell if work was done or not)
- Fix requires changing more than the target folder

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: Sibling cleanup tasks for other backlog folders (same date, same pattern)

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Files checked**:
**Issues found**:
**Issues fixed**:

## Handoff Summary
HANDOFF SUMMARY:
