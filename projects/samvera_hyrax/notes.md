# Samvera Hyrax — Agent Work Notes & Discoveries

> Technical findings, gotchas, and contextual discoveries made during agent work on this upstream project.
> Updated by agents as they discover Hyrax-specific insights.
> Note: Hyrax issues often cascade to Hyku and downstream (Knapsack), so discoveries here are critical for multi-layer debugging.

---

## 2026-06-24: Batch Edit Component Issue — Arrow Placement (Upstream from #2990)

**Origin**: Hyku issue #2990 investigation revealed upstream Hyrax component issue
**Status**: Identified, needs investigation and potential fix upstream
**Related Issues**: samvera/hyrax#7071, Hyku#2990, Knapsack#386

**Problem**:
- Batch Edit Descriptions page renders with labels vertically (one char per line)
- Arrow should appear on same line as property name (architectural behavior)
- Issue exists in both Hyrax and downstream Hyku/Knapsack

**Prior Fix Reference**:
- Hyrax commit 60807663 had a fix for this
- Question: Was fix lost in subsequent Hyrax updates?
- Need to investigate what changed in batch edit component rendering

**Investigation Path for Future Agents**:
1. Check Hyrax commit 60807663 for original implementation
2. Review batch edit component template and styles in Hyrax core
3. Look for changes in accordion-toggle rendering between that commit and current main
4. Verify if upstream fix needs to be restored or applied differently

**Key Takeaway**: When Hyku CSS-only "fix" gets community pushback, check if the issue is actually architectural in the upstream component code rather than styling. Arrow placement is a layout/rendering order problem, not a CSS spacing issue.

**For Future Agents**:
- Create Hyrax PR against samvera/hyrax once root cause identified
- Hyku override (hyrax.scss) can be removed once upstream fix is in place
- Downstream projects (Hyku, Knapsack) will auto-inherit the fix on submodule update
