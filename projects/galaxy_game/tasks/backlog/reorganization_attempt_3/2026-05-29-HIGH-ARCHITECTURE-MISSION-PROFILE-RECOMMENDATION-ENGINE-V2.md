---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: Data Foundation — Mission Profile Recommendation Engine (v2 Framework)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-05-29
**Last Updated**: 2026-05-29

---

## Local Worker Triage Report

- **Template Conformance**: PASS — complete specification of recommendation framework
- **Docker Wrapper Check**: N/A — architecture/data task, no RSpec
- **MVP Alignment**: CRITICAL — enables dynamic mission profile generation, replaces static pattern lookup, essential for v2 framework
- **MVP Impact Note**: Foundation for AI Manager to recommend best expansion path into any world based on resources; enables player-first adaptive planning vs. hardcoded missions
- **Action Line**: READY FOR SPECIFICATION REVIEW — data architecture task, requires design approval before implementation

---

## Agent Assignment

**Assigned To**: Claude Sonnet 1x (Design Review) + 0x (Implementation)
**Why This Agent**: Sonnet 1x to codify recommendation engine architecture before code; 0x to implement service + integration
**Supervision Level**: Watched carefully (design review required before 0x implementation)

---

## Context

The Mission Planner currently uses hardcoded pattern lookups: `['mars-terraforming', 'venus-industrial', 'titan-fuel', 'asteroid-mining', 'europa-water']`. These static templates don't adapt to world properties or player capacity.

The v2 framework provides **generic, reusable components**:

- **tasks_v2/** — Generic service tasks (survey, tug capture, spine integration, hollowing, power/comms setup)
- **manifests_v2/** — Generic hardware kits (Precursor Pathfinder, Industrial Starter)
- **[world]/profiles/** — Reference examples showing how to compose generic components

The Recommendation Engine **dynamically builds mission profiles** by:

1. **Analyzing world state** (resources, body properties, connectivity, DC expansion mandate)
2. **Evaluating generic task applicability** (which tasks apply to this world?)
3. **Recommending manifest kits** (which hardware best serves expansion here?)
4. **Scoring alternatives** (ROI, resource match, timeline, player capacity)
5. **Returning sorted recommendations** (player chooses from AI-ranked options)

This replaces static mission databases with **player-first adaptive planning**.

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/ai_manager/AI_MANAGER_MASTER_PLAN.md` — mission profile framework, cycler network, v2 philosophy
- `docs/architecture/systems/asteroid_conversion_physics.md` — world qualification thresholds
- `data/json-data/missions/tasks_v2/README.md` — generic task library structure
- `data/json-data/missions/manifests_v2/` — generic manifest examples
- `data/json-data/missions/venus_settlement/profiles/` — reference implementations

---

## Problem Statement

**Current behavior**: 
- Mission Planner hardcodes 5 patterns; cannot adapt to new worlds or resource availability
- Each world requires a separate profile folder (venus_settlement, etc.)
- No mechanism to recommend best expansion path based on local resources
- No player choice — missions are predefined

**Expected behavior**: 
- AI Manager scans world properties (resources, body type, location, DC mandate)
- Recommends 3-5 expansion paths from generic components
- Scores recommendations by ROI, timeline, player capacity
- Player chooses preferred path, which triggers dynamic mission profile generation
- Profile built on-the-fly from tasks_v2 + manifests_v2 + world-specific parameters

---

## Files Involved

### New Files — you will create these
| File | Purpose |
|---|---|
| `app/services/ai_manager/mission_profile_recommendation_engine.rb` | Core recommendation service |
| `app/services/ai_manager/mission_profile_builder.rb` | Dynamic profile construction from generic components |
| `spec/services/ai_manager/mission_profile_recommendation_engine_spec.rb` | Recommendation engine spec |
| `docs/architecture/ai_manager_mission_v2_framework.md` | Architecture documentation |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/mission_planner_service.rb` | Current implementation to replace |
| `data/json-data/missions/tasks_v2/` | Generic task library |
| `data/json-data/missions/manifests_v2/` | Generic manifest library |
| `data/json-data/missions/venus_settlement/profiles/` | Reference profiles for pattern analysis |
| `config/initializers/game_constants.rb` | DC mandate values, expansion metrics |

### Migration
- [ ] No migration needed — uses existing model structure

---

## Architecture Specification

### 1. Recommendation Engine: World Analysis

```ruby
class AIManager::MissionProfileRecommendationEngine
  def initialize(world)
    @world = world  # CelestialBody or settlement context
    @resources = analyze_resources
    @properties = analyze_body_properties
    @location = analyze_location
    @dc_mandate = read_dc_mandate
  end

  def recommend
    # Score all applicable expansion paths
    candidates = evaluate_expansion_candidates
    
    # Rank by ROI, player capacity, timeline
    ranked = candidates.sort_by { |c| c[:score] }.reverse.take(5)
    
    # Return actionable recommendations
    ranked.map { |c| format_recommendation(c) }
  end

  private

  def evaluate_expansion_candidates
    # Each candidate is: { path_id, description, tasks, manifest, roi, timeline, player_cost }
    [
      evaluate_orbital_foothold,
      evaluate_local_isru,
      evaluate_asteroid_conversion,
      evaluate_regional_network,
      evaluate_advanced_expansion
    ].compact
  end
end
```

### 2. Recommendation Scoring

| Factor | Weight | Calculation |
|--------|--------|-------------|
| **Resource Match** | 40% | % of required resources available locally |
| **ROI Estimate** | 30% | Economic return over timeline |
| **Timeline** | 20% | Days to first revenue (lower = better) |
| **Player Capacity** | 10% | Player GDP vs. resource requirements |

**Example scoring**:
- Venus atmospheric harvesting (high resource match, high ROI, long timeline, high upfront cost) = 78
- Luna ISRU (perfect resource match, moderate ROI, medium timeline, low cost) = 85
- Asteroid conversion (variable match, high ROI, very long timeline, high cost) = 72

### 3. Mission Profile Builder: Generic → Specific

```ruby
class AIManager::MissionProfileBuilder
  def build_from_recommendation(recommendation, player_params)
    # Load generic tasks from tasks_v2/
    generic_tasks = load_generic_tasks(recommendation[:task_ids])
    
    # Load generic manifest from manifests_v2/
    generic_manifest = load_manifest(recommendation[:manifest_id])
    
    # Interpolate world-specific parameters
    interpolated_tasks = generic_tasks.map do |task|
      task.merge(
        'target_body' => @world.name,
        'resources_available' => @resources,
        'travel_time' => calculate_travel_time,
        'player_capacity' => player_params[:budget_gcc]
      )
    end
    
    # Compose into full mission profile
    {
      profile_id: generate_profile_id,
      world: @world.name,
      recommendation_id: recommendation[:id],
      tasks: interpolated_tasks,
      manifest: generic_manifest,
      timeline_days: calculate_timeline,
      estimated_cost: calculate_cost,
      player_revenue: calculate_player_revenue,
      success_criteria: recommendation[:success_criteria],
      generated_at: Time.current
    }
  end

  private

  def load_generic_tasks(task_ids)
    task_ids.map do |task_id|
      JSON.parse(
        File.read("data/json-data/missions/tasks_v2/#{task_id}.json")
      )
    end
  end

  def load_manifest(manifest_id)
    JSON.parse(
      File.read("data/json-data/missions/manifests_v2/#{manifest_id}.json")
    )
  end
end
```

### 4. Integration with Mission Planner

Replace current hardcoded pattern logic:

```ruby
# OLD: Mission Planner directly hardcodes patterns
@available_patterns = ['mars-terraforming', 'venus-industrial', ...]

# NEW: Mission Planner uses Recommendation Engine
def planner
  @settlement = current_settlement
  @world = @settlement.celestial_body

  # Get AI recommendations
  engine = AIManager::MissionProfileRecommendationEngine.new(@world)
  @recommendations = engine.recommend  # Returns 3-5 ranked options

  # If player selects one, build dynamic profile
  if params[:selected_recommendation_id].present?
    builder = AIManager::MissionProfileBuilder.new(@world)
    @profile = builder.build_from_recommendation(
      @recommendations.find { |r| r[:id] == params[:selected_recommendation_id] },
      { budget_gcc: params[:budget_gcc] }
    )
  end
end
```

### 5. Recommendation Data Format

```json
{
  "recommendation_id": "luna_isru_phase1",
  "world": "Luna",
  "path_description": "Establish ISRU facility for regolith processing",
  "expansion_phase": 1,
  "task_ids": [
    "task_body_survey_v1",
    "task_3d_printed_mounts",
    "task_resource_processing_v1",
    "task_connect_power_comms"
  ],
  "manifest_id": "manifest_precursor_pathfinder_v1",
  "resource_requirements": {
    "orbital_platform_kit": 1,
    "survey_equipment": 1,
    "power_modules": 2
  },
  "estimated_timeline_days": 180,
  "estimated_cost_gcc": 450000,
  "estimated_player_revenue_gcc": 200000,
  "roi_estimate": 0.85,
  "score": 85,
  "success_criteria": [
    "ISRU facility online",
    "First harvest complete",
    "Revenue contracts active"
  ]
}
```

### 6. DC Mandate Integration

```ruby
def apply_dc_mandate
  # Development Corporations are non-profits with expansion mandate
  # Bias recommendations toward:
  # - Player-first benefit (70% weight)
  # - Growth opportunity (20% weight)
  # - Profit (10% weight)

  @recommendations.each do |rec|
    rec[:dc_alignment_score] = (
      (rec[:player_revenue] / rec[:cost]) * 0.7 +      # Player benefit
      (rec[:expansion_phase]) * 0.2 +                   # Growth opportunity
      (rec[:profit_estimate]) * 0.1                     # Profit (tertiary)
    )
  end
end
```

---

## Implementation Plan

### Phase 1 — Design Review (THIS TASK)
- [ ] Specification review: recommendation engine algorithm
- [ ] Data format validation: recommendation JSON schema
- [ ] Integration points verified: MissionPlanner, MissionBuilder
- [ ] DC mandate rules codified

### Phase 2 — Implementation (FOLLOW-UP TASK)
- [ ] MissionProfileRecommendationEngine service
- [ ] MissionProfileBuilder service
- [ ] Controller integration
- [ ] Comprehensive spec suite
- [ ] Reference documentation

---

## Acceptance Criteria (Design Phase)
- [ ] Recommendation engine algorithm specified with scoring weights
- [ ] Mission profile builder interpolation logic documented
- [ ] Sample recommendation JSON schema validated
- [ ] DC mandate rules explicitly codified
- [ ] Integration points with MissionPlanner identified
- [ ] Reference profiles (venus, luna) analyzed for composition patterns
- [ ] Stop conditions and edge cases identified
- [ ] Architecture documentation created

---

## Documentation
- [ ] Create `docs/architecture/ai_manager_mission_v2_framework.md` — Recommendation engine, builder, DC mandate, v2 philosophy, migration path from v1

---

## Dependencies
**Blocked by**: none
**Blocks**: Dynamic mission profile generation, adaptive expansion logic
**Related tasks**: 
- Mission Planner (will integrate Recommendation Engine)
- tasks_v2/manifests_v2 (already exist, this consumes them)

---

## Completion Report (Design Phase)
*Filled in after specification review*

**Completed by**: Claude Sonnet 1x
**Completion date**: YYYY-MM-DD
**Status**: SPECIFICATION APPROVED | NEEDS REVISIONS

### Specification Validated
- [ ] Recommendation scoring algorithm
- [ ] Mission profile builder interpolation
- [ ] DC mandate rules
- [ ] Integration with MissionPlanner
- [ ] JSON schema for recommendations
- [ ] Edge case handling

### Issues to Address Before Implementation
[Any architectural concerns raised during review]

### Next Phase Approval
[Proceed to implementation with 0x agent, or requires further review]

---

## Handoff Summary

HANDOFF SUMMARY: v2 framework architecture specified | Recommendation engine algorithm locked | Builder interpolation logic defined | Ready for 0x implementation phase
