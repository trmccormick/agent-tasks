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

The admin menu is functionally working, but the visual styling still does not match the original Rails 5 production version. The current Bootstrap 5 offcanvas is still off in several areas:

- close button is lifted or mispositioned
- the red circle is not visually centered around the X
- a white seam / band remains visible
- menu text and header proportions are not aligned with the original

We have already compared against the legacy code in the production `main` branch and restored the classic layout patterns, but the mismatch remains. This indicates a deeper rendering mismatch in the Bootstrap 5 offcanvas implementation rather than a simple CSS value issue.

---

## Why This Task Exists

The previous iterations attempted CSS-only fixes based on the legacy Rails 5 markup and styling. Those fixes did not resolve the remaining mismatch. The issue likely involves a more subtle interaction between:

- Bootstrap 5 offcanvas internals
- surrounding app layout / transformed container behavior
- browser-rendered box model differences
- default Bootstrap padding, border, and flex rules

The goal is to continue the investigation and determine whether the mismatch is caused by markup structure, the offcanvas container, CSS variables, or a rendering artifact that requires a different implementation approach.

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

1. Is the offcanvas header using the same box model as the legacy production menu?
2. Is the white seam caused by Bootstrap offcanvas border, box-shadow, or a leftover parent/container background?
3. Is the close button being affected by context-specific absolute positioning or the offcanvas header flex defaults?
4. Are the menu typography values differing because of Bootstrap defaults or because the layout is not using the same DOM structure?
5. Is there another CSS rule elsewhere in the Rails 7 app overriding the intended menu styles?
6. Does the mismatch persist only in the browser, or also in a clean local render without cached CSS?

---

## Proposed Research Steps

1. Compare the live DOM and computed styles of the actual offcanvas element against the original production page.
2. Inspect whether any Bootstrap default variable or class is still forcing padding, border, or flex behavior.
3. Check if the issue is tied to the parent admin layout or wrapper elements around the offcanvas.
4. Review whether the close button is being affected by `position: relative` container behavior or the header flex alignment.
5. Test a minimal alternative markup path that more closely mirrors the legacy production structure while still using Bootstrap 5 offcanvas.
6. Document the actual root cause before any additional code changes.

---

## Acceptance Criteria

- [ ] Root cause identified for the remaining visual mismatch
- [ ] At least one concrete hypothesis is validated against computed styles or markup inspection
- [ ] Recommendation is documented for either a CSS fix or a markup/structure change
- [ ] Next action is explicit and ready for frontend follow-up or another coding pass

---

## Notes / Context from This Session

- Production `main` branch was used as the benchmark.
- Legacy menu structure was compared directly against current Bootstrap 5 markup.
- Legacy CSS rules were restored in the offcanvas header and close-button area.
- The visual mismatch still persists, which suggests the issue is not just value-level CSS drift.
- User also plans to involve a frontend dev for a second opinion.

---

## Dispatch Notes for Qwen

Use the legacy Rails 5 main branch as the reference, but do not keep guessing at CSS values. Do a focused investigation of the actual computed styles and the offcanvas element structure. Prefer one fact-based hypothesis at a time and validate it before making more changes.

This is a UI investigation task, not a broad refactor. The goal is to find the precise rendering mismatch and propose the correct implementation direction.
