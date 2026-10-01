# Task: Fix Admin Offcanvas Overlay Viewport Coverage

**Date Created:** 2026-10-01  
**Priority:** MEDIUM (visual polish, not blocking)  
**Status:** NEEDS_QWEN_PLANNING  
**Estimated:** 2-4 hours  

## Problem Statement

Admin offcanvas menu opens correctly and is fully functional, but the dark overlay backdrop doesn't cover the entire viewport. Light/gray areas remain visible around the edges.

**Current (dev/localhost):** ~60% viewport coverage, white areas on left and bottom  
**Expected (production):** 100% viewport coverage, entire page darkened except menu panel  

## Root Cause

Hiraku library applies `transform: translateX(-300px)` to body when menu opens. This creates a new CSS stacking context that breaks:
- `position: fixed` elements (become relative to transformed body, not viewport)
- `position: absolute` elements (only cover body element, not full viewport)
- Simple dimension/offset compensations

User attempted JavaScript solution with expanded overlay dims + negative offsets, but it's still incomplete.

## What's Been Tried

✅ CSS-only approaches (failed due to transform stacking context)  
✅ JavaScript overlay with dimension compensation (not working - still gaps)  
❌ Need proper debugging of JS implementation

## Files Involved

- `databases/app/assets/javascripts/plugins/off_canvas.js` - overlay creation
- `databases/app/assets/stylesheets/interface/elements/_nav.scss` - overlay styling
- HTML: `<nav class="offcanvas-left"> ... </nav><div class="js-hiraku-offcanvas"></div>`

## Acceptance Criteria

- [ ] Overlay covers 100% of viewport width when menu open
- [ ] Overlay covers 100% of viewport height when menu open
- [ ] No light/white areas visible at viewport edges
- [ ] Overlay disappears completely when menu closes
- [ ] Clicking overlay closes menu (if that's a feature)
- [ ] Verified on dev VM with production parity test

## Testing Setup

- Dev: `https://databases.lib.wvu.edu/admin` (has SSO, can test immediately)
- Local: `http://localhost:3000/admin` (requires SSO workaround or auth bypass)
- Compare with production site screenshot for exact overlay behavior

## Key Questions for Planning

1. Should overlay be created outside the body transform (different DOM location)?
2. Is the MutationObserver approach the right pattern?
3. What's the exact viewport coverage formula needed?
4. Is there a Hiraku API or config we should be using instead?
5. Should this use a different CSS approach (containment, pseudo-elements)?

## Handoff Notes

User has implemented partial solution in off_canvas.js but it's not fully working. Need Qwen to:
1. Review the current JS implementation
2. Debug why coverage is incomplete
3. Test on dev VM where auth works
4. Implement proper fix with full viewport coverage

Branch: `rails7-circleci-test`  
Commit with user's attempt: (pending commit)
