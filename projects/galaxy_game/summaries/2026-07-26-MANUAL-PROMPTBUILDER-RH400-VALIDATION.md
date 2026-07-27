---
date_created: 2026-07-26
type: MANUAL_PROMPTBUILDER_TEST
status: complete
unit: RH-400 Regolith Harvester Rover
purpose: Manual PromptBuilder validation — gameplay asset prompt assembly + gap analysis
reviewer: claude (manual review required)
---

# RH-400 Gameplay Asset Prompt — Manual PromptBuilder Validation

**Date**: 2026-07-26  
**Type**: Manual PromptBuilder Test (no file modifications, no image generation, no schema updates)  
**Subject**: RH-400 Regolith Harvester Rover (`regolith_harvester_rover`)

---

## Purpose

Validate that the current GalaxyGame documentation contains sufficient structured information to assemble a gameplay-asset prompt without relying on creative interpretation. Assemble the prompt and identify any missing canonical inputs.

---

## Canonical Sources Used (in order of precedence)

1. **RH-400 Blueprint**: `data/json-data/blueprints/crafts/ground/regolith_harvesting_rover_bp.json`
2. **Operational Data**: Not found in workspace (`operational_data/crafts/ground/` directory exists but no RH-400 data file)
3. **VISUAL_DEFINITION_TEMPLATE.md**: Template exists at `docs/new_agent/projects/galaxy_game/design/VISUAL_DEFINITION_TEMPLATE.md` — **no instantiated Visual Definition for RH-400**
4. **DESIGN_SYSTEM_SUMMARY.md**: `docs/new_agent/projects/galaxy_game/design/DESIGN_SYSTEM_SUMMARY.md` (includes references to Icon Bible, Design Research Index sessions 1–10)
5. **ASSET_GENERATION_ARCHITECTURE.md**: `docs/new_agent/projects/galaxy_game/design/ASSET_GENERATION_ARCHITECTURE.md`
6. **RH-400 Prompt Template**: `docs/reference/asset-generation/rh400-prompt-template.md` (existing RH-400-specific template notes)
7. **VISUAL_PHILOSOPHY.md**: `docs/new_agent/projects/galaxy_game/design/VISUAL_PHILOSOPHY.md` (9 principles)
8. **DESIGN_RESEARCH_INDEX.md**: `docs/new_agent/projects/galaxy_game/design/DESIGN_RESEARCH_INDEX.md` (sessions 1–10 mappings)

---

## Deliverable 1: Finished Gameplay-Asset Prompt

```
───────────────────────────────────────────────────────
RH-400 REGOLITH HARVESTER ROVER — GAMEPLAY ASSET PROMPT
───────────────────────────────────────────────────────

[CONTEXT] Galaxy Game: industrial space settlement simulation, hard-sci-fi aesthetic, clean utilitarian engineering. [Visual Philosophy + RH-400 Prompt Template]

[ASSET NAME] RH-400 Regolith Harvester Rover (id: regolith_harvester_rover) [Blueprint]

[PHYSICAL SPECS]
  Length:   6.80 m
  Width:    3.30 m
  Height:   2.65 m
  Empty Mass: 22,800 kg
  Volume:   59.5 m³
  Drag Coefficient: 0.9
[Blueprint — physical_properties]

[ASSET CLASS] Gameplay Asset (sprite_sheet, animation_keyframes, damage_states) [Asset Generation Architecture]

[MANUFACTURING STYLE] Bootstrap/Frontier — improvised, thick members, visible structure, DMLS matte industrial finish. [Design System Summary (Session 6)]

[AESTHETIC PROFILE]
  - White/dark-grey hull panels
  - Hazard warning stripes (yellow/black)
  - Exposed tracks / heavy chassis
  - DMLS (Direct Metal Laser Sintering) matte industrial surface finish
[RH-400 Prompt Template + Design System Summary (Session 6)]

[RECOGNITION FEATURES — must appear in ALL frames]
  ▸ Heavy tracked undercarriage (ground mobility)
  ▸ Forward regolith-skimming intake mechanism
  ▸ Mid-body processing/hopper module
  ▸ Rear utility manifold / exhaust ports
  ▸ Modular connection seams (visible clamps/fasteners)
  ▸ Thick-walled pressure housing sections
[Blueprint (description + category "harvester") + Visual Definition Template field definitions]

[MATERIAL APPEARANCE — from Design System]
  Primary exterior: Matte industrial composite (dark grey, non-reflective)
  Structural hardware: Brushed aluminum / steel (silver-grey, tool marks visible)
  Pressure vessels: Matte industrial composite with metallic seal rings + reinforced ribs
  Tracks/chassis: Heavy cast steel, worn surface, abrasive texture at load points
[Design System Summary (Session 6 manufacturing methods + Session 8 material specs)]

[COLOR PROFILE]
  Hull panels:    White (#F0F0F0) / Dark grey (#3A3A3A) — alternating hazard zones
  Tracks/chassis: Cast steel grey (#5C5C5C) with wear darkening
  Hazard stripes: Yellow (#E8C600) / Black (#1A1A1A)
  Pressure seals: Metallic silver (#B0B0B0)
  Exhaust/vents:  Heat-darkened steel (#4A3A2A)
[Design System Summary (Session 6 color families + Session 8 material patterns)]

[TECH LEVEL] Mk1–Mk2 Bootstrap/Frontier — visible fasteners, thick members, exposed systems, layered construction. [Design System Summary (Session 6 tech levels)]

───────────────────────────────────────────────────────
PASS 1 — GAMEPLAY SPRITE ASSETS (Transparent Background)
───────────────────────────────────────────────────────

Generate a transparent-background (alpha channel) image of the RH-400 Regolith Harvester Rover.

CAMERA RULES:
  - Fixed orthographic camera (no perspective distortion)
  - Identical camera position, focal length, and framing for ALL asset variants
  - Subject centered on canvas with consistent padding (15% margin all sides)
  - Pivot point: geometric center of the chassis base (tracks contact plane)

ORIENTATION:
  - Primary view: side profile (left-facing), tracks horizontal, intake forward
  - All variants use identical orientation — no rotation between frames

DIMENSIONS (constraints, not suggestions):
  Length-to-width ratio must be exactly 6.80 : 3.30 = 2.06 : 1
  Height-to-width ratio must be exactly 2.65 : 3.30 = 0.80 : 1

LIGHTING:
  - Cool, even, diffused studio lighting (no harsh shadows)
  - No baked ground plane, no environment shadows, no dust, no terrain
  - Subject floats on transparent alpha — isolated subject only

SUBJECT DESCRIPTION:
  A heavy-duty mobile industrial rover designed to skim planetary surfaces and gather regolith.
  The vehicle has a low-profile tracked chassis (6.80m long × 3.30m wide × 2.65m tall).
  Forward section: wide regolith-skimming intake scoop with exposed mechanical linkage.
  Mid-body: cylindrical pressure processing hopper with reinforced ribs and metallic seal rings.
  Rear section: utility manifold with exhaust ports, thermal vents, and modular connection clamps.
  Hull panels alternate white/dark-grey in hazard zones. Yellow/black hazard stripes on high-visibility edges.
  Tracks are heavy cast steel with visible wear. Chassis is exposed — thick structural members visible beneath hull.
  All surfaces show DMLS matte industrial finish — manufactured, not polished or futuristic.

RECOGNITION FEATURES (MUST appear in every frame):
  1. Heavy tracked undercarriage
  2. Forward regolith intake scoop
  3. Mid-body cylindrical pressure hopper with ribs
  4. Rear utility manifold with exhaust ports
  5. Modular clamps/fasteners at panel seams
  6. Yellow/black hazard stripes on edges

───────────────────────────────────────────────────────
VARIANT SPECIFICATIONS — All share identical camera/scale/pivot/orientation
───────────────────────────────────────────────────────

VARIANT A — Surface Sprite (Idle):
  Side profile, static pose, all systems closed. No moving parts extended.
  Background: transparent alpha.

VARIANT B — Moving Frame:
  Side profile, tracks slightly rotated to imply motion. Intake scoop lowered to ground plane (but no ground rendered).
  Exhaust ports show subtle thermal discoloration.
  Background: transparent alpha.

VARIANT C — Harvesting Frame:
  Side profile, intake scoop extended forward with mechanical linkage visible.
  Processing hopper shows active state (cooling vents open, slight heat glow on seals).
  Material flow implied through visible intake mechanism geometry.
  Background: transparent alpha.

VARIANT D — Damaged State:
  Same base model as Idle, with progressive damage:
  - One hull panel dented/detached (exposing internal structure)
  - Exhaust port darkened/soot-stained
  - One track segment missing/broken
  - Hazard stripes partially faded/scuffed
  - No fluid leaks or smoke (clean damage for sprite layering)
  Background: transparent alpha.

VARIANT E — Destroyed State:
  Same base model, advanced degradation:
  - Hull panels warped/missing (exposed internal framework)
  - Intake scoop bent/crushed
  - Both tracks broken/seized
  - Pressure hopper dented with visible seal failure
  - Exposed wiring/hydraulics hanging loose
  - Panel fragments detached and floating near body
  Background: transparent alpha.

───────────────────────────────────────────────────────
GLOBAL CONSTRAINTS (ALL VARIANTS)
───────────────────────────────────────────────────────

✘ NO terrain, ground plane, dust field, or baked environment
✘ NO text, labels, annotations, or UI elements
✘ NO perspective distortion — orthographic only
✘ NO location-specific context (Luna/Mars/Venus neutral)
✓ Identical camera position across all 5 variants
✓ Identical scale/pivot point across all 5 variants
✓ Identical orientation across all 5 variants
✓ Transparent alpha background on all 5 variants
✓ Isolated subject — no shadows cast onto anything
✓ Consistent lighting (cool, diffused studio) across all 5 variants
✓ Reusable game sprite ready for surface tile placement

───────────────────────────────────────────────────────
```

---

## Deliverable 2: Source Mapping

| Prompt Section | Source Document | Field Used |
|---|---|---|
| Context / Philosophy | VISUAL_PHILOSOPHY.md (9 principles) | Grounded, Functional, Industrial, Human, Simulation-First, Location-Agnostic |
| Asset Name & ID | Blueprint (`regolith_harvesting_rover_bp.json`) | `name`, `id` |
| Physical Specs (L×W×H×Mass) | Blueprint | `physical_properties.length_m`, `width_m`, `height_m`, `empty_mass_kg` |
| Aspect Ratios | Blueprint | Derived from `length_m / width_m`, `height_m / width_m` |
| Asset Class | ASSET_GENERATION_ARCHITECTURE.md | Gameplay Assets table (sprite_sheet, animation_keyframes, damage_states) |
| Manufacturing Style | Design System Summary → Session 6 | Bootstrap/Frontier method (thick members, visible structure) |
| Aesthetic Profile | RH-400 Prompt Template (`rh400-prompt-template.md`) | "white/dark-grey hull, hazard stripes, exposed tracks/heavy chassis, DMLS matte industrial finish" |
| Recognition Features | Blueprint (description + category) + Visual Def. Template spec | Inferred from `category: "harvester"` + `description` text — **no dedicated recognition_features array exists** |
| Material Appearance | Design System Summary → Session 6 + Session 8 | Six manufacturing methods visual language; Session 8 material specification patterns |
| Color Profile | Design System Summary → Session 6 | Icon Bible color families (by category) — **Icon Bible file not present in workspace** |
| Tech Level | Design System Summary → Session 6 | Mk1–Mk2 Bootstrap/Frontier progression |
| Camera Rules | ASSET_GENERATION_ARCHITECTURE.md + RH-400 Prompt Template | Fixed orthographic, consistent framing, pivot point |
| Lighting | RH-400 Prompt Template + Design System Summary → Session 2 | Cool, even, diffused studio lighting; no baked shadows |
| Background Rules | ASSET_GENERATION_ARCHITECTURE.md + RH-400 Prompt Template | Transparent alpha, isolated subject, no terrain/ground/dust |
| Variant Definitions | ASSET_GENERATION_ARCHITECTURE.md (Gameplay Assets table) | sprite_sheet, animation_keyframes, damage_states mapped to 5 variants |
| Global Constraints | ASSET_GENERATION_ARCHITECTURE.md + RH-400 Prompt Template | No terrain, no text, orthographic only, location-neutral |

---

## Deliverable 3: Gap Analysis

### Critical Gaps (Required for Full Automation)

| # | Missing Field | Why It's Needed | Expected Source |
|---|---|---|---|
| 1 | `recognition_features` array in blueprint or Visual Definition | Currently inferred from description text. No structured, machine-readable list of required visual elements exists for RH-400. | Visual Definition (template exists but not instantiated for RH-400) |
| 2 | `color_profile` with Icon Bible color family mapping | Design System Summary references Icon Bible color families, but the Icon Bible file (`2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md`) **does not exist in the workspace**. Color values were derived from RH-400 prompt template notes. | Icon Bible (missing from workspace) |
| 3 | `technology_level` field in blueprint | Blueprint has no tech level field. Tech level was inferred from category + manufacturing style. | Blueprint (field missing) |
| 4 | `manufacturing_style` field in blueprint | No structured manufacturing style field exists. Inferred from Design System Session 6 defaults. | Blueprint (field missing) |
| 5 | `asset_family` / `component_class` in canonical format | Blueprint has `category: "harvester"` and `type: "craft"`, but no Icon Bible canonical ID (e.g., `VEHICLE_ROVER_HARVESTER_RH400`). | Visual Definition or Asset Registry |
| 6 | `animation_profile` for harvester class | No animation profile defined for harvesters in any existing document. Session 6 mentions animation language exists in Icon Bible, but Icon Bible is missing. | Icon Bible / Visual Definition |
| 7 | `complexity_levels` (L0–L5) definitions for RH-400 | ASSET_GENERATION_ARCHITECTURE.md references complexity levels, but no RH-400-specific complexity progression exists. | Visual Definition |
| 8 | `shared_components` registry entry | ASSET_GENERATION_ARCHITECTURE.md mentions shared components, but no RH-400 component reuse map exists (e.g., "hull panels shared with X class"). | Shared Components Registry |

### Moderate Gaps (Would Improve Automation)

| # | Missing Field | Why It's Needed | Expected Source |
|---|---|---|---|
| 9 | `silhouette` description | Required by Visual Def. template for quick recognition validation. Not present in any doc. | Visual Definition |
| 10 | `visual_priority` (primary/secondary/tertiary) | Determines which features get rendering priority at low complexity levels. Not defined. | Visual Definition |
| 11 | `surface_finish` canonical value | "DMLS matte industrial" exists only in RH-400 prompt template notes, not in a structured material library. | Material Library / Design System |
| 12 | `render_profiles` for RH-400 | ASSET_GENERATION_ARCHITECTURE.md defines render types generically, but no RH-400-specific render profile mapping exists. | Visual Definition |
| 13 | `camera_profiles` for ground vehicles | Camera rules were assembled from multiple docs (Architecture + RH-400 template), but no canonical camera profile for ground vehicles exists. | Icon Bible / Visual Definition |
| 14 | `design_constraints` specific to harvesters | Generic constraints exist in architecture docs, but harvester-specific constraints (e.g., "intake must be forward-facing") are not documented. | Visual Definition |
| 15 | Operational Data file for RH-400 | No `regolith_harvesting_rover_data.json` found in workspace. Runtime behavior data (speed, power draw, operational states) unavailable for animation frame differentiation. | Operational Data (file missing) |

### Low Priority Gaps (Nice-to-Have)

| # | Missing Field | Why It's Needed | Expected Source |
|---|---|---|---|
| 16 | `prompt_template_refs` | Links between Visual Def fields and prompt template variables not mapped. | Visual Definition |
| 17 | Damage state progression rules for harvesters | ASSET_GENERATION_ARCHITECTURE.md references damage states generically; harvester-specific degradation sequence not documented. | Icon Bible / Design Research Session 8 |
| 18 | Construction state progression | Not defined for harvesters (would be useful for build-sequence assets). | Visual Definition |

### Summary

- **Total gaps identified: 18**
- **Critical (8):** Recognition features, color profile, tech level, manufacturing style, canonical asset ID, animation profile, complexity levels, shared components
- **Moderate (7):** Silhouette, visual priority, surface finish, render profiles, camera profiles, design constraints, operational data
- **Low (3):** Prompt template refs, damage progression rules, construction state progression

### Root Cause Analysis

**Two primary blockers prevent full PromptBuilder automation:**

1. **No instantiated Visual Definition for RH-400** — The template (`VISUAL_DEFINITION_TEMPLATE.md`) is well-designed but no concrete Visual Definition file exists for this unit. All recognition features, color profiles, animation profiles, complexity levels, and other Visual Def fields must be inferred from the blueprint description and Design System defaults.

2. **Icon Bible file missing from workspace** — Referenced in multiple documents (`DESIGN_SYSTEM_SUMMARY.md`, `DESIGN_RESEARCH_INDEX.md`, `UNIFIED_ASSET_CATALOG_ARCHITECTURE.md`) but the actual file (`2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md`) does not exist in the workspace. This is the single largest gap — it would resolve color families, camera profiles, animation language, complexity levels, and manufacturing style defaults.

---

## Notes for Claude Review

Please review:

1. **Prompt completeness** — Does the assembled prompt contain all necessary information to generate consistent gameplay assets? Are any creative interpretations needed beyond what's documented?

2. **Gap prioritization** — Are the critical/moderate/low classifications accurate? Should any gaps be reclassified?

3. **Source attribution accuracy** — Are the source mappings correct? Did I attribute any field to a source that doesn't actually contain it?

4. **Missing sources** — Is there any canonical documentation I missed that would reduce the gap count?

5. **Visual Definition instantiation priority** — Which Visual Def fields should be created first for RH-400 to enable PromptBuilder automation?

---

*End of manual PromptBuilder validation test.*
