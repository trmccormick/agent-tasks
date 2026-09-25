---
status: active
priority: MEDIUM
type: refactoring
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-09-25
tags: [long-term, bootstrap5, hiraku-removal, admin-nav]
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_databases
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks/active/2026-09-25-MEDIUM-bootstrap-replace-hiraku-admin-offcanvas.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  Task file is already in active/ folder.
  Verify by running:
  find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks -name "2026-09-25-MEDIUM-bootstrap-replace-hiraku-admin-offcanvas.md"
  Expected: exactly one result at active/ path
  Paste the output in chat before proceeding.

LIFECYCLE: backlog → active → completed
  - Task status: BACKLOG (long-term refactoring)
  - Read file: everything below has prerequisites, gotchas, verification steps

READ FIRST (after Step 0 verification): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/
  Filename pattern: 2026-09-25-[SHORT-DESCRIPTION].md
  Chat is for questions only — synthesis report is saved to file, not pasted in chat.
```

---

# TASK: Replace Hiraku Offcanvas with Bootstrap 5 Native Offcanvas (Long-Term Fix)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactoring
**Created**: 2026-09-25
**Related Task**: `2026-09-25-HIGH-bug-fix-ADMIN-OFFCANVAS-NAV-RAILS7.md` (Option A quick fix)

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot
**Why This Agent**: Requires Rails 7/Bootstrap 5 expertise and full codebase refactoring
**Local attempts before cloud**: TBD
**Supervision Level**: standard

---

## Context

The admin offcanvas navigation currently uses the Hiraku.js library (yarn package ^2.1.8), which was incompatible with Turbo (Rails 7). Option A was implemented as a quick fix — it patched the event timing by switching from `turbo:load` to `turbo:render` and added a CSS safety net (`display: none` on nav).

This task replaces Hiraku entirely with Bootstrap 5.3.0's native offcanvas component, which is already included in the project. This removes a legacy dependency that requires JS event timing workarounds.

**Why now**: Option A is working as a temporary fix, but Bootstrap native offcanvas is more reliable, has better browser support, and eliminates the Hiraku maintenance burden.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/README.md`
3. **Quick Fix Context**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/2026-09-25-ADMIN-OFFCANVAS-FINDINGS.md`
4. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Critical Information for This Task

### Credentials
Not needed for this task — all work is in the local development environment.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Bootstrap Offcanvas Requires Proper HTML Structure
- ❌ Wrong: Just adding `data-bs-toggle="offcanvas"` to existing markup
- ✅ Right: Bootstrap offcanvas has a specific HTML structure with `.offcanvas`, `.offcanvas-header`, `.offcanvas-body` classes
- Why: Bootstrap's JavaScript looks for these exact class names to initialize

⚠️ **GOTACA 2**: Hiraku CSS Must Be Removed After Migration
- ❌ Wrong: Leaving `@import "hiraku/css/hiraku"` in main.scss
- ✅ Right: Remove hiraku CSS import AND verify no other files reference hiraku
- Why: Orphaned CSS classes could conflict with Bootstrap's offcanvas styling

⚠️ **GOTCHA 3**: Hiraku JS Must Be Removed After Migration
- ❌ Wrong: Leaving `//= require hiraku/js/hiraku` in interface.js
- ✅ Right: Remove hiraku require AND verify no other files reference Hiraku class
- Why: Unnecessary dependency bloat; Hiraku bundle is large (1400+ lines)

⚠️ **GOTCHA 4**: Nav Link Styling May Need Adjustment
- ❌ Wrong: Assuming nav links look the same after Bootstrap takes over
- ✅ Right: Verify dark theme colors, hover states, and list styling are preserved
- Why: Hiraku had custom CSS (dark background `darken($link-color, 40%)`, centered text)

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete synthesis report first. Post it in chat. Wait for approval.
> Do not proceed to Step 1 until synthesis is approved.

### Step 0 — Verify Task Location (MANDATORY FIRST STEP)

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks -name "2026-09-25-MEDIUM-bootstrap-replace-hiraku-admin-offcanvas.md"
```

Paste the output in chat before proceeding.

### Step 1 — Audit Hiraku Usage Across Codebase

Search for ALL references to hiraku and Hiraku across the codebase:

```bash
cd /Users/tam0013/Documents/git/databases && grep -ri "hiraku\|Hiraku" --include="*.js" --include="*.scss" --include="*.css" --include="*.erb" --include="*.html" .
```

Paste the full output. List every file that needs updating.

### Step 2 — Rewrite Navigation Partial with Bootstrap Offcanvas

Convert `databases/app/views/admin/_navigation.html.erb` to use Bootstrap 5's offcanvas:

**Current HTML structure:**
```html
<nav id="offCanvas" class="offcanvas-left" role="navigation">
  <header>...</header>
  <ul>...</ul>
</nav>
```

**New HTML structure (Bootstrap 5):**
```html
<div class="offcanvas offcanvas-end" tabindex="-1" id="offCanvas" aria-labelledby="offCanvasLabel">
  <div class="offcanvas-header">
    <h5 id="offCanvasLabel">Menu</h5>
    <button type="button" class="btn-close text-reset" data-bs-dismiss="offcanvas" aria-label="Close"></button>
  </div>
  <div class="offcanvas-body">
    <ul class="list-unstyled">
      <!-- existing nav links -->
    </ul>
  </div>
</div>
```

Update the menu button in `admin.html.erb`:
```html
<!-- Old: data-toggle-offcanvas="#off-canvas" -->
<!-- New: Bootstrap offcanvas trigger -->
<a href="javascript:void(0)" class="button menu-button" id="offcanvas-btn-left" data-bs-toggle="offcanvas" data-bs-target="#offCanvas">
  Menu
</a>
```

### Step 3 — Preserve Dark Theme Styling

Ensure the dark theme is preserved in `_nav.scss`. Bootstrap offcanvas defaults to light/white background, so we need:

```scss
// Override Bootstrap offcanvas for dark admin theme
#offCanvas {
  background-color: darken($link-color, 40%);
  color: #fff;
  
  .offcanvas-header {
    border-bottom: 1px dashed #fff;
    h5 { color: #fff; }
    .btn-close { filter: invert(1); } // White close button
  }
  
  ul.list-unstyled li {
    padding: 1em;
    border-bottom: 1px dashed rgba(#fff, .5);
    
    &:hover { background-color: darken($link-color, 50%); }
    
    a { color: $link-color; }
    
    &.title h3 { text-transform: uppercase; border-bottom: 1px dashed #fff; }
  }
}
```

### Step 4 — Remove Hiraku Dependencies

**interface.js** — remove hiraku require line:
```js
// REMOVE THIS LINE:
//= require hiraku/js/hiraku
```

**interface/main.scss** — remove hiraku import:
```scss
// REMOVE THIS LINE:
@import "hiraku/css/hiraku";
```

**off_canvas.js** — can be deleted entirely, or left empty as a no-op (but deletion is cleaner):
```js
// Hiraku has been replaced with Bootstrap 5 native offcanvas.
// This file is intentionally left empty for backward compatibility.
```

### Step 5 — Remove Hiraku from package.json

Edit `databases/package.json` and remove the hiraku entry:
```json
// REMOVE THIS LINE:
"hiraku": "^2.1.8",
```

Then run inside container:
```bash
docker exec databases sh -c "cd / && yarn install"
```

### Step 6 — Precompile and Test

```bash
cd /Users/tam0013/Documents/git/databases
docker exec databases bundle exec rake assets:precompile
docker restart databases
sleep 15
docker ps --filter "name=databases" --format "{{.Status}}"
```

### Step 7 — Browser Testing

1. Open http://localhost:3000/admin
2. Hard refresh (Cmd+Shift+R)
3. Verify menu button triggers offcanvas slide-in from RIGHT side
4. Verify close button works
5. Verify all nav links are functional
6. Check browser console for errors
7. Verify dark theme styling is preserved

---

## Files That Need Updating

| File | Action | Priority |
|---|---|---|
| `databases/app/views/admin/_navigation.html.erb` | Rewrite with Bootstrap offcanvas HTML structure | CRITICAL |
| `databases/app/views/layouts/admin.html.erb` | Update menu button with `data-bs-toggle="offcanvas"` | CRITICAL |
| `databases/app/assets/javascripts/plugins/off_canvas.js` | Delete or empty (Hiraku no longer needed) | HIGH |
| `databases/app/assets/javascripts/interface.js` | Remove hiraku require line | HIGH |
| `databases/app/assets/stylesheets/interface/main.scss` | Remove hiraku CSS import | HIGH |
| `databases/app/assets/stylesheets/interface/elements/_nav.scss` | Preserve dark theme for Bootstrap offcanvas | HIGH |
| `databases/package.json` | Remove hiraku yarn dependency | MEDIUM |

---

## Acceptance Criteria

- [ ] Menu button (`#offcanvas-btn-left`) opens offcanvas nav from RIGHT side (slide-in animation)
- [ ] Nav is hidden by default when page loads
- [ ] Close button (`btn-close`) slides nav out to the right
- [ ] Click outside nav area does NOT close it (Bootstrap default — only close button and X do)
- [ ] All nav links are functional and clickable
- [ ] Dark theme styling preserved (dark background, white text, dashed dividers, centered text)
- [ ] Nav links have hover states matching original design
- [ ] Menu section title styling preserved (uppercase headings with bottom border)
- [ ] Browser console has NO JavaScript errors
- [ ] No Hiraku references remain in JS files (`grep -ri "hiraku" --include="*.js" .` returns nothing, or only intentional comments)
- [ ] No Hiraku CSS imports remain in SCSS files (`grep -ri "hiraku" --include="*.scss" .` returns nothing, or only intentional comments)
- [ ] Tested in development environment (http://localhost:3000/admin)
- [ ] Changes committed to git with clear commit message

---

## Blocked/Blocks

**Blocked By**: `2026-09-25-HIGH-bug-fix-ADMIN-OFFCANVAS-NAV-RAILS7.md` (Option A quick fix should be verified working first)

**Blocks**: None — this is an improvement task, not blocking anything

---

## Implementation Notes for Agent

1. **Bootstrap 5 offcanvas API reference**: https://getbootstrap.com/docs/5.3/components/offcanvas/
   - `data-bs-toggle="offcanvas"` on trigger element
   - `data-bs-target="#targetId"` points to `.offcanvas` element
   - `data-bs-dismiss="offcanvas"` on close button
   - `offcanvas-end` positions nav on right side
   - Default Bootstrap classes: `.offcanvas`, `.offcanvas-header`, `.offcanvas-body`

2. **Original nav styling**: Dark background (`darken($link-color, 40%)`), white text, centered links, dashed dividers between sections, uppercase section titles with borders

3. **Important**: The close button in the original Hiraku nav was `<span class='fa fa-times-circle'></span>` — Bootstrap uses `btn-close` (X shape via pseudo-element). Both should work but the icon styling differs slightly.

4. **Testing tip**: After each step, verify the browser still works. Don't batch all changes without intermediate testing.

5. **Option A compatibility**: The Option A quick fix (`off_canvas.js` with `turbo:render`) will become dead code after this refactoring. Consider removing it or leaving as a no-op comment.
