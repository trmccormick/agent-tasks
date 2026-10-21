---
title: "Audit MockDataHelper Methods & Synthesis Reports"
status: active
priority: HIGH
created: 2026-10-21
resume: false
review_status: pending
---

# Verify MockDataHelper Methods Match Synthesis Reports

## Context

Claude's audit found method name mismatches:
- Dashboard Progress claims `player_contracts`, `pending_operations` added → NOT in MockDataHelper
- Admin synthesis lists `ai_manager_priorities`, `digital_twins`, `terraforming_scenarios` → NOT in module (has `digital_twin_scenarios` instead)
- Admin title says "Digital Twin Sandbox" but steps reference `ai_manager_status`, `economic_indices`

**Scope**: Verify every method referenced in task files and synthesis reports actually exists in `app/services/mock_data_helper.rb`. Document discrepancies for reconciliation.

## Acceptance Criteria

- [ ] MockDataHelper inspected: list all public methods (def self.*)
- [ ] Dashboard synthesis cross-checked against method list
- [ ] Admin synthesis cross-checked against method list
- [ ] Wormholes synthesis cross-checked against method list
- [ ] Discrepancies documented: method missing vs method name wrong vs synthesis text wrong
- [ ] Recommendation provided: add/rename/reword?
- [ ] All 14+ existing methods verified callable without errors

## Steps

### Step 1: Read MockDataHelper Service
```bash
cat /Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui/app/services/mock_data_helper.rb
```

List all methods (look for `def self.`):
- system_status
- character_at_settlement
- research_queue
- local_facilities
- bay_inventory
- player_inventory
- manufacturing_queue
- player_ships
- active_units
- npc_buy_orders
- active_missions
- wormhole_network
- astrolift_routes
- transit_routes
- npc_corporations
- ai_manager_status
- digital_twin_scenarios
- buy_order_status
- economic_indices
- sim_engine_events
- market_quick_panel

(Add any others you find)

### Step 2: Check Dashboard Synthesis
Read: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/2026-10-20-DASHBOARD-SYNTHESIS.md`

List all methods/data referenced:
- `system_status` → ✓ exists
- `bay_inventory` → ✓ exists
- `player_inventory` → ✓ exists (alias for bay_inventory)
- `active_units` → ✓ exists (alias for player_ships)
- `market_quick_panel` → ✓ exists
- `player_contracts` → ? (mentioned but not found)
- `pending_operations` → ? (mentioned but not found)

Document any NOT FOUND.

### Step 3: Check Admin Synthesis
Read: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/2026-10-20-ADMIN-TELEMETRY-SYNTHESIS.md`

List all methods/data referenced:
- `ai_manager_status` → ✓ exists
- `economic_indices` → ✓ exists
- `digital_twin_scenarios` → ✓ exists (synthesis might say `digital_twins` but correct method is `digital_twin_scenarios`)
- `ai_manager_priorities` → ? (synthesis claims it exists but not in code)
- `terraforming_scenarios` → ? (synthesis claims it exists but not in code)

Document mismatches.

### Step 4: Check Wormholes Synthesis
Read: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/` (find wormhole summary if exists)

List all methods referenced and verify.

### Step 5: Call Each Method from Container
Test MockDataHelper in the running app:
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui
docker exec galaxy_game bin/rails console << 'EOF'
MockDataHelper.system_status
MockDataHelper.bay_inventory
MockDataHelper.active_missions
# ... test each method
EOF
```

Confirm no errors.

### Step 6: Document Audit Report
Create file: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/audit_mock_data_helper.md`

Structure:
```
# MockDataHelper Audit — 2026-10-21

## Methods in Service (21 total)
[list all methods found]

## Dashboard Synthesis — Cross-Check
| Method | Status | Found? | Notes |
|--------|--------|--------|-------|
| system_status | ✓ | Yes | - |
| bay_inventory | ✓ | Yes | - |
| player_contracts | ✗ | No | Referenced in synthesis, missing from code. Create or rename? |
| pending_operations | ✗ | No | Referenced in synthesis, missing from code. Create or rename? |

## Admin Synthesis — Cross-Check
| Method | Status | Found? | Notes |
|--------|--------|--------|-------|
| ai_manager_status | ✓ | Yes | - |
| digital_twin_scenarios | ✓ | Yes | Synthesis calls it "digital_twins" — rename method or fix synthesis? |
| ai_manager_priorities | ✗ | No | Mentioned in synthesis, missing from code. Create or rename? |
| terraforming_scenarios | ✗ | No | Mentioned in synthesis, missing from code. Create or rename? |

## Recommendations
1. Add missing methods or fix synthesis text?
2. Rename methods for clarity?
3. All tests passed without errors? Y/N
```

## Stop Condition

Create handoff file:
```
**Audit Complete**
- 21 methods in MockDataHelper
- 4 discrepancies found (see audit_mock_data_helper.md)
- All callable methods tested without errors
- Ready for human review to decide: add missing methods or reword synthesis?
```

Await GO before proceeding to reconciliation.

## Gotchas

- `player_inventory` is an alias for `bay_inventory` — both work
- `active_units` is an alias for `player_ships` — both work
- Method names changed from old helper to new service: check both if confused
- Don't create new methods yet; just audit and report discrepancies

## Notes

- This informs whether to add methods, rename them, or reword synthesis
- Helps scope Dashboard/Admin/Wormhole tasks after design session
- Blocks final task rewrite until clear which methods are canonical
