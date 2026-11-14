## STATUS SYNTHESIS REPORT

**Task**: Replace Hiraku Admin Offcanvas with Bootstrap 5 Native Offcanvas
**Status**: active
**Date**: 2026-10-03

### What I'm About to Do
Replace the Hiraku-based admin offcanvas menu (which is unreliable under Turbo navigation) with Bootstrap 5.3's native `offcanvas-end` component. Preserve dark theme styling, right-side slide-in, full-viewport backdrop overlay, and all existing nav links. Remove all Hiraku.js dependencies from JS, SCSS, and package.json.

### Prerequisites Reviewed
| Item | Status | Notes |
|------|--------|-------|
| Workflow README (EXECUTOR Role) | ✅ Read | Synthesis-gated workflow confirmed |
| Project README | ✅ Read | Rails 7 + Bootstrap 5.3 project |
| Prior findings (`2026-09-25-ADMIN-OFFCANVAS-FINDINGS.md`) | ✅ Read | Root cause: Hiraku not initializing on Turbo nav; Option B (Bootstrap) recommended |
| Latest review (`2026-10-02-GROK-REVIEW-OVERLAY-VIEWPORT-FIX.md`) | ✅ Read | documentElement portal failed — CSS transform's containing block affects ALL fixed-position descendants regardless of depth |
| This task file | ✅ Read | All steps, gotchas, acceptance criteria reviewed |

### Architecture Gotchas Understood
1. **GOTCHA 1**: Must use full Bootstrap offcanvas HTML structure (`.offcanvas` + `.offcanvas-header` + `.offcanvas-body` + `btn-close`) — not just data-bs attributes on old markup
2. **GOTCHA 2**: Rails app is nested — paths start with `databases/databases/app/...`, NOT `databases/app/...`
3. **GOTCHA 3**: Must remove ALL Hiraku artifacts (interface.js, main.scss, package.json) and re-run `yarn install`
4. **GOTCHA 4**: Dark theme must be explicitly preserved — Bootstrap defaults are light

### Files I'll Reference/Edit
| File | Purpose | Status |
|------|---------|--------|
| databases/databases/app/views/admin/_navigation.html.erb | Rewrite to Bootstrap offcanvas structure | not started |
| databases/databases/app/views/layouts/admin.html.erb | Update menu button with data-bs-toggle | not started |
| databases/databases/app/assets/stylesheets/interface/elements/_nav.scss | Add dark-theme overrides for offcanvas | not started |
| databases/databases/app/assets/javascripts/interface.js | Remove hiraku require statement | not started |
| databases/databases/app/assets/stylesheets/interface/main.scss | Remove hiraku CSS import | not started |
| databases/databases/package.json | Remove hiraku dependency entry | not started |
| databases/databases/yarn.lock | Updated automatically by yarn install (commit) | not started |
| databases/databases/app/assets/javascripts/plugins/off_canvas.js | Delete or empty to no-op | not started |

### Prerequisites Completed Before Starting
- [x] Task file in `active/` with correct status (Step 0 done)
- [x] All prerequisites read in order
- [x] Architecture gotchas understood and noted
- [x] Synthesis report created and saved
- [x] Implementation steps reviewed and understood

### Expected Outcomes
After implementation:
1. Menu button opens a Bootstrap native right-side offcanvas with dark backdrop (full viewport)
2. All original nav links preserved and functional
3. Dark theme parity with production (dark bg, light text, dashed dividers, white close button)
4. No Hiraku references remain anywhere in codebase
5. `yarn install` + `rake assets:precompile` succeed without errors
6. No console errors on admin page

### Critical Gotchas I Will Avoid
- Don't just add data-bs attributes to old Hiraku markup — use full Bootstrap offcanvas HTML structure (GOTCHA 1)
- Don't leave Hiraku require/import/package entries — remove all three + yarn install (GOTCHA 3)
- Don't assume default Bootstrap styling is fine — explicit dark-theme overrides needed (GOTCHA 4)
- Don't use shallow `databases/app/...` paths — always use nested `databases/databases/app/...` (GOTCHA 2)

### Risk Assessment
| Risk | Level | Mitigation |
|------|-------|------------|
| Bootstrap JS not loaded in admin layout | LOW | Verify bootstrap bundle is included in admin assets |
| Dark theme mismatch with production | MEDIUM | Compare against production screenshots, iterate on SCSS overrides |
| Hiraku removal breaks something else | LOW | grep audit ensures all references removed; off_canvas.js can be reverted if needed |
| Asset pipeline cache issues | LOW | Precompile + restart container after changes |

### Next Steps (After Approval)
1. **Step 0**: Verify task location — `find agent-tasks/projects/wvulibraries_databases/tasks -name "*bootstrap-replace-hiraku*"` → confirm one file under tasks/active/
2. **Step 1**: Audit all remaining Hiraku usage across codebase
3. **Step 2-7**: Implement changes per task file (rewrite HTML, update button, add SCSS, remove Hiraku, precompile, verify)

**SYNTHESIS COMPLETE.** Ready to proceed after approval.
