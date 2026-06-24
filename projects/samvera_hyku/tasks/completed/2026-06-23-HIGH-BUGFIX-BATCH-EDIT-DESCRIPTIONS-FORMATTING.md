---
status: ready-for-pr
priority: HIGH
type: bug-fix
system_domain: Hyku 7 Release
mvp_alignment: Hyku 7 Regression Fix
review_status: css-refinement-complete-ready-for-pr
approved_date: 2026-06-24
---

# TASK: Fix Batch Edit Descriptions Formatting Regression

**Status**: ✅ READY FOR PR (CSS refinement complete, no `!important`)
**Priority**: HIGH
**Type**: bug-fix
**GitHub Issue**: https://github.com/samvera/hyku/issues/2990
**Feature Branch**: `fix/batch-edit-descriptions-2990` (latest commit pushed)
**Created**: 2026-06-23 | **Approved**: 2026-06-24 | **CSS Refinement**: 2026-06-24

## FINAL SOLUTION (CSS Refinement Complete)

**File**: `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss` (lines 53-73)

**Final Implementation** (NO `!important`):
```scss
.descriptions_display,
#descriptions_display {
  // Fix the parent link's overflow-wrap behavior
  .accordion-toggle {
    display: block;
    line-height: normal;
    overflow-wrap: normal;
  }

  // Fix the label display and wrapping
  .accordion-toggle label,
  .accordion-toggle > label {
    display: inline;
    white-space: nowrap;
    letter-spacing: normal;
  }
}
```

**Root Cause**: Parent accordion-toggle link had `overflow-wrap: anywhere` which broke text character-by-character in narrow `col-sm-4` columns

**Solution**: Set `overflow-wrap: normal` on the parent link + `white-space: nowrap` on the label. This achieves the desired layout without `!important` by addressing the actual CSS property causing the wrapping.

**Status**: ✅ Tested and working horizontally

## COMMUNITY FEEDBACK & RESOLUTION

**LaRita Robinson** (Hyku Working Group):
- ✅ Approved location: "Based on this layout, I would say what you have is fine"
- Ready to review PR: "Tag me when you have a PR up and green"

**Shana Moore** (CSS Standards):
- ❌ Requested: Avoid `!important` in CSS
- ✅ Resolved: Removed all `!important` flags, solution targets root cause instead

## NEXT STEPS

📋 **READY FOR PR SUBMISSION**
1. ✅ CSS fix tested and working
2. ✅ No `!important` flags (team standard met)
3. ✅ Feature branch pushed with latest commit
4. 👤 Tag @LaRita for review when PR is created

## RELATED REFERENCES

- Original fix: #2527 (Aug 28, 2025)
- PALS board: notch8/palni_palci_knapsack#386
- Hyrax upstream issue: samvera/hyrax#7071
