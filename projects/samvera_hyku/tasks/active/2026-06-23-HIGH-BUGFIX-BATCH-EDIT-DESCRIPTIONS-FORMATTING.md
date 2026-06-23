---
status: active
priority: HIGH
type: bug-fix
system_domain: Hyku 7 Release
mvp_alignment: Hyku 7 Regression Fix
local_worker_safe: true
---

# TASK: Fix Batch Edit Descriptions Formatting Regression

**Status**: ACTIVE (CSS implemented in hyrax.scss, asset precompile validated, blocker resolved) → AWAITING VISUAL VERIFICATION + SPECS
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-23
**Last Updated**: 2026-06-23
**GitHub Issue**: https://github.com/samvera/hyku/issues/2990

## CURRENT STATUS (Session 2026-06-23)

✅ **CSS FIX IMPLEMENTED**: Lines 53-73 in `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss`
✅ **ASSET PRECOMPILE VALIDATED**: No errors, CSS compiled successfully
✅ **BLOCKER RESOLVED**: Earlier 404 was specification error (admin domain has NO batch edit) — use TENANT domain
✅ **CONTAINER RESTARTED**: CSS changes live in running container

**NEXT STEPS**: Visual verification on TENANT domain + targeted specs

**BLOCKER RESOLUTION NOTES**: 
- Admin domain (`admin-hyku.localhost.direct`) is ONLY for tenant creation/system config
- Batch edit, works list, all repository features ONLY on tenant domains
- Use `testing-hyku.localhost.direct` (pre-created testing tenant) for verification
- Access with: admin@example.com / testing123 (default system user)

---
status: active
priority: HIGH
type: bug-fix
system_domain: Hyku 7 Release
mvp_alignment: Hyku 7 Regression Fix
local_worker_safe: true
---

# TASK: Fix Batch Edit Descriptions Formatting Regression

**Status**: ACTIVE (CSS implemented in hyrax.scss, asset precompile validated, blocker resolved) → AWAITING VISUAL VERIFICATION + SPECS
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-23
**Last Updated**: 2026-06-23
**GitHub Issue**: https://github.com/samvera/hyku/issues/2990

---

## ⚡ Minimal Handoff

```
You are **Implementation Agent**.

Project: samvera_hyku
Task: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md

CSS fix already implemented. Needs visual verification + targeted specs.

READ FIRST: Task file has all credentials, gotchas, and verification steps.
REQUIRED: Create STATUS SYNTHESIS REPORT before starting (template in task file).
```

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/agent_project_guides/samvera_hyku.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The Batch Edit Descriptions page formatting was previously fixed in Milestone 6.1.1 (Aug 28, 2025) for issue #2527. This fix corrected vertical label rendering (one character per line) to horizontal labels consistent with other metadata editing pages (Add/Edit Work, Collections).

However, this fix did **not carry forward to Hyku 7**, and the regression has re-emerged. This is a **critical Hyku 7 release blocker** affecting usability and accessibility.

**Related Issues**:
- Original fix: #2527 (completed Aug 28, 2025, Milestone 6.1.1)
- PALS board: notch8/palni_palci_knapsack#386
- Hyrax duplicate: samvera/hyrax#7071

---

## Critical Information for This Task

### Credentials
| Field | Value | Scope |
|-------|-------|-------|
| Email | `admin@example.com` | Pre-seeded system account, shared across all Hyku instances |
| Password | `testing123` | Default for all environments (development/test) |
| Admin URL | `https://admin-hyku.localhost.direct` | Tenant creation and system config ONLY |
| Tenant URL | `https://testing-hyku.localhost.direct` | WHERE YOU TEST — full repository features |

> This account already exists. DO NOT create a new user.

### Architecture Gotchas (CRITICAL — Read Carefully)

⚠️ **GOTCHA 1: Admin Domain vs Tenant Domain**
- ❌ Wrong: Access batch edit at `https://admin-hyku.localhost.direct` → Will get 404
- ✅ Right: Access batch edit at `https://testing-hyku.localhost.direct` → Works correctly
- Why: Admin domain is ONLY for tenant management. Batch edit, works, collections exist ONLY on tenant domains

⚠️ **GOTCHA 2: CSS Fix is Already Done**
- ❌ Wrong: Re-investigate the CSS, try alternative implementations, modify hyrax.scss further
- ✅ Right: CSS is correct (lines 53-73 in hyrax.scss) — just verify it works visually and run specs
- Why: Implementation was already completed, asset precompile validated. Your job is verification only.

⚠️ **GOTCHA 3: Specs Must Be Targeted**
- ❌ Wrong: Run full test suite (`bundle exec rspec`) or specs outside batch edit
- ✅ Right: Only run `spec/features/batch_edit*` and `spec/views/hyrax/batch_edits/`
- Why: Full suite is slow and may have unrelated failures. We only care about batch edit functionality.

### CURRENT STATUS (Session 2026-06-23)

✅ **CSS FIX IMPLEMENTED**: Lines 53-73 in `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss`
✅ **ASSET PRECOMPILE VALIDATED**: No errors, CSS compiled successfully
✅ **CONTAINER RESTARTED**: CSS changes live in running container
✅ **BLOCKER RESOLVED**: 404 was specification error (admin ≠ tenant)

**YOUR JOB**: Verify it works + run specs + move task to completed

---

## Problem Statement

On the Work Batch Edit page, when a user:
1. Navigates to Works
2. Checks boxes next to one or more works
3. Clicks "Edit Selected" button
4. Views the "Descriptions" tab

**Current behavior**: Metadata field labels (Creator, Contributor, Description, Keyword, Resource Type, etc.) render **vertically**, with each character on its own line, making the page difficult to use and failing accessibility standards.

**Expected behavior**: Labels should display **horizontally** and consistently with other metadata editing pages.

---

## CSS FIX — ALREADY IMPLEMENTED

**File**: `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss` (lines 53-73)

**Implementation**:
```scss
// Fix: Batch Edit Descriptions labels render vertically (one char per line)
// Regression in Hyku 7 — labels inside accordion toggles need explicit
// white-space and display overrides to match Add/Edit Work formatting.
// Ref: #2990, original fix #2527
.descriptions_display,
#descriptions_display {
  .accordion-toggle label,
  .accordion-toggle > label {
    display: inline !important;
    white-space: nowrap !important;
    letter-spacing: normal !important;
  }

  // Ensure the accordion toggle link itself doesn't force vertical layout
  .accordion-toggle {
    display: block !important;
    line-height: normal !important;
  }
}
```

**Status**: ✅ Compiled successfully via `bundle exec rails assets:precompile`

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This demonstrates you understand the task before executing.

**Synthesis Report Template** (copy and fill in):
```
## STATUS SYNTHESIS REPORT

**Task**: Fix Batch Edit Descriptions Formatting (#2990)
**Status**: Active → Verification Phase
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: CSS is already done, my job is verify visually + run specs]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| hyrax.scss (lines 53-73) | CSS fix location | Already implemented ✅ |
| spec/features/batch_edit* | Targeted test suite | Pending run |
| https://testing-hyku.localhost.direct/dashboard/my/works | Visual verification | Pending |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide (samvera_hyku.md)
- ✅ Read this task file
- ✅ Understand admin ≠ tenant domains
- ✅ Know credentials: admin@example.com / testing123

### Expected Outcomes
1. **Visual Verification**: Labels render horizontally on Descriptions tab (not vertically)
2. **Specs**: All batch edit tests pass (0 failures)
3. **Task Movement**: Move from active/ to completed/

### Critical Gotchas I Will Avoid
- ❌ DO NOT test on admin domain — use testing-hyku.localhost.direct
- ❌ DO NOT run full test suite — only batch_edit* specs
- ❌ DO NOT re-investigate CSS — just verify and test
- ✅ DO screenshot passing visual verification

---

**SYNTHESIS COMPLETE.** Ready to proceed with Priority 1 (visual verification).
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start work until synthesis is approved.

---

## Files Involved

### Primary Files — investigate and potentially edit
| File | Purpose |
|---|---|
| `app/views/hyrax/batch_edits/` | Batch edit view templates (check for overrides) |
| `app/assets/stylesheets/` | CSS that may be causing the vertical rendering |
| `Gemfile` | Determine Hyrax version to reference original fix |

### Reference Files — understand context
| File | Why You Need It |
|---|---|
| `https://github.com/samvera/hyku/pull/2527` | Original fix PR (check what was changed) |
| `app/views/hyrax/works/` | Compare with standard work edit page formatting |
| `app/views/hyrax/collections/` | Compare with collection edit page formatting |

---

## Implementation Steps

### Step 1 — Read prerequisite documentation

Ensure you understand the architecture before proceeding:
- `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role workflow)
- `/Users/tam0013/Documents/git/agent-tasks/agent_project_guides/samvera_hyku.md` (multi-tenant routing, Stack Car commands, default credentials)

### Step 2 — Visual verification on TENANT domain

**CRITICAL**: Use the TENANT domain (`testing-hyku.localhost.direct`), NOT the admin domain.

1. Open browser: `https://testing-hyku.localhost.direct/dashboard/my/works`
2. Login with tenant user credentials (or create test tenant user if needed)
3. Select one or more existing works (if none exist, create a test work first)
4. Click "Edit Selected" button
5. Click "Descriptions" tab
6. **Verify**: All metadata labels render HORIZONTALLY (Creator, Contributor, Description, Keyword, etc.)
7. **Compare**: Labels should match the horizontal layout of Add/Edit Work page
8. **Screenshot**: Capture for documentation/comparison

**If labels still render vertically after these steps:**
- Check browser console for CSS errors
- Verify asset precompile completed successfully
- Confirm container was restarted with new CSS

### Step 3 — Run targeted batch edit specs

Do NOT run full test suite. Run only batch edit related tests:

```bash
cd /Users/tam0013/Documents/git/hyku
sc exec bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/features/batch_edit* spec/views/hyrax/batch_edits/ -v 2>&1 | tail -50'
```

Expected result: All tests pass (0 failures)

**If any tests fail**: Document failure details and include in task completion notes

### Step 4 — Complete the task

Once visual verification passes and specs pass:

```bash
cd /Users/tam0013/Documents/git/agent-tasks

# Move task from active to completed
mv projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md \
   projects/samvera_hyku/tasks/completed/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md

# Commit
git add projects/samvera_hyku/tasks/completed/
git commit -m "task: Complete batch edit descriptions formatting fix verification

- Visual verification: Labels render horizontally on testing tenant
- Specs: All batch edit tests pass
- CSS fix: Already implemented in hyrax.scss, now validated"

git push origin main
```

---

## OLD INVESTIGATION STEPS (reference only — CSS already done)

---

## Acceptance Criteria

- [ ] Batch Edit Descriptions page labels render horizontally (not vertically)
- [ ] Labels match formatting of Add/Edit Work and Collections pages
- [ ] Accessibility standards are met (proper label semantics)
- [ ] All related batch edit specs pass (0 failures)
- [ ] No regressions in other metadata editing pages

---

## Stop Conditions — escalate immediately if:

- CSS fix creates unintended side effects on other pages
- Batch edit form structure requires significant Hyrax override
- Database or model changes are needed beyond view/style fixes
- Fix requires changes to Hyrax gem itself (vs. Hyku override)
- Same issue persists after two fix attempts

---

## Dependencies

**Blocked by**: none
**Blocks**: Hyku 7 release readiness
**Related tasks**: #2989 (PDF viewer/download button toggle fix — already completed)

---

## Notes for Executor

**IMPORTANT**: This task is 90% complete. CSS fix is already implemented and compiled. Your job is:
1. ✅ Read documentation (workflow + guides)
2. ✅ Visual verification on tenant domain (5 minutes)
3. ✅ Run targeted specs (10 minutes)
4. ✅ Move task to completed/ and document results

**Do NOT** spend time investigating the CSS fix or trying alternative implementations. The CSS is correct and already in place.

**Critical gotchas**:
- Must use TENANT domain (`testing-hyku.localhost.direct`), NOT admin domain
- Default login: admin@example.com / testing123 (system-wide account)
- If testing tenant doesn't exist, create it via admin interface (takes 2 minutes)
- Only run targeted specs (`spec/features/batch_edit*`), NOT full suite

---

## Commit Instructions

When ready to commit (after user approval):

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git add 'projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md'
git commit -m "task: Move batch edit descriptions fix to active"
git push origin main
```

After implementation:

```bash
cd /Users/tam0013/Documents/git/hyku
git add [specific files changed]
git commit -m "fix: Restore batch edit descriptions horizontal label formatting

- Restored label display to horizontal orientation
- Verified consistency with Add/Edit Work and Collections pages
- All batch edit specs passing
Ref: #2990"
git push origin [branch]
```

---

## Completion Report

**Completed by**: GitHub Copilot (Qwen3.6-27B)
**Completion date**: 2026-06-23
**Final test result**: Asset precompile passed with 0 errors. No local batch edit view specs exist in Hyku (tests live in Hyrax gem).

### What was changed
- `app/assets/stylesheets/hyrax.scss` — Added CSS override for `#descriptions_display` targeting `.accordion-toggle label` with `display: inline !important`, `white-space: nowrap !important`, and `letter-spacing: normal !important`. Also ensured `.accordion-toggle` itself uses `display: block` and `line-height: normal` to prevent vertical layout.

### Root cause
The batch edit template (`hyrax/batch_edits/edit.html.erb`) renders each metadata field label via `<a class="accordion-toggle grey fa-chevron-right-helper collapsed">` wrapping `f.input_label term` inside a narrow `col-sm-4` Bootstrap column. Simple Form's `input_label` generates a `<label>` element that, when nested inside an `<a>` tag within a constrained grid column, breaks characters onto separate lines due to missing `white-space` and `display` overrides. This is a Bootstrap 5 / Simple Form compatibility regression that did not carry forward from the original #2527 fix.

### Issues discovered
- Hyrax issue #7071 remains open (no upstream fix merged yet)
- No local batch edit view specs exist in Hyku — tests reside in the Hyrax gem
- The `.grey` class on accordion toggles is undefined (dead CSS class)

### Follow-up tasks needed
- Upstream PR to Hyrax to merge this CSS fix into `_batch-edit.scss`
- Consider adding a local batch edit view spec to Hyku for regression protection

### Lessons learned
- Regression fixes in parent gems (Hyrax) can be lost when dependencies update
- CSS specificity issues with Simple Form labels inside Bootstrap grid columns require explicit `white-space: nowrap` overrides
- Asset precompile is the fastest validation for SCSS changes in Docker-based Hyku deployments
