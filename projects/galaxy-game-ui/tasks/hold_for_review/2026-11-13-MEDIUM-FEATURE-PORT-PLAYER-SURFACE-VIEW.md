---
status: backlog
resume: false
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## REVIEW DISPOSITION

- [x] Independent review: GO (Grok and Gemini).
- [x] Tracy approved moving this task to backlog.
- [x] Scope and acceptance criteria retained; dispatch-time source/path checks remain required.

## Task Readiness Checklist

- [ ] Agent Dispatch Interface is complete.
- [ ] Implementation steps and stop conditions are actionable.
- [ ] Synthesis report requirement is understood.
- [ ] Source paths and access are verified at dispatch.
- [ ] Architecture gotchas and acceptance criteria are specific.
- [ ] Dependencies are clear.
- [ ] Tracy has approved dispatch.

## Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: GalaxyGame UI prototype
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SURFACE-VIEW.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SURFACE-VIEW.md \
         projects/galaxy-game-ui/tasks/active/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SURFACE-VIEW.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SURFACE-VIEW.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxy-game-ui/README.md
  3. <agent-tasks>/projects/galaxy-game-ui/SESSION_GUIDANCE.md
  4. The task file. It holds what is specific to this task; the files above hold the standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

IMPORTANT: Do not modify or abbreviate the text above. The only dispatch-time substitution is <agent-tasks>.

# TASK: Port the Player Surface View
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: feature  
**Created**: 2026-11-13  
**Last Updated**: 2026-11-13

## Agent Assignment

**Assigned To**: TBD by Tracy at dispatch  
**Why This Agent**: Human selection; no agent is assigned by this draft.  
**Local attempts before cloud**: N/A  
**Supervision Level**: watched carefully

## Prerequisites — READ FIRST

1. agent-tasks/rules/GUARDRAILS.md
2. agent-tasks/projects/galaxy-game-ui/README.md
3. agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md
4. The current prototype wiki references below.
5. This task in full.

The prototype is Rails 8.1 and is separate from the Rails 7 main GalaxyGame application. Work only in galaxy-game-ui. Main-game and EVE source repositories are read-only references.

## Context

Port and adapt the existing Surface page and reusable presentation code into the prototype. Use the existing tile-based rendering setup as the initial implementation where it works; this is a provisional UI-prototype foundation, not a final decision about the game's terrain, spatial-data or rendering architecture. Defer new terrain/biome/tileset asset exploration and pipeline repair.

## Relevant Architecture Docs — read before starting

- galaxyGame/docs/wiki/ui/02_player_view_architecture.md
- galaxyGame/docs/wiki/ui/04_current_ui_state_and_known_issues.md
- galaxy-game-ui/docs/wiki/README.md
- galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md
- galaxy-game-ui/docs/wiki/data-integration-and-migration.md

## Critical Information for This Task

### Credentials
Not required.

### Architecture Gotchas

**GOTCHA 1:** Trident is an early experiment, not an approved/default tileset. Do not select it as default or make it an acceptance requirement.

**GOTCHA 2:** Earlier FreeCiv/other free-tileset exploration was abandoned. Do not resume that search or source replacement tilesets.

**GOTCHA 3:** Some generated image samples and individual image files exist from earlier experiments. They are exploratory, not an approved or complete asset set.

**GOTCHA 4:** New terrain/biome imagery and tileset exploration are paused. Do not generate, source, convert, stage or commit new images; do not establish a permanent asset directory/convention. This pause does not prohibit porting and adapting the existing tile-based renderer and already-available assets for the initial prototype.

**GOTCHA 5:** Do not turn this task into a repair of the 0/12 tileset, blank-map or terrain pipeline issues. First establish which parts of the existing tile-based setup work in the prototype; make only bounded compatibility fixes needed to demonstrate the initial UI. Report missing/broken dependencies rather than rebuilding the asset or terrain pipeline.

**GOTCHA 6:** Surface initialization is registered for DOMContentLoaded and Turbo navigation in the source. Verify it does not double-initialize.

### Multi-Domain / Multi-Tenant Routing
Not applicable. This is a standalone visual prototype task.

## Required Status Synthesis Report

Before implementation, save an MD file in the task-management repository at projects/galaxy-game-ui/summaries/. Use the filename pattern YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md. Do not paste the report into chat.

The report must contain:

### What I'm About to Do
State the bounded goal, first slice and success criteria.

### Files I'll Reference
List the source files, prototype targets and purpose of each.

### Prerequisites Completed
Confirm startup guidance, task review and source-path checks are complete.

### Expected Outcomes
List observable acceptance outcomes and the verification method.

### Critical Gotchas I Will Avoid
List the task-specific exclusions and stop conditions.

Do not begin implementation until startup reading and the synthesis report are complete.
## Review Gate — Stop Before Committing

Complete implementation and local verification, then stop and request independent review. Do not commit or push, or mark the task complete, without reviewer GO and Tracy's explicit authorization. Commit and push authorizations are separate.

## Problem Statement

The main app has an early Surface page and tile-based rendering pipeline. The prototype should port and adapt that existing work as the initial Surface UI, including the tile-based presentation where it can be made to work with bounded changes. This is an initial visual/design setup for evaluating the page, rendering and interactions—not the final game implementation or a commitment to the final terrain-data, tile-resolution, asset or rendering architecture. Terrain/biome/tileset asset exploration and broad pipeline repair remain out of scope.

## Files Involved

### Primary Files — expected prototype edit area

| File / area | Purpose |
|---|---|
| galaxy-game-ui/app/views/ and current routes | Add/adapt this page using existing conventions; inspect exact target paths first. |
| galaxy-game-ui/app/assets/ | Only minimum view-specific styles/scripts/assets required. |
| galaxy-game-ui/config/routes.rb | Only if a new destination is required; preserve existing route conventions. |

### Reference Files — read only

| File | Purpose |
|---|---|
| galaxyGame/galaxy_game/app/views/admin/celestial_bodies/surface.html.erb | Source page and embedded surface-data payload. |
| galaxyGame/galaxy_game/app/assets/javascripts/surface_view.js | Main Surface view controller/renderer. |
| galaxyGame/galaxy_game/app/assets/javascripts/biome_renderer.js and terrain_tile_renderer.js | Supporting presentation code; inspect dependencies. |
| galaxyGame/galaxy_game/app/assets/javascripts/tileset_loader.js and simple_tileset_loader.js | Verify which loader the source view actually initializes. |
| galaxy-game-ui/ current routes, views, layouts and assets | Inspect target state and reuse current prototype work. |

Do not edit the main GalaxyGame or EVE Dashboard repositories.

### Migration (if needed)

- No database migration is authorized or expected for this visual-prototype task.
- If implementation appears to require a migration, stop and ask Tracy before proceeding.

## Implementation Steps

### Step 0 — Task lifecycle

This task is approved and is in the flat tasks/backlog/ folder. Do not begin implementation until Tracy explicitly selects and dispatches it. At dispatch, follow the Agent Dispatch Interface Step 0 exactly.

### Step 1 — Verify current state

Inspect the current prototype branch, routes, layouts, assets and referenced source paths. Reuse existing work. If a required source or checkout is unavailable, stop and report the exact path rather than guessing.

### Step 2 — Implement the bounded first slice

1. Inspect the prototype and main-app Surface view. Confirm which JavaScript files and tileset loader the view actually uses before copying.
2. Port the Surface page structure and reusable view/controller code, including the existing tile-based rendering path where practical. Keep the first pass to Earth. Adapt the current implementation to the prototype rather than redesigning it.
3. Use the embedded surface-data payload shape or a clearly labeled mock with the same shape. Do not add a new reader or fixture framework.
4. Adapt the paths, initialization and asset references needed to run the existing tile-based setup in the prototype. Reuse existing assets that are already present; do not make new imagery a prerequisite.
5. Make bounded compatibility fixes to get the initial tile-based presentation working where feasible. If a specific experimental asset or dependency prevents this, preserve the working parts of the Surface UI and report the exact blocker. Do not replace the renderer or launch a new map implementation as part of this task.
6. Add the Surface destination to existing Player navigation and make focused layout adjustments.
7. Record what was successfully ported, what was adapted, renderer dependencies, usable existing assets and remaining asset/data gaps. Clearly identify the tile-based setup as provisional prototype implementation, leaving final architecture and future asset/data choices open.

### Step 3 — Verify

Run the prototype using its documented container workflow. Check the page in the running application, verify navigation and browser console/network behavior, and capture screenshot or equivalent visual evidence. Do not expand into unrelated repairs.

### Step 4 — Report

Save required evidence/synthesis artifacts in the task-management repository's project summaries area. Report changed/reused paths, checks performed, visual evidence, mock-data notes and remaining blockers. Stop for review; do not commit or push.

## Acceptance Criteria

- [ ] Surface is reachable in the Player context and its page initializes.
- [ ] Earth Surface reuses existing renderer/presentation code where compatible.
- [ ] The existing tile-based rendering setup is ported/adapted as the initial prototype approach where feasible; any bounded compatibility changes are documented.
- [ ] No Trident default, replacement tileset, new image sourcing/generation or broad terrain pipeline repair is introduced.
- [ ] Any unavailable imagery or broken dependency is reported with its exact path/reason; the prototype does not require a complete or final asset set.
- [ ] Documentation and implementation make clear that this is provisional prototype setup, not a final terrain/rendering architecture decision.
- [ ] Navigating away and back does not double-initialize.
- [ ] No main-app API/backend or live simulation is required.
- [ ] Check the running prototype and include screenshot/evidence, changed paths and limitations.

## Out of Scope

Choosing or developing a replacement/final tileset, FreeCiv or other tileset research, terrain/biome image generation or sourcing, geospatial dataset repair, broad pipeline debugging, replacing the existing renderer with a new map implementation, finalizing terrain/rendering architecture, backend/API integration and unrelated cleanup.

## Stop Conditions — escalate to Tracy immediately if

- A required source checkout, file or asset is unavailable and no explicitly permitted fallback exists.
- Completing the task requires backend/API work, simulation behavior, a new data framework or an unapproved game mechanic.
- The wiki, code or source data materially conflict about intended behavior.
- The work requires sourcing/generating/committing new visual assets beyond the stated scope.
- The task requires broad redesign or substantial rewriting of a renderer/module intended only for reuse.
- A referenced file differs materially from this task's description.

## Commit Instructions

Do not commit or push. Stop after implementation and verification and request reviewer assessment. A commit requires reviewer GO and Tracy's explicit authorization; pushing requires separate explicit authorization.

## Documentation

Do not duplicate design intent or create new design documents. Record source paths, data/asset provenance, mock fields and future API observations in the completion report. Update a wiki only if Tracy explicitly approves a genuine design clarification.

## Dependencies

- **Blocked by:** None.
- **Blocks:** None.
- **Related:** The other GalaxyGame UI draft tasks are separate page/workspace slices, not automatic prerequisites.

## Progress / Resume

**Status:** Not started — approved backlog task.  
**Resume:** false.  
**Next step:** Await Tracy's selection and explicit dispatch. Dispatch one task at a time; do not infer authorization from backlog status.

## Completion Report

When authorized implementation is completed, report:

- **Changed files:** exact paths.
- **Reused source:** modules, source paths and revisions/provenance.
- **Verification:** commands, checks and running page inspected.
- **Evidence:** screenshot or equivalent path.
- **Data/assets:** imported data, mock fields, missing data/assets and future API observations.
- **Issues:** limitations and stop conditions encountered.
- **Follow-up:** only work genuinely outside this task and requiring a separate decision.

## Handoff Summary

Summarize the implemented first slice, verification evidence, remaining limitations and exact next step. Do not imply approval, commit or push unless separately authorized and completed.
