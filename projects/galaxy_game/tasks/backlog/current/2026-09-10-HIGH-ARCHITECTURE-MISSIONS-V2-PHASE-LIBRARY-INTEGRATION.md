---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-09-10
estimated_effort: 2-3 hours
depends_on: []
blocks:
  - lunar_settlement_simulation
  - ai_manager_dynamic_profile_generation
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template is provided
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/current/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md
projects/galaxy_game/tasks/active/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed

Tracked file: git mv (never cp or plain mv)

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
Chat is for questions only — never paste synthesis into chat (formatting breaks).


---

# TASK: Missions v2 Phase Library Integration & Validation
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-10

---

## Context

In session 2026-09-10, we identified that the missions_v2 architecture is **correctly designed** and **partially implemented**:
- **missions/tasks_v2/** contains the complete generic parametrized task library (~100+ tasks) — UNTESTED
- **missions_v2/phases/** correctly references tasks_v2 and accepts environment parameters — PARTIALLY IMPLEMENTED (14 files)
- **missions_v2/profiles/** set parameter values for specific locations (Luna, Mars, Venus, etc.) — IMPLEMENTED

The precursor mission profile was updated (v1.0) with 8 phases, concurrent operation windows documented, and parametric Venus transit model (fuel-dependent arrival window Days 526-656).

**14 JSON data files were created during architecture discovery** but need validation:
- Phase definition files in missions_v2/phases/
- Updated profile files
- Rake timing validation task updated

**This task is to validate and document the missions_v2/tasks library and ensure phase references work end-to-end.**

---

## Critical Information

### Architecture Pattern (Confirmed)

missions/tasks_v2/
├── task_deploy_car_robots_v2.json (generic, parametrized)
└── ... (~100+ tasks)

missions_v2/phases/
├── initial_hlt_landings_v2.json (references "task_ref": "tasks_v2/task_deploy_car_robots_v2.json")
└── ... (14 phase files)

missions_v2/profiles/
└── precursor_mission_profile_v1.json (sets target_body: "LUNA-01", passes to phases)


**One library. One profile template. Location is a parameter.**

**Note on task_ref format**: `task_ref` values are stored as `"tasks_v2/task_X.json"` — this resolves against `data/json-data/missions/tasks_v2/`, NOT `data/json-data/tasks_v2/` and NOT `data/json-data/missions_v2/tasks/`. The path-check script in Step 2 already accounts for this — do not "simplify" it.

### Gotchas

⚠️ **Gotcha 1**: missions/tasks_v2 is untested — all tasks from old pipeline, never run through v2 validation
- ❌ Wrong: "These tasks work because they exist in the codebase"
- ✅ Right: Run each task through the game loop to verify parametrization works
- Why: Parameters like `$inflatable_tank_capacity_kg` may not resolve correctly in current context

⚠️ **Gotcha 2**: 14 JSON files created but not yet validated as a set
- ❌ Wrong: "JSON syntax is valid so they're production-ready"
- ✅ Right: Run full mission simulation (rake task) to verify phases chain correctly
- Why: Cross-file references (task_refs pointing to missions/tasks_v2) must be resolvable

⚠️ **Gotcha 3**: The rake file was edited multiple times during the 2026-09-10 design session (Titan removal, timing fixes)
- ❌ Wrong: Assume the current file state is fully committed and treat it as ground truth without checking
- ✅ Right: Confirm via `git status`/`git diff` that all changes are committed before treating the file as the validated source of truth
- Why: If uncommitted changes exist, they could be lost or overwritten. Do NOT discard or overwrite anything found uncommitted — flag it and stop for a decision.

---

## Problem Statement

**Current state:**
- missions_v2/phases/ has 14 files that reference tasks_v2 library ✓
- Phase files are syntactically valid JSON ✓
- Rake timing validation added but only partially tested ✓

**Missing:**
- End-to-end validation: Do all 14 phases load → resolve task refs → execute in correct order?
- Parameter passing: Does target_body from profile flow through to task execution?
- Task library audit: Which missions/tasks_v2 tasks are used? Are any orphaned?
- Documentation: What is the status of each phase file and task?

---

## Files Involved

### Primary Files — you will validate/document these

| File | Purpose | Action |
|---|---|---|
| `data/json-data/missions/tasks_v2/*.json` | Generic task library (~100+ tasks) | Audit which are referenced by phases, validate parametrization |
| `data/json-data/missions_v2/phases/*.json` | 14 phase definition files created today | Validate task_refs resolve, timeline is correct |
| `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` | Precursor profile with 8 phases | Verify all phases wired correctly |
| `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` | Rake validation (updated today) | Confirm committed (Gotcha 3), then run full timing validation |
| `docs/architecture/ai_manager/MISSIONS_V2_ARCHITECTURE.md` | NEW — document the pattern | Create architectural reference doc |

### Reference Files — read but do not edit

| File | Why |
|---|---|
| `missions_v2/manifests/lunar_precursor_manifest_v2.json` | Inventory structure |
| `missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` | DAG with concurrent windows |

---

## Implementation Steps

### Step 0 — Move task file to active/
(See Agent Dispatch Interface above)

### Step 1: Confirm rake file is committed, then run full timing validation

**First**, confirm the rake file has no uncommitted changes left over from the 2026-09-10 design session (see Gotcha 3):

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git status --short galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake
git diff galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake | head -50
```

If uncommitted changes exist: **stop and report them** — do not commit them yourself without confirming they're intentional, and do not discard them.

**Then**, run the timing validation:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission:phase_timing 2>&1'
```

**Capture output.** If it fails, debug the exact phase that breaks.

### Step 2: Audit missions/tasks_v2 library

Create a task reference audit:
```bash
cd /Users/tam0013/Documents/git/galaxyGame
find data/json-data/missions_v2/phases -name "*.json" -exec grep -h "task_ref" {} \; | sort -u | wc -l
```

Count: How many unique task_refs are used across all phases?

Then verify they all exist:
```bash
for task in $(find data/json-data/missions_v2/phases -name "*.json" -exec grep -h '"task_ref"' {} \; | sed 's/.*"task_ref": "\([^"]*\)".*/\1/' | sort -u); do
  if [ ! -f "data/json-data/missions/$task" ]; then
    echo "MISSING: $task"
  fi
done
```

**Capture results.** If any are missing, flag as blocker.

**Secondary check** — flag non-`_v2` task variants referenced alongside `_v2` versions of the same task (possible stale reference drift):
```bash
find data/json-data/missions_v2/phases -name "*.json" -exec grep -h '"task_ref"' {} \; | sed 's/.*"task_ref": "\([^"]*\)".*/\1/' | sort -u | grep -v "_v2\.json$" | while read task; do
  base=$(basename "$task" .json)
  if find data/json-data/missions_v2/phases -name "*.json" -exec grep -l "\"${base}_v2.json\"" {} \; | grep -q .; then
    echo "DRIFT: both $task and ${base}_v2.json are referenced — confirm this is intentional, not a stale reference"
  fi
done
```

If drift is found, report which phase files reference which variant — do not "fix" it by picking one yourself, this needs a decision.

### Step 3: Validate phase file structure

Each phase file should have:
- `phase_id` — matches mission plan reference
- `description`
- `task_list` OR `tasks` array
- `estimated_duration_hours` (for scheduling)
- `applicable_body_types` (Luna, Mars, Venus, etc.)

Create a summary table (paste into synthesis report, do NOT commit):

| Phase File | phase_id | Task Count | Duration (hrs) | Body Types |
|---|---|---|---|---|
| initial_hlt_landings_v2.json | initial_hlt_landings | 4 | 432 | airless_rocky, thin_atmosphere |
| ... | ... | ... | ... | ... |

### Step 4: Create architectural reference doc

**File:** `docs/architecture/ai_manager/MISSIONS_V2_ARCHITECTURE.md`

(Confirm the `docs/architecture/ai_manager/` directory exists first — create it if not.)

Content:
- Overview: one library, one profile template, location as parameter
- Phase structure: what each phase file contains
- Task library audit results (from Step 2, including drift check results)
- Parameter passing flow (profile → phases → tasks)
- Validation checklist (what you verified in this task)
- Future: how AI Manager will generate profiles dynamically

Save to git.

### Step 5: Synthesis Report

Save to summaries folder covering:
- Rake file commit status (clean / had uncommitted changes — describe)
- Timing validation results (did rake pass?)
- Task reference audit (how many unique tasks? any missing? any non-_v2/_v2 drift found?)
- Phase structure validation (all files conform?)
- Architectural doc created? (yes/no)
- Ready for AI Manager profile generation? (yes, with caveats / no, blockers are:)

---

## Acceptance Criteria

- [ ] Rake file confirmed fully committed before validation (Gotcha 3)
- [ ] Rake `luna_mission:phase_timing` runs without error
- [ ] All 14 phase files have syntactically correct JSON
- [ ] All task_refs in phases resolve to files in `missions/tasks_v2/`
- [ ] No unexplained non-_v2/_v2 task_ref duplicates (or, if found, confirmed intentional and documented)
- [ ] Phase structure audit complete (table created)
- [ ] Architectural reference doc created (MISSIONS_V2_ARCHITECTURE.md)
- [ ] Synthesis report created with full audit results
- [ ] No regressions in existing mission specs

---

## Stop Conditions — escalate immediately if:

- Rake file has uncommitted changes from the prior session (Gotcha 3) — do not proceed until resolved
- Rake task fails at a specific phase (need to debug which phase broke)
- Task refs point to files that don't exist (library gap identified)
- Non-_v2/_v2 duplicate task_ref drift is found (needs a decision, not a unilateral fix)
- Phase files have inconsistent structure (need to standardize)
- Profile doesn't wire all 8 phases correctly (wiring issue)
- Any architectural question about how AI Manager will use this data

---

## Commit Instructions

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add docs/architecture/ai_manager/MISSIONS_V2_ARCHITECTURE.md
git commit -m "docs: add missions_v2 architecture reference — task library, phase structure, parameter passing"

cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md
git commit -m "chore: move missions_v2 integration validation to completed"
```

---

## Documentation

- [ ] Create MISSIONS_V2_ARCHITECTURE.md (above)

---

## Dependencies

**Blocked by**: none
**Blocks**: 
- AI Manager dynamic profile generation (needs validated library)
- Luna settlement simulation (depends on correct phase wiring)
**Related**: Launch Window Transit Timing Engine (2026-08-18)

---

## Completion Report
*Filled in by implementing agent*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was validated
- Rake file commit status: [clean / had uncommitted changes — describe what was found]
- Rake timing validation: [pass/fail — attach output]
- Task reference audit: [N unique task_refs, all resolvable/X missing]
- Non-_v2/_v2 drift check: [none found / N instances found — list]
- Phase structure: [all conform/description of deviations]
- Architectural doc: [created/location]

### Issues discovered
[Any gaps or inconsistencies found]

### Ready for AI Manager?
[Yes — architecture is sound for dynamic generation / No — these blockers must be resolved first]

---

## Handoff Summary

VALIDATION COMPLETE: 14 phase files + task library audit + architectural doc | Ready for AI Manager profile generation | Next: Create AI Manager service for dynamic profile/manifest generation
