# LegacyPortAdapter — Status Synthesis Report (2026-07-01)

## STATUS SYNTHESIS REPORT

**Task**: LegacyPortAdapter (Option C — Bus-Topology Bridge)
**Status**: active
**Date**: 2026-07-01

### What I'm About to Do

The **LegacyPortAdapter is already fully implemented** at `galaxy_game/app/services/lookup/legacy_port_adapter.rb` and the engine fix in `task_execution_engine_v2.rb:508` (`check_and_decrement_port`) already uses it. The cryo tank blueprint migration to v1.9 was also completed in a prior session (confirmed via handoff notes). **The only remaining item is Step 5**: migrating `task_deploy_gas_separator.json` from `connect_units` calls to `register_to_bus` calls, then running targeted tests and the Luna V2 rake suite to confirm the baseline holds.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/lookup/legacy_port_adapter.rb` | Already implemented — read-only confirmation | ✅ Complete |
| `app/services/ai_manager/task_execution_engine_v2.rb:508` | `check_and_decrement_port` already uses adapter — read-only confirmation | ✅ Complete |
| `data/json-data/blueprints/units/storage/inflatable_cryo_tank_bp.json` | v1.9 migration confirmed in prior session — read-only verification | ✅ Complete |
| `data/json-data/missions/tasks_v2/task_deploy_gas_separator.json` | **Remaining work**: migrate from `connect_units` to `register_to_bus` | ❌ NOT DONE |
| `inflatable_gas_storage_bp.json` | Read-only: confirm typed-port passthrough still works | pending |
| `inflatable_pressure_tank.json` | Read-only: confirm zero-port handling still works | pending |

### Prerequisites Completed
- ✅ Read REVIEW_AGENT_GUIDE.md (Rule 0)
- ✅ Read GALAXY_GAME_CONTEXT.md (via task file references)
- ✅ Read PORT_CONNECTION_SYSTEM.md in full
- ✅ Read the v1.9 connection_schema interface contract in full
- ✅ Read this task file, including all four gotchas above
- ✅ Verified current codebase state: LegacyPortAdapter exists and is wired into engine

### Expected Outcomes
LegacyPortAdapter confirmed working (already implemented). Cryo tank blueprint confirmed migrated to v1.9 (already done). Gas separator task migrated from `connect_units` → `register_to_bus`. Targeted tests for typed-port passthrough and zero-port handling passing. Luna V2 rake suite at least matching 12/13 baseline.

### Critical Gotchas I Will Avoid
- ❌ Migrating or flattening inflatable_gas_storage_bp.json — adapter reads it natively ✅
- ❌ Forcing non-zero ports on port-less units — adapter returns 0, no error ✅
- ❌ Free-text gas names in allowed_formulas — chemical formulas only ✅
- ❌ Leaving old + new port blocks both present — full removal of legacy block on migration ✅

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1 (confirm current state) upon approval.
