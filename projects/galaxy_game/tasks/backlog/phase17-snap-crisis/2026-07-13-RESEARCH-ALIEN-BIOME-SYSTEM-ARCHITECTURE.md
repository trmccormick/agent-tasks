---
status: backlog
priority: MEDIUM
type: research
system_domain: CELESTIAL_BODIES | BIOSPHERE | FUTURE_EXPANSION
mvp_alignment: POST_MVP
local_worker_safe: false
---

## ⚡ Minimal Handoff

```
Task: 2026-07-13-RESEARCH-ALIEN-BIOME-SYSTEM-ARCHITECTURE.md

BACKGROUND: Current biosphere system (2026-07-12 task) implements Earth-based 
life that requires liquid water on surface. This is perfect for Earth and 
terraformed Mars/Venus, but does not support alien biochemistries.

ROOT PROBLEM: Alien life forms (methane-based on Titan, silicon-based on 
high-temp worlds, etc.) may need completely different biosphere system. 
Current architecture may not be extensible to support alien biomes.

CRITICAL QUESTION: Can Earth biomes and alien biomes coexist on the same 
world? Or must we prevent that at the design level?

THIS TASK: Investigate and design how alien biome systems should integrate 
with (or remain separate from) Earth biosphere system. Determine compatibility 
constraints and propose architecture for future expansion.

Read task file for full research questions and design considerations.
```

---

## TASK: Alien Biome System Architecture Research

**Status**: BACKLOG (phase 15+)
**Priority**: MEDIUM
**Type**: RESEARCH
**Created**: 2026-07-13

---

## Context

The Earth biosphere system (task 2026-07-12) gates biome creation on liquid water availability. This works perfectly for:
- **Earth**: Already has liquid water, biosphere record created
- **Terraformed Mars/Venus**: After terraforming achieves liquid water, biosphere record created
- **Future terraformed worlds**: Same pattern

However, the game may eventually support **alien life forms** that don't require Earth conditions:
- **Methane-based life** (Titan, Enceladus): Would gate on liquid methane, not water
- **Silicon-based life** (High-temp worlds): Different environmental requirements entirely
- **Exotic biochemistry**: Unknowable requirements until designed

**The Critical Gap**: The current Earth-biosphere architecture makes assumptions that may not hold for alien life. We don't yet know:
1. Should alien biomes use the same biosphere record model?
2. Can Earth and alien biomes coexist on same world? Should they?
3. What are the rendering conflicts?
4. What about terraforming paths that cross biochemistry types?

---

## Research Questions

### 1. Compatibility: Can Earth and Alien Biomes Coexist?

**Scenario A**: Titan gets terraformed to have BOTH methane oceans (native) AND introduced Earth water-based biomes
- Could a single planet have two biosphere records (one alien, one Earth)?
- Rendering: How to visualize both? Separate layers? One replaces other?
- Gameplay: Do they compete or cooperate?
- Science: Would methane and water life actually conflict on same world?

**Scenario B**: Titan remains methane-based (no Earth terraforming)
- Only alien methane biosphere record
- No Earth biomes possible on surface
- Separate system entirely from Earth biosphere

**Scenario C**: Planets can only support ONE type of life
- Design constraint: Choose alien OR Earth, not both
- Cleaner but less flexible
- Would simplify rendering and gameplay

### 2. Data Model: Should Alien Biomes Use Same System?

**Current Model** (Earth biospheres):
- CelestialBody has one Biosphere record
- Biosphere gates on liquid water
- Biome grid is single unified array

**Question**: Can this extend to alien life?

**Option A**: Polymorphic Biosphere
- Biosphere has `type` field: `:earth`, `:methane`, `:silicon`, etc.
- Different gating logic per type
- Single biome grid per world (but contents change based on type)
- Simplest for database

**Option B**: Separate Alien Biome Models
- Keep Biosphere as Earth-only
- Create AlienBiosphere model
- CelestialBody has multiple biome records (optional Biosphere + optional AlienBiosphere)
- More flexible but more complex

**Option C**: Abstract BiomeSphere Layer
- Create generic biome container (BiomesLayer or similar)
- Biosphere is just one implementation (Earth)
- Easier to add methane biosphere, silicon biosphere, etc.
- Most flexible but requires refactoring

### 3. Rendering: How to Display Alien Biomes?

**Current Rendering** (Earth biomes):
- Biome types mapped to hex colors (ice, tundra, forest, etc.)
- Colors make sense for Earth: green forests, yellow deserts, white ice
- Pixel color = biome type

**Alien Biome Rendering**:
- Methane biomes: What colors represent methane-based life?
- Silicon-based: How to visualize non-carbon chemistry?
- Different color schemes per biochemistry type?
- Could alien biomes render on same canvas, or need separate visualization?

**Question**: Does rendering system need redesign for alien biomes?

### 4. Terraforming: Crossing Biochemistry Types

**Scenario**: Player terraforms Titan to add Earth-based biomes while maintaining methane oceans

**Questions**:
- Is this scientifically realistic (game design choice)?
- How does biome spreading work across two incompatible chemistries?
- Does removing methane biosphere require reversing terraforming?
- Can terraforming progress be visualized if two systems active?

**Scenario**: Player terraform Mars to have both methane AND water biomes (hypothetically)

**Questions**:
- Would water and methane environments conflict?
- Could only occupy separate regions (polar methane, equatorial water)?
- Or must terraforming choose one path?

### 5. Player Gameplay: Alien Life Discovery and Creation

**Mechanics to Consider**:
- How do players discover alien life types exist?
- Can players terraform worlds to support alien life?
- Do alien biomes spread differently than Earth biomes?
- Are there worldhouse equivalents for alien life? (Methane bio-domes?)
- Can players mix Earth and alien life intentionally?

---

## Design Recommendations (Preliminary)

⚠️ **These are research-phase recommendations pending deeper investigation**

### Near-term (This Research Task)
1. ✅ Decide if alien and Earth biomes can coexist on same world
2. ✅ Choose data model approach (polymorphic vs. separate vs. abstract layer)
3. ✅ Determine if rendering system changes needed
4. ✅ Identify compatibility constraints

### Medium-term (Future Implementation Task)
1. Implement alien biosphere system (if needed)
2. Create Methane biosphere model (proof of concept)
3. Test rendering with alien biomes
4. Add terraforming logic for alien compatibility

### Long-term (After Release)
1. Add silicon-based life biospheres
2. Add exotic biochemistry types
3. Allow players to discover alien worlds with native ecosystems
4. Advanced terraforming for biochemistry conversion

---

## Deliverables for This Research Task

By the end of this task, provide:

1. **Decision Matrix**: Yes/No/Maybe for each research question
2. **Chosen Architecture**: Which data model approach (polymorphic/separate/abstract)
3. **Rendering Impact Assessment**: Does current rendering support alien biomes?
4. **Terraforming Constraints**: What are the limits for crossing biochemistry types?
5. **Compatibility Rules**: Earth + Alien coexistence decision
6. **Phased Implementation Plan**: When/how to add alien biome support

---

## Success Criteria

✅ **Clarity**: All research questions answered with reasoning
✅ **Decision**: Clear choice on alien biome architecture approach
✅ **Documentation**: Design decisions recorded for future implementation
✅ **No Code Changes**: This is research, not implementation
✅ **Ready for Implementation**: Next agent can use findings to build alien system

---

## Out of Scope

❌ Do NOT implement alien biosphere code in this task
❌ Do NOT create migration for alien biosphere records
❌ Do NOT modify rendering system
❌ This is research/design only, not implementation
