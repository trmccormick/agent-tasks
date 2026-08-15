---
status: blocked
priority: HIGH
type: bug-fix
system_domain: UNITS | LOGISTICS
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-09
blocked_by: Architecture decision on general-storage unit identification
---

# TASK: Fix Inventory#available_general_storage — silent-zero due to missing storage_type attribute

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-09-HIGH-BUGFIX-INVENTORY-AVAILABLE-GENERAL-STORAGE-SILENT-ZERO.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-09-HIGH-BUGFIX-INVENTORY-AVAILABLE-GENERAL-STORAGE-SILENT-ZERO.md \
         projects/galaxy_game/tasks/active/2026-08-09-HIGH-BUGFIX-INVENTORY-AVAILABLE-GENERAL-STORAGE-SILENT-ZERO.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-09-HIGH-BUGFIX-INVENTORY-AVAILABLE-GENERAL-STORAGE-SILENT-ZERO.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

This bug was discovered as a side effect of `2026-08-06-MEDIUM-BUGFIX-SHELL-PRINTING-SERVICE-PREEXISTING-TEST-FAILURES` (completed, commit `04460b23`). The implementing agent had to mock `can_store?` in the shell-printing spec because `Inventory#available_general_storage` always returns 0.

**This is NOT shell-printing-specific.** Any spec, service, or feature that relies on general storage capacity calculation hits the same silent-zero problem. The bug is a latent defect in the Inventory model that affects all unit-storage-dependent systems.

---

## Problem Statement

`Inventory#available_general_storage` (in `inventory.rb`, ~line 152) iterates over units and checks `u.storage_type == 'general'` to identify general-purpose storage units. However, `BaseUnit` does not have a `storage_type` attribute at all — so the check always fails (`nil == 'general'` is `false`) and the method always returns 0 capacity, regardless of how many general-storage units are actually in the inventory.

**Impact:**
- Any system that checks available general storage gets 0 (silent failure)
- Shell-printing service had to mock `can_store?` to work around it
- Other specs/features likely silently fail when checking storage capacity
- No error is raised — the bug is a silent-zero, making it hard to diagnose

---

## Architecture Gotchas

### GOTCHA 1: How general storage units are actually identified
- ❌ Wrong: Add `storage_type` column/attribute to `BaseUnit` and populate it everywhere
- ✅ Right: [FILL IN — determine the correct identification mechanism. Does the codebase use a different attribute? A unit type/class check? A naming convention?]
- Why: Adding a new column to BaseUnit is a migration + model change that affects every unit in the system. There may already be an established pattern for identifying storage units.

### GOTCHA 2: Scope of fix — find all callers
- [FILL IN — list all callers of `available_general_storage` and any other methods that check `storage_type` on units]
- Why: If other code also checks `u.storage_type`, those need to be updated too, not just this one method.

### GOTCHA 3: Test impact is broad
- [FILL IN — estimate how many specs reference available_general_storage or can_store?]
- Mocks added in shell-printing spec (commit `04460b23`) may need to be removed once the real fix lands.

---

## Implementation Steps

### Step 1: Synthesis Report (BEFORE any code changes)

Complete a synthesis report documenting the current state:

```
## Synthesis Findings

### Current Code State
- inventory.rb line ~152: `u.storage_type == 'general'` check — what does it actually do?
- BaseUnit model: confirm no storage_type attribute exists (grep for it)
- What attributes/methods DOES BaseUnit have that could identify general storage units?

### Caller Analysis
- All callers of available_general_storage: [list them]
- Any other code checking u.storage_type: [list them]

### Fix Strategy
- [FILL IN — proposed approach based on findings above]
```

### Step 2: Implement the Fix

[FILL IN — implementation details once synthesis is complete]

**Key decisions to make during synthesis:**
1. What identifies a "general storage" unit? (type attribute? name pattern? class?)
2. Should `storage_type` be added as an attribute, or is there an existing mechanism?
3. Does the fix need to handle legacy data (units without the identifying attribute)?

### Step 3: Remove Workarounds

- Review commit `04460b23` for any mocks/workarounds added to shell-printing spec
- Remove those mocks once the real fix is in place
- Verify the spec passes without mocks

### Step 4: Verification

[FILL IN — specific verification steps based on fix approach]

**Must verify:**
- `available_general_storage` returns non-zero when general storage units exist
- No regressions in existing specs (run targeted re-run, not full suite)
- All callers of the method still work correctly

---

## Acceptance Criteria
- [ ] `Inventory#available_general_storage` returns correct capacity (non-zero) when general storage units are present
- [ ] Fix uses established codebase pattern for identifying unit types (not ad-hoc)
- [ ] No other code checks `u.storage_type` that would break with this fix
- [ ] Shell-printing spec mocks from commit `04460b23` removed and tests still pass
- [ ] Targeted re-run of affected specs: 0 failures

---

## Stop Conditions

**DO NOT proceed beyond synthesis if:**
- Synthesis reveals the codebase has no concept of "general storage" units (the feature may be incomplete, not just buggy)
- The identifying mechanism for general storage is unclear after reviewing BaseUnit and all unit subclasses

**STOP and escalate if:**
- Fix requires adding a new column to BaseUnit (migration scope — needs review)
- More than 10 specs need mock removals (scope may be larger than expected)

---

## BLOCKED: No Consistent Identifying Mechanism Exists

**Date blocked:** 2026-08-14  
**Blocker:** The codebase has NO consistent mechanism for identifying general-storage units. This is a design question, not a bug fix.

### Caller Analysis (Permanent Record)

#### Callers of `available_general_storage`

| Location | Usage |
|---|---|
| `inventory.rb:52` | Passed to capacity check in `add_item` |
| `inventory.rb:74` | Private `can_store?`: `available_general_storage >= amount` |

Only **two callers**, both internal to `Inventory`. No external service callers.

#### All code checking `u.storage_type` on units

| Location | Pattern | Status |
|---|---|---|
| `has_storage.rb:26` | `unit.storage_type \|\| 'general'` | ✅ Correct — defaults nil to general |
| `has_storage.rb:108` | `unit.storage_type == material_type_to_storage(...)` | ⚠️ Same nil-vs-'general_storage' gap |
| `inventory.rb:155` (original) | `u.storage_type == 'general'` | ❌ BUG — nil != 'general' |
| `base_unit.rb:237` | `def storage_type; operational_data&.dig('storage', 'type')` | The derived method itself |

### Broadened Grep: Every unit_type with a `'storage'` key in operational_data

#### From JSON data files (`data/json-data/operational_data/units/storage/`)

| File | `storage` structure | Has `type`? |
|---|---|---|
| `storage_unit_data.json` | `{ "capacity": 250.0, "current_level": 0.0 }` | ❌ **No `type` key at all** |
| `lox_storage_tank_mk1_data.json` | `{ "capacity": 10000, "current_level": 0 }` | ❌ **No `type` key at all** |
| `multi_purpose_cryogenic_storage_mk1_data.json` | `operational_properties.storage.type = "multi"` | ✅ But nested under `operational_properties`, NOT top-level `storage` |
| `inflatable_gas_storage_data.json` | `resource_management.storage.gas_m3.capacity` | ❌ **Completely different key path** |

#### From factories (`spec/factories/units/units.rb`)

| Factory/Trait | `unit_type` | `operational_data['storage']['type']` |
|---|---|---|
| `:gas_storage` trait | `"gas_storage"` | Not set (nil) |
| `:storage` trait | `"storage_unit"` | Not set (nil) |
| `:lox_tank` trait | `"lox_tank"` | `'liquid'` (but at top-level, not under `storage`) |
| `:methane_tank` trait | `"methane_tank"` | `'liquid'` (same issue) |
| `storage_unit` factory | `"storage_unit"` | `'general'` ✅ |

#### From specs (`spec/models/units/base_unit_spec.rb`)

| Variable | `unit_type` | `operational_data['storage']['type']` |
|---|---|---|
| `storage_tank` | `"storage_tank"` | `'general'` ✅ |
| `storage_unit` | `"storage"` | `'general'` ✅ |
| `lox_tank` | `"lox_tank"` | `'liquid'` ✅ |

#### From app code

| Location | `unit_type` check | Storage access pattern |
|---|---|---|
| `orbital_structure.rb:36` | `where(unit_type: 'storage')` | `.dig('storage', 'capacity')` |
| `base_craft.rb:438` | `where(unit_type: ['cargo_bay', 'storage_module'])` | — |
| `base_craft.rb:522` | `unit.unit_type == 'storage'` | — |
| `ai_base_building.rake:446` | `['Inflatable Pressure Tank', 'Inflatable Cryogenic Tank']` | `.dig('storage_capacity')` (different key!) |

### Why This Is a Design Question, Not a Bug Fix

The codebase has three competing approaches to identifying general storage:

1. **`storage_type == 'general'`** (the original buggy check) — only matches units that explicitly set type to 'general'
2. **`storage_type.nil?`** (my attempted fix) — matches units with no type constraint, but this is an assumption, not a confirmed pattern
3. **`unit_type` values** (`'storage'`, `'storage_unit'`, `'storage_tank'`) — used in app code but inconsistent

None of these is confirmed as THE correct mechanism. The stop condition applies:

> **DO NOT proceed beyond synthesis if:**
> - The identifying mechanism for general storage is unclear after reviewing BaseUnit and all unit subclasses

**It IS unclear.** This requires an architecture decision from Tracy on what makes a unit's storage "general-purpose" — possibly a proper `storage_type`/`category` field set consistently at creation, replacing the current patchwork of `unit_type` strings, nested paths, and missing keys.

### Actions Taken During Investigation

1. ✅ Reverted `inventory.rb` to original state (no half-confirmed guesses left)
2. ✅ Restored `can_store?` mocks in `shell_printing_service_spec.rb` (original workaround preserved)
3. ✅ This blocker documentation added to task file as permanent record
4. ❌ Did NOT commit `has_storage.rb` changes (same open question, same block)

### Next Steps (Requires Tracy's Input)

- Determine the canonical way to identify general-storage units
- Consider whether a proper `storage_type`/`category` field should be added to BaseUnit
- If a schema decision is made, this task can be unblocked and re-implemented with confidence
