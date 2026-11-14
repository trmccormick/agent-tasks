---
status: active
priority: MEDIUM
type: investigation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## Task: Bootstrap Offcanvas UI Match Investigation
**Status**: ACTIVE  
**Priority**: MEDIUM  
**Created**: 2026-10-06  
**Last Updated**: 2026-10-06

---

## Problem Statement

The admin menu is functionally working, and the remaining mismatch is narrow: the left-side shading does not sit flush against the menu panel. Instead, there is a visible light band between the dark menu background and the shaded overlay area.

The closest working comparison is the original Rails 5 production menu. The fix likely does not require a broad style rewrite. The remaining issue is specifically the shading boundary at the left edge of the menu panel.

This task should focus only on that mismatch and not reopen unrelated styling issues unless they directly affect the left-edge shading behavior.

---

## Why This Task Exists

The previous iterations mostly targeted general menu styling, but the remaining issue is narrower: the left-side shading needs to sit directly against the menu panel without a white gap.

This likely involves one of the following:

- Bootstrap 5 offcanvas border or shadow defaults
- a parent or wrapper background bleeding through
- a border-box/rendering artifact on the left edge
- a mismatch between the menu panel width and the overlay/shade offset

The goal is not a broad visual redesign. The goal is to fix the left-edge shading seam so it matches the original production menu as closely as possible.

---

## Files To Review

- `databases/databases/app/views/admin/_navigation.html.erb`
- `databases/databases/app/assets/stylesheets/interface/elements/_nav.scss`
- `databases/databases/app/views/layouts/admin.html.erb`
- `databases/databases/app/assets/javascripts/plugins/off_canvas.js`
- `databases/databases/app/assets/stylesheets/interface/main.scss`
- `databases/databases/app/assets/javascripts/interface.js`

Also compare with production behavior from the old main-branch implementation and screenshots already captured during this session.

---

## Investigation Questions

1. What part of the Bootstrap 5 offcanvas element is creating the left-side light band?
2. Is the band caused by a border, shadow, or background bleed on the menu panel itself?
3. Is the shading offset wrong, or is the menu panel slightly narrower than the original?
4. Is there a CSS rule on the parent/admin layout that creates the visible seam?
5. Does the issue persist on the dev VM even when local cache is cleared?

Only answer these questions and fix the seam itself; do not reopen unrelated typography or close-button issues unless the seam fix requires one of those values to change.

---

## Proposed Research Steps

1. Inspect the computed styles on the actual offcanvas panel and the left-edge boundary.
2. Determine whether the seam is caused by a border, shadow, box model, or parent background.
3. Compare the width and offset of the offcanvas panel to the dark shading area.
4. Confirm whether the issue is local caching or a real rendering mismatch.
5. Apply the minimal fix that removes the light seam while leaving the rest of the menu untouched.
6. Verify the fix against the production reference before concluding.

---

## Acceptance Criteria

- [ ] Root cause identified for the left-edge shading seam
- [ ] The seam is fixed or a specific structural cause is proven
- [ ] The fix is minimal and limited to the left-edge shading issue, not a broad visual redesign
- [ ] The result is verified against the production reference and documented clearly

---

## Notes / Context from This Session

- Production `main` branch was used as the benchmark.
- The remaining issue is specifically the left-edge shading seam, not the whole menu.
- There is evidence that local cache effects can distort the comparison.
- The dev VM appears closer to the original, so the investigation should compare real rendered styles rather than rely on localhost-only judgment.

---

## Dispatch Notes for Qwen

Use the legacy Rails 5 main branch as the reference and stay focused on the left-edge shading seam. Do not reopen unrelated typography or close-button problems unless they directly affect the seam.

The core task is: determine what in the Bootstrap 5 offcanvas structure or styling is leaving a visible light band between the dark panel and the overlay, and fix only that.
