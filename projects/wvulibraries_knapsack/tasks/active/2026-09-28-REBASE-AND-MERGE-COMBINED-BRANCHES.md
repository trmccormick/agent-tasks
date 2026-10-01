# Combined Branch Merge to Main (2026-09-28)

## Context
Two independent feature branches with complementary work need to be merged together:
1. `fix/hide-type-facet-add-show-more-facets` — Facet pagination + Collections button UX (Tracy's work)
2. `feature/date-created-range-facet` — Date range facet with EDTF indexing (Junior Dev's work)

**Goal**: Rebase date range work on facet fixes, test combined on hykudev, then merge everything to main in one PR.

---

## Prerequisites (User Must Confirm)

- [ ] Junior dev confirms `feature/date-created-range-facet` is complete and tested
- [ ] No new commits expected on either branch
- [ ] User approves combined merge strategy

**Status**: Waiting for user confirmation

---

## Task: Rebase + Merge Workflow

### Step 1: Checkout Date Range Branch (Local)

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git fetch origin feature/date-created-range-facet
git checkout feature/date-created-range-facet
git pull origin feature/date-created-range-facet
```

### Step 2: Rebase on Facet Fixes Branch

This ensures date range work gets all the facet pagination improvements:

```bash
git rebase fix/hide-type-facet-add-show-more-facets
```

**If conflicts occur:**
```bash
# Resolve conflicts in editor, then:
git add .
git rebase --continue
```

### Step 3: Force Push to Remote (Date Range Branch)

```bash
git push origin feature/date-created-range-facet --force-with-lease
```

### Step 4: Verify Rebase Success

```bash
git log --oneline -5
# Should show date range work on top of facet fixes
```

### Step 5: Test on hykudev

Switch hykudev to `feature/date-created-range-facet` and verify:
- ✅ Facet pagination working (inherited from rebased base)
- ✅ Collections button navigates to filtered catalog
- ✅ Date range facet appearing and functioning
- ✅ No console errors

```bash
# On hykudev:
git checkout feature/date-created-range-facet
git pull
sh up.sh  # Full restart
```

**Test URL**: https://demo-hykudev.lib.wvu.edu/

### Step 6: Create PR (Facet Fixes → Main)

Once hykudev testing passes:

```bash
# Create PR: fix/hide-type-facet-add-show-more-facets → main
# Title: "Facet Pagination & UX Improvements for Main Page"
# Body: Use PR documentation from /memories/session/pr_documentation.md
```

### Step 7: Create PR (Date Range → Main)

After first PR merged:

```bash
# Create PR: feature/date-created-range-facet → main
# Title: "Add Date Created range facet with EDTF year indexing"
# Body: Document the EDTF work, any new dependencies, testing done
```

**Commit to reference in PR**: `3dda4fd - Add Date Created range facet with EDTF year indexing`

### Step 8: Final Branch Cleanup

After BOTH PRs merged to main:

```bash
# Delete merged branches
git branch -d fix/hide-type-facet-add-show-more-facets
git branch -d feature/date-created-range-facet

git push origin --delete fix/hide-type-facet-add-show-more-facets
git push origin --delete feature/date-created-range-facet

git fetch --prune
```

---

## Acceptance Criteria

- [ ] Date range branch successfully rebased on facet fixes branch
- [ ] hykudev runs latest code from rebased branch
- [ ] All 6 testing checkpoints pass:
  - [ ] Homepage displays 5 facet values per field
  - [ ] "More" links appear on facets with 6+ items
  - [ ] "View All Collections" button navigates to filtered catalog
  - [ ] Type facet (generic_type_sim) remains hidden
  - [ ] Date Created range facet displays and functions
  - [ ] No console errors
- [ ] Both PRs created and merged to main
- [ ] Old branches deleted from local and remote

---

## Rollback Plan

If rebase conflicts are irresolvable:

```bash
git rebase --abort
git checkout -b feature/date-created-range-facet-merged
git merge fix/hide-type-facet-add-show-more-facets --no-ff
# Then continue with normal merge instead of rebase
```

---

## Notes for Qwen

- **Rebase vs Merge**: Rebase keeps history clean (preferred). Merge works if conflicts are complex.
- **Force Push**: Using `--force-with-lease` is safer than `--force` (won't overwrite others' work)
- **PR Order**: Must merge facet fixes PR first (it's the base). Date range PR builds on it.
- **Timeline**: This should take ~30-45 min total (rebase + test + create PRs)
- **User will approve PRs**: They're doing the merge approvals themselves

---

## Related Files
- PR Body Template: `/memories/session/pr_documentation.md`
- Branch Cleanup Task: `/memories/repo/` (use after merge)
- Test URLs:
  - hykudev: `https://demo-hykudev.lib.wvu.edu/`
  - Production: `https://digitalhistory.lib.wvu.edu/`

---

**Status**: Not started (waiting for user confirmation)
**Owner**: Qwen (post-confirmation from user)
**Estimated Time**: 45 minutes
**Trigger**: "Ready to proceed with rebase and merge"
