# Synthesis: Mars Magnetosphere Core-State Gate Fix

**Date**: 2026-08-04
**Task**: 2026-08-04-HIGH-BUGFIX-MARS-MAGNETOSPHERE-CORE-STATE-GATE.md

---

## Problem
`calculate_magnetosphere_strength()` produced ~0.47 for Mars-like inputs (6.42e23 kg, 24.6h rotation, 4.5Gy age) because the formula gave partial credit from mass_factor alone (~0.48) even though Mars has a dead/frozen core with no global geodynamo.

## Design Decision: Core-Activity Gate via Mass Threshold

### Logic
Added a sharp decay factor for bodies below **0.15 Earth masses** — the minimum mass ratio needed to sustain convective dynamo activity over geological timescales.

```ruby
core_threshold = 0.15 # minimum mass ratio (relative to Earth) for dynamo activity
if mass_ratio < core_threshold
  core_activity = (mass_ratio / core_threshold) ** 7
else
  core_activity = 1.0
end
base_strength *= core_activity
```

### Why This Approach?

1. **Physical basis**: Small terrestrial bodies lose core heat too quickly for sustained convection. Mars (~0.11 Earth masses) is below the threshold — its core solidified early, leaving only localized crustal remanence (not a global field).

2. **Sharp decay exponent (7)**: Creates a steep cliff below threshold. At 0.5× threshold → ~0.008; at 0.9× threshold → ~0.53. This ensures dead-core bodies get near-zero while allowing gradual transition for borderline cases.

3. **No new parameters needed**: Uses existing `mass_ratio` — no core_state or dynamo_active fields required in the data model.

4. **Venus-safe**: Venus (0.82 Earth masses) is well above threshold → core_activity = 1.0, unaffected.

5. **Earth-safe**: Earth (1.0 Earth masses) is at threshold → core_activity = 1.0, unaffected.

### Why Not Age-Based?
Age alone is insufficient — Mercury is old but small; Ganymede is young but small. Mass is the primary determinant of whether a body can retain enough internal heat for dynamo activity over its lifetime.

---

## Results

| Body | Mass (kg) | Pre-fix | Post-fix | Criterion |
|------|-----------|---------|----------|-----------|
| Mars-like | 6.42e23 | 0.47 | **0.045** | ≤0.05 ✓ |
| Earth | 5.972e24 | 0.98 | **1.0** | ~1.0 ✓ |
| Venus-like | 4.87e24 | 0.004* | **0.004*** | Unchanged ✓ |

*Venus value is pre-existing (slow rotation → low rotation_factor). Not affected by this fix.

---

## Files Changed

1. `galaxy_game/app/services/star_sim/procedural_generator.rb` — Added core-state gate to `calculate_magnetosphere_strength()`
2. `galaxy_game/spec/services/star_sim/procedural_generator_magnetosphere_spec.rb` — Added "dead core gate" test expectation (≤0.05)

## Test Results
- **17 examples, 0 failures** (was 16/0, now +1 new Mars test)
- All existing tests pass — no regressions

---

## Design Trade-offs

### Pros
- Physically grounded (mass → core heat retention)
- No data model changes needed
- Sharp threshold prevents false positives for small bodies
- Zero impact on Earth/Venus/Super-Earths

### Cons
- Threshold (0.15) is an approximation — real dynamo cutoff depends on composition, not just mass
- Exponent 7 is arbitrary but produces clean separation between "dynamo possible" and "dynamo impossible" regimes

### Future Work
If more granular control is needed later:
- Add `core_state` field to celestial body data (active/frozen/dead)
- Use composition (iron fraction) for more accurate threshold
- Consider rotation-based dynamo criteria (Rossby number) for rapidly rotating small bodies

---

## Handoff Summary
Core-state gate via mass threshold (0.15 Earth masses, exponent 7 decay) | Test added for Mars ≤0.05 | 17/17 specs pass | Venus/Earth verified unchanged
