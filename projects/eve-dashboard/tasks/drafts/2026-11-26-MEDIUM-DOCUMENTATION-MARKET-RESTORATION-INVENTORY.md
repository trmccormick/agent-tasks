---
status: draft
resume: false
priority: MEDIUM
type: documentation
system_domain: MARKET_DASHBOARD
mvp_alignment: OTHER
local_worker_safe: true
---

## Agent Dispatch Interface

```
You are **Implementation Agent**.

Project: eve-dashboard
Task: <agent-tasks>/projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
       git ls-files --error-unmatch projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
     If the source is not tracked, stage the source first:
       git add projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md \
              projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
       test ! -e projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md && test -f projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/eve-dashboard/tasks -name "2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/eve-dashboard/README.md
  3. <agent-tasks>/projects/eve-dashboard/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/eve-dashboard/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

## Draft Status Note

This task is in `tasks/drafts/` and awaits review approval. It is not approved for dispatch. After review GO and human approval, move it to `tasks/backlog/market/` and change YAML status to `backlog`.

## Task: Inventory the Existing Market Feature for Restoration

**Status:** DRAFT  
**Priority:** MEDIUM  
**Type:** documentation / read-only inventory  
**Created:** 2026-11-26  
**Last Updated:** 2026-11-26

## Context

The current working application branch is `local/docker-customizations`. The earlier Market implementation is on `local/improvements`. The goal is to restore the existing multi-character open-orders feature without disturbing the completed Docker/environment work on the current branch.

Read the design intent in `projects/eve-dashboard/wiki/market/README.md` before inspecting code. The wiki defines the initial restoration boundary; this task only inventories and compares code and prior task evidence. It does not authorize implementation or broad redesign.

## Scope

Read-only inspection of:
- Current working branch `local/docker-customizations`.
- Reference branch `local/improvements`.
- Existing Market-related task and synthesis records in `agent-tasks/projects/eve-dashboard/`.

Identify the actual current branch and working-tree state before inspecting files. Do not switch branches, reset, stash, clean, cherry-pick, merge, or modify either repository.

## Required investigation

1. Confirm the checked-out branch and working-tree status. Preserve all existing changes.
2. Compare the current branch with `local/improvements` for Market-specific files and relevant integration points:
   - Market module and templates/static assets.
   - Database schema and Market persistence helpers.
   - ESI endpoint wrapper and required scope handling.
   - Character token retrieval and background sync integration.
   - Feature toggles/preferences and navigation.
   - Routes/API endpoints and request/response shapes.
   - Tests and test fixtures.
3. Identify which files/features exist only on `local/improvements`, which already exist on the current branch, and which have diverged.
4. Trace one complete open-orders path in each branch where possible: character selection/token → ESI request → successful/failed response handling → persistence → route/API → UI rendering.
5. Inspect the old Market-related task records:
   - `2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md`
   - `2026-10-01-MARKET-BUG-SYNTHESIS.md`
   - `2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md`
   - `2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md`
   Separate findings relevant to open orders from mining ledger, wallet-journal sales, price-history, Jita-specific assumptions, and speculative/unverified claims.
6. Assess the prior investigation findings against the current code. Classify each as:
   - confirmed in current branch,
   - confirmed only in reference branch,
   - no longer applicable,
   - unverified / requires runtime reproduction.
   Do not implement fixes.
7. Produce a narrow, ordered restoration plan. Each proposed follow-on task must have one coherent goal, explicit dependencies, exact likely file boundaries, and a small verification plan suitable for local Qwen.

## Deliverable

Create `projects/eve-dashboard/summaries/2026-11-26-MARKET-RESTORATION-INVENTORY.md` containing:

- Branch names, exact commit SHAs, and working-tree status observed.
- A branch comparison table listing Market-related paths and their status.
- A verified end-to-end data-flow description for each branch (or clearly identified gaps).
- Current ESI scopes and feature-toggle integration points.
- Database tables/helpers/indexes involved.
- Routes, response payloads, UI capabilities, and existing tests.
- A findings matrix for the four legacy Market records listed above.
- A list of unresolved questions requiring human input.
- A proposed sequence of small implementation tasks, with each task linked to the wiki intent and dependencies.
- Evidence: exact file paths and relevant line numbers or concise code references; distinguish direct inspection from historical task claims.

Do not create or edit wiki pages in this task. The existing Market intent page is the specification baseline. If code evidence contradicts it or exposes a design decision not covered by it, record the conflict and ask for human direction.

## Non-goals / hard constraints

- No application code, schema, configuration, Docker, environment, or test changes.
- No branch switching, merging, cherry-picking, rebasing, committing, or pushing.
- Do not change credentials or environment files.
- Do not assume Jita is the user's universal market location.
- Do not add Market Browser, cross-region price comparisons, price history, mining ledger, wallet-journal sales tracking, or trading automation to this restoration.
- Do not move, rename, rewrite, or change the lifecycle status of existing active/backlog task files.
- Do not mark old investigations complete merely because their historical work is finished.

## Acceptance Criteria

- [ ] Both branches and their exact checked-out/reference SHAs are recorded.
- [ ] Working-tree changes are preserved and explicitly reported.
- [ ] Market-related implementation differences are documented with verified paths.
- [ ] Open-orders data flow is traced end to end or gaps are explicitly named.
- [ ] ESI scope, feature toggle, DB, sync, route, UI, and test integration points are identified.
- [ ] Legacy task claims are classified by current evidence and separated from out-of-scope work.
- [ ] At least one small, ordered follow-on task sequence is proposed and mapped to wiki sections.
- [ ] No files outside the required synthesis report are changed.
- [ ] Report distinguishes verified facts, historical claims, and unresolved questions.

## Dependencies

- **Depends on:** None.
- **Blocks:** Drafting implementation tasks for Market restoration.
- **Wiki reference:** `projects/eve-dashboard/wiki/market/README.md`.

## Architecture Gotchas

1. The Docker/environment customization work on `local/docker-customizations` is complete and must be preserved. This task is read-only.
2. The `local/improvements` branch is reference source, not a branch to merge wholesale.
3. Market order location is per-order data. Do not hardcode Jita.
4. Open orders are distinct from wallet-journal market sales and mining data.
5. Historical findings are not proof of current behavior; re-check source and label runtime-only claims unverified unless reproduced.

## Synthesis Report

Save the report at:
`projects/eve-dashboard/summaries/2026-11-26-MARKET-RESTORATION-INVENTORY.md`

Use the standard synthesis stages required by `TASK_TEMPLATE.md`. Since this is a read-only inventory task, Stage 3 records report validation and confirms no application files were modified. Do not commit or push without reviewer GO and explicit human authorization.
