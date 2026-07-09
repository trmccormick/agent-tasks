---
status: backlog
priority: HIGH
type: bugfix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-03-27
last_updated: 2026-07-07
relocated_from: active/ (2026-07-07 — scope corrected for MVP reality)
mvp_note: "Earth-delivered equipment for Luna Phase 5 MVP. Direct creation is correct; fix blueprint ID and load operational_data from JSON."
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase5+/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md \
         projects/galaxy_game/tasks/active/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md
  Then open the moved file and change: status: backlog → status: active
  Then verify with:
    find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
         -name "2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md"
  Paste the find output in chat. Only one result should exist (at active/ path).
  Do NOT read task content, run any other commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - If git mv fails with "not tracked": mv then git add at final path
  - Never leave stale copies in the source folder

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else is IN this task file.

---

# TASK: Fix EscalationService#create_automated_harvester — Use Correct Blueprint ID
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bugfix
**Created**: 2026-03-27
**Last Updated**: 2026-07-07

---

## Context

`EscalationService#create_automated_harvester` currently creates a generic `Units::Robot` with `unit_type: "robot"` and hardcoded `operational_data`. This is wrong because:

1. **Wrong blueprint ID**: Should use `"hrv_400_resource_harvester_mk1"` (the actual HRV-400 robot blueprint)
2. **Hardcoded operational_data**: Should load from JSON via `Lookup::UnitLookupService` so blueprints drive behavior
3. **No-Magic manufacturing hierarchy is premature**: Earth-delivered equipment for Luna Phase 5 MVP — direct creation is correct at this stage

**Game Design Note**: Initial robots, solar panels, and precursor equipment are all Earth-delivered. Local manufacturing (Level 3 tech via `robotics_assembly_line`) comes much later when Luna is more mature.

---

## Problem Statement

The method creates a ghost unit type (`unit_type: "robot"`) that doesn't exist in any blueprint file. It should create an HRV-400 by using the correct blueprint ID and loading operational defaults from JSON.

**Current behavior**:
```ruby
Units::Robot.create!(
  unit_type: "robot",  # ← WRONG — no such blueprint exists
  operational_data: { 'task_type' => 'atmospheric_harvesting', ... }  # ← hardcoded
)
```

**Expected behavior**:
```ruby
unit_data = Lookup::UnitLookupService.new.find_unit('hrv_400_resource_harvester_mk1')
Units::Robot.create!(
  unit_type: "hrv_400_resource_harvester_mk1",  # ← correct blueprint ID
  operational_data: unit_data['operational_data'] || {}  # ← from JSON
)
```

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Core escalation logic | `#create_automated_harvester` line ~214 |
| `galaxy_game/spec/services/ai_manager/escalation_service_spec.rb` | Spec — update to expect correct blueprint ID | line ~27 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` | HRV-400 blueprint — correct unit_type and materials |
| `data/json-data/operational_data/units/robots/resource/hrv_400_resource_harvester_mk1_data.json` | Operational data defaults (referenced by blueprint) |
| `galaxy_game/app/models/concerns/has_units.rb` | `add_unit` pattern — shows how to use UnitLookupService |
| `galaxy_game/app/models/units/robot.rb` | Robot model structure |

---

## Implementation Steps

### Step 1 — Research (read only)
```bash
docker exec -it web bash -c 'sed -n "214,300p" /home/galaxy_game/app/services/ai_manager/escalation_service.rb'
docker exec -it web bash -c 'cat /home/galaxy_game/data/json-data/operational_data/units/robots/resource/hrv_400_resource_harvester_mk1_data.json 2>/dev/null || echo "OPERATIONAL DATA FILE NOT FOUND"'
```

### Step 2 — Implement MVP Fix

Replace the hardcoded `operational_data` with blueprint lookup:

```ruby
def self.create_automated_harvester(settlement, material, quantity)
  # Use HRV-400 blueprint for all harvester types (Earth-delivered for MVP)
  unit_data = Lookup::UnitLookupService.new.find_unit('hrv_400_resource_harvester_mk1')
  
  return { status: :blocked, reason: 'HRV-400 blueprint not found' } unless unit_data

  # Override task-specific operational_data from the blueprint defaults
  base_operational = unit_data['operational_data'] || {}
  task_operational = case normalize_material(material)
  when 'O2'
    { 'task_type' => 'atmospheric_harvesting', 'target_material' => 'O2', 'target_quantity' => quantity, 'extraction_rate' => 10 }
  when 'H2O'
    { 'task_type' => 'ice_extraction', 'target_material' => 'H2O', 'target_quantity' => quantity, 'extraction_rate' => 50 }
  else
    { 'task_type' => 'regolith_mining', 'target_material' => material, 'target_quantity' => quantity, 'extraction_rate' => 25 }
  end

  operational_data = base_operational.merge(task_operational)

  Units::Robot.create!(
    name: "Automated #{material.titleize} Harvester",
    identifier: "ROBOT-#{SecureRandom.hex(4)}",
    unit_type: "hrv_400_resource_harvester_mk1",
    owner: settlement.owner,
    attachable: settlement,
    operational_data: operational_data
  )
end
```

> Key changes:
> - `unit_type` → `"hrv_400_resource_harvester_mk1"` (correct blueprint ID)
> - `operational_data` loaded from blueprint JSON, merged with task-specific overrides
> - Direct creation preserved (Earth-delivered for MVP — no manufacturing hierarchy needed yet)

### Step 3 — Update Spec

Update the spec to expect the correct blueprint ID and lookup call:

```ruby
describe '.create_automated_harvester' do
  before do
    allow(Lookup::UnitLookupService).to receive(:new).and_return(service)
    allow(service).to receive(:find_unit).with('hrv_400_resource_harvester_mk1').and_return(
      { 'id' => 'hrv_400_resource_harvester_mk1', 'operational_data' => { 'mobility_type' => 'wheeled' } }
    )
  end

  it 'creates robot with correct blueprint ID' do
    expect(Units::Robot).to receive(:create!).with(
      hash_including(unit_type: 'hrv_400_resource_harvester_mk1')
    ).and_call_original
    described_class.create_automated_harvester(settlement, 'oxygen', 100)
  end
end
```

### Step 4 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec galaxy_game/spec/services/ai_manager/escalation_service_spec.rb 2>&1 | tail -20'
```

Expected: X examples, 0 failures

---

## Acceptance Criteria
- [ ] `create_automated_harvester` uses `unit_type: "hrv_400_resource_harvester_mk1"` (not generic `"robot"`)
- [ ] Operational data loaded from blueprint JSON via `Lookup::UnitLookupService`
- [ ] Task-specific overrides merged on top of blueprint defaults
- [ ] Direct creation preserved (Earth-delivered MVP — no manufacturing hierarchy)
- [ ] Spec updated to expect correct blueprint ID and lookup call
- [ ] Isolation run: 0 failures
- [ ] No regressions in `spec/services/ai_manager/`

---

## Blueprint Audit Notes

**HRV-400 Blueprint** (`hrv_400_resource_harvester_mk1_bp.json`) — looks complete for MVP:
- Has `required_materials`, `production_data`, `cost_data`, `deployment_data`
- References operational data file at `units/robots/resource/hrv_400_resource_harvester_mk1_data.json`

**Harvesting Rover Craft** (`regolith_harvester_rover_bp.json`) — separate entity:
- Template: `base_craft` (not `unit_blueprint`)
- Different purpose: mobile surface skimmer vs deployed settlement robot
- No conflict — these are two different things serving different roles

**Potential Blueprint Gaps** (for future Gemini design task):
- [ ] Does `hrv_400_resource_harvester_mk1_data.json` exist? If not, create it with defaults matching blueprint's `operational_data_reference`
- [ ] Are there other harvester robot blueprints needed (O2-specific, H2O-specific variants)?
- [ ] Should the harvesting_rover craft have a separate deployment path in EscalationService?

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Operational data file referenced by blueprint doesn't exist (flag for design task)
- Root cause is in a shared concern, base class, or factory used across many specs
- Any architectural decision is required

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/ai_manager/escalation_service.rb
git add galaxy_game/spec/services/ai_manager/escalation_service_spec.rb
git commit -m 'fix: escalation_service — use HRV-400 blueprint ID and load operational_data from JSON'
git push
```

**Task file move on completion — use git mv, never copy:**
```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md projects/galaxy_game/tasks/completed/2026-07/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md
git commit -m 'chore: move 2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md to completed/'
```

---

## Documentation
- [ ] No doc changes needed — blueprint audit gaps listed above should be addressed via Gemini design task, not during this bugfix

---

## Dependencies
**Blocked by**: none
**Blocks**: Phase 5a Luna mission validation (robots must have correct blueprint IDs for testing)
**Related tasks**: `2026-03-26-HIGH-FEATURE-MISSION-PLANNER-NO-MAGIC-SOURCING.md` — No-Magic sourcing hierarchy belongs in Phase 6+ when local production is relevant

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
