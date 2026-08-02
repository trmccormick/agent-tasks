---
status: backlog
priority: MEDIUM
type: research
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-01
---

# Unit Naming Conventions: mk{num} vs Designation Codenames

## Context

During 2026-07-31 to 2026-08-01 work, clarified naming conventions for unit blueprints. This research task documents the agreed pattern before documentation reorganization completes (wiki cleanup is a blocker for updating formal NAMING_CONVENTIONS.md).

## Established Conventions

### Designation + Mk Version Pattern

**Format**: `[designation]_[descriptive_name]_mk{num}_bp.json`

**Components**:
1. **Designation** (codename): Model identifier for the unit line
   - Examples: CAR-300, AeroFab, VSI, PVE, TEU, SMR-500, ACR-100
   - Usually 2-4 characters, sometimes with numbers
   - All units in same line share same designation

2. **Descriptive name**: Functional purpose/type
   - Examples: lunar_deployment_robot, cnc_module, integrator, extractor
   - Humanizes the unit for easier reading

3. **mk{num}**: Version/progression number
   - **Critical rule**: If no mk number is shown, assume Mk I
   - Progression: Mk I → Mk II → Mk III, etc.
   - Each mk{num} is a different version of the **same unit**, tracked by same designation
   - Use Roman numerals (Mk I, Mk II) in names and descriptions
   - Use lowercase "mk1", "mk2" in filenames

### Examples of Correct Naming

- `car_300_lunar_deployment_robot_mk1_bp.json` (Mk I of CAR-300 line)
- `planetary_volatiles_extractor_mk1_bp.json` (Mk I of PVE line)
- `thermal_extraction_unit_mk1_data.json` (Mk I of TEU line)
- `smr_500_surveyor_mk1_bp.json` (Mk I of SMR-500 line)

### Location Rules

**Units go in `/blueprints/units/[CATEGORY]/[SUBCATEGORY]/` folders:**
- `robots/deployment/` — deployment/assembly robots (CAR-300)
- `robots/construction/` — construction robots
- `robots/exploration/` — exploration robots
- `production/extractors/` — extraction units (PVE Mk1, Mk2, etc.)
- `production/fabricators/` — fabrication modules (AeroFab CNC)
- `production/refineries/` — refinement/integration systems (Volatile Systems Integrator)
- `habitats/` — habitat modules
- `infrastructure/` — infrastructure systems (Spin-Gravity Core)

## Mistakes Fixed

### Misplacements Discovered & Corrected

1. **CAR-300** — Was in `/vehicles/` (wrong category)
   - Fixed: Moved to `/robots/deployment/` where all deployment robots live
   - Reason: CAR-300 is a robot, not a vehicle

2. **AeroFab CNC Module** — Was at `/units/` root (no category folder)
   - Fixed: Moved to `/production/fabricators/` (it's a fabrication tool)
   - Also corrected `v1` naming to `mk1` for consistency

3. **Volatile Systems Integrator** — Was at `/units/` root, named "V1"
   - Fixed: Moved to `/production/refineries/` and renamed to `mk1`
   - Reason: "V1" is not the project's convention; mk{num} is canonical

4. **PVE Mk1 (original)** — Existed in `/resource/` folder only
   - Status: Kept in `/resource/` (compact, Earth-shipped module variant)
   - Also created Mk1 in `/production/extractors/` (industrial facility variant)
   - **Both versions are valid** — same unit designation (PVE), different deployment profiles

5. **Inflatable Habitat variants** — Three similar names, different designs
   - `inflatable_habitat_unit_blueprint.json` — Manufacturing blueprint (500kg, 2500h to build from raw materials)
   - `inflatable_habitat_bp.json` — Pre-assembled compact module (950kg, 120h to assemble)
   - `small_habitat_bp.json` — Larger living quarters (3600kg, 350h production)
   - All valid; represent different designs/economies, not progressive versions

## Critical Distinction

**NOT all units with similar purposes are mk progressions.**

Example: The three inflatable habitat variants are NOT Mk1/Mk2/Mk3. They're separate unit designs:
- Blueprint vs. pre-assembled (different manufacturing approach)
- Lightweight vs. heavy (different capacity/deployment philosophy)
- If these were true mk progressions, they'd share a designation (e.g., "HAB-100" → HAB-100 Mk1, HAB-100 Mk2, HAB-100 Mk3)

## Outstanding Questions

1. **Settlement infrastructure blueprint completeness**
   - Spin-Gravity Core Mk1 blueprint created (2026-08-01) — operational_data created
   - Are all settlement units represented (habitat, power, extraction, refining, water, waste)?
   - Which units need Mk2 progressions (e.g., is there a PVE Mk2 planned)?

2. **Backward compatibility**
   - Some existing blueprints may use inconsistent naming (e.g., "v1" instead of "mk1")
   - Should these be renamed, or is a deprecation period acceptable?

3. **Documentation location**
   - Current: notes scattered in status.md, conversation transcripts, and wiki_reorganization docs
   - Should be: NAMING_CONVENTIONS.md in docs/ (once wiki reorganization complete)
   - Should include: Examples, location rules, mk{num} progression rules

## Blockers

**Wiki reorganization** (docs/wiki_reorganization/ folder) must complete before updating formal documentation:
- phase4/DOCUMENT_CLASSIFICATION.md — defines where naming docs should live
- phase4/CANONICAL_DOCUMENT_INDEX.md — indexes canonical sources of truth
- See docs/wiki_reorganization/README.md for full reorganization plan

## Next Steps (Post-Wiki-Reorganization)

1. Create `docs/NAMING_CONVENTIONS.md` with unit blueprint naming rules
2. Audit all existing blueprints for naming compliance
3. Create deprecation plan for any misnamed blueprints
4. Add mk{num} progression examples for units with multiple versions
5. Update GUARDRAILS.md with: "All units must follow [designation]_[descriptive]_mk{num} pattern"

## Related Files

- Blueprints affected: See `/data/json-data/blueprints/units/` directory structure
- Asset guide: `/docs/ASSET_GENERATION_ARCHITECTURE.md`
- Settlement specs: `/docs/new_agent/projects/galaxy_game/LAVA_TUBE_*.md` (Gemini specs)
- Current status: `/docs/new_agent/projects/galaxy_game/status.md`

---

**Metadata**:
- Depends on: Wiki reorganization (phase4 completion)
- Assigned to: Qwen (when wiki cleanup task begins)
- Reference conversation: 2026-08-01, Blueprint/Unit Infrastructure updates
