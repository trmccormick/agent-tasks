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

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md \
         projects/galaxy_game/tasks/active/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Missions v2 Phase Library Integration & Validation
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-10
**Last Updated**: 2026-09-11

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: File system/terminal access needed for audit commands and git operations
**Local attempts before cloud**: N/A — first dispatch
**Supervision Level**: watched carefully

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/agent_workflow_rules.md` (EXECUTOR Role section)
2. **Task Template**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

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

## Problem Statement

**Current state:**
- `missions_v2/phases/` has 14 files that reference tasks_v2 library ✓
- Phase files are syntactically valid JSON ✓
- Rake timing validation added but only partially tested ✓

**Missing:**
- End-to-end validation: Do all 14 phases load → resolve task refs → execute in correct order?
- Parameter passing: Does target_body from profile flow through to task execution?
- Task library audit: Which missions/tasks_v2 tasks are used? Are any orphaned?
- Documentation: What is the status of each phase file and task?

---

## Critical Information for This Task

### Architecture Pattern (Confirmed)

```
missions/tasks_v2/
├── task_deploy_car_robots_v2.json (generic, parametrized)
└── ... (~100+ tasks)

missions_v2/phases/
├── initial_hlt_landings_v2.json (references "task_ref": "tasks_v2/task_deploy_car_robots_v2.json")
└── ... (14 phase files)

missions_v2/profiles/
└── precursor_mission_profile_v1.json (sets target_body: "LUNA-01", passes to phases)
```

**One library. One profile template. Location is a parameter.**

**Note on task_ref format**: `task_ref` values are stored as `"tasks_v2/task_X.json"` — this resolves against `data/json-data/missions/tasks_v2/`, NOT `data/json-data/tasks_v2/` and NOT `data/json-data/missions_v2/tasks/`. The path-check script in Step 2 already accounts for this — do not "simplify" it.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: missions/tasks_v2 is untested — all tasks from old pipeline, never run through v2 validation
- ❌ Wrong: "These tasks work because they exist in the codebase"
- ✅ Right: Run each task through the game loop to verify parametrization works
- Why: Parameters like `$inflatable_tank_capacity_kg` may not resolve correctly in current context

⚠️ **GOTCHA 2**: 14 JSON files created but not yet validated as a set
- ❌ Wrong: "JSON syntax is valid so they're production-ready"
- ✅ Right: Run full mission simulation (rake task) to verify phases chain correctly
- Why: Cross-file references (task_refs pointing to missions/tasks_v2) must be resolvable

⚠️ **GOTCHA 3**: The rake file was edited multiple times during the 2026-09-10 design session (Titan removal, timing fixes)
- ❌ Wrong: Assume the current file state is fully committed and treat it as ground truth without checking
- ✅ Right: Confirm via `git status`/`git diff` that all changes are committed before treating the file as the validated source of truth
- Why: If uncommitted changes exist, they could be lost or overwritten. Do NOT discard or overwrite anything found uncommitted — flag it and stop for a decision.

⚠️ **GOTCHA 4**: Phase files contain non-_v2 task_refs alongside _v2 versions of the same task (drift)
- ❌ Wrong: "Fix drift by picking one variant"
- ✅ Right: Report which phase files reference which variant — this needs a human decision, not an automated fix
- Why: The mixed references may be intentional (e.g., some phases use legacy tasks, others use v2). Document before deciding.

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
| `data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json` | Inventory structure |
| `data/json-data/missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` | DAG with concurrent windows |

### Migration (if needed)
- [x] No migration needed — this is a validation/documentation task only

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Missions v2 Phase Library Integration & Validation
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
Validate the missions_v2 phase library end-to-end: confirm rake file commit status, run timing validation, audit task references across 14 phase files, verify parameter passing from profiles through phases to tasks, and create an architectural reference document.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` | Timing validation task | not started / pending / done |
| `data/json-data/missions_v2/phases/*.json` (14 files) | Phase definitions | not started / pending / done |
| `data/json-data/missions/tasks_v2/*.json` | Task library (~100+ tasks) | not started / pending / done |
| `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` | Profile wiring | not started / pending / done |
| `docs/architecture/ai_manager/MISSIONS_V2_ARCHITECTURE.md` | NEW — architectural doc | not started / pending / done |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read task template
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
1. Rake timing validation runs without error (or exact failure identified)
2. All 14 phase files have syntactically correct JSON and conform to expected structure
3. All task_refs in phases resolve to existing files in missions/tasks_v2/
4. Non-_v2/_v2 drift documented (which phases reference which variant)
5. Architectural reference doc created with audit results
6. Synthesis report with go/no-go for AI Manager profile generation

### Critical Gotchas I Will Avoid
- ❌ Assuming rake file is committed — will check git status first (Gotcha 3)
- ❌ Fixing non-_v2/_v2 drift automatically — will document and escalate (Gotcha 4)
- ❌ Treating valid JSON as "production-ready" — will run full simulation (Gotcha 2)
- ❌ Assuming untested tasks work — will flag for game loop validation (Gotcha 1)

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/current/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md \
       projects/galaxy_game/tasks/active/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Confirm rake file commit status, then run timing validation

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

### Step 2 — Audit missions/tasks_v2 library

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

### Step 3 — Validate phase file structure

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

### Step 4 — Create architectural reference doc

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

### Step 5 — Synthesis Report

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

## Stop Conditions — escalate to user immediately if:
- Rake file has uncommitted changes from the prior session (Gotcha 3) — do not proceed until resolved
- Rake task fails at a specific phase (need to debug which phase broke)
- Task refs point to files that don't exist (library gap identified)
- Non-_v2/_v2 duplicate task_ref drift is found (needs a decision, not a unilateral fix)
- Phase files have inconsistent structure (need to standardize)
- Profile doesn't wire all 8 phases correctly (wiring issue)
- Any architectural question about how AI Manager will use this data

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]
git add projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [x] Create `docs/architecture/ai_manager/MISSIONS_V2_ARCHITECTURE.md` — architectural reference doc (primary deliverable)
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: 
- AI Manager dynamic profile generation (needs validated library)
- Luna settlement simulation (depends on correct phase wiring)
**Related tasks**: Launch Window Transit Timing Engine (2026-08-18)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
