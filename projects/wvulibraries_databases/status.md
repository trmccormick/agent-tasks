# WVU Libraries Databases — Project Status & Task Tracking
**Last Update:** 2026-10-05 — ✅ Hiraku Bootstrap refactor task moved to active (ready for Qwen dispatch)

---

## Project Overview
Databases — WVU Libraries resource discovery and catalog indexing system (Ruby on Rails).

**Context**: Rails 7 modernization in progress. Admin panel offcanvas navigation works but uses unreliable Hiraku.js. Bootstrap 5 native refactoring task ready for implementation.

---

## Current Status
- **Status:** 🔄 **HIRAKU → BOOTSTRAP REFACTOR IN PROGRESS** — Task active, ready for dispatch
- **Focus:** Replace Hiraku admin offcanvas with Bootstrap 5.3 native component (full viewport overlay, preserve dark theme)
- **Active Branch:** `rails7-circleci-test`
- **Last Session:** 2026-10-05 (completed governance work, prepped Hiraku task for dispatch)
- **Last Update:** 2026-10-05 — Task moved to active/, synthesis report created, ready for Qwen

---

## ✅ COMPLETED — Session 2026-10-05: Repository Sync & Task Prep

**Summary**: Resolved git conflicts in agent-tasks repo, successfully synced governance documentation (MAG-1 through MAG-6), committed wvulibraries_databases task files and handoffs, moved Hiraku refactor task to active status.

### Governance Work (Perplexity Sessions)
- ✅ MAG-1 through MAG-6 task files created and committed
- ✅ Phase 1 review extract documented
- ✅ All governance files pushed to remote (commit 372010b, c827a02, and earlier)
- ✅ Symlink issues resolved and documented in .gitignore

### Repository Infrastructure
- ✅ Resolved git rebase conflict with untracked overlay viewport files
- ✅ Committed 4 new wvulibraries_databases files:
  - `handoffs/2026-10-01-overlay-fix-handoff.md`
  - `summaries/2026-10-01-MEDIUM-overlay-viewport-fix.md`
  - `tasks/active/2026-10-01-MEDIUM-overlay-viewport-coverage.md`
  - `tasks/active/2026-10-01-overlay-viewport-fix-plan.md`
- ✅ Successfully pushed all changes to remote
- ✅ Verified repo state is clean and in sync

### Hiraku Refactor Task Prep
- ✅ Moved task from backlog → active
- ✅ Created comprehensive synthesis report (2026-10-03-SYNTHESIS-bootstrap-replace-hiraku.md)
- ✅ Task file includes all prerequisites, gotchas, and acceptance criteria
- ✅ Ready for Qwen dispatch

---

## ✅ COMPLETED — Session 2026-08-03: Production Bug Fixes + Infrastructure Modernization + UI Restoration

**Summary**: Fixed critical production bug (subject deletion failure), improved test coverage to 97.38%, modernized CI/CD infrastructure, synchronized Node.js versions, and restored flash message styling.

### Bug Fixes & Feature Work

| Task | Change | Branch | Status |
|---|---|---|---|
| **Production Bug: Subject Deletion Failure** | Changed `dependent: :nullify` → `dependent: :destroy` in `app/models/subject.rb` because `databases_subjects` table has `subject_id NOT NULL` constraint | rails7-circleci-test | ✅ FIXED |
| **Commit**: 0ba2b2d | Added 2 comprehensive tests in `spec/models/subject_spec.rb` to verify join record deletion and database persistence | — | ✅ |

### Infrastructure & Test Quality

| Task | Change | Files Modified | Status |
|---|---|---|---|
| **Test Coverage Boost** | Enhanced RSpec suite from 94.43% → **97.38% coverage** (466 examples, 856/879 lines) | spec/ | ✅ COMPLETE |
| **Test Data Consistency** | Updated `db/seeds.rb` with 56 academic subjects + database associations for realistic testing | db/seeds.rb | ✅ |
| **SimpleCov Conditional Loading** | Made SimpleCov conditional on `ENV["COVERAGE"]` to prevent bootsnap bytecode compilation conflicts | spec/spec_helper.rb | ✅ |
| **CircleCI Version Upgrade** | Upgraded `.circleci/config.yml` from version 2 (EOL) → **version 2.1** with modernized cache syntax | .circleci/config.yml | ✅ |
| **Test Result Parsing** | Added `--format RspecJunitFormatter` to CircleCI RSpec command for proper XML test result generation | .circleci/config.yml | ✅ |
| **Node.js LTS Sync** | Upgraded Node.js **18 → 20 LTS** in both `.circleci/config.yml` and `Dockerfile` (version 18 EOL April 2024) | .circleci/config.yml, Dockerfile | ✅ |

**Verification Results:**
- ✅ RSpec: 466 examples, 0 failures, 97.38% coverage
- ✅ CircleCI config: Version 2.1 syntax validated
- ✅ Node.js: Consistent across all environments (CircleCI, Docker, Dockerfile)
- ✅ All changes committed and pushed to rails7-circleci-test

### UI Regression Fixes

| Task | Change | Files Modified | Status |
|---|---|---|---|
| **Flash Message Styling Regression** | Fixed Bootstrap alert class names from `alert <%= key %>` → `alert alert-<%= key %>` | app/views/utilities/_alerts.html.erb | ✅ FIXED |
| **Commit**: 0ba8293 | Success messages now display with green banner, error messages with red banner | — | ✅ |
| **Styling Simplification** | Removed custom CSS that was breaking layout, relied on Bootstrap 5.3.0 native alert styling | — | ✅ |

**UI Verification:**
- ✅ Colored banners appear on subject deletion (green success banner with "Deleted the subject")
- ✅ Close button (×) visible and functional
- ✅ Flash message text displays correctly
- ✅ UI team can now refine spacing/width as needed

---

## Commits Made (2026-08-03)

```
0ba2b2d fix: change subject dependent destroy strategy from nullify to destroy
        - Rationale: databases_subjects table has NOT NULL constraint on subject_id
        - Added 2 tests verifying join record deletion and database persistence
        
0ba8293 fix: restore flash message styling by using Bootstrap alert classes
        - Changed 'alert <%= key %>' to 'alert alert-<%= key %>'
        - Restores green banner for success, red for error, etc.
        
218e255 fix: simplify alert styling to use basic block layout
        
30c7863 fix: use viewport width for alerts to extend full page width
        
9664349 style: add alert styling to ensure full-width display
        
bc573c9 fix: remove custom alert styling and rely on Bootstrap defaults
        - Removed _alerts.scss, using Bootstrap 5.3.0 native styling
```

---

## PENDING — Session 2026-10-01: Offcanvas Overlay Viewport Coverage (Visual Polish)

**Summary**: Admin offcanvas menu navigation fully functional (menu opens/closes, all links work). However, dark overlay backdrop doesn't cover the full viewport — light/gray areas remain visible at edges.

### Problem Statement

**Current (dev/localhost):** Overlay covers ~60% of viewport; white areas visible on left and bottom edges  
**Expected (production):** Overlay covers 100% of viewport; entire page darkened except menu panel  
**Impact:** Visual inconsistency only; menu is fully functional and doesn't block usage

### Root Cause Analysis

Hiraku library applies `transform: translateX(-300px)` to body when menu opens, creating a new CSS stacking context that breaks:
- `position: fixed` positioning (becomes relative to transformed body, not viewport)
- `position: absolute` positioning (only covers body element, not full viewport)
- Dimension/offset compensation math

### Work Attempted Today (2026-10-01)

1. ✅ Identified root cause: CSS transform stacking context issue
2. ✅ Tried CSS-only fixes (7 attempts) — all failed due to transform context
3. ✅ User implemented JavaScript overlay solution in `off_canvas.js` with:
   - Dynamic overlay div creation with expanded dimensions
   - Negative offset positioning to counter body transform
   - MutationObserver on nav `aria-hidden` attribute for show/hide
4. ❌ JS solution incomplete — still has viewport coverage gaps
5. ✅ Created formal task file: `tasks/active/2026-10-01-MEDIUM-overlay-viewport-coverage.md`

### Files Modified

- `databases/app/assets/javascripts/plugins/off_canvas.js` — overlay creation + MutationObserver
- `databases/app/assets/stylesheets/interface/elements/_nav.scss` — overlay styling (partial)

### Next Steps (For Qwen Planning Session)

**Pending Debug & Implementation:**
- [ ] Review current JS overlay implementation for dimension/offset calculation errors
- [ ] Test on dev VM (`https://databases.lib.wvu.edu/admin`) with SSO auth
- [ ] Compare with production site screenshot for exact overlay behavior
- [ ] Determine if overlay needs different DOM positioning strategy
- [ ] Implement proper fix ensuring full 100% viewport coverage
- [ ] Verify no regression to menu functionality

**Testing**: Dev VM has working auth; local requires SSO workaround

**Priority:** MEDIUM (visual polish, not blocking functionality)

**Estimated Effort:** 2-4 hours including debugging + implementation + testing

---

1. ✅ **PR Review**: rails7-circleci-test branch ready for code review
2. **Deployment**: Merge to main when ready
3. **Optional**: Remaining 23 lines of test code could reach 100% coverage (would require production error scenario testing)

---

## Active Tasks

### 2026-09-25: HIGH — Replace Hiraku Admin Offcanvas with Bootstrap 5 Native Offcanvas
**Status**: ACTIVE (ready for dispatch)  
**Assigned**: Qwen local via Copilot  
**Task File**: `tasks/active/2026-09-25-HIGH-refactor-bootstrap-replace-hiraku-admin-offcanvas.md`  
**Synthesis Report**: `summaries/2026-10-03-SYNTHESIS-bootstrap-replace-hiraku.md`  

**What it does:**
- Replace Hiraku.js dependency with Bootstrap 5.3 native offcanvas component
- Preserve dark theme styling, right-side slide-in animation, and full-viewport dark backdrop
- Remove all Hiraku artifacts from codebase (JS, SCSS, package.json)
- Maintain all existing admin nav links

**Critical gotchas to avoid:**
1. Must use full Bootstrap offcanvas HTML structure (`.offcanvas` + `.offcanvas-header` + `.offcanvas-body`)
2. Rails app nested at `databases/databases/app/...` (not `databases/app/...`)
3. Must remove ALL Hiraku references and run `yarn install` after package.json change
4. Dark theme must be explicitly preserved via SCSS overrides

**Dispatch ready**: ✅ Synthesis complete, all prerequisites documented

---

## Completed Tasks
- **2026-08-03**: Subject deletion bug fixed (dependent: destroy strategy)
- **2026-08-03**: Test coverage boosted from 94.43% → 97.38%
- **2026-08-03**: SimpleCov made conditional to prevent bootsnap conflicts
- **2026-08-03**: Seeds.rb updated with 56 academic subjects
- **2026-08-03**: CircleCI upgraded from v2 → v2.1
- **2026-08-03**: Node.js synchronized 18 → 20 LTS across all environments
- **2026-08-03**: Flash message styling regression fixed
- **2026-07-28**: MySQL 8.4.10 upgrade + CircleCI/Docker alignment (branch `review-rails7`)
- **2026-07-24**: Agent tasks folder structure created for databases project

---

---

## Backlog

### Overlay Viewport Coverage (Merged into Hiraku Refactor)
- **Status**: Now part of the active Hiraku → Bootstrap refactor task
- **Rationale**: Original Hiraku overlay was incomplete. Full Bootstrap refactor eliminates the problem entirely by using native `offcanvas` component with proper backdrop
- **No separate action needed**: Covered by 2026-09-25 task

### UI Team Refinement
- **Flash message banner spacing/width** can be tuned by UI team (no code changes needed)
  - Currently displays with Bootstrap 5.3.0 default styling
  - Colors and functionality are restored
  - Files: `app/views/utilities/_alerts.html.erb` (if layout changes needed)

