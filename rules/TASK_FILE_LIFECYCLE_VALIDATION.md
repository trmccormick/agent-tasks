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
cd /Users/tam0013/Documents/git/agent-tasks

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
find /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/tasks -name "[TASKFILE]"
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

