---
date_created: 2026-07-19
date_modified: 2026-07-19
status: backlog
priority: HIGH
type: DESIGN
component: Asset System Architecture
phase: Phase 2 - Asset Foundation
relates_to:
  - 2026-07-19-MEDIUM-FEATURE-UNIT-SPRITE-ASSET-GENERATION (blocks this task)
  - Design system foundation for all future asset work
---

# GalaxyGame Icon Bible & Design System

## Summary

Establish a **unified visual design language** for all GalaxyGame assets (resources, units, buildings, UI, vehicles, technology, achievements). Define master style guide to eliminate visual drift and enable systematic production of 250-400+ consistent icons.

This is foundational work — nothing created without it will look cohesive.

## Problem Statement

**Current State**: Ad-hoc asset generation creates visual inconsistency:
- Image model drifts between generations (hydrogen icon doesn't match O₂ style)
- No master color palette to enforce across assets
- No lighting/border/shadow rules documented
- Each asset regenerated independently risks re-drift
- Future artists/agents will recreate inconsistently

**Impact**: 
- Sprites look disjointed, not "from same game"
- Players can't develop visual language recognition
- Asset library will feel unprofessional and scattered

**Solution**: Document comprehensive design system BEFORE generating hundreds of assets.

---

## Scope: GalaxyGame Icon Bible

### 1. Master Style Definition

**Visual Language Rules** (applies to all assets):
- [ ] Color palette (primary colors, neutrals, accent ranges)
- [ ] Border thickness & style (px width, antialiasing rules)
- [ ] Lighting direction (angle, key light, rim light, fill light)
- [ ] Material textures (metallics, plastics, glass, organic)
- [ ] Shadow rules (drop shadow, inner shadow, umbra/penumbra)
- [ ] Corner radius standards (sharp vs. rounded by category)
- [ ] Transparency handling (checkerboard vs. solid bg, alpha ranges)
- [ ] Grid alignment (pixel-perfect snapping)
- [ ] Size standards (24px, 32px, 64px, 256px export targets)

### 2. Category Architecture

**Organize by Shape + Color + Symbol** (not just name):

| Category | Shape | Count | Examples | Color Range |
|----------|-------|-------|----------|------------|
| Gases | Circle | ~15 | O₂, N₂, CO₂, CH₄, H₂, He, Ar, Ne, Kr, Xe, SO₂, H₂S, NH₃, O₃, H₂O vapor | Blues, purples, grays, oranges |
| Liquids | Water droplet | ~15 | Water, Ammonia, Sulfuric acid, Heavy water, Dirty water, Waste water, Methanol, Ethanol, Oils | Cyans, teals, browns |
| Metals | Hexagon | ~20 | Iron, Steel, Aluminum, Titanium, Copper, Nickel, Chromium, Magnesium, Tungsten, Gold, Silver, Platinum, Zinc, Lead | Silvers, golds, coppers, grays |
| Minerals/Ore | Rough hexagon/crystal | ~20 | Regolith, Basalt, Ilmenite, Anorthosite, Glass, Silicon, Quartz, Salt, Sulfur | Grays, browns, whites, yellows |
| Construction | Rounded square | ~30 | Concrete, Carbon fiber, Kevlar, Polymer, Composite panels, Beams, Printed components, Insulation, Sealant | Tans, grays, blacks |
| Electronics | Microchip | ~30 | Circuit board, Sensor, CPU, Memory, AI core, Quantum processor, Transistor, Capacitor, Diode | Teals, purples, silvers |
| Fuels | Diamond | ~20 | Hydrogen, Methane, Hydrazine, LOX, RP-1, Nuclear fuel, Antifreeze, Coolant | Oranges, reds, yellows, blues |
| Food | Rounded leaf | ~30 | Wheat, Potato, Soy, Algae, Fish, Protein paste, Meals, Seeds, Spores, Herbs | Greens, browns, oranges |
| Biological | DNA/organic | ~20 | Seeds, Spores, DNA samples, Bacteria, Yeast, Cultures, Tissue samples | Greens, purples, pinks |
| Waste | Triangle/broken | ~30 | Scrap metal, Plastic waste, Carbon scraps, Toxic waste, Radioactive waste, Organic waste, Slag | Grays, reds, yellows, greens |
| **TOTAL** | — | **~250** | — | — |

### 3. Asset Categories (Beyond Resources)

Design language must extend to:
- **Units**: Extractor, Habitat, Fabricator, Computer, Battery, Propulsion, Storage, Robot (8 sprites)
- **Craft**: Rover, Harvester, Ship, Spaceship, Satellite (5 sprites)
- **Structures**: Umbilical hub, base fallbacks (3 sprites)
- **Buildings**: Factories, labs, habitats, power plants, storage, etc.
- **UI**: Buttons, toggles, status indicators, progress bars
- **Technology/Research**: Tech tree icons
- **Achievements**: Badge icons
- **Vehicle classes**: Excavators, transporters, science vessels
- **Corporation logos**: Color schemes by faction

**Consistency requirement**: A player looking at a unit sprite should immediately recognize it came from the same game as a resource icon or a building.

### 4. Icon Bible Contents

**Document to create**: `docs/design/GALAXY_GAME_ICON_BIBLE.md`

Should include:
```
## 1. Master Color Palette
- Primary colors (hex codes, RGB, HSL)
- Neutral grays (5-step range)
- Category color ranges (which colors for gases, metals, etc.)
- Forbidden combinations (colors to avoid)

## 2. Lighting Standard
- Light direction (angle degrees)
- Key light intensity
- Rim light color/intensity
- Fill light color/intensity
- Ambient occlusion rules

## 3. Border & Edge Rules
- Line weight (px for 32px icon, 256px icon)
- Anti-aliasing style
- Feathering rules
- Hard vs. soft edges by material

## 4. Shadow & Depth
- Drop shadow (offset, blur, opacity)
- Inner shadow (for insets)
- Emboss/deboss rules
- Realistic vs. stylized shadows

## 5. Material Library
- Metallic (roughness, specularity)
- Matte plastic (diffuse, no highlights)
- Glossy (sharp highlights)
- Glass (transparency, refraction simulation)
- Organic (texture, imperfections)

## 6. Category Shape Templates
- (SVG or reference images for each shape)
- Hexagon template (metals)
- Circle template (gases)
- Droplet template (liquids)
- Diamond template (fuels)
- etc.

## 7. Naming Convention
- File naming: `icon_[category]_[resource]_[size].png`
- Example: `icon_gas_oxygen_256.png`, `icon_metal_iron_32.png`
- SVG source: `icon_[category]_[resource].svg`

## 8. Size Standards
- 24px (compact/inventory)
- 32px (standard UI)
- 64px (detail/hover)
- 256px (full quality)
- SVG source (scalable)

## 9. Status Overlays
- Damaged (red X)
- Locked (lock icon)
- Low stock (warning)
- Research incomplete (question mark)
- Tech level (star count 1-5)
- Empty/used up (faded)

## 10. Stack Indicators
- Small "+N" badge for quantities
- Mini-stack preview overlay
- Color coding for rarity/tier

## 11. Category Reference
- Visual examples for each of ~250 resource types
- Color assignment per resource
- Symbol/glyph per resource
- Texture pattern (if any)
```

### 5. Deliverables

**Phase 1 (Design System Definition)**:
- [ ] Icon Bible document (comprehensive style guide)
- [ ] Master color palette (swatch file, hex codes)
- [ ] Shape templates (SVG or reference images)
- [ ] Lighting diagram (direction, intensity)
- [ ] Material samples (6-8 representative icons showing all materials)
- [ ] Category mapping (all 250+ resources assigned to category, color, shape)

**Phase 2 (Icon Production)** — separate task, depends on Phase 1:
- [ ] Generate/commission all ~250 resource icons
- [ ] Generate unit/craft/structure sprites (16 total)
- [ ] Generate building icons (20-50)
- [ ] Generate UI elements (buttons, status, etc.)
- [ ] Generate technology/achievement badges

---

## Acceptance Criteria

**Icon Bible Acceptance**:
- [ ] Document is comprehensive (covers all 11 sections above)
- [ ] Color palette defined and tested (no colors conflict)
- [ ] Lighting direction consistent and documented
- [ ] Border/shadow rules clear enough for agent/artist to follow
- [ ] 6-8 material samples created and approved (proves consistency)
- [ ] All 250+ resources categorized and assigned colors
- [ ] SVG templates available for each shape category
- [ ] Naming convention documented and unambiguous

**Quality Bar**:
- Any asset created using this bible should be **instantly recognizable** as "from GalaxyGame"
- Lighting should be consistent across all assets
- Colors should harmonize (not clash)
- No visual drift between generations
- Player can learn visual language (blue hexagon = metal, green circle = gas, etc.)

---

## Why This Matters

**Without Design System**:
- Each sprite generation drifts independently
- Players see inconsistency and perceive game as "unfinished"
- Future asset work requires redoing old assets for consistency
- No clear hiring/commissioning spec for external artists
- Asset library grows chaotic

**With Design System**:
- All future assets automatically cohere
- One consistent "look" across 300+ icons
- Clear spec for agents/artists to follow
- Systematic, repeatable production pipeline
- Feels like professional game asset library

---

## Timeline & Priority

**Recommendation**: Create this BEFORE generating unit sprites or resource icons.

**Why**:
1. Designing system takes ~4-6 hours
2. Generating 250+ icons without system takes months and still looks inconsistent
3. With system, generation becomes faster and more reliable
4. Any assets generated now without system will need rework later

**Priority**: HIGH — foundational to all Phase 2 asset work

---

## Related Tasks

- **Blocks**: 2026-07-19-MEDIUM-FEATURE-UNIT-SPRITE-ASSET-GENERATION (unit sprites should follow this system)
- **Precedes**: Resource icon generation (Phase 2 followup)
- **Precedes**: Building/UI icon generation (Phase 2+ followup)
- **Connects to**: Phase 2 Asset Registry planning

---

## Notes

- This is inspired by professional game design systems (Factorio, Dyson Sphere Program, Satisfactory, Oxygen Not Included)
- ChatGPT flagged style drift in image generation — this system prevents that
- Consider: This bible could also guide future code architecture (asset registry service that enforces naming/sizing conventions)
- Optional: Create automated validation tool that checks icons against bible rules (correct size, color, lighting consistency)
