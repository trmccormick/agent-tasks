---
name: "Tug Construction System Design"
priority: MEDIUM
phase: phase10+
created: 2026-06-21
status: backlog
type: architecture
---

# TASK: Tug Construction System Design — Blueprint, Mission Profile, Service Fix

**Priority**: MEDIUM  
**Phase**: phase10+ (Late Game)  
**Type**: architecture/design  
**Created**: 2026-06-21  

## Problem Statement

The asteroid relocation tug is a large orbital craft built at an L1 station orbital shipyard. It is used for capturing and relocating asteroids — a key late-game industrial activity. Four specs are currently marked `xit` pending this design work.

**Current**: Tug construction specs exist but are skipped (xit) awaiting design  
**Expected**: Design document with blueprint content, mission profile structure, material requirements approved before implementation

## Evidence of Incompleteness

```bash
grep -n "xit\|skip" galaxy_game/spec/integration/tug_construction_integration_spec.rb 2>/dev/null | head -5
# Likely shows 4 skipped specs awaiting design approval
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/construction/orbital_shipyard_service.rb` | Shipyard construction | Tug bay requirements |
| `galaxy_game/spec/integration/tug_construction_integration_spec.rb` | Integration spec | xit specs to unmark |
| `docs/architecture/systems/job_system_mechanics_spec.md` | Job system docs | Design reference |

## Acceptance Criteria

- [ ] Blueprint content approved (materials, quantities, craft_type)
- [ ] Required facility type defined (orbital shipyard bay count, robotic arms)
- [ ] Construction time estimate documented
- [ ] Mission profile with 3 phases designed (capture, relocate, deploy)
- [ ] Design document ready for implementation by GPT-4.1 or equivalent

## Implementation Steps

1. Answer design decisions required from developer:
   - Asteroid relocation tug materials and quantities
   - Required facility type (orbital shipyard bay count, robotic arms)
   - Construction time estimate
   - Category/craft_type value
2. Design mission profile with 3 phases (capture asteroid, relocate to destination, deploy for mining)
3. Document design in architecture docs
4. Unmark xit specs and verify they pass after implementation

## Commit Instructions

```bash
git add docs/architecture/systems/tug_construction_design.md
git commit -m "docs: design tug construction system blueprint and mission profile"
```
