# Foothold Establishment Service Design for Multi-System Colonization

```yaml
id: 2026-06-07-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE
status: backlog
priority: MEDIUM
type: FEATURE
created: 2026-06-07
last_updated: 2026-06-07
system_domain: AI_MANAGER | SETTLEMENTS | LOGISTICS
mvp_alignment: PHASE_5_AUTONOMOUS_EXPANSION
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md` (Sub-Task 1.2)

After Luna Phases complete and AI Manager proves single-system supply chain reliability, the next expansion phase requires establishing footholds in new star systems via wormhole network. This task creates the foundational service architecture for automated colony site selection, resource allocation planning, and bootstrap package generation for multi-system colonization.

**Why Separate Task?** Unlike Wormhole Topology Integration (which builds on existing `system_discovery_service.rb`), this is greenfield work — no foothold_manager or expansion_plan models exist yet. Requires clean architectural design before implementation begins.

---

## Problem Statement

Current AI Manager can:
- Detect shortages in Luna settlement ✅ (Phase 3)
- Create import manifests from Earth ✅ (Phase 2/3)  
- Evaluate system resource potential ⚠️ (`SystemDiscoveryService` exists but limited scope)

**Missing:** No logic for establishing new settlements in discovered systems:
- Automated colony site selection based on habitability and resources
- Initial bootstrap resource package calculation per settlement type
- Foothold expansion triggers and milestone tracking  
- Integration with wormhole logistics for multi-system supply chains

---

## Current State Analysis

### Existing Code (Read Before Starting)
| File | Purpose | Relevance to This Task |
|------|---------|------------------------|
| `galaxy_game/app/services/ai_manager/system_discovery_service.rb` | System evaluation logic | Provides candidate systems for foothold establishment |
| `galaxy_game/app/models/settlement/base_settlement.rb` (if exists) or check architecture docs | Settlement data model | New footholds will extend this pattern |

### Reference Documentation — Read First  
- `docs/architecture/wormhole_system.md` — Multi-system travel mechanics and constraints
- `docs/ai_manager/PRECURSOR_INFRASTRUCTURE_CAPABILITIES.md` — System bootstrapping patterns for initial settlements
- Original source: `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md#task-12-develop-autonomous-foothold-establishment`

---

## Implementation Scope

### In Scope (Phase 5+)
**Sub-Task A: Planetary Site Selection Algorithm**
```ruby
# Analyze celestial bodies for habitability and resource potential
def evaluate_colony_site(celestial_body, strategic_requirements)
  # Return score based on atmosphere, hydrosphere, biosphere quality
  # Factor in resource availability (mining targets, ISRU materials)
  # Assess defensive positioning value if applicable
end
```

**Sub-Task B: Foothold Resource Allocation Engine**  
Calculate initial bootstrap requirements for new settlements:
```ruby
# Create initial resource package based on site characteristics and settlement type
def calculate_bootstrap_requirements(settlement_type, celestial_body_data)
  # Energy generation needs (solar vs nuclear based on star system)
  # Life support baseline (atmosphere quality determines import dependency)  
  # Construction materials for initial infrastructure
end
```

**Sub-Task C: Expansion Plan Model Design**
Create `ExpansionPlan` model to track multi-phase colonization efforts:
- Phase tracking: scouting → foothold → expansion → maturity
- Resource allocation per phase with milestone triggers
- Integration with logistics layer for supply chain establishment

### Out of Scope (Future Tasks)
- Wormhole topology integration — separate task (`2026-06-XX-MEDIUM-FEATURE-WORMHOLE-TOPOLOGY-INTEGRATION.md`)  
- Multi-system resource coordination algorithms — Phase 5+ optimization after footholds established
- TerraGen consortium formation detection and shared resource pools (Phase 6+)
- Eden system special case handling for superior terraforming targets

---

## Technical Requirements

### Foothold Establishment Service Architecture

**Step 1: Create `FootholdManager` Service Class**  
Location: `galaxy_game/app/services/ai_manager/foothold_manager.rb`

Core responsibilities:
```ruby
class AIManager::FootholdManager
  def self.evaluate_sites(system_id, strategic_requirements)
    # Query celestial bodies in target system
    # Score each based on habitability + resources  
    # Return ranked list with expansion recommendations
  end
  
  def self.create_bootstrap_plan(selected_site, settlement_type)
    # Calculate initial resource requirements
    # Generate supply chain plan from parent settlements via wormholes
    # Create ExpansionPlan record tracking phases and milestones
  end
  
  def self.track_expansion_progress(expansion_plan_id)
    # Monitor milestone completion (infrastructure built, population thresholds met)
    # Trigger next phase when conditions satisfied
    # Adjust resource allocation based on actual vs planned progress
  end
end
```

**Step 2: Design `ExpansionPlan` Model Schema**  
Required fields to track multi-phase colonization:
- Target system ID and selected celestial body
- Settlement type (mining outpost, terraforming base, strategic foothold)
- Phase tracking with milestone definitions
- Resource allocation per phase with actual vs planned comparison
- Parent settlement reference for supply chain integration

**Step 3: Integrate Site Selection Algorithm**  
Multi-factor scoring system evaluating:
```ruby
# Habitability factors (atmosphere quality, water availability, temperature range)
habitability_score = calculate_habitability(celestial_body)

# Resource extraction potential (mining targets, ISRU materials abundance)  
resource_potential = evaluate_resource_deposits(celestial_body)

# Strategic positioning value (defensive advantages, expansion corridors accessible via wormholes)
strategic_value = assess_strategic_position(system_id, celestial_body_location)

final_score = weighted_average(habitability_score * 0.4, resource_potential * 0.35, strategic_value * 0.25)
```

**Step 4: Bootstrap Resource Package Calculation**  
Generate initial supply requirements based on settlement type and site conditions:
- Energy generation infrastructure (solar arrays vs nuclear reactors based on star proximity)
- Life support systems scaled to atmosphere quality (import dependency if poor air/water)
- Construction materials for base facilities, habitats, industrial equipment
- Emergency reserves accounting for wormhole transit time from parent settlements

---

## Files to Create/Modify

### Primary Files (Create New)
| File | Purpose | Key Components |
|------|---------|----------------|
| `galaxy_game/app/services/ai_manager/foothold_manager.rb` | Foothold establishment logic | Site evaluation, bootstrap planning, progress tracking methods |
| `galaxy_game/app/models/expansion_plan.rb` + migration | Track multi-phase colonization efforts | Phase definitions, milestone tracking, resource allocation schema |

### Reference Files (Read Only)  
| File | Why You Need It |
|------|-----------------|
| `docs/architecture/wormhole_system.md` | Multi-system travel mechanics for supply chain planning |
| `galaxy_game/app/models/settlement/base_settlement.rb` (if exists) | Settlement patterns to extend for foothold types |

### Migration Needed?  
- [ ] **YES** — New `expansion_plans` table required with schema:
  ```ruby
  create_table :expansion_plans do |t|
    t.references :target_system, foreign_key: true # StarSystem or similar model
    t.string :celestial_body_name # Target planet/moon for foothold
    t.enum :settlement_type, [:mining_outpost, :terraforming_base, :strategic_foothold]
    t.integer :current_phase, default: 0 # scouting=0, foothold=1, expansion=2, maturity=3  
    t.jsonb :milestones # Phase definitions with completion criteria
    t.decimal :allocated_resources # Total resources committed per phase
    t.references :parent_settlement, foreign_key: true # Supply chain source
    t.timestamps
  end
  ```

---

## Acceptance Criteria

- [ ] `FootholdManager.evaluate_sites` returns ranked celestial bodies with habitability/resource scores  
- [ ] Bootstrap resource calculation generates realistic initial supply packages per settlement type
- [ ] `ExpansionPlan` model tracks multi-phase colonization progress with milestone triggers
- [ ] Site selection algorithm factors in atmosphere, hydrosphere, biosphere quality plus strategic positioning
- [ ] All new behavior covered by focused RSpec specs (factories for ExpansionPlan + FootholdManager tests)
- [ ] No regressions in existing settlement creation or AI Manager functionality

---

## Testing Requirements

### Targeted Specs to Add  
1. `evaluate_sites` returns ranked list when multiple celestial bodies available in system
2. Site scoring correctly weights habitability (40%), resources (35%), strategic value (25%) per requirements
3. Bootstrap calculation generates appropriate resource packages for different settlement types
4. ExpansionPlan milestone tracking triggers phase transitions when conditions met  
5. Integration with parent settlement supply chain creates valid logistics contracts

### RSpec Command Pattern
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/foothold_manager_spec.rb 2>&1 | tail -30'
```

---

## Stop Conditions — Escalate Immediately If:

- Settlement model architecture differs significantly from expected patterns (may need investigation task first)  
- Star system or celestial body data models missing required fields for habitability/resource evaluation
- Migration conflicts with existing schema constraints on settlements or star systems tables
- Architectural decision needed about settlement type enum values or phase definitions

---

## Dependencies  

**Blocked by:** None — can design service architecture independently of Luna phases  
**Blocks:** Multi-system resource coordination task (needs footholds established first)  
**Related Tasks:**
- `2026-06-XX-MEDIUM-FEATURE-WORMHOLE-TOPOLOGY-INTEGRATION.md` (provides system connectivity data for site selection)
- Original source: `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md#task-12-develop-autonomous-foothold-establishment`

---

## Future Seams (Document in Code, Don't Implement)  

Add TODO comments for Phase 5+ enhancements:
```ruby  
# TODO PHASE 5+: Integrate with TerraGen consortium formation — shared resource pools affect bootstrap requirements when multiple corporations collaborate on footholds
# TODO PHASE 6+: Add Eden system special case handling — superior terraforming targets get priority in site selection scoring  
# TODO POST-MVP: Ship availability/position tracking for real-time supply chain optimization (current model assumes infinite wormhole capacity)
```

---

## Completion Report Template  

*Fill in after implementation:*

**Completed by:** [agent name]  
**Completion date:** YYYY-MM-DD  
**Final test result:** X examples, Y failures  

### What was changed  
- `[file]` — [description of change including migration details if applicable]

### Issues discovered
[Any problems found during implementation that weren't in original task]

### Follow-up tasks needed  
[Any new backlog items identified — do not create files, just list here]

### Lessons learned  
[What worked, what didn't, what future foothold-related tasks should know about settlement patterns or resource allocation logic]

---

## Notes for Future Reviewers  

This task extracted from April 2026 backlog item `ai_manager_autonomous_expansion.md` which was over-scoped (mixed Luna-relevant concepts with Phase 5+ work). The "foothold resource allocation engine" pattern mentioned in original document is partially implemented in active Luna Phase 3 (`ShortageDetector + ImportRequestGenerator`) for single-system Earth→Moon logistics. This foothold establishment service extends that concept to multi-system colonization via wormhole network — greenfield architecture requiring clean design before implementation begins.

**Related extractions:**
- ✅ Wormhole Topology Integration (separate task created)  
- ⏸️ Multi-System Resource Coordination Algorithms (to be extracted as third independent task if backlog review continues)

---
