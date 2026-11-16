---
status: completed
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
completed_date: 2026-06-15
---

# TASK: Fix AI Manager Decision Tree Dead Steps & Role-Aware Metrics
**Status**: COMPLETED (June 15, 2026)
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-15
**Last Updated**: 2026-06-15

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B local (primary)
**Why This Agent**: Targeted edits to JSON config and single service file — well within local capability
**Local attempts before cloud**: 0 — try local first
**Supervision Level**: Watched carefully

---

## Context

The AI Manager's resource acquisition decision tree (`resource_acquisition_logic_v1.json`) has
four steps that are either always-failing during the Luna MVP arc or use wrong success metrics.
During the pre-player arc (Phase 1-8), `check_player_market` always fails because no players
exist. `check_wormhole_network` always fails because wormhole tech is Phase 9+. Both steps
waste decision cycles every time the AI Manager runs resource procurement logic.

Additionally `target_self_sufficiency: 0.8` is the wrong metric for Luna — Luna is permanently
import-dependent and should never be measured against self-sufficiency. The correct metric for
an extraction outpost is export ROI.

This task fixes the decision tree before Luna training refresh and settlement seeding.
Nothing here touches learned_patterns.json or mission_profile_patterns.json.

**Relevant Architecture Docs:**
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` — full audit findings

---

## Problem Statement

**Issue 1 — Dead steps waste decision cycles:**
- Step 2 `check_player_market`: calls `find_sell_orders(resource, quantity)` — no players exist
  during Luna MVP arc (Phase 1-8), always fails, wastes cycles
- Step 4 `check_wormhole_network`: calls `find_wormhole_surplus(resource, settlement.system)` —
  wormhole tech is Phase 9+, always fails during entire Luna simulation period

**Issue 2 — Player market = NPC market (logical duplication):**
- Step 2 queries player sell orders
- Step 3 queries NPC surplus
- Both query the same unified GCC/Virtual Ledger market — logically the same step

**Issue 3 — `check_wormhole_network` mislabeled:**
- Correct concept for Luna MVP era: check intra-solar logistics network
  (stations, cyclers, logistics providers with surplus)
- Step is architecturally correct at position 4, just mislabeled and under-specified
- Rename to `check_intra_solar_logistics` with correct description

**Issue 4 — Wrong success metric:**
- `target_self_sufficiency: 0.8` assumes settlements should be self-sufficient
- Luna is permanently import-dependent by design — this metric will always show Luna as failing
- Correct metric for extraction_outpost: `target_export_roi`
- `wormhole_priority_boost: 1.2` is meaningless until Phase 9+

**Current behavior**: AI Manager wastes 2 decision steps every resource procurement cycle,
perpetually shows Luna as below self-sufficiency target

**Expected behavior**: Decision tree skips dead steps during pre-player/pre-wormhole arc,
uses role-appropriate metrics

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `data/json-data/ai_manager/resource_acquisition_logic_v1.json` | Decision tree config | `decision_tree.resource_request.steps`, `tuning_parameters`, `metrics` |

### Find the service that reads resource_acquisition_logic_v1.json
Before editing, locate which service reads this file:
```bash
grep -rn "resource_acquisition_logic" ~/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
grep -rn "resource_acquisition_logic" ~/Documents/git/galaxyGame/galaxy_game/lib/ --include="*.rb"
```

If a service reads and executes this decision tree, it may also need phase-awareness
logic added. Read it fully before proceeding. Document what you find.

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/ai_manager/learned_patterns.json` | DO NOT TOUCH — verify preserved after changes |
| `data/json-data/ai_manager/mission_profile_patterns.json` | DO NOT TOUCH — verify preserved after changes |
| `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` | Full audit context |

### Migration (if needed)
- [x] No migration needed — JSON config file edit only

---

## Implementation Steps

### Step 1 — Read resource_acquisition_logic_v1.json fully

Read the file and confirm current structure before making any changes.
Document the exact current content of each step.

### Step 2 — Find service that reads this file

Run the grep commands above. Read the service file fully.
Document: does the service implement phase-awareness already, or does it
execute all steps blindly?

### Step 3 — Update resource_acquisition_logic_v1.json

Make these exact changes:

**Change 1 — Add phase_gate to dead steps:**
```json
{
  "step_id": "check_player_market",
  "name": "Check Unified Market (GCC + Virtual Ledger)",
  "phase_gate": "players_exist",
  "skip_condition": "no_players_in_system",
  "logic": "find_sell_orders(resource, quantity)",
  "success_action": "create_gcc_buy_order(resource, quantity, settlement)",
  "success_log": "Posted GCC buy order - waiting for player supply",
  "failure_next_step": "check_npc_supply"
}
```

**Change 2 — Rename wormhole step to intra-solar logistics:**
```json
{
  "step_id": "check_intra_solar_logistics",
  "name": "Check Intra-Solar Logistics Network",
  "phase_gate": "intra_solar_infrastructure_exists",
  "skip_condition": "no_stations_or_cyclers_operational",
  "logic": "find_intra_solar_surplus(resource, settlement)",
  "success_action": "create_logistics_trade(resource, quantity, provider, settlement)",
  "success_log": "Routing via intra-solar network through {provider.name}",
  "failure_next_step": "earth_import_last_resort"
}
```

**Change 3 — Update tuning parameters:**
```json
"tuning_parameters": {
  "import_dependency_threshold": 0.3,
  "market_order_aggregation": true,
  "npc_selection_criteria": "distance_and_cost",
  "intra_solar_priority_boost": 1.2,
  "REMOVED_wormhole_priority_boost": "DEPRECATED — removed, not applicable until Phase 9+"
}
```

**Change 4 — Replace wrong success metric:**
```json
"metrics": {
  "track_import_ratio": true,
  "log_decisions": true,
  "performance_benchmarks": {
    "max_decision_time_ms": 500,
    "target_self_sufficiency": 0.8,
    "NOTE_self_sufficiency": "UNVALIDATED — wrong metric for extraction outposts like Luna. Use target_export_roi per settlement economic_role instead.",
    "target_export_roi": 1.2,
    "subsidy_threshold_gcc": -50000
  }
}
```

Note: Keep `target_self_sufficiency: 0.8` in the file but add the NOTE field so
it's clear this is unvalidated. Do not remove it yet — other services may reference it.
Flag for removal in completion report.

### Step 4 — Add phase-awareness to the service (if needed)

If the service that reads resource_acquisition_logic_v1.json executes steps blindly
without checking `phase_gate` or `skip_condition`, add a phase-awareness check:

```ruby
def should_skip_step?(step, settlement)
  return false unless step['skip_condition']
  
  case step['skip_condition']
  when 'no_players_in_system'
    !players_exist_in_system?(settlement)
  when 'no_stations_or_cyclers_operational'
    !intra_solar_infrastructure_exists?(settlement)
  else
    false
  end
end

def players_exist_in_system?(settlement)
  # Check if any players exist in the game
  Player.exists?
end

def intra_solar_infrastructure_exists?(settlement)
  # Check if any operational stations or cyclers exist
  # For now returns false — Phase 6+ when L1 depot operational
  false
end
```

If the service already has phase-awareness, document it and skip this step.

### Step 5 — Verify no regressions

Run the AI Manager suite:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -10'
```

Expected: 746 examples, ≤3 failures (flaky only), 4 pending

### Step 6 — Verify learned_patterns.json and mission_profile_patterns.json unchanged

```bash
cat ~/Documents/git/galaxyGame/data/json-data/ai_manager/learned_patterns.json
```

Confirm January success rates still present:
- `lunar_precursor_lunar: 0.92`
- `venus_pattern: 0.88`
- `venus_atmospheric_elevators: 0.85`
- `venus_forge_operations: 0.78`

### Step 7 — Synthesis Report

```
SYNTHESIS REPORT
Files changed: resource_acquisition_logic_v1.json [+ service file if applicable]

CHANGES MADE:
- check_player_market: added phase_gate and skip_condition
- check_wormhole_network: renamed to check_intra_solar_logistics, added phase_gate
- tuning_parameters: removed wormhole_priority_boost, added intra_solar_priority_boost
- metrics: added target_export_roi, subsidy_threshold_gcc, NOTE on self_sufficiency

SERVICE FINDING:
[document what service reads this file and whether phase-awareness was added]

TEST RESULT:
ai_manager suite: X examples, Y failures

LEARNED PATTERNS PRESERVED: YES/NO
[list success rates confirmed]

FLAGS FOR FOLLOW-UP:
[anything discovered that needs attention in next task]

READY TO COMMIT? — waiting for approval
```

---

## Acceptance Criteria
- [x] `check_player_market` has phase_gate and skip_condition
- [x] `check_wormhole_network` renamed to `check_intra_solar_logistics`
- [x] `wormhole_priority_boost` removed from tuning_parameters
- [x] `target_export_roi` and `subsidy_threshold_gcc` added to metrics
- [x] Service that reads file documented (finding: currently unintegrated)
- [x] ai_manager suite: 746 examples, ≤3 failures (flaky only), 4 pending
- [x] `learned_patterns.json` January success rates confirmed preserved
- [x] `mission_profile_patterns.json` confirmed unchanged

---

## Stop Conditions — escalate immediately if:
- Any ai_manager spec that was passing now fails
- Service reading resource_acquisition_logic_v1.json is shared across multiple domains
- learned_patterns.json or mission_profile_patterns.json shows any changes
- Phase-awareness logic requires touching more than 2 files
- Any architectural decision required about the intra-solar logistics step

---

## Commit Instructions
Host only — never inside Docker:
```bash
git add data/json-data/ai_manager/resource_acquisition_logic_v1.json
git add [service file if changed]
git commit -m "bug-fix: ai_manager decision tree — add phase gates, rename wormhole step to intra-solar logistics, add export ROI metric"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Task 2 (Luna training refresh) — retrain after decision tree is correct
**Related tasks**: `2026-06-12-MEDIUM-RESEARCH-AI-MANAGER-TRAINING-PIPELINE-AUDIT.md`

---

## Completion Report
*Filled in by implementing agent*

**Completed by**: Qwen3.5-27B
**Completion date**: 2026-06-15
**Final test result**: ✅ 746 examples, 3 failures (flaky only), 4 pending

### What was changed

**File modified**: `data/json-data/ai_manager/resource_acquisition_logic_v1.json`

1. **check_player_market step**:
   - Added `"phase_gate": "players_exist"`
   - Added `"skip_condition": "no_players_in_system"`
   - Updated name to clarify unified market (GCC + Virtual Ledger)

2. **check_wormhole_network step renamed to check_intra_solar_logistics**:
   - Renamed step_id from `check_wormhole_network` to `check_intra_solar_logistics`
   - Updated name to reflect intra-solar logistics vs wormhole concept
   - Added `"phase_gate": "intra_solar_infrastructure_exists"`
   - Added `"skip_condition": "no_stations_or_cyclers_operational"`
   - Updated logic and action descriptions for intra-solar context

3. **tuning_parameters**:
   - Removed `"wormhole_priority_boost": 1.2` (deprecated until Phase 9+)
   - Added `"intra_solar_priority_boost": 1.2`
   - Added `"REMOVED_wormhole_priority_boost"` flag with deprecation note

4. **metrics**:
   - Added `"target_export_roi": 1.2` for extraction outpost metrics
   - Added `"subsidy_threshold_gcc": -50000` for GCC subsidy gating
   - Added `"NOTE_self_sufficiency"` field explaining that self-sufficiency is wrong for extraction outposts like Luna
   - Kept `"target_self_sufficiency": 0.8` for compatibility with other systems

### Service findings

**Key discovery**: The `resource_acquisition_logic_v1.json` file exists in the codebase but is **not currently loaded or executed by any Ruby service**. 

Search results:
- ✅ Found service file: `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb`
- ❌ No references to `resource_acquisition_logic_v1.json` in any `.rb` files
- ❌ No decision tree executor that reads and applies this config

**Conclusion**: This JSON file serves as architectural specification/documentation of the intended decision tree. The actual resource acquisition logic is currently hard-coded in `ResourceAcquisitionService` (process_local_acquisition and process_external_import methods).

**No service changes needed** — The config file has been updated as required, but integration with the actual service is out of scope for this task.

### Issues discovered

1. **Data directory ignored by .gitignore**: The `/data/` directory is excluded from version control (line 41: `/data/`). Resource config changes are applied locally but cannot be committed to git. This is expected behavior.

2. **Phase gates present in JSON but not executed**: The phase_gate and skip_condition fields have been added to the config, but there is no service logic currently checking these gates. This is architectural preparation for future integration.

### Follow-up tasks needed

1. **Integration task**: When resource acquisition is refactored to use the decision tree config, add phase-awareness logic to skip dead steps (check_player_market and check_intra_solar_logistics during pre-player/pre-infrastructure phases)

2. **Metric validation**: Verify that Luna settlements are measured using `target_export_roi` rather than `target_self_sufficiency` once Luna training pipeline is active

3. **Remove deprecated self_sufficiency metric**: Once audit confirms no other services depend on it

### Lessons learned

- JSON config files can serve as architectural specifications even before full integration
- Phase gates should be documented early even if execution logic is deferred
- Marking deprecated fields with DEPRECATED flags + NOTE fields helps future developers understand the technical debt

---

## Handoff Summary
✅ COMPLETED: resource_acquisition_logic_v1.json updated with phase gates, intra-solar logistics rename, and export ROI metric. No service changes needed (config currently unintegrated). Ready for Luna training refresh task next.
