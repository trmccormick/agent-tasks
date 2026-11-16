# Current State Report — Asset Generation Pipeline

**Date**: 2026-08-24  
**Method**: Filesystem audit across `galaxyGame/docs/reference/asset-generation/`, `data/images/`, `data/schemas/`, `data/json-data/templates/`, and `agent-tasks/projects/galaxy_game/tasks/`

---

## 1. Asset / Image Bible

| Field | Value |
|-------|-------|
| **File/path** | `docs/reference/asset-generation/UNIFIED_ASSET_CATALOG_ARCHITECTURE.md` |
| **Date** | 2026-07-19 (created + modified) |
| **Status** | REFERENCE / Phase 2 - Asset Architecture |
| **Canonical?** | ✅ YES — this is the canonical naming/location principle document |
| **What's implemented** | Defines unified catalog architecture: assets organized by resource type, not origin. Core rule: "Nothing is location-specific unless inherently unique." |
| **What exists on disk** | Single document, ~100+ lines of design philosophy + examples |
| **Unresolved** | No companion "Icon Bible" file with actual visual rules (color families, shapes, tech-level markers). The audit doc references an "Icon Bible" that was planned but never materialized as a separate file. |

**Stale/duplicate check**: No duplicates found. This is the only asset catalog architecture document.

---

## 2. Production Asset Render Template

| Field | Value |
|-------|-------|
| **File/path** | `docs/reference/asset-generation/PRODUCTION_ASSET_RENDER_TEMPLATE_V1.0.md` |
| **Status** | v1.0 — appears canonical |
| **Canonical?** | ✅ YES — this is the active render template |
| **What's implemented** | Complete render spec: top-down orthographic, single object ~75% canvas, transparent 1024×1024 PNG, only `{{UNIT_NAME}}`, `{{FUNCTIONAL_ROLE}}`, `{{RECOGNITION_FEATURES}}` vary. All style/materials inherited from Visual Profile. |
| **What exists on disk** | Single document with SUBJECT/STYLE/FUNCTION/MANUFACTURING/MATERIALS/RECOGNITION FEATURES/BACKGROUND sections |
| **Unresolved** | None — this is complete and matches the handoff's description exactly |

**Stale/duplicate check**: No v2 or alternate versions found. Clean.

---

## 3. Visual Definition Schema/Template

| Field | Value |
|-------|-------|
| **File/path** | `docs/reference/asset-generation/VISUAL_DEFINITION_TEMPLATE.md` |
| **Date** | 2026-07-20 |
| **Status** | active |
| **Canonical?** | ✅ YES — canonical visual definition schema |
| **What's implemented** | Required fields (asset_id, asset_family, component_class, recognition_features, material_profiles, technology_level, manufacturing_style) + optional presentation fields (silhouette, color_profile, animation_profile, render_profiles, etc.) |
| **What exists on disk** | Single template document with JSON schema examples and field definitions |
| **Unresolved** | The `asset_id` format uses Icon Bible convention (`[CATEGORY]_[TYPE]_[NAME]_[VARIANT]`) but the Icon Bible itself doesn't exist as a file — this is an unresolved dependency. |

**Stale/duplicate check**: No duplicates. Clean.

---

## 4. Component Blueprint / Relevant Blueprint Schemas

| Field | Value |
|-------|-------|
| **Active schema** | `data/schemas/component_blueprint_v1.1.json` |
| **Template versions** | `data/json-data/templates/`: v1 (2025-06), v1.1 (2025-06), v1.2 (2025-12), v1.3 (2025-12), v1.4 (2026-04) |
| **Stale versions** | `data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/templates/`: v1, v1.1, v1.2, v1.3 (archived copies) |
| **Canonical?** | ✅ Schema: v1.1 in `data/schemas/` is current. Template: v1.4 in `data/json-data/templates/` is latest. |
| **What's implemented** | Component blueprint schema defines structure for reusable industrial components. Templates show evolution from simple (v1, 302 bytes) to complex (v1.4, 2715 bytes). |
| **Unresolved** | No actual `component_blueprint.json` instances exist in the blueprints directory — only the schema/template. The handoff says I-beam/panel is "ready to become the first real component_blueprint" which confirms none exist yet. |

**Stale/duplicate check**: `old-code/` contains archived versions (expected). No active duplicates.

---

## 5. Asset Prompt Compiler / PromptBuilder Contract

| Field | Value |
|-------|-------|
| **Task file** | `agent-tasks/projects/galaxy_game/tasks/active/2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md` (moved from backlog) |
| **Status** | ACTIVE — task was just moved to active by the `mv` command in terminal history |
| **Canonical?** | 🔄 IN PROGRESS — contract is being written, not yet finalized |
| **What's implemented** | Task file exists with full context: Blueprint → Operational Data → Visual Definition → Visual Profile → Render Template → generated prompt pipeline. References all relevant architecture docs. |
| **What exists on disk** | Task file in `tasks/active/` (was just moved from backlog). No separate contract document yet — the task IS the work item to create it. |
| **Unresolved** | Contract not yet written. Handoff says "dispatched to Ryzen tonight, still running as of this handoff" — check with ChatGPT on completion status. |

**Stale/duplicate check**: No duplicates. This is a single active task.

---

## 6. Gemini-Related Prompt-Generation/Integration Work

| Field | Value |
|-------|-------|
| **Search result** | ❌ NONE FOUND |
| **Canonical?** | N/A — no Gemini-specific prompt generation work exists on disk |
| **What's implemented** | Nothing. No `gemini*prompt*` or `gemini*asset*` files found anywhere in the codebase. |
| **Unresolved** | The handoff mentions "Gemini-related prompt-generation/integration work" but no evidence of this exists on disk. Either it was never created, or it lives only in conversation context (ChatGPT side). |

**Stale/duplicate check**: N/A — nothing to duplicate.

---

## 7. Current Asset-Generation Pipeline

| Field | Value |
|-------|-------|
| **Architecture doc** | `docs/reference/asset-generation/ASSET_GENERATION_ARCHITECTURE.md` (2026-07-20) |
| **Status** | active — canonical pipeline architecture |
| **What's implemented** | Complete 4-stage pipeline: Blueprint Validator → Blueprint Enrichment → Prompt Builder → Image Generation. Defines input layer (blueprint, operational data, visual definition, material library, icon bible, visual philosophy, design research index, shared components registry). |
| **What exists on disk** | Architecture spec + supporting docs (visual philosophy, visual definition template, render template, unified catalog architecture) |
| **Unresolved** | Pipeline is fully specified but NOT implemented in code. No PromptBuilder class, no validator, no enrichment engine exist as Ruby/Python code. It's a design document waiting for implementation. |

**Stale/duplicate check**: No duplicates. Clean architecture set.

---

## 8. Existing Catalog Assets and Source Definitions

| Field | Value |
|-------|-------|
| **Location** | `data/images/catalog/` — 13 top-level subdirectories |
| **Subdirs** | components/, crafts/, infrastructure/, items/, materials/, modules/, ports/, rigs/, structures/, units/ |
| **Components subdirs** | electronics/ (empty), materials/ (empty), mechanical/ (empty), production/ (empty), specialized/ (empty), structural/ (HAS FILES) |
| **Structural assets** | 3d_printed_ibeam_mk1.png through mk4.png (production), plus mk1_test.png, mk3_test.png (tests), primary_structural_spine.png, and 6 ChatGPT Image dated Jul 12 reference files |
| **Other catalog content** | crafts/, modules/, rigs/, structures/, units/ — populated but audit says "pre-Icon Bible, inconsistent" |
| **Canonical?** | ⚠️ PARTIAL — I-beam Mk1-Mk4 are production assets. Everything else is pre-design-system (inconsistent styling/naming) |
| **Unresolved** | ~200+ catalog files need canonical naming and consistent styling per the Icon Bible (which doesn't exist as a file). infrastructure/ is empty. Most component subdirs are empty shells. |

---

## 9. Existing I-Beam, Structural-Panel, and Lunar Construction Samples

| Field | Value |
|-------|-------|
| **I-beam Mk1-Mk4** | ✅ EXIST — `data/images/catalog/components/structural/3d_printed_ibeam_mk{1-4}.png` (production versions, ~2.1-2.3MB each) |
| **I-beam test variants** | ⚠️ EXISTS — `3d_printed_ibeam_mk1_test.png`, `3d_printed_ibeam_mk3_test.png` (test iterations, not production) |
| **Primary structural spine** | ✅ EXISTS — `primary_structural_spine.png` (~2.1MB) |
| **Structural panel samples** | ❌ NOT FOUND — no `*panel*.png` files in data/images/ |
| **Lunar manufacturing visual progression** | ⚠️ PARTIAL — I-beam Mk1→Mk4 shows tech progression but all are "3d_printed" (Lunar). No Earth-manufactured structural variants exist yet. |
| **Canonical?** | ✅ I-beam Mk1-Mk4 are production assets. Panel samples need to be generated next. |

---

## 10. Local Task Files Describing Pending Image-Generation Work

| Field | Value |
|-------|-------|
| **Active** | `2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md` — write the contract spec |
| **Backlog design** | `2026-06-17-GALAXY-GAME-IMAGE-ASSETS.md` — original image assets task (old) |
| **Backlog design** | `2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION.md` — asset registry spec |
| **Backlog design** | `2026-07-19-MEDIUM-FEATURE-UNIT-SPRITE-ASSET-GENERATION.md` — unit sprite generation |
| **Backlog deferred** | `2026-08-23-LOW-BUG-FIX-MISSING-BIOME-TERRAIN-TILE-ASSETS.md` — missing biome/terrain tiles |
| **Completed** | `2026-07-15-HIGH-DATA-SESSION2-ASSET-CATALOG-DESIGN.md` |
| **Completed** | `2026-07-15-HIGH-DATA-SESSION3-ASSET-ORGANIZATION.md` |
| **Completed** | `2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING.md` |
| **Canonical?** | Active task is current. Backlog items are older design tasks — may be superseded by the 2026-08-23 architecture work. |

---

## Summary: What to Reuse vs Redesign

### ✅ REUSE (canonical, current, no redesign needed)
1. **Production Asset Render Template v1.0** — complete, canonical, matches handoff exactly
2. **Visual Definition Template** — complete schema with required/optional fields
3. **Visual Profile: precision_industrial_v1** — locked attributes, canonical reference (RH-400)
4. **Unified Asset Catalog Architecture** — naming/location principle is established canon
5. **Visual Philosophy** — 9 principles document, foundation for all visual decisions
6. **I-beam Mk1-Mk4 assets** — production-ready Lunar structural samples exist
7. **Asset Generation Architecture** — complete pipeline spec (design only, not code)

### ⚠️ PARTIAL / NEEDS ATTENTION
8. **Icon Bible** — referenced everywhere but doesn't exist as a file. The "Unified Asset Catalog" doc covers naming philosophy but not actual visual rules (color families, shapes, tech-level markers).
9. **Component Blueprint v1.4 template** — exists but no instances created yet
10. **~200 catalog assets** — pre-design-system, inconsistent, need canonical rework

### ❌ DOES NOT EXIST (no stale versions to worry about)
11. **Gemini prompt generation work** — nothing on disk
12. **Structural panel samples** — not generated yet
13. **Earth-manufactured structural variants** — not generated yet
14. **PromptBuilder code** — only specified in architecture doc, no implementation

### 🔄 IN PROGRESS
15. **Asset Prompt Compiler Contract** — task file active, contract document being written (check with ChatGPT)
