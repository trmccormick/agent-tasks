# Branch Cleanup After Merge (2026-09-28)

## Context
After `fix/hide-type-facet-add-show-more-facets` is merged to `main`, we need to clean up old branches and ensure repo hygiene.

**Trigger**: Once PR is merged to main and tested on production

---

## Task: Clean Up Old Branches

### Prerequisites
- ✅ PR merged to main
- ✅ Testing complete on hykudev
- ✅ Production deployment confirmed

### Branches to Delete (Local + Remote)

**Delete These:**
1. `feature/date-created-range-facet` — Old experimental branch (no longer needed)
2. `fix/facet-links-and-hide-type-facet` — Old PR branch (functionality merged into current PR)

**Keep These:**
1. `main` — Production
2. `fix/hide-type-facet-add-show-more-facets` — Will be kept for reference (optional: can delete after confirmed stable)
3. `required_for_knapsack_instances` — Keep (might be needed for multi-tenant setup)

### Commands to Execute

**Step 1: Verify branches to delete**
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git branch -a
```

**Step 2: Delete locally**
```bash
git branch -d feature/date-created-range-facet
git branch -d fix/facet-links-and-hide-type-facet
```

If either branch has unmerged commits and you get error, use force:
```bash
git branch -D feature/date-created-range-facet  # Force delete
```

**Step 3: Delete from remote**
```bash
git push origin --delete feature/date-created-range-facet
git push origin --delete fix/facet-links-and-hide-type-facet
```

**Step 4: Verify cleanup**
```bash
git branch -a
# Should show:
#   main
#   required_for_knapsack_instances
#   remotes/origin/main
#   remotes/origin/required_for_knapsack_instances
#   remotes/origin/fix/hide-type-facet-add-show-more-facets (optional)
```

**Step 5: Prune local references**
```bash
git fetch --prune
```

---

## Acceptance Criteria

- [ ] `feature/date-created-range-facet` deleted locally
- [ ] `feature/date-created-range-facet` deleted from remote
- [ ] `fix/facet-links-and-hide-type-facet` deleted locally
- [ ] `fix/facet-links-and-hide-type-facet` deleted from remote
- [ ] `git branch -a` output only shows expected branches
- [ ] `git fetch --prune` completes without errors
- [ ] Verified on GitHub: old branches no longer in branch list

---

## Notes for Qwen

- These are old branches from previous iterations of the facet work
- Deleting them reduces clutter and makes repo easier to navigate
- The main work is now in `fix/hide-type-facet-add-show-more-facets` (or merged to main)
- Safe to delete — all commits are either merged or no longer needed
- Use force delete (`-D`) only if you get "unmerged commits" error
- After cleanup, user can verify in GitHub UI (Settings → Branches)

---

## Related Work
- **PR**: `fix/hide-type-facet-add-show-more-facets` → `main`
- **Testing**: hykudev (demo-hykudev.lib.wvu.edu)
- **Production**: hyku (digitalhistory.lib.wvu.edu)

---

**Status**: Not started (waiting for merge completion)
**Owner**: Qwen (post-merge)
**Estimated Time**: 5 minutes
