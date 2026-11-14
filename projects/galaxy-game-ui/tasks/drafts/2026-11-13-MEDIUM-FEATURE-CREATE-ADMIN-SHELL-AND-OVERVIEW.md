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
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-CREATE-ADMIN-SHELL-AND-OVERVIEW.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/2026-11-13-MEDIUM-FEATURE-CREATE-ADMIN-SHELL-AND-OVERVIEW.md \
         projects/galaxy-game-ui/tasks/active/2026-11-13-MEDIUM-FEATURE-CREATE-ADMIN-SHELL-AND-OVERVIEW.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-11-13-MEDIUM-FEATURE-CREATE-ADMIN-SHELL-AND-OVERVIEW.md"
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

# TASK: Create the Admin Shell and Overview
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

1. agent-tasks/rules/GUARDRAILS.md
2. agent-tasks/projects/galaxy-game-ui/README.md
3. agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md
4. The current prototype wiki references below.
5. This task in full.

The prototype is Rails 8.1 and is separate from the Rails 7 main GalaxyGame application. Work only in galaxy-game-ui. Main-game and EVE source repositories are read-only references.

## Context

Create a distinct Admin shell and useful overview entry point. The current prototype overview is a thin placeholder, so this is creation/consolidation rather than polishing an already substantial dashboard.

## Relevant Architecture Docs — read before starting

- galaxyGame/docs/wiki/ui/03_admin_and_ai_manager.md §§1–2
- galaxy-game-ui/docs/wiki/README.md
- galaxy-game-ui/docs/wiki/screen-concepts-and-wireframes.md

## Critical Information for This Task

### Credentials
Not required.

### Architecture Gotchas

**GOTCHA 1:** Do not describe the current overview as a substantial dashboard to consolidate. Preserve useful pieces, but create the missing Admin shell and overview structure.

**GOTCHA 2:** Keep Admin and Player navigation visibly separate and shallow.

**GOTCHA 3:** Only include panels supported by wiki 03 §2 or clearly mark a proposed placeholder for Tracy's review.

**GOTCHA 4:** Do not invent a Pending Approvals KPI or imply mock indicators are live.

**GOTCHA 5:** AI Manager is a workspace within Admin, not a separate top-level interface or Player context.

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

The current prototype's Admin overview is a thin placeholder and renders within the Player layout/sidebar. Admin needs a distinct shell and an overview entry point, while remaining a visual prototype.

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
| galaxy-game-ui/ current routes, Admin::BaseController, layouts, dashboard views and sidebar | Inspect actual current behavior before changing the shell. |
| galaxy-game-ui/docs/wiki/ | Use current Admin page intent; do not rely on stale interface-and-workflows descriptions of Player views/interfaces. |

Do not edit the main GalaxyGame or EVE Dashboard repositories.

### Migration (if needed)

- No database migration is authorized or expected for this visual-prototype task.
- If implementation appears to require a migration, stop and ask Tracy before proceeding.

## Implementation Steps

### Step 0 — Task lifecycle

This file is a draft. Do not move it, change its status, or begin implementation. After Tracy approves it and moves it to the flat tasks/backlog/ folder, follow the Agent Dispatch Interface Step 0 exactly.

### Step 1 — Verify current state

Inspect the current prototype branch, routes, layouts, assets and referenced source paths. Reuse existing work. If a required source or checkout is unavailable, stop and report the exact path rather than guessing.

### Step 2 — Implement the bounded first slice

1. Inspect current routes, controllers, layouts and dashboard view. Confirm how Admin pages currently inherit the Player layout.
2. Create a distinct Admin layout and shallow Admin sidebar/topbar using existing prototype styling/components.
3. Create an Admin overview with 4–6 panels selected from the Admin overview/system-panel intent in wiki §2. Use grounded panel concepts and label sample data as mock.
4. Provide an AI Manager destination as a clearly marked placeholder until its workspace exists. Do not invent a broader Admin tool suite.
5. Ensure Admin pages use the Admin shell while Player pages retain the Player shell.

### Step 3 — Verify

Run the prototype using its documented container workflow. Check the page in the running application, verify navigation and browser console/network behavior, and capture screenshot or equivalent visual evidence. Do not expand into unrelated repairs.

### Step 4 — Report

Save required evidence/synthesis artifacts in the task-management repository's project summaries area. Report changed/reused paths, checks performed, visual evidence, mock-data notes and remaining blockers. Stop for review; do not commit or push.

## Acceptance Criteria

- [ ] Admin has a distinct layout/navigation separate from Player.
- [ ] The overview presents 4–6 panels grounded in wiki §2, with mock data clearly identified.
- [ ] No invented Pending Approvals KPI or live operational claims are present.
- [ ] AI Manager destination is a marked placeholder if its page is not implemented.
- [ ] Every active link resolves; unavailable destinations are visibly disabled/placeholders.
- [ ] No horizontal overflow at 1280px; check the running prototype and include visual evidence.

## Out of Scope

Implementing individual Admin tools, AI Manager internals, unsupported Map Studio/StarSim/TerraSim/Accounts, simulation controls, production services/APIs, permissions/backend functionality and unrelated redesign.

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
