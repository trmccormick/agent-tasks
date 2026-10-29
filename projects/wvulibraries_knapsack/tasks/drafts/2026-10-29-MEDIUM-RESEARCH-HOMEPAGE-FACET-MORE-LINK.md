# TASK: Research — Restore homepage facet "More" link behavior (modal limited to 5 → redirect page showing all)

---
status: draft
priority: MEDIUM
type: bug-fix
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: false
resume: false
---

## Context

Homepage facets on hykudev (feature/hyku-v7.2.0-upgrade) show a "More" link that opens a **modal limited to 5 values** instead of the working behavior on main/production which redirects to `/catalog/facet/{field_name}?locale=en` and shows ALL facet values with pagination.

The "more link limit+1" code was deleted in commit `884c788` when we stripped our facet limiting workaround (assuming upstream Hyku PR #3369 handled it). That removal also broke the "more" link expansion behavior that main/production has working correctly.

## Research Goal

On hykudev, compare feature/hyku-v7.2.0-upgrade against main to determine:

1. **What exact config difference** causes homepage facets to show a limited modal vs. redirecting to `/catalog/facet/*` page
2. **Which file needs restored or modified** to restore the working behavior
3. Whether this is caused by:
   - Missing `app/search_builders/catalog_search_builder.rb` (restored in working tree but not committed)
   - Missing `config/initializers/catalog_search_builder.rb` (restored in working tree but not committed)
   - Missing `config/wvu_facet_defaults.yml` (restored in working tree but not committed)
   - Something in hyrax-webapp submodule between branches
   - Main branch having different config than what we have locally

## Steps on hykudev

```bash
# 1. Compare main vs feature branch for these key files
git diff main..feature/hyku-v7.2.0-upgrade -- config/initializers/catalog_controller_decorator.rb
git diff main..feature/hyku-v7.2.0-upgrade -- app/search_builders/
git diff main..feature/hyku-v7.2.0-upgrade -- config/initializers/catalog_search_builder.rb
git diff main..feature/hyku-v7.2.0-upgrade -- config/wvu_facet_defaults.yml

# 2. Check if decorator exists on main (it should)
git show main:config/initializers/catalog_controller_decorator.rb | head -10

# 3. Compare hyrax-webapp submodule between branches
git diff main..feature/hyku-v7.2.0-upgrade -- hyrax-webapp/config/initializers/blacklight.rb | head -80
git diff main..feature/hyku-v7.2.0-upgrade -- hyrax-webapp/app/controllers/catalog_controller.rb | head -80

# 4. Check if our restored files are correct by reverting:
git checkout feature/hyku-v7.2.0-upgrade -- .
git checkout HEAD~1 -- app/search_builders/catalog_search_builder.rb config/initializers/catalog_search_builder.rb config/wvu_facet_defaults.yml
# Then deploy and test on hykudev to verify the "more" link works correctly

# 5. If the files from main are different, copy them from main:
git checkout main -- <file_path>
```

## Acceptance Criteria

- [ ] Root cause identified (exact file + config difference)
- [ ] Working behavior restored on hykudev (homepage "more" → `/catalog/facet/*` showing all values)
- [ ] Verified via browser test on hykudev
- [ ] AC #5 still works (search results facets show 5 values collapsed)

## Files Restored in Working Tree (not yet committed)

| File | Status | Purpose |
|---|---|---|
| `app/search_builders/catalog_search_builder.rb` | Created but untracked | Enforces `limit+1` for Blacklight "more" link detection |
| `config/initializers/catalog_search_builder.rb` | Created but untracked | Registers CatalogSearchBuilder on CatalogController |
| `config/wvu_facet_defaults.yml` | Created but untracked | Default facet config with `show_more: true` |

## Stop Conditions

- If these files don't fix it, the issue is in hyrax-webapp submodule — escalate to user
- If main branch has completely different config, need to understand why and sync
