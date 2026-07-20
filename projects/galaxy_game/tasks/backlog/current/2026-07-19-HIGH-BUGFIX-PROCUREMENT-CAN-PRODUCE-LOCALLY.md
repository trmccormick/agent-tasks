---
title: "ProcurementService.can_produce_locally? — Delegate to PrecursorCapabilityService"
priority: HIGH
status: backlog
phase: AI_MANAGER
owner: Implementation Agent (Qwen)
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-19
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md \
         projects/galaxy_game/tasks/active/2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A (RSpec may need targeted run on procurement specs)
- **MVP Alignment**: VALID — ProcurementService is core to Luna settlement resource management
- **MVP Impact Note**: `can_produce_locally?` controls whether settlements produce resources locally vs. import from Earth — broken stub means all procurement decisions default to "produce locally" (always true), which masks supply chain issues
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Ruby service fix + targeted RSpec verification
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully — architectural correction from prior research

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Research Summary**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/2026-07-11-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md` (read for context, but IGNORE its fix recommendation — see correction below)
5. **Guardrails**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`

> Agent MUST read in this order. Do not skip.

---

## ⚠️ CRITICAL CORRECTION — DO NOT IMPLEMENT THE WRONG PATTERN

**A prior research report (`2026-07-11-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md`) recommended fixing `ProcurementService.can_produce_locally?` by adopting the `NpcPriceCalculator` pattern:**

```ruby
material_data = Lookup::MaterialLookupService.new.find_material(resource)
lunar_prod = material_data.dig('pricing', 'lunar_production')
facility = lunar_prod['facility_required']
settlement.has_facility?(facility)
```

**DO NOT implement this pattern. It is superseded and incorrect.**

That report's research read `precursor_capability_service.rb` directly but did not follow through to recognize it as the correct existing solution — it recommended a facility/pricing-lookup approach instead, which is a different, redundant mechanism.

**The actually-correct fix**: delegate `ProcurementService.can_produce_locally?` to `PrecursorCapabilityService`, which already:
- Queries celestial body sphere data directly (geosphere crust composition, atmosphere gas percentages, hydrosphere water bodies)
- Is already wired into `MissionPlannerService` and confirmed working
- Uses chemical formula strings (`'CO2'`, `'N2'`, `'CH4'`, `'H2O'`) as the resource identifier, not material-lookup display names or symbols

**The remaining gap is narrow**: `ProcurementService` never calls `PrecursorCapabilityService` at all — it still returns a hardcoded `true` via the `settlement_has_facility?` stub. The fix is delegation, not a new facility-lookup implementation.

---

## Problem Statement

`ProcurementService.can_produce_locally?` is a **broken stub** that always returns `true`. It hardcodes 3 facility types (`atmospheric_processor`, `isru_processor`, `cnt_fabricator`) that don't exist in the codebase, and its helper `settlement_has_facility?` is also a stub returning `true`.

**Impact**: All procurement decisions default to "produce locally" regardless of whether the settlement actually has the capability. This masks supply chain issues and gives false confidence in local production capacity.

**Current behavior**:
```ruby
def self.can_produce_locally?(settlement, resource, _amount)
  return false unless settlement
  # Hardcoded stub — always returns true for any settlement
  settlement_has_facility?(settlement, resource)
end

def self.settlement_has_facility?(_settlement, _facility)
  true  # ← BROKEN: always returns true
end
```

**Expected behavior**: Query the celestial body's sphere data (geosphere/atmosphere/hydrosphere) via `PrecursorCapabilityService` to determine if local production is actually possible for the given resource.

---

## Context

### Callers of `can_produce_locally?`
| Caller | File:Line | Context |
|--------|-----------|---------|
| `ProcurementService.procure_resource` | `procurement_service.rb:7` | Decides whether to produce locally or buy from market |
| `NpcPriceCalculator.calculate_import_cost` | `npc_price_calculator.rb:92` | Determines cost basis (local vs. import) for NPC pricing |
| `MissionPlannerService.calculate_total_delivered_cost` | `mission_planner_service.rb:488` | Uses `PrecursorCapabilityService.can_produce_locally?` (different signature) |
| `MissionPlannerService.suggest_precursor_if_beneficial` | `mission_planner_service.rb:639` | Checks if precursor deployment would enable local production |

### Resource Naming Convention — CRITICAL
- **Backend**: Chemical formulas ONLY (`'O2'`, `'H2O'`, `'CO2'`, `'CH4'`) — never display names like `'Oxygen'`, `'Water'`
- **UI/display**: Display names (`'Oxygen'`, `'Water'`, `'Carbon Dioxide'`) — never in backend code
- `PrecursorCapabilityService` accepts chemical formula strings and queries sphere data directly

### PrecursorCapabilityService (THE CORRECT implementation)
- Location: `app/services/ai_manager/precursor_capability_service.rb`
- Takes a celestial body, queries sphere data directly
- Already wired into `MissionPlannerService` and confirmed working
- Uses chemical formula strings as resource identifiers

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | Fix the broken stub | `can_produce_locally?`, `settlement_has_facility?` |

### Reference Files — read but do not edit
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/app/services/ai_manager/precursor_capability_service.rb` | The correct implementation to delegate to |
| `galaxy_game/app/services/ai_manager/mission_planner_service.rb` | Shows how PrecursorCapabilityService is called (pattern reference) |
| `galaxy_game/spec/services/ai_manager/procurement_service_spec.rb` | Existing specs — verify they pass after fix |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md \
       projects/galaxy_game/tasks/active/2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Research existing code (do NOT edit)

Read these files to understand the current state:
- `galaxy_game/app/services/ai_manager/procurement_service.rb` — confirm the broken stub
- `galaxy_game/app/services/ai_manager/precursor_capability_service.rb` — understand the delegation target's API
- `galaxy_game/app/services/ai_manager/mission_planner_service.rb` — see how PrecursorCapabilityService is called (pattern reference)
- `galaxy_game/spec/services/ai_manager/procurement_service_spec.rb` — existing specs

### Step 2 — Fix ProcurementService.can_produce_locally?

Replace the broken stub with delegation to PrecursorCapabilityService:

```ruby
# In galaxy_game/app/services/ai_manager/procurement_service.rb

def self.can_produce_locally?(settlement, resource, _amount = nil)
  return false unless settlement
  
  celestial_body = settlement.location&.celestial_body
  return false unless celestial_body
  
  capability_service = AIManager::PrecursorCapabilityService.new(celestial_body)
  capability_service.can_produce_locally?(resource)
end

# Remove or deprecate the broken stub:
def self.settlement_has_facility?(_settlement, _facility)
  true  # ← REMOVE THIS ENTIRELY
end
```

**Key points:**
- Use chemical formula strings for `resource` (e.g., `'O2'`, `'H2O'`, `'CO2'`) — never display names
- Resolve settlement → celestial body via `settlement.location&.celestial_body`
- Delegate to `PrecursorCapabilityService` which queries sphere data directly
- Remove the broken `settlement_has_facility?` stub entirely (no callers need it)

### Step 3 — Verify with RSpec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/procurement_service_spec.rb'
```

Expected: All existing specs pass. If any fail, the failure is likely due to the stub being removed — update the spec to reflect the new delegation behavior.

### Step 4 — Verify PrecursorCapabilityService integration

Run a targeted test to confirm the delegation works end-to-end:

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/precursor_capability_service_spec.rb'
```

Expected: All specs pass (this was already working, just confirming no regressions).

---

## Acceptance Criteria
- [ ] `ProcurementService.can_produce_locally?` delegates to `PrecursorCapabilityService`
- [ ] Broken `settlement_has_facility?` stub removed (no callers depend on it)
- [ ] Resource identifiers use chemical formulas (`'O2'`, `'H2O'`, `'CO2'`) — no display names in backend
- [ ] All existing procurement_service specs pass
- [ ] No regressions in precursor_capability_service specs
- [ ] Settlement → celestial body resolution handles nil location gracefully (returns false)

---

## Stop Conditions — escalate to user immediately if:
- `PrecursorCapabilityService.can_produce_locally?` signature has changed since research (verify before implementing)
- Any caller of `can_produce_locally?` passes a display name instead of chemical formula (flag as broader issue)
- `settlement_has_facility?` has callers that were missed in the grep analysis (verify with terminal search)
- The fix requires changes to more files than specified above

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/ai_manager/procurement_service.rb
git commit -m "fix: delegate ProcurementService.can_produce_locally? to PrecursorCapabilityService

- Replace broken stub (always returns true) with sphere-data delegation
- Remove settlement_has_facility? stub — no callers depend on it
- Resource identifiers use chemical formulas ('O2', 'H2O', 'CO2') only
- Settlement → celestial body resolution handles nil location gracefully"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update this task file with completion status when done
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: None — research is complete, fix is well-defined
**Blocks**: None — independent bugfix
**Related tasks**: 
- `2026-07-11-HIGH-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md` (research — in backlog/research, read for context only)
- `2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md` (related — procurement affects gameplay decisions)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

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
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
