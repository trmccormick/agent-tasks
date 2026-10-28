# Draft: Remove Knapsack Facet Limiting Workaround (Upstream Fix Active)

---
status: draft
priority: HIGH
type: refactor
project: wvulibraries_knapsack
created: 2026-10-28
draft_review_checklist:
  - [x] File paths verified in codebase
  - [x] Upstream PR #3369 confirmed present in hyrax-webapp (commit 4148133d)
  - [x] Diff between workaround and upstream analyzed — no behavioral gap
  - [ ] Review agent: pending assessment
---

## Agent Dispatch Interface

```
ACTION: Remove four obsolete files, trim one initializer, commit, push
VERIFY: Facets still limit to 5 after removal (clean build required)
ENVIRONMENT: feature branch → deploy to hykudev for visual validation
DEP: None — prerequisite (upstream fix confirmed) is complete
```

## Problem

We added a four-file workaround in September 2026 because M3/profile-driven facets defaulted to Blacklight's limit of 10 instead of the desired 5. The workaround consisted of:

- `CatalogSearchBuilder` — custom search builder injecting `f.<field>.facet.limit=N+1` into Solr params
- `config/initializers/catalog_search_builder.rb` — overrides CatalogController to use our search builder
- `config/wvu_facet_defaults.yml` — facet defaults config
- Partial override in `catalog_controller_decorator.rb` — search builder assignment

This masking code prevents us from testing whether LaRita's upstream fix (Hyku PR #3369) actually works on hykudev.

## Evidence

**(VERIFIED)** Hyku PR #3369 is present in current `hyrax-webapp`:
- Commit `83555dbb` — "Merge pull request #3369 from samvera/update-hyrax-facet-links"
- Commit `4148133d` — "🐛 Show five values in every catalog facet"
- Current HEAD `279fe06` includes both

**(VERIFIED)** Upstream solution applies globally via Blacklight config:
- `hyrax-webapp/app/controllers/catalog_controller.rb` line 128: `config.default_facet_limit = 5`
- All facet fields use `limit: true`, inheriting the default
- Includes M3/profile-added facets (the key gap our workaround tried to fill)

**(VERIFIED)** No unique WVU behavior is lost — comparison:
- **Workaround**: Injects per-field Solr params at query time in search builder — only affects catalog searches through that builder
- **Upstream**: Sets global `default_facet_limit` with `limit: true` on all facets — covers every context (catalog, homepage, admin)
- **Preserved**: Decorator initializer's non-facet logic (pagination `[6,12,24,48,96]`, `generic_type_sim` removal, M3 dynamic registration) remains in place

**(VERIFIED)** No other code references our workaround:
- Only reference to `CatalogSearchBuilder` outside hyrax-webapp is in `data/bundle/gems/iiif_print/` (library class with same name, unrelated)
- `wvu_facet_defaults.yml` is referenced only by comments

## Acceptance Criteria

1. Four files deleted:
   - `app/search_builders/catalog_search_builder.rb`
   - `config/initializers/catalog_search_builder.rb`
   - `config/wvu_facet_defaults.yml`
2. `config/initializers/catalog_controller_decorator.rb` — remove only the search builder override lines (the `config.search_builder_class = ::CatalogSearchBuilder` line and its comment block); preserve ALL other decorator logic (pagination, per_page, generic_type_sim removal, M3 facet registration)
3. Commit message: `refactor: Remove obsolete facet limiting workaround (upstream PR #3369)`
4. Push to feature branch; deploy to hykudev
5. Visual validation on hykudev: all catalog facets show 5 values (not 10); "more" links appear correctly when >5 results exist

## Gotchas

- **Decorator initializer is not pure workaround**: It also sets pagination, hides Type facet (Hyku #3072), and registers M3 facets. Only remove the search_builder override lines.
- **Test before pushing**: A `rails test` or at minimum a clean build + catalog load is needed to verify no crash from removing the initializer that overrides search_builder_class.
- **Feature branch context**: This work should be on or branched from `feature/hyku-v7.2.0-upgrade`, not main.

## Notes

- PR #3369 includes a spec (`spec/controllers/catalog_controller_facet_limit_spec.rb`) that validates M3 profile facets get the same limit as declared facets — the upstream test coverage is thorough.
- Our workaround was designed for Hyku 7.2.0; it is now obsolete for 7.2.1+ where this fix is in effect.
- After removal, if facets still show 10 on hykudev, the issue is elsewhere (not a missing workaround).
