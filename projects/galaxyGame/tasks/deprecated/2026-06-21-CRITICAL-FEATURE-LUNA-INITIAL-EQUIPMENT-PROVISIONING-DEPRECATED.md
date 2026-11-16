---
title: "Luna Initial Equipment Provisioning (DEPRECATED)"
status: deprecated
date_created: 2026-06-21
date_deprecated: 2026-07-03
severity: N/A
type: architecture
tags: [luna, equipment-provisioning, manifest, architecture-clarification]
---

# Luna Initial Equipment Provisioning

**STATUS: DEPRECATED** — This task was created based on a flawed architectural assumption. After investigation, the feature is **already implemented correctly** in the v2 setup via manifest-driven provisioning.

## Why This Was Deprecated

### Original Premise (Incorrect)
The task assumed Luna settlement spawned with **zero equipment** because:
- Mission profiles appeared to lack a `resources` section
- Manifest alone was insufficient to provision equipment

### Actual Architecture (Correct)
The established 3-file mission pattern proves equipment provisioning is fully designed:

1. **Manifest** (`lunar_precursor_manifest_v2_DRAFT.json`)
   - Defines `required_hardware` array with equipment and counts
   - Example: `{ "id": "solar_expansion_rig", "count": 1, "role": "Power Generation Expansion" }`
   - This IS the equipment specification — no additional `resources` section needed

2. **Profile** (`luna_settlement_profile_v1.json`)
   - Defines mission phases and task sequences
   - References phase files containing tasks to execute
   - **No `resources` section by design** — profiles define structure, not equipment

3. **Phases** (e.g., `phase_1_power_comms.json`)
   - Contain task definitions and effects
   - Phases execute against settlement after equipment arrives

### Current Implementation (`luna_mission.rake`)
**Lines 75-108**: `seed_inventory_from_manifest` lambda
- Reads `required_hardware` from DRAFT manifest
- Seeds `settlement.inventory.items` with equipment + metadata
- Rake executes before tasks, so settlement spawns fully provisioned

**Lines 133-180**: Legacy staging pattern
- Creates HLT craft at Cape Canaveral with equipment
- Transfers entire cargo to Luna settlement inventory
- Ensures HLT + contents arrive as single unit

### Validation (Reference Mission: PC-001)
`pc-001-atmospheric-production-rush` mission demonstrates the standard 3-file pattern:
- **Manifest**: Defines craft, inventory (supplies, consumables)
- **Profile**: Defines phases, success conditions, no `resources` section
- **Phases**: Reference task_list_file entries
- Result: Equipment provisioning handled entirely via manifest

## Key Finding
`TaskExecutionEngineV2` line 44 reads `@manifest["resources"]["initial_equipment"]`, but this code is **dead code**. The actual provisioning happens through the manifest's `required_hardware` array and the rake seed, not through a profile `resources` section.

## Lesson Learned
**Don't assume design patterns exist without examining actual code and multiple implementations.**

Equipment provisioning flow:
```
manifest["required_hardware"] 
  → lua_mission.rake seed_inventory_from_manifest
  → settlement.inventory.items populated
  → TaskExecutionEngineV2 deploys from inventory
```

No profile `resources` section was ever needed because the manifest already handles this completely.

---

**Archived for institutional memory.**
