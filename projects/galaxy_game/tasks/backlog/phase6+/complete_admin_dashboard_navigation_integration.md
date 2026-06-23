---
name: "Complete Admin Dashboard Navigation Integration"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Complete Admin Dashboard Navigation Integration

**Priority**: MEDIUM  
**Phase**: phase6+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Admin dashboard redesign Phase 4 (navigation integration) is marked in-progress but lacks concrete implementation tasks for links, monitoring shortcuts, persistence, and breadcrumb UX.

**Current**: Partial nav integration with unclear implementation details  
**Expected**: Full hierarchical navigation and context persistence across admin flows

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/views/admin/dashboard/index.html.erb` | Update nav links and monitor shortcuts |
| `galaxy_game/app/controllers/admin/*` | Add context handling for galaxy/system selection |
| `galaxy_game/config/routes.rb` | Ensure route support for hierarchical nav |
| `galaxy_game/app/views/admin/shared/*` | Breadcrumb component partials |

## Acceptance Criteria

- [ ] Navigation links follow Galaxy → Star System → Celestial Body hierarchy
- [ ] System-specific monitor links added and functional
- [ ] Selected galaxy/system context persists across page refresh/navigation
- [ ] Breadcrumb navigation present and responsive

## Implementation Steps

1. Update admin dashboard links to hierarchical resource paths
2. Add monitor shortcuts for each system card
3. Persist selected galaxy/system using session or cookie strategy
4. Implement shared breadcrumb component and integrate into admin pages

## Commit Instructions

```bash
git add galaxy_game/app/views/admin/dashboard/index.html.erb galaxy_game/config/routes.rb galaxy_game/app/views/admin/shared/
git commit -m "feat: complete admin dashboard navigation integration for hierarchical monitoring"
```