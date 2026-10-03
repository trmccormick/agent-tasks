# Synthesis: MissionPlannerService Entry Point Refactor

**Task**: `2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md`  
**Date**: 2026-09-30  
**Status**: Ready to implement  

---

## What I'm About to Do

Add a resource-first class method `for_body(celestial_body, system_context: {}, parameters: {})` to `MissionPlannerService` while preserving the existing `.new(pattern_name, parameters = {})` pattern path. The new entry composes with the **existing** `FootholdPlanner#plan` — no FootholdPlanner internals are touched.

---

## Files I'll Reference

| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/ai_manager/mission_planner_service.rb` | Add `for_body` class method; keep `.new(pattern_name, params)` intact | Will edit |
| `galaxy_game/app/services/ai_manager/foothold_planner.rb` | Compose via `.new(body, system_context: {}).plan`; returns ranked Array of FootholdOption with `to_h` | Read-only — verified exists on main |
| `galaxy_game/app/services/ai_manager/pattern_target_mapper.rb` | Used by pattern path only — `for_body` must NOT use it | Read-only reference |

---

## Prerequisites Completed

- ✅ Step 0 completed (task moved to active/)
- ✅ FootholdPlanner verified on main: `class AIManager::FootholdPlanner` at `app/services/ai_manager/foothold_planner.rb`
- ✅ `#plan` confirmed public entry — returns ranked Array of `FootholdOption` (Struct with `to_h`)
- ✅ `system_context` keys confirmed from FootholdPlanner comments: `:moons`, `:asteroids`, `:distance_from_sun`, `:parent_body`, `:nearby_nodes`
- ✅ Capability stays internal via `PrecursorCapabilityService` inside FootholdPlanner
- ✅ `.new(pattern_name, parameters = {})` arity confirmed — first arg is positional
- ✅ All three Gotchas understood (no break pattern path, no full planner rewrite, composition over deep rewrite)

---

## Implementation Plan

### 1. Add `for_body` class method to MissionPlannerService

```ruby
# Resource-first entry point: accepts a celestial body + system context
# instead of a pattern name. Composes with FootholdPlanner for ranking.
#
# @param celestial_body [CelestialBody] The target body to plan for
# @param system_context [Hash] Optional system topology snapshot
# @param parameters [Hash] Optional additional parameters forwarded to simulation
# @return [Array<FootholdOption>] Ranked foothold options (best first)
def self.for_body(celestial_body, system_context: {}, parameters: {})
  raise ArgumentError, "celestial_body is required" if celestial_body.nil? || celestial_body.empty?

  planner = AIManager::FootholdPlanner.new(celestial_body, system_context: system_context)
  ranked_options = planner.plan

  # Optionally run simulation/costing on top of the ranked options
  # using existing @material_lookup and other instance helpers
  # (delegate to simulate method or inline as needed)

  ranked_options
end
```

### 2. No changes to `.new(pattern_name, parameters = {})`
- Keep initializer exactly as-is — no arity changes, no reordering of arguments.

### 3. Spec additions (in existing spec file or new focused spec)
- Pattern path still works: `MissionPlannerService.new("mars-terraforming", {}).simulate`
- `for_body(valid_body)` returns Array of FootholdOption without PatternTargetMapper
- `for_body(nil)` raises ArgumentError

---

## Expected Outcomes

- `.new(pattern_name, {})` still works — no arity error; simulate path intact
- `for_body(valid_body, ...)` returns foothold options without PatternTargetMapper
- `for_body(nil)` or missing body raises explicit error (no silent OpenStruct target)
- No broad rewrite of internal costing/timeline logic
- FootholdPlanner internals untouched

---

## Critical Gotchas I Will Avoid

- ❌ Breaking existing callers — instead ✅ dual path
- ❌ Implementing the full planner — instead ✅ compatibility layer only
- ❌ Using PatternTargetMapper for the `for_body` path
- ❌ Rewriting FootholdPlanner internals — instead ✅ compose via `.new(body, system_context: {}).plan`

---

**SYNTHESIS COMPLETE.** Ready to proceed with code changes.
