# Task File Lifecycle Validation — Required for All Agents

**Status**: Foundational Rule (applies to ALL projects)  
**Applies to**: Any agent operating on task files in agent-tasks

---

## Rule: Pre-Flight Validation (MANDATORY)

**When**: EVERY operation on a task file (move, edit, close, review)  
**Where**: agent-tasks root for any project  
**Who enforces**: Agent must validate before proceeding

---

## The Check: Verify Single Copy

Before doing ANYTHING with a task file, run this validation:

```bash
cd <agent-tasks>

# 1. Find all copies of the task file
find projects/[PROJECT]/tasks -name "[TASKFILE_NAME]"

# 2. Check YAML status field in each copy found
head -5 [each file path]

# 3. Verify state matches expected location
```

**Expected outputs by task state:**

| Current Status | Expected Location | Copies | Rules |
|---|---|---|---|
| `status: backlog` | `tasks/backlog/[category]/` | **1 only** | File originated here; never duplicate |
| `status: active` | `tasks/active/` | **1 only** | Moved via `git mv` from backlog; previous copy must be deleted |
| `status: completed` | `tasks/completed/YYYY-MM/` | **1 only** | Moved via `git mv` from active; previous copy must be deleted |

---

## Stop Conditions (Report, Do Not Guess)

**STOP immediately and report if:**

- ❌ Multiple copies found in same state (violation of `git mv` discipline)
- ❌ File in wrong folder vs. YAML `status:` field (state corruption)
- ❌ Stale untracked copies in source/intermediate folders
- ❌ Copy exists in both backlog AND active simultaneously
- ❌ Task file lacks YAML status field entirely

**Action when STOP condition found:**
1. Paste the `find` output in chat
2. Explain what state is wrong
3. Do NOT proceed until human confirms cleanup
4. Do NOT use `rm` or `cp`; ask for guidance on which to keep and how to fix

---

## Correct Git Workflow for Task Files

**Rule**: Use `git mv` always. Never `cp`, never plain `mv`.

### Transition: backlog → active

```bash
git add projects/[PROJECT]/tasks/backlog/[category]/[TASKFILE]
git mv projects/[PROJECT]/tasks/backlog/[category]/[TASKFILE] \
        projects/[PROJECT]/tasks/active/[TASKFILE]
# Edit YAML: status: backlog → status: active
git add projects/[PROJECT]/tasks/active/[TASKFILE]
git commit -m "chore: move [TASKFILE] to active (status: backlog → active)"
```

**Verification after commit:**
```bash
find projects/[PROJECT]/tasks -name "[TASKFILE]"
# Expected: projects/[PROJECT]/tasks/active/[TASKFILE] ONLY
```

### Transition: active → completed

```bash
git add projects/[PROJECT]/tasks/active/[TASKFILE]
git mv projects/[PROJECT]/tasks/active/[TASKFILE] \
        projects/[PROJECT]/tasks/completed/YYYY-MM/[TASKFILE]
# Edit YAML: status: active → status: completed
# Fill in Completion Report section
git add projects/[PROJECT]/tasks/completed/YYYY-MM/[TASKFILE]
git commit -m "chore: move [TASKFILE] to completed (filled completion report)"
```

**Verification after commit:**
```bash
find projects/[PROJECT]/tasks -name "[TASKFILE]"
# Expected: projects/[PROJECT]/tasks/completed/YYYY-MM/[TASKFILE] ONLY
```

---

## Why This Matters

**In multi-agent workflows:**
- Stale copies cause ambiguity about which is canonical
- Git history becomes polluted with duplicate commits
- Next agent can't reliably determine task status
- State corruption is silent (no error, just wrong behavior)

**The 30-second pre-flight check catches:**
- Inherited broken state (agent before you left stale copies)
- Accidental `cp` instead of `git mv`
- Task status misalignment (YAML says `active` but file in `backlog/`)
- Multiple copies in same folder

---

## Implementation in Agent Dispatch Interface

Every task file's STEP 0 must include:

```markdown
## STEP 0: Pre-Flight Validation (REQUIRED before reading task details)

Before you proceed, verify the task file state:

**Run:**
```bash
find <agent-tasks>/projects/[PROJECT]/tasks -name "[TASKFILE]"
head -5 [each file found]
```

**Check:**
- How many copies exist? (should be 1)
- What does YAML `status:` say in each?
- Is the file in the expected folder for that status?

**Report findings in chat before proceeding.**

**If state is wrong:** STOP, report the find output, ask human for guidance.
```

---

## Reference: File Lifecycle States

```
creation (human writes backlog task)
    ↓
backlog/[category]/[TASKFILE]
  YAML: status: backlog
  Description: Task awaiting dispatch to executor
    ↓
[agent reviews + readiness check]
    ↓
git mv → active/[TASKFILE]
  YAML: status: active
  Description: Task being actively worked on
    ↓
[agent completes + fills completion report]
    ↓
git mv → completed/YYYY-MM/[TASKFILE]
  YAML: status: completed
  Description: Task finished; closure report filed

At each transition → ONLY ONE COPY should exist in git history
```

---

## Checklist for Agents

- [ ] Run `find` before any task file operation
- [ ] Verify number of copies matches expected state
- [ ] Check YAML `status:` field matches folder location
- [ ] STOP if state is corrupt; report findings
- [ ] Use `git mv` only (never `cp`, never plain `mv`)
- [ ] After git mv + commit: verify `find` shows only new location
- [ ] Update YAML header when changing status
- [ ] Paste `find` output in chat as proof

---

## Incident: Task 2 Lifecycle Artifacts (2026-10-20)

**Date**: 2026-10-20  
**Project**: agent-tasks  
**Task**: 2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION  
**Issue**: Stale untracked lifecycle copies left in `active/` and `backlog/` after task completion

### What Happened

Task 2 completed successfully (moved to `completed/2026-10/` via git mv, committed at 315d7dc). However, during planning-session closeout validation, found:

1. **`projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md`**
   - Untracked (not in git index)
   - Different content (MD5: cb994b00d128f3ffcad6cbc55b61102b vs. authoritative a25f2044...)
   - Timestamp: Oct 20 16:40 (after task was completed at 16:23)
   - Lifecycle violation: Completed task should not remain in `active/`

2. **`projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md`**
   - Untracked (shows `?? ` in `git status`)
   - Identical content to authoritative copy (MD5 match)
   - Leftover from git recovery operation (commit 0223662)
   - Lifecycle violation: Completed task should not remain in `backlog/`

3. **`projects/agent-tasks/tasks/completed/2026-10/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md`**
   - ✅ Tracked by git (in index at commit 315d7dc)
   - ✅ Correct location and state
   - ✅ Authoritative copy

### Root Cause

Likely source: Interactive file operations during implementation session left untracked copies in working directory. Git mv was executed, but local filesystem copies remained. Not committed to git, so not cleaned up by subsequent operations.

### Resolution

Both untracked copies removed during closeout validation. Authoritative copy verified on main branch.

### Prevention Strategy for Future Closeouts

**New Closeout Step — Task Lifecycle Validation**: 

Add to [PLANNING_AGENT_CLOSEOUT.md](../PLANNING_AGENT_CLOSEOUT.md) § "1. Inventory & Pre-Flight Checks":

```bash
## Lifecycle Artifact Check (NEW)

For each completed task from this session:

1. Find all copies:
   find projects/agent-tasks/tasks -name "[TASK_FILE_NAME]"

2. Verify single authoritative copy:
   - Expected: 1 file in completed/YYYY-MM/
   - Check that YAML status: completed matches folder

3. Report:
   - If multiple copies found: STOP, report findings, do not commit
   - If single copy in correct location: confirm in chat ✅
   - If stale untracked copies exist: remove and report cleanup
```

**Acceptance Criteria for Future Planning Sessions**:
- [ ] Before closeout commit: validate each closed task has one authoritative copy
- [ ] No untracked task-file copies in working directory
- [ ] All task files in folder match YAML status field
- [ ] Document findings in closeout handoff § "What Was Checked"

