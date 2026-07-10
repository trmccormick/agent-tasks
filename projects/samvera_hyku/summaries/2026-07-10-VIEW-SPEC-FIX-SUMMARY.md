# Summary: View Spec Failures Fix — _head_tag_content.html.erb Restoration

**Date**: 2026-07-10
**Project**: Samvera Hyku
**Status**: Fix applied, awaiting verification
**Commit**: 5b8ce8a3 (Hyku repo)

---

## Executive Summary

CI reported 10 test failures in `spec/views/layouts/_head_tag_content.html.erb_spec.rb`. Root cause identified: view template file was deleted in an earlier refactor (commit 472fb339), causing all view spec tests to fail. Fix has been applied and committed; awaiting local verification.

---

## The Problem

### What Was Broken
- **Test file**: `spec/views/layouts/_head_tag_content.html.erb_spec.rb`
- **Number of failures**: 10 (all identical root cause)
- **Failure pattern**: All assertions about "Samvera Hyku" generator tag failed

### Root Cause Analysis

**Commit 472fb339** ("refactor: minimal generator tag override using Hyrax helper pattern") contained:
```
- Remove full _head_tag_content.html.erb duplication (no longer needed)
- Create _generator_meta_tag.html.erb partial (3 lines) to override identification
```

This deletion created the failure cascade:
1. ❌ View file deleted: `app/views/layouts/_head_tag_content.html.erb`
2. ✅ Spec still tries to render the view
3. ❌ View not found → Rails uses Hyrax fallback view
4. ❌ Hyrax view has `"Samvera Hyrax"` generator tag (not "Samvera Hyku")
5. ❌ All 10 tests expecting "Samvera Hyku" fail

**Why this matters**: Tests weren't actually testing Hyku's override behavior anymore—they were testing Hyrax's default behavior.

---

## The Solution Applied

### What Was Fixed

**Commit 5b8ce8a3** ("fix: Restore _head_tag_content.html.erb with hyku_generator_meta_tag helper"):

1. ✅ **Restored** `app/views/layouts/_head_tag_content.html.erb` (41 lines)
2. ✅ **Updated** generator tag line to: `<meta name="generator" content="<%= hyku_generator_meta_tag %>" />`
3. ✅ **Removed** unnecessary `_generator_meta_tag.html.erb` partial

### How It Works

The restored view:
- Contains all required head tag elements (csrf, charset, viewport, resourcesync, generator)
- Uses `hyku_generator_meta_tag` helper method to output generator tag
- Helper method defined in `app/helpers/hyku_helper.rb`: returns `"Samvera Hyku #{::Hyku::VERSION}"`
- Maintains full test coverage while minimizing code changes

**View snippet** (line 13):
```erb
<meta name="generator" content="<%= hyku_generator_meta_tag %>" />
```

---

## Failing Tests (What Will Pass After Fix)

All 10 tests in `_head_tag_content.html.erb_spec.rb`:

1. `renders Hyku generator meta tag, not Hyrax` (line 8)
2. `includes correct Hyku version in generator tag` (line 14)
3. `does NOT contain Samvera Hyrax generator tag` (line 21)
4. `generator tag format is valid HTML5` (line 28)
5. `includes CSRF protection meta tag` (line 37)
6. `includes charset meta tag` (line 43)
7. `includes viewport meta tag for responsive design` (line 49)
8. `includes resourcesync link` (line 55)
9. `renders all required elements in correct order` (line 63)
10. `does not break stylesheet or javascript inclusion` (line 77)

---

## What Needs to Happen Next

### Verification Required (LOCAL)

Run specs in Hyku repo to confirm fix:

```bash
cd /Users/tam0013/Documents/git/hyku
bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v
```

**Expected outcome**: All 10 tests PASS ✅

### After Local Verification

Once local specs pass:
1. Check CI job status (specs should pass in CI too)
2. Close this investigation
3. Proceed with merging `fix/generator-tag-hyku-identification` PR

---

## Files Changed

| File | Change | Repo |
|------|--------|------|
| `app/views/layouts/_head_tag_content.html.erb` | Restored (41 lines) | Hyku |
| `app/views/layouts/_generator_meta_tag.html.erb` | Deleted (no longer needed) | Hyku |
| `app/helpers/hyku_helper.rb` | Added `hyku_generator_meta_tag` helper (unchanged by fix) | Hyku |

---

## Git References

- **Fix commit**: 5b8ce8a3 (Hyku repo, `fix/generator-tag-hyku-identification` branch)
- **Branch**: `fix/generator-tag-hyku-identification`
- **Push status**: ✅ Pushed to GitHub
- **Task file**: Updated with root cause analysis and next steps

---

## Key Takeaway

The view spec failures were **not** caused by Devise/Warden test isolation issues or logging configuration changes. They were caused by a deleted view template from a prior refactor. The fix restores the view file while maintaining the minimal code override pattern via the helper method.

**To proceed**: Run `bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v` in Hyku repo to verify all 10 tests pass.
