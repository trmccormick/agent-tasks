# Worldhouse Design Clarification — Synthesis Report

**Date**: 2026-08-02
**Task**: 2026-07-24-MEDIUM-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md

## Key Findings from Code Audit

### Worldhouse is a Structure, NOT Terraforming

**Evidence**: The `Structures::Worldhouse` model inherits from `BaseStructure` and wraps a geological feature (valley, canyon, lava tube, or excavated cavity). It does NOT modify any planetary sphere.

### Files Found (App)
| File | Role |
|------|------|
| `app/models/structures/worldhouse.rb` | Main worldhouse model — wraps geological feature, manages segments |
| `app/models/structures/worldhouse_segment.rb` | Individual construction segment of a worldhouse |
| `app/models/structures/converted_base.rb` | Subclass of Worldhouse with atmospheric processing + energy management |
| `app/models/celestial_bodies/features/base_feature.rb` | Geological feature base; has `worldhouse` association, `enclose!`, `pressurize!` status progression |
| `app/models/celestial_bodies/features/excavated_cavity.rb` | Specific feature type (asteroid cavities) used as worldhouse substrate |
| `app/models/mega_project.rb` | Has `mariana_worldhouse` project_type enum — references worldhouses at mega-project level |
| `app/models/concerns/structures/coverable.rb` | Concern for covering structures (panels, sealing) |
| `app/services/autonomous_mission_service.rb` | References worldhouses in mission planning |
| `app/services/manufacturing/construction/segment_covering_service.rb` | Covers worldhouse segments with panels |

### Files Found (Spec)
- Factories: `worldhouse.rb`, `worldhouse_segment.rb`, `orbital_settlement.rb` (contains worldhouse reference)
- Specs: `worldhouse_segment_spec.rb`, `coverable_spec.rb`, `enclosable_spec.rb`, `shell_spec.rb`
- Service specs: `autonomous_mission_service_spec.rb`, `segment_covering_service_spec.rb`

### Critical Finding: NO Terraforming Interaction

**Confirmed**: None of the worldhouse models or services modify:
- `CelestialBodies::Spheres::Atmosphere` — NOT touched by worldhouses
- `CelestialBodies::Spheres::Biosphere` — NOT touched by worldhouses
- `CelestialBodies::Spheres::Hydrosphere` — NOT touched by worldhouses

The only "environmental" interaction is:
1. **Geological feature status progression**: `natural → surveyed → enclosed → pressurized → settlement_established` (on the geological feature, NOT the planet)
2. **ConvertedBase** adds `AtmosphericProcessing` concern — but this processes air WITHIN the structure (local), not planetary atmosphere

### Worldhouse Architecture Summary

```
Worldhouse (BaseStructure)
├── geological_feature: BaseFeature (Valley/Canyon/LavaTube/ExcavatedCavity)
├── worldhouse_segments: [WorldhouseSegment]
│   └── each segment: Coverable + Enclosable
├── total_segments / enclosed_segments / coverage_percent
├── population_capacity (50% usable space, 100 people/km³)
└── construction_complete? when enclosed_segments >= total_segments

ConvertedBase < Worldhouse
├── host_body: Asteroid/SmallMoon (polymorphic)
├── HasUnits (life support units)
├── AtmosphericProcessing (local O2/CO2 within structure)
└── EnergyManagement (local power grid)
```

### Placement Rules (from code)
- Must be built on: Valley, Canyon, LavaTube, or ExcavatedCavity
- Feature must pass `worldhouse_suitable?` check (conversion_suitability for 'pressurized_valley_section' or 'worldhouse' must be 'excellent' or 'good')
- ConvertedBase requires a host_body (Asteroid/SmallMoon) with compatible composition_type

### Indirect Terraforming Relationship
- Worldhouses can be placed on terraformed terrain (no code prevents this)
- Placement validation may use planetary conditions (via `conversion_suitability` from geological feature data)
- Worldhouse biomes do NOT feed back into planetary biosphere/atmosphere calculations
- The `pressurize!` method on BaseFeature only affects the local feature status, not planetary atmosphere

### Existing Docs That Reference Worldhouses
- `docs/storyline/PHASE_ALIGNMENT_SUMMARY_2026-06-18.md` — Phase 6+ Luna worldhouse (infrastructure timeline)
- `docs/new_agent/projects/galaxy_game/status.md` — Example reference to worldhouse on Surface View
- `docs/new_agent/projects/galaxy_game/services/ai_manager_service_inventory.md` — SegmentCoveringService description
- `docs/new_agent/projects/galaxy_game/handoffs/HANDOFF-2026-06-15.md` — Architecture confirmation (correct: "worldhouse structure")
- `docs/archive/chats/gemni-chat.md` — Design discussion comparing global terraforming vs worldhouse (already makes the distinction)

### No Conflation Found
No existing architecture docs conflate worldhouse with terraforming. The `gemni-chat.md` archive actually makes a clear comparison table between "Global Terraforming" and "Valles Marineris Worldhouse". The status.md and handoff docs reference worldhouses correctly as structures.

## Conclusion for Documentation

The documentation gap is **not** about existing docs being wrong — it's about the **absence of dedicated worldhouse design documentation**. There is no single doc that explains:
1. What a worldhouse is (structure over geological feature)
2. How it differs from terraforming (local vs planetary scope)
3. The data flow (geological feature → segments → covering → pressurization)
4. Placement rules and constraints

The new `worldhouse_design.md` should be created as a standalone reference doc, not to correct existing conflations.
