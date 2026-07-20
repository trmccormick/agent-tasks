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

### Core Principle: Location-Agnostic Asset Design

**CRITICAL ARCHITECTURE RULE** (from ChatGPT design feedback):
- ❌ NEVER create location-specific duplicates: "Lunar Regolith", "Martian Habitat", "Mars Solar Panel"
- ✅ ALWAYS use canonical generic names: "Regolith", "Habitat", "Solar Panel Mk1"
- **Where resources occur** is determined by game data (climate, geology, location JSON)
- **How resources perform** varies by planet through simulation (solar constant, atmosphere, temperature) — NOT separate assets
- **Exception**: Only create separate assets for inherently unique resources (e.g., "Helium-3" found only on Luna, or "Europa Cryobot" designed specifically for ice drilling)

**Example**:
- ❌ Bad: Lunar Habitat, Mars Habitat, Europa Habitat
- ✅ Good: Inflatable Habitat (used everywhere; deployment choice depends on location conditions)
- ❌ Bad: Lunar Solar Panel, Mars Solar Panel, Venus Solar Panel  
- ✅ Good: Solar Panel Mk1/Mk2/Mk3 (performance varies by planet through simulation parameters)

---

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

---

### 2. Unified Asset Catalog Structure

**Every asset should support these views**:
- **Icon** (24-32px, inventory/UI)
- **Detailed Illustration** (64-128px, information panels)
- **3D-style Render** (isometric perspective for manufacturing)
- **Blueprint View** (technical drawing for construction/research)
- **Manufacturing View** (production chain visualization)

**Asset metadata per resource**:
```
{
  "canonical_name": "Regolith",
  "category": "Mineral",
  "aliases": ["lunar soil", "martian soil", "asteroid regolith", "planetary dust"],
  "location_prevalence": {
    "Luna": "abundant",
    "Mars": "abundant", 
    "Mercury": "common",
    "Venus": "rare (beneath clouds)"
  },
  "icon": "icon_mineral_regolith_256.svg",
  "illustration": "illust_regolith_256.png",
  "color_theme": "gray-brown",
  "manufacturing_recipe": null,
  "notes": "Generic planetary surface dust. Location determines availability, not asset."
}
```

---

### 3. Resource Categories (Type-Based, Location-Agnostic)

**Reorganized by type, NOT location**:

#### Atmospheric Gases (~15 icons)
- Oxygen (O₂)
- Nitrogen (N₂)
- Carbon Dioxide (CO₂)
- Carbon Monoxide (CO)
- Ozone (O₃)
- Hydrogen (H₂)
- Helium (He) — generic, not "Helium-3"
- Methane (CH₄)
- Ammonia (NH₃)
- Sulfur Dioxide (SO₂)
- Hydrogen Sulfide (H₂S)
- Argon (Ar)
- Neon (Ne)
- Krypton (Kr)
- Xenon (Xe)

#### Liquids (~10 icons)
- Water
- Heavy Water (D₂O)
- Brine
- Sulfuric Acid
- Liquid Hydrogen
- Liquid Oxygen
- Liquid Methane
- Methanol
- Ethanol
- Liquid Ammonia

#### Ices (~5 icons)
- Water Ice
- Carbon Dioxide Ice (dry ice)
- Methane Ice
- Nitrogen Ice
- Ammonia Ice

#### Common Elements (~15 icons)
- Iron (Fe)
- Aluminum (Al)
- Titanium (Ti)
- Silicon (Si)
- Carbon (C)
- Nickel (Ni)
- Chromium (Cr)
- Magnesium (Mg)
- Copper (Cu)
- Lithium (Li)
- Sodium (Na)
- Potassium (K)
- Calcium (Ca)
- Sulfur (S)
- Phosphorus (P)

#### Minerals (~15 icons)
- Regolith (generic planetary dust)
- Basalt
- Granite
- Clay
- Quartz
- Olivine
- Feldspar
- Ilmenite
- Gypsum
- Limestone
- Olivine
- Obsidian
- Hematite
- Magnetite
- Pyrite

#### Manufactured Materials (~20 icons)
- Polymer
- Plastic
- Kevlar
- Carbon Fiber
- Carbon Nanotubes
- Composite Panel
- Structural Beam
- Glass
- Ceramic
- Foam Insulation
- Concrete
- Steel (Fe + Carbon)
- Aluminum Alloy
- Titanium Alloy
- Stainless Steel
- Cast Iron
- Copper Wire
- Solder
- Epoxy Resin
- Rubber

#### Electronics (~15 icons)
- Circuit Board
- Processor (CPU)
- Memory Module
- Sensor
- Optical Fiber
- Power Cell
- Battery
- Supercapacitor
- Transformer
- Capacitor
- Resistor
- Diode
- LED
- Transistor
- Microcontroller

#### Biological Resources (~15 icons)
- Algae
- Seeds
- Crops
- Biomass
- Organic Waste
- Fertilizer
- Protein Culture
- Yeast Culture
- Bacteria Culture
- Plant Tissue
- Fish Protein
- Insect Protein
- Fungus Culture
- Spirulina
- Chlorella

#### Construction Components (~20 icons)
- Pipe
- Cable
- Structural Frame
- Pressure Vessel
- Airlock
- Docking Port
- Habitat Module
- Inflatable Module
- Solar Panel Mk1/Mk2/Mk3
- Radiator Panel
- Thermal Insulation
- Micrometeorite Shield
- Thruster
- Fuel Tank
- Water Tank
- Gas Canister
- Battery Pack
- Reactor Core

#### Fuel & Energy (~10 icons)
- Electricity (as commodity)
- Heat (thermal energy)
- Hydrogen Fuel (for engines)
- Methane Fuel (for engines)
- Hydrazine
- RP-1 (Kerosene)
- LOX (Liquid Oxygen)
- Nuclear Fuel
- Fusion Fuel
- Geothermal Energy (location-dependent, shown in simulation)

#### Waste (~15 icons)
- Scrap Metal
- Plastic Scrap
- Carbon Scrap
- Slag
- Toxic Waste
- Nuclear Waste
- Electronic Waste
- Organic Waste
- Ash
- Dust
- Sludge
- Contaminated Water
- Broken Components
- Recycled Metal
- Recycled Plastic

#### Unit Types (~8 icons — not location-specific)
- Extractor (mining equipment)
- Habitat (living quarters)
- Fabricator (manufacturing)
- Computer (data center)
- Battery (energy storage)
- Propulsion System (thruster/engine)
- Storage Depot (cargo container)
- Robot (autonomous worker)

#### Craft Types (~5 icons)
- Rover (wheeled vehicle)
- Harvester (industrial extraction vehicle)
- Ship (small spacecraft)
- Spaceship (large spacecraft)
- Satellite (orbital platform)

#### Structures (~10 icons)
- Surface Base
- Orbital Station
- Power Plant
- Lab/Research Facility
- Inflatable Hub
- Umbilical Hub (surface-to-orbit transport)
- Lander Pad
- Dock
- Airlock
- Greenhouse

#### Technology/Research (~20 icons)
- Mining Tech
- Habitat Tech
- Manufacturing Tech
- Agriculture Tech
- Energy Tech
- Propulsion Tech
- Sensors/Communication Tech
- Medical Tech
- Environmental Control Tech
- Robotics Tech
- Life Support Tech
- Navigation Tech
- Geology Survey Tech
- Atmospheric Analysis Tech
- Materials Science Tech
- Fusion Research Tech
- Ai/Automation Tech
- Trade/Commerce Tech
- (Each with Mk1/Mk2/Mk3 progression)

**TOTAL**: ~250-300 icons, **none location-specific** unless inherently unique

---

### 4. Icon Bible Document Structure

**Master Document**: `docs/design/GALAXY_GAME_ICON_BIBLE.md`

Should include:
```
## 1. Design Philosophy
- Location-agnostic asset design principle
- Generic canonical names only (exceptions documented)
- Simulation determines location behavior, not separate assets

## 2. Visual Language Rules
- Color palette (hex codes, RGB, HSL, forbidden combinations)
- Lighting standard (direction, intensity, shadows)
- Border & edge rules (line weight, anti-aliasing)
- Material library (metallic, plastic, glass, organic)
- Transparency rules (checkerboard vs. solid backgrounds)

## 3. Shape System by Category
- Circle: Gases, Energy, UI elements
- Hexagon: Metals, Elements
- Rough hexagon: Minerals, Ores
- Water droplet: Liquids
- Diamond: Fuels, Energy stores
- Leaf/organic: Biological, Food
- Triangle: Waste, Broken items
- Rounded square: Manufactured goods
- Microchip: Electronics
- Special shapes: Units, Structures (defined per-type)

## 4. Complete Resource Catalog
- ~250-300 total assets
- Organized by type (gas, liquid, metal, mineral, manufactured, electronic, biological, fuel, waste)
- For each asset:
  - Canonical name (location-agnostic)
  - Aliases (common alternate names)
  - Category classification
  - Color assignment
  - Icon (all sizes: 24px, 32px, 64px, 256px)
  - Detailed illustration (64-128px)
  - 3D-style render (isometric)
  - Blueprint view (technical drawing)
  - Manufacturing view (production visualization)
  - Location prevalence table (where it's found, rarity level)
  - Simulation notes (how planet affects it)

## 5. Unit/Craft/Structure Library
- 8 unit types (generic, location-agnostic)
- 5 craft types (generic, location-agnostic)
- 10+ structure types (generic, location-agnostic)
- Each with consistent visual treatment
- Tech progression (Mk1, Mk2, Mk3 variants shown via unified design language)

## 6. Naming Convention
- Resource files: `icon_[category]_[name]_[size].png`
  - Example: `icon_gas_oxygen_256.png`, `icon_mineral_regolith_64.png`
- SVG sources: `icon_[category]_[name].svg`
- Illustrations: `illust_[name]_[size].png`
- 3D renders: `render_3d_[name]_isometric.png`
- Blueprints: `blueprint_[type]_[name].png`

## 7. Size Standards & Export Rules
- SVG source (scalable)
- 24px (compact inventory/UI)
- 32px (standard UI)
- 64px (detail view/hover)
- 256px (full quality)
- All with anti-aliasing, pixel-perfect alignment

## 8. Status Overlays & Indicators
- Damaged (red X)
- Locked (lock icon)
- Low stock (warning triangle)
- Research incomplete (question mark)
- Tech level (star count 1-5)
- Empty/depleted (faded + strikethrough)
- In transit (animated glow)

## 9. Unified Asset Record Structure (JSON)
For each resource in the catalog:
{
  "canonical_name": "...",
  "category": "...",
  "aliases": [...],
  "location_prevalence": {...},
  "color_theme": "...",
  "icon_size_variants": ["24px", "32px", "64px", "256px"],
  "detailed_illustration": true,
  "3d_render": true,
  "blueprint_view": true,
  "manufacturing_view": true,
  "manufacturing_recipe": null or {...},
  "simulation_notes": "..."
}

## 10. Location Effects (Simulation, NOT Assets)
- Solar constant (affects solar panel output)
- Atmospheric density (affects movement, drilling)
- Temperature range (affects equipment durability, resource formation)
- Gravity (affects vehicle performance)
- Magnetic field (affects power generation, radiation)
- These are simulation parameters, NOT separate assets
```

### 5. Deliverables

**Phase 1 (Design System Definition)**:
- [ ] Icon Bible document (comprehensive style guide with 10 sections above)
- [ ] Master color palette (swatch file, hex codes, forbidden combinations)
- [ ] Shape templates for each category (SVG or reference images)
- [ ] Lighting diagram (direction, intensity, shadows)
- [ ] Material samples (8-10 representative icons showing all textures/materials)
- [ ] **CRITICAL**: Complete unified asset catalog (all ~250-300 resources cataloged and assigned category, color, shape, prevalence)
  - Example format: canonical_name, category, aliases, color_theme, shape, location_prevalence, simulation_notes
  - **Note**: This is THE reference that prevents creating location-specific duplicates
- [ ] Naming convention guide (how all future assets will be named/organized)
- [ ] Location effects table (how simulation parameters replace separate assets)

**Phase 2 (Icon Production)** — separate task, depends on Phase 1:
- [ ] Generate ~15 atmospheric gas icons using unified design
- [ ] Generate ~10 liquid/ice icons
- [ ] Generate ~15 element/metal icons
- [ ] Generate ~15 mineral icons
- [ ] Generate ~20 manufactured material icons
- [ ] Generate ~15 electronics icons
- [ ] Generate ~15 biological/food icons
- [ ] Generate ~20 construction component icons
- [ ] Generate ~10 fuel/energy icons
- [ ] Generate ~15 waste icons
- [ ] Generate unit sprites (8 types, using design system)
- [ ] Generate craft sprites (5 types, using design system)
- [ ] Generate structure sprites (10+ types, using design system)
- [ ] Create detailed illustrations for all resources (64-128px)
- [ ] Create 3D isometric renders for key resources
- [ ] Create blueprint views for all units/buildings/structures
- [ ] Create manufacturing visualization assets

---

## Acceptance Criteria

**Icon Bible Acceptance**:
- [ ] Document is comprehensive (covers all 10 sections from structure above)
- [ ] Color palette defined and tested (no conflicting colors)
- [ ] Lighting direction consistent and documented
- [ ] Border/shadow rules clear enough for agent/artist to follow
- [ ] 8-10 material sample icons created and approved (proves consistency)
- [ ] **Unified Asset Catalog Complete**: All ~250-300 resources cataloged with:
  - Canonical name (location-agnostic)
  - Category classification
  - Color theme assignment
  - Shape assignment
  - Location prevalence table
  - Simulation effects table (NOT separate assets)
- [ ] **NO location-specific duplicates**: Zero "Lunar/Martian/Venusian" resource names
- [ ] SVG templates available for each shape category
- [ ] Naming convention documented and unambiguous
- [ ] Location effects clearly separated from asset design (color/shape/icon doesn't change by planet; simulation parameters do)

**Quality Bar**:
- Any asset created using this bible should be **instantly recognizable** as "from GalaxyGame"
- Lighting should be consistent across all assets
- Colors should harmonize (not clash)
- No visual drift between generations
- Player can learn visual language by resource TYPE, not by location
- A resource looks the same whether found on Luna, Mars, Venus, or Titan
- Equipment performance varies by location through simulation, NOT through asset swaps

**Critical Success Factor**:
- The unified asset catalog becomes the **single source of truth** for what resources exist in GalaxyGame, preventing duplication and location-specific clutter

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

**CRITICAL ARCHITECTURE PRINCIPLE** (ChatGPT recommendation):
- **Location-Agnostic Asset Design**: Resources are named by TYPE, not by planet of origin
- ❌ Never: "Lunar Regolith", "Martian Habitat", "Venus Solar Panel"
- ✅ Always: "Regolith", "Habitat", "Solar Panel Mk1/Mk2/Mk3"
- Where resources occur is determined by game data (JSON, climate, geology)
- How they perform varies by planet through simulation (solar constant, atmosphere, temperature) — NOT by separate assets
- This unified asset catalog becomes the single source of truth for all visual assets

**Future System Integration**:
- Asset Registry service (Phase 2) enforces naming conventions and prevents duplicates
- JSON-driven resource system references canonical asset IDs (e.g., "regolith", "habitat", "solar_panel_mk1")
- Simulation parameters determine location-specific behavior (no visual asset duplicates needed)

**Implementation Inspiration**:
- Professional game design systems: Factorio, Dyson Sphere Program, Satisfactory, Oxygen Not Included
- All use unified canonical resource naming + location-agnostic asset libraries
- Performance/appearance variations come from simulation, not separate asset files

**Image Generation Strategy**:
- ChatGPT flagged style drift problem: hydrogen icon didn't match O₂/N₂/CO₂ style
- Solution: Define complete design system FIRST, then generate all assets using it as constraint
- Benefits: Consistent style, faster generation, no rework due to drift

**Validation Tool (Optional, Future)**:
- Create automated check: icons match design rules (size, color, lighting, naming convention)
- Prevents future generation errors before assets get committed

