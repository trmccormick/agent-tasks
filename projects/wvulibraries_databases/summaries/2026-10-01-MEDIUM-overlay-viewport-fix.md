# Synthesis Report: overlay-viewport-fix

**Task**: 2026-10-01-MEDIUM-overlay-viewport-coverage.md
**Reviewed**: 2026-10-01

## Understanding Summary
The admin offcanvas menu's dark backdrop only covers ~60% of the viewport because it's appended to `document.body`, which sits inside a CSS transform stacking context (created by Hiraku's `transform: translateX(-300px)` on body). Any `position: fixed` element inside a transformed parent becomes relative to that parent — not the viewport. The fix is to append the overlay to `document.documentElement` (`<html>`) instead, where `position: fixed` works as expected. Then simplify the dimensions from compensation-based calc() back to clean 100vw/100vh.

## File Analysis Results
| File | Verified Exists? | Current State |
|------|-----------------|---------------|
| off_canvas.js | YES | Overlay appended to body with calc() compensation (-300px top, -150px left, 100vw+450px width) |
| _nav.scss | YES | Minimal placeholder — positioning handled by JS inline styles |

## Confirmed Approaches (check only what applies)
- [x] One-line DOM target change (documentElement portal) — **RECOMMENDED**
- [ ] CSS-only fix using containment/pseudo-elements
- [ ] MutationObserver watching for transform changes
- [ ] Other: [describe]

## Architecture Concerns
- None. The fix is a one-line DOM append target change plus dimension simplification. Both are self-contained in `off_canvas.js`.
- No SCSS changes needed — overlay positioning is JS inline styles.
- Production parity should be exact since the approach matches the known-working pattern.

## Plan Summary
1. Change `document.body.appendChild(menuOverlay)` to `document.documentElement.appendChild(menuOverlay)`.
2. Simplify inline CSS from compensation-based dimensions (`top:-300px; left:-150px; width:calc(100vw + 450px)`) to clean viewport coverage (`top:0; left:0; width:100vw; height:100vh`).
3. Commit and push to `rails7-circleci-test`.

## Questions for Human
None
