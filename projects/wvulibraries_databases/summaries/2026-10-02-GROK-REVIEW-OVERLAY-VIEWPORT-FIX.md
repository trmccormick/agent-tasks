# Grok Review: Admin Offcanvas Overlay Viewport Coverage Fix

**Prepared**: 2026-10-02  
**Status**: Code pushed, fix NOT working — needs expert review  
**Branch**: `rails7-circleci-test` (commit `5528e97`, pushed to origin)

---

## 1. Problem Statement

When the admin offcanvas menu opens, a dark overlay backdrop should cover the entire viewport to dim underlying content. Currently the overlay only covers ~60% of the viewport — light/gray areas remain visible around the edges (left and bottom).

**Menu functionality is NOT broken.** Menu opens/closes correctly, all links work. This is purely a visual coverage issue.

---

## 2. Root Cause (Confirmed)

### CSS Transform Stacking Context

The Hiraku library applies this rule when the menu opens:

```css
.js-hiraku-offcanvas-body-active .js-hiraku-offcanvas-body-right {
  transform: translateX(-300px);
}
```

This `transform` is applied to `<body>`. A CSS `transform` creates a new containing block. Any descendant with `position: fixed` becomes relative to that transformed body — NOT the viewport. **No amount of dimension compensation can fix this.**

### Why 7 Previous CSS Attempts Failed

Every previous fix was a CSS rule targeting `.menu-overlay` or similar selectors. All failed because they were trying to compensate for the transform within the same containing block — mathematically impossible regardless of calc() values used.

---

## 3. What Was Done (Two Commits)

### Commit `576bcc1` — JS Overlay + documentElement Portal
**File**: `databases/app/assets/javascripts/plugins/off_canvas.js`

Added three new functions plus a MutationObserver:

```javascript
var menuOverlay = null;

function createMenuOverlay() {
  if (menuOverlay) return menuOverlay;
  
  menuOverlay = document.createElement('div');
  menuOverlay.className = 'menu-overlay';
  menuOverlay.style.cssText = [
    'display:none;',
    'position:fixed;',
    'top:0;',
    'left:0;',
    'width:100vw;',
    'height:100vh;',
    'z-index:100000;',
    'background:rgba(44,62,80,0.95);',
    'pointer-events:auto;'
  ].join('');
  
  document.documentElement.appendChild(menuOverlay); // ← KEY CHANGE
  return menuOverlay;
}

function showOverlay() {
  var overlay = createMenuOverlay();
  if (overlay) overlay.style.display = 'block';
}

function hideOverlay() {
  if (menuOverlay) menuOverlay.style.display = 'none';
}
```

Added MutationObserver in `initHirakuOffCanvas()`:

```javascript
var sideEl = document.querySelector('.offcanvas-left');
if (sideEl) {
  var observer = new MutationObserver(function(mutations) {
    mutations.forEach(function(mutation) {
      if (mutation.attributeName === 'aria-hidden') {
        var newVal = sideEl.getAttribute('aria-hidden');
        if (newVal === 'false') showOverlay();
        else hideOverlay();
      }
    });
  });
  observer.observe(sideEl, { attributes: true });
}
```

### Commit `5528e97` — SCSS Cleanup
**File**: `databases/app/assets/stylesheets/interface/elements/_nav.scss`

Removed the old CSS overlay selector that was fighting with JS logic:

```scss
// REMOVED (was lines 73-89):
// .js-hiraku-offcanvas-body [aria-hidden=false] + .js-hiraku-offcanvas {
//   display: block; position: fixed; top: 0; left: 0; z-index: 100001;
//   width: 100vw; height: 100vh; background: rgba($dark-gray, 0.7); opacity: 0.7;
// }

// REPLACED WITH:
.menu-overlay {
  display: none;
}
```

---

## 4. Why It's NOT Working — Turbo Timing is the Likely Culprit

### Issue A: Turbo Navigation Early Return (Most Likely)

The app uses Rails Turbo. When Turbo navigates:
1. Old DOM is destroyed (including our `document.documentElement` child overlay)
2. New DOM loads from server (no overlay element exists yet)
3. `initHirakuOffCanvas()` runs on `turbo:load` event
4. **BUT** the function has an early return if `hirakuInstance` already exists:
   ```javascript
   if (hirakuInstance) return; // Already initialized — blocks re-observing!
   ```

This means on Turbo navigations, the MutationObserver is NEVER set up for new DOM nodes.

### Issue B: documentElement Children Still Affected by Body Transform

While theoretically correct (children of `<html>` with `position: fixed` should be viewport-relative), there may be a browser-specific quirk where the transform's containing block cascades through the entire rendering tree regardless of element ancestry depth. This would require verification in DevTools.

### Issue C: Cached Assets

Even though `_nav.scss` changes are now committed, Rails asset pipeline may still serve cached versions until the next deployment cycle. A hard refresh (Cmd+Shift+R) is required locally.

---

## 5. Current Working State

### Repositories
| Repo | Branch | Status | Latest Commit |
|------|--------|--------|---------------|
| agent-tasks | main | ✅ Pushed to origin | `6d7b10b` |
| databases | rails7-circleci-test | ✅ Pushed to origin | `5528e97` |

### File Paths (CRITICAL: Rails app is nested)
```
Repo root: /Users/tam0013/Documents/git/databases/
Rails app: /Users/tam0013/Documents/git/databases/databases/app/assets/...
```
The actual Rails app lives at `databases/databases/` — all reads must use this path.

### Dev Environment
- **Dev VM**: `https://databasesdev.lib.wvu.edu/admin` (SSO auth working)
- **Production**: `https://databases.lib.wvu.edu/admin` (overlay works correctly here)

---

## 6. What Grok Should Review

### Primary Question: Why isn't the documentElement approach working?

The fix should theoretically work because:
1. `<html>` has no CSS transform → `position: fixed` children are viewport-relative
2. Overlay is appended directly to `<html>` via `document.documentElement.appendChild()`
3. Dimensions are clean `100vw`/`100vh` — no calc() compensation needed

**Suspected root causes to investigate:**
1. Turbo navigation destroying the overlay element and not recreating it (early return when `hirakuInstance` exists)
2. MutationObserver watching wrong attribute (`aria-hidden` vs `class`) or wrong target (`.offcanvas-left` vs `<body>`)
3. Browser caching preventing new JS from loading
4. Browser-specific quirk where transform's containing block affects ALL descendants regardless of depth

### Specific Questions for Grok:

1. **Is the documentElement portal approach fundamentally sound**, or is there a browser-specific behavior I'm missing where `position: fixed` on a `<html>` child can also be affected by ancestor transforms?

2. **Should we instead revert to CSS-only** and find a selector that targets the overlay BEFORE the transform fires? For example, using `::before` pseudo-element on the `<body>` which is created before any class changes happen.

3. **Is the Turbo timing issue killing this fix?** The early return pattern (`if (hirakuInstance) return;`) combined with page destruction/reconstruction is a known anti-pattern in Turbo apps. Should we destroy and recreate the Hiraku instance on each turbo:load, or should overlay management be completely decoupled from Hiraku init?

4. **Could the MutationObserver target be wrong?** The observer watches `aria-hidden` on `.offcanvas-left`, but does that attribute actually change when the menu opens/closes? Alternative trigger points to consider:
   - Watching for class changes on `<body>` (`js-hiraku-offcanvas-body-active`)
   - Listening to custom events emitted by Hiraku (if available)
   - Using `transitionend` event on the nav panel

5. **Should we use a CSS pseudo-element fallback?** The `<body>::before` selector is created at render time before any transform classes are added, so it would always be viewport-relative:
```scss
body { position: relative; }
body::before {
  content: '';
  position: fixed;
  top: 0; left: 0; width: 100vw; height: 100vh;
  background: rgba(44,62,80,0.95);
  z-index: 100000;
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}
body.js-hiraku-offcanvas-body-active.js-hiraku-offcanvas-body-right::before {
  opacity: 1;
}
```

---

## 7. Testing Checklist for Grok Review

- [ ] Pull `rails7-circleci-test` on dev VM (`5528e97`)
- [ ] Hard refresh browser (Cmd+Shift+R) to clear asset cache
- [ ] Open admin panel, click menu button
- [ ] DevTools Console: verify no JS errors in overlay creation
- [ ] DevTools Elements: check if `.menu-overlay` element exists and its parent node
- [ ] DevTools Console: `document.querySelector('.menu-overlay')?.offsetParent` (should be `null`)
- [ ] Verify overlay appears/disappears when menu toggles
- [ ] If no overlay: try navigating to different admin page, then open menu (Turbo timing test)

---

## 8. Previous Attempts Summary

## 8. Previous Attempts Summary

| # | Approach | File Changed | Result | Why Failed |
|---|----------|-------------|--------|------------|
| 1-7 | CSS-only selectors on `.menu-overlay` | `_nav.scss` | ❌ | Same containing block as body (transform context) |
| 8 | JS overlay on `<body>` with calc() compensation | `off_canvas.js` | ❌ | Wrong containing block, dimensions can't fix it |
| 9 (current) | JS overlay on `<html>` + MutationObserver | `off_canvas.js` + `_nav.scss` | ❌ NOT WORKING | Turbo timing / observer targeting / browser quirk? |

---

## 9. Files for Grok to Inspect Directly

1. **`/Users/tam0013/Documents/git/databases/databases/app/assets/javascripts/plugins/off_canvas.js`** — Main fix (both commits)
2. **`/Users/tam0013/Documents/git/databases/databases/app/assets/stylesheets/interface/elements/_nav.scss`** — Overlay styles (CSS selector removed in commit 5528e97)
3. **`/Users/tam0013/Documents/git/databases/databases/app/assets/javascripts/interface.js`** — Sprockets manifest
4. **Hiraku gem source** (if available in vendor/) — Check if it emits custom events

---

## 10. Recommended Next Steps (if Grok agrees)

### Option A: Revert to CSS Pseudo-Element (Safest)
If documentElement approach truly doesn't work in this browser/rendering context, fall back to `body::before` pseudo-element which may escape transform context differently.

```scss
body { position: relative; }

body::before {
  content: '';
  position: fixed;
  top: 0; left: 0; width: 100vw; height: 100vh;
  background: rgba(44,62,80,0.95);
  z-index: 100000;
  opacity: 0;
  transition: opacity 0.3s ease;
}

body.js-hiraku-offcanvas-body-active::before {
  opacity: 1;
}
```

### Option B: Fix Turbo Timing (If Element Works But Only After Refresh)
1. Remove `if (hirakuInstance) return;` early return in `initHirakuOffCanvas()`
2. Always re-create overlay on turbo:load
3. Or manage overlay via body class MutationObserver instead of Hiraku events

### Option C: Production Parity Investigation
Compare production site's DOM/network with dev VM to identify what initialization step is missing in Rails 7 modernization that still works there.

---

**End of review summary.** Send this document to Grok for expert CSS/rendering analysis.
