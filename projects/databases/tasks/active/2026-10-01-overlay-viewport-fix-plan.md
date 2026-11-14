# Planning Document: Admin Offcanvas Overlay Viewport Coverage Fix

**Created:** 2026-10-01  
**Project:** WVU Databases Rails 7 Modernization  
**Branch:** `rails7-circleci-test`  
**Task File:** `2026-10-01-MEDIUM-overlay-viewport-coverage.md`  
**Priority:** MEDIUM (visual polish, not blocking)  
**Estimated:** 2-4 hours  

---

## Executive Summary

The admin offcanvas menu is **fully functional** — opens/closes correctly, all links work. The only remaining issue: the dark overlay backdrop doesn't cover the full viewport when the menu is open (~60% coverage, white areas visible on left/bottom edges).

**Root cause is confirmed and understood:** Hiraku's CSS transform creates a stacking context that breaks all conventional overlay positioning strategies (fixed, absolute, expanded dimensions). The user attempted a JavaScript-based overlay with dimension compensation — committed but not working. **This planning doc exists to break the iteration cycle by selecting one strategy and executing it.**

---

## Root Cause Analysis

### What's Happening

1. Hiraku CSS rule `.js-hiraku-offcanvas-body-active .js-hiraku-offcanvas-body-right` applies `transform: translateX(-300px)` to the `<body>` element
2. **CSS transforms create a new containing block** — this is the key insight all previous attempts missed
3. Any element inside the transformed body that uses `position: fixed` does NOT become viewport-relative — it becomes relative to the transformed body, which is offset 300px left
4. The user's JS solution compounds this by using:
   - `position: fixed` (breaks inside transform context)
   - `top: -300px` + `left: -150px` (negative offsets don't help when the containing block itself is transformed)
   - `width: calc(100vw + 450px)` (expanded width helps partially, but the element is still clipped to the transformed body bounds)

### Why All 7 CSS-Only Attempts Failed

Every CSS attempt hit the same wall: **you cannot override a containing block established by a transform from within that context.** This includes:
- `position: fixed` on any child of body → relative to transformed body
- `z-index` manipulation → doesn't change containing block
- `overflow: visible` → only affects overflow, not stacking/containing blocks
- Negative margins/padding → same problem, still inside the transform

### Why the Current JS Attempt Fails

The current code does this:
```js
menuOverlay.style.cssText = [
  'position:fixed;',        // ❌ fixed inside transformed body = broken
  'top:-300px;',            // ❌ offsets don't help in wrong containing block
  'left:-150px;',           // ❌ same issue
  'width:calc(100vw + 450px);',  // ✅ expanded width is conceptually right
  'height:calc(100vh + 600px);', // ✅ expanded height is conceptually right
].join('');
document.body.appendChild(menuOverlay);
```

The overlay IS appended to `document.body`, but because `<body>` itself has the CSS transform, any child of body inherits the transformed containing block. The element's `position: fixed` makes it appear in the wrong location (shifted by the body's translateX offset).

---

## Solution Approaches

### Approach A: Portal Overlay Outside Transform Context ✅ RECOMMENDED

**Core Idea:** Place the overlay element at a DOM location that is NOT inside the transformed `<body>`. In Rails layouts, there's typically a wrapper div or the `<html>` element outside the body transform scope. However, since the body IS the root container, we need to use **`document.documentElement`** (`<html>`) or create an overlay using a different technique.

**Implementation:**
```js
// Option A1: Create overlay on documentElement (outside body transform)
function createMenuOverlay() {
  menuOverlay = document.createElement('div');
  menuOverlay.className = 'menu-overlay';
  // Position relative to <html>, NOT body
  Object.assign(menuOverlay.style, {
    display: 'none',
    position: 'fixed',        // fixed to viewport (html is not transformed)
    top: '0', left: '0',
    width: '100vw',
    height: '100vh',
    zIndex: '9999',
    background: 'rgba(44,62,80,0.95)',
    pointerEvents: 'auto'
  });
  
  // Append to html, not body — escapes the transform context
  document.documentElement.appendChild(menuOverlay);
  return menuOverlay;
}
```

**Why this works:** `document.documentElement` is the `<html>` element. Elements appended to it and positioned with `position: fixed` are relative to the viewport, NOT to any ancestor's transform. The `<html>` element does not have the CSS transform — only the `<body>` child of html has it.

**Risk:** Minimal. Standard DOM manipulation pattern. Won't affect menu functionality since we only modify overlay visibility.

---

### Approach B: MutationObserver on `body` Class + `position: fixed` on `html` ✅ FALLBACK

**Core Idea:** Same as Approach A, but if appending to `document.documentElement` causes layout issues (unlikely), fall back to using a **global style injection** instead of a DOM element:

```js
// Create a full-viewport overlay via global stylesheet
function createOverlayStylesheet() {
  const style = document.createElement('style');
  style.id = 'hiraku-overlay-style';
  style.textContent = `
    .menu-overlay-active {
      position: fixed !important;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      z-index: 9999;
      background: rgba(44,62,80,0.95);
    }
  `;
  document.head.appendChild(style);
}

// Show/hide by toggling body class
function showOverlay() {
  createOverlayStylesheet(); // Create if not exists
  document.body.classList.add('menu-overlay-active');
}

function hideOverlay() {
  document.body.classList.remove('menu-overlay-active');
  // Remove stylesheet after transition (if any)
  const style = document.getElementById('hiraku-overlay-style');
  if (style && !document.body.classList.contains('menu-overlay-active')) {
    style.remove();
  }
}
```

**Why this works:** `position: fixed` on an element inside `<body>` that is styled via stylesheet rule targeting `body.menu-overlay-active` — the selector itself creates a fresh stacking context because it's a class on the same body that has the transform. The key difference from previous attempts: we're using `100vw / 100vh` WITHOUT negative offsets, relying on fixed positioning which (when the rule targets the body directly) should still respect viewport dimensions.

**Risk:** Low. Pure CSS approach via dynamic stylesheet injection. No dimension math needed.

---

### Approach C: Direct DOM Injection + `pointer-events: none` Body Hack

**Core Idea:** Instead of fighting the transform, **neutralize it temporarily**:

```js
function showOverlay() {
  if (!menuOverlay) createMenuOverlay();
  
  // Store original body style to restore later
  const origTransform = document.body.style.transform || '';
  
  // Neutralize the transform that breaks positioning
  document.body.style.setProperty('transform', 'none', 'important');
  
  // Create overlay at full viewport (normal fixed position)
  menuOverlay.style.cssText = [
    'display:block;',
    'position:fixed;',
    'top:0;left:0;width:100vw;height:100vh;',
    'z-index:9999;pointer-events:auto;'
  ].join('');
  
  document.body.appendChild(menuOverlay);
  
  // Restore transform AFTER overlay is created
  requestAnimationFrame(() => {
    document.body.style.transform = origTransform;
  });
}
```

**Risk:** HIGH — Neutralizing the body transform will cause the menu panel to become visible again during the brief window where transform is removed. This approach is fragile and not recommended unless Approaches A and B fail.

---

## Recommendation: Approach A First, B as Fallback

| Criterion | Approach A | Approach B | Approach C |
|-----------|------------|------------|------------|
| Reliability | High (DOM-level isolation) | Medium-High (CSS only) | Low (timing-dependent) |
| Risk of regression | None | None | High (transform flash) |
| Complexity | Low (4 lines change) | Low (stylesheet injection) | Medium |
| Recommended? | **YES — implement first** | YES — if A fails | NO — last resort |

---

## Implementation Plan

### Step 1: Replace `document.body.appendChild` with `document.documentElement.appendChild` in `off_canvas.js`

**File:** `databases/app/assets/javascripts/plugins/off_canvas.js`

**Change the `createMenuOverlay()` function:**
```js
function createMenuOverlay() {
  if (menuOverlay) return menuOverlay;
  
  menuOverlay = document.createElement('div');
  menuOverlay.className = 'menu-overlay';
  Object.assign(menuOverlay.style, {
    display: 'none',
    position: 'fixed',
    top: '0',          // ← was -300px (unnecessary when on html)
    left: '0',         // ← was -150px (unnecessary when on html)
    width: '100vw',    // ← simplified from calc()
    height: '100vh',   // ← simplified from calc()
    zIndex: '9999',    // ← reduced from 100000 (sufficient z-index)
    background: 'rgba(44,62,80,0.95)',
    pointerEvents: 'auto'
  });
  
  // KEY CHANGE: append to html, not body — escapes the transform context
  document.documentElement.appendChild(menuOverlay);
  return menuOverlay;
}
```

### Step 2: Simplify SCSS overlay styles

**File:** `databases/app/assets/stylesheets/interface/elements/_nav.scss`

Replace the `.menu-overlay` rule with minimal styles (JS handles everything):
```scss
.menu-overlay {
  // All positioning handled by JavaScript via inline style.
  // No CSS overrides needed since we append to documentElement.
}
```

### Step 3: Commit and push to dev VM

```bash
cd /Users/tam0013/Documents/git/databases
git add databases/app/assets/javascripts/plugins/off_canvas.js databases/app/assets/stylesheets/interface/elements/_nav.scss
git commit -m "fix(admin): escape body transform context for full viewport overlay

- Move overlay from body to documentElement to escape CSS transform stacking context
- Simplify dimensions from calc() offsets to straight 100vw/100vh
- Remove unnecessary negative top/left offsets (were needed only inside transformed body)
- This fixes the root cause: fixed-position elements on html are viewport-relative
  regardless of body's transform"

git push origin rails7-circleci-test
```

### Step 4: Test on dev VM

1. SSH to `databasesdev.lib.wvu.edu`
2. Navigate to `https://databases.lib.wvu.edu/admin` (SSO auth)
3. Click the admin nav toggle button
4. Verify: dark overlay covers ENTIRE viewport (no white/light areas at edges)
5. Verify: clicking overlay does NOT close menu (unless that's a desired feature)
6. Close menu via × button — verify overlay disappears completely
7. Reopen menu — verify overlay appears again

### Step 5: Verification Checklist

- [ ] Overlay covers 100% viewport width
- [ ] Overlay covers 100% viewport height
- [ ] No visible white/light areas at any edge
- [ ] Overlay disappears on menu close
- [ ] Menu itself still positions correctly (not affected by overlay)
- [ ] Reopening menu creates working overlay again
- [ ] Turbo page transitions work (overlay appears on each nav open after turbo load)

---

## Testing Strategy

### Dev VM (Primary)
- URL: `https://databases.lib.wvu.edu/admin`
- Auth: SSO (configured and working on dev)
- Test flow: Open menu → full coverage → close → reopen → verify still works
- Compare against production site for visual parity

### Local Development (Secondary)
- If local dev has auth configured, test there too
- Check browser DevTools Network tab to confirm overlay CSS loads correctly
- Verify no console errors related to overlay or Hiraku init

### Browser DevTools Debugging Technique
If overlay STILL doesn't cover full viewport after the fix:
1. Open browser DevTools
2. Click element picker, select the `.menu-overlay` div
3. Check its computed dimensions (Computed panel) — should be 1920x1080 (or whatever viewport is)
4. Check `offsetParent` — should be `null` for a proper fixed element on html
5. If offsetParent is NOT null, there's an ancestor with `transform/opacity/filter/perspective` — identify and remove it

---

## What Happens After This Fix

1. ✅ Overlay viewport coverage complete → mark task as completed
2. PR review of `rails7-circleci-test` branch
3. Merge to main when ready
4. **Optional future work (not in scope):** Approach B from previous session — refactor offcanvas to use Bootstrap 5's native offcanvas component instead of the deprecated Hiraku library

---

## Files Modified

| File | Change |
|------|--------|
| `databases/app/assets/javascripts/plugins/off_canvas.js` | Move overlay from `document.body.appendChild()` to `document.documentElement.appendChild()`, simplify dimensions |
| `databases/app/assets/stylesheets/interface/elements/_nav.scss` | Minimal SCSS (JS handles all positioning) |
