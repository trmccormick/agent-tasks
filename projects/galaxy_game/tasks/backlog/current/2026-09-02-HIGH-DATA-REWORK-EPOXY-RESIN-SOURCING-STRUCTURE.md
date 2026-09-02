---
status: backlog
priority: HIGH
type: data
system_domain: MANUFACTURING
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
created: 2026-09-02
# DISPATCH ORDERING — do not dispatch a task whose depends_on is not yet completed.
depends_on: []
blocks: []
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md \
         projects/galaxy_game/tasks/active/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md
  Update YAML: status: backlog → status: active
  Commit the move before writing any code.

STEP 1 — READ THE FULL TASK FILE. Do not skip any section.

STEP 2 — COMPLETE THE STATUS SYNTHESIS REPORT (see template below) and save it
  to the summaries folder BEFORE touching any files.

Do not proceed to implementation until the synthesis report is approved.
```

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, save a synthesis report as MD to the summaries folder covering:

1. **Current state of `epoxy_resin.json`** — paste the full `sourcing` and `production` blocks as they exist on disk right now
2. **Existing multi-source pattern** — list the 3 reference files you found (`regolith_composite.json`, `aerogel_insulation.json`, `regolith_shielding_layer.json`) and describe the per-location key structure they use
3. **Existing production block pattern** — list 2-3 reference files with real `production` blocks (e.g., `methane.json`, `precision_components.json`) and describe the field structure
4. **Your proposed new `sourcing` structure** — show the exact JSON you plan to write (not pseudocode)
5. **Your proposed new `production` structure** — show the exact JSON you plan to write
6. **What you will NOT change** — confirm you are only touching `sourcing` and `production` blocks, not `properties`, `cost_data`, `applications`, etc.
7. **Verification plan** — how you'll confirm the JSON still parses, the material still loads in the game, and `graphene_composite`'s dependency on `epoxy_resin` is unaffected

---

## Problem Statement

**Current behavior**: `epoxy_resin.json` has a flat single-source `sourcing` block:
```json
"sourcing": {
  "method": "earth_import",
  "origin": "Earth chemical supply chain",
  "logistics_note": "Available from Phase 1; no production infrastructure required."
}
```
and an entirely empty `production` block:
```json
"production": {
  "facility_type": "",
  "energy_kwh_per_kg": 0.0,
  "input_materials": [],
  "byproducts": []
}
```

**Expected behavior**: The `sourcing` block should use the same per-location key structure that other multi-source materials in the codebase already use (e.g., `regolith_composite.json` has `lunar`, `martian`, `earth` keys). The `production` block should have a real structure (even if currently Earth-only) so that other locations can be added later without a schema change.

**Why this matters**: The original requirement was that epoxy resin be "sourced generically — Earth-imported for now, but structured so other locations could eventually produce it too." The current flat-string structure does not meet this requirement.

---

## Files Involved

### Primary File — you will edit this
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/data/json-data/resources/materials/processed/polymers/epoxy_resin.json` | Rework `sourcing` and `production` blocks | `sourcing`, `production` |

### Reference Files — read but do NOT edit (existing patterns to follow)
| File | Why You Need It |
|---|---|
| `galaxy_game/data/json-data/resources/materials/processed/composites/regolith_composite.json` | Multi-source `sourcing` pattern: per-location keys (`lunar`, `martian`, `earth`) with `availability`, `extraction_method`, `in_situ` |
| `galaxy_game/data/json-data/resources/materials/building/functional/aerogel_insulation.json` | Single-location `sourcing` pattern (lunar only) with `manufacturing_difficulty` |
| `galaxy_game/data/json-data/resources/materials/building/functional/regolith_shielding_layer.json` | Multi-location `sourcing` (lunar + martian) |
| `galaxy_game/data/json-data/resources/materials/gases/compound/methane.json` | Real `production` block: `facility_type`, `energy_kwh_per_kg`, `input_materials` (array of `{id, amount}`), `byproducts` |
| `galaxy_game/data/json-data/resources/materials/components/precision_components.json` | Real `production` block: `facility_type: "machine_shop"`, `input_materials` as string array |
| `galaxy_game/data/json-data/resources/materials/byproducts/manufacturing_dust.json` | `production` block with `input_materials` as `{id, amount}` objects |

### Consumer File — read to verify no breakage (do NOT edit)
| File | Why You Need It |
|---|---|
| `galaxy_game/data/json-data/resources/materials/processed/composites/graphene_composite.json` | Lists `epoxy_resin` in its `input_materials` — verify this still resolves after the rework |

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: The `sourcing` block structure is NOT standardized across the codebase.
- `epoxy_resin.json` uses a flat `{method, origin, logistics_note}` structure
- `regolith_composite.json` uses per-location keys `{lunar: {...}, martian: {...}, earth: {...}}`
- `aerogel_insulation.json` uses a single-location key `{lunar: {...}}`
- **Do not invent a new pattern.** Follow the per-location key structure that the majority of multi-source materials use.
- **Do not break the flat-string pattern** if any code reads `sourcing.method` directly — grep for `sourcing.method` or `sourcing\["method"\]` in the codebase before changing the structure.

⚠️ **GOTCHA 2**: The `production` block is consumed by the manufacturing/ISRU pipeline.
- Check how `production.facility_type`, `production.input_materials`, and `production.energy_kwh_per_kg` are read in the codebase (grep for these field names in `app/services/` and `app/models/`).
- If the code expects `input_materials` to be an array of strings (e.g., `["steel"]`) vs. an array of objects (e.g., `[{"id": "steel", "amount": 1.0}]`), match the dominant pattern.
- An empty `production` block (`facility_type: ""`) may be treated as "not producible" by the game logic — verify this before filling it in.

⚠️ **GOTCHA 3**: `epoxy_resin` is a dependency of `graphene_composite`.
- `graphene_composite.json` lists `epoxy_resin` in its `input_materials`.
- After your changes, verify that `graphene_composite` can still resolve `epoxy_resin` as an input material.
- Do NOT change the `id`, `name`, or `dependency_of` fields in `epoxy_resin.json`.

⚠️ **GOTCHA 4**: The `cost_data.import_config` block is tied to the Earth-import sourcing model.
- If you restructure `sourcing` to per-location keys, the `import_config` in `cost_data` may need to move or be restructured.
- Check how `cost_data.import_config` is consumed before moving it.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above.)

### Step 1: Audit the codebase for `sourcing` and `production` consumption

Before changing any JSON, grep the codebase to understand how these fields are read:

```bash
# How is sourcing consumed?
grep -rn "sourcing" galaxy_game/app/ --include="*.rb" | grep -v "spec\|test"

# How is production consumed?
grep -rn "production\.\|production\[" galaxy_game/app/ --include="*.rb" | grep -v "spec\|test"

# Specifically check for sourcing.method (the flat-string pattern)
grep -rn "sourcing\.method\|sourcing\[:method\]\|sourcing\[.method.\]" galaxy_game/app/ --include="*.rb"

# Check how input_materials is read (string array vs object array)
grep -rn "input_materials" galaxy_game/app/ --include="*.rb" | head -20
```

**Record your findings in the synthesis report.** If `sourcing.method` is read directly anywhere, you must preserve backward compatibility (e.g., keep a `method` field alongside the per-location keys, or update the consumer code).

### Step 2: Rework the `sourcing` block

Replace the flat-string structure with the per-location key pattern. Based on the reference files, the target structure should look like:

```json
"sourcing": {
  "earth": {
    "availability": "high",
    "method": "import",
    "origin": "Earth chemical supply chain",
    "in_situ": false,
    "logistics_note": "Available from Phase 1; imported in bulk for later composite fabrication."
  },
  "lunar": {
    "availability": "none",
    "note": "No local production path yet. Future: polymer synthesis from local volatiles."
  },
  "martian": {
    "availability": "none",
    "note": "No local production path yet. Future: polymer synthesis from ISRU-derived feedstocks."
  }
}
```

**Key rules:**
- Use the same per-location key names as the reference files (`earth`, `lunar`, `martian`)
- Each location key has its own `availability`, `method`/`extraction_method`, `in_situ`
- Locations with no current path get `availability: "none"` and a `note` explaining the future path
- Do NOT remove the `logistics_note` content — move it into the `earth` key
- If Step 1 found that `sourcing.method` is read directly in code, add a top-level `method: "earth_import"` field for backward compatibility AND flag it in the synthesis report

### Step 3: Rework the `production` block

Replace the empty block with a real structure. Based on the reference files (`methane.json`, `precision_components.json`), the target structure should look like:

```json
"production": {
  "facility_type": "polymer_synthesis_unit",
  "energy_kwh_per_kg": 5.0,
  "input_materials": [
    { "id": "bisphenol_a", "amount": 0.6 },
    { "id": "epichlorohydrin", "amount": 0.4 }
  ],
  "byproducts": [
    { "id": "sodium_chloride", "amount": 0.1 }
  ],
  "notes": "Placeholder production path. Input materials and energy values are estimates pending chemistry validation. Facility type is provisional — may be consolidated with a general chemical processing unit."
}
```

**Key rules:**
- `facility_type` must be a non-empty string (the codebase treats `""` as "not producible")
- `input_materials` should use the `{id, amount}` object format (dominant pattern in `methane.json`, `manufacturing_dust.json`)
- `energy_kwh_per_kg` should be a non-zero estimate (flag as placeholder in `notes`)
- Add a `notes` field explaining this is a placeholder pending chemistry validation
- **Do NOT create new material files** for the input materials (`bisphenol_a`, `epichlorohydrin`) — they don't exist yet and creating them is out of scope. The `input_materials` entries are forward-looking references.
- If the codebase validates that `input_materials` IDs must resolve to existing materials, flag this in the synthesis report and use a structure that won't break validation (e.g., empty `input_materials` with a `notes` field explaining the future path)

### Step 4: Verify JSON validity and game compatibility

```bash
# JSON parses
python3 -c "import json; json.load(open('galaxy_game/data/json-data/resources/materials/processed/polymers/epoxy_resin.json')); print('OK')"

# graphene_composite still references epoxy_resin correctly
python3 -c "
import json
gc = json.load(open('galaxy_game/data/json-data/resources/materials/processed/composites/graphene_composite.json'))
inputs = gc.get('input_materials', [])
ids = [i['id'] if isinstance(i, dict) else i for i in inputs]
assert 'epoxy_resin' in ids, f'epoxy_resin not in graphene_composite inputs: {ids}'
print('OK — epoxy_resin still in graphene_composite inputs')
"

# If any RSpec specs reference epoxy_resin, run them
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec --pattern "spec/**/*epoxy*" 2>&1 | tail -20'
```

### Step 5: Synthesis Report (before committing anything)
Save to summaries folder. Do not commit until explicitly approved.

---

## Acceptance Criteria
- [ ] `epoxy_resin.json` `sourcing` block uses per-location key structure (matching `regolith_composite.json` pattern)
- [ ] `epoxy_resin.json` `production` block has non-empty `facility_type`, non-zero `energy_kwh_per_kg`, and structured `input_materials`
- [ ] `epoxy_resin.json` still parses as valid JSON
- [ ] `graphene_composite.json` still resolves `epoxy_resin` as an input material
- [ ] No code that reads `sourcing.method` or `production.*` fields is broken (verified via grep + spec run)
- [ ] Synthesis report saved to summaries folder
- [ ] No new material files created (input materials are forward-looking references only)
- [ ] `id`, `name`, `dependency_of`, `applications`, `cost_data` fields are unchanged

---

## Stop Conditions — escalate to user immediately if:
- Code reads `sourcing.method` directly and the per-location restructure would break it (requires a code change, not just a JSON change)
- The manufacturing pipeline validates that `input_materials` IDs must resolve to existing material files (the placeholder inputs would fail validation)
- `cost_data.import_config` is tightly coupled to the flat-string `sourcing` structure and can't be preserved
- Any change to `epoxy_resin.json` breaks `graphene_composite` or other downstream consumers
- The `facility_type` value needs to match an existing facility enum/constant in the codebase (check before choosing a name)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container.

```bash
git add galaxy_game/data/json-data/resources/materials/processed/polymers/epoxy_resin.json
git commit -m "refactor: rework epoxy_resin sourcing to per-location structure + add production path placeholder"
```

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md
git commit -m "chore: move epoxy resin sourcing rework to completed"
```

---

## Documentation
- [ ] If the per-location `sourcing` pattern is not yet documented in the material schema docs, add a note to the relevant schema doc
- [ ] Flag in DECISIONS.md: "epoxy_resin production inputs are placeholders pending chemistry validation"

---

## Dependencies
**Blocked by**: (none)
**Blocks**: (none)
**Related tasks**:
- `completed/2026-08/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` (original blueprint creation — completed, but sourcing structure was insufficient)
- `backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` (Fabrication Plant — DEFERRED Phase 11+; the `facility_type` chosen here may need to align with that blueprint when it's eventually dispatched)

---

## Completion Report
(To be filled in by the agent upon completion)

- **Date**:
- **Agent**:
- **Files changed**:
- **RSpec results**:
- **Synthesis report location**:
- **Notes / follow-ups**:
