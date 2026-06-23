---
status: backlog
priority: HIGH
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
created: 2026-03-27
last_updated: 2026-06-22
relocated_from: reorganization_attempt_3/ (2026-06-22)
codebase_status: "VIOLATION STILL PRESENT — app/services/ai_manager/escalation_service.rb contains multiple Units::Robot.create! calls (lines 124, 141, 159, 182) that violate No-Magic Protocol. Robot instantiation bypasses inventory checks and manufacturing constraints. Task requires: (1) refactor create_automated_harvester to check settlement inventory first, (2) implement manufacturing job queuing when materials available, (3) implement 3-tier sourcing hierarchy escalation, (4) update specs to verify No-Magic compliance."
---

# TASK: Refactor EscalationService#create_automated_harvester — No-Magic Robot Deployment
**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-03-27
**Last Updated**: 2026-06-20

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*

- **Template Conformance**: FAIL — original uses old format (no YAML frontmatter, no Docker wrapper, no TASK_TEMPLATE sections)
- **Docker Wrapper Check**: PASS — RSpec commands use correct `docker exec` format without `cd /home/galaxy_game`
- **MVP Alignment**: VALID — robot deployment is core to Luna ISRU buildout (Phase 5). The No-Magic Protocol violation blocks proper AI Manager behavior.
- **MVP Impact Note**: Prevents AI Manager from deploying harvesters during Luna settlement construction without violating the "no conjuring units" principle.
- **Action Line**: READY FOR LOCAL DISPATCH — rewritten to current template format, moved to phase5+

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B local (primary)
**Why This Agent**: Multi-step decision tree requiring architectural reasoning across inventory, manufacturing, and sourcing systems — well-specified with clear insertion points
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Context
`EscalationService#create_automated_harvester` currently calls `Units::Robot.create!` directly, bypassing the No-Magic Protocol entirely. This violates the core AI Manager principle: the AI cannot conjure units from nothing. A robot must either exist in settlement inventory (undeployed) or be built from available materials and infrastructure. GCC is only required when materials must be purchased from the market. This refactor replaces the direct `create!` call with a proper decision tree that respects the sourcing hierarchy and manufacturing constraints.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/architecture/services/ai_manager/planner.md` — No-Magic sourcing hierarchy (3 tiers)
- `docs/architecture/services/ai_manager/escalation_service.md` — Robot creation patterns and escalation rules
- `docs/architecture/services/ai_manager/AI_MANAGER_CONSTRUCTION_ECONOMICS.md` — Cost-benefit logic, GCC vs materials

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Problem Statement
`create_automated_harvester` uses `Units::Robot.create!` to instantiate a robot directly without checking inventory, manufacturing capability, or material availability. This violates the No-Magic Protocol and bypasses `load_unit_info` which loads operational defaults from the JSON blueprint.

**Error output**:
```
#<Units::Robot> received :create! with unexpected arguments
expected: ({unit_type: "robot", operational_data: {...5 keys...}})
     got: ({unit_type: "robot", operational_data: {...6 keys including deployment_site...}})
```

**Current behavior**: Service creates a robot from nothing using hardcoded `operational_data`.

**Expected behavior**: Service follows the No-Magic decision tree:
1. Check settlement inventory for undeployed HRV-400
2. If not found — check if AI can build one (facility + materials available)
3. If can build — queue ManufacturingJob (no GCC needed if materials owned)
4. If cannot build — escalate through 3-tier sourcing hierarchy
5. If all fail — return BLOCKED status, do not create robot

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Core escalation logic | `#create_automated_harvester` line ~113 |
| `galaxy_game/spec/services/ai_manager/escalation_service_spec.rb` | Spec — must be rewritten to test correct pattern | line ~27 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/concerns/has_units.rb` | `#add_unit` and `#install_unit` patterns |
| `galaxy_game/app/models/units/robot.rb` | Robot model — check for deploy/activate methods |
| `galaxy_game/app/models/settlement/base_settlement.rb` | Inventory query patterns |
| `galaxy_game/app/models/units/base_unit.rb` | `load_unit_info` — confirms operational_data loaded from blueprint |
| `galaxy_game/config/initializers/game_constants.rb` | `AI_PRIORITIES` — life support is critical tier |

### Blueprint Reference
**Robot Blueprint**: `hrv_400_resource_harvester_mk1`
**File**: `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json`
**Operational Data**: `data/json-data/operational_data/units/robots/resource/hrv_400_resource_harvester_mk1_data.json`

**Required facility**: `robotics_assembly_line`

**Required materials to build**:
| Material | Amount |
|---|---|
| `advanced_composites` | 120 kg |
| `hydraulic_systems` | 50 kg |
| `navigation_module` | 1 unit |
| `communication_relay` | 1 unit |
| `radiation_shielding` | 30 kg |

**Purchase cost**: 85,000 GCC (only if materials unavailable and market accessible)

### Migration
- [x] No migration needed

---

## Implementation Steps

> All agents: follow these steps exactly in order.
> Do not skip steps or reorder them.
> Do not proceed to the next step if the current step has not produced a clean result.

**Debug prints OK for complex callbacks** — add temporary `puts` statements, remove after verification.

### Step 1 — Research phase (read only, no changes)
```bash
docker exec -it web bash -c 'sed -n "88,140p" galaxy_game/app/services/ai_manager/escalation_service.rb'
docker exec -it web bash -c 'sed -n "15,55p" galaxy_game/app/models/concerns/has_units.rb'
docker exec -it web bash -c 'grep -n "def inventory\|undeployed\|unit_type\|available" galaxy_game/app/models/settlement/base_settlement.rb | head -20'
docker exec -it web bash -c 'grep -n "def " galaxy_game/app/models/units/robot.rb | head -20'
docker exec -it web bash -c 'grep -n "robotics_assembly\|ManufacturingJob\|manufacturing" galaxy_game/app/services/ai_manager/escalation_service.rb | head -10'
```

### Step 2 — Produce Synthesis Report and STOP

After research, produce the report (format below) and wait for approval.

### Step 3 — Implement decision tree in `create_automated_harvester`

Replace the direct `Units::Robot.create!` call with this decision tree:
```ruby
def self.create_automated_harvester(settlement, material, quantity)
  blueprint_id = harvester_blueprint_for(material)
  return { status: :blocked, reason: 'No blueprint for material' } unless blueprint_id

  # Step 1 — Check inventory for undeployed robot
  existing = find_undeployed_harvester(settlement, blueprint_id)
  if existing
    return deploy_harvester(existing, material, quantity)
  end

  # Step 2 — Check if AI can build one
  if can_build_harvester?(settlement, blueprint_id)
    return queue_harvester_build(settlement, blueprint_id, material, quantity)
  end

  # Step 3 — Escalate through sourcing tiers
  # (defer to MissionPlannerService sourcing hierarchy)
  { status: :blocked, reason: 'Insufficient inventory and manufacturing capability' }
end
```

> Implement `harvester_blueprint_for`, `find_undeployed_harvester`, `deploy_harvester`,
> `can_build_harvester?`, and `queue_harvester_build` as private methods.
> Match existing private method patterns in the service file.

### Step 4 — Implement `can_build_harvester?`

Check facility and materials — GCC not required if both are present:
```ruby
def self.can_build_harvester?(settlement, blueprint_id)
  has_robotics_facility?(settlement) &&
    has_required_materials?(settlement, blueprint_id)
end

def self.has_robotics_facility?(settlement)
  settlement.base_units
    .any? { |u| u.unit_type == 'robotics_assembly_line' }
end
```

> Verify the correct `unit_type` string for robotics assembly line
> by checking existing unit blueprints before implementing.

### Step 5 — Rewrite spec to test correct pattern

The spec must test the decision tree, not mock `Units::Robot.create!` directly:
```ruby
describe '.create_automated_harvester' do
  context 'when undeployed harvester exists in inventory' do
    it 'deploys existing robot without creating a new one' do
      # setup existing undeployed HRV-400 in settlement
      # expect deploy called, not create!
    end
  end

  context 'when no robot in inventory but facility and materials available' do
    it 'queues a manufacturing job' do
      # setup robotics_assembly_line + materials
      # expect ManufacturingJob queued
    end
  end

  context 'when neither inventory nor build capability available' do
    it 'returns blocked status' do
      # expect { status: :blocked }
    end
  end
end
```

> Write specs that test observable behavior, not internal `create!` calls.

### Step 6 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec galaxy_game/spec/services/ai_manager/escalation_service_spec.rb 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

### Step 7 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: [file:line]
Error: [exact message]
Expected: [value]
Got: [value]

ROOT CAUSE
[one paragraph]

PROPOSED FIX
[exact code change]

RISK
[any shared code affected]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] `create_automated_harvester` never calls `Units::Robot.create!` directly
- [ ] Decision tree checks inventory before attempting build
- [ ] Build check verifies facility AND materials (not GCC)
- [ ] GCC only checked if materials must be purchased from market
- [ ] Blocked status returned clearly when all options exhausted
- [ ] Spec tests decision tree behavior, not internal `create!` mock
- [ ] Isolation run: 0 failures
- [ ] No regressions in `spec/services/ai_manager/`

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/ai_manager/escalation_service.rb
git add galaxy_game/spec/services/ai_manager/escalation_service_spec.rb
git commit -m 'refactor: escalation_service — No-Magic robot deployment decision tree'
git push
```

**Task file move on completion — use git mv, never copy:**
```bash
git mv projects/galaxy_game/tasks/backlog/phase5+/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md projects/galaxy_game/tasks/completed/2026-03/2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md
git commit -m 'chore: move 2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md to completed/'
```

---

## Documentation
- [ ] No doc changes needed — `docs/architecture/services/ai_manager/escalation_service.md` already covers this pattern
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: `2026-03-26-HIGH-FEATURE-MISSION-PLANNER-NO-MAGIC-SOURCING.md` — shares No-Magic sourcing hierarchy

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
