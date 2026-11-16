[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/add_admin_celestial_body_show_view.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Add Admin Celestial Body Show View"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Add Admin Show View for Celestial Bodies

**Priority**: MEDIUM  
**Phase**: phase6+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

In admin simulation, the "View" link routes to the public celestial body page instead of an admin page with controls and diagnostics.

**Current**: `celestial_body_path(body)` points to public route  
**Expected**: Admin simulation links to `admin_celestial_body_path(body)` with admin show page and controls

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/config/routes.rb` | Add admin show route |
| `galaxy_game/app/controllers/admin/celestial_bodies_controller.rb` | Add `show` action |
| `galaxy_game/app/views/admin/celestial_bodies/show.html.erb` | New admin show view |
| `galaxy_game/app/views/admin/simulation/index.html.erb` | Update View link |

## Acceptance Criteria

- [ ] "View" link on admin simulation opens admin celestial body show
- [ ] Admin show displays full body + sphere + settlement/context data
- [ ] No routing errors
- [ ] Interface consistent with admin monitor patterns

## Implementation Steps

1. Add admin `show` route under `resources :celestial_bodies`
2. Implement `show` action loading sphere associations
3. Create admin show template with controls and links to monitor/surface tools
4. Switch simulation list link to admin path

## Commit Instructions

```bash
git add galaxy_game/config/routes.rb galaxy_game/app/controllers/admin/celestial_bodies_controller.rb galaxy_game/app/views/admin/celestial_bodies/show.html.erb galaxy_game/app/views/admin/simulation/index.html.erb
git commit -m "feat: add admin celestial body show page and simulation link routing"
```