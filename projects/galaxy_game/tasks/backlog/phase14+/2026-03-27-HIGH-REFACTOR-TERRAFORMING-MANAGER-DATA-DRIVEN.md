[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/2026-03-27-HIGH-REFACTOR-TERRAFORMING-MANAGER-DATA-DRIVEN.md]
[RATIONALE: Terraforming manager refactor — belongs in Phase 14 (coordinated Mars-Venus terraforming), not Luna surface]
---
name: "TerraformingManager Data-Driven Refactor"
priority: HIGH
phase: phase6
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor TerraformingManager to be Data-Driven

**Priority**: HIGH  
**Phase**: phase6 (Tutorial Arc)  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

TerraformingManager uses hardcoded `@worlds` hash with named keys (`:mars`, `:venus`, `:titan`, `:saturn`). This makes the service brittle and non-extensible.

**Current behavior**: Only works for four hardcoded worlds, cannot generalize to new celestial bodies.  
**Expected behavior**: Accepts a solar_system, queries celestial_bodies dynamically, never hardcodes world names.

## Evidence of Incompleteness

```bash
grep -n "@worlds" galaxy_game/app/services/ai_manager/terraforming_manager.rb
# Output: 5+ references to @worlds[world_key] lookups
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/ai_manager/terraforming_manager.rb` | Main service | All `@worlds`, `world_key` references |
| `galaxy_game/spec/services/ai_manager/terraforming_manager_spec.rb` | Tests | World setup and references |

## Acceptance Criteria

- [ ] TerraformingManager initializer accepts a `solar_system` object, not a `worlds` hash
- [ ] All `@worlds[world_key]` lookups replaced with dynamic queries against `solar_system.celestial_bodies`
- [ ] No hardcoded world names remain in comments or logic
- [ ] All existing tests pass after refactor
- [ ] Service works with any solar system configuration (not just Mars/Venus/Titan/Saturn)

## Implementation Steps

1. Change TerraformingManager initializer to accept `solar_system` instead of `worlds` hash
2. Replace all `@worlds[world_key]` and `@worlds.each` references with dynamic queries against `solar_system.celestial_bodies`
3. Remove all hardcoded world name references and comments
4. Update tests to use solar_system setup instead of worlds hash

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/terraforming_manager.rb galaxy_game/spec/services/ai_manager/terraforming_manager_spec.rb
git commit -m "refactor: make TerraformingManager data-driven, remove hardcoded world references"
```
