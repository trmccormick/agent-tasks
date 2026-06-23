---
name: "Fix AI Mission Control Section in Monitor"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Fix AI Mission Control Section in Celestial Bodies Monitor

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

Admin monitor page has misplaced AI mission control elements: general buttons mixed with AI testing tools, duplicates between sections, and non-functional AI test buttons with no JavaScript handlers.

**Current**: Confused UI organization with duplicates and missing handlers  
**Expected**: Clean separation with functional AI test tools

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/views/admin/celestial_bodies/monitor.html.erb` | Remove AI MISSION CONTROL section |
| `galaxy_game/app/views/admin/simulation/index.html.erb` | Move AI test buttons |
| `galaxy_game/app/assets/javascripts/monitor.js` | Remove unused functions |

## Acceptance Criteria

- [ ] "View Public Page" and "Edit" removed from AI MISSION CONTROL
- [ ] AI test buttons moved to simulation page
- [ ] Entire AI MISSION CONTROL section removed from monitor
- [ ] No duplicate buttons between sections
- [ ] AI test buttons functional in new location
- [ ] Admin navigation still works

## Implementation Steps

1. Remove general navigation buttons from AI MISSION CONTROL
2. Move AI test buttons to /admin/simulation page
3. Add buttons to TESTING TOOLS section
4. Remove entire AI MISSION CONTROL section
5. Clean up monitor.js
6. Test all admin workflows
7. Verify UI organization

## Commit Instructions

```bash
git add galaxy_game/app/views/admin/ galaxy_game/app/assets/javascripts/monitor.js
git commit -m "fix: reorganize AI testing controls from monitor to simulation page"
```