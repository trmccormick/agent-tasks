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
source_materials:
  - 10 ChatGPT design review sessions (July 2026)
  - Analysis: Asset categorization, visual consistency, AI generation standards
---

# GalaxyGame Icon Bible — Visual Language Definition

## Executive Summary

The Icon Bible defines **HOW everything should look** — a stable, professional visual language that applies across all GalaxyGame assets. This document is foundational to a broader **GalaxyGame Art Bible** (which will eventually include Material Bible, Color Bible, Typography, Animation Language, and Prompt Standards). This Icon Bible NEVER changes once approved (barring major redesigns). Asset cataloging and growth happens in companion documents (Resource Catalog, Unit Catalog, etc.).

**Companion Document**: Asset Registry Specification (`2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION.md`) defines **WHAT assets exist** and serves as the single source of truth for all game content.

**Broader Context**: This Icon Bible is the foundational visual philosophy. As the project matures, it will become Section 2 of a complete Art Bible that includes Material Standards, Typography, Animation Rules, Corporate Branding, and AI Prompt Templates.

---

## Research Sources: 10 ChatGPT Design Review Sessions (July 2026)

This Icon Bible synthesizes insights from **10 ChatGPT design review sessions** focusing on:

1. **Asset Categorization** — Distinction between Resources → Items → Equipment → Structures → Vehicles (not monolithic "units")
2. **Location-Agnostic Naming** — Resources named by TYPE, manufacturing origin for visual variation (not planet-specific asset duplication)
3. **Visual Language Stability** — Shape language (circle→gas, droplet→liquid, hex→metal) that survives player ignorance of labels
4. **Material Consistency** — Defined material library with lighting/finish standards applied uniformly
5. **Tech Level Progression** — Clear Mk1→Mk5 visual evolution showing capability without separate asset creation
6. **Animation Standardization** — Prevent drift by defining animation rules per category (pulse, ripple, flicker, blink, etc.)
7. **Damage State Model** — Physical degradation progression instead of separate damage assets
8. **Complexity Levels** — L0-L5 framework for different use cases (map symbol vs. promotional art) from single master asset
9. **Prompt Template Standardization** — Reduce AI generation drift through structured prompt structure
10. **Production Pipeline Formalization** — 11-stage repeatable workflow ensuring consistency across 300+ planned assets

All 10 principles inform this document's structure, acceptance criteria, and implementation guidance.

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
- Components (Pipes, Cables, Frames, Vessels, Airlocks, Tanks, Battery Modules, etc.)
- Electronics (Boards, CPUs, Memory, Sensors, Capacitors, Diodes, LEDs, etc.)

### Equipment (Mobile Production/Support)
- Extractor (mining equipment)
- Fabricator (manufacturing equipment)
- Life Support (atmospheric/pressure management)
- Battery Module (energy storage unit)
- Processing Equipment (refinement/conversion)

### Structures (Stationary Installations)
- Power Systems (solar, thermal, nuclear, reactors)
- Habitats (living quarters, hubs, umbilical stations)
- Labs/Research Facilities (science, engineering, medical)
- Mining Operations (surface extraction sites)
- Processing Plants (refinement, conversion)
- Storage Facilities (cargo, resources, inventory)
- Docks/Landing Pads (spacecraft operations)
- Greenhouses/Agricultural (food production)
- Factories/Manufacturing Plants

### Vehicles (Craftable Mobiles)
- Rovers (wheeled, surface exploration)
- Shuttles (spacecraft, atmospheric/orbital)
- Tugs (specialty transport, orbital mechanics)
- Satellites (automated orbital operations)
- Construction Vehicles (automated building equipment)

### Robots (Autonomous Equipment)
- Construction Robots
- Mining Robots
- Survey Robots
- Maintenance Robots
- Agricultural Robots

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

**Future**: This material library will eventually become its own **Material Bible** document as the project expands. For now, use these definitions as reference.

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

## 7. Tech Level Progression (Mk1 → Mk5)

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

## 8. Manufacturing Origin Variation (Instead of Location)

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

## 9. Asset ID System (Canonical, Stable)

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

## 6. Production Pipeline (Repeatable Workflow)

Every asset follows this pipeline. Artists/AI systems follow it in order:

```
1. Icon Bible (define visual language)
   ↓
2. Asset Registry (define what exists)
   ↓
3. Prompt Generation (using Prompt Template from Section 12)
   (AI system receives structured prompt with all rules)
   ↓
4. Concept Sketch
   (Quick pencil/digital sketch or AI draft proving concept)
   ↓
5. Flat Icon (L0-L3 versions)
   (SVG vector or base raster, all sizes generated from single source)
   ↓
6. Master Asset (L4 - Full Detail)
   (256px high-quality version with full material/lighting/animation definition)
   ↓
7. Complexity Scaling (L5 if needed)
   (Promotional/high-detail version if project requires it)
   ↓
8. Animation Integration
   (Apply animation rules from Section 9, test timing)
   ↓
9. Damage State Testing
   (Verify degradation levels apply correctly, visual progression clear)
   ↓
10. Game Integration
    (Sprite sheet, canvas export, in-game testing with actual geometry/lighting)
    ↓
11. Deployed
    (Committed to asset library, referenced by ID in all systems)
```

**Rule**: Every asset goes through all 11 stages. No shortcuts. Consistency enforced by process. Prompt Template (Section 12) guides AI generation automatically.

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

## 8.5. Animation Language (Consistency Across Assets)

Every asset category has a consistent animation style. This prevents stylistic drift and creates visual coherence:

| Category | Animation | Speed | Effect |
|----------|-----------|-------|--------|
| **Gases** | Slow pulse | 2-3s cycle | Breathing motion, ±10% scale |
| **Liquids** | Gentle ripple | 1-2s cycle | Wave motion across surface, vertical shimmer |
| **Solids/Minerals** | Slight rotation | 4-5s cycle | Slow 360° rotation or gentle rocking |
| **Energy/Electricity** | Quick flicker | 0.3-0.5s cycle | Brightness variation, ±30% opacity |
| **Electronics** | LED blink | 0.8-1.2s cycle | Single point-source brightens, fades (like indicator light) |
| **Biological** | Breathing/sway | 2-4s cycle | Organic expansion/contraction or gentle side-to-side |
| **Waste/Hazard** | Particle emission | 1-2s cycle | Subtle particles float upward, fade (smoke/steam effect) |
| **Vehicles** | Status lights | 0.5-1s cycle | Tiny running lights cycle (indicates operation) |
| **Structures** | Operational glow | 1-2s cycle | Faint halo intensifies/dims (life sign indicator) |
| **Manufacturing** | Assembly animation | 2-3s cycle | Components appear/disappear or rotate assembly (shows activity) |

**Rule**: Every asset plays ONE animation when visible in UI or game world. No random variations — consistency is key to visual language. Animations pause when asset is not in viewport.

---

## 8.6. Damage States (Physical Progression)

Instead of creating separate assets for each damage level, apply progressive visual degradation:

### Resources
- **No damage** (100% integrity) — pristine appearance
- Damage not applicable; resources don't degrade in inventory

### Items
- **Pristine** (100% integrity) — clean, manufacturer finish
- **Scratched** (75% integrity) — surface marks visible, minor wear
- **Dented** (50% integrity) — visible deformation, serious wear marks
- **Broken** (25% integrity) — cracked, severely degraded, barely functional appearance
- **Destroyed** (0% integrity) — crumbled, shattered, unusable scrap appearance

### Structures
- **Operational** (100% integrity) — full lighting, clean appearance, all features visible
- **Damaged** (75% integrity) — some panel damage, minor burn marks, lights flicker slightly
- **Critical** (50% integrity) — large structural damage, heavy scorch/rust marks, lights dim
- **Destroyed** (0% integrity) — collapsed/ruined, looks like burned-out husk, all lights dark

### Vehicles
- **Operational** (100% integrity) — clean, all lights active, sharp details
- **Smoke** (75% integrity) — light smoke particle emission from engine/damage area
- **Fire** (50% integrity) — visible flames/heavy smoke, critical damage appearance
- **Destroyed** (0% integrity) — wreckage, fire extinguished, hull integrity compromised, dark

**Implementation**: Damage states are achieved through:
- Opacity reduction (darkens asset)
- Desaturation (colors fade toward gray)
- Overlay application (burn marks, rust, corrosion patterns)
- Particle effects (smoke, fire, sparks)
- Animation speed reduction (slows or stops as damage increases)

**Rule**: Base asset never changes. Damage states layer effects on top. Smooth visual progression from pristine → destroyed creates intuitive "health" communication without complex asset management.

---

## 8.7. Visual Complexity Levels

Every asset can exist at multiple complexity levels depending on use case. Define each level upfront to guide AI generation and asset reuse:

| Level | Purpose | Size Range | Detail | Animation | Examples |
|-------|---------|------------|--------|-----------|----------|
| **L0** | Map symbols, terrain overlays | 16-24px | Silhouette only | None | Resource deposit marker, settlement dot |
| **L1** | Inventory slots, toolbar icons | 24-32px | Minimal detail, silhouette clear | Optional (pulse only) | Tool icons, resource stacks |
| **L2** | UI inspection panels | 64px | Basic material/color definition | Optional (gentle animation) | Item inspect view, character portrait |
| **L3** | Encyclopedia/wiki artwork | 128px | Full material detail, clear lighting | Optional (narrative animation) | Research encyclopedia entries |
| **L4** | Construction/manufacturing renders | 256px | Complete detail, shows assembly/operation | Yes (operational animation) | Surface unit rendering, crafting view |
| **L5** | Promotional artwork | 512px+ | Hyper-detailed, cinematic quality | Yes (cinematic animation) | Marketing materials, game trailers |

**Workflow**: 
1. Create L4 (256px) as master asset
2. Generate L0-L3 by downsampling/simplifying L4
3. Enhance L5 separately for promotional use
4. All levels reference same Material/Color/Shape language

**Rule**: Do not create separate assets per level. Scale and simplify the master asset. Consistency maintained automatically.

---

## 8.8. Prompt Templates for AI Generation

When generating assets with AI, use standardized prompt structure to minimize stylistic drift:

### Master Prompt Template

```
Subject: [ASSET_ID or asset name]
Category: [Resource|Item|Equipment|Structure|Vehicle|Robot|UI]
Material: [Material from Material Library]
Color Family: [Color from Color Families table]
Shape Language: [Circle|Droplet|Hex|Diamond|Square|Leaf|Triangle|Microchip|Cube]
Tech Level: [Mk1|Mk2|Mk3|Mk4|Mk5|N/A]
Manufacturing Origin: [Earth|Luna|Orbital|Asteroid|Factory|Nanofab]
Lighting: 45° top-left, key light strong, rim light subtle, fill light 30%, ambient occlusion present, 2-3px drop shadow
Background: [Transparent with checkerboard|Solid gradient bottom-darkened]
Animation: [None|Pulse|Ripple|Flicker|Blink|Breathe|Particles|Glow|Assembly]
Animation Speed: [Seconds per cycle or None]
Complexity Level: [L0|L1|L2|L3|L4|L5]
Output Size: [Pixels: 24px|32px|64px|128px|256px|512px]
Style Reference: [Factorio|Dyson Sphere Program|Satisfactory] (indicates professional sci-fi game quality)
Acceptance: Must match Icon Bible rules exactly. No deviations.
```

### Example Usage

```
Subject: RES_METAL_IRON
Category: Resource
Material: Cast Steel
Color Family: Silver
Shape Language: Perfect Hexagon
Tech Level: N/A
Manufacturing Origin: N/A (raw resource)
Lighting: 45° top-left, key light strong, rim light subtle, fill light 30%, no ambient occlusion (raw material)
Background: Transparent checkerboard
Animation: None
Complexity Level: L4 (then downscale to L0-L3)
Output Size: 256px
Style Reference: Factorio
Acceptance: Match Icon Bible rules exactly.
```

```
Subject: UNIT_ROVER_MK3
Category: Vehicle
Material: Polished Titanium + Inflatable Polymer
Color Family: Silver (metal) + White (fabric)
Shape Language: Rounded square overall, wheel details
Tech Level: Mk3
Manufacturing Origin: Earth Manufactured
Lighting: 45° top-left, strong specular highlight on metal, subtle on fabric, prominent ambient occlusion in wheel wells and joints
Background: Solid gradient, darker at bottom
Animation: Status lights (cycle, 0.8-1.2s)
Animation Speed: 1.0s per cycle
Complexity Level: L4 (in-game rendering quality)
Output Size: 256px
Style Reference: Satisfactory
Acceptance: Match Icon Bible and Mk3 progression rules exactly. No Mk2 or Mk4 characteristics.
```

**Rule**: Every AI generation request uses this template. Copy-paste, fill fields, submit. This standardization prevents prompt engineering drift and ensures outputs consistently follow Icon Bible rules.

---

## Acceptance Criteria

**Icon Bible Acceptance** (this document):
- [ ] All 8+ sections complete and documented
- [ ] Color families defined with semantic meaning
- [ ] Shape language established (circle→gas, droplet→liquid, hex→metal, etc.)
- [ ] 12+ material definitions documented with visual examples
- [ ] Lighting standard documented (45° top-left, consistent shadows)
- [ ] Tech level progression (Mk1-Mk5) defined visually
- [ ] Manufacturing origin variations documented (not location-based)
- [ ] Asset ID format established and examples provided
- [ ] 11-step production pipeline defined
- [ ] Status overlay system documented
- [ ] Animation language defined (pulse, ripple, flicker, blink, breathe, particles, glow, etc.)
- [ ] Damage state progression documented (pristine → destroyed)
- [ ] Visual complexity levels (L0-L5) defined with examples
- [ ] Prompt template provided for standardized AI generation
- [ ] Material Library noted as future expansion document

**Related Task**: Asset Registry Specification (`2026-07-19-HIGH-DESIGN-ASSET_REGISTRY_SPECIFICATION.md`) catalogs WHAT assets exist using this Bible as template.

**Quality Bar**:
- Professional game studio standard (reference: Factorio, Dyson Sphere Program, Satisfactory)
- Consistent lighting/color/shape across 300+ planned assets
- New artist/AI system can follow pipeline and produce consistent output
- No location-specific duplicates; all variations by manufacturing origin or tech level
- Players recognize visual language without reading labels (after 20 hours)

---

## Material Bible (Future Expansion)

**Note**: The Material Library section will eventually expand into its own **Material Bible** document as the project matures. It will include:

- Surface finish standards (brushed vs. polished vs. matte)
- Material-specific lighting rules (how metals shine vs. how plastics diffuse)
- Weathering/aging patterns (patina, corrosion, UV damage)
- Manufacturing-specific surface characteristics (3D printing layer lines, casting sand marks, etc.)
- Microscopic detail levels (when/how to show texture)
- Color value ranges for each material type (RGB/HSL/Hex standards)

For now, the Material Library in Section 3 is sufficient. As the asset catalog grows, this document will be split out for separate management.

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
