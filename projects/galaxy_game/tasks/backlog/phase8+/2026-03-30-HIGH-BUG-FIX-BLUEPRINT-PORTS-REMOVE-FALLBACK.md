---
name: "Blueprint Ports Remove Hardcoded Fallback"
priority: HIGH
phase: phase8
created: 2026-06-21
status: backlog
type: bug-fix
---

# TASK: Fix HasBlueprintPorts — Remove Fallback and Hardcoded Blueprint ID

**Priority**: HIGH  
**Phase**: phase8 (Cycler Arc)  
**Type**: bug-fix  
**Created**: 2026-06-21  

## Problem Statement

HasBlueprintPorts concern has two critical bugs that violate blueprint-driven design:

1. **Hardcoded fallback to 'generic_satellite' blueprint** for all craft types
2. **Silent default port fallback** returning 5 ports of each type when no blueprint found

These bugs mean misconfigured or missing blueprints silently give crafts more ports than intended, breaking game balance and economic differentiation.

## Evidence of Bug

```bash
grep -n "generic_satellite\|hardcoded" galaxy_game/app/models/concerns/has_blueprint_ports.rb
# Output: line 15: blueprint_id = 'generic_satellite'
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/models/concerns/has_blueprint_ports.rb` | Port definitions concern | Lines 11-20 (blueprint lookup) |

## Acceptance Criteria

- [ ] No hardcoded `'generic_satellite'` fallback in blueprint lookup
- [ ] Missing blueprints raise an error or return zero ports (not silent default of 5)
- [ ] Craft port counts are strictly constrained by their actual blueprint data
- [ ] All craft models correctly inherit port definitions from their specific blueprints
- [ ] Tests verify that missing blueprints do NOT silently grant default ports

## Implementation Steps

1. Remove `blueprint_id = 'generic_satellite'` hardcoded fallback (line 15)
2. Replace silent default of 5 ports with proper error handling or zero-port return
3. Ensure craft models always look up their specific blueprint, not a generic one
4. Add test case for missing blueprint scenario

## Commit Instructions

```bash
git add galaxy_game/app/models/concerns/has_blueprint_ports.rb
git commit -m "fix: remove hardcoded generic_satellite fallback in HasBlueprintPorts"
```
