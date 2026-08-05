---
status: active
priority: HIGH
type: feature
system_domain: AI_MANAGER_LUNA_SETTLEMENT
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
last_updated: 2026-08-05
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-04-HIGH-FEATURE-WORLD-AGNOSTIC-HABITABILITY-EVALUATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-04-HIGH-FEATURE-WORLD-AGNOSTIC-HABITABILITY-EVALUATION.md \
         projects/galaxy_game/tasks/active/2026-08-04-HIGH-FEATURE-WORLD-AGNOSTIC-HABITABILITY-EVALUATION.md
  Then open the moved file and change: status: active → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-04-HIGH-FEATURE-WORLD-AGNOSTIC-HABITABILITY-EVALUATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Replace calculate_habitability with world-agnostic, stage-gate evaluation matrix

**Status**: ACTIVE | **Priority**: HIGH | **Type**: feature
**Created**: 2026-08-04 | **Last Updated**: 2026-08-04

## Problem

The current `calculate_habitability` method in `galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb` uses hardcoded Mars-specific thresholds (e.g., absolute temperature values like 310K, O2 at 21%, pressure at 2.0 bar). This breaks the **Universal Planetary Terraforming SOP** which requires all evaluation to be **world-agnostic** — every celestial body in the game must use the same formula with thresholds relative to its own ambient conditions.

The old task (`2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md`) prescribed exact code with arbitrary Mars-specific numbers. This new task defines the **framework** — let the agent determine reasonable thresholds based on actual game data and the SOP's stage-gate logic.

## Reference

See `docs/gameplay/PLANETARY_TERRAFORMING_SOP.md` for the full 5-phase framework that this method supports.

## Implementation Requirements

### Replace `calculate_habitability` in `galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb`

The new method must evaluate habitability using a **weighted factor matrix** with these components:

1. **Oxygen factor** (weight: 30%) — suitability relative to the body's ambient O2 level
2. **Temperature factor** (weight: 30%) — suitability relative to surface temperature, using the body's ambient as baseline
3. **Liquid water factor** (weight: 25%) — derived from hydrosphere `state_distribution['liquid']`
4. **Pressure factor** (weight: 15%) — suitability relative to the body's ambient pressure
5. **Life presence bonus** (up to +10%) — bootstrapping effect when life already exists on the body

### Key Design Constraints

- **NO absolute thresholds** — all ranges must be expressed as deltas or ratios relative to the celestial body's own ambient conditions
- **World-agnostic** — same formula works for Mars, Eden, System B, or any procedurally generated exoplanet
- **Stage-gate compatible** — the output feeds into SOP Phase 3→Phase 4 gating (liquid water trigger) and Bio-Nursery Dome adaptation progress
- **Graceful degradation** — if hydrosphere data is missing or incomplete, fall back to reasonable defaults without crashing

### Implementation Approach

1. Read the celestial body's ambient conditions (temperature, pressure, O2 level) as the baseline
2. Express each factor's suitability range relative to that baseline (e.g., "optimal temperature = ambient ± 30K" rather than "optimal = 310K")
3. Use `state_distribution['liquid']` for water factor — this is already available on the hydrosphere model
4. Apply life presence bonus based on existing life forms' population and diversity

## Prerequisites

1. **Verify hydrosphere has `state_distribution`**: Check that `CelestialBodies::Hydrosphere` model has a `state_distribution` JSONB column with a `'liquid'` key.
2. **Check existing callers**: Search for all callers of `calculate_habitability`:
   ```bash
   grep -rn "calculate_habitability" galaxy_game/app/
   ```
   Ensure the new formula won't break dependent logic.
3. **Review ambient data on celestial bodies**: Check what ambient temperature, pressure, and O2 data is available on the body model to use as baseline references.

## Implementation Steps

1. Replace `calculate_habitability` in `galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb` with world-agnostic formula
2. Update specs in `galaxy_game/spec/models/celestial_bodies/spheres/biosphere_spec.rb`:
   - Add examples for each factor using relative thresholds
   - Add examples for life presence bonus
   - Test graceful degradation when hydrosphere data is missing
3. Run full biosphere spec suite:
   ```bash
   docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/models/celestial_bodies/spheres/biosphere_spec.rb --format documentation
   ```

## Acceptance Criteria

- [ ] `calculate_habitability` uses 5-component weighted matrix (O2 30%, temp 30%, water 25%, pressure 15%, life bonus up to 10%)
- [ ] All thresholds are relative to the celestial body's ambient conditions — no hardcoded absolute values
- [ ] Liquid water factor derives from hydrosphere `state_distribution['liquid']`
- [ ] Life presence bonus applies when existing life forms have sufficient population/diversity
- [ ] Graceful fallback when hydrosphere data is missing (no crashes, reasonable default)
- [ ] All biosphere specs pass (0 failures, 0 errors)
- [ ] No regressions in dependent services (callers identified in prerequisites)

## Risks & Gotchas

- **Breaking change**: Existing callers may get different habitability values — verify with integration tests
- **Hydrosphere dependency**: If `hydrosphere.state_distribution` doesn't have a `'liquid'` key, fall back to 0.0 — document this behavior
- **Baseline data quality**: The formula depends on accurate ambient data from the celestial body model — ensure it's populated during generation

## Notes

- This is a feature enhancement aligned with the Universal Planetary Terraforming SOP
- The old task (`2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md`) prescribed exact code with arbitrary Mars-specific thresholds — superseded by this framework-first approach
- See `docs/gameplay/PLANETARY_TERRAFORMING_SOP.md` for the broader context

## Supersedes

- `2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md` (deprecated — prescribed exact code instead of defining framework)
