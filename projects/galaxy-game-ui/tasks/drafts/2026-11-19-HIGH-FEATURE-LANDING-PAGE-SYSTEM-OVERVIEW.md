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

## 🟡 DRAFT STATUS NOTE (awaiting independent review)

**This task is in draft status. Its earlier placement in backlog was not supported by Tracy's approval and has been reversed. It is not approved for backlog, implementation, or dispatch.**

**Current State**: In `projects/galaxy-game-ui/tasks/drafts/`, committed to the agent-tasks repository.  
**Prior supporting reviews (reported)**: Perplexity and Gemini returned GO on a supplied three-document package. These are document-only reviews and do not replace the required independent repository-aware review.  
**Independent final review required**: An agent instance other than the task creator must inspect both actual codebases and relevant authoritative wikis, compare them with this task, and issue GO before Tracy considers backlog promotion. Select from currently available agents based on actual access; do not hardcode model names. Premium GitHub Copilot model availability is controlled by Tracy.  
**Next Step**: Independent repository-aware review → Tracy's explicit approval for promotion to backlog. Promotion does not authorize dispatch; dispatch requires separate explicit approval.

---

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/)

- [ ] Acceptance criteria are clear, measurable, and supported by the supplied project context.
- [ ] Scope is bounded and does not duplicate another task.
- [ ] Dependencies and sequencing agree with the current dispatch plan.
- [ ] No contradictions remain between the task, project guidance, and shared workflow.
- [x] Prior document-only technical reviews by Perplexity and Gemini were reported as GO (supporting context only).
- [ ] An independent reviewer instance other than the task creator inspected the actual main app, prototype, and relevant wikis; final disposition is GO.
- [ ] Tracy explicitly approves promotion from drafts/ to backlog.
- [ ] Dispatch approval remains a separate later decision.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before implementation dispatch)

**This task is a draft. These checks are not authorization to promote or dispatch. Complete them only after independent review and Tracy's approval.**

- [ ] Agent Dispatch Interface is complete and accurate for the approved project lifecycle.
- [ ] Step 0 and startup instructions are actionable.
- [ ] Synthesis report template is complete.
- [ ] No unresolved placeholders remain in implementation instructions.
- [ ] Referenced paths and source locations have been verified during investigation.
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

## Objective and context

Complete the existing public landing page and its small Rails-side JSON reader as one coordinated slice. The reader exists only to supply this page; do not split it into a separate data-platform task.

The main GalaxyGame app is Rails 7.0.8.7. The separate prototype is Rails 8.1 with Propshaft, importmap, Turbo, Stimulus, Tailwind, and PostgreSQL. Keep their code and runtime assumptions separate. Adapt the existing views and styling rather than redesigning the application.

## Established project facts

- Prototype landing files: `app/controllers/landing_controller.rb`, `app/views/landing/index.html.erb`, and `app/assets/stylesheets/pages/landing.css`.
- The controller currently supplies hard-coded system-card data.
- The prototype's system JSON is local-only, gitignored, and excluded from a fresh clone. Resolve all paths relative to the prototype Rails root, galaxy-game-ui/ (not the repository root):
  - data/json-data/star_systems/sol.json
  - data/json-data/star_systems/sol-complete.json
  - data/json-data/generated_star_systems/aol-732356.json (Eden)
  - data/json-data/generated_star_systems/fr-488530.json
  - data/json-data/generated_star_systems/djew-716790.json
  - data/json-data/generated_star_systems/hybrid_AC-01_20260116_023525.json (Alpha Centauri)
- Because this data is gitignored, it will not exist on a fresh clone. The Docker volume mapping may need adjustment so the container can see data/json-data under the Rails root; Tracy expects this adjustment to be considered during investigation.
- The five intended entries are Sol, Alpha Centauri, FR-488530, DJEW-716790, and Eden. Source identifiers include SOL-01, AC-01, and AOL-732356; do not use the mock controller's SOL or ALPHA-C values as source identity. The galaxy identifier (MILKY-WAY versus MILKYWAY-01) is ignored.
- sol-complete.json is the fuller Sol collection; sol.json is a separate smaller fixture. Keep both files separate and unchanged.
- These files describe systems and celestial objects, not terrain.
- The hero image already exists in both apps. The prototype copy is app/assets/images/hero-banner.png; the main-app copy is galaxy_game/app/assets/images/hero-banner.png. Both are PNG blob 9e89572f, 2,536,258 bytes, 1536×1024, SHA-256 30fff39437a409a0e0ce9b9bc2cdbc0c5e71b044b30a81c19793926c22f46d49. Verify the prototype reference renders; do not copy or replace the image.
- Prototype wiki references:
  - `docs/wiki/interface-and-workflows.md` — public landing page and system overview.
  - `docs/wiki/data-integration-and-migration.md` — JSON folders, Sol fixture roles, and integration boundary.

The prototype wiki is the authority for UI intent. Read it before making presentation choices. If code, wiki, and task disagree, record the conflict rather than silently choosing a new design decision.

## Current state to change

- The prototype controller hard-codes four systems: SOL, ALPHA-C, FR-488530, and DJEW-716790. It supplies settlements_count, celestial_bodies_count, and has_wormhole for each, plus @systems_count = 4 and @wormholes_count = 5.
- Eden is absent from the controller's hard-coded set.
- The view renders a Wormhole Hub badge, Settlements and Bodies figures, and a Galaxy Status section using the mock totals.
- The landing page view itself has no logo. The shared branding partial app/views/shared/_branding.html.erb references logos/GalaxyGame.png.
- The unsupported settlements count, Wormhole Hub badge, Systems Discovered, and Wormhole Connections require a Tracy decision: omit or label mock/demo. DECISION PENDING (Tracy).

## Source-shape and count facts

Record these source baselines without deciding the display-count rule:

- sol.json contains 16 bodies grouped in a dictionary by category: 4 terrestrial, 4 major moons, 4 gas giants, and 4 dwarf planets.
- sol-complete.json contains 49 bodies in a flat list with parent_body: 31 moons, 9 dwarf planets, 4 terrestrial, 2 gas giants, 2 ice giants, and 1 protoplanet. It has no solar_system key.
- Eden (aol-732356.json) contains 26 top-level bodies plus 10 nested moons.
- Alpha Centauri has four entries; two share identifier AC-01-b.
- Source identity fields include SOL-01, AC-01, and AOL-732356. The controller's SOL and ALPHA-C values are mock identifiers and are not source identity. Ignore the galaxy identifier (MILKY-WAY versus MILKYWAY-01).
- The two Sol fixtures have different data shapes and must be handled as such. The correct rule for whether moons or nested moons contribute to the displayed object count is DECISION PENDING (Tracy).

## Known source defects — do not edit source files

- fr-488530.json does not parse: a trailing comma follows the system identifier, and the file is truncated mid-object (33 opening braces versus 29 closing braces).
- djew-716790.json does not parse: malformed hydrosphere blocks occur around line 74.
- hybrid_AC-01_20260116_023525.json parses but contains duplicate AC-01-b identifiers; Proxima b and Proxima c have no identifier.
- Do not repair, regenerate, or otherwise edit any of these JSON files. Failure behavior must isolate an unavailable or ambiguous system. Handling the FR-488530 and DJEW-716790 cards, the duplicate Alpha Centauri identifier, and which Alpha Centauri file is canonical are DECISION PENDING (Tracy).

## Scope and implementation requirements

1. Inspect the existing landing route, controller, view, stylesheet, asset conventions, test directories/runners, Docker volume mapping, and local-only JSON before editing.
2. Resolve the five intended entries from the exact files listed above. Record a compact source table in the synthesis report with display name, exact path relative to the prototype Rails root, source identifier/key, identity field, object-count basis, parse status, and ambiguity. Do not infer identity from display names.
3. Add the smallest Rails-side reader/controller integration that supplies view-ready data. Keep filesystem access and JSON parsing in Rails. Browser JavaScript must not fetch or parse source JSON, and no public raw-JSON endpoint is needed.
4. Use sol-complete.json for normal Sol representation. Do not silently fall back to sol.json if the complete fixture is missing. Do not merge, rewrite, regenerate, or delete either Sol fixture or any other source JSON.
5. Derive every displayed system-specific fact from the selected source. The Sol fixtures have different shapes: sol.json is a category dictionary; sol-complete.json is a flat list with parent_body. Handle both shapes where required. Do not assume the same object-count rule across systems. Record the count rule only after Tracy decides whether moons and nested moons count.
6. Verify the existing hero image renders and correct its asset reference if needed. The prototype image is already at app/assets/images/hero-banner.png and is byte-identical to the main-app source. Do not copy, replace, or modify it. The prototype landing.css currently hard-codes url('/assets/hero-banner.png'); inspect Propshaft conventions and use a compatible reference.
7. The existing hero CSS already specifies center center / cover no-repeat. Verify responsive rendering at desktop and narrow-mobile sizes; preserve proportions and avoid obscuring important content. Do not treat this as new CSS implementation if already satisfied.
8. Leave the existing logo unchanged. The landing page view itself has no logo; the logo is rendered through app/views/shared/_branding.html.erb at logos/GalaxyGame.png. Verify no diff to that partial or image. Unsupported illustrative metrics/counts must be omitted or labeled mock/demo, pending Tracy's decision.
9. Do not present speculative wormhole relationships as established facts. The wiki permits either omitting the network graphic or using a clearly provisional explanatory treatment; see [interface and workflows, network topology guidance](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/interface-and-workflows.md#L135) and [the related topology discussion](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/interface-and-workflows.md#L150). DECISION PENDING (Tracy).

## Exclusions and preservation

No production API, database/model/migration work, generic provider/lookup framework, broad lookup-service port, StarSim execution, system generation, survey/calibration, scientific refinement, terrain/map generation, simulation behavior, or unrelated page redesign.

Do not modify main-app code or source JSON. The only main-repository asset interaction is reading and copying the approved hero image. Do not change any copied source JSON.

## Investigation, approval, and verification

- Complete the source-resolution table. Record source defects: fr-488530.json has a trailing comma after the system identifier and is truncated mid-object (33 opening braces versus 29 closing braces); djew-716790.json has malformed hydrosphere blocks around line 74; hybrid_AC-01_20260116_023525.json parses but has duplicate AC-01-b and Proxima b/c lack identifiers. Do not edit these files.
- Verify the existing prototype hero image and Propshaft reference. Record both asset paths, matching blob 9e89572f, 2,536,258 bytes, 1536×1024, and SHA-256 30fff39437a409a0e0ce9b9bc2cdbc0c5e71b044b30a81c19793926c22f46d49. No copy operation is required.
- Confirm the Docker volume mapping exposes the gitignored data/json-data directory to the container; it may need adjustment.
- The prototype has both minitest (test/, including the empty landing_controller_test.rb stub) and RSpec (spec/). Confirm the actual runner during Stage 2 before choosing focused commands. If the authoritative wiki does not prescribe one, leave the runner choice as DECISION PENDING (Tracy).
- Update the synthesis report with verified paths, source mapping, proposed data flow, asset approach, exact planned edit paths, and test/visual plan. **Wait for Tracy's explicit approval before editing application files.**
- After implementation, run focused tests only. Verify the public route, source selection, missing/malformed/unexpected JSON behavior, and unchanged source files.
- Capture desktop and narrow-mobile screenshots and record viewport dimensions. Confirm the hero loads, covers without tiling or distortion, and leaves foreground text readable.
- Record exact test commands/results and a final diff/preservation check in the synthesis report.

### Failure behavior

A missing, malformed, or unexpected-shape source for one system must not crash the public route or hide unaffected entries. Show an explicit unavailable state for the affected entry, do not fabricate identity or show zero for an unavailable count, and log the underlying error server-side using the prototype's existing convention. Do not expose filesystem paths or exception details publicly. If all sources fail, the surrounding landing page must still render with unavailable states.

## Decisions for Tracy — do not resolve in this task

1. Fix or regenerate fr-488530.json and djew-716790.json before dispatch, or accept two unavailable cards in the first slice.
2. Decide whether duplicate AC-01-b is an upstream data defect to fix or something the page should tolerate, and identify the canonical Alpha Centauri file.
3. Decide the object-count rule: whether moons count and whether Eden's nested moons count.
4. Decide whether the network graphic is omitted or receives a clearly provisional explanatory treatment. The wiki permits either; see [interface and workflows, lines 135 and 150](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/interface-and-workflows.md#L135). Do not choose here.
5. Decide whether unsupported mock items (settlements count, Wormhole Hub badge, Systems Discovered, Wormhole Connections) are omitted or explicitly labeled mock/demo.
6. Decide the test runner if the authoritative wiki does not settle it.

Each unresolved item remains DECISION PENDING (Tracy) until Tracy decides.

## Wiki conflict to report — do not edit wiki

The Sol fixtures section of docs/wiki/data-integration-and-migration.md describes JSON under a top-level json-data/. The verified local layout is data/json-data/ relative to the prototype Rails root. Record this as a documentation conflict for Tracy; do not edit the wiki or NEEDS_REVIEW.md in this task.

## Acceptance criteria

- [ ] The public landing route renders the five intended entries from the exact local-only source paths, or shows an explicit unavailable state where a source cannot safely be resolved. FR-488530 and DJEW-716790 are known malformed inputs; the route must isolate their failures, and none of the source JSON files may be edited.
- [ ] The synthesis report records each entry's exact path relative to the prototype Rails root, source identifier/key, identity field, count basis, and parse status.
- [ ] Normal Sol data uses sol-complete.json; sol.json remains a separate smaller fixture. Neither Sol fixture nor any other source JSON is modified or used as a silent fallback.
- [ ] The reader handles the distinct Sol fixture shapes (category dictionary and flat parent_body list).
- [ ] All displayed system facts are source-backed; any displayed count has a documented source collection and a rule approved by Tracy.
- [ ] Rails loads and resolves the JSON; browser code does not fetch or parse source files. The Docker volume exposes the gitignored data directory.
- [ ] Missing, malformed, and unexpected-shape data is isolated to the affected entry; unaffected entries and the public route remain available. Tests use temporary fixtures rather than gitignored real data.
- [ ] Unsupported mock metrics/counts, the Wormhole Hub badge, Systems Discovered, and Wormhole Connections are omitted or explicitly labeled mock/demo according to Tracy's pending decision; speculative topology is not presented as fact.
- [ ] The existing prototype hero image renders using a verified Propshaft-compatible reference; no image copy or replacement occurs. Its hash and dimensions are recorded.
- [ ] Existing hero cover/positioning behavior is verified at desktop and narrow-mobile sizes.
- [ ] No diff exists in app/views/shared/_branding.html.erb or app/assets/images/logos/GalaxyGame.png.
- [ ] Focused tests, route verification, screenshots, source mapping, and final preservation checks are recorded in the synthesis report.
- [ ] No main-app code, source JSON, database schema, simulation behavior, or unrelated page is changed.

## Files and references

**Prototype files to inspect/edit only as needed:** landing controller, landing view, landing stylesheet, Rails asset configuration, Docker volume configuration, and focused tests. JSON paths below are relative to the prototype Rails root (galaxy-game-ui/) and live under data/json-data/. This directory is local-only and gitignored.

**Exact local JSON sources:**
- data/json-data/star_systems/sol.json
- data/json-data/star_systems/sol-complete.json
- data/json-data/generated_star_systems/aol-732356.json
- data/json-data/generated_star_systems/fr-488530.json
- data/json-data/generated_star_systems/djew-716790.json
- data/json-data/generated_star_systems/hybrid_AC-01_20260116_023525.json

**Read-only references:** main-app landing view and stylesheet; both existing hero-banner.png copies; main-app system JSON; the two prototype wiki pages listed above.

**Migration**: None.

## Dependencies and sequencing

- **Prerequisites**: The local-only, gitignored data/json-data files must be present in the developer environment and mounted into Docker if Docker is used. A fresh clone will not contain them. Verify/adjust the Docker volume mapping during investigation. The prototype hero image already exists; verify rendering rather than copying it.
- **Blocked by**: None known.
- **Blocks / sequencing**: Review the Player Planetary data-integration task after this landing-page slice is implemented and reviewed. Do not assume this landing-page reader is automatically the correct Planetary data contract.
- Dispatch one task at a time, only with Tracy's explicit approval.

## Stop conditions

Stop and ask Tracy before implementation if competing source records require an authoritative choice, local-only data is unavailable and cannot be mounted, the five identities cannot be represented without fabrication, or implementation would require a material scope expansion (API, migration, generic framework, or simulation work). Known malformed FR-488530 and DJEW-716790 files, duplicate Alpha Centauri identifier, missing Proxima identifiers, object-count semantics, network graphic treatment, and mock/demo presentation are DECISION PENDING (Tracy); do not resolve them independently.

Do not stop merely because optional properties or future simulation refinements are absent; omit/mark them unavailable and record the gap.

## Required synthesis report

Save under `projects/galaxy-game-ui/summaries/` using the filename pattern in the dispatch interface. Update it after Step 0, after investigation and before implementation, and after implementation/verification.

```markdown
# STATUS SYNTHESIS REPORT

**Task**: 2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW
**Status**: draft (pending Tracy approval) → backlog → active → completed
**Date**: YYYY-MM-DD

## Stage 1 — Initial understanding and Step 0
Record exact lifecycle commands/results, source/destination verification, YAML status, objective, boundaries, unknowns, and planned evidence.

## Stage 2 — Investigation findings (before implementation)
Record verified paths relative to prototype Rails root, five-system source mapping, source shapes and defects, count semantics without deciding Tracy's open count rule, Docker volume mapping, hero hash/Propshaft findings, logo preservation check, proposed data flow, planned edit paths, verified test runner, temporary-fixture test plan, and unresolved decisions. Wait for Tracy's approval.

## Stage 3 — Implementation and verification
Record changes, exact test commands/results, route verification, screenshots and viewport dimensions, hero asset-load verification, unchanged hero/logo evidence, source JSON preservation checks, temporary-fixture failure tests, final diff/preservation checks, and remaining issues.

## Final disposition
List acceptance-criteria results and whether reviewer assessment is requested.
```

## Review gate, documentation, and closeout

- **Independent pre-promotion final review is mandatory:** before backlog promotion, a reviewer instance other than the task creator must inspect the main GalaxyGame repository, UI prototype repository, and relevant authoritative wikis. Verify source paths, current landing implementation, JSON fixture shapes and defects, existing hero asset and asset-pipeline conventions, logo location, mock metrics, and code/wiki conflicts. The reviewer must return GO before Tracy considers backlog promotion. Promotion and dispatch remain separate human approval gates. Record revisions, files/pages inspected, evidence, findings, and disposition. If repository access is unavailable or review is incomplete, the task remains in drafts.
- Perplexity and Gemini's earlier document-only GO assessments are supporting reviews only, not the final repository-aware GO. Reviewer selection is capability- and availability-based; no fixed model roster applies.
- After implementation and verification, stop and request reviewer assessment. A reviewer GO is not commit/push authorization.
- Do not commit, push, or mark complete without Tracy's separate explicit authorization for each action.
- Record genuine design/data conflicts in the prototype's `NEEDS_REVIEW.md`; do not silently change main-game wiki intent. Do not duplicate existing wiki material.
- Follow `SESSION_CLOSEOUT.md` for status, evidence, task lifecycle, and handoff closeout.

**Draft only.** No backlog promotion, implementation, or dispatch is authorized. The task creator must not review this task; the final review must come from separate agent instances with the required repository access.
