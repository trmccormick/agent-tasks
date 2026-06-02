---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: SPEC_HEALTH
local_worker_safe: false
---

# TASK: Scout Ships — Seismic Survey Implementation
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-03-24
**Last Updated**: 2026-05-29

---

## Local Worker Triage Report

- **Template Conformance**: PASS — all sections complete, well-specified
- **Docker Wrapper Check**: PASS — RSpec commands use correct Docker isolation
- **MVP Alignment**: VALID — enables AI Manager asteroid conversion candidate evaluation
- **MVP Impact Note**: Provides structural integrity classification required before AI Manager approves Eden AWS Anchor placement on asteroids
- **Action Line**: READY FOR CLOUD HANDOFF — Sonnet 1x autonomous OK, migration review + code implementation + testing sequence clear

---

## Agent Assignment

**Assigned To**: Claude Sonnet 1x
**Why This Agent**: Cross-service feature touching model, migration, two AI manager services, and controller logic; requires architectural reasoning to integrate seismic mode correctly into WormholeScoutingService; well-specified with clear insertion points
**Supervision Level**: Standard

---

## Context

Scout-class ships perform scouting missions via `WormholeScoutingService`. The AI Manager uses scouting results to evaluate asteroid conversion candidates via `StationCostBenefitAnalyzer`. This task adds a seismic survey mode that classifies asteroids as Rubble Piles or Solid Anchors — a mandatory prerequisite for the AI Manager's asteroid conversion strategy. Without this classification, the cost-benefit analyzer cannot safely approve Eden AWS Anchor placement.

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/systems/asteroid_conversion_physics.md` — physics thresholds for structural integrity and thermal risk classification
- `docs/architecture/systems/survey_and_handshake_protocol.md` — survey result handshake format between scouting and AI manager
- `docs/developer/WORMHOLE_SCOUTING_INTEGRATION.md` — integration pattern for WormholeScoutingService extensions
- `config/initializers/game_constants.rb` — physics constants for density/integrity calculations

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Problem Statement

Scout ships have no seismic survey capability. The AI Manager's asteroid conversion strategy requires structural integrity data before approving an asteroid as an Eden AWS Anchor candidate. Currently `StationCostBenefitAnalyzer` cannot reject low-integrity asteroids because that data is never collected.

**Current behavior**: 
- `WormholeScoutingService#execute_scouting_mission` has no seismic mode
- Asteroids have no `structural_integrity_score` or `surveyed_at` fields
- `StationCostBenefitAnalyzer` cannot filter on integrity

**Expected behavior**: 
- When called with `:seismic_mode`, the scouting mission classifies the asteroid's structural integrity and thermal risk
- Asteroid model updated with new fields
- Analyzer rejects any asteroid with `integrity_score < 0.5` for Eden AWS Anchor placement

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/models/asteroid.rb` | Asteroid model | add new fields after migration |
| `app/services/ai_manager/wormhole_scouting_service.rb` | Scouting mission execution | `#execute_scouting_mission` line ~52 |
| `app/services/ai_manager/station_cost_benefit_analyzer.rb` | Asteroid candidate evaluation | `#analyze` line — grep for Eden/anchor logic |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/architecture/systems/asteroid_conversion_physics.md` | Source of truth for density/ice/integrity thresholds |
| `docs/architecture/systems/survey_and_handshake_protocol.md` | Required handshake format for survey results |
| `docs/developer/WORMHOLE_SCOUTING_INTEGRATION.md` | Established pattern for extending scouting service |

### Migration
- [x] Migration needed: add `surveyed_at` (datetime) and `structural_integrity_score` (float) to asteroids table

```bash
docker exec -it web bash -c 'unset DATABASE_URL && bundle exec rails generate migration AddSeismicFieldsToAsteroids surveyed_at:datetime structural_integrity_score:float'
```

Review the generated migration before running it. Then:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && bundle exec rails db:migrate'
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails db:migrate'
```

---

## Implementation Steps

> Use as a guide, apply judgment. Read all three reference docs before touching any file.

### Step 1 — Generate and run migration

Generate migration as shown above. Verify schema.rb reflects both new columns before proceeding.

### Step 2 — Add seismic fields to Asteroid model

In `app/models/asteroid.rb`, add any necessary validations or scopes for `structural_integrity_score` and `surveyed_at`. Check existing model for established patterns before adding.

```ruby
validates :structural_integrity_score, numericality: { 
  greater_than_or_equal_to: 0.0, 
  less_than_or_equal_to: 1.0 
}, allow_nil: true

scope :surveyed, -> { where.not(surveyed_at: nil) }
scope :high_integrity, -> { where('structural_integrity_score >= ?', 0.5) }
scope :low_integrity, -> { where('structural_integrity_score < ?', 0.5) }
```

### Step 3 — Add seismic mode to WormholeScoutingService

Inside `#execute_scouting_mission` (line ~52), add a conditional block for `:seismic_mode`. Logic per `asteroid_conversion_physics.md`:

```ruby
# seismic mode classification
if mode == :seismic_mode
  # Density-based integrity estimation (per asteroid_conversion_physics.md)
  integrity_score = asteroid.density < 1.5 ? rand(0.0..0.2) : rand(0.5..1.0)
  
  # Thermal risk assessment (per survey_and_handshake_protocol.md)
  ice_percentage = asteroid.composition&.fetch('ice_percentage', 0).to_f
  thermal_risk = ice_percentage > 20.0

  # Update asteroid with survey results
  asteroid.update!(
    structural_integrity_score: integrity_score,
    surveyed_at: Time.current,
    metadata: asteroid.metadata.merge(
      'thermal_risk' => thermal_risk,
      'classification' => integrity_score < 0.2 ? 'rubble_pile' : 'solid_anchor'
    )
  )
end
```

> Verify the exact field names and composition data structure against the actual Asteroid model and existing scouting result format before applying. Adjust if the model uses different accessors.

### Step 4 — Add integrity gate to StationCostBenefitAnalyzer

Locate the Eden AWS Anchor evaluation logic in `station_cost_benefit_analyzer.rb`. Add a rejection condition:

```ruby
# Reject asteroids with insufficient structural integrity
if asteroid.structural_integrity_score.present? && asteroid.structural_integrity_score < 0.5
  return rejection_result('structural_integrity_below_threshold')
end
```

Match the existing rejection pattern used elsewhere in the analyzer — do not invent a new return format.

### Step 5 — Verify isolation

Run the scouting service spec:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/wormhole_scouting_service_spec.rb -v 2>&1'
```

Expected result: All examples pass, 0 failures.

### Step 6 — Synthesis Report (before committing)

Pause and produce this report — **do not commit until user approves**:

```
SYNTHESIS REPORT
Files modified:
  - app/models/asteroid.rb — added validations + scopes (lines ~XX-YY)
  - app/services/ai_manager/wormhole_scouting_service.rb — added seismic mode (lines ~XX-YY)
  - app/services/ai_manager/station_cost_benefit_analyzer.rb — added integrity gate (lines ~XX-YY)
  - db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb — new file
  - db/schema.rb — auto-updated by migration

Migration applied:
  ✅ dev database
  ✅ test database
  
Asteroid model state:
  [specify: validations added, scopes added, existing patterns verified]

Scouting service integration:
  [specify: seismic mode added at execution point, composition data access verified]

Analyzer integration:
  [specify: integrity gate integrated with existing rejection pattern]

Spec result:
  [paste test output summary — should be: X examples, 0 failures]

RISKS
  - Asteroid used across multiple scouting and conversion services
  - No other models reference structural_integrity_score (verified via grep)
  - Random integrity calculation may need adjustment based on actual density/composition data
  - Thermal risk logic depends on composition field structure

READY TO APPLY? — waiting for approval
```

---

## Testing Sequence

After Step 6 synthesis report and user approval:

1. **Isolation run**:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/wormhole_scouting_service_spec.rb 2>&1'
```

Expected: X examples, 0 failures

2. **Related AI manager specs** (ensure no regressions):
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/station_cost_benefit_analyzer_spec.rb 2>&1'
```

Expected: no new failures

3. **All model specs**:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/asteroid_spec.rb 2>&1'
```

Expected: no new failures

4. **Full suite** — only after steps 1-3 are green:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec > /home/galaxy_game/log/rspec_full_$(date +%s).log 2>&1'
```

Then verify:
```bash
docker exec -it web bash -c 'tail -50 /home/galaxy_game/log/rspec_full_*.log | grep -E "examples?|failures?"'
```

---

## Acceptance Criteria
- [ ] Migration file generated and reviewed
- [ ] Migration runs cleanly on dev database without errors
- [ ] Migration runs cleanly on test database without errors
- [ ] `schema.rb` shows `surveyed_at` datetime and `structural_integrity_score` float columns
- [ ] `Asteroid` model has validations and scopes for new fields
- [ ] `execute_scouting_mission` with `:seismic_mode` sets integrity score and thermal risk correctly
- [ ] Asteroids with `integrity_score < 0.5` are rejected by `StationCostBenefitAnalyzer` for Eden AWS Anchor
- [ ] Isolation run: 0 failures
- [ ] No regressions in `spec/services/ai_manager/` suite
- [ ] Full suite run completed and logged

---

## Stop Conditions — escalate to user immediately if:
- Migration generates unexpected schema changes beyond the two new columns
- `execute_scouting_mission` signature or mode-handling pattern differs significantly from what this task assumes
- `StationCostBenefitAnalyzer` rejection pattern is not obvious — do not invent a new one
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Composition data structure differs from expected format
- Any architectural decision is required beyond what the reference docs cover

---

## Commit Instructions

Run git commands on **host**, not inside container:

```bash
git add app/models/asteroid.rb
git add app/services/ai_manager/wormhole_scouting_service.rb
git add app/services/ai_manager/station_cost_benefit_analyzer.rb
git add "db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb"
git add db/schema.rb
git commit -m "feature: asteroid seismic survey — add structural integrity classification and analyzer gate"
git push
```

---

## Documentation
- [ ] No additional doc changes needed — reference docs already cover this feature

---

## Dependencies
**Blocked by**: none
**Blocks**: AI Manager asteroid conversion strategy tasks (requires structural data for qualification)
**Related tasks**: 
- Mission Planner (uses this classification in sourcing decisions)
- Asteroid conversion physics implementation

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `app/models/asteroid.rb` — added `structural_integrity_score` and `surveyed_at` fields with validations and scopes
- `app/services/ai_manager/wormhole_scouting_service.rb` — added `:seismic_mode` classification logic
- `app/services/ai_manager/station_cost_benefit_analyzer.rb` — added integrity score gating for Eden AWS Anchor
- `db/migrate/[timestamp]_add_seismic_fields_to_asteroids.rb` — new migration file
- `db/schema.rb` — auto-updated by migration

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: Migration applied | Seismic survey mode implemented | Integrity gating active in cost-benefit analyzer
