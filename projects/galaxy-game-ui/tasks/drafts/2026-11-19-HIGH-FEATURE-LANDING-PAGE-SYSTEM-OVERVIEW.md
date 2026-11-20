---
status: draft
resume: false
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-19
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project_name]`, `[project]`, `[SUBFOLDER]` and `[FILENAME]` in the file when the task is drafted, so the block carries no other placeholders at dispatch; the only substitution made at dispatch is `<agent-tasks>`. If the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: GalaxyGame UI prototype
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxy-game-ui/tasks/backlog/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md
       git ls-files --error-unmatch projects/galaxy-game-ui/tasks/backlog/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md
     If the source is not tracked, stage the source first:
       git add projects/galaxy-game-ui/tasks/backlog/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxy-game-ui/tasks/backlog/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md \
              projects/galaxy-game-ui/tasks/active/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxy-game-ui/tasks/active/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md
       test ! -e projects/galaxy-game-ui/tasks/backlog/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md && test -f projects/galaxy-game-ui/tasks/active/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxy-game-ui/README.md
  3. <agent-tasks>/projects/galaxy-game-ui/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog or implementation.

**Current State**: In `projects/galaxy-game-ui/tasks/drafts/`, committed to the agent-tasks repository.
**Next Step**: Independent review assessment → Tracy's approval → human moves the task according to the agreed project lifecycle.
**Do NOT move to active or dispatch to an implementation agent** until Tracy explicitly approves.

---

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/)

**APPLIES ONLY IF status: draft above. Use this checklist after the review agent returns its assessment.**

- [ ] Acceptance criteria are clear, measurable, and supported by the supplied project context.
- [ ] Task scope is bounded and does not duplicate another task.
- [ ] Dependencies and sequencing are consistent with the current dispatch plan.
- [ ] No contradictions remain between task instructions, project guidance, and the shared task workflow.
- [ ] Technical review disposition is GO.
- [ ] Tracy has explicitly approved the task's next lifecycle step.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before implementation dispatch)

**This task is still a draft. These checks are not authorization to dispatch. Complete them only after review and approval.**

- [ ] Agent Dispatch Interface is complete and accurate for the approved project lifecycle.
- [ ] Step 0 and startup instructions are clear and actionable.
- [ ] Synthesis report template is complete.
- [ ] No unresolved placeholders remain in implementation instructions.
- [ ] All referenced paths and source locations have been verified by the implementation agent during investigation.
- [ ] Architecture boundaries and data-preservation rules are explicit.
- [ ] Acceptance criteria are measurable.
- [ ] Dependencies and blocked/blocks relationships are clear.
- [ ] Reviewer GO and separate human commit/push authorization gates are understood.

---

# TASK: Implement the landing-page system overview using copied JSON

**Status**: DRAFT
**Priority**: HIGH
**Type**: feature
**Created**: 2026-11-19
**Last Updated**: 2026-11-19

## Context and objective

The prototype's existing public landing page should become the GalaxyGame entry page, using the existing page structure and visual direction rather than a redesign. Its five system entries need source-backed identity and supported celestial-object information. A small Rails-side JSON reader is part of this same task because it supplies the landing page; it is not a separate data-platform project.

The main application is Rails 7.0.8.7. The separate prototype is Rails 8.1 with Propshaft, importmap, Turbo, Stimulus, Tailwind, and PostgreSQL. Keep their code, runtime assumptions, and asset conventions distinct.

## Project-specific source context

The following context is supplied to make this task reviewable without repository access. The implementation agent must still verify current files and source records during investigation; where code differs, report the discrepancy rather than silently assuming this description is current.

### Existing landing-page implementation

- Main application view: `app/views/landing/index.html.erb`.
- Main application stylesheet: `app/assets/stylesheets/pages/_landing.scss`.
- Main application approved hero image: `app/assets/images/hero-banner.png`.
- Prototype view: `app/views/landing/index.html.erb`.
- Prototype controller: `app/controllers/landing_controller.rb`.
- Prototype stylesheet: `app/assets/stylesheets/pages/landing.css`.

The prototype currently has hard-coded system-card data in its landing controller. The main application landing page is the source reference for the earlier hero treatment. Its stylesheet uses the hero image as a centered CSS background with `cover`; the prototype has a corresponding hero background reference but needs the image restored and the asset reference checked for Propshaft compatibility.

**The missing image is `hero-banner.png`, not the GalaxyGame logo. The logo has already been fixed. Do not redo or replace the logo.**

### JSON data

The prototype has a repository-level `json-data/` directory containing:

- `json-data/star_systems/`
- `json-data/generated_star_systems/`

These mirror the main application's current star-system data organization. The JSON describes star systems and celestial objects; it does not contain terrain.

The landing page is intended to show these five entries:

1. Sol
2. Alpha Centauri
3. FR-488530
4. DJEW-716790
5. Eden — system identifier `AOL-732356`

Do not assume a display label is the filename or unique lookup key. Verify the exact source path, system identifier/key, and record shape for every entry during investigation.

Sol has two intentionally separate fixtures:

- `sol.json`: a smaller, valid celestial-object sample, including Earth, Mars, Venus, Titan, and other objects. It is useful as a smaller UI test fixture.
- `sol-complete.json`: the fuller Sol celestial-object collection. Use this for the normal, fuller Sol landing-page representation.

Do not merge, overwrite, regenerate, or treat `sol.json` as broken/incomplete. Do not silently fall back from `sol-complete.json` to `sol.json` for the normal landing page if the complete file is unavailable; report the issue and render the affected entry as unavailable.

### Visual and data presentation intent

The prototype wiki is authoritative for the landing-page presentation and copied JSON semantics:

- `docs/wiki/interface-and-workflows.md` — public landing page and system overview.
- `docs/wiki/data-integration-and-migration.md` — JSON data organization, Sol fixture roles, and integration boundary.

The landing page uses restrained dark styling, cyan accents, compact readable typography, a prominent space-background hero, and five compact system entries. It should not become an EVE Online clone.

System identity, counts, and any other system-specific factual fields must be traceable to the selected JSON source. Do not invent population, economy, discovery state, operational status, resources, distances, classifications, or wormhole connections. If a property is not supported by the selected source, omit it or show an explicit unavailable/unknown state.

Any dashboard metrics or recent-activity content that remains illustrative must be visibly labeled as mock/demo and not live. Keep that content clearly separate from JSON-backed system facts.

The complete wormhole/network topology is not confirmed. Do not draw speculative connections as established fact. It is acceptable to omit a network diagram or show only relationships whose endpoints and connection are explicitly supported by confirmed data.

## Read first

After the dispatch interface startup sequence, read:

1. `projects/galaxy-game-ui/README.md` and `SESSION_GUIDANCE.md`.
2. The two prototype wiki pages listed above.
3. The current prototype landing view, controller, stylesheet, routes, asset setup, JSON directories, and relevant existing data-loading code.
4. The corresponding main-app landing view and stylesheet, and the source `hero-banner.png` asset. Treat the main repository as read-only except for reading/copying this approved image.
5. Relevant current main-app JSON records and their schema/shape. Do not run StarSim or modify source data.

If current code, wiki, and this task disagree, record the exact conflict in the synthesis report. Do not silently resolve a game-design conflict. For an unresolved UI choice, follow the prototype's `NEEDS_REVIEW.md` process.

## Scope

- Complete the existing public landing page, reusing its current structure and prototype styling wherever practical.
- Replace the hard-coded system-card records with a small Rails-side reader/controller integration that loads the copied JSON and supplies view-ready data.
- Keep file access, JSON parsing, source-record resolution, and error handling on the Rails side. Do not make browser JavaScript fetch or parse source JSON files.
- During investigation, establish and record a five-row source-resolution table before implementation:

  | Display entry | Exact JSON path | Source identifier / lookup key | Supported identity fields | Supported object collection and count rule |
  |---|---|---|---|---|
  | Sol | Verify | Verify | Verify | Verify |
  | Alpha Centauri | Verify | Verify | Verify |
  | FR-488530 | Verify | Verify | Verify |
  | DJEW-716790 | Verify | Verify | Verify |
  | Eden | Verify | AOL-732356 (confirm in source) | Verify | Verify |

- Use the smallest existing Rails pattern that fits the prototype. Reuse an existing reader/loader if appropriate; otherwise add only the minimal focused reader needed for this page. Do not introduce a generic provider/lookup framework.
- Define any displayed celestial-object count from the actual source collection and document exactly which categories are included. If a consistent, meaningful count cannot be established from the source, omit the count rather than guessing.
- Restore the existing `hero-banner.png` from the main application's `app/assets/images/hero-banner.png` into the prototype's Rails asset tree. Do not generate, substitute, or redesign the image.
- Inspect the prototype's actual Propshaft asset conventions and reference the image using a compatible asset path. Do not assume that a hard-coded `/assets/hero-banner.png` URL is correct.
- Display the hero image as a CSS background using `background-size: cover`, `background-repeat: no-repeat`, and an appropriate background position. Preserve the image's aspect ratio; responsive cropping is acceptable, distortion/stretching is not. Adjust background position only as needed to keep important image content visible.
- Keep the existing GalaxyGame logo unchanged.
- Keep any illustrative metrics/activity visibly identified as mock/demo content and separate from source-backed system facts.

## Exclusions and preservation rules

Do not add:

- A production API or endpoint layer.
- A database model, migration, persistence mechanism, or import pipeline.
- A generic data-provider/lookup framework or broad main-app lookup-service port.
- StarSim execution, system generation, survey calibration, scientific refinement, terrain/map generation, or simulation behavior.
- A speculative network/topology model.
- Unrelated dashboard redesign or changes to other pages.

Do not modify the main GalaxyGame repository's code or JSON files. The only main-repository asset interaction is reading and copying the existing approved `hero-banner.png` into the prototype. Do not modify either Sol JSON fixture or any other copied source JSON.

## Investigation and implementation sequence

### Step 1 — Verify the current implementation and sources

- Confirm the actual Rails app root, current branch, working-tree state, landing route/controller/view/style paths, asset conventions, and test framework.
- Inspect the five candidate system records and complete the source-resolution table.
- Confirm the actual schema path used to count celestial objects. Record unsupported or ambiguous fields.
- Verify the main-app hero asset exists at the stated path and inspect how the prototype currently references assets.
- Identify the smallest Rails-side loading pattern that matches the prototype.

If two competing source records could represent the same requested entry and no established selection rule resolves them, stop and ask Tracy. Do not choose arbitrarily. If a source record or optional property is absent, use the permitted unavailable/omitted state and record the gap; do not block the whole task unless the five-entry identity itself cannot be safely represented.

### Step 2 — Update the synthesis report and obtain approval

Before editing application files, update the synthesis report with:

- Verified file paths and source mappings.
- The completed five-row source-resolution table.
- Count semantics and fields that will be omitted/unavailable.
- The proposed Rails reader/controller/view data flow.
- The Propshaft asset-reference approach.
- Exact planned edit paths.
- The focused test and screenshot plan.
- Any unresolved issue requiring Tracy's decision.

Stop and wait for Tracy's explicit approval of the proposed approach before implementation.

### Step 3 — Implement the bounded landing-page slice

- Implement the minimal Rails-side reader/controller integration.
- Replace hard-coded system-card data with the five source-resolved entries.
- Render only supported source-backed facts and clearly labeled unavailable states.
- Preserve the distinction between the two Sol fixtures.
- Restore and reference the existing hero background using the prototype's verified Propshaft-compatible convention and responsive `cover` styling.
- Preserve the logo and unrelated page structure.

### Step 4 — Verify and capture evidence

Use the prototype's actual test runner and commands, verified during Step 1. Do not assume the main application's Docker container or test commands apply. Run focused tests only; do not run a full suite unless Tracy explicitly requests it.

Capture:

- The five-row source-resolution table.
- Exact focused test commands and unabridged relevant results.
- Successful public landing-route rendering.
- Missing-file and malformed-JSON behavior.
- Desktop and narrow-mobile screenshots, with viewport dimensions recorded.
- Confirmation that the hero asset loads, covers the banner without tiling or distortion, and keeps foreground text readable.
- A final diff check showing that source JSON and the main repository were not modified.

## Failure behavior

- A missing or malformed source for one system must not crash the public landing route or remove unaffected system entries.
- The affected entry must show an explicit unavailable state; do not silently substitute another system, show a fabricated identity, or report unavailable data as a zero count.
- Log the underlying source error server-side using the prototype's existing Rails logging convention. Do not expose filesystem paths or exception details in public page content.
- Unexpected JSON shapes must be handled explicitly and covered by a focused test.
- If all sources fail, the public route must still render its surrounding page and show unavailable states for the affected entries.

## Acceptance criteria

- [ ] The public landing route renders the five intended entries: Sol, Alpha Centauri, FR-488530, DJEW-716790, and Eden.
- [ ] The implementation report records the exact source path and identifier/key for each entry; unresolved identities are not fabricated.
- [ ] Normal Sol landing-page data comes from `sol-complete.json`; `sol.json` remains a separate smaller valid fixture and is not merged, rewritten, or silently used as a fallback.
- [ ] Every displayed system-specific factual field is traceable to its selected JSON record; unsupported properties are omitted or explicitly marked unavailable.
- [ ] Any displayed object count has a documented source collection and counting rule; unavailable counts are not silently shown as zero.
- [ ] Rails loads and resolves the JSON. Browser code does not fetch or parse the source JSON files, and no raw source directory is exposed as a public endpoint.
- [ ] Missing, malformed, and unexpected-shape data for one entry do not break the public route or hide unaffected entries; the affected entry shows an unavailable state and the error is logged server-side.
- [ ] Existing illustrative metrics/activity are visibly labeled mock/demo and are not presented as live system facts.
- [ ] No speculative wormhole/network topology is presented as established fact.
- [ ] The existing `hero-banner.png` is copied unchanged from the main app into the prototype and loads through the verified Propshaft-compatible asset reference.
- [ ] The hero background uses `cover`, does not tile or distort, and is visually checked at desktop and narrow-mobile widths; cropping preserves important content and foreground text remains readable.
- [ ] The existing GalaxyGame logo is unchanged.
- [ ] Focused tests pass, the public route is verified, and desktop/mobile screenshots plus source-mapping and diff evidence are recorded.
- [ ] No source JSON, main-app code, unrelated page, database schema, or simulation behavior is changed.

## Files involved

### Prototype files to inspect; edit only where required

| File / path | Purpose |
|---|---|
| `app/controllers/landing_controller.rb` | Existing landing-page data currently supplied to the view |
| `app/views/landing/index.html.erb` | Existing public landing page |
| `app/assets/stylesheets/pages/landing.css` | Prototype hero and landing-page styling |
| `app/assets/images/hero-banner.png` | Destination for the approved hero asset |
| `json-data/star_systems/` | Copied system JSON |
| `json-data/generated_star_systems/` | Copied generated-system JSON |
| Existing test files | Use the current test structure discovered during investigation |

### Read-only reference files

| File / path | Purpose |
|---|---|
| Main app `app/views/landing/index.html.erb` | Existing main-app landing structure |
| Main app `app/assets/stylesheets/pages/_landing.scss` | Existing hero background treatment |
| Main app `app/assets/images/hero-banner.png` | Approved source image; copy unchanged |
| Main app system JSON | Source data and schema reference; do not edit |
| Prototype `docs/wiki/interface-and-workflows.md` | Landing-page design intent |
| Prototype `docs/wiki/data-integration-and-migration.md` | JSON organization and Sol fixture semantics |

**Migration**: None.

## Dependencies and sequencing

- **Prerequisites**: The copied JSON data and approved main-app hero image are expected to be available. Verify them during investigation.
- **Blocked by**: None known.
- **Blocks / sequencing**: The Player Planetary data-integration task is reviewed after this landing-page slice is implemented and reviewed. Do not assume this landing-page reader's data payload is automatically the correct Planetary data contract.
- Dispatch remains one task at a time and requires Tracy's explicit approval.

## Stop conditions and escalation

Stop before implementation and ask Tracy if:

- Two plausible source records conflict and no established identity rule selects one.
- The approved hero image cannot be found at the stated main-app location or cannot be copied without changing the source.
- The required five system identities cannot be safely represented without inventing facts.
- The implementation would require a database migration, production API, generic framework, simulation work, or another material scope expansion.
- Current code, authoritative wiki intent, and this task contain a consequential conflict that cannot be resolved by using an explicit unavailable state.

Do not stop merely because optional properties or future simulation refinements are absent; omit or mark them unavailable and record the gap.

## Required synthesis report

Save the report under `projects/galaxy-game-ui/summaries/` using the filename pattern required by the Agent Dispatch Interface. Update the same report at the required stages.

```markdown
# STATUS SYNTHESIS REPORT

**Task**: 2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW
**Status**: draft → backlog → active → completed
**Date**: YYYY-MM-DD

## Stage 1 — Initial understanding and Step 0
Record the exact task lifecycle commands/results, source/destination verification, YAML status, task objective, boundaries, unknowns, and planned evidence.

## Stage 2 — Investigation findings (complete before implementation)
Record verified source paths, the five-row source-resolution table, count semantics, asset/Propshaft findings, exact proposed edit paths, focused test plan, and any unresolved decisions. Wait for Tracy's explicit approval before implementation.

## Stage 3 — Implementation and verification
Record actual changes, exact test commands/results, runtime route verification, desktop/mobile screenshots and viewport dimensions, asset-load verification, final diff/preservation checks, documentation updates, and remaining issues.

## Final disposition
State acceptance-criteria results, outstanding gaps, and whether reviewer assessment is requested.
```

## Review gate and authorization

- Complete implementation and verification, then stop and request reviewer assessment with the synthesis/evidence report path.
- A reviewer GO does not authorize a commit, push, or task completion.
- Do not commit, push, or mark the task complete without Tracy's separate explicit authorization for each action. Commit authorization does not imply push authorization.
- If the reviewer returns REVISE or STOP, follow that disposition and wait for human guidance where required.

## Documentation and closeout

- Do not duplicate the detailed design or JSON schema in new task/wiki pages.
- If implementation exposes a genuine game-design or source-data conflict, record it for Tracy in the prototype's `NEEDS_REVIEW.md`; do not silently change main-game wiki intent.
- Update the relevant prototype wiki only if implementation reveals a concrete UI/integration detail that is not already documented and Tracy's established workflow permits that update.
- Follow `SESSION_CLOSEOUT.md` for task lifecycle, status, evidence, and handoff closeout. In chat, provide the synthesis report path and a concise status; do not paste raw command output.

## Draft disposition

This is a review draft only. It is not approved for backlog promotion, implementation, or dispatch.
