---
date_created: 2026-07-24
type: REVIEW_SUMMARY
status: active
purpose: v1.1 refinement findings for ASSET_GENERATION_ARCHITECTURE.md
target_doc: ASSET_GENERATION_ARCHITECTURE.md
reviewer: qwen
---

# ASSET_GENERATION_ARCHITECTURE.md — v1.1 Review Findings

## 1. Executive Summary

The architecture document is **structurally sound** and has been validated through first asset generation. All seven proposed improvements are valid refinements. Most can be incorporated with minimal changes to the existing document. A few recommendations belong in other canonical documents instead.

---

## 2. Recommended Changes

### Change 1: Explicit Visual Definition Layer ✅ RECOMMENDED

**Rationale**: The pipeline diagrams already *implicitly* include Visual Definition (it appears in the INPUT LAYER box and as a versioned input to Prompt Builder). However, the **Prompt Builder description** should explicitly name it as one of four canonical sources — not just "enriched blueprints." This clarifies that prompts are synthesized from multiple independent documents.

**Where**: Prompt Builder section (both inputs list and pipeline diagram)

**Recommended text for Prompt Builder Inputs box**:
```
  Inputs:
  - Blueprint (validated + enriched)           [manufacturing spec]
  - Operational Data                           [runtime behavior]
  - Visual Definition                          [appearance spec]
  - Design System                              [Icon Bible + Material Library + Philosophy]
```

**Rationale**: This makes the four-source architecture explicit. "Design System" groups Icon Bible, Material Library, and Visual Philosophy as a single conceptual input (they're all design-system-level references). The bracketed labels clarify each source's role.

**Cross-reference note**: This is already partially reflected in VISUAL_DEFINITION_TEMPLATE.md's position diagram. The ASSET_GENERATION_ARCHITECTURE.md should be the *canonical* place where this four-source pattern is documented as an architectural principle.

---

### Change 2: Distinguish Asset Classes ✅ RECOMMENDED

**Rationale**: Inspection Assets and Gameplay Assets have fundamentally different rendering requirements (e.g., catalog renders need white backgrounds and multiple angles; surface sprites need transparent backgrounds, correct pivots, baked terrain). The architecture should acknowledge this distinction because it affects:
- Which render profiles are generated
- QA validation criteria
- Prompt template selection

**Where**: After the pipeline diagram, add a new section "Asset Classes" before "Component Details."

**Recommended text**:
```markdown
## Asset Classes

Assets fall into two classes with different rendering requirements. The distinction affects
which render profiles are generated, which prompt templates are used, and which QA criteria apply.

### Inspection Assets
Purpose: Human-readable reference documentation.

| Render Type | Background | Detail Level | Use Case |
|-------------|-----------|--------------|----------|
| inventory_icon | Opaque (solid) | L0-L1 silhouette | UI slots, menus |
| catalog_render | White | Two isometric views | Encyclopedia, reference |
| engineering_render | White/light | Callouts + dimensions | Technical documentation |
| blueprint | White | Section cuts, internal routing | Design review |
| exploded_view | White | Disassembled components | Assembly instructions |

### Gameplay Assets
Purpose: Runtime rendering in the game world.

| Render Type | Background | Detail Level | Use Case |
|-------------|-----------|--------------|----------|
| sprite_sheet | Transparent | L0-L5 complexity | Surface tiles, inventory |
| animation_keyframes | Transparent | Per-frame detail | In-world animation |
| damage_states | Transparent | Progressive degradation | Combat/decay visuals |
| construction_states | Transparent | Progressive assembly | Building sequence |

### Cross-Class Rules
- Both classes share the same Visual Definition (appearance spec)
- Both classes use the same canonical asset ID
- Inspection assets validate against documentation standards
- Gameplay assets validate against runtime suitability (pivot, transparency, tile alignment)
```

**Cross-reference note**: The render profile values (`inventory_icon`, `catalog_render`, etc.) already exist in VISUAL_DEFINITION_TEMPLATE.md. This section doesn't invent new profiles — it organizes existing ones into two functional classes.

---

### Change 3: Asset Provenance ✅ RECOMMENDED (partial)

**Rationale**: The Prompt Archive and Asset Registry sections *already* track most provenance fields:
- `blueprint_version` ✓
- `visual_definition_version` ✓
- `prompt_template_version` ✓
- `generated_at` (timestamp) ✓
- `generator_id` (generator version) ✓
- `status` / `qa_reviewer` (approval status) ✓

**What's missing**: `operational_data_version` and explicit `visual_definition_revision`. These should be added to the Prompt Archive entry schema.

**Recommended changes to Prompt Archive Entry schema**:
```json
{
  "prompt_id": "PROMPT_20260720_001",
  "blueprint_version": "14",
  "operational_data_version": "3",
  "visual_definition_revision": "5",
  "template_version": "3",
  "generator_id": "prompt_builder_v2.0",
  "generated_at": "2026-07-20T12:00:00Z",
  "prompt_text": "...",
  "output_asset_ids": ["UNIT_INFLATABLE_HAB_MK3_catalog_render"],
  "status": "completed"
}
```

**Rationale**: `operational_data_version` is needed because gameplay behavior can change independently of appearance. `visual_definition_revision` (separate from template version) tracks when the visual spec itself changed, not just the prompt template.

---

### Change 4: Location-Agnostic Assets ✅ RECOMMENDED (wording clarification)

**Rationale**: The document mentions "location-agnostic" in several places but doesn't explicitly state *how* location independence is achieved. A single clarifying paragraph should be added to the pipeline diagram or as a design principle.

**Where**: After the complete pipeline diagram, add a "Design Principle: Location Independence" note.

**Recommended text**:
```markdown
## Design Principle: Location Independence

Assets are **location-agnostic**. The same asset library is used across all planets (Luna, Mars, Venus, etc.).

Planetary appearance is achieved through:
- Terrain layer composition (surface tiles)
- Environmental effects (atmosphere, weather)
- Lighting and color grading
- Simulation parameters (gravity, temperature, pressure)

**Not through duplicate asset libraries.** A `UNIT_INFLATABLE_HAB_MK3` on Luna is the same visual asset as one on Mars — only the terrain beneath it and the atmospheric lighting differ.

This principle is enforced by:
- Asset IDs that contain no location prefix
- Visual Definitions that specify appearance, not environment
- Prompt templates that exclude planetary context from subject description
```

**Cross-reference note**: This reinforces UNIFIED_ASSET_CATALOG_ARCHITECTURE.md's naming principle but belongs in ASSET_GENERATION_ARCHITECTURE.md because it directly affects prompt generation (prompts must not include planetary context).

---

### Change 5: Shared Components ⚠️ PARTIAL — belongs in VISUAL_DEFINITION_TEMPLATE.md

**Rationale**: The architecture *already* references shared components in the Asset Registry entry (`shared_components` array) and in Blueprint Validator (`shared_components_exist` validation rule). The **Visual Definition Template** already has a `shared_components` field with examples.

**What ASSET_GENERATION_ARCHITECTURE.md needs**: A single sentence in the pipeline diagram or Prompt Builder section acknowledging that shared components are a first-class architectural concept, not an afterthought.

**Recommended text** (add to Pipeline Diagram INPUT LAYER):
```
  Shared Components Registry (reusable visual primitives)
```

And add to the QA Review validation checklist:
| Shared Component Consistency | Asset Registry | Same asset used everywhere? | WARNING |

**What NOT to change**: Do not list specific shared component examples (airlocks, ports, etc.) in this document — those belong in VISUAL_DEFINITION_TEMPLATE.md and the Icon Bible.

---

### Change 6: Human QA Stage ✅ RECOMMENDED

**Rationale**: The QA Review section already exists but is framed as an automated validation stage. It should be explicitly repositioned as a **Human QA stage with automated pre-filtering**. Recent asset generation demonstrated that certain qualities (transparent backgrounds, sprite pivots, baked terrain, gameplay suitability) require human judgment.

**Where**: Rename "QA Review" to "Human QA Review" and add an automated pre-filter stage before it.

**Recommended changes**:

1. **Rename the stage** in the pipeline diagram:
```
┌─────────────────────────────────────────────────────────────┐
│           AUTOMATED PRE-FILTER              │  (new)
│                                                             │
│  Runs before human review:                                  │
│  - Background transparency check                            │
│  - Sprite pivot alignment                                   │
│  - Tile edge continuity                                     │
│  - Resolution/compliance                                    │
│  - Color family validation                                  │
│                                                             │
│  Output: Pre-filtered assets (passed → Human QA)            │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│              HUMAN QA REVIEW                                │  (renamed)
```

2. **Add to QA Review section**:
```markdown
### 6. Human QA Review

**Purpose**: Human review of assets that passed automated pre-filtering.

**Automated Pre-Filter** (runs before human review):
| Check | Method | Pass Criteria |
|-------|--------|---------------|
| Background transparency | Pixel analysis | No opaque pixels outside asset bounds |
| Sprite pivot alignment | Bounding box analysis | Pivot at expected position |
| Tile edge continuity | Edge comparison | Adjacent tiles connect visually |
| Resolution compliance | Dimension check | Matches spec dimensions |
| Color family validation | Histogram analysis | Colors within Icon Bible families |

**Human Review Checklist**:
| Check | Why Automated Can't Do It |
|-------|--------------------------|
| Grounded in reality | Requires domain knowledge (NASA/ESA reference) |
| Functional clarity | Requires understanding of engineering intent |
| Gameplay suitability | Requires playtesting intuition |
| Baked terrain quality | Requires visual judgment of tile seams |
| Sprite pivot correctness | Requires understanding of in-world behavior |
| Manufacturing style accuracy | Requires comparison to real hardware |
| Tech level progression | Requires comparative analysis across Mk1-Mk5 |

**Output**: Approved / Rejected with specific human-readable feedback
```

---

### Change 7: Current Status ✅ RECOMMENDED

**Recommended text** (replace existing "Current Status" section):
```markdown
## Current Status

### ✅ Validated Through Production
- [x] Architecture validated through first asset generation (catalog renders)
- [x] Initial catalog renders produced and reviewed
- [x] PromptBuilder philosophy refined based on production experience
- [x] First surface sprites generated (preliminary)
- [x] Human QA process established (transparent backgrounds, pivots, tile seams)

### ✅ Architecture Complete
- [x] Four-source input model (Blueprint + Operational Data + Visual Definition + Design System)
- [x] Asset class distinction (Inspection vs Gameplay) documented
- [x] Provenance tracking in Prompt Archive and Asset Registry
- [x] Location-agnostic principle codified
- [x] Shared components recognized as first-class concept
- [x] Human QA stage with automated pre-filtering defined

### 🔄 In Progress
- [ ] Blueprint schema refinement (visualization section addition)
- [ ] Visual Definition refinement (field completeness, defaults)
- [ ] Prompt template automation (PromptBuilder service implementation)
- [ ] First terrain/biome tile generation using compressed biome spec

### 🔴 Pending
- [ ] Unit sprites regeneration (16 files) — first full design system implementation
- [ ] Corporate logos regeneration (~30 files) — Session 5 + Session 7 specs
- [ ] Terrain Layer 0 tiles (~50+ files) — foundation for surface rendering
- [ ] Biome tiles alignment (14 → ~16 canonical) — Session 10 compression
- [ ] Catalog components regeneration (~200+ files) — Icon Bible hierarchy
```

---

## 3. Changes NOT Recommended

| # | Proposal | Reason Not Recommended |
|---|----------|----------------------|
| N1 | Add automated image quality scoring (perceptual hash, SSIM) | Over-engineering. Human QA is the right gate for subjective quality. Can revisit when generating 10,000+ assets. |
| N2 | Add CI/CD pipeline stage for asset generation | Belongs in DevOps documentation, not asset architecture. |
| N3 | Add asset compression/format optimization section | Belongs in rendering/engine documentation, not generation architecture. |
| N4 | Add version migration strategy between asset generations | Already covered by `regeneration_eligible` + `obsolete_assets` fields. Sufficient for current scale. |
| N5 | Add specific shared component examples (airlocks, ports) to this document | Belongs in VISUAL_DEFINITION_TEMPLATE.md and Icon Bible. This document should reference the concept, not enumerate instances. |

---

## 4. Recommendations That Belong Elsewhere

| Recommendation | Should Be In | Reason |
|---------------|-------------|--------|
| Shared component examples (airlocks, ports, docking collars) | VISUAL_DEFINITION_TEMPLATE.md | These are visual definition data, not architecture |
| Render profile specifications (white bg, isometric views) | VISUAL_DEFINITION_TEMPLATE.md | Already there — this document references them |
| Color families and shape language | Icon Bible / DESIGN_SYSTEM_ARCHITECTURE.md | Framework-level content |
| Manufacturing style examples | Icon Bible / DESIGN_SYSTEM_ARCHITECTURE.md | Framework-level content |
| Biome compression specs | DESIGN_RESEARCH_INDEX.md (Session 10) | Research specification, not architecture |
| Corporate branding guidelines | Session 5 + Session 7 files | Domain-specific research |

---

## 5. Summary of All Recommended Modifications

| # | Modification | Location in Document | Effort |
|---|-------------|---------------------|--------|
| 1 | Explicit four-source input model | Prompt Builder inputs box + pipeline diagram | Low (text addition) |
| 2 | Asset Classes section (Inspection vs Gameplay) | New section after pipeline diagram | Medium (new section with tables) |
| 3 | Add `operational_data_version` to Prompt Archive schema | Prompt Archive entry JSON | Low (one field) |
| 4 | Location Independence design principle | New subsection after pipeline diagram | Low (paragraph addition) |
| 5 | Shared Components as first-class concept | Pipeline diagram + QA checklist | Low (one line each) |
| 6 | Human QA with automated pre-filter | Rename stage + add pre-filter box + expand checklist | Medium (structural change) |
| 7 | Update Current Status section | Replace existing status text | Low (text replacement) |

**Total impact**: ~15-20 lines of new content, 2 structural additions (Asset Classes section, Human QA pre-filter box), 3 field additions to schemas. No breaking changes to existing architecture.

---

## 6. Rationale Summary

All seven improvements stem from the same root cause: **the architecture was designed theoretically and has now been validated through production**. The refinements don't change the architecture — they make it match what was actually discovered during first asset generation. Each recommendation either:
- Makes implicit patterns explicit (Changes 1, 4, 5)
- Fills a gap revealed by production experience (Changes 2, 6)
- Adds missing provenance fields (Change 3)
- Reflects actual project progress (Change 7)

The architecture remains model-agnostic, version-independent, and location-agnostic. These are **refinements**, not **revisions**.
