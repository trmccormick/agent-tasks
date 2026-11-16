# Phase 1 Implementation Guidance: Critical Path & Priorities

**Synthesized from**: ChatGPT Architecture Review + Gemini Technical Validation  
**Audience**: Implementation Team (agents, developers)  
**Purpose**: Translate design framework into implementation sequence  
**Status**: Ready for execution

---

## 🎯 Core Principle: Execution Order Matters

Both design reviews converge on this: **implementation sequence determines code quality**.

**The Sequence**:
1. **Foundation** (Canonical Biome Map + CanonicalMapService)
2. **Layer 0** (Terrain rendering, 6 families × 20+ variants)
3. **Integration** (BiomeRenderer + Layer 0 + Layer 1 combined rendering)
4. **Validation** (Port system, structural integrity checks)
5. **Future** (Asset Registry, Blueprint Inheritance, AI layer)

---

## 📋 Phase 1 Critical Path (Weeks 1-2)

### Week 1: Foundation + Layer 0

**Day 1-2: CanonicalMapService + Canonical Biome Map**

**Task 1.1**: Create `config/canonical_biomes.json`
- 16 canonical tiles with complete alias mapping
- Verify all PNG files exist: `/data/images/biomes/`
- Success criteria: All 16 tiles render in fallback colors (no NPEs on missing alias)

**Task 1.2**: Implement `CanonicalMapService.normalize_biome(input_name)`
- Downcase + underscore normalization
- Lookup against alias arrays (NOT substring fallback)
- Return canonical name or original if not found
- Test: `normalize_biome("Temperate Forest")` → `"forest"`
- Test: `normalize_biome("arctic")` → `"ice"`

**Success Checkpoint**: 
- [ ] 20 RSpec tests passing for CanonicalMapService
- [ ] Existing 120 BiomeRenderer tests still passing
- [ ] Zero substring fallback behavior

---

**Day 3-4: Layer 0 Terrain Rendering Foundation**

**Task 1.3**: Create `TerrainSpriteRenderer` service
- Similar pattern to BiomeRenderer (parallel PNG loading, async init)
- 6 terrain families: dust, frozen, regolith, temperate, volcanic, basalt
- 20+ variants per family (120+ total PNG files)
- Lookup: temperature + pressure + geochemistry → family → variant

**Files Needed**:
- `/data/images/terrain/dust/dust_*.png` (9 variants)
- `/data/images/terrain/frozen/frozen_*.png` (9 variants)
- `/data/images/terrain/regolith/regolith_*.png` (9 variants)
- `/data/images/terrain/temperate/temperate_*.png` (9 variants)
- `/data/images/terrain/volcanic/volcanic_*.png` (9 variants)
- `/data/images/terrain/basalt/basalt_*.png` (9+ variants)

**Property-Driven Pipeline** (in code):
```ruby
def self.terrain_for(temperature, pressure, geochemistry)
  case temperature
  when -100..0
    :frozen
  when 0..15
    :regolith
  when 15..30
    :temperate
  when 30..100
    :volcanic
  else
    :basalt
  end
end
```

**Task 1.4**: BiomeRenderer Integration (Layer 1)
- Update `BiomeRenderer.draw()` to call `CanonicalMapService.normalize_biome()`
- Remove substring fallback logic (2-3 lines removal)
- Surface View Layer 1 now uses normalized biome names

**Success Checkpoint**:
- [ ] Layer 0 renders contextual terrain (frost on cold regions, dust on hot)
- [ ] Layer 1 renders canonical biomes (16 tiles)
- [ ] Layers 0 + 1 combined rendering works (terrain under biome)
- [ ] Existing 120 RSpec tests still passing

**Week 1 Deliverable**:
- ✅ CanonicalMapService fully operational
- ✅ Canonical biome map with 16 tiles
- ✅ Layer 0 (terrain) + Layer 1 (biome) rendering combined
- ✅ Zero substring fallbacks in codebase
- ✅ All tests passing (120+ existing + new suite)

---

### Week 2: Port System + Asset Registry Prep

**Day 1-2: Port System Implementation**

**Task 2.1**: Port Schema in Blueprints
- Add to blueprint JSON: `port_coordinates`, `connection_types`, `ports` array
- Implement port validation in `TerrainForge` (detail view)
- Validate: i-beam_socket present if structure requires it
- Validate: power_bus_link available if component demands power

**Task 2.2**: Blueprint Data Structure
- Create sample blueprints: regolith panel, i-beam, thruster, radiator
- Each blueprint includes: blueprint_id, asset_registry_id, ports, connection_types
- Asset metadata: fabrication_cost_index, utility_weight, import_feasibility

**Success Checkpoint**:
- [ ] 5+ sample blueprints with complete port definitions
- [ ] Port coordinate system tested (3+ placements validate correctly)
- [ ] Structural integrity rejection working (placement without valid port fails)

---

**Day 3-4: Asset Registry ID Structure**

**Task 2.3**: Prep Asset Registry (Minimal Implementation)
- Add `asset_registry_id` field to blueprint schema
- Create `/app/services/asset_registry_service.rb` (minimal, just reads blueprints)
- Store registry_id pattern: `[blueprint_id]_v[version]` (e.g., "panel_regolith_mk1_v1")
- No batch regeneration yet, just data structure

**Example Registry Entry** (for Phase 2):
```json
{
  "registry_id": "panel_regolith_mk1_v1",
  "blueprint_id": "panel_regolith_mk1",
  "style_guide_version": "2026-07-15-v1.0",
  "generated_files": {
    "image": "/images/components/panel_regolith_mk1_v1.png",
    "thumbnail": "/images/components/panel_regolith_mk1_v1_thumb.png"
  },
  "version_history": [
    {
      "version": 1,
      "created_date": "2026-07-16",
      "style_guide": "2026-07-15-v1.0"
    }
  ]
}
```

**Why Now**: Minimal effort (1-2 days) to add structure. Huge payoff in Phase 2 (batch regeneration, versioning, faction-specific styling).

**Success Checkpoint**:
- [ ] Asset Registry service can load/store registry entries
- [ ] Blueprint → Registry ID lookups working
- [ ] Version history structure ready for Phase 2 population

**Week 2 Deliverable**:
- ✅ Port system with structural integrity validation
- ✅ 5+ sample blueprints with complete metadata
- ✅ Asset Registry ID structure ready for Phase 2
- ✅ No breaking changes to existing rendering pipeline
- ✅ All Phase 1 tests passing

---

## ⚠️ Critical Implementation Constraints

### Do NOT Do These Things

1. ❌ **Substring fallbacks**: Use CanonicalMapService.normalize_biome() everywhere
2. ❌ **Duplicate data**: Every Mk1/Mk2/Mk3 separately defined (Phase 2: Blueprint Inheritance)
3. ❌ **Hardcoded biome mappings**: Use canonical map JSON for all lookups
4. ❌ **Manual asset paths**: Use asset_registry_id for all asset references
5. ❌ **Visual drift**: All component prompts must include hard sci-fi constraint suffix

### MUST Do These Things

1. ✅ **Document every change**: Update `/docs/architecture/` for each system modified
2. ✅ **Test every layer**: Layer 0, Layer 1, combined rendering all tested separately
3. ✅ **Preserve existing tests**: 120+ existing RSpec tests must still pass
4. ✅ **Use registry_id**: All new blueprints include `asset_registry_id` field
5. ✅ **Data ownership**: CanonicalMapService owns biome normalization, TerrainSpriteRenderer owns terrain lookup

---

## 🔗 Implementation Reference Documents

**Required Reading** (in order):
1. [2026-07-16-CONSOLIDATED-10SESSION-DESIGN-FRAMEWORK.md](/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-16-CONSOLIDATED-10SESSION-DESIGN-FRAMEWORK.md)
   - Section: Port Blueprint Schema (copy example exactly)
   - Section: CanonicalMapService Implementation Pattern (use as template)
   - Section: Canonical Biome Map (copy JSON to config/)

2. [2026-07-16-EVENING-DESIGN-FRAMEWORK-CONSOLIDATION-HANDOFF.md](/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/handoffs/2026-07-16-EVENING-DESIGN-FRAMEWORK-CONSOLIDATION-HANDOFF.md)
   - Section: Phase 1 Implementation Roadmap (5 tasks with success criteria)
   - Section: Critical Implementation Constraints
   - Section: Integration Reference Points

3. [2026-07-16-CHATGPT-ARCHITECTURE-REVIEW-ROUND2.md](/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/analysis/2026-07-16-CHATGPT-ARCHITECTURE-REVIEW-ROUND2.md)
   - Section: Asset Registry (understand structure, not implemented yet)
   - Section: Simulation Object Model (reference for future)
   - Section: Blueprint Inheritance (coming Phase 2)

**Code References**:
- BiomeRenderer.js: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/javascripts/biome_renderer.js` (use as pattern)
- surface_view.js: Integration point for Layer 0 + Layer 1 rendering
- BiomeValidator: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/terra_sim/biome_validator.rb` (use CanonicalMapService here)

---

## 🎬 Getting Started

**Step 1**: Read consolidated framework document (1-2 hours)  
**Step 2**: Review BiomeRenderer.js implementation pattern (30 min)  
**Step 3**: Start Task 1.1 (CanonicalMapService) - unblocks all other tasks  
**Step 4**: Run existing tests to establish baseline (ensure 120 passing)  
**Step 5**: Implement tasks in sequence (no parallelization across tasks)

---

## ✅ Success Criteria (Phase 1 Complete)

**Rendering**:
- [ ] Layer 0 (terrain) renders with 6 families
- [ ] Layer 1 (biome) renders with 16 canonical tiles
- [ ] Combined Layer 0 + Layer 1 visible on surface view
- [ ] Zoom-aware rendering (hex color zoomed out, tiles zoomed in)

**Data Integrity**:
- [ ] CanonicalMapService normalizes all biome inputs
- [ ] Zero substring fallback behavior in codebase
- [ ] All 16 canonical biomes render correctly
- [ ] All tests passing (120+ existing + new)

**Documentation**:
- [ ] docs/architecture/ updated for each system
- [ ] Port system documented (port types, coordinates, validation)
- [ ] Asset registry ID structure documented
- [ ] Implementation notes captured for Phase 2

**Preparation for Phase 2**:
- [ ] Asset Registry ID structure in place (ready for batch regeneration)
- [ ] Blueprint inheritance pattern understood (ready to implement)
- [ ] Prompt Template Engine interface designed (ready to build)

---

## 🚀 After Phase 1: Path to Phase 2

**Blocked until Phase 1 complete**:
- Asset generation automation (Prompt Template Engine)
- Image API integration
- Component assembly logic (full port validation)
- Manufacturing progression visuals

**Ready immediately after Phase 1**:
- Asset Registry implementation (2-3 days, huge payoff)
- Blueprint Inheritance refactor (consolidates 100+ Mk1/Mk2/Mk3 definitions)
- Simulation Object Model formalization (ChatGPT recommendation)

**Timeline**: Phase 1 (2 weeks) → Asset Registry prep (2-3 days) → Phase 2 (2 weeks)

---

**This roadmap is ready for execution. Teams can start immediately.**

