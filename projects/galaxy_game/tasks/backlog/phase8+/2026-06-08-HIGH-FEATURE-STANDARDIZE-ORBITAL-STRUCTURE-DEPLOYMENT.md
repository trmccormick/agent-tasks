# Standardize Orbital Structure Deployment with CelestialLocation Creation

```yaml
id: 2026-06-08-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT
status: backlog
priority: HIGH
type: REFACTOR_FEATURE
created: 2026-06-08
last_updated: 2026-06-09
system_domain: ORBITAL_INFRASTRUCTURE | DEPLOYMENT_SERVICES
mvp_alignment: PHASE_6_L1_DEPOT_INFRASTRUCTURE
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (April 2026 backlog triage)

The April 2026 task identified that `OrbitalSettlement#location` returns nil because orbital structures don't create `CelestialLocation` on deployment. A reference implementation exists in `AIManager::DepotAdapter#create_depot`, but it's isolated — no standard pattern applies to all orbital structure types (L1 stations, cycler assembly points, Venus processing platforms).

**Why Phase 6 Prerequisite?** This is required before L1 Depot and LEO Depot construction begins. It blocks:
- Gas processing pipeline at L1 Station (`2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md`) — needs valid locations for skimmer docking routing  
- WormholeNavigator upgrade pathfinding costs — uses SpatialLocation distances between celestial bodies  
- Foothold establishment service — creates orbital depots at new systems

**Phase placement note (updated 2026-06-09):** Originally tagged PHASE_5_AUTONOMOUS_EXPANSION_PREREQUISITE. Corrected to PHASE_6_L1_DEPOT_INFRASTRUCTURE — Phase 5 is simulation observation only, no feature work. This task becomes relevant when L1 Station construction begins.

---

## Problem Statement

**Current State:**
```ruby
# app/models/settlement/orbital_settlement.rb (LIVE)
def location
  structures.first&.celestial_location # ← Returns nil if no structure has CelestialLocation ❌
end

# app/services/ai_manager/depot_adapter.rb (PARTIAL — works only for depots created via this service)
unless depot.location
  Location::CelestialLocation.create!(
    celestial_body: world,
    latitude: 0.0, longitude: 0.0,
    altitude: calculate_orbital_altitude(world),
    locationable: depot # ← Creates CelestialLocation ✅ but only for depots created here
  
end

# app/models/structures/orbital_structure.rb (MISSING — no auto-creation)
after_create :initialize_atmosphere_if_needed  # ← No celestial_location creation ❌
```

**Expected Behavior:** Every `OrbitalStructure` gets a `CelestialLocation` when deployed, regardless of deployment method. Standard pattern works for:
- L1 Station (LDC + AstroLift joint venture)  
- Venus orbital processing platforms  
- Mars/Phobos depots on cycler routes  
- Titan/Belt outpost stations

---

## Reference Implementation — Read Before Starting

**Working Pattern**: `app/services/ai_manager/depot_adapter.rb`
```ruby
module AIManager
  module DepotAdapter
    
    def self.create_depot(world_key, world)
      depot = Settlement::OrbitalSettlement.find_or_create_by!(...)
      
      unless depot.location
        name_service = NameGeneratorService.new
        Location::CelestialLocation.create!(
          celestial_body: world,
          name: name_service.generate_identifier,  # ← Generates unique location identifier
          coordinates: '0.00°N 0.00°E',           # ← Static orbital position (no simulation yet)
          altitude: calculate_orbital_altitude(world),
          locationable: depot                      # ← Polymorphic association to OrbitalStructure
      
      end

    def self.calculate_orbital_altitude(world)
      km = world.properties&.dig('standard_orbital_altitude_km')
      return km.to_f * 1000.0 if km.present?
      10_000_000.0 # default 10,000 km fallback
      
    end

```

**Key Design Decisions Embedded:**
- Static orbital positions (no dynamic simulation) — documented limitation  
- Altitude from world properties with sensible fallback  
- Name generation service for unique location identifiers  
- Bridge convention: one structure per settlement until multi-structure routing implemented

---

## Current State Analysis

### Existing Infrastructure to Leverage (Read Before Starting)

| File | Purpose | Key Methods/Sections |
|------|---------|---------------------|
| `app/models/settlement/orbital_settlement.rb` | Settlement model with location delegation | `location`, `celestial_body`, `total_storage_capacity` methods ✅ |
| `app/services/ai_manager/depot_adapter.rb` | Reference implementation for depot creation | `create_depot`, `calculate_orbital_altitude` — pattern to generalize ✅ |
| `app/models/location/celestial_location.rb` | Location model with polymorphic association | Confirm required fields: celestial_body, latitude, longitude, altitude, locationable |

### Related Phase 5+ Tasks (Blocked by This One)
- `2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md` — L1 Station gas processing needs valid locations  
- `2026-06-07-MEDIUM-FEATURE-WORMHOLE-NAVIGATOR-UPGRADE.md` — pathfinding uses SpatialLocation distances
- `2026-06-07-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE.md` — creates orbital depots at new systems

---

## Implementation Scope

### In Scope (Phase 5 Prerequisite)

**Step 1: Add after_create Callback to OrbitalStructure**
```ruby
# app/models/structures/orbital_structure.rb
module Structures
  class OrbitalStructure < BaseStructure
    
    # Existing associations ✅
    has_one :celestial_location, as: :locationable, 
             class_name: 'Location::CelestialLocation', dependent: :destroy
    
    delegate :celestial_body, to: :celestial_location, allow_nil: true
    
    # ADD THIS — create CelestialLocation on deployment (mirrors DepotAdapter pattern)
    after_create :initialize_celestial_location_if_missing  # ← New callback
    
    private

      def initialize_celestial_location_if_missing
        return if celestial_location.present?  # Skip if already created
        
        Location::CelestialLocation.create!(
          celestial_body: determine_orbital_around_body,
          name: generate_unique_identifier,
          coordinates: '0.00°N 0.00°E',  # Static orbital position (no simulation yet)
          altitude: calculate_default_orbital_altitude,
          locationable: self
        )
      end
      
      def determine_orbital_around_body
        # Infer from settlement name or operational_data context  
        # Fallback to first celestial body if ambiguous — document this limitation
        Settlement::OrbitalSettlement.find_by(orbital_structures: { id: id })&.celestial_body || 
          CelestialBody.first
      
      end

      def generate_unique_identifier
        NameGeneratorService.new.generate_identifier  # Reuse existing service from DepotAdapter
      end
      
      def calculate_default_orbital_altitude
        body = determine_orbital_around_body
        km = body.properties&.dig('standard_orbital_altitude_km')
        return km.to_f * 1000.0 if km.present?
        10_000_000.0 # default 10,000 km fallback (matches DepotAdapter)
      
      end

```

**Step 2: Update SettlementDeploymentService for Orbital Structures**  
```ruby
# app/services/settlement_deployment_service.rb — extend existing service
class SettlementDeploymentService
  
  def self.establish_orbital_settlement(world:, owner:)
    # Create settlement + structure with automatic CelestialLocation via after_create callback
    
    ActiveRecord::Base.transaction do
      settlement = Settlement::OrbitalSettlement.create!(
        name: "#{world.name} Orbital Station",
        settlement_type: :station,
        owner: owner,
        operational_data: { 'purpose' => 'orbital_depot', 'world_key' => world.key.to_s }
      )

      # Create primary structure — after_create callback will add CelestialLocation automatically ✅  
      structure = Structures::OrbitalStructure.create!(
        settlement: settlement,
        identifier: 'l1_station_blueprint_id',  # ← Use appropriate blueprint for station type
        shell_status: :operational
      )

```

**Step 3: Deprecate DepotAdapter#create_depot (Keep as Alias)**  
```ruby
# app/services/ai_manager/depot_adapter.rb — maintain backward compatibility
module AIManager
  module DepotAdapter
    
    # DEPRECATED: Use SettlementDeploymentService.establish_orbital_settlement instead
    def self.create_depot(world_key, world)
      Rails.logger.warn("DeprecationWarning: DepotAdapter#create_deprecated is deprecated")
      
      # Delegate to new standard service — maintains existing behavior for now  
      SettlementDeploymentService.establish_orbital_settlement(
        world: world, 
        owner: Organizations::BaseOrganization.find_by!(type: 'station_construction')  # Zenith Orbital
      
    end

```

---

### Out of Scope (Extracted as Separate Future Tasks) 📝

**1. Multi-Structure Routing Per Settlement — Phase 6+ Task Extracted:**
📄 `2026-06-MEDIUM-FEATURE-MULTI-STRUCTURE-ROUTING-PER-SETTLEMENT.md` created separately  
Why: Current bridge convention (one structure per settlement) is intentional simplification. Multi-structure routing requires:
- Routing logic to select which structure handles docking/processing for given craft type  
- Load balancing across structures based on storage capacity, processing queue depth  
- Settlement-level location abstraction that aggregates multiple CelestialLocations

**2. Dynamic Orbital Position Simulation — Phase 6+ Task Extracted:**
📄 `2026-06-MEDIUM-FEATURE-DYNAMIC-ORBITAL-POSITION-SIMULATION.md` created separately  
Why: Static positions are documented limitation but acceptable for MVP. Dynamic simulation requires:
- Game loop time advancement system (doesn't exist yet)  
- Orbital mechanics calculations using existing `OrbitalMechanics#orbital_period_around`, `calculate_orbital_velocity` methods  
- Position recalculation on each game tick or query-time calculation

**3. Blueprint Selection Logic — Phase 5+ Task:**
📄 Consider extracting as part of Foothold Establishment Service task (already exists)  
Why: Different orbital structure types need different blueprints (L1 station vs Venus processing platform). This belongs with foothold establishment logic that determines what to build where.

---

## Technical Requirements

### Location Model Validation — Confirm Before Implementing
```bash
# Check CelestialLocation required fields and associations
cat app/models/location/celestial_location.rb | head -40

# Verify polymorphic association works correctly  
grep -n "has_one.*locationable\|belongs_to.*location" \
  ~/Documents/git/galaxyGame/galaxy_game/app/models/location/celestial_location.rb

# Check if NameGeneratorService exists and how it's used
cat app/services/name_generator_service.rb | head -30 || echo "File not found — may need to create or use alternative pattern"
```

### Energy Model Assumption (Document in Code)
Static orbital positions mean no dynamic eclipse periods for solar power calculations. Gas processing pipeline can assume continuous energy availability at all orbital stations. Document this assumption clearly:

```ruby
# app/models/structures/orbital_structure.rb — add comment block
# ORBITAL POSITION LIMITATION (Phase 6+ Enhancement)
# CelestialLocation stores static coordinates seeded during deployment. 
# No dynamic orbital simulation exists yet — celestial bodies don't move as game time advances.
# This means:
# - Solar power is always available at orbital stations (no eclipse periods to model)  
# - Intra-system distances use snapshot geometry, not real-time positions
# - Gas processing pipeline can assume continuous energy input without battery buffers
# When simulation is built, OrbitalMechanics concern already has methods ready:
#   - #orbital_period_around — calculates orbital period around parent body
#   - #calculate_orbital_velocity — determines velocity at current position

```

---

## Testing Requirements

### RSpec Coverage — Must Include

**Unit Tests:**
- `OrbitalStructure` after_create callback creates valid CelestialLocation with all required fields  
- Altitude calculation uses world properties when available, falls back to 10M meters default  
- Name generation produces unique identifiers (no collisions across multiple deployments)

**Integration Tests:**
- SettlementDeploymentService.establish_orbital_settlement creates settlement + structure + location in one transaction  
- `OrbitalSettlement#location` returns valid CelestialLocation after deployment service runs  
- Existing DepotAdapter#create_depot still works (backward compatibility test)

**System Tests:**
- Gas processing pipeline can access station.location.celestial_body for skimmer routing logic  
- WormholeNavigator pathfinding uses SpatialLocation distances correctly between celestial bodies  

---

## Dependencies & Blockers

### Required Before Starting (Already Implemented ✅)
- `OrbitalSettlement#location` method delegates to structures — **LIVE**  
- `CelestialLocation` model with polymorphic association — **LIVE**  
- DepotAdapter reference implementation proves pattern works — **LIVE**  

### Phase 5+ Tasks That Depend on This (Blocked Until Complete)
- Gas processing pipeline at L1 Station (`2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md`)  
- WormholeNavigator upgrade pathfinding costs (`2026-06-07-MEDIUM-FEATURE-WORMHOLE-NAVIGATOR-UPGRADE.md`)
- Foothold establishment service creates orbital depots at new systems

### Not Blocking (Extracted as Separate Tasks)
- Multi-structure routing per settlement → Phase 6+ task created separately  
- Dynamic orbital position simulation → Phase 6+ task created separately  

---

## Success Criteria & Acceptance Tests

**Functional:**
1. ✅ Every `OrbitalStructure` has valid CelestialLocation after creation (no nil returns)  
2. ✅ `OrbitalSettlement#location` works for all settlement types, not just depots from DepotAdapter  
3. ✅ SettlementDeploymentService can create orbital settlements with automatic location assignment  
4. ✅ Existing DepotAdapter#create_depot still functions correctly (backward compatibility maintained)

**Non-Functional:**
5. ✅ Full RSpec coverage — unit + integration tests pass green  
6. ✅ No breaking changes to existing code that uses OrbitalSettlement#location or CelestialLocation associations  
7. ✅ Clear documentation of static position limitation and Phase 6+ enhancement path  

---

## Notes & Caveats

**Static Position Limitation:** Documented in code comments — acceptable for MVP, dynamic simulation is Phase 6+. This doesn't block gas processing pipeline since orbital stations always have solar power (no eclipse periods to model).

**Blueprint Selection Logic:** Task uses placeholder blueprint ID (`'l1_station_blueprint_id'`). Real implementation needs logic to select appropriate blueprint based on station type/purpose. Consider extracting this into Foothold Establishment Service task if it becomes complex enough to warrant separate focus.

**NameGeneratorService Dependency:** If service doesn't exist, use alternative pattern (UUID generation or timestamp-based identifiers). Document the choice clearly for future maintainers.

---

## Related Tasks & References

### Phase 5+ Task Files in Same Folder
- `2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md` — depends on this task completing  
- `2026-06-07-MEDIUM-FEATURE-WORMHOLE-NAVIGATOR-UPGRADE.md` — uses SpatialLocation distances
- `2026-06-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md` (this task)

### Phase 6+ Tasks Extracted from This Review Session  
- `2026-06-MEDIUM-FEATURE-MULTI-STRUCTURE-ROUTING-PER-SETTLEMENT.md` — multi-structure routing per settlement
- `2026-06-MEDIUM-FEATURE-DYNAMIC-ORBITAL-POSITION-SIMULATION.md` — dynamic orbital mechanics

### Archived Source Material  
Original task: `docs/agent/archive/backlog_april_2026/deprecated/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (move to deprecated after this created)

---

## Task File Metadata (For Agent Workflow)

**Created By:** Qwen3.5-27B during April 2026 backlog triage session  
**Extracted From:** `docs/agent/archive/backlog_april_2026/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (April 2026 task review)  
**Assigned To:** TBD — requires executor with experience in model callbacks + deployment services  
**Estimated Complexity:** MEDIUM — single model change + service extension, clear reference implementation exists  
**Supervision Level:** 🟡 Standard — well-scoped but needs validation of NameGeneratorService dependency
