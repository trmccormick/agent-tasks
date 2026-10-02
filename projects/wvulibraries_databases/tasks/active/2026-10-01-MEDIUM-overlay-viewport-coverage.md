---
status: active
priority: MEDIUM
type: refactor
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as their startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: wvulibraries_databases
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks/backlog/[SUBFOLDER]/2026-10-01-MEDIUM-overlay-viewport-coverage.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_databases/tasks/backlog/[SUBFOLDER]/2026-10-01-MEDIUM-overlay-viewport-coverage.md \
         projects/wvulibraries_databases/tasks/active/2026-10-01-MEDIUM-overlay-viewport-coverage.md
  Then open the moved file and change: status: active → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_databases/tasks -name "2026-10-01-MEDIUM-overlay-viewport-coverage.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Fix Admin Offcanvas Overlay Viewport Coverage
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-10-01
**Last Updated**: 2026-10-01

---

## Context

The WVU Databases Rails 7 application uses the Hiraku library for its offcanvas admin navigation. When the admin menu opens, a dark overlay backdrop should cover the entire viewport to dim the underlying content and focus attention on the menu panel. Currently the overlay only covers ~60% of the viewport — light/gray areas remain visible around the edges (left and bottom).

This is a visual polish issue. The menu itself is fully functional — it opens, closes, and all links work. Only the backdrop coverage is broken.

## Root Cause Analysis

### The Fundamental Problem: CSS Transform Stacking Context

When the Hiraku offcanvas menu opens, this CSS rule fires:

```css
.js-hiraku-offcanvas-body-active .js-hiraku-offcanvas-body-right {
  transform: translateX(-300px);
}
```

This `transform` property is applied to the `<body>` element. A CSS `transform` creates a new containing block (stacking context). Any descendant element with `position: fixed` becomes positioned relative to that transformed containing block — NOT the viewport.

### Why All Previous Fixes Failed

Every fix attempt so far has appended the overlay to `document.body`:

```javascript
document.body.appendChild(menuOverlay); // ❌ INSIDE transform context
```

Even with expanded dimensions like `width: calc(100vw + 450px)` and negative offsets like `top: -300px`, the overlay is trapped inside the transformed containing block. No amount of dimension compensation can fix a wrong containing block.

### The Fix (Confirmed by Research)

Append the overlay to `document.documentElement` (`<html>`) instead of `document.body`:

```javascript
document.documentElement.appendChild(menuOverlay); // ✅ OUTSIDE transform context
```

The `<html>` element does NOT have any CSS transform, so its children with `position: fixed` are correctly viewport-relative.

This is a **one-line change** in the JavaScript, plus simplification of dimensions.

---

## Files Involved (all verified to exist)

| File | Purpose |
|------|---------|
| `databases/databases/app/assets/javascripts/plugins/off_canvas.js` | Overlay creation & Hiraku init |
| `databases/databases/app/assets/stylesheets/interface/elements/_nav.scss` | Overlay placeholder styles |
| `databases/databases/app/assets/javascripts/interface.js` | Sprockets manifest (requires off_canvas) |

**Important path note**: The Rails app is nested at `databases/databases/` inside the repo root. All tool-based reads must use this nested path, not `databases/app/...`.

---

## Architecture Gotchas

1. **Nested Rails structure**: The project is a "Rails-in-Rails" setup. The actual Rails app lives at `databases/databases/` (inside the repo at `databases/`). Tool-based file reads failed on `databases/app/...` paths — they are one level too shallow.

2. **CSS Transform breaking fixed positioning**: This is a well-known CSS behavior. A `transform` on a parent element creates a new containing block for `position: fixed` descendants. The overlay must live outside this context (on `<html>`).

3. **Sprockets asset pipeline**: Assets are loaded via `//= require` manifests in `interface.js`, not ES modules. Do not attempt to migrate to import maps or bundlers — this task is strictly scoped to the one-line fix.

4. **Hiraku library is deprecated**: The offcanvas component comes from the Hiraku gem which is no longer maintained. Any workaround must not depend on internal Hiraku APIs that could break on updates.

5. **Production parity**: The production site (`databases.lib.wvu.edu`) has a fully working overlay. Dev VM (`databasesdev.lib.wvu.edu`) uses the same codebase from branch `rails7-circleci-test`. Test results must match production behavior exactly.

---

## Implementation Steps

### Step 1 — One-line DOM change in `off_canvas.js`

**File**: `databases/databases/app/assets/javascripts/plugins/off_canvas.js`

Find the `createMenuOverlay()` function (approximately lines 80-130). Replace the append target:

```diff
- document.body.appendChild(menuOverlay);
+ document.documentElement.appendChild(menuOverlay);
```

### Step 2 — Simplify overlay dimensions in `off_canvas.js`

Also in `createMenuOverlay()`, simplify the inline CSS from the current compensation attempt back to clean viewport coverage:

```javascript
// BEFORE (current — broken, overcomplicated):
menuOverlay.style.cssText = [
  'display:none;',
  'position:fixed;',
  'top:-300px;',
  'left:-150px;',
  'width:calc(100vw + 450px);',
  'height:calc(100vh + 600px);',
  'z-index:100000;',
  'background:rgba(44,62,80,0.95)',
  'pointer-events:auto;'
].join('');

// AFTER (fixed — clean):
menuOverlay.style.cssText = [
  'display:none;',
  'position:fixed;',
  'top:0;',
  'left:0;',
  'width:100vw;',
  'height:100vh;',
  'z-index:100000;',
  'background:rgba(44,62,80,0.95)',
  'pointer-events:auto;'
].join('');
```

### Step 3 — Update SCSS if needed

**File**: `databases/databases/app/assets/stylesheets/interface/elements/_nav.scss`

The overlay positioning is managed by JS inline styles. SCSS should only contain the placeholder:

```scss
.menu-overlay {
  // Position and dimensions are managed by JavaScript inline styles.
  // The overlay is appended to document.documentElement (not body)
  // to avoid CSS transform stacking context issues from Hiraku.
  display: none;
}
```

No changes required if already minimal — just add/keep the comment explaining why positioning is JS-driven.

### Step 4 — Git commit and push

```bash
cd /Users/tam0013/Documents/git/databases
git checkout rails7-circleci-test
# Make the code changes above
git add databases/databases/app/assets/javascripts/plugins/off_canvas.js
git add databases/databases/app/assets/stylesheets/interface/elements/_nav.scss
git commit -m "Fix: offcanvas overlay viewport coverage via documentElement portal

- Move overlay from body to html element to escape CSS transform stacking context
- Simplify dimensions from calc() compensation to straight 100vw/100vh
- Add architectural comment explaining why positioning is JS-driven"
git push origin rails7-circleci-test
```

---

## Testing Strategy

### Dev VM (primary)
1. SSH to dev VM: `ssh tam0013@databasesdev.lib.wvu.edu`
2. Navigate to Rails app directory
3. Pull latest from `rails7-circleci-test` branch or copy changed files directly
4. Restart Rails if needed (`bin/rails server` or Puma restart)
5. Open browser to `https://databasesdev.lib.wvu.edu/admin`
6. Log in via SSO
7. Click admin menu button — verify overlay covers 100% of viewport
8. Close menu — verify overlay disappears completely
9. DevTools check: overlay element's `offsetParent` should be `null` (confirms true fixed positioning)

### Production Parity Test
Compare against the production site (`databases.lib.wvu.edu/admin`). The overlay behavior must match exactly — same z-index layering, same opacity, same click-to-dismiss behavior.

### Browser DevTools Verification
```javascript
// Run in browser console after opening admin menu:
document.querySelector('.menu-overlay');
// Should be a direct child of <html>, NOT <body>
// offsetParent should be null (true fixed positioning)
```

---

## Acceptance Criteria (all must pass)

- [ ] Overlay element is a direct child of `<html>` (`document.documentElement`), not `<body>`
- [ ] 100% viewport width coverage — no light/gray areas visible at any edge
- [ ] 100% viewport height coverage — no light/gray areas visible at any edge
- [ ] Overlay opacity is `rgba(44,62,80,0.95)` (dark teal, 95% opacity)
- [ ] Overlay z-index is 100000 (above all content)
- [ ] Overlay disappears completely when menu closes (display: none or element removed)
- [ ] Clicking the overlay toggles menu close if that's a feature (verify parity with production)
- [ ] `offsetParent` of the overlay is `null` in DevTools (confirms true fixed positioning)
- [ ] No regressions — existing menu open/close functionality unchanged

---

## Synthesis Report Template

**Save this to**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md`

Paste BEFORE starting any work. Fill in after reviewing the task file and codebase:

```markdown
# Synthesis Report: overlay-viewport-fix

**Task**: 2026-10-01-MEDIUM-overlay-viewport-coverage.md
**Reviewed**: YYYY-MM-DD

## Understanding Summary
[One paragraph restating the problem in your own words]

## File Analysis Results
| File | Verified Exists? | Current State |
|------|-----------------|---------------|
| off_canvas.js | YES/NO | [summary] |
| _nav.scss | YES/NO | [summary] |

## Confirmed Approaches (check only what applies)
- [ ] One-line DOM target change (documentElement portal) — **RECOMMENDED**
- [ ] CSS-only fix using containment/pseudo-elements
- [ ] MutationObserver watching for transform changes
- [ ] Other: [describe]

## Architecture Concerns
[Any concerns about the approach — list them here]

## Plan Summary
[2-3 sentences of what you will do in order]

## Questions for Human
[Any clarifications needed — if none, write "None"]
```

---

## Handoff / Previous Work State

This task originated from a planning session that analyzed 3 solution approaches:
- **Approach A** (documentElement portal) — RECOMMENDED — one-line fix, most reliable
- **Approach B** (CSS containment) — Fallback if Approach A has issues
- **Approach C** (MutationObserver) — Not recommended — overengineered

A comprehensive planning document was created at:
`/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks/active/2026-10-01-overlay-viewport-fix-plan.md`

**Previous attempts that failed**: 7 CSS-only approaches + multiple JS dimension-compensation attempts. All failed because they appended the overlay to `document.body` which is inside the Hiraku CSS transform context.

**Branch**: `rails7-circleci-test` (pushed to origin)
**Dev VM**: `https://databasesdev.lib.wvu.edu/admin` (SSO auth working)
**Production**: `https://databases.lib.wvu.edu/admin`

---

## Implementation Record (2026-10-01)

### Changes Made
| Step | File | Change | Status |
|------|------|--------|--------|
| 1 | off_canvas.js | `document.body.appendChild()` → `document.documentElement.appendChild()` | ✅ Done |
| 2 | off_canvas.js | Dimensions: `-300px/-150px/calc(100vw+450px)` → `0/0/100vw/100vh` | ✅ Done |
| 3 | _nav.scss | No changes needed (JS inline styles handle positioning) | ✅ N/A |

### Git Info
- **Commit**: `576bcc1` on branch `rails7-circleci-test`
- **Message**: "Fix: offcanvas overlay viewport coverage via documentElement portal"
- **Pushed**: Yes, to origin

### Remaining Acceptance Criteria (needs human verification)
- [ ] 100% viewport width coverage — no light/gray areas at any edge
- [ ] 100% viewport height coverage — no light/gray areas at any edge
- [ ] `offsetParent` of overlay is `null` in DevTools (true fixed positioning)
- [ ] No regressions on menu open/close functionality

### Testing Needed
1. Pull `rails7-circleci-test` on dev VM or wait for deploy
2. Open `https://databasesdev.lib.wvu.edu/admin` as logged-in admin
3. Toggle admin menu — verify full viewport overlay coverage
4. Run in DevTools: `document.querySelector('.menu-overlay').offsetParent` (should be `null`)

### Status
Code is implemented and pushed. Awaiting visual verification on dev VM.
