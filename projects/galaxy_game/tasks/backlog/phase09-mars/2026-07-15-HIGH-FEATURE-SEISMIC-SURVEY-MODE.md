---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md \
         projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY*"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-SEISMIC-SURVEY-MODE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Seismic Survey Mode — WormholeScoutingService Classification
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context

The AI Manager's station conversion strategy requires structural integrity data before approving an asteroid for relocation and hollowing. This task adds a `:seismic_mode` to `WormholeScoutingService#execute_scouting_mission` that classifies asteroids using physics-based thresholds from `asteroid_conversion_physics.md`. The classification feeds into the system-agnostic tasks_v2 pipeline (object_transit_and_relocation, task_capture_stabilization, task_hollowing_mining_operation).

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/systems/asteroid_conversion_physics.md` — **SOURCE OF TRUTH** for density/integrity thresholds
  - Density < 1.5 g/cm³ → rubble pile (disqualified)
  - Ice > 20% → thermal risk (requires Thermal Shielding sub-phase)
  - Mass-to-void ratio: `is_hollow: true` → mandatory Internal Bracing phase
- `docs/architecture/systems/survey_and_handshake_protocol.md` — survey result handshake format
- `docs/new_agent/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md` — companion task (scout ship vessel)

---

## Problem Statement

**Current behavior**: 
- `WormholeScoutingService#execute_scouting_mission(target_system_name)` has no seismic mode
- Asteroids have no `structural_integrity_score` or `surveyed_at` fields
- `StationCostBenefitAnalyzer` cannot filter on integrity (no data to filter)

**Expected behavior**: 
- When called with `:seismic_mode`, the scouting mission classifies the asteroid's structural integrity and thermal risk
- Asteroid model updated with new fields (`structural_integrity_score`, `surveyed_at`)
- Analyzer can reject any asteroid with `integrity_score < 0.5` for station conversion (anchor/wormhole station)

---

## Architecture Gotchas

⚠️ **GOTCHA 1: Method signature change**
- ❌ Wrong: Assume `execute_scouting_mission(mode, asteroid)` — it takes `(target_system_name)` string
- ✅ Right: Add seismic mode as an optional second parameter or keyword argument
- Why: Current signature is `execute_scouting_mission(target_system_name)`. Need to extend without breaking existing callers.

⚠️ **GOTCHA 2: Asteroid model path**
- ❌ Wrong: `app/models/asteroid.rb` — this file does not exist at that path
- ✅ Right: `celestial_bodies/minor_bodies/asteroid.rb` — STI under CelestialBody
- Why: Model is namespaced under `CelestialBodies::MinorBodies::Asteroid`

⚠️ **GOTCHA 3: Physics thresholds are in docs, not code**
- ❌ Wrong: Hard-code arbitrary thresholds
- ✅ Right: Use exact thresholds from `asteroid_conversion_physics.md`:
  - Density < 1.5 g/cm³ → rubble pile (integrity 0.0–0.2)
  - Ice > 20% → thermal risk flag
  - Integrity ≥ 0.5 → solid_anchor (qualified for station conversion)

⚠️ **GOTCHA 4: SuperMarsSettlementService already handles asteroid redirection**
- `ai_manager/super_mars_settlement_service.rb` has `moonless_planet_pattern` that redirects asteroids to Mars
- This service should check integrity BEFORE redirecting — but that's Task C, not this task

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb` | **NEW** — add integrity fields | Migration |
| `galaxy_game/app/models/celestial_bodies/minor_bodies/asteroid.rb` | Add validations + scopes | After line 3 (class definition) |
| `galaxy_game/app/services/ai_manager/wormhole_scouting_service.rb` | Add seismic mode | `#execute_scouting_mission` line ~82 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/architecture/systems/asteroid_conversion_physics.md` | **SOURCE OF TRUTH** for physics thresholds |
| `galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb` | Understand existing rejection pattern (Task C) |
| `galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb` | Understand existing asteroid handling (Task C) |

### Migration
- [x] Migration needed: add `surveyed_at` (datetime) and `structural_integrity_score` (float) to asteroids table

```bash
docker exec -it web bash -c 'unset DATABASE_URL && bundle exec rails generate migration AddSeismicFieldsToAsteroids surveyed_at:datetime structural_integrity_score:float'
```

Review the generated migration before running it. Then:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake db:migrate
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake db:migrate RAILS_ENV=test
```

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md \
       projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Generate and run migration

```bash
docker exec -it web bash -c 'unset DATABASE_URL && bundle exec rails generate migration AddSeismicFieldsToAsteroids surveyed_at:datetime structural_integrity_score:float'
```

Review the generated migration. Verify it targets the `asteroids` table (not a different table). Then run:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake db:migrate
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake db:migrate RAILS_ENV=test
```

Verify schema.rb reflects both new columns.

### Step 2 — Add seismic fields to Asteroid model

In `galaxy_game/app/models/celestial_bodies/minor_bodies/asteroid.rb`, add validations and scopes after the existing validations:

```ruby
# Seismic survey fields
validates :structural_integrity_score, numericality: { 
  greater_than_or_equal_to: 0.0, 
  less_than_or_equal_to: 1.0 
}, allow_nil: true

scope :surveyed, -> { where.not(surveyed_at: nil) }
scope :high_integrity, -> { where('structural_integrity_score >= ?', 0.5) }
scope :low_integrity, -> { where('structural_integrity_score < ?', 0.5) }
scope :rubble_pile, -> { where('structural_integrity_score < ?', 0.2) }
```

### Step 3 — Add seismic mode to WormholeScoutingService

In `galaxy_game/app/services/ai_manager/wormhole_scouting_service.rb`, modify `execute_scouting_mission` to accept an optional mode parameter:

```ruby
# Execute full scouting mission: create wormhole, deploy probes, complete system data
# Optional mode: :seismic_mode for asteroid structural integrity classification
def execute_scouting_mission(target_system_name, mode: nil)
  puts "[WormholeScoutingService] Executing scouting mission to #{target_system_name}" if defined?(Rails)
  Rails.logger.info "[WormholeScoutingService] Executing scouting mission to #{target_system_name}" if defined?(Rails)

  # Step 1: Create artificial wormhole for access
  wormhole = create_scouting_wormhole(target_system_name)
  return { status: :failed, reason: :wormhole_creation_failed } unless wormhole

  # Step 2: Load and complete system data
  system_data = load_system_data(target_system_name)
  return { status: :failed, reason: :system_data_not_found } unless system_data

  # Step 3: Deploy probes for intelligence gathering
  probe_results = deploy_scouting_probes(system_data)

  # Step 4: Analyze scouting results
  analysis = analyze_scouting_results(probe_results, system_data)

  # Step 5: Seismic mode — classify asteroids if requested
  seismic_results = nil
  if mode == :seismic_mode
    seismic_results = perform_seismic_classification(system_data)
  end

  # Step 6: Determine if system warrants full wormhole investment
  recommendation = generate_investment_recommendation(analysis)

  {
    status: :success,
    wormhole: wormhole,
    system_data: system_data,
    probe_results: probe_results,
    analysis: analysis,
    seismic_results: seismic_results,
    recommendation: recommendation
  }
end
```

Add the seismic classification method (in private section):

```ruby
def perform_seismic_classification(system_data)
  asteroids = system_data.dig('asteroids') || []
  
  results = asteroids.map do |ast_data|
    density = ast_data.dig('physical_properties', 'density') || ast_data['density'] || 2.0
    ice_percentage = ast_data.dig('composition', 'ice_percentage') || ast_data.dig('composition', 'volatile_percentage') || 0
    
    # Density-based integrity estimation (per asteroid_conversion_physics.md)
    integrity_score = if density < 1.5
      rand(0.0..0.2)  # rubble pile
    else
      rand(0.5..1.0)  # solid anchor
    end
    
    # Thermal risk assessment (per asteroid_conversion_physics.md)
    thermal_risk = ice_percentage > 20.0
    
    classification = if integrity_score < 0.2
      'rubble_pile'
    elsif integrity_score < 0.5
      'moderate_integrity'
    else
      'solid_anchor'
    end
    
    {
      asteroid_id: ast_data['id'] || ast_data['name'],
      density: density,
      ice_percentage: ice_percentage,
      structural_integrity_score: integrity_score,
      thermal_risk: thermal_risk,
      classification: classification
    }
  end
  
  results
end
```

> **NOTE**: The seismic classification uses `system_data` (loaded from star system JSON) to get asteroid density/composition. If the actual data structure differs, adjust field accessors accordingly. Verify against `data/json-data/star_systems/` files.

### Step 4 — Synthesis Report (before committing)

```
SYNTHESIS REPORT
Files modified:
  - db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb — new migration
  - galaxy_game/app/models/celestial_bodies/minor_bodies/asteroid.rb — added validations + scopes
  - galaxy_game/app/services/ai_manager/wormhole_scouting_service.rb — added seismic mode

Migration applied:
  ✅ dev database
  ✅ test database
  
Asteroid model state:
  [specify: validations added, scopes added, existing patterns verified]

Scouting service integration:
  [specify: execute_scouting_mission now accepts mode: keyword arg]
  [specify: seismic classification uses density < 1.5 threshold per physics docs]
  [specify: thermal risk flag set when ice_percentage > 20%]

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] Migration file generated and reviewed
- [ ] Migration runs cleanly on dev database without errors
- [ ] Migration runs cleanly on test database without errors
- [ ] `schema.rb` shows `surveyed_at` datetime and `structural_integrity_score` float columns on asteroids table
- [ ] `Asteroid` model has validations and scopes for new fields
- [ ] `execute_scouting_mission` accepts optional `mode:` keyword argument
- [ ] When `mode: :seismic_mode`, classification logic runs per physics docs thresholds
- [ ] Density < 1.5 → rubble pile (integrity 0.0–0.2)
- [ ] Ice > 20% → thermal_risk = true
- [ ] Integrity ≥ 0.5 → solid_anchor (qualified for Eden AWS Anchor)
- [ ] No existing specs broken by method signature change

---

## Stop Conditions — escalate to user immediately if:
- Migration generates unexpected schema changes beyond the two new columns
- `execute_scouting_mission` signature or mode-handling pattern differs significantly from what this task assumes
- Asteroid data structure in star system JSON doesn't have density/composition fields
- Same failure persists after two attempts
- Any architectural decision is required beyond what the reference docs cover

---

## Commit Instructions

Run git commands on **host**, not inside container:

```bash
git add db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb
git add galaxy_game/app/models/celestial_bodies/minor_bodies/asteroid.rb
git add galaxy_game/app/services/ai_manager/wormhole_scouting_service.rb
git add db/schema.rb
git commit -m "feature: seismic survey mode — add structural integrity classification to scouting service"
git push
```

---

## Documentation
- [ ] No additional doc changes needed — reference docs already cover this feature

---

## Dependencies
**Blocked by**: none (standalone implementation task)
**Blocks**: Task C (Integrity Gate) — needs survey data before gate can be wired in
**Related tasks**: 
- `2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md` — companion task (scout ship vessel)
- `2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md` — companion task (integrity gate)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb` — new migration file
- `galaxy_game/app/models/celestial_bodies/minor_bodies/asteroid.rb` — added validations + scopes
- `galaxy_game/app/services/ai_manager/wormhole_scouting_service.rb` — added seismic mode classification

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: Migration applied | Seismic survey mode implemented | Integrity gating active in cost-benefit analyzer