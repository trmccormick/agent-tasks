---
status: backlog
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
cross_project_impact: false
---

# TASK: Audit and Remove Legacy Agent Repository Concept Folder

**Status**: ACTIVE
**Priority**: MEDIUM
**Type**: documentation (cleanup)
**Created**: 2026-07-03
**Last Updated**: 2026-07-03

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxyGame
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-07-03-MEDIUM-DOCUMENTATION-CLEANUP-LEGACY-AGENT-REPO.md

READ FIRST: Task file contains all prerequisites, verification steps, and acceptance criteria.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

## Context

The galaxyGame repository contains a legacy agent repository concept folder at `docs/agent/` that was created during initial exploration. The new agent-tasks repository (at `/Users/tam0013/Documents/git/agent-tasks/`) is now the authoritative system for all agent-related documentation, task workflows, and operational rules.

This folder needs to be audited to confirm:
1. All content has been migrated to agent-tasks or is truly obsolete
2. No other parts of galaxyGame depend on it
3. No broken references will result from deletion

After verification, the legacy folder should be removed to eliminate confusion and maintain single source of truth.

---

## Critical Information for This Task

### What You're Auditing
| Item | Location | Purpose |
|---|---|---|
| Legacy agent repo | `/Users/tam0013/Documents/git/galaxyGame/docs/agent/` | Original concept folder (now obsolete) |
| WORKFLOW_README.md | `docs/agent/WORKFLOW_README.md` | **PRIORITY**: Project-specific workflow cheat sheet (RSpec commands, session patterns, agent handoff process) |
| New agent repo | `/Users/tam0013/Documents/git/agent-tasks/` | Authoritative system (on host filesystem) |
| New local copy | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/` | Mirror of agent-tasks (reference only) |

### Architecture Gotcha

⚠️ **GOTCHA**: Do NOT confuse these three folders:
- ❌ Wrong: Leaving `docs/agent/` in place because you think it's the current system
- ✅ Right: Verify `docs/new_agent/` contains everything needed, then delete `docs/agent/`
- Why: Single source of truth is `/Users/tam0013/Documents/git/agent-tasks/` on host. `docs/new_agent/` is a read-only mirror. `docs/agent/` is legacy exploration and must be removed.

⚠️ **PRIORITY FILE**: `docs/agent/WORKFLOW_README.md` contains project-specific workflow cheat sheet:
- ❌ Wrong: Delete WORKFLOW_README.md assuming it's generic or migrated
- ✅ Right: Identify WORKFLOW_README.md as **galaxy-game-specific content** that MUST be migrated
- Why: Contains RSpec command patterns, session handoff workflows, docker-compose guidance, agent routing notes, and development practices specific to this project. This is a **living document** that changes as workflow evolves. Check if newer version exists in docs/new_agent/ before proceeding.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or examining any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template**:
```
## STATUS SYNTHESIS REPORT

**Task**: Audit and Remove Legacy Agent Repository Concept Folder
**Status**: active → in-progress
**Date**: 2026-07-03

### What I'm About to Do
Audit the legacy docs/agent/ folder, verify all content has been migrated or is obsolete, 
confirm no references exist elsewhere in galaxyGame, then remove the folder via git rm.

### Files I'll Examine
| File/Folder | Purpose |
|---|---|
| `/Users/tam0013/Documents/git/galaxyGame/docs/agent/` | Legacy repo (to be audited) |
| `/Users/tam0013/Documents/git/galaxyGame/docs/agent/WORKFLOW_README.md` | **PRIORITY** — project-specific workflow cheat sheet (check if migrated) |
| `/Users/tam0013/Documents/git/galaxyGame/docs/agent/archive/` | Archive folder (if exists) with backlog/completed tasks |
| `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/` | New system (reference) — check if WORKFLOW_README already exists here |
| `/Users/tam0013/Documents/git/galaxyGame/` | Full repo (grep for references) |

### Prerequisites Completed
- ✅ Understand difference between docs/agent/, docs/new_agent/, and /agent-tasks/
- ✅ Know that agent-tasks is authoritative on host
- ✅ Understand this is cleanup, not feature work

### Expected Outcomes
1. List all files in docs/agent/
2. Verify each is either migrated to agent-tasks or truly obsolete
3. Search galaxyGame for any references to docs/agent/
4. If all clear: git rm -rf docs/agent/ and commit
5. Verify deletion in git log

### Critical Gotchas I Will Avoid
- ❌ Delete docs/agent/ without checking if content was migrated
- ❌ Confuse docs/agent/ with docs/new_agent/ (different purposes)
- ❌ Leave broken references after deletion

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The legacy `docs/agent/` folder was the initial concept repository for agent workflow documentation. The new `agent-tasks` repository (external, on host filesystem) is now the authoritative system for all agent operations, rules, and task workflows across all projects (galaxyGame, Samvera, WVU Libraries).

However, `docs/agent/` may contain:
1. **Galaxy-game-specific information** that needs to be preserved and migrated to `docs/new_agent/`
2. **Backlog task files** in an archive folder that may need to be migrated to the new task system
3. **Completed work** that should be preserved in the new repo for historical reference

**Current state**: `docs/agent/` still exists with potentially valuable content that hasn't been fully migrated.
**Desired state**: All galaxy-game-specific content moved to `docs/new_agent/`, archival backlog audited for task migration, completed work preserved, then `docs/agent/` is removed.

---

## Files Involved

### Primary (will be deleted)
| File | Purpose |
|---|---|
| `docs/agent/` | Legacy agent repository concept (entire directory) |

### Reference (do not edit)
| File | Purpose |
|---|---|
| `docs/new_agent/` | Authoritative local mirror of `/Users/tam0013/Documents/git/agent-tasks/` |
| `.git/` | Repository metadata (verify deletion via git log) |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: You must have completed and posted your STATUS SYNTHESIS REPORT above.
> Do not proceed with any of these steps until synthesis is posted and approved.

### Phase 1: Audit and Document Legacy Content

#### Step 1.1 — Inventory docs/agent/ structure

Create a complete directory tree:
```bash
find /Users/tam0013/Documents/git/galaxyGame/docs/agent -type f -name "*.md" -o -name "*.json" -o -name "*.yml" | sort
```

Document in synthesis report:
- File count by category
- Any galaxy-game-specific content (not generic agent rules)
- Any task files or backlog references

#### Step 1.2 — Audit docs/agent/archive/ folder (if it exists)

```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/docs/agent/archive/ 2>/dev/null || echo "No archive folder found"
find /Users/tam0013/Documents/git/galaxyGame/docs/agent/archive -type f 2>/dev/null | head -20
```

Document:
- What's in the archive (backlog files, completed work, etc.)
- Which files appear to be galaxy-game-specific task backlog
- Dates and status of archived items

#### Step 1.3 — PRIORITY: Check WORKFLOW_README.md status

```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/docs/agent/WORKFLOW_README.md 2>/dev/null && echo "FOUND" || echo "NOT FOUND"
```

If WORKFLOW_README.md exists:
1. Compare with any version in docs/new_agent/:
   ```bash
   ls /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/ | grep -i workflow
   ```
2. Document in synthesis:
   - Date last modified
   - Key content (RSpec commands, session workflows, handoff patterns, docker guidance)
   - Whether a newer version exists in new repo

**This file MUST be migrated if it contains information not duplicated elsewhere.**

### Phase 2: Identify Content That Needs Migration

#### Step 2.1 — Categorize content

For each file in docs/agent/, classify as:
- **Generic agent rules** → already migrated to agent-tasks/rules/GUARDRAILS.md
- **Galaxy-game-specific content** → needs to be moved to docs/new_agent/projects/galaxy_game/
- **Task backlog files** → needs audit for migration to tasks/backlog/ in new repo
- **Completed work** → should be moved to tasks/completed/[YYYY-MM]/ in new repo
- **Truly obsolete** → safe to delete

Create a table in your synthesis report mapping each file to its category and disposition.

#### Step 2.2 — Verify migration targets exist

For galaxy-game-specific content, verify destination folders in docs/new_agent/:
```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/
```

### Phase 3: Execute Content Migration (if needed)

> Do not proceed with this phase unless synthesis report approves each migration.

#### Step 3.0 — PRIORITY: Migrate WORKFLOW_README.md

If WORKFLOW_README.md exists and newer version NOT found in docs/new_agent/:
```bash
cp /Users/tam0013/Documents/git/galaxyGame/docs/agent/WORKFLOW_README.md /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/WORKFLOW_README.md
```

Document in completion report:
- File migrated successfully ✅
- Mark as "living document — requires regular updates as workflow evolves"
- Note any updates made during audit (if procedures changed)

If newer version ALREADY exists in docs/new_agent/:
- Keep the newer version
- Document in completion report that legacy version was superseded
- Mark legacy version safe to delete

If galaxy-game-specific files are found in docs/agent/:
```bash
# Example: if docs/agent/ has galaxy_game_specific_rules.md
cp /Users/tam0013/Documents/git/galaxyGame/docs/agent/galaxy_game_specific_rules.md /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/[appropriate folder]/
```

For each migrated file:
- Verify it copied successfully
- Update any internal links/references to point to new location
- Document in your completion report

#### Step 3.2 — Migrate backlog task files

If docs/agent/archive/ contains backlog task files:
```bash
# Copy valid backlog files to new repo
cp /Users/tam0013/Documents/git/galaxyGame/docs/agent/archive/[backlog-file].md /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/[YYYY-MM]/
```

Ensure files follow naming convention: `YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTION.md`

#### Step 3.3 — Preserve completed work

If docs/agent/archive/ contains completed task documentation:
```bash
# Copy to completed tasks folder
cp /Users/tam0013/Documents/git/galaxyGame/docs/agent/archive/[completed-file].md /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/completed/[YYYY-MM]/
```

### Phase 4: Verify References

#### Step 4.1 — Search for all references to docs/agent/

```bash
cd /Users/tam0013/Documents/git/galaxyGame && grep -r "docs/agent" --include="*.md" --include="*.rb" --include="*.yml" . 2>/dev/null | grep -v node_modules | grep -v ".git" | grep -v ".OLD" | grep -v "archive"
```

If you find active references, **STOP and report to user** — do not delete if files are still referenced.

#### Step 4.2 — Check for any code imports

```bash
cd /Users/tam0013/Documents/git/galaxyGame && grep -r "from.*docs/agent\|require.*docs/agent\|include.*docs/agent" --include="*.rb" --include="*.js" . 2>/dev/null
```

If found, **STOP and report to user**.

### Phase 5: Clean Up and Remove Legacy Folder

> Only proceed after all migrations complete and all approval gates pass.

#### Step 5.1 — Git add migrated files (if any)

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add docs/new_agent/projects/galaxy_game/tasks/backlog/[YYYY-MM]/*
git add docs/new_agent/projects/galaxy_game/tasks/completed/[YYYY-MM]/*
git add docs/new_agent/projects/galaxy_game/[other-migrated-folders]/*
```

#### Step 5.2 — Remove legacy folder

```bash
git rm -r docs/agent/
```

#### Step 5.3 — Single commit

```bash
git commit -m "chore: migrate legacy docs/agent/ content to new repo and remove obsolete folder

MIGRATION SUMMARY:
- [X files] moved to docs/new_agent/projects/galaxy_game/
- [Y files] moved to tasks/backlog/
- [Z files] moved to tasks/completed/
- Removed docs/agent/ (legacy)

All galaxy-game-specific content preserved in new repo structure."
```

#### Step 5.4 — Verify deletion

```bash
git log --oneline -3
ls /Users/tam0013/Documents/git/galaxyGame/docs/agent 2>&1
```

Second command should show: "No such file or directory"

---

## Acceptance Criteria
- [ ] Phase 1 — Complete inventory of docs/agent/ contents documented
- [ ] Phase 1 — Archive folder audited and its contents documented
- [ ] Phase 2 — All files categorized (generic | galaxy-specific | backlog | completed | obsolete)
- [ ] Phase 2 — Migration target destinations verified in docs/new_agent/
- [ ] Phase 3 — All galaxy-game-specific content migrated to docs/new_agent/ (if any found)
- [ ] Phase 3 — All valid backlog files migrated to tasks/backlog/ (if any found)
- [ ] Phase 3 — All completed work migrated to tasks/completed/ (if any found)
- [ ] Phase 4 — Search for references completed — zero active references found
- [ ] Phase 4 — Search for imports completed — zero code imports found
- [ ] Phase 5 — `git rm -r docs/agent/` executed
- [ ] Phase 5 — Commit message includes migration summary
- [ ] Phase 5 — Verification confirms folder is removed from working directory and git history shows deletion

---

## Stop Conditions — escalate to user immediately if:
- Galaxy-game-specific content found in docs/agent/ that is NOT already in docs/new_agent/
- WORKFLOW_README.md exists but a newer version is NOT found in docs/new_agent/ (must be migrated)
- Backlog task files in archive folder that appear valid but don't match new naming convention
- Active references found to docs/agent/ in code or docs
- Content found in docs/agent/ that cannot be categorized (uncertain disposition)
- Archive folder contains completed work marked as important but not documented
- Any file in legacy folder has no clear migration path

---

## Documentation
- [ ] No doc changes needed — this IS the cleanup of documentation

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: 2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md (now completed)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### Phase 1 — Audit Results
- Total files in docs/agent/: [X]
- Archive folder exists: [yes/no]
- Archive folder file count: [X or N/A]
- **WORKFLOW_README.md found**: [yes/no]
- **WORKFLOW_README.md newer version exists in docs/new_agent/**: [yes/no]
- **WORKFLOW_README.md migration decision**: [migrated | superseded by newer version | error during audit]

### Phase 2 — Content Categorization
| Category | Count | Files | Disposition |
|---|---|---|---|
| Generic agent rules | X | [list] | Already in agent-tasks |
| Galaxy-game-specific | X | [list] | Migrated to docs/new_agent/ |
| Backlog tasks | X | [list] | Migrated to tasks/backlog/ |
| Completed work | X | [list] | Migrated to tasks/completed/ |
| Obsolete | X | [list] | Deleted |

### Phase 3 — Migration Execution
- Files migrated: [X] (galaxy-specific + backlog + completed)
- Verification checks passed: [yes/no]
- Issues encountered: [none | describe]

### Phase 4 — Reference Verification
- Active references to docs/agent/: [zero | X references in: [files]]
- Code imports from docs/agent/: [zero | X imports in: [files]]

### Phase 5 — Legacy Folder Removal
- `git rm -r docs/agent/`: [success]
- Commit hash: [commit]
- Verification: Folder confirmed deleted ✅

### Summary
- Total content preserved: [X] files migrated to new repo
- Total content deleted: [Y] obsolete files
- References check: ✅ All clear
- Final status: `docs/agent/` successfully removed, all galaxy-game content migrated to new repo

### Follow-up tasks needed
[Any new backlog items identified during audit — or "none"]

---

## Handoff Summary

HANDOFF SUMMARY: 5-phase audit + migration | Inventory docs/agent/ and archive/ | Categorize content (generic vs galaxy-specific vs tasks vs obsolete) | Migrate galaxy-game-specific content to docs/new_agent/ | Migrate valid backlog/completed tasks to new task system | Verify no active references | Remove docs/agent/ via git rm | One comprehensive commit with detailed summary
