# Task: Remove Knapsack Facet Limiting Workaround (Upstream Fix Active)

---
status: backlog
resume: false
priority: HIGH
type: refactor
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: true
project: wvulibraries_knapsack
created: 2026-10-28
last_updated: 2026-10-28
draft_review_checklist:
  - [x] File paths verified in codebase
  - [x] Upstream PR #3369 confirmed present in hyrax-webapp (commit 4148133d)
  - [x] Diff between workaround and upstream analyzed — no behavioral gap
  - [ ] Template compliance: awaiting review
  - [ ] Review agent disposition: pending GO/REVISE/STOP
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🔨 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Template compliance: all required YAML fields present, DRAFT STATUS NOTE section included, full Agent Dispatch Interface block present per TASK_TEMPLATE.md
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual; if the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/backlog/[SUBFOLDER]/2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/[SUBFOLDER]/2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md \
         projects/wvulibraries_knapsack/tasks/active/2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-REFACTOR-FACET-WORKAROUND-REMOVE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

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
