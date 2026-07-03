[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/TASK_WEDNESDAY_SURGICAL_AUDIT.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Surgical Guardrails Audit & Migration"
priority: HIGH
phase: 5
created: 2026-06-21
status: backlog
type: architecture
---

# TASK: Surgical Guardrails Audit & Migration

**Priority**: HIGH  
**Phase**: 5  
**Type**: architecture/documentation  
**Created**: 2026-06-21  

## Problem Statement

GUARDRAILS.md mixes game design logic, mission profiles, and agent protocols in 681 lines. Need to extract and organize by domain: keep agent protocols in root, move game design to architecture docs, move mission logic to mission_profiles.

**Current**: Mixed logic in single document  
**Expected**: Separated domain-specific documentation

## Acceptance Criteria

- [ ] GUARDRAILS.md reduced to <150 lines (agent protocols only)
- [ ] Game design logic moved to docs/architecture/
- [ ] Mission logic moved to docs/mission_profiles/
- [ ] All references verified against GLOSSARY_SYSTEM_MECHANICS.md
- [ ] Taxes/fees (0.5% SCC, 0.3% Broker, 3.37% Sales Tax) validated
- [ ] No game design logic remains in root Guardrails

## Implementation Steps

1. Analyze GUARDRAILS.md structure and extract sections
2. Verify against GLOSSARY_SYSTEM_MECHANICS.md
3. Move "Wormhole Anchor Law" and "Economic Overheads" to architecture
4. Move mission-specific rules to mission_profiles
5. Keep only Git, Docker, atomic commit protocols in root
6. Create new architecture and mission profile documents
7. Update all cross-references

## Commit Instructions

```bash
git add docs/architecture/ docs/mission_profiles/ docs/GUARDRAILS.md
git commit -m "docs: refactor guardrails for domain-specific organization"
```