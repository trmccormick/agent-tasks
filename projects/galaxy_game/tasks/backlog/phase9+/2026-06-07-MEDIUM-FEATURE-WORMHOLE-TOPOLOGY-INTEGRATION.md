# Wormhole Topology Integration for System Expansion Planning

```yaml
id: 2026-06-07-MEDIUM-FEATURE-WORMHOLE-TOPOLOGY-INTEGRATION
status: backlog
priority: MEDIUM
type: FEATURE
created: 2026-06-07
last_updated: 2026-06-09
system_domain: AI_MANAGER | WORMHOLES | LOGISTICS
mvp_alignment: PHASE_9_SOL_EXPANSION
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md` (Sub-Task 1.4.1)

The AI Manager's system discovery service exists but operates in isolation — it doesn't consider wormhole network topology when evaluating expansion opportunities. This task wires existing `system_discovery_service.rb` with real-time wormhole connection analysis to enable strategic multi-hop expansion planning.

**Why Phase 9+?** Per LUNA-MVP-SIMULATION-DESIGN.md revised phases:
- Luna simulation (Phase 4-5) must prove single-system supply chain reliability first
- Wormhole network infrastructure is Act 3/4 work — requires proven wormhole logistics and dual-link stabilization before multi-hop expansion planning

---

## Problem Statement

Current `SystemDiscoveryService` evaluates star systems based on:
- Resource potential
- Habitability indicators  
- Strategic position (generic)

**Missing:** No consideration of wormhole network connectivity, which is critical for expansion viability:
- Multi-hop route planning through existing wormholes
- Wormhole stability and capacity constraints
- Network centrality calculations for strategic positioning
- Expansion corridor optimization considering transit costs

---

## Current State Analysis

### Existing Code (Read Before Starting)
| File | Purpose | Key Methods/Sections |
|------|---------|---------------------|
| `galaxy_game/app/services/ai_manager/system_discovery_service.rb` | System evaluation logic | Needs wormhole topology integration |
| `galaxy_game/app/models/wormhole.rb` (if exists) or check architecture docs | Wormhole data model | Connection endpoints, stability metrics |

### Reference Documentation — Read First
- `docs/architecture/wormhole_system.md` — Wormhole mechanics and rules
- `docs/ai_manager/PRECURSOR_INFRASTRUCTURE_CAPABILITIES.md` — System bootstrapping patterns
- Original source: `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md#sub-task-141-wormhole-topology-integration`

---

## Implementation Scope

### In Scope (Phase 5)
- Query active wormhole connections and ranges from current settlement's star system
- Implement pathfinding algorithms for multi-hop expansion routes through wormhole network
- Add wormhole stability and capacity considerations to system scoring
- Create network centrality calculations identifying strategic chokepoints
- Integrate topology data into `SystemDiscoveryService#evaluate_system` output

### Out of Scope (Future Tasks)
- Foothold establishment logic (separate task: "Foothold Establishment Service Design")
- Multi-system resource coordination algorithms (Phase 5+ separate task)
- TerraGen consortium formation detection and shared resource pools
- Eden system prioritization special cases
- Player mission offer generation for expansion opportunities

---

## Technical Requirements

### Wormhole Topology Integration Steps

**Step 1: Query Active Wormhole Connections**
```ruby
# Add to SystemDiscoveryService or create helper module
def wormhole_connections_from(settlement_star_system)
  # Return array of connected systems with stability metrics, capacity limits
end
```

**Step 2: Multi-Hop Pathfinding Algorithm**  
Implement BFS/DFS or Dijkstra's algorithm for finding optimal expansion routes through wormhole network considering:
- Transit time per hop
- Stability risk factors
- Capacity constraints (cargo volume limits)

**Step 3: Network Centrality Calculation**
```ruby
# Identify strategic positioning value of each system in network
def calculate_network_centrality(system, all_connections)
  # Return centrality score based on connectivity degree and betweenness
end
```

**Step 4: Integrate into System Scoring**
Update `SystemDiscoveryService` output to include topology metrics:
```ruby
{
  system_id: ...,
  resource_score: ...,
  habitability_score: ...,
  wormhole_connectivity: {
    direct_connections: [...],
    multi_hop_routes: {...},
    centrality_score: ...,
    strategic_value: ...
  }
}
```

---

## Files to Create/Modify

### Primary Files (Edit)
| File | Purpose | Key Changes |
|------|---------|-------------|
| `galaxy_game/app/services/ai_manager/system_discovery_service.rb` | Add wormhole topology methods | Integrate connection queries, pathfinding, centrality calculations |
| `galaxy_game/spec/services/ai_manager/system_discovery_service_spec.rb` | Test new behavior | Specs for multi-hop routing and network analysis |

### Reference Files (Read Only)
| File | Why You Need It |
|------|-----------------|
| `docs/architecture/wormhole_system.md` | Wormhole mechanics, stability rules, capacity constraints |
| `galaxy_game/app/models/star_system.rb` (if exists) | Star system data structure for connection endpoints |

### Migration Needed?
- [ ] No migration needed — uses existing wormhole and star system models only

---

## Acceptance Criteria

- [ ] System discovery output includes wormhole connectivity metrics
- [ ] Multi-hop pathfinding returns optimal routes through network (not just direct connections)
- [ ] Wormhole stability factors into expansion viability scoring
- [ ] Network centrality calculation identifies strategic chokepoints
- [ ] All new behavior covered by focused RSpec specs
- [ ] No regressions in existing system discovery functionality

---

## Testing Requirements

### Targeted Specs to Add
1. `wormhole_connections_from` returns correct connected systems for given star system
2. Multi-hop pathfinding finds optimal route when direct connection unavailable
3. Pathfinding respects wormhole stability constraints (avoids unstable routes)
4. Network centrality calculation correctly identifies high-connectivity nodes
5. System scoring integrates topology data without breaking existing resource/habitability scores

### RSpec Command Pattern
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/system_discovery_service_spec.rb 2>&1 | tail -30'
```

---

## Stop Conditions — Escalate Immediately If:

- Wormhole model structure differs significantly from architecture docs (may need investigation task first)
- Star system connection data not available in current schema (requires database migration)
- Fix requires changing more files than specified above
- Architectural decision needed about pathfinding algorithm choice or scoring weights

---

## Dependencies

**Blocked by:** None — Luna Phases 1-3 can complete independently  
**Blocks:** Foothold establishment service design, multi-system resource coordination tasks  
**Related Tasks:** 
- `2026-06-XX-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE.md` (to be created)
- Original source: `docs/agent/archive/backlog_april_2026/ai_manager_autonomous_expansion.md`

---

## Future Seams (Document in Code, Don't Implement)

Add TODO comments for Phase 5+ enhancements:
```ruby
# TODO PHASE 5+: Integrate with TerraGen consortium formation detection — shared resource pools affect expansion priorities
# TODO PHASE 6+: Add Eden system special case handling — superior terraforming targets get priority routing
# TODO POST-MVP: Ship availability/position tracking for real-time route optimization (current model assumes infinite capacity)
```

---

## Completion Report Template

*Fill in after implementation:*

**Completed by:** [agent name]  
**Completion date:** YYYY-MM-DD  
**Final test result:** X examples, Y failures  

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create files, just list here]

### Lessons learned
[What worked, what didn't, what future wormhole-related tasks should know]

---

## Notes for Future Reviewers

This task extracted from April 2026 backlog item `ai_manager_autonomous_expansion.md` which was over-scoped (mixed Luna-relevant concepts with Phase 5+ work). The "foothold resource allocation" pattern from that original task is already being implemented in active Luna Phase 3 (`ShortageDetector + ImportRequestGenerator`). This wormhole topology integration is the first of several focused tasks extracted for future autonomous expansion phases.

**Related extraction:** Consider creating separate tasks for:
- Foothold Establishment Service Design (greenfield work, no existing code)
- Multi-System Resource Coordination Algorithms (cross-settlement optimization)

---
