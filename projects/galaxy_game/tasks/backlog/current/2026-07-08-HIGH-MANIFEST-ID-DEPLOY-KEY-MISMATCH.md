---
status: backlog
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-08
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current
  mv 2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md \
     /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (mv only, never cp)
Verify with: find /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks -name "2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md"
Only ONE result should exist. Paste this output before starting synthesis.

READ FIRST: Task file contains all prerequisites, gotchas, and verification steps.
CRITICAL: Save synthesis report as MD file to summaries/ BEFORE starting any work.
```

---

# MANIFEST HARDWARE ID ↔ DEPLOY LOOKUP KEY MISMATCH — CASCADING FAILURE FIX

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-08
**Last Updated**: 2026-07-08

---

## Context

During Profiles V2 canonical implementation, the rake task `luna_mission:execute` loaded all v2 paths correctly (profile, phases, tasks, manifest) but **9 of 13 tasks failed** due to a root cause: manifest hardware IDs contain `_mk1` suffixes that don't match deploy lookup keys. This cascades — no Planetary Umbilical Hub deployed → every task requiring PUH fails.

Additionally, the `robot_logistics` phase is SKIPPED because `execution_order` has `"robot_logistics_site_hardening"` but the profile/mission plan uses `"robot_logistics"`.

This task fixes both issues so all 17 v2 tasks execute without path/data errors.

---

## Problem Statement

### Root Cause: Manifest ID ↔ Deploy Key Mismatch

The manifest (`lunar_precursor_manifest_v2_DRAFT.json`) hardware IDs have `_mk1` suffixes, but deploy lookup keys strip them. The inventory seeding uses the raw manifest `id` as the key, so nothing matches.

| Manifest ID (in JSON) | Deploy Lookup Key (expected) | Match? |
|---|---|---|
| `comms_equipment_mk1` | `comms_equipment` | ❌ No match |
| `thermal_extraction_unit_mk1` | `thermal_extraction_unit` | ❌ No match |
| `planetary_umbilical_hub_mk1` | `planetary_umbilical_hub` | ❌ No match |
| `planetary_power_management_unit_mk1` | `planetary_power_management_unit` | ❌ No match |
| `planetary_volatiles_extractor_mk1` | `planetary_volatiles_extractor_mk1` | ✅ Match (already correct) |
| `inflatable_cryo_tank` | `inflatable_cryogenic_tank` | ❌ No match (different name entirely) |

### Cascading Failures

Because PUH (`planetary_umbilical_hub`) is never deployed:
- `deploy_solar_rig` fails — cannot connect without PUH
- `deploy_pve_unit` fails — cannot connect without PUH
- `deploy_volatiles_storage` fails — tanks must connect to PUH
- `print_inflatable_tank_shells` fails — no cryogenic tank (never deployed)
- `deploy_gas_separator` fails — cannot register to bus without PUH

### Phase Name Mismatch

`execution_order` array has `"robot_logistics_site_hardening"` but the v2 profile/mission plan uses `"robot_logistics"`. No match in phase_index → SKIPPED.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/lib/tasks/luna_mission.rake` | Luna mission execution rake | Inventory seeding lambda (line ~68), phase_index builder (line ~285), execution_order array (line ~280) |
| `data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json` | V2 manifest data | `required_hardware[*].id` values |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/missions_v2/profiles/luna_base_profile_v2.json` | Confirms phase_ids use `"robot_logistics"` not `"robot_logistics_site_hardening"` |
| `galaxy_game/app/services/lookup/deploy_lookup_service.rb` | Understand how deploy keys are normalized (strip `_mk1`, suffixes) |
| `data/json-data/blueprints/units/propulsion/methane_rocket_engine_mk1_bp.json` | Reference for correct ID naming convention in blueprints |

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: Do NOT modify the manifest JSON — it's a data artifact that should reflect what was shipped
- ❌ Wrong: Change manifest IDs to remove `_mk1` suffixes
- ✅ Right: Normalize IDs during inventory seeding in the rake task (strip `_mk1`, map `inflatable_cryo_tank` → `inflatable_cryogenic_tank`)
- Why: The manifest is a historical record. Normalization belongs in the ingestion layer, not the data layer

⚠️ **GOTCHA 2**: Do NOT change deploy lookup keys — they match blueprint/operational data conventions
- ❌ Wrong: Change `deploy_lookup` to expect `_mk1` suffixes
- ✅ Right: Normalize manifest IDs to match existing deploy key conventions during seeding
- Why: Deploy keys are used across many systems (manufacturing, cost evaluation, etc.) and changing them would break other code

⚠️ **GOTCHA 3**: The `execution_order` array is hardcoded in the rake — it must match profile phase_ids exactly
- ❌ Wrong: Change the profile/mission plan phase_id
- ✅ Right: Update `execution_order` to use `"robot_logistics"` (matching the v2 profile)
- Why: Phase IDs are part of the v2 schema contract; the rake should adapt to the data, not vice versa

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template**:
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Manifest ID ↔ Deploy Key Mismatch — Cascading Failure Fix
**Status**: backlog → active
**Date**: 2026-07-08

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/lib/tasks/luna_mission.rake` | Fix inventory seeding normalization + phase_index builder | not started |
| `data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json` | Read-only reference for current manifest IDs | read only |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with mv (find output pasted in chat)
- ✅ Read TASK_TEMPLATE.md EXECUTOR section
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know deploy lookup key conventions from inspecting deploy_lookup_service.rb

### Expected Outcomes
All 17 v2 tasks execute. 4+ pass (those with correct inventory), 0 fail due to data/path issues. Remaining failures are only runtime/infrastructure sequence issues (not manifest or path problems).

### Critical Gotchas I Will Avoid
- ❌ Modifying the manifest JSON — instead ✅ Normalize during rake seeding
- ❌ Changing deploy lookup keys — instead ✅ Match existing conventions
- ❌ Changing profile phase_ids — instead ✅ Update execution_order to match

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current
mv 2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md \
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md
```

Then open the moved file and change: `status: backlog` → `status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks -name "2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/`.

---

### Step 1 — Inspect deploy lookup key normalization (before editing)

Understand how deploy keys are generated from unit names. This tells us what format to normalize manifest IDs to.

```bash
grep -n "normalized\|gsub.*downcase\|deploy_lookup" galaxy_game/app/services/lookup/deploy_lookup_service.rb | head -20
```

Also check the rake's existing inventory seeding lambda for any current normalization:

```bash
grep -n "unit_type\|metadata\|inventory.items.create" galaxy_game/lib/tasks/luna_mission.rake | head -15
```

Paste findings in chat before proceeding.

---

### Step 2 — Fix manifest ID normalization in inventory seeding (rake line ~68)

In the `seed_inventory_from_manifest` lambda, normalize manifest hardware IDs to match deploy lookup keys BEFORE creating inventory items.

**Current code** (approximate):
```ruby
(manifest["required_hardware"] || []).each do |hw|
  count = hw["count"].to_i
  next if count <= 0
  
  settlement.inventory.items.create!(
    name: hw["id"],  # ← This uses raw manifest ID with _mk1 suffix
    amount: count,
    owner: item_owner,
    metadata: { ... }
  )
end
```

**Fix**: Add normalization before the `create!` call:

```ruby
(manifest["required_hardware"] || []).each do |hw|
  count = hw["count"].to_i
  next if count <= 0
  
  # Normalize manifest ID to match deploy lookup key conventions.
  # Strip _mk1 suffix, map known aliases.
  raw_id = hw["id"].to_s
  normalized_id = raw_id
    .sub(/_mk\d+$/, "")           # strip _mk1, _mk2, etc.
    .gsub("inflatable_cryo_tank", "inflatable_cryogenic_tank")  # known alias
  
  settlement.inventory.items.create!(
    name: normalized_id,
    amount: count,
    owner: item_owner,
    metadata: {
      "unit_type" => normalized_id,
      "role" => hw["role"],
      "task_affinity" => hw["task_affinity"],
      "original_manifest_id" => raw_id  # preserve for audit trail
    }
  )
end
```

Apply the same normalization to the HLT staging flow (the `heavy_lift.inventory.items.create!` block ~line 175).

---

### Step 3 — Fix execution_order phase name mismatch (rake line ~280)

Update the hardcoded `execution_order` array to match v2 profile phase_ids:

**Current**:
```ruby
execution_order = ["power_comms", "isru_deployment", "gas_processing", "robot_logistics_site_hardening"]
```

**Fix**:
```ruby
execution_order = ["power_comms", "isru_deployment", "gas_processing", "robot_logistics"]
```

---

### Step 4 — Verify no regressions

Run the rake task and confirm all v2 paths resolve correctly:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=development bundle exec rake luna_mission:execute 2>&1 | tail -60'
```

**Expected**: 
- All 4 phases load from v2 files (no SKIPPED due to path issues)
- All 13 tasks found and executed via v2 paths
- Inventory seeding uses normalized IDs — PUH, comms_equipment, thermal_extraction_unit all present in inventory
- robot_logistics phase executes (not SKIPPED)
- Remaining failures are only runtime/infrastructure sequence issues (not manifest or path problems)

Paste full output in chat.

---

## Acceptance Criteria
- [ ] Manifest hardware IDs normalized during seeding (`_mk1` stripped, `inflatable_cryo_tank` → `inflatable_cryogenic_tank`)
- [ ] Original manifest ID preserved in metadata for audit trail
- [ ] `execution_order` uses `"robot_logistics"` matching v2 profile phase_id
- [ ] All 4 phases load from v2 files (no SKIPPED due to path/name issues)
- [ ] robot_logistics phase executes (not SKIPPED)
- [ ] PUH, comms_equipment, thermal_extraction_unit present in settlement inventory after seeding
- [ ] No regressions in related specs

---

## Stop Conditions — escalate to user immediately if:
- Deploy lookup key normalization differs from what I find in deploy_lookup_service.rb
- Manifest has additional ID formats not covered by the normalization rules above
- Fix causes new failures in specs I did not touch
- Same failure persists after two attempts

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/lib/tasks/luna_mission.rake
git commit -m "fix(luna_mission): normalize manifest hardware IDs during inventory seeding and fix robot_logistics phase name

- Strip _mk1 suffix from manifest IDs to match deploy lookup keys
- Map inflatable_cryo_tank → inflatable_cryogenic_tank alias
- Preserve original_manifest_id in metadata for audit trail
- Update execution_order to use robot_logistics (matching v2 profile)"
```

**Task file move on completion:**
```bash
mv /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md \
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/completed/2026-07/2026-07-08-HIGH-MANIFEST-ID-DEPLOY-KEY-MISMATCH.md
git add -A
git commit -m "chore: move manifest ID mismatch task to completed/"
```

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final rake result**: X tasks executed, Y failures (list remaining failures)

### What was changed
- `galaxy_game/lib/tasks/luna_mission.rake` — inventory seeding normalization + execution_order fix

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
