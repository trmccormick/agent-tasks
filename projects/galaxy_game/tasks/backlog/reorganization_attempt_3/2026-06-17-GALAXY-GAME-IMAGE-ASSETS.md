# Galaxy Game — Image Assets Task File
**Created**: 2026-06-16
**Status**: In Progress — Asset collection and style guide documented. UI integration task pending.

---

## Overview

Galaxy Game requires two categories of visual assets:

1. **Corporate Logos** — in-universe megacorporation branding displayed in faction screens, lore documents, loading screens, and corporate terminals
2. **Structure & Unit Renders** — multi-angle photorealistic renders of buildable structures and components displayed in admin/player UI when viewing buildable items on a tile

Assets are generated via ChatGPT image generation. This document captures the established style guides, prompt templates, asset inventory, and pending work.

---

## ⚠️ Critical Design Distinction — Manufacturing Origin

All structure and unit assets must respect this visual hierarchy:

| Origin | Visual Style | Examples |
|---|---|---|
| **Earth-manufactured** | Clean, polished, NASA/SpaceX aesthetic — white panels, precision engineering, corporate finish, pristine | Cyclers, Earth-built hab modules, precision instruments, tugs (core hull) |
| **Field-built / ISRU** | Industrial, raw, regolith-printed, heavy, weathered — exposed trusses, rough texture, functional not elegant | Lunar surface structures, ISRU facilities, local construction, foundation blocks |
| **Hybrid (Cyclers & Tugs)** | Earth-built core with field-modified additions — clean hull sections showing wear, jury-rigged adaptors, patched sections, lived-in long-haul spacecraft | AstroLift HLT fleet, cyclers, tugs |

This distinction is fundamental to the game's world-building. A support column printed from regolith on Luna looks nothing like a precision instrument shipped from Earth.

---

## Corporate Logo Style Guide

### Master Prompt Template

Use this as the base for all corporate logo generation in ChatGPT:

```
Create a premium science-fiction corporate logo for [COMPANY NAME].

The logo should feel like an in-universe megacorporation operating in a mature interplanetary
civilization. This is not a modern Earth company logo. It should resemble branding seen on
spacecraft hulls, orbital stations, colony infrastructure, corporate terminals, mission patches,
and government contracts.

Visual Style
- Cinematic space-art logo
- Dark space background — deep black backdrop with stars, nebulae, and subtle cosmic lighting
- High contrast lighting
- Metallic typography
- Corporate but aspirational
- AAA video game faction logo quality
- Detailed illustration combined with logo design
- Clean composition suitable for loading screens, faction screens, and lore documents

Typography
- Large metallic company acronym
- Smaller full company name beneath
- Brushed aluminum or titanium appearance
- Subtle blue/orange illumination
- Strong readable corporate lettering
- Futuristic but practical
- Avoid cartoon fonts

Composition
- Top: Hero illustration representing the corporation's mission
- Middle: Large acronym
- Bottom: Full corporation name
- Background: Space environment relevant to corporation

Lighting
- Blue technological lighting
- Orange industrial lighting
- Bright focal glow near center
- Cinematic rim lighting
- Space-grade premium appearance

Quality
- Ultra detailed
- Sharp edges
- Professional branding
- Concept art quality
- Science fiction realism
- No cartoon style
- No fantasy elements
- No white background

Negative Prompt — Avoid:
White backgrounds, flat minimalist logos, modern startup branding, cartoon appearance,
excessive lens flare, fantasy elements, magic effects, cyberpunk clutter, abstract symbols
without context, empty backgrounds

Design Language Reference:
NASA + SpaceX + The Expanse + Mass Effect corporate design language
```

### Development Corporation Variant (append to master prompt)

For planetary/moon development corporations (LDC, MDC, VDC, TDC, SDC):

```
Depict a thriving settlement or infrastructure project representing humanity's expansion
into that world. The colony should appear established, functional, and economically
productive rather than exploratory.
```

### Per-Corporation Scene Guidance

| Corp | Full Name | Scene Elements | Palette |
|---|---|---|---|
| **LDC** | Lunar Development Corporation | Lunar city, regolith structures, lava tube infrastructure, Earth visible in sky, industrial lunar development | Blue-white |
| **MDC** | Mars Development Corporation | Martian settlement, domes and towers, red-orange landscape, terraforming infrastructure, heavy industry, frontier expansion | Red-orange |
| **VDC** | Venus Development Corporation | Floating cloud cities, aerostat habitats, golden clouds, atmospheric industry, airship traffic, elegant high-altitude civilization | Gold-amber |
| **TDC** | Titan Development Corporation | Methane seas, cryogenic industrial facilities, Titan haze, offshore platforms, hydrocarbon extraction | Orange-cyan |
| **SDC** ⚠️ | Saturn Development Corporation *(name TBD)* | Saturn dominates background, massive orbital infrastructure, multiple moons visible, interplanetary transport hub, system-wide authority | TBD |
| **AstroLift** | AstroLift | Space elevator, orbital transfer vehicles, Earth-to-orbit logistics, upward motion, infrastructure focus | — |
| **Vector Hauling** | Vector Hauling | Cargo freighters, industrial shipping, freight containers, working spacecraft, blue-collar space logistics | — |
| **Zenith Orbital** | Zenith Orbital | Orbital stations, corporate headquarters, premium orbital services, high-tech infrastructure, prestigious corporate image | — |
| **WTC** | Wormhole Transit Consortium | Wormhole gateway, interstellar transportation, traffic control, transit authority, galactic-scale infrastructure | Blue-white |
| **Wormhole Station** | Wormhole Station | Asteroid-converted station, excavated asteroid habitat, docking facilities embedded into rock, industrial frontier aesthetic, functional rather than elegant | — |
| **Terragen** | Terraforming Consortium | Terraformed world, lush green landscape, Earth visible, colony infrastructure, transformation of hostile world | Green |

---

## Corporate Logo Inventory

| Corp | Version | Status | Notes |
|---|---|---|---|
| LDC | v1 (badge style) | ✅ Saved | White background — older style |
| LDC | v2 (cinematic) | ✅ Saved | **Preferred** — dark background, cinematic |
| MDC | v1 (badge style) | ✅ Saved | White background — older style |
| MDC | v2 (cinematic) | ✅ Saved | **Preferred** — dark background, cinematic |
| VDC | v1 (badge style) | ✅ Saved | Black background |
| VDC | v2 (cinematic) | ✅ Saved | **Preferred** — cloud city platforms, more detailed |
| WTC | v1 (cinematic) | ✅ Saved | Wormhole portal + ship, strong |
| Terragen | v1 (cinematic) | ✅ Saved | Green terraformed world |
| TDC | — | ❌ Not yet generated | Use master prompt + TDC scene guidance |
| SDC | — | ❌ Not yet generated | ⚠️ Name TBD before generating |
| AstroLift | — | ❓ Generated, not reviewed | Retrieve from ChatGPT history |
| Vector Hauling | — | ❓ Generated, not reviewed | Retrieve from ChatGPT history |
| Zenith Orbital | — | ❓ Generated, not reviewed | Retrieve from ChatGPT history |
| Wormhole Station | — | ❓ Generated, not reviewed | Retrieve from ChatGPT history |

---

## Structure & Unit Asset Style Guide

Two distinct visual languages — never mix them within a single asset.

---

### Earth-Manufactured Technology (Clean / Precision)

**Core philosophy:** Earth-built equipment communicates precision manufacturing, aerospace quality control, expensive components, high reliability, modular maintenance. Designed in factories, not fabricated in the field.

**Feel:** NASA + SpaceX + Boeing + Lockheed Martin + industrial energy infrastructure

**Materials — always use:**
- Painted aerospace alloys
- White composite panels
- Gray structural elements
- Titanium-colored piping
- Thermal protection materials
- Clean machined surfaces
- Precision welds
- Bolted access panels

**Materials — never use:**
- Rough concrete textures
- Regolith textures
- Visible print layers
- Rust
- Improvised construction

**MK Version Modifiers:**

| Version | Description |
|---|---|
| **MK1** | First-generation production model with conservative engineering, exposed serviceable subsystems, overbuilt industrial construction, designed for reliability in extraterrestrial operations. NOT primitive — overbuilt. |
| **MK2** | Improved second-generation model with integrated systems, reduced mass, enhanced maintainability, optimized industrial design, and cleaner subsystem integration. |
| **MK3** | Mature industrial platform with highly integrated systems, modular upgrade architecture, refined aerospace engineering, and optimized operational efficiency. |

**Earth-Built Reusable Prompt Module:**
```
Earth-manufactured aerospace industrial hardware. Precision fabricated construction with
clean white and gray paneling, machined metal components, modular access panels, realistic
piping, aerospace-grade materials, NASA-inspired engineering, functional industrial design,
realistic near-future technology.
```

**Earth-Built Catalog Prompt Template:**
```
Clean product catalog render of a [UNIT NAME].

Show exactly two views:
- Front 3/4 view on the left
- Rear 3/4 view on the right

White background.
Subtle shadows.
No text. No labels. No logos. No infographic elements. No environment.

Earth-manufactured aerospace industrial hardware. Precision fabricated construction with
clean white and gray paneling, machined metal components, modular access panels, realistic
piping, aerospace-grade materials, NASA-inspired engineering, functional industrial design,
realistic near-future technology.

[FUNCTIONAL DESCRIPTION]

[MK VERSION DESCRIPTION]
```

**Earth-Built Unit Categories:**

| Category | Examples | Key Visual Cues |
|---|---|---|
| **Processing Equipment** | TEU, PVE, gas processors, refineries | Tanks, heat exchangers, piping, maintenance access doors |
| **Vehicles** | HLT, cargo shuttles, orbital tugs | Clean hulls, large access panels, docking ports, standardized interfaces |
| **Robotics** | Deployment, maintenance, construction robots | Articulated arms, modular tool heads, sensor packages, clean industrial housings |
| **Life Support** | Atmosphere processors, water recyclers, habitat support | Filtration systems, service panels, equipment racks |

---

### Lunar-Manufactured Technology (Industrial / Raw)

**Core philosophy:** Lunar-built asks "How do we build this locally?" Everything is printed from regolith, assembled in the field, built for function not elegance.

**Feel:** Megastructure construction, heavy industry, frontier engineering

**Master Prompt Template:**
```
Industrial component render of [COMPONENT DESCRIPTION].

Show multiple angles of the same object in a single image:
- Perspective view
- [Second angle — e.g. side profile, underside detail, front elevation]
- [Third angle — e.g. structural base detail, top-down view, close-up detail]

[Describe the object's function, scale, and construction method]

Material appearance:
- Rough lunar concrete-like texture / regolith composite
- Thick industrial trusses
- Layered 3D printed fabrication patterns
- Reinforced corners with embedded metal hardware

Style:
- Megastructure construction component / industrial lunar construction equipment
- Industrial and massive / rugged and practical
- Realistic lunar engineering
- No text or labels
```

---

### Hybrid Technology (Cyclers, Tugs)

Earth-built core with field-modified additions. Clean precision hull sections showing wear and adaptation — jury-rigged docking adaptors, patched sections, field-welded additions, lived-in long-haul spacecraft.

**Core ratio (Cycler economic model):** 85–95% Lunar by mass, 85–95% Earth by value. The vessel should visually read as a gigantic rough industrial Lunar structure with relatively small clusters of clean white Earth aerospace hardware attached — NOT a NASA spacecraft with trusses bolted on. Luna = regolith-printed/ISRU fabrication (no steel production yet — everything is 3D printed from local regolith composite). Earth = electronics factory.

**Future tech progression (not yet implemented):** Limited early metal processing exists, but proper ore mining and a smelter are planned later-game additions requiring significantly more power than the initial ISRU/regolith-printing setup. Until that arc is built, Lunar structural assets should remain regolith-composite only — no smelted/cast metal components in early-game asset prompts.

**Note on future metal processing:** Early-game Luna has limited metal processing capability only. A proper smelter and ore mine for real metal extraction is planned for later game phases, requiring significantly more power than the initial ISRU setup. Until that tech tier unlocks, all Lunar structural assets should depict regolith composite construction, not refined metal fabrication.

Prompt approach: Start with Earth-Built template for precision modules, then add:
```
Showing years of field service with field-modified additions — patched hull sections,
jury-rigged external systems, non-factory docking adaptors, lived-in long-haul
spacecraft aesthetic layered over the original precision engineering.
```

#### Cycler Component Catalog (20 components, split by manufacturing origin)

Cyclers are split into two catalogs reflecting the established worldbuilding rule: bulk mass is Lunar, precision hardware is Earth.

**Lunar-Built Structural Components (bulk mass, cheap, locally manufactured):**

| # | Component | Description |
|---|---|---|
| 1 | Structural Truss Segment | The "I-beam" of space — 3D printed regolith composite, standardized length, multiple connection ports, internal utility channels |
| 2 | Structural Ring Frame | Large circular reinforcement rings — connect truss sections, support rotating modules, mount utility conduits, attach armor panels |
| 3 | Hull Panel Section | Exterior shell pieces — printed from lunar materials, micrometeor protection, thermal shielding, replaceable, many variants |
| 4 | Utility Spine Section | Internal service corridor — pipes, cables, gas lines, data buses, runs through entire cycler |
| 5 | Docking Framework | Heavy structural support around ports (not the hardware itself) — printed on Luna |
| 6 | Cargo Lattice Module | Open-frame cargo bay — containers attach directly, no pressure vessel, extremely cheap |
| 7 | Radiation Shield Tank Segment | Large printed tank sections filled with water/regolith/ice/waste — passive shielding |
| 8 | Worldhouse Construction Segment | Future reuse of cycler tech — same structural pieces become valley roof supports, canyon enclosure supports, lava tube reinforcement (technology continuity) |

**Earth-Built Precision Modules (expensive, imported):**

| # | Component | Description |
|---|---|---|
| 9 | Life Support Core Mk1 | Air processors, CO2 scrubbers, water recycling, environmental control |
| 10 | Power Distribution Module | Transformers, power electronics, battery controls, grid management |
| 11 | Reactor Interface Module | Reactor controls, safety systems, cooling systems — connects to fission (later fusion) reactors |
| 12 | Avionics Module | Computers, AI systems, communications, navigation — the "brain" |
| 13 | Sensor Package | Radar, lidar, star trackers, telescope systems |
| 14 | Docking Port Assembly | Actual precision docking hardware — seals, alignment systems, latches, guidance sensors |
| 15 | Propulsion Control Module | RCS, station keeping, orbital corrections (not the engines themselves) |
| 16 | Cryogenic Storage Control Module | Controls LOX/LH2/methane/nitrogen — valves and instrumentation |

**Hybrid Modules (mostly Lunar-built shell, Earth hardware inside):**

| # | Component | Lunar Part | Earth Part |
|---|---|---|---|
| 17 | Habitat Drum | Structure, hull, shielding | Interiors, lighting, air systems |
| 18 | Agricultural Drum | Frame, hull | Hydroponics, sensors, pumps |
| 19 | Industrial Manufacturing Drum | Structural shell | CNC machines, refiners, electronics |
| 20 | Observatory Module | Main structure | Optics, detectors, tracking systems |

**Priority asset generation order (first 10):**
1. Structural Truss Segment
2. Structural Ring Frame
3. Hull Panel Section
4. Utility Spine Section
5. Cargo Lattice Module
6. Radiation Shield Tank Segment
7. Habitat Drum
8. Docking Port Assembly
9. Life Support Core Mk1
10. Avionics Module

These ten alone can generate dozens of unique Cycler designs procedurally while staying consistent with the lunar bulk + Earth precision philosophy.

#### Cycler Visual Component Answers (locked design decisions)

| Component | Decision | Prompt Language |
|---|---|---|
| **Spine/Frame** | Segmented Lunar-built truss sections assembled in orbit, NOT one continuous piece (Luna cannot launch a monolithic structure; fits modular construction + expansion over time) | "segmented lunar-manufactured regolith composite truss spine, assembled from modular structural sections, visible connection flanges and utility conduits" |
| **Habitat Rings** | NOT entirely Earth-built — ring structure/hull/shielding are Lunar; life support/electronics/furnishings are Earth. Industrial, not ISS/Starship sleek | "large cylindrical habitat drums built from lunar-manufactured structural panels, fitted with Earth-built life support systems and white pressure interfaces" |
| **Docking Ports** | Earth-built precision mechanisms, visibly protruding (docking hardware is valuable visual language) | "protruding white precision docking adapters with guidance sensors and alignment mechanisms" |
| **Engines** | NOT constantly thrusting — primary orbit-change engines at stern only; secondary stationkeeping thrusters distributed along spine every few hundred meters | "distributed Earth-built maneuvering thruster pods mounted along the truss structure, larger propulsion cluster mounted at the aft end" |
| **Command & Control** | Centered near the middle of the spine, NOT at the nose — a cycler is operated like a station, not flown like a ship (shortest comms runs, best protection, easiest access) | "compact Earth-built command and control module embedded near the central spine" |
| **Power System** | Early Cyclers: large solar arrays. Mature Cyclers: small reactor modules + supplemental solar (matches broader nuclear transition arc in game economy) | "compact reactor module integrated into the central structure, supplemented by large articulated solar wings" |

#### Cycler Reference Sheet
A reference infographic exists (1320m scale shown vs ISS 109m) showing module color coding: Habitat (blue), Cargo/Storage (tan), Industry/Workshop (orange), Power/Reactor (red), Command/Control (purple), Docking Port (grey), Utilities (green). 4x radial docking ports, rotational gravity habitat rings, low-thrust maneuvering engines. **Scale (1320m) is reference only, not yet confirmed canonical** — check against existing Galaxy Game JSON/model data for actual specs.

---

### ChatGPT Refinement Workflow

When a render doesn't match the intended design (e.g. TEU renders below drifted into Lunar-built aesthetic despite Earth-built prompt), branch the ChatGPT conversation from the initial render and ask clarifying questions before committing to a final prompt. Useful question categories:
- Component origin (which parts are Lunar vs Earth vs hybrid)
- Structural assembly (continuous vs segmented/modular)
- Hardware placement (where do engines, docking ports, C&C go)
- Material contrast (how starkly should Lunar and Earth materials clash visually)

Avoid asking about game data questions (scale, capacity, crew, transit time) in image-generation sessions — that data belongs in Rails models/JSON, not visual prompts.

### Established Component Hierarchy (Luna Surface)

Components build on each other in this order — assets should visually suggest they connect:

**Field-built (Lunar-manufactured):**
1. **Foundation Block** — regolith composite, anchor spikes for uneven terrain, lifting rings on top, heavy and low-profile
2. **Support Column** — mounts onto foundation block, cross-braced trusses, modular connection points, tall and structural
3. *(Next: Worldhouse enclosure panels, connecting beams, hab modules, etc.)*

**Earth-manufactured (TEU → PVE production chain):**
1. **TEU** (Terrain Excavation Unit) — processes raw regolith → outputs regolith fines feedstock
2. **PVE** (Planetary Volatiles Extractor) — takes regolith fines via top hopper → extracts oxygen → outputs depleted regolith waste
3. *(Next: LOX storage, atmosphere processing, water extraction)*

### Example Prompts (Proven Working)

**TEU MK1 (Earth-built catalog format — corrected):**
```
Clean product catalog render of a MK1 Thermal Electric Unit (TEU).

Show exactly two views:
- Front 3/4 view on the left
- Rear 3/4 view on the right

White background. Subtle shadows. No text. No labels.

Earth-built aerospace industrial hardware. Large top hopper for regolith intake,
thermal processing chamber, gas extraction systems, rear output chute for processed
regolith, modular tanks and piping.

First-generation production model with conservative engineering, exposed serviceable
subsystems, overbuilt industrial construction, designed for reliability in
extraterrestrial operations.
```

**Lunar Regolith Printer (Lunar-built catalog format):**
```
Clean product catalog render of a MK1 lunar regolith printer.

Show exactly two views.

White background.

Rough lunar-built manufacturing equipment with visible print layering, rugged frame,
regolith feed system, industrial robotic print head.
```
```
Clean product catalog render of a MK1 Planetary Volatiles Extractor (PVE).

Show exactly two views:
- Front 3/4 view on the left
- Rear 3/4 view on the right

White background. Subtle shadows. No text. No labels. No logos.
No infographic elements. No environment.

Earth-built industrial oxygen extraction hardware. Processes regolith fines from TEU
to extract oxygen from oxides. Includes processing chamber, oxygen storage connections,
waste output for depleted regolith feedstock.

First-generation production model with conservative engineering, exposed serviceable
subsystems, overbuilt industrial construction, designed for reliability in
extraterrestrial operations.
```

### Example Prompts (Proven Working)

**Support Column:**
```
Industrial component render of a massive structural support column for worldhouse enclosure
construction. Show multiple angles of the same object in a single image:
- Perspective view
- Side profile
- Structural base detail

The support column is assembled from large regolith-printed structural sections with exposed
truss reinforcement and modular connection points.

Material appearance:
- Rough lunar concrete-like texture
- Thick industrial trusses
- Layered 3D printed fabrication patterns

Style:
- Megastructure construction component
- Industrial and massive
- Realistic lunar engineering
- No text or labels
```

**Foundation Block:**
```
Industrial component render of a modular lunar foundation block used for stabilizing surface
structures. Show multiple angles of the same object in a single image:
- Perspective view
- Underside anchoring detail
- Side profile

The block is thick and heavy with integrated anchoring points for uneven rocky terrain.

Material appearance:
- Rough regolith composite
- Abrasive dusty texture
- Reinforced corners with embedded metal hardware

Style:
- Industrial lunar construction equipment
- Rugged and practical
- Realistic engineering render
- No text or labels
```

---

## Structure & Unit Asset Inventory

### Vehicles & Spacecraft
| Asset | Status | Notes |
|---|---|---|
| HLT MK1 (Heavy Lift Transport) | ✅ Saved | Earth-built, two-view catalog format |
| Cycler | ⚠️ Reference sheet exists, components not generated | Full design system documented above — 20 component catalog, priority list of 10. Scale (1320m) needs canonical confirmation. |
| Tug | ❌ Not generated | Hybrid style |
| Skimmer (Venus/Titan) | ❌ Not generated | Earth-built, atmospheric vehicle |
| Lunar Regolith Printer | ❌ Not generated | Lunar-built manufacturing equipment, visible print layering, rugged frame, robotic print head — prompt documented below |

### Luna Surface (Field-built / ISRU style)
| Asset | Status | Notes |
|---|---|---|
| Foundation Block | ✅ Saved | Multi-angle, proven prompt |
| Support Column | ✅ Saved | Multi-angle, proven prompt |
| Worldhouse enclosure panel | ❌ Not generated | Next logical component |
| ISRU facility | ❌ Not generated | Core MVP structure |
| Hab module (ISRU-printed) | ❌ Not generated | Field-built variant |
| LOX storage tank | ❌ Not generated | Key resource structure |
| Water extraction unit | ❌ Not generated | — |
| Solar array | ❌ Not generated | Power infrastructure |
| Comms/relay tower | ❌ Not generated | — |
| HLT launch pad | ❌ Not generated | Critical for MVP |
| Regolith processing unit | ❌ Not generated | — |

### Earth-Manufactured Units
| Asset | Status | Notes |
|---|---|---|
| HLT MK1 (Heavy Lift Transport) | ✅ Saved | Two-view catalog format, front/rear 3/4, cargo bay open view included |
| PVE MK1 (Planetary Volatiles Extractor) | ✅ Saved | Two-view catalog format, proven prompt documented |
| TEU (Terrain Excavation Unit / Thermal Electric Unit) | ⚠️ Generated but wrong style — needs regeneration | Multiple renders exist but drifted into Lunar-built aesthetic despite Earth-built prompt intent. Regenerate using corrected Earth-Built Catalog Prompt Template. One infographic-style render exists with labeled callouts but has OCR text errors — reference only, not usable as game asset. |
| TEU MK2 | ❌ Not generated | — |
| TEU MK3 | ❌ Not generated | — |
| PVE MK2 | ❌ Not generated | — |
| PVE MK3 | ❌ Not generated | — |
| HLT MK2 | ❌ Not generated | — |
| HLT MK3 | ❌ Not generated | — |
| LOX storage unit | ❌ Not generated | Earth-built, connects to PVE output |
| Atmosphere processor | ❌ Not generated | Life support category |
| Water recycler | ❌ Not generated | Life support category |
| Deployment robot | ❌ Not generated | Robotics category |
| Maintenance robot | ❌ Not generated | Robotics category |

### Terrain Tiles
| Asset | Status | Notes |
|---|---|---|
| Earth biomes (pixel style) | ✅ Saved locally | base_terrain.png — ocean, grassland, desert, forest, mountains, snow, plains, jungle |
| Luna surface tiles (photorealistic) | ✅ Saved locally | gemini_base_terrain.png — regolith, craters, boulders |
| Mars surface tiles (photorealistic) | ✅ Saved locally | gemini_base_terrain.png — red dust, dunes, cracked terrain |
| Ice world tiles (photorealistic) | ✅ Saved locally | gemini_base_terrain.png — ice sheets, frozen lakes |
| Volcanic tiles (photorealistic) | ✅ Saved locally | gemini_base_terrain.png — lava flows, calderas |
| Titan-specific tiles | ❌ Not generated | Methane seas, hydrocarbon terrain |
| Venus atmospheric tiles | ❌ Not generated | Cloud layers, aerostat platforms |

**⚠️ Style decision pending:** Earth pixel tiles vs photorealistic tiles for other worlds — are these intentionally different styles per world, or does one style need to be standardized across all bodies?

---

## Asset Storage

**Current state:** Assets saved locally on Intel MacBook and/or M4. Some remain only in ChatGPT conversation history.

**Recommended structure for Galaxy Game repo:**
```
app/assets/images/
  corporations/
    ldc_logo.png
    mdc_logo.png
    vdc_logo.png
    wtc_logo.png
    terragen_logo.png
    [etc]
  structures/
    luna/
      foundation_block.png
      support_column.png
      [etc]
    mars/
    venus/
    titan/
  vehicles/
    hlt.png
    cycler.png
    tug.png
    [etc]
  terrain/
    earth/
    luna/
    mars/
    titan/
    venus/
```

**Priority action:** Retrieve any logos still only in ChatGPT history (AstroLift, Vector Hauling, Zenith Orbital, Wormhole Station) before the conversation expires.

---

## UI Integration Task (Future)

**Feature:** Display structure/unit images in admin and player views when browsing buildable items on a tile.

**Scope:**
- Admin side: building management screens showing available structures per tile type
- Player side: build queue UI showing what can be constructed
- Both sides: corporation faction screens showing logos

**Dependencies:**
- Asset library needs sufficient coverage of MVP structures before UI work begins
- Rails image asset pipeline integration
- Tile-to-structure mapping (which structures are available on which terrain types)

**Status:** Not yet started. Create separate implementation task file when asset library reaches MVP coverage (ISRU facility, hab module, HLT launch pad minimum).

---

## Pending Decisions

1. **SDC name** — Saturn Development Corporation name TBD before logo generation
2. **Tile style standardization** — pixel art (Earth style) vs photorealistic (Gemini style) across all worlds
3. **Asset storage location** — confirm repo path before committing images
4. **Minimum asset set for UI feature** — define what "enough assets" means before starting Rails integration
5. **Smelter / proper ore mining tier** — future power-intensive tech progression beyond initial ISRU regolith printing. When implemented, will introduce a new visual tier (smelted/cast metal components) distinct from current regolith-composite-only Lunar assets. Not yet in scope for current asset generation.
5. **Smelter / Mine assets (future tech tier)** — when proper metal ore mining and smelting unlocks (higher power requirement than initial ISRU), new asset category needed: refined metal structural components, distinct from current regolith composite look. Not needed for current MVP scope.
