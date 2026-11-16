---
status: backlog
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md \
         projects/galaxy_game/tasks/active/2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Replace calculate_habitability with improved formula (water factor + life bonus)
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-25
**Last Updated**: 2026-07-25

## Problem

The current `calculate_habitability` method in `galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb` (line ~105) uses a simple habitability formula. A proposed replacement from `docs/developer/pending_changes.md` adds:

1. **Liquid water factor** — derived from hydrosphere's `state_distribution['liquid']`
2. **Life presence bonus** — bootstrapping effect when life already exists (up to 10% bonus)
3. **Refined weighting** — O2 30%, temp 30%, water 25%, pressure 15%
4. **Granular factor curves** — each factor has multi-tier suitability ranges

## Proposed Implementation

### New `calculate_habitability` method (replace existing at line ~105)

```ruby
def calculate_habitability
  atmo = celestial_body&.atmosphere
  return 0.0 unless atmo && atmo.gases.exists?
  
  # Get environmental parameters
  o2_level = atmo.gases.find_by(name: 'O2')&.percentage.to_f
  pressure = atmo.pressure.to_f
  temp = celestial_body.surface_temperature.to_f
  
  # Get liquid water availability from hydrosphere
  hydro = celestial_body.hydrosphere
  liquid_water_pct = hydro&.state_distribution&.dig('liquid').to_f || 0.0
  
  # === OXYGEN FACTOR ===
  # Need at least 0.1% O2 for complex life
  # Optimal: 10-21%
  # Toxic: >50%
  o2_factor = if o2_level < 0.1
    o2_level / 0.1
  elsif o2_level < 10.0
    0.5 + (o2_level / 20.0)
  elsif o2_level <= 21.0
    1.0
  elsif o2_level <= 50.0
    1.0 - ((o2_level - 21.0) / 58.0)
  else
    0.1
  end
  
  # === TEMPERATURE FACTOR ===
  temp_factor = if temp < 200
    0.0
  elsif temp < 250
    (temp - 200) / 50.0 * 0.3
  elsif temp < 273
    0.3 + ((temp - 250) / 23.0 * 0.3)
  elsif temp <= 310
    1.0
  elsif temp < 350
    1.0 - ((temp - 310) / 40.0 * 0.5)
  else
    0.1
  end
  
  # === PRESSURE FACTOR ===
  pressure_factor = if pressure < 0.001
    0.0
  elsif pressure < 0.01
    pressure / 0.01 * 0.3
  elsif pressure < 0.5
    0.3 + ((pressure - 0.01) / 0.49 * 0.4)
  elsif pressure <= 2.0
    1.0
  elsif pressure < 5.0
    1.0 - ((pressure - 2.0) / 3.0 * 0.3)
  else
    0.3
  end
  
  # === WATER FACTOR (NEW) ===
  water_factor = if liquid_water_pct < 0.1
    liquid_water_pct / 0.1 * 0.2
  elsif liquid_water_pct < 1.0
    0.2 + (liquid_water_pct / 1.0 * 0.3)
  else
    [0.5 + (liquid_water_pct / 100.0 * 0.5), 1.0].min
  end
  
  # === COMBINED HABITABILITY ===
  habitability = (
    o2_factor * 0.30 +
    temp_factor * 0.30 +
    water_factor * 0.25 +
    pressure_factor * 0.15
  )
  
  # Apply life presence bonus (bootstrapping effect)
  if life_forms.any? && life_forms.sum(:population) > 1_000_000
    life_bonus = [life_forms.count * 0.02, 0.1].min
    habitability = [habitability + life_bonus, 1.0].min
  end
  
  self.habitable_ratio = habitability
  save!
  
  puts "  Habitability: #{(habitability * 100).round(2)}% " \
       "(O2:#{(o2_factor * 100).round(0)}% Temp:#{(temp_factor * 100).round(0)}% " \
       "H2O:#{(water_factor * 100).round(0)}% P:#{(pressure_factor * 100).round(0)}%)"
  
  habitability
end
```

## Prerequisites

1. **Verify hydrosphere has `state_distribution`**: Check that `CelestialBodies::Hydrosphere` model has a `state_distribution` JSONB column with a `'liquid'` key. If not, add it or use an alternative water metric.

2. **Verify `life_forms` association exists**: The Biosphere model already has `has_many :life_forms, class_name: 'Biology::LifeForm'` — confirm this is correct.

3. **Check existing callers**: Search for all callers of `calculate_habitability`:
   ```bash
   grep -rn "calculate_habitability" galaxy_game/app/
   ```
   Ensure the new formula won't break any dependent logic.

## Implementation Steps

1. Replace the existing `calculate_habitability` method in `galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb` (line ~105) with the proposed implementation above.

2. Update specs in `galaxy_game/spec/models/celestial_bodies/spheres/biosphere_spec.rb`:
   - Add examples for water factor (low/medium/high liquid water scenarios)
   - Add examples for life presence bonus
   - Update existing expectations if the formula produces different values

3. Run full biosphere spec suite:
   ```bash
   docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/models/celestial_bodies/spheres/biosphere_spec.rb --format documentation
   ```

## Acceptance Criteria
- [ ] `calculate_habitability` uses 4-factor weighted formula (O2 30%, temp 30%, water 25%, pressure 15%)
- [ ] Liquid water factor derives from hydrosphere's `state_distribution['liquid']`
- [ ] Life presence bonus applies when `life_forms.sum(:population) > 1_000_000`
- [ ] All biosphere specs pass (0 failures, 0 errors)
- [ ] No regressions in dependent services (check callers identified in prerequisites)

## Risks & Gotchas
- **Hydrosphere dependency**: If `hydrosphere.state_distribution` doesn't have a `'liquid'` key, the method falls back to 0.0 — this is intentional but should be documented
- **Life forms association**: The bonus uses `life_forms.sum(:population)` — ensure the Biology::LifeForm model has a `population` column
- **Breaking change**: Existing callers may get different habitability values — verify with integration tests

## Notes
- This is a feature enhancement, not a bug fix
- The formula is inspired by real-world astrobiology habitability indices
- Consider adding this to the biosphere simulation pipeline if applicable
