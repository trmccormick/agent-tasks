# ASSET_GENERATION_ARCHITECTURE.md — v1.1 Changes Applied

**Date**: 2026-07-25  
**Purpose**: Shareable summary of all v1.1 changes applied to ASSET_GENERATION_ARCHITECTURE.md  
**Commit**: `85ac242` in galaxyGame/docs/new_agent repo  
**Diff**: +130 lines, -27 lines

---

## Executive Summary

The architecture document was refined through 7 changes validated against production experience (first catalog renders and surface sprites). All changes are **refinements**, not rewrites — the overall architecture is preserved.

---

## Changes Applied

### Change 1: Explicit Four-Source Input Model

**What changed**: Prompt Builder box now explicitly lists four canonical sources instead of implying prompts come from "enriched blueprints" alone.

**Before**:
```
│  Converts enriched blueprints into structured prompts:      │
│                                                             │
│  Inputs:                                                    │
│  - Blueprint (validated + enriched)                         │
│  - Material Library                                         │
│  - Icon Bible                                               │
│  - Visual Philosophy                                        │
│  - Manufacturing Rules                                      │
│  - Design Research Index                                    │
```

**After**:
```
│  Converts four canonical sources into structured prompts:   │
│                                                             │
│  Inputs:                                                    │
│  - Blueprint (validated + enriched)           [manufacturing spec] │
│  - Operational Data                         [runtime behavior] │
│  - Visual Definition                        [appearance spec] │
│  - Design System                            [Icon Bible + Material Library + Philosophy] │
```

**Rationale**: Makes the four-source architecture explicit. "Design System" groups Icon Bible, Material Library, and Visual Philosophy as a single conceptual input. Bracketed labels clarify each source's role.

---

### Change 2: Asset Classes (Inspection vs Gameplay)

**What changed**: New section added after pipeline diagram with two tables categorizing render types by purpose.

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

**Rationale**: Inspection and Gameplay assets have fundamentally different rendering requirements (white bg vs transparent, multiple angles vs correct pivots). This distinction affects prompt template selection and QA criteria.

---

### Change 3: Asset Provenance Fields

**What changed**: Two new fields added to Prompt Archive entry schema.

```json
{
  "prompt_id": "PROMPT_20260720_001",
  "blueprint_version": "14",
  "operational_data_version": "3",        // NEW
  "visual_definition_revision": "5",       // NEW (was visual_definition_version)
  "template_version": "3",
  "generated_at": "2026-07-20T12:00:00Z",
  "generator_id": "prompt_builder_v2.0",
  "prompt_text": "...",
  "output_asset_ids": ["UNIT_INFLATABLE_HAB_MK3_catalog_render"],
  "status": "completed"
}
```

**Rationale**: `operational_data_version` tracks gameplay behavior changes independent of appearance. `visual_definition_revision` (renamed from `visual_definition_version`) tracks when the visual spec itself changed, separate from prompt template version.

---

### Change 4: Location Independence Design Principle

**What changed**: New section added after pipeline diagram.

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

**Rationale**: The document mentioned "location-agnostic" in several places but didn't explicitly state *how* location independence is achieved. This clarifies that prompts must not include planetary context.

---

### Change 5: Shared Components as First-Class Concept

**What changed**: Two additions — one line in pipeline diagram, one row in QA checklist.

Pipeline INPUT LAYER now includes:
```
│  Shared Components Registry (reusable visual primitives)    │
```

QA Review validation checklist now includes:
| Shared Component Consistency | Asset Registry | Same asset used everywhere? | WARNING |

**Rationale**: The architecture already referenced shared components in Asset Registry and Blueprint Validator. This makes it explicit as a first-class architectural concept without enumerating specific examples (those belong in VISUAL_DEFINITION_TEMPLATE.md).

---

### Change 6: Human QA with Automated Pre-Filter

**What changed**: Three modifications — pipeline diagram, section rename, expanded checklists.

**Pipeline diagram**: Added AUTOMATED PRE-FILTER box before HUMAN QA REVIEW:
```
┌─────────────────────────────────────────────────────────────┐
│           AUTOMATED PRE-FILTER              │  (new)        │
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
```

**Section renamed**: "QA Review" → "Human QA Review"

**Expanded checklists**:

Automated Pre-Filter (5 checks):
| Check | Method | Pass Criteria |
|-------|--------|---------------|
| Background transparency | Pixel analysis | No opaque pixels outside asset bounds |
| Sprite pivot alignment | Bounding box analysis | Pivot at expected position |
| Tile edge continuity | Edge comparison | Adjacent tiles connect visually |
| Resolution compliance | Dimension check | Matches spec dimensions |
| Color family validation | Histogram analysis | Colors within Icon Bible families |

Human Review (7 checks — why automated can't do it):
| Check | Why Automated Can't Do It |
|-------|--------------------------|
| Grounded in reality | Requires domain knowledge (NASA/ESA reference) |
| Functional clarity | Requires understanding of engineering intent |
| Gameplay suitability | Requires playtesting intuition |
| Baked terrain quality | Requires visual judgment of tile seams |
| Sprite pivot correctness | Requires understanding of in-world behavior |
| Manufacturing style accuracy | Requires comparison to real hardware |
| Tech level progression | Requires comparative analysis across Mk1-Mk5 |

**Rationale**: Recent asset generation demonstrated that transparent backgrounds, sprite pivots, baked terrain, and gameplay suitability require human judgment. The automated pre-filter catches what can be checked programmatically; humans handle subjective quality.

---

### Change 7: Updated Current Status

**What changed**: New section added with verified figures from EXISTING_ASSET_AUDIT.md.

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

**Figure verification against EXISTING_ASSET_AUDIT.md**:
| Figure | Source Document | Match? |
|--------|----------------|--------|
| Unit sprites pending | 16 files in `unit_sprites/` | ✅ |
| Corporate logos pending | ~30 files in `logos/` | ✅ |
| Terrain Layer 0 tiles | ~50+ existing | ✅ |
| Biome tiles (14→16) | 14 PNGs in `biomes/` | ✅ |
| Catalog components | ~200+ files in `catalog/` | ✅ |

---

## Changes NOT Applied

| # | Proposal | Reason Not Applied |
|---|----------|-------------------|
| N1 | Automated image quality scoring (perceptual hash, SSIM) | Over-engineering. Human QA is the right gate for subjective quality. |
| N2 | CI/CD pipeline stage for asset generation | Belongs in DevOps documentation, not asset architecture. |
| N3 | Asset compression/format optimization section | Belongs in rendering/engine documentation. |
| N4 | Version migration strategy between generations | Already covered by `regeneration_eligible` + `obsolete_assets` fields. |
| N5 | Specific shared component examples (airlocks, ports) | Belongs in VISUAL_DEFINITION_TEMPLATE.md and Icon Bible. |

---

## Recommendations That Belong Elsewhere

| Recommendation | Should Be In | Reason |
|---------------|-------------|--------|
| Shared component examples | VISUAL_DEFINITION_TEMPLATE.md | Visual definition data, not architecture |
| Render profile specifications | VISUAL_DEFINITION_TEMPLATE.md | Already there — this doc references them |
| Color families and shape language | Icon Bible / DESIGN_SYSTEM_ARCHITECTURE.md | Framework-level content |
| Manufacturing style examples | Icon Bible / DESIGN_SYSTEM_ARCHITECTURE.md | Framework-level content |
| Biome compression specs | DESIGN_RESEARCH_INDEX.md (Session 10) | Research specification, not architecture |

---

## Verification Checklist

- [x] No duplicate "Current Status" sections
- [x] No shared component examples duplicating VISUAL_DEFINITION_TEMPLATE.md
- [x] No conflicts with DESIGN_SYSTEM_ARCHITECTURE.md
- [x] All figures verified against EXISTING_ASSET_AUDIT.md
- [x] Architecture Complete items match what was actually applied (changes 1-6)
- [x] Single commit for all changes
