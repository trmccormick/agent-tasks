---
status: backlog
priority: MEDIUM
type: research-investigation
system_domain: Blueprint/Data Infrastructure
mvp_alignment: DATA_INTEGRITY
local_worker_safe: true
created: 2026-08-16
---

# Task: Investigate CNT Fabricator Naming Collision

## Background

NEEDS_REVIEW #5 (agent-tasks/NEEDS_REVIEW.md): The rename audit surfaced two separate CNT fabricator blueprint families in different folders:
- `industrial/cnt_fabricator_unit_mk1_bp.json` (part of the v1→mk1 rename batch)
- `production/fabricators/cnt_fabricator_mk1_bp.json` (pre-existing, has mk1/mk2/mk3 progression with real production data)

Near-identical names, different directories — unclear if these represent the same unit with two deployment profiles, true duplicates, or two genuinely distinct things that happen to share a name.

## Scope

Side-by-side comparison of both blueprint families to determine:
1. Are they functionally identical (same capabilities, materials, costs)?
2. Are they intentionally distinct (different deployment contexts, different specs)?
3. Is one a stale duplicate that should be removed?
4. If distinct, do the names need disambiguation to prevent future confusion?

## Steps

### Step 1: Locate both files
```bash
find galaxy_game/data/json-data/blueprints -name "*cnt*fabricator*" | sort
```

### Step 2: Side-by-side comparison
For each file, extract and compare:
- `display_name` / `designation`
- `required_materials` (quantities and types)
- `production_time` / `build_cost`
- `output_resources` / capabilities
- `category` / `subcategory`
- Any deployment-specific fields

### Step 3: Cross-reference check
Search the codebase for references to each file's ID:
```bash
grep -r "cnt_fabricator" galaxy_game/app/ galaxy_game/spec/ --include="*.rb" | grep -v "_spec.rb:"
```
Check if both are used, or if one is dead code.

### Step 4: Decision and recommendation
Based on comparison:
- **If identical**: Recommend removing the stale one (likely the industrial/ one from the rename batch)
- **If distinct but similar names**: Recommend renaming one for clarity (e.g., `cnt_fabricator_industrial_mk1_bp.json` vs `cnt_fabricator_production_mk1_bp.json`)
- **If intentionally distinct with clear purpose**: Document the distinction in a note

## Stop Conditions
- Both files compared side-by-side
- Codebase references documented
- Recommendation made (remove/renamedocument)
- No changes committed — this is research only

## Expected Deliverables
1. Comparison table (markdown, saved to `summaries/`)
2. Recommendation with rationale
3. If rename needed: proposed new names for both files
