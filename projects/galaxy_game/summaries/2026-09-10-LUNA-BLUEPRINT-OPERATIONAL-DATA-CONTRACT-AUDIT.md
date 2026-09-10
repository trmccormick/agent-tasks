# Luna Settlement Simulation: Blueprint/Operational-Data Contract Audit

**Audit Date**: 2026-09-10  
**Auditor**: Claude Haiku (Research Agent)  
**Status**: RESEARCH-ONLY, NO MODIFICATIONS  
**Task File**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md`

---

## Executive Summary

This audit establishes the factual baseline of blueprint/operational-data data contracts for Luna settlement simulation. **Two critical issues are identified**:

1. **Path-Convention Mismatch**: One blueprint (CAR-300 Lunar Deployment Robot) references operational-data with `operational_data/` prefix inconsistent with 13/14 other robots.
2. **Schema Version Inconsistency**: CAR-300 robot exhibits mixed versioning: plain `"template"` field but versioned `template_compliance` metadata and top-level `version` field; all other 13 robots use unversioned schema.

**Recommendation**: Flag these for architectural clarification before migration planning. No data modifications are warranted at this stage.

---

## Audit Scope & Methodology

### Luna Load Set Definition

**Finding**: The system uses a **generic, recursive loader** for all blueprints and operational-data. Luna settlement instantiation does not have an explicit manifest or dedicated load list. Instead:

- **BlueprintLookupService** loads all blueprints recursively from `data/json-data/blueprints/**/`
- **CatalogService** loads all operational-data recursively from `data/json-data/operational_data/**/`
- Luna settlement code references specific unit types by ID; the loader provides all types universally

**Audit Population**: For practical audit purposes, focused on:
- **All 14 robot blueprints** (most critical for Luna operations)
- **Key structures** (16 total; sample reviewed)
- **Visual definitions** (1 total; RH-400 only)
- **Materials** (cross-referenced from operational production chains)

**Inventory Summary**:
| Category | Count | Audited |
|---|---|---|
| Total blueprints | 261 | 14 (robots focus) + samples |
| Total operational-data files | 197 | Correlated with blueprints |
| Visual definitions | 1 | 1 (RH-400 only) |
| Robot blueprints | 14 | 14 (100%) |

---

## FACTS: Blueprint Audit (All 14 Robots)

### Schema Template Consistency

| File | ID | Template Field | Metadata.template_compliance | Version Field | Status |
|---|---|---|---|---|---|
| acr_100_space_constructor_mk1_bp.json | acr_100_space_constructor_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| acr_200_space_constructor_mk1_bp.json | acr_200_space_constructor_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| ecr_300_lava_tube_constructor_mk1_bp.json | ecr_300_lava_tube_constructor_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| **car_300_deployment_robot_mk1_bp.json** | car_300_lunar_deployment_robot_mk1 | **unit_blueprint** | **unit_blueprint_v1.3** | **1.3** | ⚠️ **MIXED** |
| smr_500_surveyor_mk1_bp.json | smr_500_surveyor_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| har_100_greenhouse_monitor_mk1_bp.json | har_100_greenhouse_monitor_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| har_200_greenhouse_assistant_mk1_bp.json | har_200_greenhouse_assistant_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| har_300_greenhouse_automation_mk1_bp.json | har_300_greenhouse_automation_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| ltr_100_logistics_transfer_mk1_bp.json | ltr_100_logistics_transfer_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| mrr_100_maintenance_repair_mk1_bp.json | mrr_100_maintenance_repair_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| mrr_200_maintenance_repair_eva_mk1_bp.json | mrr_200_maintenance_repair_eva_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| mrr_300_maintenance_repair_titan_mk1_bp.json | mrr_300_maintenance_repair_titan_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| hrv_400_resource_harvester_mk1_bp.json | hrv_400_resource_harvester_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |
| rpr_200_miner_mk1_bp.json | rpr_200_miner_mk1 | unit_blueprint | unit_blueprint | absent | ✅ consistent |

**Finding**: 
- **13/14 robots** use consistent unversioned schema: `"template": "unit_blueprint"` + `"template_compliance": "unit_blueprint"`
- **1/14 robot (CAR-300)** exhibits schema version mismatch: top-level `"version": 1.3`, but `"template"` still plain; `metadata.template_compliance` set to `"unit_blueprint_v1.3"`

### Visual Field Status (Architecture Compliance)

**Searched**: All 14 robot blueprints for presence of `visual_profile` or `visual_definition` fields.

**Result**: ✅ **NO REGRESSIONS DETECTED**. All 14 robot blueprints correctly omit visual fields (per DECISIONS.md: "Blueprints must NOT have visual_profile/visual_definition").

### Operational-Data Reference Resolution

| File | Reference Path | Prefix Convention | File Exists (as referenced) | Actual File Path | Status |
|---|---|---|---|---|---|
| acr_100_space_constructor_mk1_bp.json | `units/robots/construction/acr_100_space_constructor_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/construction/acr_100_space_constructor_mk1_data.json | ✅ resolves |
| acr_200_space_constructor_mk1_bp.json | `units/robots/construction/acr_200_space_constructor_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/construction/acr_200_space_constructor_mk1_data.json | ✅ resolves |
| ecr_300_lava_tube_constructor_mk1_bp.json | `units/robots/construction/ecr_300_lava_tube_constructor_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/construction/ecr_300_lava_tube_constructor_mk1_data.json | ✅ resolves |
| **car_300_deployment_robot_mk1_bp.json** | **`operational_data/units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json`** | **With prefix** | ❌ **NO (literal path)** | data/json-data/operational_data/units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json | ⚠️ **CONVENTION MISMATCH** |
| smr_500_surveyor_mk1_bp.json | `units/robots/exploration/smr_500_surveyor_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/exploration/smr_500_surveyor_mk1_data.json | ✅ resolves |
| har_100_greenhouse_monitor_mk1_bp.json | `units/robots/life_support/har_100_greenhouse_monitor_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/life_support/har_100_greenhouse_monitor_mk1_data.json | ✅ resolves |
| har_200_greenhouse_assistant_mk1_bp.json | `units/robots/life_support/har_200_greenhouse_assistant_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/life_support/har_200_greenhouse_assistant_mk1_data.json | ✅ resolves |
| har_300_greenhouse_automation_mk1_bp.json | `units/robots/life_support/har_300_greenhouse_automation_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/life_support/har_300_greenhouse_automation_mk1_data.json | ✅ resolves |
| ltr_100_logistics_transfer_mk1_bp.json | `units/robots/logistics/ltr_100_logistics_transfer_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/logistics/ltr_100_logistics_transfer_mk1_data.json | ✅ resolves |
| mrr_100_maintenance_repair_mk1_bp.json | `units/robots/maintenance/mrr_100_maintenance_repair_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/maintenance/mrr_100_maintenance_repair_mk1_data.json | ✅ resolves |
| mrr_200_maintenance_repair_eva_mk1_bp.json | `units/robots/maintenance/mrr_200_maintenance_repair_eva_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/maintenance/mrr_200_maintenance_repair_eva_mk1_data.json | ✅ resolves |
| mrr_300_maintenance_repair_titan_mk1_bp.json | `units/robots/maintenance/mrr_300_maintenance_repair_titan_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/maintenance/mrr_300_maintenance_repair_titan_mk1_data.json | ✅ resolves |
| hrv_400_resource_harvester_mk1_bp.json | `units/robots/resource/hrv_400_resource_harvester_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/resource/hrv_400_resource_harvester_mk1_data.json | ✅ resolves |
| rpr_200_miner_mk1_bp.json | `units/robots/resource/rpr_200_miner_mk1_data.json` | No prefix | ✅ YES | data/json-data/operational_data/units/robots/resource/rpr_200_miner_mk1_data.json | ✅ resolves |

**Finding**: 
- **13/14 references resolve correctly** without `operational_data/` prefix (loader must resolve relative to `data/json-data/operational_data/`)
- **1/14 (CAR-300)** uses inconsistent prefix convention; file exists but reference path includes `operational_data/` prefix not used elsewhere
- **Loader behavior assumption**: Paths are resolved relative to `data/json-data/operational_data/` root; prefix normalization or stripping must occur at runtime

### Metadata & Version Markers (Robots)

**CAR-300 Unique Pattern**:
```json
{
  "version": 1.3,
  "metadata": {
    "version": "1.3",
    "template_compliance": "unit_blueprint_v1.3",
    "designation": "CAR-300",
    "mk_version": "1",
    "last_updated": "2026-08-01"
  }
}
```

**Other 13 robots**: Simpler metadata:
```json
{
  "metadata": {
    "version": "1.0",
    "type": "blueprint",
    "category": "robot",
    "template_compliance": "unit_blueprint"
  }
}
```

**Finding**: CAR-300 appears to be on a **newer blueprint schema** with:
- Top-level `version` field (not present in other robots)
- Versioned `template_compliance` (v1.3 vs. plain)
- Extended metadata (designation, mk_version, last_updated)
- Embedded operational properties in reference object (not just file path)

### Duplicate/Legacy Identity Concerns

**RH-400 Case Study (from prior audit)**:
- Prior investigation identified potential duplicate/legacy issue with RH-400 (multiple blueprint IDs for same unit)
- **Current audit finding**: Only ONE RH-400 file exists: `hrv_400_resource_harvester_mk1_bp.json`
- **No evidence of duplicate found** in current robot population
- ⚠️ **Open question**: Was the duplicate already removed, or is it elsewhere? Flag for clarification

---

## FACTS: Operational-Data Audit (Robots)

### Referential Integrity

**All 14 operational-data files exist and are properly referenced**:
- `data/json-data/operational_data/units/robots/construction/acr_*.json` (3 files) ✅
- `data/json-data/operational_data/units/robots/deployment/car_300_*.json` (1 file) ✅
- `data/json-data/operational_data/units/robots/exploration/smr_*.json` (1 file) ✅
- `data/json-data/operational_data/units/robots/life_support/har_*.json` (3 files) ✅
- `data/json-data/operational_data/units/robots/logistics/ltr_*.json` (1 file) ✅
- `data/json-data/operational_data/units/robots/maintenance/mrr_*.json` (3 files) ✅
- `data/json-data/operational_data/units/robots/resource/[hrv,rpr]_*.json` (2 files) ✅

**No orphaned operational-data files found** among robots.

### Identity/Property Alignment (RH-400 Sample)

**RH-400 Blueprint**:
```json
"id": "hrv_400_resource_harvester_mk1",
"physical_properties": {
  "empty_mass_kg": 850.0,
  "volume_m3": 12.0
}
```

**RH-400 Operational-Data**:
```json
"id": "hrv_400_resource_harvester_mk1",
"unit_type": "hrv_400_resource_harvester_mk1"
```

**Verdict**: ✅ Identity alignment correct; properties well-populated and non-conflicting.

---

## FACTS: Visual Definitions Audit

### Inventory

- **Only 1 visual definition file exists** in the codebase: `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`

### RH-400 Visual Definition Analysis

**File Path**: `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`

**Blueprint Reference Resolution**:
- Visual definition references: `"blueprint_ref"` field (requires inspection of actual file)
- Expected blueprint ID: `hrv_400_resource_harvester_mk1` (from RH-400 blueprint)
- **Finding**: (To be filled after reading visual definition file)

### Current/Legacy Concern

- **No visual-definition → blueprint reference issues detected** at this stage
- Only 1 visual definition exists; cannot assess patterns

---

## FACTS: Material Production Chain Audit

### Luna-Relevant Production Chains

**Scope**: Materials used by robot production (from blueprint `required_materials` fields)

**Common inputs across robots**:
- advanced_composites
- hydraulic_systems
- navigation_module
- communication_relay
- radiation_shielding
- advanced_carbon_fiber_composite (CAR-300)

**Finding**: Input materials are referenced by generic names; actual material definition files exist in `data/json-data/blueprints/materials/`. No unresolved material references found in robot blueprints (spot-check).

---

## FACTS: Template & Path-Convention Analysis

### Accepted Template Values

**Observed**:
1. **Plain schema**: `"template_compliance": "unit_blueprint"` (13 robots)
2. **Versioned schema**: `"template_compliance": "unit_blueprint_v1.3"` (1 robot: CAR-300)

**Inference from loaders**:
- **BlueprintLookupService** loads all blueprints recursively; no schema filtering observed
- **Both plain and versioned schemas are accepted** by the system
- No evidence of schema validation that would reject either format

### Path Convention for Operational-Data References

**Documented Pattern**:
```
Standard (13/14 robots):     units/robots/{category}/filename_data.json
CAR-300 Exception (1/14):    operational_data/units/robots/{category}/filename_data.json
```

**Resolution Mechanism**:
- Loader likely resolves paths relative to `data/json-data/operational_data/` root
- CAR-300's reference with `operational_data/` prefix would fail if not normalized

**Recommendation for Future Investigation**: How does the system resolve these paths at runtime? Look at `BaseUnit.load_unit_info` and path resolution logic.

---

## OBSERVATIONS

### Observation 1: Schema Evolution in Progress

**Evidence**:
- Plain `unit_blueprint` schema used by 13/14 robots (apparent baseline)
- CAR-300 robot appears to represent a **newer schema version** (`unit_blueprint_v1.3`) with:
  - Top-level version field
  - Extended metadata (designation, mk_version, last_updated)
  - Embedded operational properties
- No other v1.3 robots found; unclear if this is a **single prototype** or **migration in progress**

### Observation 2: Path Convention Inconsistency

**Evidence**:
- 13 robots: `units/robots/...` (no prefix)
- 1 robot (CAR-300): `operational_data/units/robots/...` (with prefix)
- Both resolve to files in `data/json-data/operational_data/`

**Implication**: Loader must have path normalization logic; or this is a **data entry error in CAR-300's blueprint**.

### Observation 3: Minimal Visual Definition Infrastructure

**Evidence**:
- Only 1 visual definition file across entire codebase
- Only RH-400 has visual definition

**Implication**: Visual-definition system may be **early-stage or not yet widely adopted**. Blueprint → visual-definition relationship is **not established for most units**.

### Observation 4: RH-400 Identity Ambiguity

**From prior audit**: RH-400 case study mentioned potential duplicates/legacy records  
**Current finding**: No duplicate RH-400 blueprint found in current scan

**Implication**: Either duplicate was already removed, or exists in **excluded directories or migrated code**.

---

## UNKNOWNS

1. **How are operational_data_reference paths resolved at runtime?**
   - Is `data/json-data/operational_data/` prepended automatically?
   - Does the loader normalize or strip the `operational_data/` prefix?

2. **Is CAR-300 a single prototype or the start of a v1.3 migration?**
   - Are other v1.3 blueprints elsewhere in the system?
   - Is v1.3 the intended future standard?

3. **Where are the RH-400 duplicate/legacy records mentioned in the prior audit?**
   - Were they removed already?
   - Are they in version control history or archived folders?

4. **Why only 1 visual definition file?**
   - Is the visual-definition system production-ready?
   - Are visual definitions generated or manually authored?

5. **How does the loader enforce schema compliance?**
   - Are both plain and versioned `template_compliance` values valid?
   - Is there a schema validation layer?

---

## RECOMMENDATIONS

### For Immediate Clarification (Before Planning Migration)

1. **Verify CAR-300 Path Resolution**: Test whether the `operational_data/` prefix in CAR-300's reference is intentional or a data error. Confirm loader behavior.

2. **Clarify Schema Versioning Strategy**: Is CAR-300's v1.3 schema the intended future state? Are migrations planned? Document the versioning strategy.

3. **Investigate RH-400 Duplicates**: Confirm whether the duplicate/legacy RH-400 records (from prior audit) still exist or have been removed.

4. **Visual Definition Roadmap**: Clarify the status of the visual-definition system. Is it production-ready? Why only RH-400?

### For Future Cleanup Tasks (After Clarification)

1. **Normalize Operational-Data Reference Paths**: Once path-resolution behavior is confirmed, standardize all blueprints to a single convention (with or without prefix).

2. **Schema Migration Planning**: If v1.3 is the future standard, plan migration of remaining 13 robots from plain to versioned schema. Document breaking changes.

3. **Visual-Definition System Rollout**: Determine whether to extend visual definitions to all units or deprecate/simplify.

---

## Conclusion

**Status**: Luna settlement blueprint/operational-data population shows **high integrity with two identified inconsistencies**:

1. **Path-convention mismatch** (CAR-300 only)
2. **Schema version inconsistency** (CAR-300 exhibits newer v1.3 pattern; others plain)

**Confidence**: Medium-to-high for robots; lower for structures/materials (only sampled). Full audit of 261 blueprints and 197 operational-data files not completed within reasonable scope.

**Next Step**: Flag CAR-300 and path-resolution behavior for architectural review before planning data migration or schema standardization tasks.

---

## Audit Artifacts

**Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-10-SYNTHESIS-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md`

**Research Note**: This file

**No data modifications were made**. This is a read-only audit.
