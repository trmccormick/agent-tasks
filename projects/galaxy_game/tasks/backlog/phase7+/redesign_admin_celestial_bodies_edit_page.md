---
name: "Redesign Admin Celestial Bodies Edit Page"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Redesign Admin Celestial Bodies Edit Page

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Admin edit page mixes property editing with complex AI terrain generation features. Should be simplified to focus on basic properties with optional terrain regeneration and clearer navigation.

**Current**: Overcomplicated with manual terrain generation and AI map selection  
**Expected**: Simple property editing with optional regeneration and clear feature access

## Acceptance Criteria

- [ ] Edit page simplified to name/alias editing
- [ ] AI map selection and generation UI removed
- [ ] Complex terrain analysis features removed
- [ ] "Regenerate Terrain" button added with confirmation
- [ ] "Map Studio" button for terrain editing
- [ ] "Monitor" button for viewing current terrain
- [ ] Automatic GeoTIFF or procedural generation
- [ ] Clear, intuitive button layout

## Implementation Steps

1. Simplify edit form to essential properties
2. Remove AI map selection UI
3. Remove complex terrain analysis features
4. Add "Regenerate Terrain" button with confirmation
5. Add "Map Studio" and "Monitor" navigation buttons
6. Implement automatic terrain loading
7. Test all edit page functionality
8. Validate button visibility and behavior

## Commit Instructions

```bash
git add galaxy_game/app/views/admin/celestial_bodies/ galaxy_game/app/controllers/admin/celestial_bodies_controller.rb
git commit -m "feat: simplify admin celestial body edit page with clear terrain controls"
```