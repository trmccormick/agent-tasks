---
name: "AI Manager SCSS and 2-Pane Layout"
priority: HIGH
phase: phase6+
created: 2026-06-21
status: backlog
type: frontend
---

# TASK: AI Manager SCSS File and 2-Pane Layout Standardization

**Priority**: HIGH  
**Phase**: phase6+  
**Type**: frontend/layout  
**Created**: 2026-06-21  

## Problem Statement

AI Manager pages use inconsistent layout patterns, including 3-pane structures with unused panes. Styling is fragmented and lacks a dedicated AI Manager stylesheet.

**Current**: Mixed layouts and styling across AI manager pages  
**Expected**: Shared neon-blue theme and consistent 2-pane layout across all AI manager pages

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/assets/stylesheets/admin/_ai_manager.scss` | New shared stylesheet |
| `galaxy_game/app/assets/stylesheets/admin/dashboard.scss` | Import `_ai_manager.scss` |
| `galaxy_game/app/views/admin/ai_manager/*.html.erb` | Apply 2-pane layout classes |
| `galaxy_game/app/views/admin/ai_manager/testing/*.html.erb` | Apply 2-pane layout classes |

## Acceptance Criteria

- [ ] `_ai_manager.scss` created and imported
- [ ] All targeted AI Manager pages use consistent 2-pane layout
- [ ] Unused third pane removed from layouts
- [ ] No controller or business logic changes introduced

## Commit Instructions

```bash
git add galaxy_game/app/assets/stylesheets/admin/_ai_manager.scss galaxy_game/app/assets/stylesheets/admin/dashboard.scss galaxy_game/app/views/admin/ai_manager/
git commit -m "style: standardize AI Manager pages with shared SCSS and 2-pane layout"
```