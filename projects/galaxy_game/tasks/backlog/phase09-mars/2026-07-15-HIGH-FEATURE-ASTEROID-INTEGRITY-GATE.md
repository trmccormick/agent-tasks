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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md \
         projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY*"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-ASTEROID-INTEGRITY-GATE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Asteroid Conversion Integrity Gate — StationCostBenefitAnalyzer + SuperMarsSettlementService
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context

This task wires the seismic survey results (from Task B) into one decision point:
1. **StationCostBenefitAnalyzer** — rejects low-integrity asteroids for station conversion

The physics thresholds come from `asteroid_conversion_physics.md`:
- Density < 1.5 g/cm³ → rubble pile (disqualified for station conversion)
- Ice > 20% → thermal risk (requires Thermal Shielding sub-phase)

**Note**: SuperMarsSettlementService was a thought exercise (no moons, no Earth/Venus analog). It's being removed as dead code in a separate task (`2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md`). Mars moon integrity checking will be wired into the actual Phase 9a Mars settlement service when it's implemented.

**System-Agnostic Intent**: The tasks_v2 pipeline (`object_transit_and_relocation`, `task_capture_stabilization`, `task_hollowing_mining_operation`) is designed to work in any discovered system. This task ensures the integrity gate supports that goal — checking asteroid properties against physics thresholds, not world names.
- Mass-to-void ratio: `is_hollow: true` → mandatory Internal Bracing phase

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/systems/asteroid_conversion_physics.md` — **SOURCE OF TRUTH** for thresholds
- `docs/architecture/systems/survey_and_handshake_protocol.md` — survey result handshake format
- `docs/new_agent/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md` — companion task (seismic mode that creates the data)

---

## Problem Statement

**Current behavior**: 
- `StationCostBenefitAnalyzer` has `:asteroid_conversion` cases but NO cohesion/thermal/rubble checks
- `SuperMarsSettlementService#moonless_planet_pattern` redirects asteroids to Mars without integrity check
- AI Manager can approve low-integrity asteroids for station conversion

**Expected behavior**: 
- `StationCostBenefitAnalyzer` rejects any asteroid with `integrity_score < 0.5` for Eden AWS Anchor
- `SuperMarsSettlementService` checks integrity before redirecting asteroids to Mars
- Thermal risk (ice > 20%) flagged as requiring "Thermal Shielding" sub-phase

---

## Architecture Gotchas

⚠️ **GOTCHA 1: StationCostBenefitAnalyzer works on generic construction_options**
- ❌ Wrong: Assume analyzer has asteroid-specific logic already
- ✅ Right: Add integrity gate to the existing `analyze_construction_option` method
- Why: Analyzer evaluates generic options with `:option[:type] == :asteroid_conversion`. Need to check for integrity data in the option's metadata.

⚠️ **GOTCHA 2: SuperMarsSettlementService uses hash-based asteroid data**
- ❌ Wrong: Call `asteroid.structural_integrity_score` on a hash
- ✅ Right: Check `asteroid[:structural_integrity_score]` or add integrity check before the redirect call
- Why: `moonless_planet_pattern` receives asteroids as hashes (`planet[:nearby_asteroids]`), not model instances

⚠️ **GOTCHA 3: Integrity data may not exist yet**
- ❌ Wrong: Crash if `structural_integrity_score` is nil (survey hasn't run)
- ✅ Right: If no integrity data exists, allow the operation but log a warning
- Why: Survey must run before conversion decision. Missing data = "unknown" not "disqualified"

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb` | Add integrity gate to analyzer | `#analyze_construction_option` line ~38 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/architecture/systems/asteroid_conversion_physics.md` | **SOURCE OF TRUTH** for thresholds |
| `galaxy_game/app/models/celestial_bodies/minor_bodies/asteroid.rb` | Model with integrity fields (from Task B) |

### Migration
- [x] No migration needed — uses fields created by Task B

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md \
       projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Add integrity gate to StationCostBenefitAnalyzer

In `galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb`, add integrity check in `analyze_construction_option`:

```ruby
def analyze_construction_option(option, available_resources, strategic_requirements)
  # INTEGRITY GATE: Check asteroid structural integrity for conversion options
  if option[:type] == :asteroid_conversion
    integrity_result = check_asteroid_integrity(option)
    return integrity_result if integrity_result[:rejected]
  end

  # Calculate financial metrics (existing code)
  financial_analysis = calculate_financial_metrics(option, available_resources)
  # ... rest of existing method unchanged
end

private

def check_asteroid_integrity(option)
  asteroid_data = option.dig(:option, :asteroid_data) || option[:asteroid_data] || {}
  integrity_score = asteroid_data['structural_integrity_score'] || asteroid_data[:structural_integrity_score]
  
  # If no survey data exists, allow but warn (survey may not have run yet)
  return { option: option, warning: 'No seismic survey data available for this asteroid' } if integrity_score.nil?
  
  # Cohesion gate: reject rubble piles (integrity < 0.5) for station conversion
  if integrity_score < 0.5
    return {
      rejected: true,
      reason: :structural_integrity_below_threshold,
      message: "Asteroid integrity score #{integrity_score} is below threshold 0.5 for station conversion"
    }
  end
  
  # Thermal risk flag (ice > 20%)
  ice_percentage = asteroid_data['ice_percentage'] || asteroid_data[:ice_percentage] || 0
  if ice_percentage > 20.0
    return {
      option: option,
      warning: :thermal_risk,
      message: "Asteroid ice content #{ice_percentage}% exceeds 20% — requires Thermal Shielding sub-phase"
    }
  end
  
  # Passed all checks
  { option: option, integrity_verified: true }
end
```

### Step 2 — Add integrity check to SuperMarsSettlementService

In `galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb`, add integrity check in `moonless_planet_pattern`:

```ruby
def moonless_planet_pattern(planet)
  asteroids = find_nearby_asteroids(planet)
  tug_craft = available_tug_craft(planet)
  redirected = []
  
  asteroids.each do |asteroid|
    # INTEGRITY CHECK: Skip asteroids without survey data or low integrity
    next unless asteroid_integrity_ok?(asteroid)
    
    if phobos_deimos_sized?(asteroid) && tug_craft.any?
      redirected << redirect_asteroid(asteroid, planet, tug_craft.pop)
    end
  end
  
  redirected
end

private

def asteroid_integrity_ok?(asteroid)
  integrity_score = asteroid['structural_integrity_score'] || asteroid[:structural_integrity_score]
  
  # No survey data — allow but log warning (survey may not have run yet)
  return true if integrity_score.nil?
  
  # Low integrity — skip this asteroid
  if integrity_score < 0.5
    puts "[SuperMarsSettlementService] Skipping asteroid #{asteroid['name'] || 'unknown'}: integrity #{integrity_score} below threshold" if defined?(Rails)
    return false
  end
  
  true
end
```

### Step 3 — Synthesis Report (before committing)

```
SYNTHESIS REPORT
Files modified:
  - galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb — added check_asteroid_integrity method + integrity gate in analyze_construction_option
  - galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb — added asteroid_integrity_ok? method + integrity check in moonless_planet_pattern

Thresholds applied:
  ✅ Integrity < 0.5 → rejected for Eden AWS Anchor (analyzer) / skipped (SuperMars)
  ✅ Ice > 20% → thermal risk warning (analyzer only)
  ✅ No survey data → allowed with warning (graceful degradation)

Analyzer integration:
  [specify: integrity gate integrated with existing rejection pattern]

SuperMars integration:
  [specify: integrity check added before redirect_asteroid call]

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `StationCostBenefitAnalyzer` rejects asteroids with `integrity_score < 0.5` for Eden AWS Anchor
- [ ] `StationCostBenefitAnalyzer` flags thermal risk (ice > 20%) as warning
- [ ] `SuperMarsSettlementService` skips low-integrity asteroids before redirecting to Mars
- [ ] Missing survey data is handled gracefully (no crash, warning logged)
- [ ] No existing specs broken by changes
- [ ] Integrity gate uses exact thresholds from `asteroid_conversion_physics.md`

---

## Stop Conditions — escalate to user immediately if:
- Analyzer's rejection pattern is not obvious — do not invent a new one
- SuperMarsSettlementService asteroid data structure differs from hash format
- Same failure persists after two attempts
- Any architectural decision is required beyond what the reference docs cover

---

## Commit Instructions

Run git commands on **host**, not inside container:

```bash
git add galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb
git add galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb
git commit -m "feature: asteroid integrity gate — add cohesion/thermal checks to analyzer and SuperMars service"
git push
```

---

## Documentation
- [ ] No additional doc changes needed — reference docs already cover this feature

---

## Dependencies
**Blocked by**: Task B (Seismic Survey Mode) — needs survey data before gate can be wired in
**Blocks**: Phase 9a Mars moon conversions, Phase 10d Venus asteroid capture
**Related tasks**: 
- `2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md` — companion task (scout ship vessel)
- `2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md` — companion task (survey mode that creates the data)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb` — added integrity gate for Eden AWS Anchor
- `galaxy_game/app/services/ai_manager/super_mars_settlement_service.rb` — added integrity check before asteroid redirection

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: Integrity gate wired into analyzer + SuperMars | Low-integrity asteroids rejected | Thermal risk flagged