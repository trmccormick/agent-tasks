---
status: draft
resume: false
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## DRAFT STATUS NOTE

This task is a draft awaiting independent review and Tracy's approval. It is not approved for dispatch and must remain in tasks/drafts/.

## DRAFT REVIEW CHECKLIST

- [ ] Acceptance criteria are clear and measurable.
- [ ] Scope is bounded and does not overlap another active task.
- [ ] Dependencies and source paths have been verified.
- [ ] The task matches the current wiki and prototype state.
- [ ] Independent reviewer disposition is GO.
- [ ] Tracy approves moving this task to backlog.

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
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-PLANETARY-VIEW.md.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-PLANETARY-VIEW.md.md \
         projects/galaxy-game-ui/tasks/active/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-PLANETARY-VIEW.md.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-PLANETARY-VIEW.md"
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

# TASK: Port the Player Planetary View
**Status**: DRAFT  
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

1. <agent-tasks>/rules/GUARDRAILS.md
2. <agent-tasks>/projects/galaxy-game-ui/README.md
3. <agent-tasks>/projects/galaxy-game-ui/SESSION_GUIDANCE.md
4. The current prototype wiki references below.
5. This task in full.

The prototype is Rails 8.1 and is separate from the Rails 7 main GalaxyGame application. Work only in <galaxy-game-ui>. Main-game and EVE source repositories are read-only references.

## Context

Port the existing Planetary view into the separate Rails 8.1 prototype, reusing its renderer and presentation behavior. This is a static visual prototype; the main GalaxyGame backend remains paused.

## Relevant Architecture Docs — read before starting

- <galaxyGame>/docs/wiki/ui/02_player_view_architecture.md
- <galaxyGame>/docs/wiki/ui/04_current_ui_state_and_known_issues.md
- <galaxy-game-ui>/docs/wiki/README.md
- <galaxy-game-ui>/docs/wiki/javascript-reuse-and-porting-plan.md
- <galaxy-game-ui>/docs/wiki/data-integration-and-migration.md

## Critical Information for This Task

### Credentials
Not required.

### Architecture Gotchas

**GOTCHA 1:** The source view is Admin-facing. Do not port Generate/Regenerate Terrain, Edit Properties, or Admin links.

**GOTCHA 2:** monitor.js currently polls every five seconds and fetches /admin/celestial_bodies/:id/sphere_data. The prototype must not call this endpoint.

**GOTCHA 3:** The wiki records coarse/blurry map rendering. Do not diagnose or repair that known issue in this task; report it as a limitation.

**GOTCHA 4:** Earth is the required first body. Mars, Venus and Titan are not acceptance requirements.

### Multi-Domain / Multi-Tenant Routing
Not applicable. This is a standalone visual prototype task.

## Required Status Synthesis Report

Before implementation, save a concise synthesis report as an MD file in <agent-tasks>/projects/galaxy-game-ui/summaries/. Include the goal, source files/targets, constraints, stop conditions, acceptance checks and verification plan. Do not paste the report into chat. Do not begin implementation until startup reading and synthesis are complete.

## Review Gate — Stop Before Committing

Complete implementation and local verification, then stop and request independent review. Do not commit or push, or mark the task complete, without reviewer GO and Tracy's explicit authorization. Commit and push authorizations are separate.

## Problem Statement

The main app has a useful Planetary presentation, but it is an Admin page and its renderer polls a main-app endpoint. The prototype needs a Player-facing, self-contained version without Admin actions or live backend dependencies.

## Files Involved

### Primary Files — expected prototype edit area

| File / area | Purpose |
|---|---|
| <galaxy-game-ui>/app/views/ and current routes | Add/adapt this page using existing conventions; inspect exact target paths first. |
| <galaxy-game-ui>/app/assets/ | Only minimum view-specific styles/scripts/assets required. |
| <galaxy-game-ui>/config/routes.rb | Only if a new destination is required; preserve existing route conventions. |

### Reference Files — read only

| File | Purpose |
|---|---|
| <galaxyGame>/galaxy_game/app/views/admin/celestial_bodies/planetary.html.erb | Source page and embedded monitor-data payload. |
| <galaxyGame>/galaxy_game/app/javascript/admin/monitor.js | Renderer; inspect initialization and polling. |
| <galaxy-game-ui>/ current routes, views, layouts and assets | Inspect and reuse the current prototype. |

Do not edit the main GalaxyGame or EVE Dashboard repositories.

## Implementation Steps

### Step 0 — Task lifecycle

This file is a draft. Do not move it, change its status, or begin implementation. After Tracy approves it and moves it to the flat tasks/backlog/ folder, follow the Agent Dispatch Interface Step 0 exactly.

### Step 1 — Verify current state

Inspect the current prototype branch, routes, layouts, assets and referenced source paths. Reuse existing work. If a required source or checkout is unavailable, stop and report the exact path rather than guessing.

### Step 2 — Implement the bounded first slice

1. Inspect the current prototype and source view/renderer. Verify paths and the embedded payload shape before copying.
2. Port the Planetary page for Earth, including terrain and one available overlay: hydrosphere or temperature.
3. Reuse the existing renderer and required presentation assets where practical. Convert only necessary styles to existing prototype conventions.
4. Remove the polling timer and fetch dependency from the prototype copy. Use an available imported snapshot or a clearly labeled mock matching the inspected payload shape. Do not add a new data reader/framework.
5. Remove Admin-only controls and links. Add the page to existing Player navigation without redesigning the shell.
6. Record data/assets used, mocked fields, and missing information for later API discovery.

### Step 3 — Verify

Run the prototype using its documented container workflow. Check the page in the running application, verify navigation and browser console/network behavior, and capture screenshot or equivalent visual evidence. Do not expand into unrelated repairs.

### Step 4 — Report

Save required evidence/synthesis artifacts in the task-management repository's project summaries area. Report changed/reused paths, checks performed, visual evidence, mock-data notes and remaining blockers. Stop for review; do not commit or push.

## Acceptance Criteria

- [ ] Planetary is reachable in the Player context.
- [ ] Earth renders with terrain and one available overlay.
- [ ] No polling timer, main-app endpoint, or backend is required.
- [ ] Admin-only controls and links are absent.
- [ ] Imported/mock provenance is visibly distinguishable.
- [ ] Check the running prototype; report console errors, failed requests and visual limitations. Include screenshot/evidence and exact changed/reused paths.

## Out of Scope

Surface, Space, Settlement, Admin shell work, new data infrastructure, terrain-quality repair, production API integration, simulation behavior and unrelated cleanup.

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

**Status:** Not started — draft only.  
**Resume:** false.  
**Next step:** Independent review by Grok, then Tracy decides whether to revise, approve or retire this draft.

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
