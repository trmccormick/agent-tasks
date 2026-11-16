---
status: not-started
priority: HIGH
type: feature
created: 2026-06-22
last-updated: 2026-06-22
phase: 6+
---

# Worldhouse Failure Analysis Service

## Context
Worldhouse construction is a multi-segment enclosed valley terraforming system (`Structures::Worldhouse` with `Structures::WorldhouseSegment`). As segments are enclosed, systems degrade over time due to environmental stress, material fatigue, and seal failures. This task implements failure detection and analysis logic.

**Reference Models**:
- [worldhouse.rb](galaxy_game/app/models/structures/worldhouse.rb) — main worldhouse model with geological feature linking
- [worldhouse_segment.rb](galaxy_game/app/models/structures/worldhouse_segment.rb) — segment with coverable functionality and material tracking

## Problem Statement
Worldhouse segments need to track failure cascade, TTR (time-to-repair), and resource recovery logic. Currently, segments have a simple `status` enum but no failure simulation or analysis.

## Scope
- Implement failure modes (seal degradation, structural stress, environmental damage)
- Calculate TTR (time-to-repair) based on failure type and available resources
- Model salvage/resource recovery (partial material recovery from failed segments)
- Cascade failures (segment failure can trigger maintenance events in adjacent segments)
- Integrate with maintenance and state schema
- Full RSpec coverage

## Target Files
- `app/services/worldhouse_failure_analyzer.rb` (NEW)
- `app/models/structures/worldhouse.rb` (modify to support failure tracking)
- `app/models/structures/worldhouse_segment.rb` (add failure fields: `failure_type`, `ttr_days`, `failure_at`)
- `spec/services/worldhouse_failure_analyzer_spec.rb` (NEW)
- `data/schemas/worldhouse_state.schema.json` (extend to include failure cascade rules)

## Acceptance Criteria
- [ ] Failure modes defined (seal_degradation, structural_stress, environmental_damage)
- [ ] TTR calculation implemented and tested
- [ ] Salvage recovery logic (% recovery by material type)
- [ ] Cascade logic implemented (adjacent segment maintenance triggers)
- [ ] State schema updated with failure tracking fields
- [ ] RSpec: >90% coverage for all failure scenarios

## Implementation Steps
1. Define failure modes as enum on `WorldhouseSegment`
2. Implement `WorldhouseFailureAnalyzer` service with:
   - `analyze_failures(worldhouse)` → detects failed segments
   - `calculate_ttr(segment, failure_type)` → returns days to repair
   - `calculate_salvage_recovery(segment)` → returns {material => qty} hash
3. Add failure tracking fields to model and schema
4. Write comprehensive RSpec covering all failure paths
5. Integrate with maintenance system (triggers maintenance events)

## Dependencies
- Must complete BEFORE: `2026-06-22-HIGH-FEATURE-WORLDHOUSE-MAINTENANCE-SERVICE.md` (maintenance event system depends on failure analysis output)
- Requires: `Structures::Worldhouse` model with geological feature and segments already exist
- Requires: Material/item lookup system for salvage calculations

## Notes
- Keep failure analysis as pure service logic (no side effects on segment state during analysis)
- Separate analysis from action (repair/salvage are handled by maintenance system)
- Failure cascade should be configurable (adjacent segment sensitivity)
