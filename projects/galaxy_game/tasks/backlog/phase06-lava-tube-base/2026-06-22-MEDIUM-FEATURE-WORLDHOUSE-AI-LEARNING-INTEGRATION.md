---
status: not-started
priority: MEDIUM
type: feature
created: 2026-06-22
last-updated: 2026-06-22
phase: 6+
---

# Worldhouse AI Learning Integration

## Context
AI Manager learns construction patterns, failure risks, and maintenance optimization from worldhouse deployment and operation data. This task extracts patterns from construction, maintenance, and failure history, then uses these patterns to improve future worldhouse planning and resource allocation.

**Reference Models**:
- [worldhouse.rb](galaxy_game/app/models/structures/worldhouse.rb)
- [worldhouse_segment.rb](galaxy_game/app/models/structures/worldhouse_segment.rb)
- [app/services/ai_manager/](galaxy_game/app/services/ai_manager/) — AI manager services

## Problem Statement
Worldhouse construction/maintenance/failure data is generated but not fed back into AI decision-making. The AI Manager should learn:
- Which segment types fail first (pattern recognition)
- Optimal construction order for risk reduction
- Resource allocation effectiveness
- Environmental factors affecting failure rates

## Scope
- Pattern extraction from construction history (segment sequence, timing, resources used)
- Risk assessment modeling (which environmental conditions correlate with failures)
- Scaling logic (how to size future worldhouses based on past performance)
- Integration with AI Manager planning services
- RSpec coverage for pattern extraction

## Target Files
- `app/services/ai_manager/worldhouse_learning.rb` (NEW)
- `app/services/ai_manager/pattern_loader.rb` (may extend existing pattern system)
- `spec/services/ai_manager/worldhouse_learning_spec.rb` (NEW)
- `data/schemas/worldhouse_state.schema.json` (extend with learning metrics)

## Acceptance Criteria
- [ ] Pattern recognition from construction sequences
- [ ] Risk assessment model (failure probability by segment position, environmental factors)
- [ ] Scaling recommendations (projected segment count for target population)
- [ ] Integration with AI Manager expansion planning
- [ ] Learned patterns persist and improve recommendations over time
- [ ] RSpec: >80% coverage for all pattern extraction paths

## Implementation Steps
1. Define pattern extraction queries:
   - Segment sequence analysis (which segment types succeeded/failed in sequence)
   - Failure correlation analysis (environmental conditions → failure rate)
   - Resource effectiveness (resources spent → time-to-completion)
2. Implement `WorldhouseLearning` service:
   - `extract_patterns(worldhouses)` → returns pattern hash
   - `assess_risk(segment_plan, environmental_conditions)` → probability
   - `recommend_scaling(target_population, geological_features)` → segment count + resources
3. Integrate with `AIManager::ExpansionService` (use learned scaling for new settlements)
4. Create RSpec for pattern extraction accuracy

## Dependencies
- Requires AFTER: `2026-06-22-HIGH-FEATURE-WORLDHOUSE-FAILURE-ANALYSIS-SERVICE.md`
- Requires AFTER: `2026-06-22-HIGH-FEATURE-WORLDHOUSE-MAINTENANCE-SERVICE.md`
- Requires: AI Manager pattern system already in place
- Requires: Environmental data model for risk correlation

## Notes
- Patterns should be versioned (old vs new patterns as conditions change)
- Risk assessment should be probabilistic, not deterministic
- Learning should improve incrementally (each new worldhouse refines models)
- Consider geographic/environmental locality (Mars vs Venus patterns differ)
- Scaling recommendations should account for settlement type (mining vs colony vs industrial)
