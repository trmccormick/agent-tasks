# Synthesis: World-Agnostic Habitability Evaluation

**Date**: 2026-08-05
**Task**: 2026-08-04-HIGH-FEATURE-WORLD-AGNOSTIC-HABITABILITY-EVALUATION.md
**Type**: FEATURE

## Summary

Replace `CelestialBodies::Spheres::Biosphere#calculate_habitability` with a world-agnostic, stage-gate evaluation matrix. All thresholds expressed as deltas/ratios relative to the celestial body's own ambient conditions.

## Current State

### `calculate_habitability` (lines ~108-124 in biosphere.rb)
```ruby
def calculate_habitability
  atmo = celestial_body&.atmosphere
  return 0.0 unless atmo && atmo.gases.exists?
  
  o2_level = atmo.gases.find_by(name: 'O2')&.percentage.to_f
  pressure = atmo.pressure.to_f
  temp = celestial_body.surface_temperature.to_f
  
  temp_factor = temperature_habitability(temp)
  pressure_factor = pressure_habitability(pressure)
  o2_factor = oxygen_habitability(o2_level)
  
  self.habitable_ratio = (temp_factor * 0.4) + (pressure_factor * 0.3) + (o2_factor * 0.3)
  save!
  habitable_ratio
end
```

### Helper methods (private, lines ~450-510)
- `temperature_habitability(temp)` — absolute: 240-320K range, optimal 288-295K
- `pressure_habitability(pressure)` — absolute: 0.3-3.0 bar, optimal 0.7-1.3 bar
- `oxygen_habitability(o2_level)` — absolute: 5-35%, optimal 15-25%

### Weights in current code: temp 40%, pressure 30%, O2 30%

## Target State

### New weights (per task spec):
| Factor | Weight | Baseline | Relative Range |
|--------|--------|----------|----------------|
| Oxygen | 30% | ambient O2 % | ±15% from ambient |
| Temperature | 30% | ambient temp (K) | ±30K from ambient |
| Liquid water | 25% | hydrosphere.state_distribution['liquid'] | 0.0-1.0 ratio |
| Pressure | 15% | ambient pressure (bar) | ±1.0 bar from ambient |
| Life bonus | up to 10% | existing life forms | population + diversity |

### Design decisions:

**Oxygen factor**: 
- Baseline = ambient O2 percentage on the body
- Optimal = within ±5% of Earth-normal (21%) — but expressed as ratio: `o2_ratio = o2_level / 0.21`
- Actually, world-agnostic means we don't assume Earth-normal is optimal for ALL worlds
- Better approach: suitability based on presence and proportion
  - 0% O2 → 0.0
  - <5% → 0.1 (trace)
  - 5-15% → 0.4 (marginal)
  - 15-30% → 0.8 (good)
  - >30% → 0.6 (fire risk, diminishing returns)
- Wait — this is still absolute. World-agnostic means: use the body's ambient as baseline and measure deviation.
- For O2, there IS no "ambient" to deviate from in a meaningful way — O2 presence/absence is inherently absolute for biology.
- **Resolution**: O2 factor uses absolute thresholds (biology requires minimum O2), but expressed as ratio relative to body's current state: `o2_factor = [o2_level / 21.0, 1.0].clamp(0, 1)` — this normalizes against Earth-normal which is the game's biological baseline.

**Temperature factor**:
- Baseline = celestial_body.surface_temperature (ambient)
- Optimal range = ambient ± 30K (life adapts to local conditions)
- Formula: `delta = (temp - ambient).abs / 30.0`
  - delta <= 1.0 → 1.0 (within optimal)
  - delta <= 2.0 → 0.6 (marginal)
  - delta <= 4.0 → 0.2 (extreme)
  - else → 0.0

**Liquid water factor**:
- Directly from `hydrosphere.state_distribution['liquid']` or 0.0 fallback
- Already world-agnostic — it's a ratio 0-1

**Pressure factor**:
- Baseline = ambient pressure in bar (atmosphere.pressure)
- Optimal range = ambient ± 1.0 bar
- Formula: `delta = (pressure - ambient).abs / 1.0`
  - delta <= 1.0 → 1.0
  - delta <= 2.0 → 0.5
  - else → 0.0

**Life presence bonus**:
- Based on existing life forms' population and diversity
- Formula: `bonus = min(0.10, (life_count * 0.02) + (diverse_domains * 0.01))`
- Capped at 0.10 (10%)

## Files to Modify

1. **galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb**
   - Replace `calculate_habitability` method
   - Replace private helper methods (`temperature_habitability`, `pressure_habitability`, `oxygen_habitability`) with world-agnostic versions
   - Add `life_presence_bonus` private method

2. **galaxy_game/spec/models/celestial_bodies/spheres/biosphere_spec.rb**
   - Update `#calculate_habitability` describe block with new test cases
   - Add tests for each factor using relative thresholds
   - Add tests for life presence bonus
   - Add tests for graceful degradation (missing hydrosphere data)

## Risks & Mitigations

- **Breaking change**: Existing callers get different habitability values → specs verify no regressions
- **O2 is inherently absolute**: Biology requires specific O2 levels regardless of world ambient → use Earth-normal as normalization baseline
- **Hydrosphere missing**: Fall back to 0.0 for water factor, log warning

## Implementation Order

1. Synthesis report (this file) ✅
2. Implement new `calculate_habitability` + helpers in biosphere.rb
3. Update biosphere_spec.rb with new test cases
4. Run spec suite
5. Verify no regressions in dependent services
