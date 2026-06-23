---
status: active
priority: HIGH
type: bugfix
system_domain: AI_MANAGER | TASK_EXECUTION | LUNA_MVP
mvp_alignment: LUNA_PRECURSOR_MISSION_1
local_worker_safe: true
created: 2026-06-23
updated: 2026-06-23
assigned_to: available
---

# TASK: Inflatable Cryo Tank Port Configuration Bugfix

**Status**: ACTIVE
**Priority**: HIGH
**Type**: bugfix (blocking rake validation)
**Impact**: Blocks deploy_gas_separator task from executing

---

## Problem

Rake validation returned:
```
Failed: deploy_gas_separator — InfrastructureSequenceError: Cannot connect: 
unit 'Inflatable Cryogenic Tank 1' has no available internal_unit_ports ports (0 of 0 free)
```

The `task_deploy_gas_separator.json` task attempts to connect gas separator outputs to the inflatable cryo tank. The tank blueprint has **0 ports defined**, so the connection fails.

---

## Root Cause

File: `data/json-data/blueprints/units/storage/inflatable_cryo_tank.json`

The blueprint either:
1. Is missing the `"ports"` section entirely, OR
2. Has `"internal_unit_ports": []` (empty array)

The gas separator expects to connect gas output to the tank's `internal_unit_ports`.

---

## Success Criteria

- ✅ `inflatable_cryo_tank.json` has `"ports"` section with at least one `internal_unit_port`
- ✅ Port definition matches the schema used in other storage units (check `thermal_extraction_unit.json` or `pve_unit.json` for reference)
- ✅ All JSON files pass `python3 -m json.tool` syntax validation
- ✅ Rake execution: `docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute` returns **11/11 tasks passing** (deploy_gas_separator should pass)
- ✅ Changes committed with clear message
- ✅ Pushed to `origin/main`

---

## Scope

**What to fix**:
- `data/json-data/blueprints/units/storage/inflatable_cryo_tank.json` — add `internal_unit_ports` definition

**Reference files** (check port patterns):
- `data/json-data/blueprints/units/processing/thermal_extraction_unit_bp.json`
- `data/json-data/blueprints/units/processing/pve_unit_bp.json`

**Do not change**:
- `task_deploy_gas_separator.json` (task definition is correct)
- Phase files (unrelated)

---

## What the Port Should Do

The gas separator needs to **output gas** into the cryo tank for storage. The tank should have at least one `internal_unit_port` that can accept this connection.

Port type: `internal_unit_port` (units connect to other units via this port type)
Port capacity: At least 1 (to allow the gas separator connection)

---

## Verification Steps

1. Examine `inflatable_cryo_tank.json` and identify the current port configuration
2. Compare with a working unit (e.g., TEU or PVE) to see the correct pattern
3. Add `internal_unit_ports` section with at least 1 port
4. Validate JSON syntax
5. Run rake and confirm deploy_gas_separator passes
6. Commit and push

---

## Completion Report

**Completed by**: [agent name]
**Completion date**: [date]

### Port Configuration Added
- File: inflatable_cryo_tank.json
- Ports added: [describe what was added]
- Reference pattern used: [which unit was used as reference]

### Validation Results
- JSON syntax: [PASS/FAIL]
- Rake result: [X/11 tasks passing]
- Commit hash: [git log -1 --oneline]

### Issues Encountered
[If any blockers or unexpected results]
