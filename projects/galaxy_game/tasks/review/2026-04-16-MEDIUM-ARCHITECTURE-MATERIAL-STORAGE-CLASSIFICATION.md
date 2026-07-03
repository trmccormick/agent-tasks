---
name: "Material Storage Classification Architecture"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: architecture
---

# TASK: Material Storage Classification — Architecture Design

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: architecture/design  
**Created**: 2026-06-21  

## Problem Statement

Surface settlements can store outdoor-eligible materials on the planet surface without enclosure (effectively unlimited capacity). Materials like iron I-beams and solar panels can sit on Luna's surface. Gases, biologicals, and hazardous materials must be enclosed. The `DockingTransactionService` needs to determine outdoor eligibility from material data.

**Current**: No explicit `requires_enclosure` flag in material template; derivation logic undefined  
**Expected**: Classification system that derives enclosure requirements from material properties (state_at_stp, storage.stability, transport_category)

## Evidence of Incompleteness

```bash
grep -n "requires_enclosure\|outdoor_eligible" data/json-data/materials/*.json 2>/dev/null | head -5
# Likely returns no results — classification not implemented
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `data/schemas/material_v1.6.schema.json` | Material template | storage/transport fields |
| `galaxy_game/app/services/docking_transaction_service.rb` | Docking service | Outdoor eligibility logic |
| `docs/architecture/material_storage_classification.md` (new) | Design document | Classification rules |

## Acceptance Criteria

- [ ] Classification system derives enclosure requirements from material properties
- [ ] Derivation uses: state_at_stp, storage.stability, import_config.transport_category
- [ ] Decision documented: derive at runtime vs bake into material JSON files
- [ ] DockingTransactionService can query outdoor eligibility for any material
- [ ] Design document includes derivation rules and examples

## Implementation Steps

1. Analyze existing material fields available for derivation (state_at_stp, storage.pressure/temperature/stability, transport_category)
2. Design classification system: derive enclosure requirements from material properties
3. Decide whether to derive at runtime or bake into material JSON files
4. Document classification rules and examples in design document
5. Integrate with DockingTransactionService for outdoor eligibility checks

## Commit Instructions

```bash
git add docs/architecture/material_storage_classification.md
git commit -m "docs: design material storage classification system for docking transactions"
```
