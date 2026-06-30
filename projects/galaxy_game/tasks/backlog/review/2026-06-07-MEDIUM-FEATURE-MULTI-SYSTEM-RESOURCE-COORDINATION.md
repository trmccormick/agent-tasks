# Multi-System Resource Coordination Algorithms for Cross-Settlement Optimization

```yaml
id: 2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION  
status: backlog
priority: MEDIUM
type: FEATURE
created: 2026-06-07
last_updated: 2026-06-09
system_domain: AI_MANAGER | LOGISTICS | ECONOMICS | WORMHOLES
mvp_alignment: PHASE_9_SOL_EXPANSION  
local_worker_safe: true
```

---

## Context & Strategic Purpose  

**Extracted from:** `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md` (Task 2.3 — "Post-MVP Expansion Features")

After Luna Phases prove single-system supply chain reliability and foothold establishment services enable multi-system colonization, the AI Manager needs cross-settlement resource optimization algorithms. This task implements inter-system trade automation, economic balancing across settlements, and wormhole logistics integration for efficient resource routing throughout expanding empire.

**Why Phase 5+?** Only becomes relevant after:
- ✅ Single-system supply chains proven reliable (Luna Phases complete)  
- ⏸️ Footholds established in multiple systems via `FootholdManager` service

**Updated for Phase 9+:** Per LUNA-MVP-SIMULATION-DESIGN.md revised phases, this is Act 4: Hammer Protocol & Network Mastery work — requires proven wormhole logistics and dual-link stabilization before cross-system optimization can be effective.
- 🔄 Wormhole topology integrated into expansion planning (`SystemDiscoveryService`)

---

## Problem Statement  

Current AI Manager logistics handles:
- Earth→Moon supply chain for Luna settlement ✅ (Phase 2/3)  
- Single-settlement shortage detection and import requests ✅ (ShortageDetector + ImportRequestGenerator)

**Missing:** Cross-system resource optimization when multiple settlements exist across wormhole network:
- Multi-system resource flow optimization (which settlement produces what, where to route excess capacity)
- Inter-settlement trade automation (automatic contracts between NPC settlements for surplus/deficit balancing)  
- System-wide economic balancing algorithms (prevent cascading shortages or overproduction in isolated systems)
- Wormhole logistics integration with retargeting costs and transit time optimization

---

## Current State Analysis  

### Existing Code (Read Before Starting)
| File | Purpose | Relevance to This Task |
|------|---------|------------------------|  
| `galaxy_game/app/services/logistics/shortage_detector.rb` | Single-settlement shortage detection | Foundation for multi-system extension — needs cross-settlement awareness |
| `galaxy_game/app/services/logistics/import_request_generator.rb` | Earth→Moon import manifests | Current single-source logic must evolve to multi-source selection across wormhole network |
| `galaxy_game/app/services/ai_manager/service_coordinator.rb` (if exists) | Service orchestration layer | May need extension for cross-system coordination decisions |

### Reference Documentation — Read First  
- `docs/architecture/wormhole_system.md` — Transit costs, retargeting mechanics, capacity constraints
- `docs/market/economic_baseline.md` — Market fundamentals and NPC economic behavior patterns  
- Original source: `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md#task-23-inter-system-resource-coordination`

---

## Implementation Scope  

### In Scope (Phase 5+)

**Sub-Task A: Multi-System Resource Flow Optimization**  
Implement algorithms for determining optimal production and routing across settlement network:
```ruby
class AIManager::ResourceCoordinator
  
  def self.optimize_resource_flow(settlements, resource_type)
    # Analyze current stock levels vs targets across all settlements
    # Identify surplus producers (have > need with excess capacity)
    # Match deficits to nearest viable source considering wormhole transit costs  
    # Generate routing plan minimizing total system-wide shortage duration
  end
  
  def self.calculate_optimal_production_targets(settlements, resource_type)
    # Factor in each settlement's ISRU capability and production rates
    # Consider strategic reserves for emergency scenarios (wormhole instability events)
    # Balance local consumption vs export capacity to other settlements  
  end
end
```

**Sub-Task B: Inter-Settlement Trade Automation**  
Create automatic contracts between NPC settlements for surplus/deficit balancing:
```ruby
# Generate Logistics::Contract records when cross-system trade identified as optimal
def self.create_inter_settlement_trade(surplus_settlement, deficit_settlement, resource_type, quantity)
  # Calculate transit cost via wormhole network (may be multi-hop route)  
  # Compare against local production costs at deficit settlement
  # Create contract only if trade reduces total system-wide economic impact
end
```

**Sub-Task C: System-Wide Economic Balancing Algorithms**  
Prevent cascading failures or overproduction across empire:
```ruby
def self.detect_cascading_risk(resource_type, all_settlements)
  # Identify single points of failure (one settlement producing critical resource for multiple others)
  # Flag settlements with no backup supply sources if primary fails
  # Recommend diversification strategies to reduce systemic risk  
end

def self.balance_production_across_system(settlements, resource_type)
  # Prevent overproduction in one system while another starves due to poor routing
  # Adjust production targets based on cross-system demand patterns
  # Factor in wormhole stability and transit time uncertainty into safety stock calculations
end
```

### Out of Scope (Future Tasks or Different Domains)  
- TerraGen consortium formation detection — shared resource pools require organization system integration (Phase 6+)
- Eden system special case handling for superior terraforming targets (separate optimization task)  
- Player mission offer generation based on cross-system shortages (Act 2+ player-facing feature, not NPC logistics)

---

## Technical Requirements  

### Multi-System Resource Coordination Architecture

**Step 1: Extend Shortage Detection to Cross-Settlement Awareness**  
Current `ShortageDetector.detect_shortages(settlement)` only evaluates single settlement. Add system-wide view:
```ruby
# New method alongside existing single-settlement detection
def self.detect_cross_system_shortages(resource_type, all_settlements)
  # Aggregate shortages across settlements considering wormhole connectivity
  # Identify which deficits can be resolved via inter-system trade vs local production  
end
```

**Step 2: Implement Multi-Source Selection Algorithm**  
Replace single-source logic (Cape Canaveral only in Luna Phase 3) with network-aware selection:
```ruby
def self.select_optimal_source(deficit_settlement, resource_type, candidate_sources)
  # Score each source based on:
  #   - Transit cost via wormhole network (multi-hop routing if needed)  
  #   - Source settlement's surplus capacity after meeting own needs
  #   - Wormhole stability risk factors along route
  # Return ranked list with recommended primary and backup sources
end
```

**Step 3: Create Economic Balancing Service Methods**  
System-wide optimization to prevent cascading failures or overproduction:
- Detect single points of failure in resource supply chains (one settlement critical for multiple others)
- Calculate optimal production distribution across settlements based on ISRU capabilities and strategic reserves needed
- Generate recommendations for diversification when systemic risk detected

---

## Files to Create/Modify  

### Primary Files (Create New / Edit Existing)  
| File | Purpose | Key Changes |
|------|---------|-------------|
| `galaxy_game/app/services/ai_manager/resource_coordinator.rb` (new) | Cross-settlement resource optimization logic | Multi-system flow algorithms, trade automation, economic balancing methods |
| `galaxy_game/spec/services/ai_manager/resource_coordinator_spec.rb` (new) | Test new behavior | Specs for cross-system shortage detection and optimal source selection |  
| `galaxy_game/app/services/logistics/import_request_generator.rb` (edit) | Extend single-source to multi-source logic | Integrate with ResourceCoordinator for network-aware sourcing decisions |

### Reference Files (Read Only)
| File | Why You Need It |
|------|-----------------|
| `docs/architecture/wormhole_system.md` | Transit costs, retargeting mechanics, capacity constraints affect routing optimization |  
| `galaxy_game/app/services/logistics/shortage_detector.rb` | Current single-settlement detection pattern to extend for cross-system awareness |

### Migration Needed?  
- [ ] **NO** — Uses existing settlement and logistics models only; no new database schema required  

---

## Acceptance Criteria  

- [ ] `ResourceCoordinator.optimize_resource_flow` returns optimal production/routing plan across multiple settlements
- [ ] Multi-source selection algorithm factors in wormhole transit costs, stability risks, and source capacity  
- [ ] Inter-settlement trade automation creates valid Logistics::Contract records when beneficial vs local production
- [ ] Cascading risk detection identifies single points of failure in resource supply chains
- [ ] All new behavior covered by focused RSpec specs (factories for multi-settlement scenarios)
- [ ] No regressions in existing Luna Phase 3 Earth→Moon import request functionality  

---

## Testing Requirements  

### Targeted Specs to Add  
1. `optimize_resource_flow` correctly identifies surplus producers and deficit consumers across settlement network  
2. Multi-source selection ranks sources by total cost including wormhole transit expenses
3. Inter-settlement trade creation only generates contracts when cheaper than local production at deficit settlement  
4. Cascading risk detection flags settlements with no backup supply sources for critical resources
5. Economic balancing prevents overproduction in one system while another has unmet demand

### RSpec Command Pattern  
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/resource_coordinator_spec.rb 2>&1 | tail -30'
```

---

## Stop Conditions — Escalize Immediately If:  

- Wormhole transit cost calculation logic not documented in architecture docs (may need investigation task first)  
- Logistics::Contract model missing required fields for multi-hop routing or cross-system trade tracking  
- Fix requires changing more files than specified above (especially core logistics layer beyond ImportRequestGenerator)
- Architectural decision needed about production target algorithms or economic balancing weights  

---

## Dependencies  

**Blocked by:** 
- `2026-06-XX-MEDIUM-FEATURE-WORMHOLE-TOPOLOGY-INTEGRATION.md` (needs wormhole connectivity data for routing optimization)  
- `2026-06-XX-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE.md` (requires multiple settlements established across systems before cross-system coordination relevant)

**Blocks:** TerraGen consortium formation detection, Eden system prioritization special cases  

**Related Tasks:**
- Original source: `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md#task-23-inter-system-resource-coordination`  
- Luna Phase 1-3 (single-system supply chain foundation — must complete before multi-system optimization relevant)

---

## Future Seams (Document in Code, Don't Implement)  

Add TODO comments for Phase 5+ enhancements:
```ruby
# TODO PHASE 6+: Integrate with TerraGen consortium formation detection — shared resource pools change optimal production distribution when corporations collaborate across systems  
# TODO POST-MVP: Ship availability/position tracking for real-time route optimization (current model assumes infinite wormhole capacity and static transit times)
# TODO FUTURE: Player mission offer generation based on cross-system shortages detected by this coordinator service (Act 2+ player-facing feature, separate task needed)
```

---

## Completion Report Template  

*Fill in after implementation:*

**Completed by:** [agent name]  
**Completion date:** YYYY-MM-DD  
**Final test result:** X examples, Y failures  

### What was changed  
- `[file]` — [description of change including any modifications to existing logistics layer services]

### Issues discovered
[Any problems found during implementation that weren't in original task or assumptions about wormhole transit cost calculations were incorrect]

### Follow-up tasks needed  
[Any new backlog items identified — do not create files, just list here such as TerraGen consortium integration requirements discovered during testing]

### Lessons learned  
[What worked well for cross-system optimization algorithms, what didn't (e.g., complexity of multi-hop routing), and recommendations for future economic balancing or logistics coordination tasks]  

---

## Notes for Future Reviewers  

This task extracted from April 2026 backlog item `ai_manager_autonomous_expansion.md` which was over-scoped (mixed Luna-relevant concepts with Phase 5+ work). The original document correctly identified this as "Post-MVP Expansion Features" — only becomes relevant after single-system supply chains proven reliable and multiple settlements established across wormhole network.

**Extraction sequence from original backlog item:**
1. ✅ Wormhole Topology Integration (Task A created)  
2. ✅ Foothold Establishment Service Design (Task B created)
3. ✅ Multi-System Resource Coordination Algorithms (this task — Task C, completes extraction of actionable Phase 5+ work)

**Remaining in original April 2026 backlog item:** TerraGen consortium formation detection and Eden system prioritization special cases may warrant separate tasks if those features remain relevant to future roadmap after Luna phases complete. Consider reviewing again when Phase 3-4 priorities shift toward multi-system expansion planning.

---
