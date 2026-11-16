# RESEARCH: Pattern-Based Settlement Decision Logic Architecture

**Date**: 2026-08-10
**Researcher**: Research Agent (Qwen3.6-35b)
**Task**: `2026-07-07-MEDIUM-RESEARCH-PATTERN-BASED-SETTLEMENT-DECISION-LOGIC.md`
**Status**: COMPLETED

---

## Executive Summary

**CRITICAL FINDING**: The codebase already has a substantial pattern learning infrastructure that was NOT anticipated in the original task file. Pattern capture, storage, scoring, validation, and application mechanisms all exist — they are just not wired into the primary AI Manager decision loop (`StrategySelector`).

The research task is **partially superseded** by existing architecture. The remaining work is integration (wiring existing pattern infrastructure into `StrategySelector`) rather than architectural design.

---

## 1. Current Architecture Alignment

### 1.1 StrategySelector Scoring Mechanism ✅ AUDITED

**File**: `galaxy_game/app/services/ai_manager/strategy_selector.rb`

**How it scores settlement options today**:
- Uses `MissionScorer.prioritize_missions()` with fixed `SCORING_WEIGHTS`:
  - Priority multipliers: critical=3.0, high=2.0, medium=1.5, low=1.0
  - Strategic value: 1.2x, risk penalty: 0.8x, urgency bonus: 1.5x, capability bonus: 1.1x
- `StateAnalyzer.analyze_state()` provides input data:
  - Unfilled buy orders (Market::Order)
  - Inventory + surface storage
  - Power available (sum of operational base units)
  - Cost analysis via `Settlements::CostAnalyzer.compare_costs()` per resource
  - Cost pressure score (ratio of import recommendations)
- **NO pattern-based scoring exists** — all decisions are stateless, computed fresh each tick
- Expansion readiness boost: +30% when `expansion_readiness >= 0.8`

**Gap**: StrategySelector has zero awareness of learned patterns. It scores purely on current state (resources, power, cost pressure).

### 1.2 StateAnalyzer State Data ✅ AUDITED

**File**: `galaxy_game/app/services/ai_manager/state_analyzer.rb`

**Available for pattern matching**:
- `unfilled_buy_orders` — open buy orders by settlement
- `inventory` — current inventory state
- `surface_storage` — surface storage state
- `power_available` — computed from operational base units
- `cost_analysis.viable` — can costs be optimized?
- `cost_analysis.cost_pressure` — 0.0-1.0 urgency score
- `cost_analysis.recommendations` — resources where local production is cheaper

**Gap**: No historical data, no deployment success/failure tracking, no pattern features available for matching.

### 1.3 tasks_v2 Pattern Metadata Support ✅ AUDITED

**Location**: `data/json-data/missions_v2/` (profiles/, phases/, mission_plans/, etc.)

**Findings**:
- `tasks_v2` is a **task library**, not a pattern system
- Each JSON file describes a single task (e.g., `task_deploy_pve_unit.json`)
- No pattern metadata fields exist in task definitions
- Task selection is driven by `TaskExecutionEngineV2` capability-based planning, not pattern matching

**Verdict**: tasks_v2 does NOT support pattern-based task selection. It's purely a data-driven task execution library.

### 1.4 Worldhouse Learning Generalization ✅ AUDITED

**File**: `galaxy_game/app/services/ai_manager/wormhole_coordinator.rb` (lines 570-630)

**Findings**:
- `WormholeCoordinator#update_coordinator_with_learned_patterns()` applies learned patterns to wormhole-specific algorithms
- Pattern types: `adaptive_scouting`, `dual_system_valuation`, `counterbalance_assessment`, `aws_cost_benefit_analysis`, `simultaneous_operations`
- **NARROW SCOPE**: Only relevant for multi-wormhole events (Phase 4+ story milestone)
- Does NOT generalize to settlement pattern learning

**Verdict**: Worldhouse Learning is wormhole-specific. It does NOT cover the general case of settlement pattern learning.

---

## 2. Data Source for Pattern Capture

### 2.1 Existing Pattern Infrastructure Discovered

The codebase has **five distinct pattern mechanisms**:

| Mechanism | Location | Purpose | Status |
|---|---|---|---|
| `MissionProfileAnalyzer` | `app/services/ai_manager/mission_profile_analyzer.rb` | Extracts patterns from mission profiles (phases, equipment, economics) | ✅ Functional, saves to `AI_MISSION_PATTERNS_PATH` |
| `OperationalManager#load_trained_patterns` | `app/services/ai_manager/operational_manager.rb:676` | Loads settlement patterns from `AI_SETTLEMENT_PATTERNS_PATH/*.json` | ✅ Functional, directory empty |
| `PatternValidator` | `app/services/ai_manager/pattern_validator.rb` | Validates patterns against world knowledge, assesses compatibility | ✅ Functional |
| `learned_patterns.json` | `data/json-data/ai_manager/learned_patterns.json` | AI decision patterns (used by GCC bootstrap) | ⚠️ Path defined but file doesn't exist yet |
| `WormholeCoordinator#update_coordinator_with_learned_patterns` | `app/services/ai_manager/wormhole_coordinator.rb:617` | Applies learned patterns to wormhole algorithms | ✅ Functional (wormhole-specific) |

### 2.2 Pattern Data Structures

**MissionProfileAnalyzer pattern format**:
```ruby
{
  pattern_id: 'lunar_pattern',              # Extracted from mission_id
  deployment_sequence: [...],                # Unit deployment order
  resource_dependencies: [...],              # Inter-unit dependencies
  equipment_requirements: {                  # Equipment specs
    total_unit_count: N,
    units: [...]
  },
  phase_structure: {                         # Phase breakdown
    total_phases: N,
    phases: [...]
  },
  economic_model: {                          # Economics
    estimated_gcc_cost: N,
    local_production_ratio: 0.0-1.0
  },
  critical_path: [...],                      # Critical path analysis
  learned_from: 'mission_json_analysis',
  learned_at: '2026-XX-XX...',
  source_file: 'lunar_mission_profile_v1.json'
}
```

**PatternValidator compatibility assessment**:
```ruby
{
  score: 0.0-1.0,                          # Overall compatibility
  reasons: ['water_extraction_matches_world', ...],
  world_resources: { water_available: true, ... },
  production_capabilities: { ... }
}
```

### 2.3 Concrete Data Sources for Pattern Capture

| Source | What It Provides | Usability |
|---|---|---|
| `MissionProfileAnalyzer.analyze_all_mission_profiles` | Patterns from mission JSON profiles | ✅ Ready — scans `MISSIONS_PATH/**/*.json` |
| `OperationalManager.pattern_score` | ISRU ratio + equipment completeness + world compatibility scoring | ✅ Ready — but patterns dir is empty |
| `CelestialBody#geosphere.terrain_map` | Terrain data for world compatibility | ✅ Available via `PatternValidator` |
| `PerformanceTracker` (in OperationalManager) | Decision outcome recording | ⚠️ Exists but not wired to pattern capture |

---

## 3. Pattern Storage Architecture

### 3.1 Options Evaluated

**Option A: Extend tasks_v2 library with pattern metadata fields**
- ❌ **REJECTED**: tasks_v2 is a task execution library, not a pattern system. Adding pattern metadata would conflate concerns.

**Option B: Create a lightweight SettlementPattern model**
- ❌ **REJECTED**: Confirmed obsolete in 2026-06-25 handoff. Pattern learning should live within existing services.

**Option C: Store patterns as JSON in `data/json-data/missions/patterns/`**
- ⚠️ **PARTIALLY VALID**: The path exists (`AI_SETTLEMENT_PATTERNS_PATH = AI_MANAGER_PATH.join('settlement-patterns')`) but the directory is empty. This IS the intended location — it just needs population.

**Option D: Use existing AI Manager state persistence mechanism**
- ✅ **RECOMMENDED**: The codebase already has `MissionProfileAnalyzer` (pattern extraction) + `OperationalManager` (pattern loading/scoring) + `PatternValidator` (validation). These form a complete pattern system.

### 3.2 Recommended Architecture

**Use the existing pattern infrastructure as-is.** The architecture is:

```
MISSIONS_PATH/**/*.json (mission profiles)
    ↓ MissionProfileAnalyzer.analyze_all_mission_profiles()
AI_MISSION_PATTERNS_PATH (mission_profile_patterns.json) ← PATTERN STORAGE
    ↓ load_trained_patterns()
OperationalManager.find_expansion_pattern() ← PATTERN APPLICATION
    ↓ pattern_score() + PatternValidator.assess_world_compatibility()
Settlement expansion decisions
```

**Justification**:
1. Pattern extraction already exists (`MissionProfileAnalyzer`)
2. Pattern storage already exists (`AI_MISSION_PATTERNS_PATH` / `AI_SETTLEMENT_PATTERNS_PATH`)
3. Pattern scoring already exists (`OperationalManager.pattern_score`)
4. Pattern validation already exists (`PatternValidator`)
5. All use JSON files (no database model needed)
6. Follows existing codebase conventions (JSON-based game data, not ORM models)

---

## 4. Pattern Application Mechanism

### 4.1 Current State

**Pattern capture**: `MissionProfileAnalyzer.analyze_all_mission_profiles()` extracts patterns from mission JSON profiles and saves to `AI_MISSION_PATTERNS_PATH`.

**Pattern scoring**: `OperationalManager.pattern_score()` scores patterns by:
- ISRU ratio × 50 (local production importance)
- Equipment count min(50, count) (completeness)
- World compatibility score × 30 (PatternValidator)

**Pattern application**: `OperationalManager.find_expansion_pattern()` returns highest-scoring suitable pattern for expansion decisions.

### 4.2 The Gap: StrategySelector Is Not Wired In

`StrategySelector.evaluate_next_action()` does NOT call `OperationalManager` or consult learned patterns. It scores purely on current state (resources, power, cost pressure).

**The gap is integration, not architecture.**

### 4.3 Proposed Data Contract for Pattern Flow

```
CAPTURE:
  MissionProfileAnalyzer.analyze_all_mission_profiles()
    → AI_MISSION_PATTERNS_PATH (mission_profile_patterns.json)
    → AI_SETTLEMENT_PATTERNS_PATH/*.json (per-world patterns)

APPLICATION:
  StrategySelector.evaluate_next_action(settlement)
    ↓
  # NEW: Consult learned patterns before scoring
  patterns = OperationalManager.load_trained_patterns()
  best_pattern = OperationalManager.find_expansion_pattern()
  
  if best_pattern && pattern_confidence(best_pattern) > THRESHOLD
    # Apply pattern-based boost to settlement_expansion options
    scored_options.map { |opt| 
      opt[:type] == :settlement_expansion ? 
        opt.merge(score: opt[:score] * pattern_boost(best_pattern)) : opt
    }
  end

SUCCESS FEEDBACK LOOP (future):
  After deployment completes:
    if deployment.success?
      OperationalManager.record_successful_deployment(settlement, pattern)
      # Update pattern confidence scores in AI_SETTLEMENT_PATTERNS_PATH
    end
```

---

## 5. Phase Alignment Assessment

| Phase | Relevance | Notes |
|---|---|---|
| **MVP (Phase 1-6)** | Low | Pattern learning is not MVP-scoped. Current StrategySelector scoring is sufficient for single-world MVP. |
| **Phase 6+** | Medium | Worldhouse Learning exists but is wormhole-specific. General pattern infrastructure could be wired in during Phase 6 if cross-world settlement becomes relevant. |
| **Phase 14+ (Act 2+)** | High | Pattern learning across multiple worlds/systems is the primary use case. This is when players enter and AI Manager operates across worlds. |

**Recommendation**: Defer implementation to Phase 14+. The existing infrastructure is ready for integration when the feature scope becomes relevant.

---

## 6. Acceptance Criteria Status

| Criterion | Status | Notes |
|---|---|---|
| Audit of StrategySelector scoring mechanism | ✅ DONE | Stateless, priority-weighted scoring, no pattern awareness |
| Audit of StateAnalyzer state data | ✅ DONE | Current state only (inventory, power, cost pressure), no history |
| Assessment of tasks_v2 pattern metadata support | ✅ DONE | No pattern support — pure task execution library |
| Assessment of Worldhouse Learning generalization scope | ✅ DONE | Wormhole-specific only, does not generalize |
| Architecture recommendation with justification | ✅ DONE | Use existing MissionProfileAnalyzer + OperationalManager + PatternValidator |
| Data contract proposal for pattern flow | ✅ DONE | Capture → Storage → Application → Success feedback loop defined |
| Phase alignment assessment (MVP vs Phase 6+ vs Phase 14+) | ✅ DONE | Phase 14+ primary, Phase 6+ possible if cross-world needed earlier |

---

## 7. Critical Findings & Recommendations

### 7.1 Task Supersession Assessment

**The research task is PARTIALLY SUPERSEDED by existing architecture.**

What was expected: Research how to build pattern learning from scratch.
What exists: Complete pattern infrastructure (capture, storage, scoring, validation) — just not wired into StrategySelector.

### 7.2 Remaining Work (If Pursued in Phase 14+)

1. **Integration**: Wire `OperationalManager.find_expansion_pattern()` into `StrategySelector.evaluate_next_action()`
2. **Population**: Run `MissionProfileAnalyzer.train_ai_manager_with_patterns()` to populate pattern storage
3. **Feedback Loop**: Add `record_successful_deployment()` to capture deployment outcomes back into patterns
4. **Confidence Scoring**: Add confidence/trust scores to patterns based on historical success rate

### 7.3 Gotchas Verified

| Gotcha | Status | Finding |
|---|---|---|
| SettlementPattern model obsolete | ✅ Confirmed | Pattern learning lives in services, not models |
| Act 2+ concept, not MVP-scoped | ✅ Confirmed | Phase 14+ primary alignment |
| Worldhouse Learning narrow scope | ✅ Confirmed | Wormhole-specific only |
| **NEW: Existing pattern infrastructure** | ⚠️ Not anticipated | MissionProfileAnalyzer + OperationalManager + PatternValidator form complete system |

---

## 8. Files Audited

| File | Purpose | Lines Reviewed |
|---|---|---|
| `galaxy_game/app/services/ai_manager/strategy_selector.rb` | Audit scoring mechanism | 1-350 (full) |
| `galaxy_game/app/services/ai_manager/state_analyzer.rb` | Audit state data available | 1-100 (full) |
| `galaxy_game/app/services/ai_manager/mission_scorer.rb` | Audit scoring weights | 1-150 (partial) |
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Check task library structure | 1-150 (partial) |
| `galaxy_game/app/services/ai_manager/operational_manager.rb` | Audit pattern loading/scoring | 1-100, 660-850 (partial) |
| `galaxy_game/app/services/ai_manager/mission_profile_analyzer.rb` | Audit pattern extraction | 1-100 (partial) |
| `galaxy_game/app/services/ai_manager/pattern_validator.rb` | Audit pattern validation | 1-100 (partial) |
| `galaxy_game/app/services/ai_manager/wormhole_coordinator.rb` | Check Worldhouse Learning scope | 570-650 (partial) |
| `galaxy_game/app/controllers/admin/celestial_bodies_controller.rb` | Check GCC bootstrap patterns | 740-970 (partial) |
| `galaxy_game/config/initializers/game_data_paths.rb` | Audit path definitions | 243-265 (partial) |
| `data/json-data/missions_v2/` | Check for pattern metadata | Directory listing |
| `integration-tests/ai_manager_mission_training_integration.rb` | Verify training integration | 1-100 (partial) |

---

## 9. Conclusion

The pattern-based settlement decision logic architecture **already exists** in the codebase as a collection of services:

- **Capture**: `MissionProfileAnalyzer` extracts patterns from mission JSON profiles
- **Storage**: `AI_MISSION_PATTERNS_PATH` + `AI_SETTLEMENT_PATTERNS_PATH` (JSON files)
- **Scoring**: `OperationalManager.pattern_score()` with ISRU/equipment/world-compatibility weights
- **Validation**: `PatternValidator.assess_world_compatibility()` checks terrain/resource alignment
- **Application**: `OperationalManager.find_expansion_pattern()` returns best pattern for expansion

**The missing piece is integration**: `StrategySelector` does not consult learned patterns during decision-making. When this feature becomes relevant (Phase 14+, cross-world AI Manager operation), wiring `OperationalManager` into `StrategySelector` will complete the pattern learning loop.

**Recommendation**: Close this research task as **SUPERSEDED_BY_EXISTING_ARCHITECTURE**. The remaining work is a straightforward integration task, not architectural research.
