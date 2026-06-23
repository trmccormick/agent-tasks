---
status: active
priority: HIGH
type: validation
system_domain: AI_MANAGER | TASK_EXECUTION | LUNA_MVP
mvp_alignment: LUNA_PRECURSOR_MISSION_1
local_worker_safe: true
created: 2026-06-23
updated: 2026-06-23
assigned_to: m4
---

# TASK: Luna Precursor Rake Validation — LSPU + Regolith Recycling Integration

**Status**: ACTIVE (moved from backlog 2026-06-23)
**Priority**: HIGH
**Type**: validation (execution + test)
**Assigned to**: m4

---

## Context

LSPU (Surface Prep Unit) blueprint and closed-loop regolith recycling workflow are now complete in galaxyGame:
- ✅ `surface_prep_unit_lspu_bp.json` — microwave-sintering unit (1200 kg, 45 kW @ 900°C)
- ✅ `regolith_harvester_rover_bp.json` — deployment task added
- ✅ Phase 1 reordered to 6 tasks (was 4) with regolith harvesting + LSPU sequencing
- ✅ Manifest updated with both new units + task_affinity mappings

**Expected outcome**: `bundle exec rake luna_mission:execute` returns **14/14 tasks passing** (was 8/9 before LSPU addition).

---

## Scope

**Deliverable**: Confirm all files compile + rake returns 14/14 passing + commit changes

**Target Files**:
1. `data/json-data/blueprints/units/construction/surface_prep_unit_lspu_bp.json`
2. `data/json-data/blueprints/units/excavation/regolith_harvester_rover_bp.json`
3. `data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json`
4. `data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json`
5. `data/json-data/missions/tasks_v2/task_deploy_regolith_harvester_rover.json`
6. `data/json-data/missions/tasks_v2/task_deploy_lspu.json`

---

## Success Criteria

- ✅ All 6 JSON files pass `python3 -m json.tool` validation (no syntax errors)
- ✅ Rake execution: `docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute` → **14/14 tasks pass**
- ✅ All 6 files committed with clear commit message
- ✅ Changes pushed to `origin/main`
- ✅ Synthesis report delivered (see below)

---

## Steps

### Step 1: Validate JSON Syntax (5 min)
```bash
cd /Users/tam0013/Documents/git/galaxyGame
python3 -m json.tool data/json-data/blueprints/units/construction/surface_prep_unit_lspu_bp.json > /dev/null
python3 -m json.tool data/json-data/blueprints/units/excavation/regolith_harvester_rover_bp.json > /dev/null
python3 -m json.tool data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json > /dev/null
python3 -m json.tool data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json > /dev/null
python3 -m json.tool data/json-data/missions/tasks_v2/task_deploy_regolith_harvester_rover.json > /dev/null
python3 -m json.tool data/json-data/missions/tasks_v2/task_deploy_lspu.json > /dev/null
```
Expected: No errors, silent completion.

### Step 2: Run Luna Mission Rake (10-15 min)
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute 2>&1
```
Expected: "14/14 tasks passing" (or similar success output). If fewer than 14 pass, capture the full error output and report which task(s) failed.

### Step 3: Stage & Commit (2 min)
```bash
git add \
  data/json-data/blueprints/units/construction/surface_prep_unit_lspu_bp.json \
  data/json-data/blueprints/units/excavation/regolith_harvester_rover_bp.json \
  data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json \
  data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json \
  data/json-data/missions/tasks_v2/task_deploy_regolith_harvester_rover.json \
  data/json-data/missions/tasks_v2/task_deploy_lspu.json

git commit -m "design: Implement closed-loop regolith recycling for Luna precursor Phase 1

✅ LSPU (Surface Prep Unit) microwave-sintering blueprint (1200 kg, 45 kW @ 900°C)
✅ Regolith Harvester Rover deployment task (foundation prep workflow)
✅ Phase 1 reordered: 6 tasks (excavation + LSPU sequencing added)
✅ Manifest updated: Both units added with task_affinity mappings
✅ Rake validation: 14/14 tasks passing (closed-loop regolith workflow complete)

Workflow: Excavate raw regolith → TEU/PVE extract volatiles → LSPU sinter depleted material → compacted foundation ready for infrastructure deployment

Fixes: Foundation_sintered gate now has material flow path; landing pad / shell printing sequencing unblocked by regolith workflow"

git push origin main
```

---

## Return Format

**Completion report** (paste into this file's "Completion Report" section):
1. **JSON validation**: All passed / Failed (which file?)
2. **Rake output**: X/Y tasks passing (capture last 10 lines of rake output)
3. **Commit hash**: [hash]
4. **Status**: ✅ Complete / ⚠️ Incomplete with blockers

If rake fails, include:
- Which task(s) failed
- Full error message (from rake stderr)
- Hypothesis about cause

---

## Completion Report
*Filled in by m4 after execution*

**Completed by**:
**Completion date**:

### Validation Results
- JSON syntax check: [PASS/FAIL]
- Rake execution: [X/14 tasks passing]
- Commit hash: [git log -1 --oneline]
- Push status: [success/error]

### Issues Encountered
[Any blockers, errors, or unexpected results]

### Next Steps
[If rake passes: closed-loop regolith recycling gate opened, unblock shell printing / landing pad sequencing. If rake fails: report blockers for strategist review.]
