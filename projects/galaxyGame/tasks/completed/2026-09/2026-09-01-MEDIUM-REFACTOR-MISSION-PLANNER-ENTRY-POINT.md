---
status: completed
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
last_updated: 2026-09-30
completed_by: Implementation Agent
completed_date: 2026-09-30
final_test_result: "22 examples, 0 failures (focused spec)"
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for implementation.**

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
**Last Updated**: 2026-09-30  

---

## Context

`MissionPlannerService` is currently initialized with a `pattern_name` and uses `PatternTargetMapper` to select a target. This locks the AI Manager into pre-written patterns.

After the Resource-First Foothold Planner architecture exists, this service (or a thin wrapper around it) needs an entry path that accepts a body/system snapshot instead of (or in addition to) a pattern name. Existing pattern-driven callers must continue to work.

This is a compatibility/refactor step, not a full rewrite.

**Relevant files**:
- `app/services/ai_manager/mission_planner_service.rb` — current pattern-first service
- `app/services/ai_manager/pattern_target_mapper.rb` — existing mapper (used by pattern path only)
- `app/services/ai_manager/foothold_planner.rb` — **ALREADY EXISTS on main** (not a future file); compose with it, do not rewrite

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

⚠️ **GOTCHA 4: Out of scope — do not touch these**
- Multi-system coordination
- Rewriting FootholdPlanner internals
- Deprecating or changing existing pattern callers
- Full resource-first architecture rewrite

---

## Required Interface (Source-Backed, No Invented APIs)

### 1. Pattern path MUST stay unchanged
```ruby
AIManager::MissionPlannerService.new(pattern_name, parameters = {})
```
- First argument is **POSITIONAL** (not keyword).
- Uses `PatternTargetMapper` internally — do not remove or alter this path.

### 2. Add resource-first entry point
```ruby
AIManager::MissionPlannerService.for_body(celestial_body, system_context: {}, parameters: {})
```
- Class method (factory-style), NOT a new initializer.
- Must **NOT** use `PatternTargetMapper` for this path.
- `system_context` keys (from FootholdPlanner comments): `:moons`, `:asteroids`, `:distance_from_sun`, `:parent_body`, `:nearby_nodes`.
- Capability stays internal via `PrecursorCapabilityService` inside FootholdPlanner — do not expose it here.

### 3. Compose with existing FootholdPlanner (do not rewrite)
```ruby
AIManager::FootholdPlanner.new(celestial_body, system_context: {}).plan
```
- Public entry is `#plan`, **not** `#call`.
- Returns ranked Array of `FootholdOption` (has `to_h`).
- MissionPlannerService should delegate to this, not reimplement the ranking logic.

---

## Problem Statement

MissionPlannerService can only be driven by a pattern name. There is no clean way for a resource-first foothold decision to flow into the existing simulation / costing / contract helpers.

**Current behavior**: `MissionPlannerService.new(pattern_name, parameters)`  
**Expected behavior**: Pattern path still works; an additional `for_body` entry path accepts body + system context and composes with the existing FootholdPlanner.

---

## Files Involved

| Path | Role |
|---|---|
| `galaxy_game/app/services/ai_manager/mission_planner_service.rb` | Add `for_body` class method; keep `.new(pattern_name, params)` intact |
| `galaxy_game/app/services/ai_manager/pattern_target_mapper.rb` | Reference only — not used by `for_body` path |
| `galaxy_game/app/services/ai_manager/foothold_planner.rb` | **ALREADY EXISTS on main** — compose via `.new(body, system_context: {}).plan`; do not rewrite |

---

## Implementation Steps

1. Verify FootholdPlanner exists on main and confirm its public interface: `#plan` returns ranked Array of FootholdOption with `to_h`.
2. Add class method `for_body(celestial_body, system_context: {}, parameters: {})` to MissionPlannerService.
3. Inside `for_body`: validate that `celestial_body` is present (raise if nil); compose with `FootholdPlanner.new(celestial_body, system_context: {}).plan`.
4. Ensure `.new(pattern_name, parameters = {})` still works — no arity changes to the existing initializer.
5. Document the dual-path contract in code comments.
6. Do **not** rewrite FootholdPlanner internals, multi-system coordination, or existing pattern callers.

---

## Acceptance Criteria (Measurable)

- [ ] `.new(pattern_name, {})` still works — no arity error; simulate path intact
- [ ] `for_body(valid_body, ...)` returns foothold options without using PatternTargetMapper
- [ ] `for_body(nil)` or missing body raises explicit error (no silent OpenStruct target)
- [ ] No multi-system coordination logic added
- [ ] FootholdPlanner internals untouched; composed via its public `#plan` entry only

---

## Dependencies

**Blocked by**: None  
**Blocks**: Clean integration of resource-first decisions into existing planning helpers  
**Related**: All other ai-manager/ backlog tasks

---

## Stop Conditions

- Requires deleting or heavily breaking the pattern path without a migration plan
- Turns into a full rewrite of MissionPlannerService
- Requires rewriting FootholdPlanner internals

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
- `.new(pattern_name, {})` still works (no arity error; simulate path intact)
- `for_body(valid_body, ...)` returns foothold options without PatternTargetMapper
- `for_body(nil)` or missing body raises explicit error (no silent OpenStruct target)
- No broad rewrite of internal costing/timeline logic

### Critical Gotchas I Will Avoid
- ❌ Breaking existing callers — instead ✅ dual path
- ❌ Implementing the full planner — instead ✅ compatibility layer only
- ❌ Using PatternTargetMapper for the `for_body` path
- ❌ Rewriting FootholdPlanner internals — instead ✅ compose via `.new(body, system_context: {}).plan`

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
| `galaxy_game/app/services/ai_manager/mission_planner_service.rb` | Add `for_body` class method; keep `.new(pattern_name, params)` intact |

### Reference (do not edit)
| File | Why |
|---|---|
| `galaxy_game/app/services/ai_manager/pattern_target_mapper.rb` | Used by pattern path only — `for_body` must NOT use it |
| `galaxy_game/app/services/ai_manager/foothold_planner.rb` | **ALREADY EXISTS on main** — compose via `.new(body, system_context: {}).plan`; do not rewrite |

### Call Sites (verify no breakage)
| File | Why |
|---|---|
| All callers of `MissionPlannerService.new(...)` | Must continue to work unchanged |

---

## Implementation Steps

1. Verify FootholdPlanner exists on main and confirm its public interface: `#plan` returns ranked Array of FootholdOption with `to_h`.
2. Add class method `for_body(celestial_body, system_context: {}, parameters: {})` to MissionPlannerService.
3. Inside `for_body`: validate that `celestial_body` is present (raise if nil); compose with `FootholdPlanner.new(celestial_body, system_context: {}).plan`.
4. Ensure `.new(pattern_name, parameters = {})` still works — no arity changes to the existing initializer.
5. Document the dual-path contract in code comments.
6. Do **not** rewrite FootholdPlanner internals, multi-system coordination, or existing pattern callers.

---

## Acceptance Criteria (Measurable)

- [ ] `.new(pattern_name, {})` still works — no arity error; simulate path intact
- [ ] `for_body(valid_body, ...)` returns foothold options without using PatternTargetMapper
- [ ] `for_body(nil)` or missing body raises explicit error (no silent OpenStruct target)
- [ ] No multi-system coordination logic added
- [ ] FootholdPlanner internals untouched; composed via its public `#plan` entry only

---

## Dependencies

**Blocked by**: None  
**Blocks**: Clean integration of resource-first decisions into existing planning helpers  
**Related**: All other ai-manager/ backlog tasks

---

## Stop Conditions

- Requires deleting or heavily breaking the pattern path without a migration plan
- Turns into a full rewrite of MissionPlannerService
- Requires rewriting FootholdPlanner internals
