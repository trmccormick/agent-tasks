---
name: "Docs Agent Directory Cleanup"
priority: LOW
phase: phase8+
created: 2026-06-21
status: backlog
type: documentation
---

# TASK: docs/agent Directory Cleanup

**Priority**: LOW  
**Phase**: phase8+  
**Type**: documentation  
**Created**: 2026-06-21  

## Problem Statement

The docs/agent directory contains legacy files, misplaced assets, and orphaned documents from previous sessions. This creates routing confusion for agents and causes outdated references to be used.

**Current**: Cluttered directory with superseded files at root  
**Expected**: Clean directory with active files only at root and archived files moved appropriately

## Files to Edit

| File/Path | Purpose |
|---|---|
| `docs/agent/` | Move/mkdir/rm cleanup operations |

## Acceptance Criteria

- [ ] Misplaced root-level files moved to correct subdirectories
- [ ] Superseded files archived (including RULES.md)
- [ ] Session handoff folder created and populated
- [ ] outputs/completed directory cleanup performed per plan
- [ ] No code files modified

## Implementation Steps

1. Move misplaced root files to target folders (`image-generation`, `planning`, `archive`, `rules`)
2. Archive superseded `RULES.md`
3. Create `docs/agent/tasks/session-handoffs/` if missing
4. Remove or consolidate obsolete `outputs/` and root `completed/` structures
5. Verify resulting directory layout and links

## Commit Instructions

```bash
git add docs/agent/
git commit -m "docs: clean up docs/agent directory structure and archive legacy files"
```