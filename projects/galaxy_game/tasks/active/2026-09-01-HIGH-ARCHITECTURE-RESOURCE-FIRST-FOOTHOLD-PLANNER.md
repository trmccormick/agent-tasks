---
status: active
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md \
         projects/galaxy_game/tasks/active/2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-01-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Resource-First Foothold Planner (Architecture + Minimal Interface)
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: architecture  
**Created**: 2026-09-01  
**Last Updated**: 2026-09-02  

---

## Context

This task extends an **already established design vision**. It does not introduce a new philosophy.

### Prior art (read first — do not reinvent)

The following documents already establish resource-first, body-agnostic, non-hardcoded decision making. The Foothold Planner must sit in the same family:

1. **`docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md`**  
   - "The AI Manager does not hardcode which materials are emergencies. It evaluates the current state of the settlement and decides."  
   - ISRU-first even inside emergency response  
   - State-based triggers (`time_to_critical` vs resupply window); seed patterns teach, they do not permanently lock behavior  

2. **`docs/architecture/services/ai_manager/AI_MANAGER_CONSTRUCTION_ECONOMICS.md`**  
   - Build priority: local resources + robot labor → player contracts → NPC import last  
   - "The AI Manager always checks local extraction cost against import EAP and chooses the cheaper option."  
   - Player-first; DC self-sufficiency exists to prevent failure, not to replace players  

3. **`docs/architecture/services/ai_manager/CYCLER_SYSTEM_ARCHITECTURE.md`**  
   - Generic base platform + mission fits as operational data  
   - Same cycler reconfigured per mission; equipment transfers or returns — not per-world code paths  

4. **`docs/architecture/simulation/construction_system.md`**  
   - Generic regolith I-beam + panel methodology; Luna is first implementation, not a separate architecture  
   - Pressurization adapts to body resources (Mars ambient compression vs Luna shipped N2 + extracted O2)  

5. **v2 task / phase model** (derived from world-specific v1)  
   - Tasks are reusable, parameterized, event-completed (e.g. `deploy_lspu`, `site_prep_foundation`)  
   - Phases compose `task_ref`s; tagged with `applicable_body_types` and `world_agnostic: true`  
   - Example: `power_comms` phase applies to `airless_rocky`, `thin_atmosphere`, `atmospheric`  
   - v1 often carried world-specific detail; v2 deliberately generalized  

**Design line:** evaluate location + resources + units + components + technology + manufacturing + environment + economics at runtime; select and sequence the appropriate implementation. Do not maintain parallel Luna/Mars/Venus code paths.

### What this task adds

The gap is **initial foothold / system-entry planning**: given a body (and system topology) with little or no existing settlement, produce ranked options for how to establish a workable presence — using the same evaluation principles above, composed from v2-style task/phase building blocks where possible.

This includes cases the current pattern-name entry point handles poorly (e.g. Super-Mars with no moons: prefer moving small asteroids into orbit and converting them rather than forcing a named "mars-standard" pattern).

**Scope note:** `MissionPlannerService`, `PrecursorCapabilityService`, and `ISRUEvaluator` are currently driven by rake tasks / isolated scripts and are not wired into the live game-tick loop. Building better planner architecture here is still worthwhile; live-loop integration is a later concern, not part of this task.

**Relevant code / sensors to reuse (not rewrite):**
- `app/services/ai_manager/precursor_capability_service.rb`
- `app/services/ai_manager/isru_evaluator.rb`
- `app/services/ai_manager/mission_planner_service.rb` (current pattern-first entry — dual path later)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: This is an extension of existing design — not a new philosophy**
- ❌ Wrong: Present "resource-first foothold planning" as a novel idea or alternate design track
- ✅ Right: Cite the prior-art docs above; frame this as applying the same principles to initial foothold / system entry
- Why: One consistent design vision across escalation, construction economics, cyclers, v2 tasks, and foothold planning

⚠️ **GOTCHA 2: Do not start from a pattern name**
- ❌ Wrong: `FootholdPlanner.new(pattern_name: "mars-standard")`
- ✅ Right: Input is a celestial body + system context snapshot (resources, moons/asteroids, accessibility, logistics distance)
- Why: Inverts the current pattern-first entry while aligning with state/resource evaluation elsewhere

⚠️ **GOTCHA 3: Reuse existing capability sensors and v2 task vocabulary**
- ❌ Wrong: Reimplement atmosphere/regolith logic or invent a parallel task language
- ✅ Right: Call `PrecursorCapabilityService` (and later `ISRUEvaluator`); prefer composing existing v2 task/phase building blocks
- Why: Sensors and v2 tasks are already the data-driven layer; the planner should select and order them

⚠️ **GOTCHA 4: Keep scope architectural + minimal interface**
- ❌ Wrong: Full deployment sequencing, costing, contract generation, or live-loop wiring in this task
- ✅ Right: Input/output contract, ranking criteria, thin service skeleton, explicit non-goals
- Why: Luna settlement loop and core logistics remain the primary implementation focus; this is design foundation

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Resource-First Foothold Planner
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Define architecture and minimal interface for a planner that takes a body + system snapshot and returns ranked foothold options, extending existing resource-first / world-agnostic design (escalation, construction economics, cycler, v2 tasks) — not inventing a new philosophy.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md` | Prior art: state-based, ISRU-first | pending |
| `docs/architecture/services/ai_manager/AI_MANAGER_CONSTRUCTION_ECONOMICS.md` | Prior art: local cost vs import, player-first | pending |
| `docs/architecture/services/ai_manager/CYCLER_SYSTEM_ARCHITECTURE.md` | Prior art: generic platform + data fits | pending |
| `docs/architecture/simulation/construction_system.md` | Prior art: generic ISRU construction | pending |
| `app/services/ai_manager/precursor_capability_service.rb` | Capability sensor to reuse | pending |
| `app/services/ai_manager/mission_planner_service.rb` | Current pattern-first entry | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ Step 0: YAML status updated to active
- ✅ Read prior-art docs listed above
- ✅ Understand the four Architecture Gotchas

### Expected Outcomes
- Clear input contract (body + system context; no pattern_name required)
- Clear output contract (ranked foothold options)
- Ranking criteria aligned with existing local-first / cost principles
- Thin service skeleton
- Explicit non-goals; prior art cited in design note or comments

### Critical Gotchas I Will Avoid
- ❌ Framing as new philosophy — instead ✅ extension of prior art
- ❌ Starting from pattern_name — instead ✅ resource/system snapshot
- ❌ Reimplementing sensors or parallel task language — instead ✅ reuse
- ❌ Full planner implementation — instead ✅ architecture + minimal interface

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

Planning still enters primarily through named patterns (`MissionPlannerService` + pattern_name). There is no first-class path that says:

> Given this body and system topology, what are viable foothold strategies ranked by local-resource leverage and import dependency — composed from the same evaluation rules and v2 task vocabulary already used elsewhere?

That blocks novel configurations (e.g. no-moon Super-Mars) and keeps initial settlement planning out of step with escalation, construction economics, and world-agnostic v2 tasks.

**Current behavior**: Planner driven by pattern name.  
**Expected behavior**: Planner driven by body + system resource/topology analysis; patterns and successful Luna (etc.) runs become reference examples, not the only legal entry.

---

## Files Involved

### Primary Files — create or lightly edit
| File | Purpose |
|---|---|
| `app/services/ai_manager/foothold_planner.rb` (new) or equivalent | Minimal interface / skeleton |
| Short design note under `docs/architecture/ai_manager/` (optional) | Capture contract + prior-art linkage |

### Reference Files — read; do not rewrite
| File | Why |
|---|---|
| Prior-art docs listed in Context | Philosophical and economic grounding |
| `app/services/ai_manager/precursor_capability_service.rb` | Local capability sensor |
| `app/services/ai_manager/mission_planner_service.rb` | Current entry point to complement later |
| `app/services/ai_manager/isru_evaluator.rb` | Later operational sensor |
| v2 task/phase JSON examples (site prep, LSPU, power_comms) | Building blocks to compose |

---

## Implementation Steps

1. **Read prior art** (escalation, construction economics, cycler, construction_system, sample v2 tasks/phases). Confirm the consistent design line before writing anything new.
2. Define the **input contract**: celestial body + system topology snapshot (resources, moons present/absent, nearby asteroids, accessibility, distance/logistics context). No `pattern_name` required.
3. Define the **output contract**: ranked list of foothold options. Each option should include preferred location type (surface / feature / orbital depot / captured asteroid / atmospheric), high-level sequence outline (preferably referencing v2-style task/phase ideas), expected local vs import bias, and brief rationale.
4. Define **ranking criteria** aligned with existing principles: local-resource leverage first, local extraction cost vs import, import dependency, time-to-positive-flow, risk; player opportunity where relevant later.
5. Create a **thin service skeleton** that accepts the input contract, calls `PrecursorCapabilityService`, and returns placeholder or minimal ranked options.
6. Document **explicit non-goals**: no full costing, no contract generation, no pattern deletion, no live game-tick wiring, no Super-Mars full implementation.
7. Optionally add a short architecture note that cites the prior-art docs and states this is one design vision, not a parallel track.

---

## Acceptance Criteria

- [ ] Context / design note explicitly references the prior-art docs (escalation, construction economics, cycler, construction_system, v2 world-agnostic tasks) as grounding — not as optional reading
- [ ] Input contract does **not** require a pattern name
- [ ] Output is a ranked list of foothold options
- [ ] Ranking criteria align with local-first / local-cost-vs-import principles already documented
- [ ] `PrecursorCapabilityService` is used (not reimplemented)
- [ ] Design considers composition of v2-style task/phase building blocks
- [ ] Super-Mars / no-moon style reasoning is explicitly allowed for in the design
- [ ] Scope remains architectural + minimal interface
- [ ] Non-goals are written down

---

## Dependencies

**Blocked by**: None (architecture can proceed independently of Luna loop completion)  
**Blocks**: MissionPlanner entry-point dual path, Super-Mars test case usage, learning from worked examples  
**Related**:  
- `2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md`  
- `2026-09-01-MEDIUM-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md`  
- `2026-09-01-MEDIUM-ARCHITECTURE-SUPER-MARS-NO-MOON-TEST-CASE.md`  

---

## Stop Conditions

- Requires changes to shared economic or unit models beyond the AI Manager boundary
- Turns into a large implementation effort instead of architecture + thin interface
- Starts rewriting escalation, construction economics, or v2 task formats instead of extending them
