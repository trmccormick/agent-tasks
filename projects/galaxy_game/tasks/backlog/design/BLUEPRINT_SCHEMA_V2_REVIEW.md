---
date_created: 2026-07-20
type: TASK_SPECIFICATION
status: backlog
priority: HIGH
phase: Design System Infrastructure
component: Blueprint Schema Evolution
relates_to:
  - VISUAL_DEFINITION_TEMPLATE.md (companion document)
  - ASSET_GENERATION_ARCHITECTURE.md (pipeline that uses the schema)
---

# Blueprint Schema v2 Review — Task Specification

**Purpose**: Review every existing blueprint template and recommend schema additions to support automated asset generation, without embedding prompt text.

**Scope**: All blueprint templates across all object classes.

**Deliverable**: Schema v2.0 specification with migration notes from v1.4 → v2.0.

---

## Background

ChatGPT identified that existing blueprints answer "Can I build this?" but not "Can an AI consistently draw this?" These are different problems requiring separate documents.

**Current State**:
- Blueprint templates (v1.4) contain manufacturing specs, material requirements, production data, variants
- Operational Data files contain runtime behavior (ports, consumables, AI, maintenance)
- No third document for visual appearance

**Proposed State**:
- Blueprint (manufacturing) — unchanged
- Operational Data (runtime behavior) — unchanged
- **Visual Definition** (appearance) — NEW document family

---

## Phase 1: Inventory

Review every blueprint template. Determine:

### Common Fields
What fields exist across ALL templates? These are stable and should not change.

### Duplicated Fields
What fields appear in multiple templates with the same purpose? Consolidate into shared schema.

### Missing Fields
What fields are needed to support automated asset generation that don't currently exist?

### Obsolete Fields
What fields are no longer relevant or have been superseded by newer patterns?

### Templates to Review
- `component_blueprint_v1.4` (existing)
- `unit_blueprint_v1.4` (existing)
- `structure_template.json` (if exists)
- `vehicle_template.json` (if exists)
- `resource_template.json` (if exists)
- `technology_template.json` (if exists)
- `corporation_template.json` (if exists)

---

## Phase 2: Visual Requirements Analysis

For each blueprint, determine what visual information is missing.

### Current Blueprint Gap
Existing blueprints have ZERO visualization metadata. They contain:
- ✅ Material requirements
- ✅ Production time
- ✅ Printer compatibility
- ✅ Output quality
- ✅ Physical dimensions
- ✅ Variants
- ❌ Recognition features
- ❌ Material profiles (for visual rendering)
- ❌ Technology level (visual progression)
- ❌ Manufacturing style (surface treatment)
- ❌ Render profiles (what views are needed)
- ❌ Camera profiles (which angles)
- ❌ Animation profile (idle animation)
- ❌ Complexity levels (L0-L5)
- ❌ Shared components (catalog consistency)

### Proposed Visualization Section
```json
"visualization": {
  "asset_id": "",
  "asset_family": "",
  "component_class": "",
  "recognition_features": [],
  "material_profiles": [],
  "technology_level": 1,
  "manufacturing_style": "",
  "silhouette": "",
  "visual_priority": {
    "primary": [],
    "secondary": [],
    "tertiary": []
  },
  "surface_finish": "",
  "color_profile": {},
  "animation_profile": "",
  "render_profiles": [],
  "camera_profiles": [],
  "complexity_levels": [],
  "shared_components": [],
  "design_constraints": {},
  "prompt_template_refs": {}
}
```

### Per-Template Analysis Required
For each template, determine:
1. Which visualization fields are REQUIRED vs OPTIONAL
2. Which existing blueprint fields map to visual concepts (e.g., `physical_properties.dimensions` → silhouette)
3. What new fields are needed beyond the standard visualization section
4. How variants interact with visualization (each variant may have different manufacturing style)

---

## Phase 3: Asset Metadata Requirements

For each blueprint, determine what asset metadata is needed.

### Proposed Assets Section
```json
"assets": {
  "inventory_icon": null,
  "catalog_render": null,
  "engineering_render": null,
  "blueprint_render": null,
  "exploded_view": null,
  "animation": null,
  "sprite": null
}
```

### Per-Template Analysis Required
For each template:
1. Which asset types are relevant? (not all templates need all assets)
2. What is the canonical naming convention for each asset type?
3. How do these references link to the Asset Registry?
4. What metadata is needed per asset type?

---

## Phase 4: Versioning Strategy

### Proposed Version Fields
```json
"metadata": {
  "schema_version": "2.0",
  "blueprint_version": "14",
  "operational_version": "3",
  "visualization_version": "5"
}
```

### Per-Template Analysis Required
For each template:
1. What is the current version?
2. What changes between versions?
3. How does schema_version relate to blueprint_version?
4. What migration path exists from v1.4 → v2.0?

---

## Phase 5: Schema Design Review (Qwen Task)

### Task for Qwen
Review every blueprint template and produce:

1. **Schema v2.0 Specification** — complete schema with all required/optional fields
2. **Per-Template Analysis** — which fields are required vs optional per template type
3. **Backward Compatibility Matrix** — what v1.4 fields map to v2.0 fields
4. **Migration Notes** — step-by-step migration from v1.4 → v2.0 for each template
5. **Missing Metadata Report** — what additional metadata is needed beyond the standard visualization section

### Constraints
- Preserve backward compatibility with existing blueprints
- Identify minimum additional metadata required for automated asset generation
- Ensure visualization data is descriptive (not prompt-oriented)
- Recommend common visualization section shareable across all template types
- Separate immutable blueprint facts from generator-specific concerns
- Produce migration guidance that doesn't break existing systems

### What NOT to Do
- Do NOT generate prompts
- Do NOT embed prompt text in the schema
- Do NOT change existing manufacturing/operational fields (only add new sections)
- Do NOT redesign the blueprint structure (only extend it)

---

## Deliverables

1. **Blueprint Schema v2.0 Specification** — complete schema document
2. **Per-Template Analysis Report** — required/optional fields per template type
3. **Backward Compatibility Matrix** — v1.4 → v2.0 field mapping
4. **Migration Notes** — step-by-step migration for each template
5. **Missing Metadata Report** — additional metadata needed beyond standard visualization

---

## Notes

This task is architectural, not implementation-focused. It lets Qwen concentrate on designing a schema that supports the asset pipeline without locking into any one image generation model or prompting strategy.

The key insight: Blueprint Schema v2 adds a `visualization` section (descriptive facts about appearance) while leaving manufacturing and operational data unchanged. This keeps concerns properly separated.
