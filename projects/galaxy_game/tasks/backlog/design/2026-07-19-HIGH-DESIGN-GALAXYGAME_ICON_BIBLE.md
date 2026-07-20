---
date_created: 2026-07-19
date_modified: 2026-07-19
status: backlog
priority: HIGH
type: DESIGN
component: Asset System Architecture
phase: Design System Infrastructure
relates_to:
  - 2026-07-19-MEDIUM-FEATURE-UNIT-SPRITE-ASSET-GENERATION (blocks this task)
  - 2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION (companion document)
  - UNIFIED_ASSET_CATALOG_ARCHITECTURE.md (architectural principle)
---

# GalaxyGame Icon Bible — Visual Language Definition

## Executive Summary

The Icon Bible defines **HOW everything should look** — a stable, professional visual language that applies across all GalaxyGame assets. This document NEVER changes once approved (barring major redesigns). Asset cataloging and growth happens in companion documents (Resource Catalog, Unit Catalog, etc.).

**Companion Document**: Asset Registry Specification (`2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION.md`) defines **WHAT assets exist** and serves as the single source of truth for all game content.

---

## Core Principle: Location-Agnostic, Canonically Organized

**ChatGPT Insight**: Resources named by TYPE, not location. Manufacturing origin, not planet of origin, determines visual variation.

- ❌ NEVER: "Lunar Regolith", "Mars Habitat", "Venus Solar Panel"
- ✅ ALWAYS: "Regolith", "Habitat", "Solar Panel Mk1" (then note if "Lunar Printed" or "Earth Manufactured")
- Location (where resource occurs) → game data JSON only
- Manufacturing origin (how it was built) → visual language (subtle artwork variation)
- Performance (how it works) → simulation parameters only

**Example**: 
```
Iron (resource)
├─ Earth Manufactured → cast/rolled appearance
├─ Lunar Printed → additive layer lines
├─ Asteroid Refined → rough, unpolished
└─ All reference same canonical "RES_METAL_IRON" asset ID
    Performance varies by planet (simulation), appearance varies by origin (visual)
```

---

## 1. Asset Hierarchy

All GalaxyGame assets organize into these categories (NEVER mixed):

### UI Assets
- Icons (32px, 64px, 256px sizes)
- Buttons (interactive elements)
- Panels (frames, backgrounds)
- Status Indicators (damage, lock, research, etc.)

### Resources
- Gases (O₂, N₂, CO₂, CH₄, H₂, He, Ar, Ne, Kr, Xe, SO₂, H₂S, NH₃, O₃, CO)
- Liquids (Water, Acids, Ammonia, Methane, Oils, etc.)
- Ices (Water Ice, Dry Ice, Methane Ice, Nitrogen Ice, Ammonia Ice)
- Elements (Fe, Al, Ti, Si, C, Ni, Cr, Mg, Cu, Li, Na, K, Ca, S, P)
- Minerals (Regolith, Basalt, Granite, Quartz, Ilmenite, Olivine, Feldspar, etc.)

### Manufactured Items
- Materials (Polymer, Plastic, Kevlar, Carbon Fiber, Composite, Glass, Ceramic, Steel, Alloys)
- Components (Pipes, Cables, Frames, Vessels, Airlocks, Tanks, Batteries, etc.)
- Electronics (Boards, CPUs, Memory, Sensors, Capacitors, Diodes, LEDs, etc.)

### Units (Mobile Equipment)
- Extractor (mining)
- Habitat (living quarters)
- Fabricator (manufacturing)
- Computer (data center)
- Battery (energy storage)
- Propulsion (thruster/engine)
- Storage Depot (cargo)
- Robot (autonomous worker)

### Structures (Stationary Installations)
- Power Plants (solar, thermal, nuclear)
- Labs/Research Facilities
- Habitats/Hubs (Inflatable, Umbilical, etc.)
- Mining Operations
- Processing Facilities
- Storage Depots
- Docks/Landing Pads
- Greenhouses/Agricultural

### Vehicles (Craftable Mobiles)
- Rovers (wheeled)
- Harvesters (extraction)
- Ships/Shuttles (spacecraft)
- Satellites (orbital)
- Tugs/Tenders (specialized)

### Organizations
- Faction Logos
- Corporation Symbols
- Headquarters Markers

### Technology/Research
- Tech Tree Icons (Mining, Habitat, Manufacturing, Agriculture, Energy, Propulsion, Sensors, Medical, etc.)

### Map Symbols
- Terrain overlays
- Settlement markers
- Resource deposits
- Trade route indicators

---

## 2. Resource vs. Item Distinction (Critical)

**RESOURCES** (raw materials):
- Iron (Fe)
- Water
- Regolith
- Oxygen (O₂)

Design: Simple, minimal, focus on substance. Icon only shows "what it is."

**ITEMS** (manufactured/crafted products):
- Steel Beam (made from Fe + C)
- Inflatable Habitat (made from Polymer + Electronics)
- Solar Panel Mk2 (made from Silicon + Copper + Electronics)

Design: More complex, shows manufacturing (rivets, welds, bolts, additive lines). Icon shows "how it was made."

**UNITS** (mobile equipment):
- Extractor (equipment type)
- Rover (vehicle type)

Design: Sophisticated, shows functionality and tech level. Icon shows "what it does."

**STRUCTURES** (stationary installations):
- Power Plant (solar/thermal/nuclear type)
- Mining Operation (facility type)

Design: Context-aware, shows integration with environment. Icon shows "where it works."

This hierarchy prevents confusion and allows each category to have its own visual language.

---

## 3. Visual Language Rules (Stable, Unchanging)

### Color Families (Semantic Meaning)

| Color | Meaning | Examples |
|-------|---------|----------|
| **Blue** | Atmosphere/Gas | O₂, N₂, CO₂, He, Ne, Kr, Xe |
| **Cyan** | Water/Liquid | Water, Heavy Water, Brine, Acids |
| **Gray** | Stone/Minerals | Regolith, Basalt, Quartz, Clay |
| **Silver** | Metals/Elements | Iron, Aluminum, Titanium, Copper |
| **Orange** | Fuel/Energy | Hydrogen, Methane, Hydrazine, RP-1, LOX |
| **Yellow** | Electricity/Power | Electricity, Heat, Battery cells |
| **Green** | Biology/Organic | Algae, Seeds, Crops, Biomass |
| **Purple** | Rare/Advanced | Rare elements, advanced tech |
| **Red** | Waste/Hazard | Toxic waste, nuclear waste, scrap |
| **Brown** | Manufactured/Organic | Wood, Concrete, ceramics, soil-based |
| **Black** | Carbon/Tech | Carbon fiber, advanced materials |
| **White** | Electronics/Tech | Circuit boards, processors, LEDs |

**Rule**: Players after 20 hours stop reading labels — they recognize color first.

### Shape Language (Semantic, Never Changes)

| Shape | Meaning | Scale | Examples |
|-------|---------|-------|----------|
| **Circle** | Gas/Atmosphere | All sizes | All gases — O₂, N₂, CO₂ |
| **Droplet** | Liquid/Water | Small-medium | Water, acids, methanol |
| **Crystal/Rough Hex** | Ice/Mineral | All sizes | Ice types, regolith, ores |
| **Perfect Hexagon** | Metal/Element | All sizes | Iron, aluminum, copper |
| **Diamond** | Energy/Fuel | All sizes | Hydrogen, methane, electricity |
| **Rounded Square** | Manufactured/Tech | All sizes | Polymer, plastic, composite |
| **Leaf/Organic** | Biology/Living | All sizes | Seeds, crops, algae |
| **Triangle** | Waste/Broken | All sizes | Scrap, slag, toxic waste |
| **Microchip** | Electronics | Small-medium | CPUs, boards, sensors |
| **Cube** | Cargo/Container | All sizes | Storage units, cargo containers |

**Rule**: Shape is the primary visual identifier. After shape recognition, color refines it.

### Material Library (Specific, Reference-Based)

Every rendered asset uses one of these material definitions:

**Metals**:
- Brushed Aluminum (satin finish, light gray)
- Cast Steel (rough, dark gray with slight patina)
- Polished Titanium (reflective, silver-gray)
- Anodized Aluminum (colored metallic)
- Oxidized Copper (green/brown patina)

**Manufactured Materials**:
- 3D-Printed Regolith (layered texture, tan/brown)
- Woven Carbon Fiber (diagonal weave pattern, black)
- Extruded Plastic (smooth, slightly glossy)
- Inflatable Polymer (seams, ribbed, UV-resistant appearance)
- Cast Concrete (rough aggregate, gray)

**Advanced Materials**:
- Nanofabricated Composite (seamless, advanced sheen)
- Automated Fusion Welded (perfect joints, no visible seams)

**Natural Materials**:
- Raw Silicon (slightly crystalline, pale gray)
- Ceramic (matte, hard appearance)
- Organic Compounds (slightly varied texture, alive)

**Electronic Elements**:
- Silicon Wafer (patterned, iridescent reflections)
- Copper Trace Patterns (fine golden lines on substrate)
- LED Surface (bright point-source, directional)

**Rule**: Every asset references ONE of these materials. Consistency enforced by asset generation system.

### Lighting Rules (Standard, Unchanging)

- **Light Direction**: 45° from top-left (consistent across all assets)
- **Key Light**: Strong (provides primary shadow)
- **Rim Light**: Subtle (defines edges, 10-15% intensity)
- **Fill Light**: Soft (prevents pure black shadows, 30% of key intensity)
- **Ambient Occlusion**: Present (crevices/joints darker, enhances depth)
- **Drop Shadow**: 2-3px, 40% opacity, slight blur (grounds object)
- **Specular Highlights**: Material-dependent (shiny metal has strong, matte plastic subtle)

**Rule**: Every asset lit identically. Players learn "light from top-left = standard orientation."

### Border & Edge Rules

- **Line Weight**: 1-2px depending on asset size
- **Anti-Aliasing**: Always applied (no jagged edges)
- **Soft Edges**: 0.5px feather on exterior boundaries
- **Hard Edges**: Maintained for technical/manufactured items
- **Rounded Corners**: 2-4px for UI elements, minimal for mechanical items

**Rule**: Technical consistency prevents visual noise.

### Size & Export Standards

- **SVG Source**: All assets start as scalable vector
- **24px**: Compact UI (inventory slots, list items)
- **32px**: Standard UI (toolbar, buttons, small icons)
- **64px**: Detail UI (hover tooltips, item inspect)
- **128px**: Medium illustration (tech tree, encyclopedia)
- **256px**: Full quality (surface units, detailed inspect view)

**Rule**: All sizes derived from single SVG — no manual resizing per resolution.

### Transparency Handling

- **UI Context**: Transparent checkerboard background (shows composability)
- **In-Game Context**: Solid gradient background (bottom darkens slightly)
- **Alpha Ranges**: Fully opaque (1.0) for primary asset; status overlays use 0.7-0.8

---

## 4. Tech Level Progression (Mk1 → Mk5)

Every item/unit/structure has tech level variants. Visual progression must be clear:

### Mk1 (Basic)
- **Appearance**: Simple, painted, bolted
- **Finish**: Matte
- **Assembly**: Visible fasteners (bolts, welds)
- **Materials**: Cast/rolled stock
- **Detail**: Minimal, functional

### Mk2 (Standard)
- **Appearance**: Cleaner, welded
- **Finish**: Slight sheen
- **Assembly**: Fewer visible fasteners
- **Materials**: Better alloys, controlled
- **Detail**: More refined, fewer rough edges

### Mk3 (Advanced)
- **Appearance**: Integrated systems
- **Finish**: Polished, professional
- **Assembly**: Nearly seamless
- **Materials**: Specialized alloys, precision-cast
- **Detail**: Smooth, streamlined, no visible joints

### Mk4 (Automated/AI)
- **Appearance**: Futuristic, optimized
- **Finish**: Metallic, sometimes iridescent
- **Assembly**: Perfectly seamless
- **Materials**: Nanofabricated, exotic
- **Detail**: Organic curves, advanced structure

### Mk5 (Late-Game/Mastery)
- **Appearance**: Otherworldly, elegant
- **Finish**: Living metal appearance, responsive-looking
- **Assembly**: Impossible to see how it works
- **Materials**: Post-human manufacturing
- **Detail**: Hyperfocus detail, quantum precision

**Rule**: Progression visible at a glance. Players immediately recognize which tech level by silhouette + finish.

---

## 5. Manufacturing Origin Variation (Instead of Location)

Instead of planet-specific assets, show manufacturing method:

| Origin | Visual Indicator | Examples |
|--------|-----------------|----------|
| **Earth Manufactured** | Rolled/cast finish, slight oxidation | Iron Ore, Aluminum Sheet |
| **Lunar Printed** | Additive layer lines, precise geometry | 3D-Printed Regolith, Lunar Substrate |
| **Orbital Fabricated** | Seamless, microgravity smoothness | Orbital Composites, Welded Frames |
| **Asteroid Refined** | Rough, unpolished, slightly chaotic | Raw Metals, Unprocessed Ore |
| **Automated Factory** | Perfect precision, no human touch | Circuit Boards, Advanced Alloys |
| **Nanofabricated** | Impossible geometric precision | Quantum Materials, Post-Human Tech |

**Example**: 
```
Steel Beam
├─ Earth Manufactured → rolled surface, slight scale oxidation
├─ Lunar Printed → layer lines visible, perfect geometry  
├─ Orbital Fabricated → seamless welding, zero imperfections
└─ Each uses same base design, different material finish applied
```

**Rule**: Manufacturing origin affects visual finish ONLY, not fundamental design. Players recognize "this is a Steel Beam, made on Luna" not "this is a different asset."

---

## 6. Asset ID System (Canonical, Stable)

All assets identified by ID, never by filename. Filenames can change; IDs do not.

### ID Format: `[CATEGORY]_[TYPE]_[NAME]_[VARIANT]`

**Examples**:
```
RES_GAS_OXYGEN          (resource: gas, oxygen, no variant)
RES_GAS_OXYGEN_PURE     (resource: gas, oxygen, pure variant)
RES_LIQUID_WATER        (resource: liquid, water)
RES_METAL_IRON_MK1      (resource: metal, iron, tech level 1)

ITEM_MATERIAL_STEEL_BEAM        (manufactured: material, steel, beam)
ITEM_COMPONENT_AIRLOCK_MK2      (manufactured: component, airlock, Mk2)
ITEM_ELECTRONICS_CIRCUIT_BOARD  (manufactured: electronics, circuit board)

UNIT_EXTRACTOR          (unit type: extractor)
UNIT_HABITAT_MK3        (unit type: habitat, Mk3)
UNIT_ROVER_LUNAR        (unit type: rover, lunar variant [optional])

STRUCT_POWER_SOLAR_MK1  (structure: power, solar, Mk1)
STRUCT_LAB_RESEARCH_MK2 (structure: lab, research, Mk2)

VEHICLE_ROVER_MK1       (vehicle: rover, Mk1)
VEHICLE_SHIP_CARGO_MK3  (vehicle: ship, cargo type, Mk3)

TECH_MINING_MK2         (technology: mining, Mk2)
TECH_HABITAT_MK3        (technology: habitat, Mk3)

ORG_CORPORATION_ICC     (organization: corporation, ICC)
```

**Rule**: All code, JSON, and systems reference IDs. Filenames are documentation only.

---

## 7. Production Pipeline (Repeatable Workflow)

Every asset follows this pipeline. Artists/AI systems follow it in order:

```
1. Icon Bible
   ↓
2. Asset Registry (defines what exists)
   ↓
3. Concept Sketch
   (Quick pencil/digital sketch proving concept)
   ↓
4. Flat Icon
   (SVG vector, all sizes generated from single source)
   ↓
5. Detailed Icon
   (Enhanced version with material/shadow detail)
   ↓
6. Blueprint View
   (Technical drawing for crafting/research screens)
   ↓
7. 3D Render
   (Isometric or 3D view for manufacturing/construction)
   ↓
8. Animation
   (Idle animation, interaction animations if needed)
   ↓
9. Game Integration
   (Sprite sheet, canvas export, in-game testing)
   ↓
10. Deployed
    (Committed to asset library, referenced by ID in all systems)
```

**Rule**: Every asset goes through all 10 stages. No shortcuts. Consistency enforced by process.

---

## 8. Status Overlays (Applied Over Base Asset)

Status indicators are OVERLAYS, not separate assets:

- **Damaged**: Red X (20° angle) overlaid with 60% opacity
- **Locked**: Lock icon in top-right, 40% opacity
- **Low Stock**: Yellow warning triangle, top-left corner
- **Research Incomplete**: Question mark, center, 50% opacity
- **Tech Level**: Star count (1-5 stars) below asset, gold color
- **Empty/Depleted**: Asset faded to 50% opacity + diagonal stripes pattern
- **In Transit**: Animated glow (1-2px halo, pulsing)
- **Powered Off**: Asset grayed to 60% saturation
- **Malfunction**: Red warning symbol, blinking (animated)

**Rule**: Base asset NEVER changes. Overlays layer on top. Keeps asset definition separate from state.

---

## Acceptance Criteria

**Icon Bible Acceptance** (this document):
- [ ] All 8 sections complete and documented
- [ ] Color families defined with semantic meaning
- [ ] Shape language established (circle→gas, droplet→liquid, hex→metal, etc.)
- [ ] 12+ material definitions documented with visual examples
- [ ] Lighting standard documented (45° top-left, consistent shadows)
- [ ] Tech level progression (Mk1-Mk5) defined visually
- [ ] Manufacturing origin variations documented (not location-based)
- [ ] Asset ID format established and examples provided
- [ ] 10-step production pipeline defined
- [ ] Status overlay system documented

**Related Task**: Asset Registry Specification (`2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION.md`) catalogs WHAT assets exist using this Bible as template.

**Quality Bar**:
- Professional game studio standard (reference: Factorio, Dyson Sphere Program, Satisfactory)
- Consistent lighting/color/shape across 300+ planned assets
- New artist/AI system can follow pipeline and produce consistent output
- No location-specific duplicates; all variations by manufacturing origin or tech level
- Players recognize visual language without reading labels (after 20 hours)

---

## Related Documentation

- **UNIFIED_ASSET_CATALOG_ARCHITECTURE.md**: Architectural principle (location-agnostic, canonical naming)
- **2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION.md**: Catalog of what exists (uses this Bible as template)
- **2026-07-19-MEDIUM-FEATURE-UNIT-SPRITE-ASSET-GENERATION.md**: First sprites follow this Bible's design rules

---

## Notes

This Icon Bible is **stable and rarely changes**. It answers: **"How should everything look?"**

Companion Asset Registry answers: **"What assets exist?"** and grows continuously as development adds new resources, units, structures, etc.

Together, Icon Bible + Asset Registry = complete definition of all GalaxyGame visual assets.
