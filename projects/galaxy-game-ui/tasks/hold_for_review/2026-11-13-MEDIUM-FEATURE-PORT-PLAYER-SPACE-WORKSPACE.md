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
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SPACE-WORKSPACE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SPACE-WORKSPACE.md \
         projects/galaxy-game-ui/tasks/active/2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SPACE-WORKSPACE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SPACE-WORKSPACE.md"
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

# TASK: Create the Player Space Workspace First Slice
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

Build one bounded Sol Space visualization by reusing the existing GalaxyGame renderer where appropriate and adapting only the EVE Dashboard fleet-map behavior that genuinely fits.

## Relevant Architecture Docs — read before starting

- galaxyGame/docs/wiki/ui/02_player_view_architecture.md
- galaxy-game-ui/docs/wiki/README.md
- galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md
- galaxy-game-ui/docs/wiki/interface-pattern-references.md
- galaxy-game-ui/docs/wiki/data-integration-and-migration.md

## Critical Information for This Task

### Credentials
Not required.

### Architecture Gotchas

**GOTCHA 1:** This is a concrete first slice, not an open-ended audit of EVE Dashboard modules.

**GOTCHA 2:** Use fleet_map.js as the only initial EVE candidate. If it is unavailable or adapting it would require substantial rewriting, skip the EVE interaction pattern and continue with a minimal Sol view using only the GalaxyGame renderer baseline. Report the unavailable source or reason for skipping it; do not invent a substitute.

**GOTCHA 3:** Do not use chain.js, wealth.js, market.js, assets.js or structures.js.

**GOTCHA 4:** An in-system solar-system view is distinct from an inter-system wormhole-connection map. Do not substitute one for the other.

**GOTCHA 5:** Do not import EVE terminology, branding, account data, game rules, backend behavior, route/shortest-path features or fleet gameplay assumptions.

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

The Player Space context needs a useful first visual workspace. The main GalaxyGame renderer is the domain baseline; EVE Dashboard may offer a reusable interaction pattern, but its other modules and inter-system map concepts should not be imported indiscriminately.

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
| galaxyGame/galaxy_game/app/javascript/system_renderer.js | Read-only reference for GalaxyGame's in-system solar-system rendering baseline. |
| galaxyGame/galaxy_game/app/assets/javascripts/system_renderer.js | Verify this copy; it was reported as a one-line file and is not presumed to be implementation. |
| EVE Dashboard checkout: fleet_map.js | Only initial EVE candidate for pan/zoom and marker selection; verify source access and dependencies. |
| galaxy-game-ui/ current routes, views, assets and navigation | Inspect target prototype and reuse current shell/components. |

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

1. Inspect the prototype and locate the actual GalaxyGame Space/system presentation baseline. Read app/javascript/system_renderer.js; do not assume the similarly named asset-pipeline file is equivalent.
2. Check whether the EVE Dashboard checkout and fleet_map.js are accessible and whether the relevant interaction code can be adapted without substantial rewriting. If the file is unavailable or would require substantial rewriting, skip the EVE adaptation, continue with the minimal Sol view using only the GalaxyGame renderer baseline, and report the missing path or reason for skipping it. Do not use machine-specific absolute paths or fabricate source.
3. Create one Sol-focused Space page showing sample bodies, stations and ships. Reuse the GalaxyGame renderer if it fits without substantial rewriting.
4. If fleet_map.js is available, adapt only its pan/zoom, marker-selection and selected-object-panel interaction pattern. Keep GalaxyGame's in-system representation and terminology.
5. Use available imported data or clearly labeled mock values. Do not create a new data framework.
6. Integrate the page into existing Player navigation/shell. Record source provenance, dependencies and EVE-specific terms removed.

### Step 3 — Verify

Run the prototype using its documented container workflow. Check the page in the running application, verify navigation and browser console/network behavior, and capture screenshot or equivalent visual evidence. Do not expand into unrelated repairs.

### Step 4 — Report

Save required evidence/synthesis artifacts in the task-management repository's project summaries area. Report changed/reused paths, checks performed, visual evidence, mock-data notes and remaining blockers. Stop for review; do not commit or push.

## Acceptance Criteria

- [ ] Space is reachable in the Player context.
- [ ] A Sol view displays representative celestial bodies, stations and ships using GalaxyGame terminology.
- [ ] If fleet_map.js is available and can be adapted without substantial rewriting, its bounded pan/zoom and selection pattern is used. If it is unavailable or unsuitable, deliver the minimal Sol view using the GalaxyGame renderer baseline alone and report the fallback.
- [ ] The implementation distinguishes an in-system view from inter-system wormhole connections.
- [ ] No EVE gameplay rules, backend or live route calculations are introduced.
- [ ] Record source revisions/provenance and check the page in the running prototype with visual evidence.

## Out of Scope

Inter-system wormhole map, chain/pathfinding features, EVE chain/wealth/market/assets/structures modules, orbital simulation, route calculations, backend/API integration and wholesale EVE Dashboard port.

## Stop Conditions — escalate to Tracy immediately if

- A required GalaxyGame source checkout, file or asset is unavailable and no explicitly permitted fallback exists. The EVE Dashboard checkout or fleet_map.js being unavailable is not, by itself, a blocker; use the documented GalaxyGame-only fallback.
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
