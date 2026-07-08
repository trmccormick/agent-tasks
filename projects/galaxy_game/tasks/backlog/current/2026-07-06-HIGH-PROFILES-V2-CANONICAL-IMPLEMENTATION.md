---
status: backlog
priority: HIGH
type: implementation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-06
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md

LIFECYCLE: backlog → active → completed (use git mv, never copy)
READ FIRST: Task file contains all prerequisites and implementation details.
MOVE TO ACTIVE: Before starting work, move task file to active/ and commit the move.
```

---

# PROFILES_V2 CANONICAL IMPLEMENTATION — RAKE PATH RESOLUTION ONLY

**Status**: BACKLOG
**Priority**: HIGH
**Type**: implementation
**Created**: 2026-07-06
**Approved by**: Claude (design review complete)
**Canonical examples location**: `data/json-data/missions_v2/`

---

## Context

Claude reviewed the canonical V2 examples and approved them as a reference implementation. All production files already exist in `missions_v2/`. This task covers only rake path resolution and verification — no engine changes, no LegacyPortAdapter bridge, no DAG execution engine work.

### What Exists (Already Complete)

| File | Path | Status |
|------|------|--------|
| Profile schema reference | `missions_v2/README.md` | ✅ Created |
| Luna profile | `missions_v2/profiles/luna_base_profile_v2.json` | ✅ Created |
| Phase 1: power_comms | `missions_v2/phases/power_comms_v2.json` | ✅ Created |
| Phase 2: isru_deployment | `missions_v2/phases/isru_deployment_v2.json` | ✅ Created |
| Phase 3: gas_processing | `missions_v2/phases/gas_processing_v2.json` | ✅ Created |
| Phase 4: robot_logistics | `missions_v2/phases/robot_logistics_v2.json` | ✅ Created |
| Mission plan (DAG) | `missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` | ✅ Created |
| Task index | `missions_v2/task_index/all_tasks.json` | ✅ Created |
| 17 V2.1 tasks | `missions_v2/tasks/*_v2.json` | ✅ Copied + converted |

### Claude's Scope Constraints (MANDATORY)

**IN SCOPE for this task:**
1. Update `luna_mission.rake` with `missions_v2/` path resolution and legacy fallback
2. Verify 17 tasks still execute correctly (no regressions)
3. No rake refactor — only add path resolution logic

**NOT IN SCOPE — separate tasks later:**
- Engine changes to `TaskExecutionEngineV2`
- Parameter interpolation (`$variable` resolution)
- LegacyPortAdapter bridge integration
- Venus foundry migration
- DAG execution engine changes

---

## Implementation Steps

### Step 1: Verify Canonical Examples Are Complete

Confirm all files in `missions_v2/` are present and valid JSON:
- ✅ 1 profile in `missions_v2/profiles/`
- ✅ 4 phases in `missions_v2/phases/` with `_v2.json` suffix
- ✅ 1 mission plan in `missions_v2/mission_plans/`
- ✅ 17 tasks in `missions_v2/tasks/` with `_v2.json` suffix

Check that the 17 V2.1 tasks have `"template": "task_v2.1"` and `"version": "2.1"`.

### Step 2: Update luna_mission.rake with missions_v2/ Path Resolution

Update `galaxy_game/lib/tasks/luna_mission.rake` to check `missions_v2/` paths first, then fall back to legacy paths. Example logic:

```ruby
# Profile path resolution
v2_profile = GalaxyGame::Paths::MISSIONS_PATH.join("missions_v2/profiles/#{profile_id}.json")
legacy_profile = GalaxyGame::Paths::MISSIONS_PATH.join("luna_base_establishment/#{profile_id}.json")

profile_path = File.exist?(v2_profile) ? v2_profile : legacy_profile

# Phase path resolution
v2_phase = GalaxyGame::Paths::MISSIONS_PATH.join("missions_v2/phases/#{phase_id}.json")
legacy_phase = GalaxyGame::Paths::MISSIONS_PATH.join("luna_base_establishment/#{phase_id}.json")

phase_path = File.exist?(v2_phase) ? v2_phase : legacy_phase
```

### Step 3: Verify No Regressions

Run the rake task and confirm all 17 tasks still execute correctly:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna:precursor_mission_v2[luna_base_profile_v2] 2>&1 | tail -50
```

Expected result: All 17 tasks execute without errors. No engine changes needed.

---

## Stop Conditions

- If any canonical file is missing or malformed — document which file and stop
- If rake modifications require changes beyond path resolution — escalate before proceeding
- If task execution fails after path resolution updates — debug and document the root cause

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final task result**: X tasks executed, 0 failures
**Files modified**: [`galaxy_game/lib/tasks/luna_mission.rake`]
**Output location**: `galaxy_game/lib/tasks/luna_mission.rake` (updated paths only)
