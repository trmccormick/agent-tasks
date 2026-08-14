---
status: completed
priority: MEDIUM
type: bugfix
system_domain: AI_MANAGER | LOGISTICS
mvp_alignment: NPC_ECONOMY_LIFECYCLE
local_worker_safe: true
blocked_by: []
blocker_reason: "None"
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-06-MEDIUM-BUGFIX-CONTRACT-CREATION-SERVICE-IMPORT-ORDER-STUB.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-06-MEDIUM-BUGFIX-CONTRACT-CREATION-SERVICE-IMPORT-ORDER-STUB.md \
         projects/galaxy_game/tasks/active/2026-08-06-MEDIUM-BUGFIX-CONTRACT-CREATION-SERVICE-IMPORT-ORDER-STUB.md

Then update YAML status: backlog → active

Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, architecture gotchas, and verification steps.
```

## Problem Statement

`AIManager::ContractCreationService.create_import_order` is a stub that only logs — no database writes occur. It's called live from `ResourceAcquisitionService` line 121 but silently fails to create any records.

## Current State

### Method body (contract_creation_service.rb):
```ruby
def self.create_import_order(amount, material, cost_usd)
  Rails.logger.debug "ImportOrder created for #{amount} #{material} at #{cost_usd} USD."
end
```

### Call site (resource_acquisition_service.rb line 121):
`create_import_order` is called during USD import path processing.

### Models available:
- `PlayerContract` — exists, has operational data
- `MissionContract` — exists
- `ImportOrder` — **does NOT exist** in the codebase

## Scope

1. **Decide**: Should `create_import_order` create an `ImportOrder` model, or should it create a `PlayerContract`/`MissionContract` record instead?
2. **If ImportOrder is needed**: Create the model (migration + ActiveRecord), populate with relevant fields (amount, material, cost_usd, source, destination, status)
3. **If existing model is sufficient**: Update the method to create a `PlayerContract` or `MissionContract` record instead
4. **Verify**: Ensure the call chain in `ResourceAcquisitionService` works end-to-end

## Architecture Gotchas

- No `ImportOrder` model exists — only `PlayerContract` and `MissionContract` for contract records
- The USD funding path appears to be a planned feature that was never implemented
- `create_player_contract` also only logs (GCC path) — may need the same fix
- Check if any specs reference these methods before modifying

## Verification Steps

1. Run RSpec for affected services: `ai_manager/contract_creation_service_spec.rb`, `ai_manager/resource_acquisition_service_spec.rb`
2. Verify no regressions in existing contract-related specs
3. Confirm import order records are created in DB (not just logged)

## Stop Conditions

- Task is complete when the method creates actual database records AND all related specs pass
- If decision on model choice requires human input, stop and report findings

## Completion Report

### Model Chosen: LogisticsContract (existing DB table, no model file)
- **Key finding**: `LogisticsContract` had a DB table with all needed fields (from_settlement_id, to_settlement_id, material, quantity, shipping_cost, status, operational_data) but NO model file — Rails was using string-based table name magic
- `ImportOrder` model NOT created — unnecessary new model when LogisticsContract already existed
- `MissionContract` has no DB table (orphaned model)
- `PlayerContract` exists with DB table but wrong semantics (player-facing contracts, not AI import orders)

### Files Created/Modified
1. **galaxy_game/app/models/logistics_contract.rb** (NEW) — ActiveRecord model with belongs_to associations to Settlement::BaseSettlement
2. **galaxy_game/db/migrate/20260813000000_allow_null_from_settlement_for_logistics_contracts.rb** (NEW) — Migration allowing null from_settlement_id for Earth imports
3. **galaxy_game/app/services/ai_manager/contract_creation_service.rb** (MODIFIED) — Replaced stub with real LogisticsContract.create! call
4. **galaxy_game/spec/services/ai_manager/contract_creation_service_spec.rb** (NEW) — 4 examples, 0 failures

### Test Results: 4 examples, 0 failures ✅
- `.create_import_order creates a LogisticsContract record in the database` — PASS
- `.create_import_order sets from_settlement_id to nil for Earth imports` — PASS
- `.create_import_order logs the creation` — PASS
- `.create_player_contract is still a stub (logs only, no DB write)` — PASS

### Design Decisions
1. **LogisticsContract over ImportOrder**: Existing table already had all needed fields; creating a new model would duplicate schema
2. **from_settlement_id = nil**: Earth source not tracked in this call path; migration allows null (was previously NOT NULL)
3. **status = 0 (pending)**: Matches existing logistics pipeline convention
4. **operational_data stores import metadata**: {import_type: 'earth_import', currency: 'USD'} for traceability
5. **create_player_contract left as stub**: Not in scope; would need separate task if GCC path is needed
