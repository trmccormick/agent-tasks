[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/implement_phosphorus_mechanics.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Implement Phosphorus Resource Mechanics"
priority: MEDIUM
phase: 6
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Phosphorus Resource Mechanics

**Priority**: MEDIUM  
**Phase**: 6  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Game lacks phosphorus (P) as strategic resource despite its critical role in biological systems. P availability determines habitability for biospheres, creating essential gameplay for terraforming strategy and resource management.

**Current**: Material system doesn't distinguish P availability; terraforming phases lack P requirements  
**Expected**: P mechanics integrated into terraforming, AI scouting, and logistics

## Acceptance Criteria

- [ ] Phosphorus availability metrics on celestial bodies
- [ ] P requirements for biological terraforming phases
- [ ] AI scouting evaluates P-rich systems
- [ ] P supply chain and logistics mechanics working
- [ ] No performance impact on current systems

## Implementation Steps

1. Add phosphorus_availability to celestial bodies and materials
2. Implement P requirements for biological terraforming
3. Create P-triage protocol for AI scouting
4. Build P processing and supply chain mechanics
5. Update AI resource evaluation logic
6. Test P-dependent terraforming across environments

## Commit Instructions

```bash
git add galaxy_game/app/models/ galaxy_game/app/services/ai_manager/ galaxy_game/db/migrate/
git commit -m "feat: add phosphorus resource mechanics to terraforming and logistics"
```