## FINDINGS REPORT

**Date**: 2026-09-25

### Console Errors
Need browser verification (Step 2) but likely: `Hiraku is not defined` or similar if Hiraku fails to load. Will verify in browser.

### Hiraku Status
- **Hiraku object**: To verify in browser console (depends on whether JS loads correctly)
- **.offcanvas-left element**: Exists — confirmed at `databases/app/views/admin/_navigation.html.erb` line 1: `<nav id="offCanvas" class="offcanvas-left" role="navigation">`
- **Menu button element**: Exists — confirmed at `databases/app/views/layouts/admin.html.erb` line ~58: `<a href="javascript:void(0)" class="button menu-button" id="offcanvas-btn-left" data-toggle-offcanvas="#off-canvas">Menu</a>`

### CSS Analysis

The `_nav.scss` file has NO default hiding for `.offcanvas-left`. The raw CSS only styles the nav CONTENT (padding, background-color, colors). There is no `display: none`, `position: fixed`, or `left/right` on the element itself. 

Hiraku CSS (`hiraku/css/hiraku.css`) adds classes dynamically when initialized:
- `js-hiraku-offcanvas-sidebar-right` or `js-hiraku-offcanvas-sidebar-left` (added to nav element)
- `js-hiraku-offcanvas-body` (added to body)

**Critical Finding**: If Hiraku does NOT initialize, the nav renders as a bare `<nav id="offCanvas" class="offcanvas-left">` with NO special CSS rules targeting that exact selector — it just appears as a normal block element at the top of the page. This matches the reported symptom perfectly.

### Test Results (Automated Investigation)
- **Hiraku gem in bundle**: NOT installed as a Ruby gem — confirmed via `bundle show hiraku` returned empty
- **Hiraku yarn package**: ✅ Installed at `^2.1.8` in `databases/package.json`, verified files exist in container's `node_modules/hiraku/js/` (hiraku.js, jquery-hiraku.js)
- **Hiraku CSS**: ✅ Included in `interface/main.scss` via `@import "hiraku/css/hiraku";`
- **Asset precompilation**: ✅ Just completed successfully
- **Container health**: ✅ `Up 8 minutes (healthy)`

### ROOT CAUSE ANALYSIS

The root cause is a **double initialization failure** involving two separate issues:

1. **Hiraku JS constructor mismatch**: The `off_canvas.js` file calls `new Hiraku(...)` which expects the global `Hiraku` class from `hiraku/js/hiraku.js`. However, looking at the Hiraku bundle (`jquery-hiraku.js`), the `Hiraku` class is defined inside that bundle's IIFE. The issue is that `interface.js` includes hiraku in this order:
   ```
   //= require hiraku/js/hiraku      ← defines global Hiraku class
   //= require plugins/off_canvas    ← calls new Hiraku(...)
   ```
   But then later on turbo:load, the `off_canvas.js` code re-initializes Hiraku. The problem is that **turbo navigation doesn't trigger a full page reload**, so `new Hiraku(...)` in the script execution context only runs once on the initial load — but on Turbo navigation, the event listener (`$(document).on('turbo:load', ...)`) SHOULD fire it again. However, there's a subtle issue: `rails-ujs` (used in `application.js`) and **Turbo** have conflicting behavior with `turbo:load` event timing. Turbo fires its load events BEFORE the DOM is fully stable, and jQuery's `.on()` binding may not catch them reliably if the DOM hasn't completed rendering.

2. **More likely root cause**: The admin layout uses `data-turbolinks-track: 'reload'` which is a Turbolinks attribute — but the project has migrated to Turbo. The attribute `data-turbolinks-track` is silently ignored by Turbo (it uses `data-turbo-track` instead). This means **the asset pipeline is serving the old assets**, and if any cached/turbolinks-specific behavior was interfering with turbo:load events, it wouldn't be obvious.

3. **CSS specificity**: Without Hiraku initializing, there's NO CSS rule in `_nav.scss` that hides or positions the nav element. It renders as a normal `<nav>` block at the top of the page — exactly the reported symptom. The `display`, `position: fixed`, and `transform` rules only apply when Hiraku adds its `js-hiraku-offcanvas-sidebar-*` classes dynamically.

**CONCLUSION**: Hiraku is not initializing on either initial page load or Turbo navigation, causing the nav to render unstyled (bare `<nav>` element) at the top of the page. The most likely cause is that the `turbo:load` event fires but either:
- The browser console will show a JS error because `Hiraku` is undefined (script loading order issue)
- OR `new Hiraku(...)` silently fails because the DOM element isn't ready yet on turbo:load

### PROPOSED SOLUTION

**Option A (Recommended): Fix the existing Hiraku approach** — make off_canvas.js compatible with Turbo by using proper DOM readiness:
1. Change `off_canvas.js` to use jQuery's document.ready instead of/in addition to turbo:load, OR use `turbo:before-cache` and `turbo:render` events which fire at the right time
2. Ensure the script only initializes once per page load (add guard: if element already has hiraku classes, skip init)
3. Remove all Turbolinks references (`data-turbolinks-track` → `data-turbo-track`)

**Option B (Nuclear): Replace Hiraku with Bootstrap 5 offcanvas** — Since Bootstrap 5.3.0 is already in the project (`//= require bootstrap` in application.js), and Bootstrap has a built-in offcanvas component:
1. Convert `databases/app/views/admin/_navigation.html.erb` to use Bootstrap's data attributes (`data-bs-toggle="offcanvas"`, `data-bs-target="#offCanvas"`)
2. Add proper Bootstrap classes (`offcanvas`, `offcanvas-end`, etc.)
3. Remove Hiraku dependency entirely
4. Simplify `off_canvas.js` to just handle the button click if needed

### RISK ASSESSMENT
- Option A: Low risk — maintains existing architecture, just fixes event handling
- Option B: Medium risk — requires HTML restructuring of the nav partial and potentially some CSS adjustments; but long-term benefit is reduced dependency on unmaintained Hiraku library
- Neither option should break other features since offcanvas is isolated to admin navigation

### READY TO PROCEED?
**Option A first** (quick fix), then **Option B** as a follow-up if issues persist. Option B is the better long-term solution since Bootstrap already provides offcanvas functionality and removes a legacy dependency. Need approval before implementing.
