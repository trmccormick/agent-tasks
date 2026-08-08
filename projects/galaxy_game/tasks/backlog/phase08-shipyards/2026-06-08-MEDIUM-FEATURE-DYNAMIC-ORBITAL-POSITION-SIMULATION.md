# Dynamic Orbital Position Simulation — Celestial Body Movement Over Time

```yaml
id: 2026-06-08-MEDIUM-FEATURE-DYNAMIC-ORBITAL-POSITION-SIMULATION
status: backlog
priority: MEDIUM
type: FEATURE
created: 2026-06-08
last_updated: 2026-06-08
system_domain: ORBITAL_INFRASTRUCTURE | SIMULATION_ENGINE
mvp_alignment: PHASE_6_ADVANCED_ORBITAL_OPERATIONS  
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (April 2026 backlog triage — identified as future enhancement)

Current limitation: **Static orbital positions** stored in `Location::CelestialLocation`. Celestial bodies don't move as game time advances. This is acceptable for MVP but limits realism and blocks advanced features like eclipse period modeling, dynamic transit times between moving targets, accurate solar power availability calculations at orbital stations during shadow periods.

This task implements basic orbital mechanics simulation using existing infrastructure in `OrbitalMechanics` concern (`orbital_period_around`, `calculate_orbial_velocity`). Positions recalculated on game tick or query-time based on elapsed time since deployment.

**Why Phase 6?** Static positions work fine for Luna Phases + Phase 5 supply chain operations:
- Gas processing at orbital stations assumes continuous solar power (no eclipse periods to model)  
- WormholeNavigator pathfinding uses snapshot distances between bodies — acceptable approximation until cycler routes need precise timing windows  
- Skimmer overflow routing doesn't require real-time body positions

Dynamic simulation becomes relevant when:
- Eclipse period modeling affects energy availability at orbital stations  
- Cycler route planning needs accurate transit time calculations to moving targets  
- Player missions involve intercepting craft on specific orbital trajectories  

---

## Problem Statement

**Current State (Static Positions):**
```ruby
# app/models/structures/orbital_structure.rb — after_create callback creates static location
def initialize_celestial_location_if_missing
  Location::CelestialLocation.create!(
    celestial_body: determine_orbital_around_body,
    name: generate_unique_identifier,  
    coordinates: '0.00°N 0.00°E',  # ← Static position (no simulation yet) ❌
    altitude: calculate_default_orbital_altitude,
    locationable: self
  
end

# app/models/location/spatial_location.rb — stores seeded x/y/z coordinates  
class SpatialLocation < ApplicationRecord
  # No time-based recalculation exists — positions are snapshot at deployment only
  
```

**Expected Behavior:** Positions recalculated based on elapsed game time using orbital mechanics formulas. Two implementation patterns to consider:
- **Tick-based**: Advance all body positions each game tick (computationally expensive but accurate)  
- **Query-time calculation**: Compute position when needed from last known state + elapsed time (lazy evaluation, more efficient)

---

## Current State Analysis

### Existing Infrastructure to Leverage (Read Before Starting)

| File | Purpose | Key Methods/Sections |
|------|---------|---------------------|
| `app/models/concerns/orbital_mechanics.rb` | Orbital mechanics formulas already exist | `orbital_period_around`, `calculate_orbital_velocity` — foundation for simulation ✅ |
| `app/models/location/spatial_location.rb` | 3D coordinate storage with distance_to method | Euclidean distance calculation works, needs extension for time-based positions ⚠️ |

### Reference Documentation — Read First  
- Original source: `docs/agent/archive/backlog_april_2026/deprecated/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (static position limitation documented)
- Status.md architecture section — "Confirmed Architecture" notes static positions are intentional simplification for MVP

---

## Implementation Scope

### In Scope (Phase 6+)

**Step 1: Extend SpatialLocation with Time-Based Position Calculation**  
```ruby
# app/models/location/spatial_location.rb — add temporal awareness
  
module Location
  class SpatialLocation < ApplicationRecord
    
    # Existing static position storage ✅
    attribute :x, :float
    attribute :y, :float  
    attribute :z, :float

    # NEW: Temporal tracking for dynamic positions (Phase 6+)
    attribute :reference_time, :datetime  # Game time when x/y/z snapshot was valid
    
    def position_at(game_time)
      return [x, y, z] if game_time == reference_time
      
      elapsed_seconds = (game_time - reference_time).to_f
      
      # Calculate new position based on orbital mechanics  
      velocity_vector = calculate_velocity_vector(elapsed_seconds)
      
      new_x = x + (velocity_vector[:vx] * elapsed_seconds)
      new_y = y + (velocity_vector[:vy] * elapsed_seconds)  
      new_z = z + (velocity_vector[:vz] * elapsed_seconds)

    def distance_to_at(other_location, game_time: nil)
      # Use current time if not specified — enables dynamic transit calculations
      
      my_pos = position_at(game_time || Time.current)
      
      other_pos = other_position_at(other_location, game_time || Time.current)
    
```

**Step 2: Orbital Mechanics Integration**  
```ruby
# app/models/concerns/orbital_mechanics.rb — extend existing formulas
  
module OrbitalMechanics
    
    # Existing methods (already implemented ✅):
    def orbital_period_around(parent_body_mass:)
      # Returns period in seconds using Kepler's third law
      
    end

    def calculate_orbital_velocity(distance_from_parent:, parent_body_mass:)  
      # Returns velocity magnitude at given distance
      
    end

```

**Step 3: Eclipse Period Detection (Optional Enhancement)**  
```ruby
# app/models/structures/orbital_structure.rb — add energy availability tracking
  
module Structures
  class OrbitalStructure < BaseStructure
    
    def in_eclipse_period?(game_time = Time.current)
      # Calculate if currently shadowed by parent body from sun's perspective
      
      orbital_position = celestial_location.spatial_location.position_at(game_time)
      
      parent_body_radius = celestial_body.radius_meters  
      sun_vector = calculate_sun_direction(game_time)  # ← Requires solar system model
      
      dot_product = (orbital_position - parent_center).dot(sun_vector)
      
      in_shadow_zone? = dot_product < 0 && distance_from_parent_edge < eclipse_threshold
      
    end

```

---

### Out of Scope (Future Enhancements or Already Handled Elsewhere)

- **Full N-body gravitational simulation** — overkill for game purposes, Keplerian orbits sufficient  
- **Real-time orbital perturbations** (atmospheric drag at low orbit, third-body effects from moons/planets)
- **Player craft trajectory planning with Hohmann transfers** — separate mission system task if needed

---

## Technical Requirements

### Game Time System Dependency  
```bash
# Check how game time is tracked and advanced in the simulation
grep -rn "game_time\|advance_time" ~/Documents/git/galaxyGame/galaxy_game/app/services/ai_manager/ --include="*.rb" | head -10

# Verify if there's a central clock or each service manages its own time state  
cat app/models/game.rb 2>/dev/null || echo "No Game model found — may need to create global time tracking"
```

### Implementation Pattern Choice (Document Decision)  

**Option A: Tick-Based Advancement**  
- Pros: Accurate, all positions synchronized at same game timestamp  
- Cons: Computationally expensive if many bodies tracked, requires persistent storage updates each tick  

**Option B: Query-Time Calculation (Recommended for MVP)**  
- Pros: Lazy evaluation, no continuous computation overhead, stores only reference state + orbital parameters  
- Cons: Slightly more complex distance calculations when comparing positions at different times

---

## Testing Requirements

### RSpec Coverage — Must Include

**Unit Tests:**
- SpatialLocation.position_at(game_time) returns correct coordinates based on elapsed time and orbital velocity  
- Distance calculation between two moving bodies accounts for both position changes over time  

**Integration Tests:**
- Eclipse period detection correctly identifies shadow zones during orbital transit around parent body  
- WormholeNavigator pathfinding still works with dynamic positions (backward compatibility test)

---

## Dependencies & Blockers

### Required Before Starting:
- Standardize orbital structure deployment (`2026-06-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md`) — ensures all structures have valid CelestialLocation associations  
- Game time tracking system exists (verify during implementation, may need to create if missing)

### Not Blocking (Can Implement Independently):
- Multi-structure routing per settlement → separate Phase 6+ task  

---

## Success Criteria & Acceptance Tests

**Functional:**
1. ✅ Celestial body positions update based on elapsed game time using orbital mechanics formulas  
2. ✅ Distance calculations between moving bodies account for temporal position changes  
3. ✅ Eclipse period detection works correctly (optional enhancement, document if skipped)

**Non-Functional:**
4. ✅ Full RSpec coverage for dynamic position calculation and distance methods  
5. ✅ No breaking changes to existing code that assumes static positions (backward compatible via query-time pattern)  

---

## Notes & Caveats

**Query-Time Pattern Recommended:** Store reference state + orbital parameters, calculate actual position when queried with game timestamp. This avoids expensive tick-based updates while enabling accurate dynamic calculations when needed.

**Eclipse Detection Optional:** Gas processing pipeline assumes continuous solar power at orbital stations (Phase 5+ design decision). Eclipse period modeling is enhancement for realism but not required for core functionality to work correctly.

---

## Related Tasks & References

### Phase 6+ Peer Tasks (Independent but Related):
- `2026-06-MEDIUM-FEATURE-MULTI-STRUCTURE-ROUTING-PER-SETTLEMENT.md` — multi-structure routing per settlement  

### Archived Source Material  
Original task: `docs/agent/archive/backlog_april_2026/deprecated/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (static position limitation documented during review)

---

## Task File Metadata (For Agent Workflow)

**Created By:** Qwen3.5-27B during April 2026 backlog triage session  
**Extracted From:** `docs/agent/archive/backlog_april_2026/deprecated/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (identified as future enhancement during review)  
**Assigned To:** TBD — requires executor with experience in orbital mechanics + time-based simulation patterns  
**Estimated Complexity:** MEDIUM to HIGH depending on game time system complexity and whether eclipse detection implemented  
**Supervision Level:** 🟡 Standard — clear scope but needs architectural decisions on implementation pattern (tick vs query-time)
