## STATUS SYNTHESIS REPORT

**Task**: Biosphere Development Modeling
**Status**: backlog → active (research phase)
**Date**: 2026-07-14

---

### What I'm About to Research

Model planetary age, water history, magnetic field, and atmospheric retention to determine biosphere development stage. The goal is to add historical context to biosphere data so planets have scientific grounding for why life exists (or doesn't).

---

### Research Areas

| Area | What I Need to Understand | Source |
|---|---|---|
| Earth timeline | 3.8 Ga first life → 2.4 Ga GOE → 540 Ma Cambrian | Scientific literature, existing code comments |
| Mars history | 4.1 Ga age, 3.8-2.2 Ga water, ~4.0 Ga field loss | Scientific literature |
| Current model | Where planetary age is stored (if at all) | Grep `planetary_age`, `age_ga`, date fields in CelestialBody |
| Biosphere stage | How to calculate from multiple factors | BiosphereGeneratorService, CelestialBody spec |

---

### Key Design Questions to Answer

- **Q1**: Should `planetary_age_ga` be stored in CelestialBody model or only in JSON seed data?
- **Q2**: How do we weight the factors? (age: 40%, water: 30%, field: 20%, pressure: 10%?)
- **Q3**: Should biosphere development stage be calculated or stored? (calculated = dynamic; stored = historical record)
- **Q4**: What's the minimum age for each stage? (eukaryotes need 1.5+ Ga?)

---

### Current Codebase State (What I Found)

#### Existing Infrastructure

**CelestialBody model** (`app/models/celestial_bodies/celestial_body.rb`):
- ✅ Has `has_magnetosphere` stored in JSONB `properties` field
- ✅ Has `magnetic_field` method that estimates Gauss based on body type, mass, rotation
- ✅ Has `can_support_surface_life?` method for habitability gate
- ❌ **NO** `planetary_age_ga` attribute anywhere
- ❌ **NO** historical water tracking
- ❌ **NO** biosphere_stage attributes (historical or current)

**Biosphere model** (`app/models/celestial_bodies/spheres/biosphere.rb`):
- ✅ Has `habitable_ratio` (0.0-1.0)
- ✅ Has `biodiversity_index` (0.0-1.0)
- ✅ Has `biome_distribution` (hash)
- ✅ Has `life_forms` association (Biology::LifeForm)
- ✅ Has `base_values` store for simulation reset
- ❌ **NO** biosphere_stage_historical
- ❌ **NO** biosphere_stage_current
- ❌ **NO** water history tracking

**BiosphereGeneratorService** (`app/services/star_sim/biosphere_generator_service.rb`):
- ✅ Supports complexity levels: `none`, `primitive`, `basic`, `complex`
- ✅ Auto-detects complexity from temperature, pressure, gravity, liquid water
- ✅ Supports seed_era parameter (including `:terraformed`)
- ❌ **NO** planetary age input
- ❌ **NO** magnetic field consideration
- ❌ **NO** historical water tracking

**Seed Data**:
- Sol system (`data/json-data/sol-complete.json`) has planets with `biosphere_attributes` but no age/historical data
- AOL-732356 (`data/json-data/generated_star_systems/aol-732356.json`) has empty `biosphere: {}`

---

### Open Questions for Product Team

1. **Model vs. Storage**: Should biosphere_stage be calculated on-the-fly from attributes, or stored as a cached value?
   - Pro (calculated): Always reflects current state, no sync issues
   - Pro (stored): Faster lookups, can track historical changes separately

2. **Seed Data Source**: Where do we get planetary ages?
   - Option A: Hardcode based on real solar system (Sol) or research + game balance (procedural)
   - Option B: Generate randomly with age distribution that matches exoplanet surveys
   - Option C: Hybrid — seed data provides ages where known, procedural fills in unknowns

3. **Terraformed Worlds**: How do we model biosphere recovery after terraformation?
   - Does `biosphere_stage_historical` stay frozen as "extinct"?
   - Does `biosphere_stage_current` change to "establishing" and evolve over time?
   - Do we need a `biosphere_recovery_timeline_ga` field?

4. **Early Game Design**: Does the player care about ancient habitability in the first mission?
   - If not, we can defer full modeling until gameplay requires it
   - If yes, we need historical stages fully working day 1

5. **Magnetic Field Threshold**: At what field strength does atmospheric retention become a problem?
   - Mars: 0.0 (lost field, lost atmosphere)
   - Earth: 1.0 (strong field, keeps atmosphere)
   - Cutoff for habitability: ~0.3 or 0.5?

---

### Expected Outcomes

1. Document listing 5-7 missing attributes (with rationale)
2. 8 biosphere development stages defined (sterile → complex_ecosystems)
3. Scoring algorithm outline (pseudocode)
4. Mars & Venus & Earth examples worked through
5. Open questions for product team
6. Recommendation: which attributes go in seed data, which are calculated

---

### Critical Gotchas I Will Avoid

- ❌ Do NOT modify seed data during research — design first
- ❌ Do NOT assume age_ga = biosphere stage — need to score multiple factors
- ❌ Do NOT conflate "had water" with "has water" — Mars shows why
- ❌ Do NOT create new code during research — summarize design only

---

**SYNTHESIS COMPLETE.** Ready to research and design (no implementation).
