---
status: active
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

# TASK: Admin Offcanvas Menu Button Click Handler Not Working (Rails 7)
**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-09-25
**Last Updated**: 2026-09-25

---

## Context

Partial fix applied: The nav is now hidden by CSS (no longer visible at top). However, clicking the menu button does not trigger the offcanvas to slide in. The click handler initialization is still broken.

**Current State**:
- ✅ Nav hidden by default (CSS working)
- ❌ Menu button click has no effect (JavaScript event handler broken)
- ❌ Hiraku not initializing on page load

**Previous Investigation**:
- Hiraku library (v2.1.8) is installed in node_modules
- CSS rules exist and are working (nav now hidden)
- Changed `turbolinks:load` → `turbo:load` in off_canvas.js
- But Hiraku constructor still not running or click handler not binding

---

## Problem Statement

When user clicks the "Menu" button on `/admin` page, nothing happens. The offcanvas nav should slide in from the right, but it remains hidden.

**Root Cause**: Hiraku is not being instantiated. Either:
1. The `turbo:load` event is not firing
2. The Hiraku constructor is failing silently
3. The wrong Turbo event is being used

---

## Files Involved

### Primary Files — you will investigate/edit

| File | Purpose |
|---|---|
| `databases/app/assets/javascripts/plugins/off_canvas.js` | Hiraku initialization with event listener |
| Browser Console | Debug whether events fire and Hiraku initializes |

---

## Implementation Steps

### Step 1 — Add Debug Logging to off_canvas.js

Edit `/Users/tam0013/Documents/git/databases/databases/app/assets/javascripts/plugins/off_canvas.js`:

```javascript
$( document ).on('turbo:load', function() {
  console.log('turbo:load event fired');
  console.log('Hiraku object exists:', typeof Hiraku);
  try {
    new Hiraku(".offcanvas-left", {
      btn: "#offcanvas-btn-left",
      direction: "right", 
      closeBtn: '.close-button',
      width: '300px' 
    });
    console.log('Hiraku initialized successfully');
  } catch(error) {
    console.error('Hiraku initialization error:', error);
  }
});
```

### Step 2 — Recompile Assets and Restart

```bash
cd /Users/tam0013/Documents/git/databases
docker exec -it databases bundle exec rake assets:precompile
docker restart databases
sleep 20
```

### Step 3 — Test and Check Console

1. Open http://localhost:3000/admin
2. Open DevTools (F12)
3. Go to Console tab
4. Look for these log messages:
   - `turbo:load event fired` — Event is firing
   - `Hiraku object exists:` — Should show "function"
   - `Hiraku initialized successfully` OR `Hiraku initialization error:` — Check which appears

**Paste console output in chat.**

### Step 4 — Try Alternative Turbo Events

If `turbo:load` is NOT firing, try `turbo:render` instead:

```javascript
$( document ).on('turbo:render', function() {
  console.log('turbo:render event fired');
  new Hiraku(".offcanvas-left", {
    btn: "#offcanvas-btn-left",
    direction: "right", 
    closeBtn: '.close-button',
    width: '300px' 
  });
});
```

Repeat Step 2-3 with this change.

### Step 5 — Check if Button Click Works

If Hiraku initializes successfully, test the button:
1. Click the "Menu" button on the page
2. Watch the console for any errors
3. Verify if nav slides in

If nav slides in → **fix complete, remove debug logging**
If nav doesn't slide in → **Hiraku initialized but click handler still broken**

### Step 6 — Final Fix

Once working, remove the `console.log` statements and commit.

---

## Acceptance Criteria

- [ ] `turbo:load` (or correct Turbo event) is firing
- [ ] Hiraku object initializes without errors
- [ ] Menu button click triggers nav to slide in from right
- [ ] Nav slides out when closed
- [ ] No console errors
- [ ] Debug logging removed before commit
- [ ] Works on both local and VM environments

---

## Notes

- Nav CSS is now correct (hidden by default)
- Only need to fix the JavaScript click handler
- Hiraku should work with Turbo but needs correct event timing
- If Hiraku fails, Option B (Bootstrap 5 native offcanvas) becomes necessary
