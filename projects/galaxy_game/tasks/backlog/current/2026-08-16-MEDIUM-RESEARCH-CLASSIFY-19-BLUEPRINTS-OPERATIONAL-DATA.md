---
status: backlog
priority: MEDIUM
type: research-classification
system_domain: Blueprint/Data Infrastructure
mvp_alignment: TEST_RELIABILITY
local_worker_safe: true
created: 2026-08-16
---

# Task: Classify 19 Renamed Blueprints — Operational Data Requirement

## Background

NEEDS_REVIEW #4 (agent-tasks/NEEDS_REVIEW.md): The v1→mk1 rename audit found 19 blueprints across propulsion, sensors, electronics, specialized, storage, industrial, mechanical, life_support, infrastructure, and power_generation categories. NONE have a matching operational_data file.

Per Tracy's rule: active/deployable units need operational data; components used in construction of other things don't.

## Scope

For each of the 19 blueprints, classify as:
- **Active deployable unit** → needs operational data written (new task)
- **Component/subunit** → no operational data needed (document decision)

## Steps

### Step 1: Inventory all 19 blueprints
```bash
find galaxy_game/data/json-data/blueprints -name "*_mk1_bp.json" | sort
```
List each file with its category and display name.

### Step 2: Classification pass
For each blueprint, determine:
- Is this an active deployable unit (harvester, transport, habitat, power plant, etc.)?
- Or is this a component/subunit (clamp, connector, module, processor)?
- Does it appear in any `required_materials` or `component` references of other blueprints?

### Step 3: Document decisions
Create a classification table:

| Blueprint ID | Category | Display Name | Classification | Rationale |
|-------------|----------|--------------|----------------|-----------|
| ... | ... | ... | active/component | ... |

### Step 4: File follow-up tasks (if any)
For blueprints classified as "active" — file a separate task for operational data writing. Do NOT write operational data in this task; just identify what's needed.

## Stop Conditions
- All 19 blueprints classified with rationale
- No operational data written yet (that's a follow-up task)
- Decision documented in a summary file

## Expected Deliverables
1. Classification table (markdown, saved to `summaries/`)
2. List of blueprints needing operational data (for follow-up task)
3. Task file for operational data writing (if any active units found)
