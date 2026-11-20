---
status: backlog
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

## 🟡 BACKLOG STATUS NOTE (human-approved promotion; implementation still gated)

**This task has been promoted to backlog with Tracy's approval. This is not implementation authorization.**

**Current State**: In `projects/galaxy-game-ui/tasks/backlog/`, committed to the agent-tasks repository.  
**Technical review**: Perplexity first pass and Gemini second pass both returned GO on the supplied three-document package.  
**Final review still required**: A repository-aware reviewer (Claude or Grok) must inspect both actual codebases and their relevant wikis, compare them with this task, and issue the final GO before implementation dispatch.  
**Next Step**: Repository-aware final review → Tracy's explicit dispatch approval. Do NOT move to active or dispatch based only on the document-only technical review.

---

## 🟡 BACKLOG REVIEW CHECKLIST (Human — before implementation dispatch)

- [ ] Acceptance criteria are clear, measurable, and supported by the supplied project context.
- [ ] Scope is bounded and does not duplicate another task.
- [ ] Dependencies and sequencing agree with the current dispatch plan.
- [ ] No contradictions remain between the task, project guidance, and shared workflow.
- [x] Document-only technical review: Perplexity and Gemini returned GO.
- [ ] Repository-aware final review by Claude or Grok inspected the actual main app, prototype, and relevant wikis; final disposition is GO.
- [ ] Tracy explicitly approves dispatch after the repository-aware review.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before implementation dispatch)

**This task is in backlog. These checks are not authorization to dispatch. Complete them only after the repository-aware final review and Tracy's approval.**

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

**Status**: BACKLOG  
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
- The prototype has copied JSON under `json-data/star_systems/` and `json-data/generated_star_systems/`.
- The five intended entries are Sol, Alpha Centauri, FR-488530, DJEW-716790, and Eden (identifier `AOL-732356`). Verify exact source paths and keys; do not infer them from display names.
- `sol-complete.json` is the fuller Sol celestial-object collection for normal landing-page use. `sol.json` is a smaller, valid UI fixture containing major worlds including Earth, Mars, Venus, and Titan. Keep both separate and unchanged.
- These JSON files describe systems and celestial objects, not terrain.
- The missing landing-page image is `hero-banner.png`, not the logo. The logo has already been fixed. The approved source image is in the main app's `app/assets/images/hero-banner.png`.
- Prototype wiki references:
  - `docs/wiki/interface-and-workflows.md` — public landing page and system overview.
  - `docs/wiki/data-integration-and-migration.md` — JSON folders, Sol fixture roles, and integration boundary.

The prototype wiki is the authority for UI intent. Read it before making presentation choices. If code, wiki, and task disagree, record the conflict rather than silently choosing a new design decision.

## Scope and implementation requirements

1. Inspect the existing landing route, controller, view, stylesheet, asset conventions, test framework, and copied JSON before editing.
2. Resolve the five intended entries from actual JSON records. Record a compact source table in the synthesis report with display name, exact path, source identifier/key, supported identity fields, and any object-count basis. If a source identity is ambiguous, ask Tracy; do not choose arbitrarily.
3. Add the smallest Rails-side reader/controller integration that supplies view-ready data. Keep filesystem access and JSON parsing in Rails. Browser JavaScript must not fetch or parse source JSON, and no public raw-JSON endpoint is needed.
4. Use `sol-complete.json` for the normal fuller Sol representation. Do not silently fall back to `sol.json` if the complete fixture is missing. Do not merge, rewrite, regenerate, or delete either fixture.
5. Derive every displayed system-specific fact from the selected source. Define the collection and categories behind any displayed object count. Omit unsupported properties and counts rather than inventing them or showing unavailable data as zero.
6. Restore the existing `hero-banner.png` by copying it unchanged from the main app into the prototype's Rails asset tree. Inspect the prototype's Propshaft conventions and use a compatible asset reference; do not assume a hard-coded `/assets/hero-banner.png` URL is correct.
7. Render the hero image as a CSS background with `background-size: cover`, `background-repeat: no-repeat`, and an appropriate position. Preserve its proportions; responsive cropping is acceptable, stretching/distortion is not. Verify desktop and narrow-mobile layouts and adjust positioning only if important content is obscured.
8. Leave the already-fixed GalaxyGame logo unchanged. Keep any remaining illustrative metrics/activity visibly labeled mock/demo and separate from JSON-backed system facts.
9. Do not show speculative wormhole connections or topology. Omit the network graphic if confirmed relationships are unavailable.

## Exclusions and preservation

No production API, database/model/migration work, generic provider/lookup framework, broad lookup-service port, StarSim execution, system generation, survey/calibration, scientific refinement, terrain/map generation, simulation behavior, or unrelated page redesign.

Do not modify main-app code or source JSON. The only main-repository asset interaction is reading and copying the approved hero image. Do not change any copied source JSON.

## Investigation, approval, and verification

- Complete the source-resolution table and identify count semantics before implementation.
- Verify the main-app hero source exists and inspect the prototype's actual Propshaft asset convention.
- Identify the prototype's actual test runner and focused commands; do not assume the main app's Docker container or test commands apply.
- Update the synthesis report with verified paths, source mapping, proposed data flow, asset approach, exact planned edit paths, and test/visual plan. **Wait for Tracy's explicit approval before editing application files.**
- After implementation, run focused tests only. Verify the public route, source selection, missing/malformed/unexpected JSON behavior, and unchanged source files.
- Capture desktop and narrow-mobile screenshots and record viewport dimensions. Confirm the hero loads, covers without tiling or distortion, and leaves foreground text readable.
- Record exact test commands/results and a final diff/preservation check in the synthesis report.

### Failure behavior

A missing, malformed, or unexpected-shape source for one system must not crash the public route or hide unaffected entries. Show an explicit unavailable state for the affected entry, do not fabricate identity or show zero for an unavailable count, and log the underlying error server-side using the prototype's existing convention. Do not expose filesystem paths or exception details publicly. If all sources fail, the surrounding landing page must still render with unavailable states.

## Acceptance criteria

- [ ] The public landing route renders the five intended entries, using verified JSON records or an explicit unavailable state where a source cannot safely be resolved.
- [ ] The synthesis report records each entry's exact source path and identifier/key.
- [ ] Normal Sol data uses `sol-complete.json`; `sol.json` remains a separate smaller fixture. Neither file is modified or used as a silent fallback.
- [ ] All displayed system facts are source-backed; any displayed count has a documented source collection and counting rule.
- [ ] Rails loads and resolves the JSON; browser code does not fetch or parse source files.
- [ ] Missing, malformed, and unexpected-shape data is isolated to the affected entry; unaffected entries and the public route remain available.
- [ ] Illustrative metrics/activity are visibly labeled mock/demo; unsupported system properties and speculative topology are not presented as facts.
- [ ] The existing hero image is copied unchanged and served through the verified Propshaft-compatible asset reference.
- [ ] The hero uses responsive `cover` without tiling or distortion and is checked at desktop and narrow-mobile sizes.
- [ ] The existing logo is unchanged.
- [ ] Focused tests, route verification, screenshots, source mapping, and final preservation checks are recorded in the synthesis report.
- [ ] No main-app code, source JSON, database schema, simulation behavior, or unrelated page is changed.

## Files and references

**Prototype files to inspect/edit only as needed:** landing controller, landing view, landing stylesheet, prototype asset tree, and existing focused tests.

**Read-only references:** main-app landing view and stylesheet; main-app `app/assets/images/hero-banner.png`; main-app system JSON; the two prototype wiki pages listed above.

**Migration**: None.

## Dependencies and sequencing

- **Prerequisites**: Copied JSON and the approved hero image are expected to exist; verify during investigation.
- **Blocked by**: None known.
- **Blocks / sequencing**: Review the Player Planetary data-integration task after this landing-page slice is implemented and reviewed. Do not assume this landing-page reader is automatically the correct Planetary data contract.
- Dispatch one task at a time, only with Tracy's explicit approval.

## Stop conditions

Stop and ask Tracy before implementation if competing source records require an authoritative choice, the approved hero image cannot be found/restored unchanged, the five identities cannot be represented without fabrication, or implementation would require a material scope expansion (API, migration, generic framework, or simulation work).

Do not stop merely because optional properties or future simulation refinements are absent; omit/mark them unavailable and record the gap.

## Required synthesis report

Save under `projects/galaxy-game-ui/summaries/` using the filename pattern in the dispatch interface. Update it after Step 0, after investigation and before implementation, and after implementation/verification.

```markdown
# STATUS SYNTHESIS REPORT

**Task**: 2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW
**Status**: draft → backlog → active → completed
**Date**: YYYY-MM-DD

## Stage 1 — Initial understanding and Step 0
Record exact lifecycle commands/results, source/destination verification, YAML status, objective, boundaries, unknowns, and planned evidence.

## Stage 2 — Investigation findings (before implementation)
Record verified paths, five-system source mapping, count semantics, asset/Propshaft findings, proposed data flow, planned edit paths, test plan, and unresolved decisions. Wait for Tracy's approval.

## Stage 3 — Implementation and verification
Record changes, exact test commands/results, route verification, screenshots and viewport dimensions, asset-load verification, final diff/preservation checks, documentation updates, and remaining issues.

## Final disposition
List acceptance-criteria results and whether reviewer assessment is requested.
```

## Review gate, documentation, and closeout

- **Pre-implementation final review is mandatory:** after backlog promotion, a repository-aware reviewer (Claude or Grok) must inspect the actual main GalaxyGame repository, the UI prototype repository, and the relevant authoritative wikis. The review must verify the task's stated source paths, existing landing-page implementation, JSON fixture mappings/semantics, hero asset existence and asset-pipeline conventions, current UI behavior, and any code/wiki conflicts. The reviewer must return an explicit GO before this task can be dispatched. Record the reviewed branches/commits, files/pages inspected, evidence, findings, and disposition. If repository access is unavailable or the review is incomplete, the task remains in backlog.
- Perplexity and Gemini's document-only technical GO assessments are supporting reviews, not the final repository-aware GO.
- After implementation and verification, stop and request reviewer assessment. A reviewer GO is not commit/push authorization.
- Do not commit, push, or mark complete without Tracy's separate explicit authorization for each action.
- Record genuine design/data conflicts in the prototype's `NEEDS_REVIEW.md`; do not silently change main-game wiki intent. Do not duplicate existing wiki material.
- Follow `SESSION_CLOSEOUT.md` for status, evidence, task lifecycle, and handoff closeout.

**Draft only.** No backlog promotion, implementation, or dispatch is authorized.
