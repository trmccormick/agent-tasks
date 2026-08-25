---
status: backlog
priority: MEDIUM
type: data
system_domain: ASSET_PIPELINE
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-24-MEDIUM-DATA-FIRST-COMPONENT-BLUEPRINT-LUNAR-IBEAM-MK1.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/current/2026-08-24-MEDIUM-DATA-FIRST-COMPONENT-BLUEPRINT-LUNAR-IBEAM-MK1.md
projects/galaxy_game/tasks/active/2026-08-24-MEDIUM-DATA-FIRST-COMPONENT-BLUEPRINT-LUNAR-IBEAM-MK1.md
Then open the moved file and change: status: backlog → status: active
Paste the find command output confirming exactly one result before proceeding:
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks
-name "2026-08-24-MEDIUM-DATA-FIRST-COMPONENT-BLUEPRINT-LUNAR-IBEAM-MK1.md"

READ FIRST (after Step 0): Task file contains full context below.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: 2026-08-24-DATA-IBEAM-MK1-BLUEPRINT.md
Chat is for questions only — never paste synthesis into chat.


---

# TASK: Create first real component_blueprint.json instance — Lunar 3D-Printed I-Beam Mk1

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: data
**Created**: 2026-08-24
**Last Updated**: 2026-08-24

---

## Context

A ChatGPT design session (2026-08-23/24) confirmed `component_blueprint_v1.4` is the current
canonical schema for reusable manufactured components (verified via `metadata.template_compliance`),
but a filesystem audit the same night confirmed ZERO actual `component_blueprint.json` instances
exist anywhere in the codebase yet — only the schema/template.

The Lunar 3D-Printed I-Beam Mk1 is the strongest first candidate: it's confirmed as a genuine
shared-catalog component (reused across LEO Depot, L1 Depot, Shipyard, Tug, Cycler — not
cycler-specific), it's 100% Lunar-manufactured (no provenance-variant ambiguity to resolve),
and production-quality art already exists on disk at
`data/images/catalog/components/structural/3d_printed_ibeam_mk1.png` (part of a Mk1-Mk4
progression set, all four already saved).

This is a data-authoring task, not code and not image generation — writing the actual JSON
instance that will let the eventual Asset Compiler have something real to read.

**Relevant Architecture Docs** — read before starting:
- The real `component_blueprint_v1.4` schema/template (confirm exact file location first —
  audit found the schema referenced by `template_compliance` but the actual canonical template
  FILE location was not itself pinned down in that audit; find it, don't assume a path)
- `ASSET_PROMPT_COMPILER_CONTRACT.md` (drafted 2026-08-23/24, in `docs/reference/asset-generation/`
  per Qwen's summary — verify exact path) — the I-beam was used as its worked example
- `base_cycler_bp.json` — for reference on how a craft's `repair.materials_needed_for_repair`
  and `blueprint_data.materials` reference `ibeam` by id (confirms the expected id string)

---

## Problem Statement

**Current state**: No `component_blueprint.json` instance exists for the I-beam or any other
component. `base_cycler_bp.json` already references an `ibeam` material/component id in its
repair and build material lists, but nothing defines what that id actually IS as a manufactured
component (materials required to print it, production time, printer compatibility, variants
for Mk1-Mk4, physical properties).

**Required output**: A real `component_blueprint.json` instance for the Lunar I-Beam, conforming
exactly to the `component_blueprint_v1.4` schema shown below (do not invent fields not in this
schema, do not omit required fields):

```json
{
  "template": "component_blueprint",
  "id": "",
  "name": "",
  "description": "",
  "category": "",
  "subcategory": "",
  "blueprint_data": {
    "material_requirements": [ /* depleted_regolith as primary input — confirm exact
      material id against data/resources/materials/, do not guess the filename */ ],
    "production_time_hours": 0,
    "waste_products": [],
    "printer_compatibility": { "categories": [], "material_types": [] },
    "output": {
      "weight_kg": 0, "volume_m3": 0, "material_efficiency": 1.0,
      "quality": { "structural_integrity": 1.0, "precision": 1.0, "durability": 1.0 }
    },
    "required_tools": [], "required_skills": [], "required_technology": []
  },
  "variants": [
    /* Mk1/Mk2/Mk3/Mk4 as manufacturing variants — NOT deployment contexts.
       Each variant = different material_overrides/quality_modifiers/production_time_modifier
       reflecting the confirmed visual progression (Mk1 rough/porous → Mk4 smooth/dense) */
  ],
  "physical_properties": {
    "mass_kg": 0, "volume_m3": 0,
    "dimensions": { "length_m": 0, "width_m": 0, "height_m": 0 },
    "durability_rating": "", "environmental_resistance": ""
  },
  "metadata": {
    "version": "1.4", "last_updated": "", "type": "blueprint",
    "category": "", "template_compliance": "component_blueprint_v1.4",
    "changelog": ["v1.0: initial component_blueprint instance for Lunar I-Beam Mk1-Mk4"]
  }
}
```

**Do NOT**:
- Add any "appearance"/visual field to this file — visual info belongs in the (not-yet-created)
  Visual Definition, per ChatGPT's explicit 2026-08-24 architecture decision
- Add `operational_data` — the I-beam is a component, not an active unit/craft, per the confirmed
  hierarchy (RESOURCE → COMPONENT → STRUCTURE/UNIT/CRAFT); components never get operational_data
- Guess the `depleted_regolith` material id — verify it against the real file in
  `data/resources/materials/` before referencing it

---

## Files Involved

### Primary Files — likely relevant, confirm before editing
| File | Purpose | Key Method/Section |
|---|---|---|
| `[FILL IN]` — new file, exact path TBD | The new component_blueprint.json instance | whole file |
| `[FILL IN]` — component_blueprint_v1.4 template's actual file location | Schema source of truth | whole file |
| `data/resources/materials/[FILL IN]` | Confirm exact depleted_regolith material id | `[FILL IN]` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `base_cycler_bp.json` | Confirms how `ibeam` id is referenced by consuming blueprints — the new component's `id` field must match what other files already expect |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
[Standard — see dispatch interface above]

### Step 1 — Locate the canonical schema file and confirm the correct output directory
Find where `component_blueprint_v1.4`'s actual template file lives, and where instance files
of this type are expected to be saved (likely alongside other blueprint instances — check
existing blueprint directory conventions, e.g. propulsion blueprints live in a `propulsion/`
directory per last night's audit; component blueprints likely have an equivalent).

### Step 2 — Confirm the material id
Find the real `depleted_regolith` (or equivalent) material JSON in `data/resources/materials/`
and use its exact id — do not invent or guess a name.

### Step 3 — Write the instance
Fill the schema above with real values, deriving physical dimensions/mass from a reasonable
structural I-beam scale (cross-reference `base_cycler_bp.json`'s repair quantities — 120 ibeam
units per repair cycle — as a sanity check on scale, not a hard constraint). Define 4 variants
(Mk1-Mk4) reflecting the confirmed visual/quality progression.

### Step 4 — Synthesis Report

SYNTHESIS REPORT
Schema file location: [path]
Instance file location: [path]
Material id used: [id, confirmed against real file]
Variants defined: [Mk1-Mk4 summary]
Cross-check against base_cycler_bp.json's "ibeam" reference: [confirmed matching id / mismatch found]
RISK: [anything uncertain]


---

## Acceptance Criteria
- [ ] Real `component_blueprint.json` instance created, conforming exactly to v1.4 schema
- [ ] No invented fields, no visual/appearance data, no operational_data
- [ ] Material id verified against real file, not guessed
- [ ] `id` field matches what `base_cycler_bp.json` already expects (`ibeam`) or discrepancy explicitly flagged
- [ ] 4 variants (Mk1-Mk4) defined as manufacturing variants, not deployment contexts

---

## Stop Conditions — escalate to user immediately if:
- The canonical component_blueprint_v1.4 template file cannot be located
- `base_cycler_bp.json`'s `ibeam` reference doesn't match a sensible id for this new component
- No depleted_regolith (or equivalent) material exists in data/resources/materials/

---

## Dependencies
**Blocked by**: none
**Blocks**: eventual Visual Definition + Asset Compiler test run for the I-beam
**Related tasks**: `2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md` (completed) — this task's output is what the Contract will eventually compile against

---

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
*Filled in at end of session*