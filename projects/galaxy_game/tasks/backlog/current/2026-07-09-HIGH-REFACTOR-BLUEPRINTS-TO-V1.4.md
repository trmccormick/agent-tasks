---
status: backlog
priority: HIGH
type: refactor
system_domain: UNITS
mvp_alignment: OTHER
local_worker_safe: true
date_created: 2026-07-09
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md \
         projects/galaxy_game/tasks/active/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md
  If git mv fails: mv the file then git add the destination path.
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
  -name "2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md"
Only ONE result should exist. Paste this output before starting synthesis.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and implementation steps.
CRITICAL: Save synthesis report as MD file to summaries/ BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md

CRITICAL: data/json-data/ is gitignored — do NOT git add or force-add JSON files.
  JSON changes are local + Time Machine backed only. Only Ruby files get committed.
  NO GIT COMMITS without explicit human approval in chat.
```

---

# TASK: Refactor Blueprints to Current Template Version + Add Missing Mass Data

**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-07-09

---

## Context

The lunar precursor manifest weight analysis revealed that several blueprints are missing
critical data (`required_materials`, `empty_mass_kg` in blueprint_data, `payload_capacity_kg`)
or use outdated formats (`materials` instead of `required_materials`). This task brings all
mission-relevant blueprints up to the most current available template standard and adds the
missing mass data needed for manifest validation against HLT transport capacity.

**Reference templates location**: `data/json-data/templates/`
The agent must inspect this directory first to determine the most current template version
for each blueprint type (unit vs craft) before editing any files.

---

## Step 0.5 — Inspect Available Templates Before Touching Any Files

```bash
find data/json-data/templates -name "*.json" | sort
```

For each template found:
- Read its `metadata.version` and `metadata.template_compliance` fields
- Identify the most current unit blueprint template
- Identify the most current craft blueprint template (may differ from unit)
- Paste the template list and versions in chat before proceeding

This determines what `template_compliance` value to set on each file.

---

## Blueprints That Need Fixes

### 1. Planetary Power Management Unit (PPMU)
**File**: `data/json-data/blueprints/units/infrastructure/planetary_power_management_unit_bp.json`

**Current state**:
- `physical_properties.empty_mass_kg: 400` at top level (outside blueprint_data)
- `blueprint_data.required_materials` is empty (0 items)
- No `connection_schema` block

**Fix needed**:
- Move `empty_mass_kg: 400` into `blueprint_data.physical_properties.empty_mass_kg`
- Populate `blueprint_data.required_materials` using the existing top-level
  `required_materials` data (power_converter, energy_storage_unit, etc.) — convert to
  standard `{amount, unit}` format
- Add `connection_schema.mounting_slots` for power bus connections (at least 2 slots)
- Set `metadata.template_compliance` to the most current unit blueprint version
  (from Step 0.5)

### 2. Planetary Umbilical Hub (PUH)
**File**: `data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json`

**Current state**:
- `physical_properties.empty_mass_kg: 300` at top level (outside blueprint_data)
- `blueprint_data.required_materials` is empty (0 items)
- No `connection_schema` block

**Fix needed**:
- Move `empty_mass_kg: 300` into `blueprint_data.physical_properties.empty_mass_kg`
- Populate `blueprint_data.required_materials` using existing top-level
  `required_materials` (advanced_composites:50kg, high_conductivity_cables, etc.)
- Add `connection_schema.mounting_slots` for power/resource transfer connections
  (at least 4 slots)
- Set `metadata.template_compliance` to the most current unit blueprint version

### 3. Regolith Harvester Rover
**File**: `data/json-data/blueprints/crafts/ground/harvesting_rover_bp.json`

**Current state**:
- Uses `blueprint_data.materials` (old format): aluminum:150kg, electronics:60kg,
  steel:120kg = 330kg total
- `physical_properties.empty_mass_kg: 820` at top level (outside blueprint_data)
- No `connection_schema` block

**Fix needed**:
- Rename `blueprint_data.materials` → `blueprint_data.required_materials` (same data,
  just rename the key)
- Move `empty_mass_kg: 820` into `blueprint_data.physical_properties.empty_mass_kg`
- Add `connection_schema.mounting_slots` for harvesting equipment (at least 1 slot)
- Set `metadata.template_compliance` to the most current **craft** blueprint version
  (from Step 0.5 — may differ from unit blueprint version)

### 4. Heavy Lander Transport (HLT)
**File**: `data/json-data/blueprints/crafts/space/landers/heavy_lander_bp.json`

**Current state**:
- Uses `blueprint_data.materials` (old format)
- No `payload_capacity_kg` field — **blocking manifest validation**
- No `connection_schema` block

**Fix needed**:
- Add `payload_capacity_kg: 30000` to blueprint_data (manifest total is ~24,480 kg;
  30,000 kg provides adequate margin)
- Convert `blueprint_data.materials` → `blueprint_data.required_materials`
- Add `connection_schema.mounting_slots` for cargo bay connections
- Set `metadata.template_compliance` to the most current craft blueprint version

---

## Implementation Steps

### Step 1 — Read each blueprint before editing

For each of the 4 files, read the full current content before making any changes.
Paste the key fields (metadata.version, physical_properties, blueprint_data keys,
connection_schema if present) in your synthesis report.

### Step 2 — Apply fixes to all 4 files

Apply changes per the blueprint descriptions above. Follow the field structure from the
most current template found in Step 0.5 exactly. Do not invent new fields.

### Step 3 — Validate all 4 blueprints

```bash
python3 -c "
import json
files = [
  'data/json-data/blueprints/units/infrastructure/planetary_power_management_unit_bp.json',
  'data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json',
  'data/json-data/blueprints/crafts/ground/harvesting_rover_bp.json',
  'data/json-data/blueprints/crafts/space/landers/heavy_lander_bp.json'
]
for f in files:
    json.load(open(f))
    print(f'✅ {f}')
print('All valid')
"
```

Paste output in chat before any commit.

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: data/json-data/ is gitignored — JSON files are local + Time Machine only
- ❌ Never: `git add -f data/...` or `git add data/...`
- ✅ Right: JSON changes stay local. Only commit Ruby files if any were changed.
- These 4 blueprints are JSON data — NO COMMIT needed for this task.

⚠️ **GOTCHA 2**: Check template versions from disk, don't assume
- Craft blueprint templates may have a different version than unit blueprint templates
- Step 0.5 is mandatory — do not skip it

⚠️ **GOTCHA 3**: Do not estimate material data that doesn't exist
- PPMU and PUH have existing top-level required_materials — use those values
- Do not invent material amounts that aren't already in the file
- If required_materials data is truly absent from a file, flag it as a stop condition

⚠️ **GOTCHA 4**: No Ruby code changes in this task
- This task is JSON data only. If any Ruby changes are needed, flag and stop.
- No RSpec run needed.

---

## Acceptance Criteria
- [ ] All 4 blueprints have `blueprint_data.physical_properties.empty_mass_kg` populated
- [ ] All 4 blueprints have `blueprint_data.required_materials` with material data (not empty)
- [ ] All 4 blueprints have `connection_schema.mounting_slots` block
- [ ] HLT has `payload_capacity_kg: 30000` in blueprint_data
- [ ] Rover uses `required_materials` format (not old `materials` format)
- [ ] All JSON files validate without errors
- [ ] `metadata.template_compliance` updated to most current version on all 4 files

---

## Stop Conditions — escalate immediately if:
- Required material data for PPMU or PUH is absent (not just misplaced)
- A craft blueprint template does not exist in templates/ — flag which version to use
- Any blueprint has consumers in Ruby code that would break from structural changes
  (grep for the blueprint id before editing)

---

## Commit Instructions

These are JSON data files — no git commit needed for the blueprint changes.
Only commit the task file lifecycle moves in agent-tasks.

**Task file move on completion (agent-tasks repo only):**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md
git add -A
git commit -m "chore: move blueprint refactor task to completed/2026-07/"
git push origin main
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Luna precursor manifest validation against HLT transport capacity
**Related**: `data/json-data/missions_v2/guides/luna_precursor_mission_focus.md`

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Templates found in Step 0.5**: [list template files and versions]
**template_compliance set to**: [unit version] for units, [craft version] for crafts

### What was changed
- [file] — [description]

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[List only — do not create files]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [template versions applied] | [next action needed]
