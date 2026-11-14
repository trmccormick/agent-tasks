# Implementation Closeout Handoff — Facet Limiting Workaround Removal

**Date:** 2026-10-28
**Agent:** Implementation Agent (copilot)
**Project:** wvulibraries_knapsack
**Task:** `projects/wvulibraries_knapsack/tasks/completed/2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md`

---

## 1. Task

`2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md` — Remove obsolete facet-limiting workaround files (upstream PR #3369 provides global fix).

## 2. What Was Done

### Code Changes (wvu_knapsack repo)
Three workaround files deleted:
- `app/search_builders/catalog_search_builder.rb` — Custom search builder that injected per-field `facet.limit+1` Solr params
- `config/initializers/catalog_search_builder.rb` — Initializer that overrode CatalogController to use custom search builder
- `config/wvu_facet_defaults.yml` — YAML-driven facet defaults config

Surgical decorator edit:
- `config/initializers/catalog_controller_decorator.rb` — Removed `config.search_builder_class = ::CatalogSearchBuilder` line + associated comment block + YAML-driven config block (`yaml_path`/`yml_config`/`force_fields` logic). **Preserved:** pagination `[6,12,24,48,96]`, Type facet removal (`generic_type_sim`), M3 dynamic facet registration from FlexibleSchema.

### Summary Report
Saved to: `projects/wvulibraries_knapsack/summaries/2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md`

## 3. Commits

| Commit | Branch | Message | Status |
|--------|--------|---------|--------|
| `884c788` | `feature/hyku-v7.2.1-upgrade` | refactor: Remove obsolete facet limiting workaround (upstream PR #3369) | Pushed ✅ |

**Stats:** 3 files deleted, 84 lines removed.

## 4. Status

**COMPLETE — Code (ACs 1–4)**
- ✅ AC #1: Three workaround files deleted
- ✅ AC #2: Decorator trimmed (search builder override only; all other logic preserved)
- ✅ AC #3: Commit message matches spec
- ✅ AC #4: Pushed to `feature/hyku-v7.2.1-upgrade`

**PENDING — Visual Validation (AC #5)**
- ⏳ AC #5: Human must verify on hykudev that all catalog facets show 5 values (not 10) and "more" links appear correctly when >5 results exist.
- Source of limit=5: Upstream Hyku PR #3369 (`config.default_facet_limit = 5` in `hyrax-webapp/app/controllers/catalog_controller.rb:128`)

## 5. Next Action

**Human/Reviewer:** Visual validation on hykudev after the feature branch is deployed.

1. Deploy `feature/hyku-v7.2.1-upgrade` to hykudev (via existing deployment process)
2. Navigate to catalog search results page
3. Verify each facet shows exactly 5 values (not 10, not fewer)
4. Verify "More" links appear correctly when a facet has >5 values
5. If facets still show 10: report findings — issue is elsewhere, not a missing workaround

**Planning Agent:** Close out this task in agent-tasks (move to completed/ if AC #5 confirmed by human).

## 6. Blockers

None — waiting on human visual validation for deployment verification.

## 7. Files Left Uncommitted

The following files are staged/uncommitted in the **agent-tasks** repo and require human authorization to commit:

| File | Action |
|------|--------|
| `projects/wvulibraries_knapsack/tasks/active/2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md` → `tasks/completed/` (git mv) | Proposed for commit |
| `projects/wvulibraries_knapsack/status.md` (session log entry appended) | Proposed for commit |
| This handoff file (`handoffs/copilot/2026-10-28-FACET-WORKAROUND-REMOVAL-CLOSEOUT.md`) | Proposed for commit |

**Proposed commit message:**
```
closeout: complete facet-limiting workaround removal task

Code landed 884c788; AC5 hykudev visual pending human confirmation.
Task moved active→completed; status.md updated with session log.
```

## Provenance

- Upstream fix: Hyku PR #3369 (commit `4148133d`, "Show five values in every catalog facet")
- Companion: Hyrax PR #7678 ("Adding limit: true causes it to honor your default limit for facets from M3 profile")
- Workaround added: September 2026 (superseded by upstream)
- Workaround removed: 2026-10-28 (commit `884c788`)
