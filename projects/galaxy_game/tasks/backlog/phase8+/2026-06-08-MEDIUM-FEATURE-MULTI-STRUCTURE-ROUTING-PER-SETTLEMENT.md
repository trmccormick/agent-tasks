# Multi-Structure Routing Per Settlement — Load Balancing & Selection Logic

```yaml
id: 2026-06-08-MEDIUM-FEATURE-MULTI-STRUCTURE-ROUTING-PER-SETTLEMENT
status: backlog
priority: MEDIUM
type: FEATURE
created: 2026-06-08
last_updated: 2026-06-08
system_domain: ORBITAL_INFRASTRUCTURE | ROUTING_LOGIC
mvp_alignment: PHASE_6_ADVANCED_ORBITAL_OPERATIONS
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (April 2026 backlog triage — identified as future enhancement)

Current bridge convention: **One structure per OrbitalSettlement** until multi-structure routing is implemented. This simplification works for MVP but limits orbital station scalability. Real L1 Station will have multiple specialized structures:
- Primary docking hub (craft arrivals/departures)  
- Gas processing platforms (liquefaction/compression units)  
- Storage depots (bulk propellant reserves)  
- Habitat modules (crew quarters, emergency refuge)

This task implements routing logic to select which structure handles given operations based on craft type, cargo manifest, and current load.

**Why Phase 6?** Luna Phases + Phase 5 establish single-system supply chains with one orbital depot per location. Multi-structure routing only becomes relevant when:
- L1 Station scales beyond initial deployment  
- Cycler assembly requires multiple specialized structures working together  
- Venus processing platforms need dedicated skimmer docking vs cycler refueling bays

---

## Problem Statement

**Current State (Bridge Convention):**
```ruby
# app/models/settlement/orbital_settlement.rb — assumes single structure
def location
  structures.first&.celestial_location  # ← Only first structure matters ❌
end

def total_storage_capacity  
  structures.sum(&:total_storage_capacity)  # Aggregates but doesn't route to specific structure
  
end

# No logic exists for: "Which structure should this skimmer dock at?" or 
# "Where does cycler refueling happen vs gas processing intake?"
```

**Expected Behavior:** Routing service selects optimal structure based on:
- Craft type (skimmer, cycler, HLT, cargo hauler)  
- Cargo manifest (raw gases need processing platforms, propellant needs storage depots)  
- Current load balancing (avoid overloading single docking hub when alternatives available)

---

## Implementation Scope

### In Scope (Phase 6+)

**Step 1: Structure Selection Service**
```ruby
# app/services/orbital/structure_router.rb — new service
module Orbital
  class StructureRouter
    
    def self.select_docking_structure(settlement, craft_type:, cargo_manifest:)
      # Priority routing logic based on structure capabilities
      
      if cargo_manifest&.includes_raw_gases? && settlement.has_processing_platforms?
        return settlement.processing_structures.find_least_loaded_by_storage_capacity
        
      elsif cargo_manifest&.requires_propellant_refueling?  
        return settlement.storage_depots.find_with_highest_available_capacity(cargo_manifest.propellant_requirements)
      
      else  # General docking — use primary hub or least loaded structure
      
        return settlement.primary_docking_hub || 
               settlement.operational_structures.min_by(&:current_utilization_rate)
        
      end

    def self.can_accept_craft?(structure, craft_type:, cargo_manifest:)  
      storage_available = structure.total_storage_capacity - structure.current_inventory_volume
      
      # Check specialized capabilities match cargo needs
      has_processing_capability = if cargo_manifest.includes_raw_gases?
        structure.base_units.any? { |u| u.unit_type == 'gas_processing_unit' }
      
      end

    def self.calculate_utilization_rate(structure)  
      current_storage_used = structure.current_inventory_volume.to_f
      
      return 0.0 unless capacity > 0
    
```

**Step 2: Settlement-Level Location Abstraction**  
```ruby
# app/models/settlement/orbital_settlement.rb — extend location method for multi-structure context
  
module Settlement
  class OrbitalSettlement < ApplicationRecord
    
    # Keep existing single-structure behavior for backward compatibility ✅
    def location
      structures.first&.celestial_location
      
    end

    # NEW: Route-aware location selection based on craft type and cargo  
    def location_for(craft_type:, cargo_manifest:)
      selected_structure = Orbital::StructureRouter.select_docking_structure(
        self, 
        craft_type: craft_type, 
        cargo_manifest: cargo_manifest
      )
      
      selected_structure&.celestial_location
      
    end

```

**Step 3: Load Balancing Metrics & Monitoring**  
```ruby
# app/models/structures/orbital_structure.rb — add utilization tracking
  
module Structures
  class OrbitalStructure < BaseStructure
    
    def current_utilization_rate
      return 0.0 if total_storage_capacity == 0
      
      used = base_units.sum { |u| u.operational_data&.dig('storage', 'current_volume') || 0 }
      
      (used.to_f / total_storage_capacity).round(4)
    
    end

```

---

### Out of Scope (Future Enhancements or Already Handled Elsewhere)

- **Dynamic orbital position simulation** — separate Phase 6+ task (`2026-06-MEDIUM-FEATURE-DYNAMIC-ORBITAL-POSITION-SIMULATION.md`)  
- **Gas processing pipeline implementation** — handled by `2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md` (Phase 5+)
- **Craft docking mechanics** — existing Docking concern on OrbitalStructure handles physical docking, this task only selects which structure

---

## Technical Requirements

### Structure Capability Detection Pattern  
```ruby
# How to identify specialized structures within settlement?
def processing_structures
  orbital_structures.where(identifier: ['gas_processing_platform', 'liquefaction_station'])
end

def storage_depots
  orbital_structures.where(identifier: ['bulk_propellant_depot', 'cryogenic_storage_hub'])
end

def primary_docking_hub  
  orbital_structures.find_by(identifier: 'l1_primary_docking') || 
    operational_structures.first
  
end

### Load Balancing Algorithm Choice (Document Decision)
Options to consider during implementation:
- **Round-robin**: Simple, fair distribution but ignores cargo type matching  
- **Least-loaded by storage capacity**: Prevents bottlenecks at single structure  
- **Specialized routing first** (recommended): Match craft/cargo to capable structures, then load balance within category

---

## Testing Requirements

### RSpec Coverage — Must Include

**Unit Tests:**
- StructureRouter.select_docking_structure returns correct structure type for given cargo manifest  
- Utilization rate calculation handles edge cases (zero capacity, no inventory)  

**Integration Tests:**
- Settlement.location_for(craft_type:, cargo_manifest:) routes to appropriate specialized structures  
- Load balancing distributes craft arrivals across multiple storage depots when available

---

## Dependencies & Blockers

### Required Before Starting (Phase 5+ Tasks Must Complete First):
- Standardize orbital structure deployment (`2026-06-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md`) — ensures all structures have valid locations  
- Gas processing pipeline at L1 Station (`2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md`) — defines structure types and capabilities

### Not Blocking (Can Implement Independently):
- Dynamic orbital position simulation → separate Phase 6+ task  

---

## Success Criteria & Acceptance Tests

**Functional:**
1. ✅ Routing service selects correct specialized structure based on craft type + cargo manifest  
2. ✅ Load balancing distributes arrivals across multiple structures of same capability class  
3. ✅ Settlement.location_for() method works alongside existing location delegation (backward compatible)

**Non-Functional:**
4. ✅ Full RSpec coverage for routing logic and load balancing algorithms  
5. ✅ No breaking changes to single-structure settlements (bridge convention still supported during transition period)  

---

## Notes & Caveats

**Bridge Convention Still Supported:** Single-structure settlements continue working via existing `location` method delegation. Multi-structure routing is additive enhancement, not replacement of current pattern.

**Structure Identification Pattern:** Task assumes structures have identifiable types via blueprint identifier or operational_data flags. May need to extend OrbitalStructure model with explicit capability tags during implementation if identifiers prove insufficient for categorization.

---

## Related Tasks & References

### Phase 5+ Prerequisites (Must Complete First):
- `2026-06-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md` — ensures all structures have valid locations  
- `2026-06-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md` — defines gas processing structure types

### Phase 6+ Peer Tasks (Independent but Related):
- `2026-06-MEDIUM-FEATURE-DYNAMIC-ORBITAL-POSITION-SIMULATION.md` — orbital mechanics simulation  

---

## Task File Metadata (For Agent Workflow)

**Created By:** Qwen3.5-27B during April 2026 backlog triage session  
**Extracted From:** `docs/agent/archive/backlog_april_2026/deprecated/2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md` (identified as future enhancement during review)  
**Assigned To:** TBD — requires executor with experience in routing algorithms + load balancing patterns  
**Estimated Complexity:** MEDIUM — new service layer, extends existing models without breaking changes  
**Supervision Level:** 🟡 Standard — clear scope but needs architectural decisions on structure categorization pattern
