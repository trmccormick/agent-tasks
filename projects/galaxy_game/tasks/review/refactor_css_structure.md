---
name: "Refactor CSS/SCSS Structure"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor and Organize CSS/SCSS Structure

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

CSS/SCSS structure mixes global, admin, and card-specific styles. Some views import component styles as layout styles, causing confusion and inconsistent styling.

**Current**: Mixed global/admin/card styles in unclear structure  
**Expected**: Separated, modular stylesheet organization

## Acceptance Criteria

- [ ] Global layout styles separated from admin/card-specific
- [ ] Each view imports only relevant stylesheets
- [ ] Shared layout styles in main application stylesheet
- [ ] Admin section styles modular (sidebar, dashboard, etc.)
- [ ] Card styles in own SCSS file, imported where needed
- [ ] No mixing of component and layout styles
- [ ] Documentation updated

## Implementation Steps

1. Audit all SCSS/CSS files for purpose and usage
2. Identify global vs admin vs card-specific styles
3. Refactor SCSS files to appropriate locations
4. Update views to import only necessary stylesheets
5. Test all views for layout and style consistency
6. Update stylesheet structure documentation
7. Validate no regressions in application styling

## Commit Instructions

```bash
git add galaxy_game/app/assets/stylesheets/
git commit -m "refactor: reorganize CSS/SCSS for modularity and consistency"
```