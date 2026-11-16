# LegacyPortAdapter — Blocker: register_to_bus Not Implemented

**Date**: 2026-07-01
**Status**: BLOCKED — pending architectural decision
**Task**: `2026-06-24-HIGH-ARCHITECTURE-LEGACY-PORT-ADAPTER-IMPLEMENTATION.md` (Step 5)

---

## Blocker Summary

The remaining task (Step 5: migrate `task_deploy_gas_separator.json` from `connect_units` to `register_to_bus`) **cannot proceed** because `register_to_bus` does not exist anywhere in the codebase.

## Evidence

### 1. No `register_to_bus` in any task JSON
```bash
grep -r "register_to_bus" /Users/tam0013/Documents/git/galaxyGame/data/json-data/
# → zero results
```

### 2. No `register_to_bus` in any Ruby file
```bash
grep -rn "register_to_bus" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
# → zero results
```

### 3. Engine only handles `connect_units`
```ruby
# task_execution_engine_v2.rb:338-343
def execute_effect(effect)
  case effect['action']
  when 'connect_units'
    connect_units_from_effect(effect)
  # ... no 'register_to_bus' case exists
```

## What This Means

Migrating the task file to use `"action": "register_to_bus"` would require:

1. **Adding a new case** in `execute_effect` (line ~342) to handle `'register_to_bus'`
2. **Implementing** `register_to_bus_from_effect(effect)` method
3. **Defining the JSON schema** for what fields `register_to_bus` effects accept

This is **not a simple find-and-replace** — it requires engine code changes before the task file can reference it.

## Current State of Task (Steps 1-4)

| Step | Description | Status |
|------|-------------|--------|
| 1 | Confirm current state | ✅ Complete — LegacyPortAdapter exists, wired into engine |
| 2 | Evaluate existing engine fix | ✅ Complete — `check_and_decrement_port` uses adapter correctly |
| 3 | Implement LegacyPortAdapter | ✅ Complete — already implemented at `app/services/lookup/legacy_port_adapter.rb` |
| 4 | Migrate cryo tank blueprint | ✅ Complete — migrated to v1.9 in prior session |
| 5 | Rewrite gas separator task logic | ❌ **BLOCKED** — `register_to_bus` not implemented in engine |

## Recommendation for Architectural Decision

Options:
- **A**: Implement `register_to_bus` in the engine first, then migrate the task file
- **B**: Keep `connect_units` as-is (it works via LegacyPortAdapter) and defer bus registration to a future task
- **C**: Define the `register_to_bus` JSON schema and engine handler before proceeding

## Pending Architectural Decision

Who decides which option to take? This is beyond the scope of the original task file, which assumed `register_to_bus` was already available.
