---
name: "Fix Patterns and Decisions Pages"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: frontend
---

# TASK: Fix Patterns Page and Decisions Page Cleanup

**Priority**: MEDIUM  
**Phase**: phase6+  
**Type**: frontend/view  
**Created**: 2026-06-21  

## Problem Statement

Patterns page includes non-functional JS alert controls and hardcoded options. Decisions page stat cards are not driven by real decision data.

**Current**: Mock interactions and static counts  
**Expected**: Form-based pattern actions + decisions stats bound to controller data

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/controllers/admin/ai_manager_controller.rb` | Update `patterns` action data |
| `galaxy_game/app/views/admin/ai_manager/patterns.html.erb` | Replace alert buttons with form submission |
| `galaxy_game/app/views/admin/ai_manager/decisions.html.erb` | Bind stat cards to `@decisions` data |

## Acceptance Criteria

- [ ] Patterns page loads available patterns from controller
- [ ] Pattern actions use real route/form submission (no alert placeholders)
- [ ] Decisions page stat cards reflect actual counts from loaded records
- [ ] Layout classes aligned with AI Manager shared layout

## Commit Instructions

```bash
git add galaxy_game/app/controllers/admin/ai_manager_controller.rb galaxy_game/app/views/admin/ai_manager/patterns.html.erb galaxy_game/app/views/admin/ai_manager/decisions.html.erb
git commit -m "fix: wire AI Manager patterns and decisions pages to real data/actions"
```