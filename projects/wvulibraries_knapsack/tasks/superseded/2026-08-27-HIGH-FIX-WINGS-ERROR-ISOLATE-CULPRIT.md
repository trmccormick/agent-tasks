# TASK: Isolate Wings Error on fix/hide-type-facet-add-show-more-facets Branch

**Status**: Ready for Qwen dispatch  
**Priority**: HIGH (blocking feature deployment)  
**Estimated**: 2-3 hours  

## Problem Statement

The `fix/hide-type-facet-add-show-more-facets` branch has **Wings::ModelRegistry errors in background jobs** during imports, but **main branch imports cleanly**. 

**Evidence**:
- Main: 5,447 succeeded with 0 errors ✅
- fix/hide-type-facet-add-show-more-facets: Multiple Wings::ModelRegistry errors ❌
- Committed branch diff shows only 4 files changed (decorators, view template)
- **Hypothesis**: Uncommitted local initializer files are breaking job deserialization

## Investigation Goals

1. **Identify uncommitted changes** on the branch that aren't in the official diff
2. **Isolate the culprit file** breaking job deserialization
3. **Recommend fix path**: Either remove/fix the file, or cherry-pick cleanly from main

## Acceptance Criteria

✅ Identified which file(s) cause Wings error  
✅ Tested each file in isolation  
✅ Recommended solution: Keep facet-limiting feature without Wings errors  
✅ Created clean branch off main with only necessary changes  

## Steps

### 1. Checkout Branch & Inspect Uncommitted Changes

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git checkout fix/hide-type-facet-add-show-more-facets
git status
git diff --name-only  # Show uncommitted changes
git diff  # Show uncommitted content
```

**Look for**:
- `config/initializers/valkyrie_wings_guard.rb`
- `config/initializers/999_catalog_controller_decorator.rb`
- `app/search_builders/catalog_search_builder_wrapper.rb`
- Any other uncommitted files

### 2. List All Changes (Committed + Uncommitted)

```bash
# Committed files on branch vs main
git diff main --name-only

# All files (committed + local changes)
find . -name "*.rb" -newer $(git show -s --format=%ci $(git rev-parse main) | head -1) 2>/dev/null | grep -E "(initializer|search_builder)" | head -20
```

### 3. Binary Search to Isolate Culprit

For each suspected file:

```bash
# Test 1: Disable valkyrie_wings_guard.rb
mv config/initializers/valkyrie_wings_guard.rb config/initializers/valkyrie_wings_guard.rb.disabled
sh down.sc.local.sh && sleep 5 && sh up.sc.local.sh
# Run small import, check Sidekiq for Wings errors
# Result: YES error / NO error?

# If still errors, restore and test next file
mv config/initializers/valkyrie_wings_guard.rb.disabled config/initializers/valkyrie_wings_guard.rb
```

Repeat for each initializer/search builder file.

### 4. Compare Committed Branch Changes with Main

```bash
# Show all committed changes on branch
git diff main..fix/hide-type-facet-add-show-more-facets --stat
git diff main..fix/hide-type-facet-add-show-more-facets

# Files changed:
# - app/controllers/catalog_controller_decorator.rb (added begin/rescue protection)
# - app/controllers/hyrax/homepage_controller_decorator.rb (new, uses HomepageSearchBuilderWrapper)
# - app/search_builders/hyrax/homepage_search_builder_wrapper.rb (new)
# - app/views/themes/wvu_home/hyrax/homepage/_facet_limit.html.erb (modified)
```

### 5. Create Clean Branch Option

If uncommitted files are the issue:

```bash
# Option A: Start fresh from main with ONLY clean commits
git checkout main
git checkout -b fix/facet-limiting-clean
git cherry-pick <commit-sha-1> <commit-sha-2> ...
# Only cherry-pick commits that are safe

# Option B: Remove problematic files from current branch
git checkout fix/hide-type-facet-add-show-more-facets
rm config/initializers/valkyrie_wings_guard.rb
rm config/initializers/999_catalog_controller_decorator.rb
rm app/search_builders/catalog_search_builder_wrapper.rb
git add -A
git commit -m "remove: delete problematic initializers causing Wings job errors"
```

## Key Questions to Answer

1. **What exact files are uncommitted on the branch?**
2. **Which one breaks jobs?** (valkyrie guard? decorator initializer? search builder wrapper?)
3. **Are the committed facet-limiting changes safe?** (homepage/catalog decorators + view template)
4. **Recommended action**: Delete problem file(s)? Or create fresh branch from main?

## Success Metric

- [ ] Identified culprit file(s)
- [ ] Tested in isolation
- [ ] Branch or fix path recommended
- [ ] Wings errors eliminated
- [ ] Facet-limiting feature still works (test homepage & catalog)

## Notes

- **Don't**: Try to fix Wings initialization (that's treating symptom)
- **Do**: Find which file breaks job deserialization
- **Focus**: Binary search to isolate—fastest path is testing one file at a time
- **Test each result**: Small import after each file disable/enable to confirm Wings error status
