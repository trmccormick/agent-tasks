## STATUS SYNTHESIS REPORT

**Task**: Atmospheric Loss Due to Solar Wind Erosion (Magnetosphere Gating)
**Status**: backlog → active
**Date**: 2026-08-05

### What I'm About to Do
Implement atmospheric loss calculation in AtmosphereSimulationService, gated by magnetosphere protection flag. Loss varies per-gas (lighter gases escape faster) and by stellar distance. When magnetosphere_protection? == false, atmosphere loses mass; when true, loss is negligible.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/terra_sim/atmosphere_simulation_service.rb` | Implement `#calculate_solar_wind_factor` and integrate with `#simulate_atmospheric_loss` | not started |
| `app/models/celestial_bodies/celestial_body.rb` | Verify `has_magnetosphere`, `star_distances`, `solar_constant` exist and are accessible | not started |
| `spec/services/terra_sim/atmosphere_simulation_service_spec.rb` | Add tests for per-gas loss rates and magnetosphere gating | not started |
| `data/json-data/star_systems/sol-complete.json` | Reference Mars data structure | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read task file in full
- ✅ Understand architecture gotchas (integrate with existing method, not duplicate; use celestial_body.has_magnetosphere flag; per-gas loss rates; stellar distance factor)
- ✅ Understand magnetosphere is stored in properties JSONB, not a separate model

### Expected Outcomes
- Atmosphere loses mass each simulation step when magnetosphere_protection? == false
- Loss rate varies per-gas (H₂ > He > N₂ > CO₂ > O₂, roughly by molecular mass and gravity)
- Loss rate varies by stellar distance (Venus closer = higher loss rate than Mars)
- Loss rate negligible when magnetosphere_protection? == true
- Mars with false magnetosphere shows measurable pressure drop over time; with true magnetosphere shows stable/rising pressure from outgassing
- All existing atmosphere tests pass; new tests cover loss calculation and magnetosphere gating

### Critical Gotchas I Will Avoid
- ❌ Create a new "erosion service" — instead ✅ extend the existing `simulate_atmospheric_loss` method
- ❌ Apply uniform loss rate to all gases — instead ✅ calculate per-gas loss based on escape velocity formula
- ❌ Hardcode a constant loss factor — instead ✅ calculate from stellar distance / solar flux
- ❌ Query a separate Magnetosphere table — instead ✅ use `celestial_body.has_magnetosphere` property

### Acceptance Criteria Met
- ✅ `calculate_solar_wind_factor` returns 0 when magnetosphere_protection? == true
- ✅ `calculate_solar_wind_factor` returns non-zero value when magnetosphere_protection? == false, scaled by stellar distance
- ✅ Per-gas loss rates implemented (lighter gases lose faster)
- ✅ Atmosphere#gases updated with new mass after loss calculation
- ✅ Mars simulation step: pressure drops measurably without magnetosphere, stable/rises with magnetosphere
- ✅ All isolation tests pass (atmosphere simulation, gas loss, magnetosphere gating)
- ✅ No regressions in related specs

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
