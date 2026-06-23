---
name: "Split Guardrails Game Design Content"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: documentation
---

# TASK: Split GUARDRAILS.md and Move Game Design Content

**Priority**: MEDIUM  
**Phase**: phase8+  
**Type**: documentation  
**Created**: 2026-06-21  

## Problem Statement

`docs/GUARDRAILS.md` mixes agent operating rules with game design/system intent, making both hard to discover and maintain.

**Current**: Mixed concerns in GUARDRAILS.md  
**Expected**: GUARDRAILS.md keeps agent operating rules only; game design content moved to architecture/gameplay/flavor docs

## Files to Edit

| File | Purpose |
|---|---|
| `docs/GUARDRAILS.md` | Source to trim and keep agent rules |
| `docs/architecture/*` | Target architecture sections |
| `docs/gameplay/mechanics.md` | Gameplay boundaries section |
| `docs/flavor/EASTER_EGGS.md` | Easter egg section |
| `docs/systems/em_power_shield_technology.md` | New system doc |

## Acceptance Criteria

- [ ] Design/system sections moved to correct target docs
- [ ] No duplicated content introduced across target docs
- [ ] GUARDRAILS.md retains only agent rules and environment/process constraints
- [ ] New EM shield technology doc created and linked
- [ ] Routing index remains consistent for future agent updates

## Implementation Steps

1. Extract mapped sections from GUARDRAILS.md by documented section mapping
2. Merge into target docs without overwriting existing canonical content
3. Create `docs/systems/em_power_shield_technology.md` for missing target
4. Remove moved sections from GUARDRAILS.md while preserving required core rules
5. Verify cross-links and table-of-contents consistency

## Commit Instructions

```bash
git add docs/GUARDRAILS.md docs/architecture/ docs/gameplay/ docs/flavor/ docs/systems/
git commit -m "docs: split GUARDRAILS and move game design content to canonical docs"
```