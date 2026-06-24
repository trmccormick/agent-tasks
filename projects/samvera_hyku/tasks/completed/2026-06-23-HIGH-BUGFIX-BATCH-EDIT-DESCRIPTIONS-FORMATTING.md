---
status: approved
priority: HIGH
type: bug-fix
system_domain: Hyku 7 Release
mvp_alignment: Hyku 7 Regression Fix
review_status: ready-for-pr-after-css-refinement
approved_date: 2026-06-24
---

# TASK: Fix Batch Edit Descriptions Formatting Regression

**Status**: ✅ APPROVED (ready for PR after CSS refinement)
**Priority**: HIGH
**Type**: bug-fix
**GitHub Issue**: https://github.com/samvera/hyku/issues/2990
**Feature Branch**: `fix/batch-edit-descriptions-2990` (ready for PR)
**Created**: 2026-06-23 | **Approved**: 2026-06-24

## COMPLETION SUMMARY (Session 2026-06-24)

✅ **CSS FIX IMPLEMENTED & VERIFIED**: Lines 53-73 in `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss`
✅ **VISUAL VERIFICATION COMPLETE**: Batch edit description labels render horizontally on testing tenant
✅ **ASSET PRECOMPILATION VALIDATED**: No errors
✅ **FEATURE BRANCH CREATED**: `fix/batch-edit-descriptions-2990`
✅ **COMMIT PUSHED**: Ready for review
✅ **COMMUNITY APPROVED**: LaRita Robinson confirmed fix location is correct

## COMMUNITY FEEDBACK (2026-06-24 — Slack Review)

**LaRita Robinson** (Hyku Working Group):
- Investigated Hyrax rendering to validate placement
- Confirmed: "Based on this layout, I would say what you have is fine"
- **APPROVED**: Fix is in correct location (Hyku hyrax.scss)
- Ready to review PR: "Tag me when you have a PR up and green"

**Shana Moore** (CSS Standards):
- Team preference: Avoid `!important` in CSS when possible
- Current implementation uses `!important` for specificity
- **Requested**: Refactor CSS to achieve same result without `!important`

## CSS FIX LOCATION & IMPLEMENTATION

**File**: `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss` (lines 53-73)

**Current Implementation** (with `!important`):
```scss
.descriptions_display,
#descriptions_display {
  .accordion-toggle label,
  .accordion-toggle > label {
    display: inline !important;
    white-space: nowrap !important;
    letter-spacing: normal !important;
  }
  .accordion-toggle {
    display: block !important;
    line-height: normal !important;
  }
}
```

**Refinement Needed**:
Investigate whether specificity can be increased to avoid `!important` while preserving functionality. Options:
1. More specific selector targeting the template structure
2. Reorder CSS rules to increase cascade priority
3. Review Hyrax template to understand what styles are overriding these

## NEXT STEPS

1. 🔧 **CSS REFINEMENT**: Refactor to avoid `!important` (team standard)
2. 📋 **CREATE PR**: Once CSS is updated
3. 👥 **TAG LARITA**: For review and approval when PR is green

## RELATED REFERENCES

- Original fix: #2527 (Aug 28, 2025)
- PALS board: notch8/palni_palci_knapsack#386
- Hyrax upstream issue: samvera/hyrax#7071
- Hyrax reference commit: 60807663 (checked but LaRita confirmed Hyku fix is correct placement)
