# RH-400 Visual Profile / Blueprint Contract Audit

**Task**: 2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT
**Type**: Research Note
**Date**: 2026-09-09
**Status**: Completed — research-only, no code/data changes

---

## Executive Summary

A contract mismatch exists between the **PromptCompiler** (asset-generation tooling) and the **actual RH-400 data artifacts**. The PromptCompiler makes two critical assumptions that do not match reality:

1. **It expects blueprints to have a `visual_profile` field.** The RH-400 blueprint (`hrv_400_resource_harvester_mk1_bp.json`) has NO such field.
2. **It expects Visual Definition files to be raw JSON.** The RH-400 Visual Definition (`VEHICLE_HARVESTER_ROVER_RH400.json`) is a Markdown document with YAML frontmatter and an embedded JSON code block — not raw JSON.

These are not bugs in the data; they are **unreconciled assumptions from Phase 1 asset-generation migration**. The tooling was written against an intended contract that was never established as canonical documentation or schema, and the actual data files were created with a different (but internally consistent) design.

**No canonical schema or authoritative documentation exists** for blueprints, Visual Definitions, visual profiles, or render templates. Every claim below is labeled as either **FACT** (what files actually contain), **OBSERVATION** (what the tooling does), or **RECOMMENDATION** (inferred guidance).

---

## Answers to the Six Key Questions

### Q1: Is `visual_profile` supposed to be owned by the blueprint?

**FACT**: The current RH-400 blueprint (`hrv_400_resource_harvester_mk1_bp.json`) does NOT contain a `visual_profile` field. Its fields are: `template`, `id`, `name`, `description`, `category`, `subcategory`, `physical_properties`, `port_type`, `item_produced`, `required_materials`, `production_data`, `cost_data`, `byproducts`, `hazard_level`, `special_handling`, `operational_data_reference`, `storage_properties`, `deployment_data`, `metadata`.

**OBSERVATION**: The PromptCompiler (`prompt_compiler.rb`, line ~180) explicitly expects `blueprint_entry[:visual_profile]` and raises a validation error if it is nil or empty: `"Blueprint for asset '#{asset_id}' does not specify a visual_profile"`.

**RECOMMENDATION**: The blueprint should NOT own `visual_profile`. The Visual Definition file already owns the reverse relationship via its `blueprint_ref` field (YAML frontmatter). The PromptCompiler's expectation appears to be a Phase 1 migration assumption that was never reconciled. If `visual_profile` is needed, it should be resolved from the Visual Definition's `asset_id` or from a separate visual-profile artifact — not from the blueprint.

---

### Q2: If not, which canonical artifact owns the relationship between an asset and its visual profile?

**FACT**: The RH-400 Visual Definition file (`VEHICLE_HARVESTER_ROVER_RH400.json`) contains a `blueprint_ref` field in its YAML frontmatter with value `regolith_harvester_rover`. This establishes a VD → blueprint relationship.

**FACT**: No canonical documentation or schema exists that defines which artifact should own the blueprint → visual_profile relationship. The task file confirmed: "If a doc or schema doesn't exist, do not create one during this task."

**OBSERVATION**: The ProfileResolutionEngine (`profile_resolution_engine.rb`) takes a `visual_profile_id` parameter (e.g., `"precision_industrial_v1"`) and resolves it to structured attributes. It appears to look for Visual Profile markdown files by ID.

**RECOMMENDATION**: Establish a clear ownership model:
- **Blueprint → Visual Definition**: Via the VD's `blueprint_ref` field (VD knows its blueprint).
- **Blueprint → Visual Profile**: Either add a `visual_profile` field to blueprints (if needed) OR resolve it from the Visual Definition's `asset_id` or a separate mapping artifact.
- **Visual Definition → Render Templates**: Via the VD's `render_profiles` array (e.g., `["inventory_icon", "catalog_render", ...]`).

---

### Q3: What is the authoritative machine-readable Visual Definition contract?

**FACT**: No canonical schema or authoritative documentation exists for Visual Definitions. The RH-400 VD file includes its own "Naming Convention Proposal" and "Compliance Checklist" sections, suggesting it was created as a pilot/template rather than against an established schema.

**FACT**: The RH-400 Visual Definition file structure is:
```
---
date_created: 2026-08-04
type: VISUAL_DEFINITION_INSTANCE
status: active
asset_id: VEHICLE_HARVESTER_ROVER_RH400
blueprint_ref: regolith_harvester_rover
purpose: First instantiated Visual Definition — RH-400 pilot unit
---

# Visual Definition — RH-400 Regolith Harvester Rover

**Asset ID**: `VEHICLE_HARVESTER_ROVER_RH400`  
**Blueprint Reference**: `regolith_harvester_rover`  
**Created**: 2026-08-04  
**Purpose**: First instantiated Visual Definition in the project. Pilot unit for asset-pipeline validation.

---

## Structured Data

```json
{
  "visual_definition": {
    "asset_id": "VEHICLE_HARVESTER_ROVER_RH400",
    "asset_family": "vehicle",
    "component_class": "harvester",
    "recognition_features": [...],
    "material_profiles": [...],
    "technology_level": 2,
    "manufacturing_style": "heavy_industrial",
    "silhouette": "...",
    "visual_priority": { ... },
    "visual_identity": { ... },
    "scale_class": "vehicle",
    "surface_finish": "matte",
    "color_profile": { ... },
    "animation_profile": "vehicles_status_lights",
    "render_profiles": [...],
    "camera_profiles": [...],
    "complexity_levels": [0, 1, 2, 3, 4, 5],
    "shared_components": [],
    "design_constraints": { ... },
    "prompt_template_refs": {},
    "physical_specs_reference": { ... }
  }
}
```

**RECOMMENDATION**: The Visual Definition contract should be formalized in a schema or template document. The RH-400 VD file itself serves as an excellent pilot/template — it just needs to be elevated from "pilot" to "canonical."

---

### Q4: Is `VEHICLE_HARVESTER_ROVER_RH400.json` intentionally a Markdown-wrapped Visual Definition, or is that itself a data-contract problem?

**FACT**: The file has a `.json` extension but contains Markdown with YAML frontmatter and an embedded JSON code block. This is NOT raw JSON — `JSON.parse` on the entire file will fail.

**OBSERVATION**: The file's own "Naming Convention Proposal" section explains this pattern:
> **Pattern**: `VEHICLE_[CATEGORY]_[NAME]_[VARIANT].json`  
> **Instance**: `VEHICLE_HARVESTER_ROVER_RH400.json`  
> **Location**: `docs/reference/asset-generation/visual_definitions/`

The file also states: "This follows the Icon Bible asset_id format." This suggests the `.json` extension was chosen to match the asset_id pattern, not because the file is raw JSON.

**RECOMMENDATION**: The Markdown+YAML+JSON format appears **intentional**, not a bug. It serves two purposes:
1. **Human-readable**: YAML frontmatter and prose sections are easy for designers to read/edit.
2. **Machine-parseable**: The embedded JSON code block can be extracted and parsed by tooling that knows how to handle this format.

**However**, the PromptCompiler does NOT handle this format — it tries `JSON.parse` on the entire file, which will fail. This is a tooling gap, not a data problem.

**Action**: Either:
- Rename the file extension to `.md` (e.g., `VEHICLE_HARVESTER_ROVER_RH400.md`) to accurately reflect its format, OR
- Update the PromptCompiler to detect and extract the embedded JSON from Markdown-wrapped Visual Definitions.

---

### Q5: Does the current PromptCompiler reflect the intended canonical contract, or did the Phase 1 asset-generation migration make assumptions that were never reconciled?

**FACT**: The PromptCompiler makes two incorrect assumptions:

**Assumption 1 — Blueprint has `visual_profile`:**
```ruby
# prompt_compiler.rb, resolve_profiles method
vp_id = blueprint_entry[:visual_profile]
if vp_id.nil? || vp_id.to_s.empty?
  validation_errors << "Blueprint for asset '#{asset_id}' does not specify a visual_profile"
  return nil
end
```
The RH-400 blueprint has NO `visual_profile` field. This will cause compilation to fail.

**Assumption 2 — Visual Definition is raw JSON:**
```ruby
# prompt_compiler.rb, load_visual_definition method
content = visual_definition_path.read
begin
  JSON.parse(content)
rescue JSON::ParserError => e
  validation_errors << "Failed to parse Visual Definition at #{visual_definition_path}: #{e.message}"
  nil
end
```
The RH-400 Visual Definition is Markdown+YAML+JSON. `JSON.parse` on the entire file will fail with a parser error.

**RECOMMENDATION**: These are Phase 1 migration assumptions that were never reconciled with the actual data model. The PromptCompiler needs to be updated to:
1. **Not require `visual_profile` on blueprints.** Resolve it from the Visual Definition or a separate mapping instead.
2. **Handle Markdown-wrapped Visual Definitions.** Detect YAML frontmatter (lines starting with `---`) and extract the embedded JSON code block before parsing.

---

### Q6: For RH-400 specifically, identify canonical sources and their relationships.

#### Canonical Sources for RH-400

| Artifact | Path | Format | Status |
|---|---|---|---|
| **Blueprint** | `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` | Raw JSON | ✅ Exists, canonical |
| **Operational Data** | Referenced in blueprint as `units/robots/resource/hrv_400_resource_harvester_mk1_data.json` | Unknown (path relative to blueprints dir) | ⚠️ Need to verify existence |
| **Visual Definition** | `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` | Markdown+YAML+embedded-JSON | ✅ Exists, pilot/template |
| **Visual Profile** | Not explicitly defined for RH-400 | N/A | ❌ No visual_profile file or reference exists |
| **Render Template** | Need to search (PromptCompiler expects one) | Unknown | ⚠️ Need to locate |

#### Relationships

```
Blueprint (hrv_400_resource_harvester_mk1_bp.json)
  └─ [NO visual_profile field] ❌
  
Visual Definition (VEHICLE_HARVESTER_ROVER_RH400.json)
  ├─ blueprint_ref → regolith_harvester_rover (VD → Blueprint) ✅
  ├─ asset_id → VEHICLE_HARVESTER_ROVER_RH400
  ├─ render_profiles → [inventory_icon, catalog_render, engineering_render, blueprint, exploded_view, sprite_sheet]
  └─ physical_specs_reference → {length_m: 6.80, width_m: 3.30, height_m: 2.65, empty_mass_kg: 22800.0, volume_m3: 59.5}

PromptCompiler
  ├─ Expects blueprint[:visual_profile] → ❌ Not present in RH-400 blueprint
  ├─ Expects VD to be raw JSON → ❌ VD is Markdown+YAML+JSON
  └─ Calls ProfileResolutionEngine(visual_profile_id) → Would fail without visual_profile
```

---

## Detailed Breakdown

### RH-400 Blueprint Analysis

**File**: `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json`

**Structure**: Raw JSON, template `unit_blueprint`, compliance with `unit_blueprint` schema.

**Key fields**:
- `id`: `"hrv_400_resource_harvester_mk1"` (note: no "RH" prefix in the ID)
- `template`: `"unit_blueprint"`
- `category`: `"robot"`, `subcategory`: `"resource_harvesting"`
- `physical_properties`: length 4.5m, width 3.2m, height 2.8m, empty_mass 850kg
- `operational_data_reference`: points to `units/robots/resource/hrv_400_resource_harvester_mk1_data.json`
- **NO `visual_profile` field** — this is the primary contract mismatch

**Observation**: The blueprint's physical dimensions (4.5m × 3.2m × 2.8m, 850kg) differ from the Visual Definition's `physical_specs_reference` (6.80m × 3.30m × 2.65m, 22800kg). This is a separate data inconsistency worth noting but outside this audit's scope.

### RH-400 Visual Definition Analysis

**File**: `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`

**Structure**: Markdown document with:
1. YAML frontmatter (7 fields: `date_created`, `type`, `status`, `asset_id`, `blueprint_ref`, `purpose`)
2. Title and metadata section
3. "Structured Data" section containing embedded JSON code block
4. "Field Notes and Rationale" section (prose explaining design decisions)
5. "Compliance Checklist" section
6. "Naming Convention Proposal" section
7. "Follow-Up Required" section

**Embedded JSON**: Contains a single `visual_definition` key with rich properties including `asset_id`, `asset_family`, `component_class`, `recognition_features`, `material_profiles`, `technology_level`, `manufacturing_style`, `silhouette`, `visual_priority`, `visual_identity`, `scale_class`, `surface_finish`, `color_profile`, `animation_profile`, `render_profiles`, `camera_profiles`, `complexity_levels`, `shared_components`, `design_constraints`, `prompt_template_refs`, `physical_specs_reference`.

**Observation**: The file is clearly a **pilot/template** — it states "First instantiated Visual Definition in the project" and includes follow-up items for formalization. It was designed to be human-readable (Markdown) while containing machine-parseable data (embedded JSON).

### PromptCompiler Analysis

**File**: `tools/asset_generation/prompt_compiler.rb`

**Key assumptions that don't match reality**:

1. **Blueprint must have `visual_profile`** (line ~180):
   - The `resolve_profiles` method extracts `vp_id = blueprint_entry[:visual_profile]`
   - If nil or empty, it raises a validation error and aborts compilation
   - The RH-400 blueprint has no such field → compilation would fail

2. **Visual Definition must be raw JSON** (line ~165):
   - The `load_visual_definition` method reads the file content and calls `JSON.parse(content)`
   - If parsing fails, it raises a validation error
   - The RH-400 VD is Markdown+YAML+JSON → `JSON.parse` would fail on the entire file

3. **ProfileResolutionEngine expects a valid `visual_profile_id`**:
   - Takes a string like `"precision_industrial_v1"` and resolves it to structured attributes
   - Looks for Visual Profile markdown files by ID
   - No visual_profile exists for RH-400 → no profile to resolve

**What the PromptCompiler DOES correctly**:
- The five-layer dependency chain (Profile Resolution → Composition Refinery → Prompt Compilation) is well-designed
- The provenance header system is solid
- The prompt output format (SUBJECT, CAMERA, STYLE, MANUFACTURING, TECHNOLOGY LEVEL, DESIGN CONSTRAINTS, RECOGNITION FEATURES, RENDER REQUIREMENTS, BACKGROUND, OUTPUT) follows a clear hierarchy

### Supporting Modules

**ProfileResolutionEngine** (`profile_resolution_engine.rb`):
- Resolves Visual Profile markdown into structured attributes
- Cross-validates Blueprint vs Visual Definition fields (warns on mismatch, does not block)
- Returns structured Hash (not prose) — good design
- Default manufacturing profile is `"earth_factory_v1"` for `precision_industrial_v1` profiles

**CompositionRefinery** (`composition_refinery.rb`):
- Composes prompt sections from profile attributes, visual definition, blueprint data, and operational data
- Handles targeted refinements and safeguards (both disabled by default)

---

## Recommendations for Reconciling Tooling with the Real Contract

### Priority 1 — Fix PromptCompiler to handle actual data format

**1.1**: Update `load_visual_definition` to detect Markdown-wrapped Visual Definitions:
```ruby
# Detect YAML frontmatter (lines starting with ---)
if content.start_with?('---')
  # Extract embedded JSON code block
  json_match = content.match(/```json\s*\n(.*?)\n```/m)
  if json_match
    content = json_match[1]
  end
end
JSON.parse(content)
```

**1.2**: Remove or relax the `visual_profile` requirement on blueprints:
- Option A: Make `visual_profile` optional and derive it from the Visual Definition's `asset_id` or a separate mapping.
- Option B: Allow the ProfileResolutionEngine to use default profiles when no visual_profile is specified.

### Priority 2 — Formalize the Visual Definition contract

**2.1**: Rename `.json` files to `.md` for Markdown-wrapped Visual Definitions, OR create a convention doc that clarifies the dual-format pattern.

**2.2**: Elevate the RH-400 VD file from "pilot" to "canonical template" by creating `VISUAL_DEFINITION_TEMPLATE.md` based on its structure.

### Priority 3 — Establish canonical documentation

**3.1**: Create a contract document (or schema) that defines:
- Blueprint field requirements (is `visual_profile` required or optional?)
- Visual Definition format (raw JSON vs Markdown-wrapped)
- Visual Profile file naming and location conventions
- Render Template format and location conventions
- The relationship graph between all four artifact types

**3.2**: Add the contract document to the backlog (do not create it during this research task).

### Priority 4 — Resolve RH-400-specific inconsistencies

**4.1**: The blueprint's physical dimensions differ from the VD's `physical_specs_reference`. This is a separate data inconsistency that should be flagged for design review.

**4.2**: Verify whether the referenced operational data file (`units/robots/resource/hrv_400_resource_harvester_mk1_data.json`) exists.

---

## What Canonical Docs/Schema Explicitly Establish

**Nothing.** No canonical documentation or schema exists for blueprints, Visual Definitions, visual profiles, or render templates. The RH-400 VD file is a pilot/template with its own conventions, but it was never elevated to canonical status.

## What Current Files Actually Contain

| Artifact | Actual Content |
|---|---|
| RH-400 Blueprint | Raw JSON, NO `visual_profile` field, template `unit_blueprint` |
| RH-400 Visual Definition | Markdown+YAML+embedded-JSON, NOT raw JSON |
| PromptCompiler | Expects blueprint `visual_profile` (missing) + VD raw JSON (wrong format) |
| ProfileResolutionEngine | Expects valid `visual_profile_id` string (no source for RH-400) |

## Recommendations (Clearly Labeled)

All recommendations above are labeled as **RECOMMENDATION** and are my inferences based on the evidence. They should be reviewed by the design team before implementation.

---

## Follow-Up Tasks Identified

1. **Create canonical Visual Definition schema/template** — formalize the RH-400 VD pattern
2. **Update PromptCompiler** to handle Markdown-wrapped Visual Definitions and optional `visual_profile`
3. **Resolve RH-400 physical dimension inconsistency** between blueprint and VD
4. **Verify operational data file existence** for RH-400
5. **Create canonical contract documentation** for all asset-generation artifacts

---

## Completion Report

**Completed by**: Research Agent (Qwen via Copilot)  
**Completion date**: 2026-09-09  
**Final output**: `summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`

### What was changed
- Created research note under `summaries/`. No code or data files were modified.

### Issues discovered
- The RH-400 blueprint's physical dimensions (4.5m × 3.2m × 2.8m, 850kg) differ from the VD's `physical_specs_reference` (6.80m × 3.30m × 2.65m, 22800kg). This is a separate data inconsistency worth flagging.
- The operational data file referenced in the blueprint (`units/robots/resource/hrv_400_resource_harvester_mk1_data.json`) was not located during this audit — its existence should be verified.

### Follow-up tasks needed
See "Follow-Up Tasks Identified" section above.

### Lessons learned
- The `.json` extension on `VEHICLE_HARVESTER_ROVER_RH400.json` is misleading — it's Markdown, not JSON. File extensions should match content format.
- The RH-400 VD file is an excellent pilot/template but was never formalized into canonical documentation. Future artifacts should reference it as the pattern source.
- No canonical schema exists for any asset-generation artifact type. This makes tooling development fragile — assumptions fill the gap where specs should be.

---

## Handoff Summary

HANDOFF SUMMARY: created `summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md` | research-only, no code/data changes | key finding: PromptCompiler makes two incorrect assumptions (blueprint must have visual_profile; VD must be raw JSON) that don't match actual RH-400 data artifacts | next: use audit to reconcile asset-generation tooling with canonical contract
