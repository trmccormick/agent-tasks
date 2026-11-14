---
status: backlog
priority: HIGH
type: research
system_domain: INFRASTRUCTURE | GIT_WORKFLOW
mvp_alignment: MODERNIZATION_PHASE_1_PREREQUISITE
local_worker_safe: true
agent_routing: Local Qwen (Copilot)
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: wvulibraries_acda_portal
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/tasks/backlog/2026-07-15-HIGH-RESEARCH-BRANCH-MERGE-RECONCILIATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_acda_portal/tasks/backlog/2026-07-15-HIGH-RESEARCH-BRANCH-MERGE-RECONCILIATION.md \
         projects/wvulibraries_acda_portal/tasks/active/2026-07-15-HIGH-RESEARCH-BRANCH-MERGE-RECONCILIATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

This is a pure research task. Your job is to:
  1. Audit the two branches and identify all differences
  2. Determine merge strategy (rebase, cherry-pick, manual reconciliation)
  3. List conflicts and dependencies
  4. Document exact steps to align branches

CRITICAL: Save your findings as a markdown report to:
  /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/tasks/summaries/2026-07-15-RESEARCH-BRANCH-MERGE-RECONCILIATION.md

Reference: Task file contains all prerequisites, git commands, and acceptance criteria.
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# RESEARCH: Branch Merge Reconciliation — `fix/bundler-git-gem-caching` into `main`

**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## The Problem

The development environment (`dev_vm`) is running on branch `fix/bundler-git-gem-caching`, which has diverged from `main`. Today's bug fixes (gem version constraints, docker-compose optimization) were committed to `main`, but **cannot auto-merge** into the dev VM branch.

**Blocker**: Cannot proceed with Modernization Phase 1 (PostgreSQL migration) until branches are unified. Need to:
1. Identify all differences between branches
2. Determine whether dev VM branch should be:
   - Rebased onto main (if dev branch is just for testing)
   - Merged into main (if dev branch has production-ready changes)
   - Cherry-picked selectively (if only some changes are needed)
3. Document exact reconciliation steps
4. Verify unified codebase works on both local machine and dev VM

---

## Context: Branches Involved

### `main` (Local Working Branch)
- **Current state**: Contains today's fixes
  - ✅ `blacklight_advanced_search` gem fixed (`~> 7.0`)
  - ✅ `blacklight_range_limit` gem pinned (`~> 8.0`)
  - ✅ docker-compose race condition fixed (bundle install optimization)
  - ✅ Fedora 6 → 7 upgrade (drop-in replacement with `fcrepo/fcrepo:7-tomcat10`)
  - ✅ Asset cache cleanup added to cleanup-dev.sh
  - ✅ Application running locally at localhost:3000
  
- **Gemfile changes**: `hydra/Gemfile` and `hydra/Gemfile.lock` modified
- **docker-compose changes**: Both `docker-compose.yml` and `docker-compose.dev.yml` modified
- **Branch is**: Production-ready, tested locally

### `fix/bundler-git-gem-caching` (Dev VM Branch)
- **Purpose**: Unknown — likely was created to solve git gem caching issue
- **Current state**: Unknown — need to audit
  - What changes are on this branch?
  - When was it created?
  - Does it include today's fixes?
  - Does it have changes NOT on main?
  
- **Branch is**: In active use (dev VM running on it), blocking progress

---

## Research Tasks

### Task 1: Branch Audit — Identify All Differences

**Goal**: Document exactly what's different between the two branches.

**Commands to run** (in `/Users/tam0013/Documents/git/hydra_acda_portal_public`):

```bash
# See branch structure
git branch -a
git log --oneline main | head -20
git log --oneline fix/bundler-git-gem-caching | head -20

# See all commits ONLY on dev branch (not on main)
git log main..fix/bundler-git-gem-caching --oneline

# See all commits ONLY on main (not on dev branch)
git log fix/bundler-git-gem-caching..main --oneline

# Compare file-by-file differences
git diff main fix/bundler-git-gem-caching --name-only

# See specific file changes
git diff main fix/bundler-git-gem-caching -- hydra/Gemfile
git diff main fix/bundler-git-gem-caching -- docker-compose.yml
git diff main fix/bundler-git-gem-caching -- docker-compose.dev.yml
```

**Deliverable**: Document in research summary:
- [ ] Commits unique to `fix/bundler-git-gem-caching`
- [ ] Commits unique to `main`
- [ ] Files modified on each branch
- [ ] Specific changes in each modified file (Gemfile, docker-compose, etc.)
- [ ] Any merge conflicts that would occur

---

### Task 2: Determine Merge Strategy

**Goal**: Decide how to reconcile branches based on audit findings.

**Options to evaluate**:

1. **Rebase dev branch onto main** (if dev branch is just exploratory):
   ```bash
   git checkout fix/bundler-git-gem-caching
   git rebase main
   # Fix any conflicts
   ```
   ✅ Pros: Clean history, no merge commit, dev branch inherits main's fixes
   ❌ Cons: Rewrites dev branch history (breaks if others are using it)

2. **Merge main into dev branch** (if dev branch has production-ready changes):
   ```bash
   git checkout fix/bundler-git-gem-caching
   git merge main
   # Fix any conflicts
   ```
   ✅ Pros: Preserves both histories, safe if branch is shared
   ❌ Cons: Creates merge commit, less clean history

3. **Cherry-pick specific commits** (if only some changes from dev branch are needed):
   ```bash
   git checkout main
   git cherry-pick <commit-hash>  # For each dev branch commit needed
   ```
   ✅ Pros: Surgical control, only desired changes
   ❌ Cons: Manual, error-prone, doesn't preserve intent

4. **Discard dev branch, start from main** (if dev branch is obsolete):
   ```bash
   git checkout main
   # Delete dev branch
   git branch -D fix/bundler-git-gem-caching
   ```
   ✅ Pros: Simplest, eliminates divergence entirely
   ❌ Cons: Loses any unique work on dev branch

**Deliverable**: Document in research summary:
- [ ] Recommended strategy with justification
- [ ] Why other strategies were rejected
- [ ] Specific commands needed to execute recommended strategy
- [ ] Expected conflicts and how to resolve them

---

### Task 3: Conflict Analysis

**Goal**: Identify which files would conflict during merge/rebase.

**Commands to run**:

```bash
# Simulate merge without actually executing it
git merge --no-commit --no-ff fix/bundler-git-gem-caching

# If conflicts exist, show them
git status

# Abort the test merge
git merge --abort

# For each file likely to conflict, show the diff
git diff main fix/bundler-git-gem-caching -- hydra/Gemfile
git diff main fix/bundler-git-gem-caching -- docker-compose.yml
git diff main fix/bundler-git-gem-caching -- docker-compose.dev.yml
```

**Deliverable**: Document in research summary:
- [ ] List of files with conflicts
- [ ] Nature of each conflict (gem versions, docker config, etc.)
- [ ] How each conflict can be resolved
- [ ] Whether conflicts are resolvable or require manual intervention

---

### Task 4: Verification Plan

**Goal**: Define how to verify that branch reconciliation is successful.

**Verification steps to document**:

```bash
# After reconciliation (rebase/merge/cherry-pick):

# 1. Verify branch is clean
git status  # Should show "working tree clean"

# 2. Verify all commits from main are on reconciled branch
git log --oneline | grep "blacklight_advanced_search\|blacklight_range_limit\|docker-compose\|Fedora"

# 3. Test application startup
cd hydra
bundle install
# Then on dev VM:
cd hydra
bundle install

# 4. Verify docker-compose works
docker-compose -f docker-compose.dev.yml up --no-build
# Check that app, workers, and infrastructure all start correctly

# 5. Verify application loads
curl http://localhost:3000
# Should return HTML, not error

# 6. Verify Gemfile locks match
diff hydra/Gemfile.lock <(git show main:hydra/Gemfile.lock)
# Should show no differences
```

**Deliverable**: Document in research summary:
- [ ] Step-by-step verification checklist
- [ ] Expected outputs for each verification step
- [ ] What to do if verification fails
- [ ] Roll-back procedure if reconciliation breaks something

---

### Task 5: Documentation & Handoff

**Goal**: Create clear, actionable reconciliation guide for implementation.

**Deliverable**: Complete research summary should include:

1. **Branch Audit** (from Task 1):
   - All commits on each branch
   - All file differences
   - Merge conflict summary

2. **Recommended Strategy** (from Task 2):
   - Which approach: rebase / merge / cherry-pick / discard
   - Justification for choice
   - Why other approaches were rejected

3. **Conflict Resolution** (from Task 3):
   - Exact conflicts to expect
   - How to resolve each conflict
   - Decision points for user

4. **Verification Checklist** (from Task 4):
   - Exact commands to run after reconciliation
   - Expected outputs
   - How to detect if reconciliation succeeded

5. **Implementation Handoff**:
   - Concise steps for implementation agent
   - Critical warnings ("stop if X", "don't do Y")
   - Rollback procedure if something goes wrong

---

## Success Criteria

- [ ] All branch differences audited and documented
- [ ] Merge strategy chosen with clear rationale
- [ ] All conflicts identified and resolution documented
- [ ] Verification plan is executable and comprehensive
- [ ] Implementation agent can reconcile branches following the guide without clarification
- [ ] After reconciliation:
  - [ ] Both `main` and `fix/bundler-git-gem-caching` have same commits (or one is deleted)
  - [ ] Application builds and starts locally
  - [ ] Application builds and starts on dev VM
  - [ ] No dangling or stale branches remain
  - [ ] `git log` shows clean history with no conflicting changes

---

## Context Files & References

- **Main branch changes** (today's work):
  - `hydra/Gemfile` — gem version constraints fixed
  - `docker-compose.yml` — race condition fixed
  - `docker-compose.dev.yml` — race condition fixed
  - `scripts/cleanup-dev.sh` — asset cache cleanup added

- **Dev VM info**:
  - Running on: `fix/bundler-git-gem-caching` branch
  - Purpose of branch: Originally for git gem caching fix (unclear if complete)
  - Status: Diverged from main, blocking progress

- **Dependency**:
  - Must complete this research BEFORE Phase 1 modernization begins
  - Phase 1 (PostgreSQL migration) cannot proceed with split branches

---

## Notes for Implementation Agent

After this research completes:

1. A new task will be created: **IMPLEMENT: Branch Reconciliation**
   - Will use this research summary as prerequisites
   - Will execute reconciliation steps
   - Will verify success and report results

2. No work should begin on Modernization Phase 1 until:
   - [ ] Branches are unified
   - [ ] All verification steps pass
   - [ ] Implementation agent confirms success

3. If reconciliation fails at any step:
   - Roll back (see rollback procedure in research summary)
   - Report failure to user
   - Do NOT force-push or discard commits without explicit approval

---

## Research Report Template (for agent to fill)

When saving findings, use this structure in the research summary markdown:

```markdown
# Branch Merge Reconciliation Research Report
**Date**: 2026-07-15
**Status**: [READY FOR IMPLEMENTATION | BLOCKED BY X | REQUIRES USER INPUT]

## Branch Audit
### Commits Unique to main (not on dev branch)
[List from git log main..fix/bundler-git-gem-caching]

### Commits Unique to fix/bundler-git-gem-caching (not on main)
[List from git log fix/bundler-git-gem-caching..main]

### Files Modified (file-by-file summary)
[From git diff main fix/bundler-git-gem-caching --name-only]

## Recommended Reconciliation Strategy
[Option chosen: rebase/merge/cherry-pick/discard]
[Justification]
[Why other options rejected]

## Conflict Analysis
[List conflicts and resolution approach]

## Verification Checklist
[Exact steps and expected outputs]

## Implementation Handoff
[Commands for implementation agent to run in sequence]
```
