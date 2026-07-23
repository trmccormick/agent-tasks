---
status: backlog
priority: HIGH
type: bug-fix
system_domain: UNITS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

# TASK: Fix HasBlueprintPorts — Remove Hardcoded Fallback and Silent Default
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-03-30
**Last Updated**: 2026-07-15

---

## Local Worker Triage Report
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: FAIL — original task file predates current template, missing many sections
- **Docker Wrapper Check**: PASS — RSpec strings use correct `docker exec` format
- **MVP Alignment**: VALID — bug is still present in codebase, affects game balance and economic differentiation
- **MVP Impact Note**: Misconfigured blueprints silently grant 5 ports each type to all craft types, breaking economic differentiation across all satellite/craft models
- **Action Line**: READY FOR LOCAL DISPATCH — well-specified, single file, explicit paths provided

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Single file fix, fully specified with explicit code and acceptance criteria
**Local attempts before cloud**: N/A — first dispatch
**Supervision Level**: Watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md` (EXECUTOR Role section)
2. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
`HasBlueprintPorts` concern defines port counts for craft models (satellites, probes, etc.). It has two critical bugs that violate blueprint-driven design: a hardcoded fallback to `'generic_satellite'` for ALL craft types, and a silent default of 5 ports each type when no blueprint is found. This means any misconfigured or missing blueprint silently grants excess ports, breaking game balance.

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/operations/wh-expansion.md` — generic_satellite blueprint context
- `data/json-data/blueprints/craft/space/satellites/generic_satellite_bp.json` — canonical port data structure

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: The hardcoded `'generic_satellite'` on line 15 is used as a fallback for ALL craft types, not just satellites
- ❌ Wrong: Keep the generic_satellite fallback and only fix the silent default
- ✅ Right: Remove the hardcoded fallback entirely — each craft type should use its own `default_blueprint_id` via the class method
- Why: Every craft (satellite, probe, tug, etc.) falls through to generic_satellite first, giving them all satellite port counts regardless of their actual blueprint

⚠️ **GOTCHA 2**: The silent default `{ internal_module_ports: 5, ... }` on lines 30-36 masks missing blueprints
- ❌ Wrong: Change the default to a different number (e.g., 0 or 1)
- ✅ Right: Raise an error or return zero ports — missing blueprints should be visible and fixable, not silently ignored
- Why: Silent defaults hide configuration errors and give unintended game balance

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Fix HasBlueprintPorts — Remove Hardcoded Fallback and Silent Default
**Status**: backlog → active → completed
**Date**: 2026-07-15

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/models/concerns/has_blueprint_ports.rb` | Port definitions concern | not started |
| `spec/models/concerns/has_blueprint_ports_spec.rb` | Existing tests | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to current/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read TASK_TEMPLATE.md EXECUTOR section
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
Exact description of what "done" looks like

### Critical Gotchas I Will Avoid
- ❌ Keep hardcoded generic_satellite fallback — instead remove it entirely
- ❌ Change silent default number — instead raise error or return zero ports

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
`HasBlueprintPorts` concern has two critical bugs that violate blueprint-driven design:

1. **Hardcoded fallback to 'generic_satellite' blueprint** for all craft types (line 15)
2. **Silent default port fallback** returning 5 ports of each type when no blueprint found (lines 30-36)

These bugs mean misconfigured or missing blueprints silently give crafts more ports than intended, breaking game balance and economic differentiation.

**Evidence of Bug**:
```bash
grep -n "generic_satellite\|hardcoded" galaxy_game/app/models/concerns/has_blueprint_ports.rb
# Output: line 15: blueprint_id = 'generic_satellite'
```

**Current behavior**: 
- Every craft type falls through to `'generic_satellite'` blueprint lookup first
- If no blueprint found, silently returns 5 ports of each type
- Missing blueprints are masked by silent defaults

**Expected behavior**: 
- Each craft type uses its own `default_blueprint_id` via the class method
- Missing blueprints raise an error or return zero ports (not silent default)
- Craft port counts are strictly constrained by their actual blueprint data

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/models/concerns/has_blueprint_ports.rb` | Port definitions concern | Lines 11-20 (blueprint lookup) + lines 30-36 (default fallback) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/models/concerns/has_blueprint_ports_spec.rb` | Existing tests for concern |
| `data/json-data/blueprints/craft/space/satellites/generic_satellite_bp.json` | Canonical port data structure |
| `galaxy_game/app/models/craft/satellite/base_satellite.rb` | Example craft model using this concern |

### Migration (if needed)
- [x] No migration needed — pure code fix, no schema changes

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to current/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside galaxy_game repo root:
git mv docs/new_agent/projects/galaxy_game/tasks/active/2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md \
       docs/new_agent/projects/galaxy_game/tasks/current/2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks \
     -name "2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `current/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Remove hardcoded generic_satellite fallback

In `galaxy_game/app/models/concerns/has_blueprint_ports.rb`, remove the hardcoded blueprint_id assignment on line 15:

```ruby
# before
def get_ports_data
  # Try operational_data first
  if operational_data&.dig('ports')
    return operational_data['ports']
  end
  
  # Fallback to blueprint data
  blueprint_service = Lookup::BlueprintLookupService.new
  
  # Try the specific blueprint ID first
  blueprint_id = 'generic_satellite'  # ← REMOVE THIS LINE
  blueprint_data = blueprint_service.find_blueprint(blueprint_id, 'satellite')
  
  if blueprint_data&.dig('ports')
    return blueprint_data['ports']
  end
  
  # If that fails, try the default_blueprint_id from the class
  blueprint_id = default_blueprint_id
  blueprint_data = blueprint_service.find_blueprint(blueprint_id, blueprint_category)
  
  if blueprint_data&.dig('ports')
    return blueprint_data['ports']
  end
  
  # Return default ports if no blueprint data found
  {
    'internal_module_ports' => 5,
    'external_module_ports' => 5,
    'internal_rig_ports' => 5,
    'external_rig_ports' => 5
  }
end

# after
def get_ports_data
  # Try operational_data first
  if operational_data&.dig('ports')
    return operational_data['ports']
  end
  
  # Fallback to blueprint data
  blueprint_service = Lookup::BlueprintLookupService.new
  
  # Try the default_blueprint_id from the class (no hardcoded fallback)
  blueprint_id = default_blueprint_id
  blueprint_data = blueprint_service.find_blueprint(blueprint_id, blueprint_category)
  
  if blueprint_data&.dig('ports')
    return blueprint_data['ports']
  end
  
  # Raise error if no blueprint data found — do not silently default
  raise ArgumentError, "No ports data found for #{self.class} with blueprint_id: #{blueprint_id}"
end
```

### Step 2 — Verify existing tests pass

Run the existing spec to establish baseline:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/concerns/has_blueprint_ports_spec.rb 2>&1 | tail -20'
```

Expected result: All examples pass, 0 failures (or document pre-existing failures)

### Step 3 — Add test for missing blueprint scenario

In `spec/models/concerns/has_blueprint_ports_spec.rb`, add a test that verifies missing blueprints raise an error:

```ruby
describe 'missing blueprint handling' do
  let(:craft) { create(:base_satellite) }
  
  it 'raises ArgumentError when no blueprint data is found' do
    allow_any_instance_of(Lookup::BlueprintLookupService).to receive(:find_blueprint).and_return(nil)
    
    expect { craft.get_ports_data }.to raise_error(ArgumentError, /No ports data found/)
  end
end
```

### Step 4 — Verify all tests pass

Run the concern spec again:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/concerns/has_blueprint_ports_spec.rb 2>&1 | tail -20'
```

Expected result: All examples pass, 0 failures

### Step 5 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: spec/models/concerns/has_blueprint_ports_spec.rb
Error: [exact message if any]
Expected: X examples, 0 failures
Got: [actual result]

ROOT CAUSE
[one paragraph if tests failed]

PROPOSED FIX
[exact code change if needed]

RISK
- HasBlueprintPorts is used by all craft models (satellites, probes, tugs)
- Any craft with missing blueprints will now raise errors instead of silently defaulting
- May need to fix blueprints for existing craft data

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] No hardcoded `'generic_satellite'` fallback in blueprint lookup
- [ ] Missing blueprints raise an error or return zero ports (not silent default of 5)
- [ ] Craft port counts are strictly constrained by their actual blueprint data
- [ ] All craft models correctly inherit port definitions from their specific blueprints
- [ ] Tests verify that missing blueprints do NOT silently grant default ports
- [ ] Isolation run: 0 failures
- [ ] No regressions in related specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

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
git add docs/new_agent/projects/galaxy_game/tasks/current/2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md
git add galaxy_game/app/models/concerns/has_blueprint_ports.rb
git commit -m "fix: remove hardcoded generic_satellite fallback in HasBlueprintPorts"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv docs/new_agent/projects/galaxy_game/tasks/current/2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md \
       docs/new_agent/projects/galaxy_game/tasks/completed/2026-07/2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md

git commit -m "chore: move 2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md to completed/"
```

---

## Documentation
- [x] No doc changes needed — reference docs already cover blueprint structure

---

## Dependencies
**Blocked by**: none
**Blocks**: None directly
**Related tasks**: 
- Blueprint-driven craft design (upstream)
- Economic differentiation tuning (downstream)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `galaxy_game/app/models/concerns/has_blueprint_ports.rb` — removed hardcoded generic_satellite fallback + replaced silent default with error raise
- `spec/models/concerns/has_blueprint_ports_spec.rb` — added missing blueprint test

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: Hardcoded generic_satellite removed | Silent default replaced with error raise | Missing blueprint test added
