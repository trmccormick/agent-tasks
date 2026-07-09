---
status: active
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-08
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-08-HIGH-MISSIONS-V2-MANIFEST-AND-CAR300-FIX.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current
  mv 2026-07-08-HIGH-MISSIONS-V2-MANIFEST-AND-CAR300-FIX.md \
     /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-07-08-HIGH-MISSIONS-V2-MANIFEST-AND-CAR300-FIX.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (mv only, never cp)
Verify with: find /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks \
  -name "2026-07-08-HIGH-MISSIONS-V2-MANIFEST-AND-CAR300-FIX.md"
Only ONE result should exist. Paste this output before starting synthesis.

READ FIRST: Task file contains all prerequisites, gotchas, and verification steps.
CRITICAL: Save synthesis report as MD file to summaries/ BEFORE starting any work.
NO GIT COMMITS without explicit human approval in chat. Show diffs and wait for "yes, commit".
```

---

# MISSIONS_V2 MANIFEST LOCATION + CAR-300 UNIT NAME CORRECTIONS

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-08

---

## Context

Three related data correctness issues blocking Luna rake progress after the Mk1 fix.
Root cause: `missions_v2/manifests/` is empty (manifest was never moved from the DRAFT
location), and the CAR-300 task files were either not converted to v2.1 or have wrong
unit names that don't normalize to match manifest hardware IDs.

Current rake progress: 9/13 tasks pass after Mk1 fix. These issues block the remaining
robot_logistics phase tasks.

---

## Issues to Fix (3 total)

### Issue 1 — Manifest at Wrong Location (PRIMARY BLOCKER)

The rake loads the manifest from the old DRAFT path:
```ruby
# luna_mission.rake line 12 — WRONG
manifest_path = GalaxyGame::Paths::MISSIONS_PATH.join("manifests_v2", "lunar_precursor_manifest_v2_DRAFT.json").to_s
```

`missions_v2/manifests/` exists but is empty. The manifest needs to be finalized there
and the rake + game_data_paths.rb updated to use it.

**Fix:**
- Create `data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json`
  (copy of DRAFT with `_DRAFT` removed from manifest_id and filename)
- Add `MISSIONS_V2_MANIFESTS_PATH` constant to `game_data_paths.rb`
- Update rake line 12 to use `MISSIONS_V2_MANIFESTS_PATH`
- Update v2 profile `manifest_ref` field to point to new location
- Update rake lines 62-66 to handle `missions_v2/manifests/` prefix

### Issue 2 — CAR-300 Unit Name Mismatch

`task_car_300_charge_cycle_v2.json` uses `"CAR-300 Robot"` which normalizes to
`car_300_robot`. But the manifest hardware ID is `car_300_deployment_robot_mk1`.

The engine normalizes via:
```ruby
unit_name.to_s.downcase.gsub(/[^a-z0-9]+/, "_").gsub(/^_+|_+$/, "")
```

| Current | Normalizes to | Manifest ID | Match? |
|---|---|---|---|
| `"CAR-300 Robot"` | `car_300_robot` | `car_300_deployment_robot_mk1` | ❌ |
| `"CAR-300 Deployment Robot Mk1"` | `car_300_deployment_robot_mk1` | `car_300_deployment_robot_mk1` | ✅ |

**Fix**: Update all `"CAR-300 Robot"` references in `task_car_300_charge_cycle_v2.json`
to `"CAR-300 Deployment Robot Mk1"` (effects, completion_criteria, set_unit_state,
check_unit_state).

Also check `robot_charging_port` — current unit name `"Robot Charging Port"` normalizes
to `robot_charging_port` but manifest ID is `robot_charging_port` (no Mk1). Verify
whether this needs `"Robot Charging Port Mk1"` by checking the manifest entry.

### Issue 3 — task_deploy_car_robots.json Not Converted to V2.1

`data/json-data/missions/tasks_v2/task_deploy_car_robots.json` exists as old
`generic_repeatable_task` format. No corresponding `task_deploy_car_robots_v2.json`
exists in `missions_v2/tasks/`.

Current file issues:
- Template is `generic_repeatable_task` not `task_v2.1`
- Unit name is `"CAR-300"` — does not normalize to `car_300_deployment_robot_mk1`
- No `metadata.version: "2.1"` field

**Fix**: Create `data/json-data/missions_v2/tasks/task_deploy_car_robots_v2.json`
with correct v2.1 format:

```json
{
  "metadata": {
    "template": "task_v2.1",
    "version": "2.1",
    "description": "Deploy CAR-300 deployment robots for surface construction operations.",
    "intended_usage": "Reference in mission profiles for CAR-300 robot deployment.",
    "author": "AI Copilot",
    "date_created": "2026-07-08"
  },
  "tasks": [
    {
      "task_id": "deploy_car_robots",
      "name": "Deploy CAR-300 Deployment Robots",
      "description": "Deploy two CAR-300 Deployment Robot Mk1 units for surface construction operations.",
      "priority": 1,
      "effects": [
        { "action": "deploy_unit", "unit": "CAR-300 Deployment Robot Mk1", "count": 2 }
      ],
      "completion_criteria": {
        "type": "unit_state",
        "unit": "CAR-300 Deployment Robot Mk1",
        "state": "active"
      }
    }
  ]
}
```

---

## Files Involved

### Files to Create
| File | Purpose |
|---|---|
| `data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json` | Finalized manifest at correct v2 path |
| `data/json-data/missions_v2/tasks/task_deploy_car_robots_v2.json` | V2.1 CAR-300 deploy task |

### Files to Edit
| File | Change |
|---|---|
| `config/initializers/game_data_paths.rb` | Add `MISSIONS_V2_MANIFESTS_PATH` constant |
| `galaxy_game/lib/tasks/luna_mission.rake` | Update manifest_path line 12, fix lines 62-66 prefix handling |
| `data/json-data/missions_v2/profiles/luna_base_profile_v2.json` | Update `manifest_ref` to new path |
| `data/json-data/missions_v2/tasks/task_car_300_charge_cycle_v2.json` | Fix unit names to `"CAR-300 Deployment Robot Mk1"` |
| `data/json-data/missions_v2/tasks/task_deploy_robot_charging_port_v2.json` | Verify/fix unit name Mk1 suffix |

### Reference Files — Do Not Edit
| File | Why |
|---|---|
| `data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json` | Source for finalized manifest — copy content, don't modify |
| `data/json-data/missions/tasks_v2/task_deploy_car_robots.json` | Old format reference — do not modify, create v2.1 version instead |

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: `missions_v2/manifests/` path prefix handling in rake lines 62-66
The existing prefix strip logic only handles `"data/json-data/missions/"`:
```ruby
if manifest_ref.start_with?("data/json-data/missions/")
  engine_manifest_param = manifest_ref.sub("data/json-data/missions/", "")
```
After this fix, the v2 profile's `manifest_ref` will point to `missions_v2/manifests/...`
which does NOT start with `"data/json-data/missions/"`. Update this logic to also handle
the new path, or use `MISSIONS_V2_MANIFESTS_PATH` directly instead of string stripping.

⚠️ **GOTCHA 2**: `MISSIONS_V2_MANIFESTS_PATH` constant placement
Add in `game_data_paths.rb` under the `# V2 Mission System Paths` block added in the
Profiles V2 task, after `MISSIONS_V2_REGISTRY_PATH`:
```ruby
MISSIONS_V2_MANIFESTS_PATH = MISSIONS_V2_PATH.join('manifests').freeze
```

⚠️ **GOTCHA 3**: Do NOT modify the DRAFT manifest file
The DRAFT file at `data/json-data/missions/manifests_v2/` stays as-is for reference.
Create a new file at `missions_v2/manifests/` — copy content, change `manifest_id`
from `"lunar_precursor_manifest_v2_DRAFT"` to `"lunar_precursor_manifest_v2"`.

⚠️ **GOTCHA 4**: robot_charging_port Mk1 check
Before changing `task_deploy_robot_charging_port_v2.json`, verify the manifest entry:
manifest has `"id": "robot_charging_port"` (no Mk1). Check whether the engine lookup
for `"Robot Charging Port"` → `robot_charging_port` already matches. If it matches,
no change needed. Only add Mk1 if there's a mismatch.

---

## Implementation Steps

### Step 1 — Add MISSIONS_V2_MANIFESTS_PATH to game_data_paths.rb

Add after `MISSIONS_V2_REGISTRY_PATH` in the V2 paths block:
```ruby
MISSIONS_V2_MANIFESTS_PATH = MISSIONS_V2_PATH.join('manifests').freeze
```

Verify resolves correctly:
```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=development bundle exec rails runner \
  "puts GalaxyGame::Paths::MISSIONS_V2_MANIFESTS_PATH"'
```
Expected: path ending in `data/json-data/missions_v2/manifests`

### Step 2 — Create finalized manifest

```bash
cp data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json \
   data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json
```

Then edit the new file: change `"manifest_id": "lunar_precursor_manifest_v2_DRAFT"`
→ `"manifest_id": "lunar_precursor_manifest_v2"`. No other content changes.

Validate JSON:
```bash
python3 -m json.tool data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json
```

### Step 3 — Update v2 profile manifest_ref

In `data/json-data/missions_v2/profiles/luna_base_profile_v2.json`, update:
```json
"manifest_ref": "data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json"
```

### Step 4 — Update rake manifest loading

In `luna_mission.rake`:

**Line 12** — replace hardcoded DRAFT path:
```ruby
# BEFORE
manifest_path = GalaxyGame::Paths::MISSIONS_PATH.join("manifests_v2", "lunar_precursor_manifest_v2_DRAFT.json").to_s

# AFTER
manifest_path = GalaxyGame::Paths::MISSIONS_V2_MANIFESTS_PATH.join("lunar_precursor_manifest_v2.json").to_s
```

**Lines 62-66** — update prefix strip to handle new path:
```ruby
# BEFORE
if manifest_ref.start_with?("data/json-data/missions/")
  engine_manifest_param = manifest_ref.sub("data/json-data/missions/", "")
else
  engine_manifest_param = manifest_ref
end

# AFTER — resolve directly using JSON_DATA root
if manifest_ref.present?
  abs_manifest = GalaxyGame::Paths::JSON_DATA.join('..', '..', manifest_ref).expand_path.to_s
  engine_manifest_param = manifest_ref.start_with?("data/json-data/missions/") ?
    manifest_ref.sub("data/json-data/missions/", "") : manifest_ref
end
```

Actually — inspect how the engine uses `engine_manifest_param` before changing this.
Paste the engine init and usage in chat first. Don't guess.

### Step 5 — Fix CAR-300 unit names

In `data/json-data/missions_v2/tasks/task_car_300_charge_cycle_v2.json`:
Replace all occurrences of `"CAR-300 Robot"` with `"CAR-300 Deployment Robot Mk1"`.
This affects: `deploy_unit` effect, all `set_unit_state` effects, all `check_unit_state`
effects, and `completion_criteria.unit`.

Also check and fix `"CAR-300 Robot 1"` and `"CAR-300 Robot 2"` references — update
to `"CAR-300 Deployment Robot Mk1 1"` and `"CAR-300 Deployment Robot Mk1 2"` if the
engine uses indexed unit names, or confirm how multi-unit targeting works before editing.

### Step 6 — Check robot_charging_port Mk1

Verify manifest entry:
```bash
python3 -c "import json; m=json.load(open('data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json')); [print(h) for h in m['required_hardware'] if 'robot' in h['id'].lower() or 'charging' in h['id'].lower()]"
```

If manifest has `robot_charging_port` (no Mk1): `"Robot Charging Port"` → `robot_charging_port` ✅ no change needed.
If manifest has `robot_charging_port_mk1`: update task file to `"Robot Charging Port Mk1"`.

### Step 7 — Create task_deploy_car_robots_v2.json

Create `data/json-data/missions_v2/tasks/task_deploy_car_robots_v2.json` using the
template in the Issues section above. Validate JSON after creating.

### Step 8 — Run rake and verify

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=development bundle exec rake luna_mission:execute 2>&1 | tail -80'
```

Expected: robot_logistics phase tasks now execute. CAR-300 charge cycle passes.
Paste full SUMMARY block in chat before committing.

---

## Stop Conditions

- Engine init usage of `engine_manifest_param` is unclear — inspect before editing lines 62-66
- CAR-300 indexed unit naming (`"CAR-300 Robot 1"`) doesn't have a clear v2.1 convention — document and escalate
- New manifest fails JSON validation — fix before proceeding
- Any new failures introduced that weren't present before this task — document and stop

---

## Acceptance Criteria
- [ ] `MISSIONS_V2_MANIFESTS_PATH` constant resolves correctly
- [ ] `missions_v2/manifests/lunar_precursor_manifest_v2.json` exists and is valid JSON
- [ ] Rake loads manifest from new v2 path (not DRAFT)
- [ ] CAR-300 unit name normalizes to `car_300_deployment_robot_mk1`
- [ ] `task_deploy_car_robots_v2.json` exists in `missions_v2/tasks/`
- [ ] No regressions — tasks that were passing (9/13) still pass

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final rake result**: X tasks pass, Y failures
**Files created**: [list]
**Files modified**: [list]
**engine_manifest_param investigation**: [what the engine does with the param]
**robot_charging_port Mk1 finding**: [needed or not]
**Remaining failures after this task**: [list with root causes]
