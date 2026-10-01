---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md \
         projects/galaxy_game/tasks/active/2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-01-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Evolve MissionPlannerService Entry Point (Pattern-First → Resource-First Compatible)
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: refactor  
**Created**: 2026-09-01  
**Last Updated**: 2026-09-01  

---

## Context

`MissionPlannerService` is currently initialized with a `pattern_name` and uses `PatternTargetMapper` to select a target. This locks the AI Manager into pre-written patterns.

After the Resource-First Foothold Planner architecture exists, this service (or a thin wrapper around it) needs an entry path that accepts a body/system snapshot instead of (or in addition to) a pattern name. Existing pattern-driven callers must continue to work.

This is a compatibility/refactor step, not a full rewrite.

**Relevant files**:
- `app/services/ai_manager/mission_planner_service.rb`
- `app/services/ai_manager/pattern_target_mapper.rb` (if present)
- Future `foothold_planner.rb` (from the HIGH architecture task)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Do not break existing pattern-based callers**
- ❌ Wrong: Remove or rename the pattern_name initializer and break all current usage
- ✅ Right: Add a parallel resource/system entry path; keep the old path working
- Why: Luna and other current work still relies on the existing interface.

⚠️ **GOTCHA 2: Do not implement the full foothold planner here**
- ❌ Wrong: Turn this task into the complete resource-first planner
- ✅ Right: Make MissionPlannerService able to accept or cooperate with resource-first input
- Why: The architecture task owns the new planner design; this task only opens the door.

⚠️ **GOTCHA 3: Prefer composition over deep rewrite**
- ❌ Wrong: Large internal restructuring of costing, timeline, and TerraSim logic
- ✅ Right: New entry method or thin adapter that can later call FootholdPlanner + existing simulation helpers
- Why: Keeps risk low while Luna work continues.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Evolve MissionPlannerService Entry Point
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Add a resource/system-compatible entry path to MissionPlannerService (or a thin wrapper) while preserving the existing pattern_name path.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/mission_planner_service.rb` | Current pattern-first service | pending |
| Resource-First Foothold Planner architecture task / skeleton | Target design | pending |

### Prerequisites Completed
- ✅ Step 0 completed
- ✅ Read this task and the HIGH foothold-planner architecture task
- ✅ Understand the three Gotchas

### Expected Outcomes
- Existing pattern_name path still works
- New entry path accepts body/system context (or clearly documents how it will)
- No broad rewrite of internal costing/timeline logic

### Critical Gotchas I Will Avoid
- ❌ Breaking existing callers — instead ✅ dual path
- ❌ Implementing the full planner — instead ✅ compatibility layer only

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

MissionPlannerService can only be driven by a pattern name. There is no clean way for a resource-first foothold decision to flow into the existing simulation / costing / contract helpers.

**Current behavior**: `MissionPlannerService.new(pattern_name, parameters)`  
**Expected behavior**: Pattern path still works; an additional path accepts (or is prepared to accept) body + system context.

---

## Files Involved

### Primary
| File | Purpose |
|---|---|
| `app/services/ai_manager/mission_planner_service.rb` | Add or adapt entry point |

### Reference
| File | Why |
|---|---|
| Future foothold planner skeleton | Target interface |
| Call sites of MissionPlannerService | Must not break |

---

## Implementation Steps

1. Inventory current initializers and public methods of MissionPlannerService.
2. Design a parallel entry (new class method, new initializer overload, or thin adapter service).
3. Implement the minimal change that allows resource/system context to be accepted without requiring a pattern name.
4. Ensure all existing pattern-based call sites continue to function.
5. Document the dual-path contract in code comments or a short note.
6. Do **not** rewrite the internal simulation/costing logic in this task.

---

## Acceptance Criteria

- [ ] Existing `pattern_name` entry path still works
- [ ] A resource/system-compatible entry path exists (or is clearly stubbed with the intended contract)
- [ ] No broad rewrite of costing, timeline, or TerraSim integration
- [ ] Call-site breakage is zero (or explicitly listed and approved)
- [ ] Relationship to the FootholdPlanner architecture is documented

---

## Dependencies

**Blocked by**: Preferably the HIGH Resource-First Foothold Planner architecture task (can be sketched in parallel)  
**Blocks**: Clean integration of resource-first decisions into existing planning helpers  
**Related**: Foothold Planner architecture, Luna worked-example capture

---

## Stop Conditions

- Requires deleting or heavily breaking the pattern path without a migration plan
- Turns into a full rewrite of MissionPlannerService
