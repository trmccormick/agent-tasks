---
status: active
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

# TASK: Implement Spatial Boundary Validation in UniverseRegistrationJob

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md \
         projects/galaxy_game/tasks/active/2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

`StarSim::SystemGeneratorService#fallback_build` and `StarSim::ProceduralGenerator` generate planetary bodies and assign orbital distances without validating against the macro spatial boundaries defined in `GameConstants`. A planet or asteroid can be placed outside valid bounds and persisted silently. `UniverseRegistrationJob` is the mandated gating layer but currently has no boundary validation.

**Relevant Architecture Docs** — read before starting:
- `docs/GUARDRAILS.md` — wormhole and spatial boundary rules
- `docs/wiki/System-Blueprints.md` — Section 3 (The Validation Gap) and Section 6 (Gap Tracking)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: AU vs meters unit mismatch**
- ❌ Wrong: Compare `semi_major_axis_au` directly against `GameConstants::SAFE_DISTANCE_FROM_STAR` or `MAX_DISTANCE_FROM_STAR`
- ✅ Right: Convert first — `distance_m = semi_major_axis_au * 1.496e11`, then compare against meter constants
- Why: Seed stores orbital distances in AU; GameConstants are in meters. Direct comparison produces silent false negatives.

⚠️ **GOTCHA 2: Seed hash key type**
- ❌ Wrong: Assume symbol keys without checking
- ✅ Right: Check if seed uses string or symbol keys — adjust `dig` calls accordingly
- Why: If the fix requires changes beyond this file, stop and flag it.

---

## Problem Statement

**Current behavior**: `SystemGeneratorService#fallback_build` creates celestial bodies with no orbital distance validation. `UniverseRegistrationJob` writes system seeds to `SYSTEMS_PATH` without checking spatial boundaries or wormhole cap.

**Expected behavior**: `UniverseRegistrationJob` raises `InvalidSystemBoundariesError` before any write when a seed contains a body outside the valid orbital band or exceeds `MAX_WORMHOLES_PER_SYSTEM`.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/jobs/universe_registration_job.rb` | Registration gating layer | `#perform` |
| `spec/jobs/universe_registration_job_spec.rb` | Job spec | create if does not exist |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `star_sim/system_generator_service.rb` | Shows fallback_build structure and seed format |
| `star_sim/procedural_generator.rb` | Shows seed hash structure including orbits array |
| `config/initializers/game_constants.rb` | Source of truth for boundary constants |
| `docs/wiki/System-Blueprints.md` | Full spec of mandated validation behaviour |

### Migration (if needed)
- [ ] No migration needed

---

## Implementation Steps

### Step 1 — Add exception class and validation method to UniverseRegistrationJob

```ruby
# In app/jobs/universe_registration_job.rb

class InvalidSystemBoundariesError < StandardError; end

def validate_system_envelope!(system_seed)
  if system_seed[:wormholes]&.count.to_i > GameConstants::MAX_WORMHOLES_PER_SYSTEM
    raise InvalidSystemBoundariesError,
      "Wormhole count #{system_seed[:wormholes].count} exceeds structural cap of #{GameConstants::MAX_WORMHOLES_PER_SYSTEM}"
  end

  system_seed[:planets]&.each do |planet|
    au = planet.dig(:orbits, 0, :semi_major_axis_au).to_f
    distance_m = au * 1.496e11

    if distance_m < GameConstants::SAFE_DISTANCE_FROM_STAR
      raise InvalidSystemBoundariesError,
        "'#{planet[:name]}' at #{au} AU is inside inner boundary (#{GameConstants::SAFE_DISTANCE_FROM_STAR} m)"
    end

    if distance_m > GameConstants::MAX_DISTANCE_FROM_STAR
      raise InvalidSystemBoundariesError,
        "'#{planet[:name]}' at #{au} AU exceeds outer boundary (#{GameConstants::MAX_DISTANCE_FROM_STAR} m)"
    end
  end
end
```

### Step 2 — Call validate_system_envelope! at top of perform

```ruby
def perform(system_seed)
  validate_system_envelope!(system_seed)
  # ... rest of existing perform logic
end
```

### Step 3 — Write specs

```ruby
# spec/jobs/universe_registration_job_spec.rb

RSpec.describe UniverseRegistrationJob, type: :job do
  describe '#validate_system_envelope!' do
    let(:valid_planet) do
      { name: 'Test Planet', orbits: [{ semi_major_axis_au: 1.5 }] }
    end

    it 'raises when planet is inside inner boundary (< 1 AU)' do
      seed = { planets: [{ name: 'Too Close', orbits: [{ semi_major_axis_au: 0.5 }] }], wormholes: [] }
      expect { subject.send(:validate_system_envelope!, seed) }
        .to raise_error(InvalidSystemBoundariesError, /inside inner boundary/)
    end

    it 'raises when planet exceeds outer boundary (> 100 AU)' do
      seed = { planets: [{ name: 'Too Far', orbits: [{ semi_major_axis_au: 150.0 }] }], wormholes: [] }
      expect { subject.send(:validate_system_envelope!, seed) }
        .to raise_error(InvalidSystemBoundariesError, /exceeds outer boundary/)
    end

    it 'raises when wormhole count exceeds MAX_WORMHOLES_PER_SYSTEM' do
      seed = { planets: [valid_planet], wormholes: [{}, {}, {}, {}] }
      expect { subject.send(:validate_system_envelope!, seed) }
        .to raise_error(InvalidSystemBoundariesError, /Wormhole count/)
    end

    it 'does not raise for a valid seed within all bounds' do
      seed = { planets: [valid_planet], wormholes: [{}] }
      expect { subject.send(:validate_system_envelope!, seed) }.not_to raise_error
    end

    it 'does not raise for a seed with no wormholes' do
      seed = { planets: [valid_planet], wormholes: [] }
      expect { subject.send(:validate_system_envelope!, seed) }.not_to raise_error
    end
  end
end
```

### Step 4 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/jobs/universe_registration_job_spec.rb 2>&1 | tail -20'
```

Expected result: 5 examples, 0 failures

---

## Acceptance Criteria
- [ ] `InvalidSystemBoundariesError` defined in `UniverseRegistrationJob`
- [ ] `validate_system_envelope!` called at top of `perform` before any write
- [ ] All 5 spec cases pass
- [ ] AU-to-meters conversion explicit — no direct AU vs meters comparison
- [ ] No regressions in existing generation specs
- [ ] `docs/wiki/System-Blueprints.md` gap tracker item for boundary validation updated to resolved

---

## Stop Conditions — escalate to user immediately if:
- `perform` method signature differs from expected — do not guess, flag it
- Seed hash uses string keys instead of symbol keys — adjust validation method accordingly and note in completion report
- Fix requires changes to `SystemGeneratorService` or `ProceduralGenerator` beyond what is specified here

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add app/jobs/universe_registration_job.rb spec/jobs/universe_registration_job_spec.rb
git commit -m "bug-fix: universe_registration_job — add spatial boundary validation and InvalidSystemBoundariesError"
git push
```

**Task file move on completion:**
```bash
mv projects/galaxy_game/tasks/active/2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md \
   projects/galaxy_game/tasks/completed/2026-08/2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md
git add projects/galaxy_game/tasks/completed/2026-08/2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md
git commit -m "chore: move 2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md to completed/"
```

---

## Documentation
- [ ] Update `docs/wiki/System-Blueprints.md` — mark gap tracker item resolved for boundary validation

---

## Dependencies
**Blocked by**: none — requires only test environment access
**Blocks**: none
**Related tasks**: `2026-05-23-MEDIUM-DOCUMENTATION-PLANET-TEMPLATE-LOADER-VERIFICATION.md`

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | spatial boundary gate implemented | next: expand fallback_build planet type coverage
```

---

## Documentation
- [ ] Update `docs/wiki/System-Blueprints.md` — mark gap tracker item resolved for boundary validation

---

## Dependencies
**Blocked by**: none — requires only test environment access
**Blocks**: none
**Related tasks**: `2026-05-23-MEDIUM-DOCUMENTATION-PLANET-TEMPLATE-LOADER-VERIFICATION.md`

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | spatial boundary gate implemented | next: expand fallback_build planet type coverage
