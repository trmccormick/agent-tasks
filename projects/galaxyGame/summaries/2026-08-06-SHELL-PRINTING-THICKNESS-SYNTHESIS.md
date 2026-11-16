# Synthesis: Shell Printing Thickness Uses Construction-Time Shielding

**Task**: 2026-08-03-HIGH-BUGFIX-SHELL-PRINTING-THICKNESS-USes-CONSTRUCTION-TIME-SHIELDING
**Date**: 2026-08-06
**Status**: Ready to implement

---

## Summary

`Manufacturing::ShellPrintingService#calculate_target_thickness` currently uses atmosphere pressure buckets only (0→150mm, 0-1→140mm, 1-10→130mm, 10-50→110mm, 50+→80mm). The fix adds magnetosphere presence as a second input to produce a more nuanced thickness value, and enforces a minimum thickness floor of 100mm.

## Implementation Plan

### 1. Update `calculate_target_thickness` in `shell_printing_service.rb`

**Changes**:
- Add constant `MINIMUM_SHELL_THICKNESS_MM = 100.0` at class top
- After computing the pressure-based thickness, check `celestial_body.has_magnetosphere` (boolean)
- If magnetosphere present: reduce thickness by 20mm (strong shielding benefit)
- Apply minimum floor clamp: `thickness.clamp(MINIMUM_SHELL_THICKNESS_MM, Float::INFINITY)`
- Add comments explaining the two-input formula

**Formula**:
```
pressure_bucket = case pressure ... end  # existing buckets
shielding_bonus = celestial_body.has_magnetosphere ? -20.0 : 0.0
thickness = [pressure_bucket + shielding_bonus, MINIMUM_SHELL_THICKNESS_MM].max
```

### 2. Update specs in `shell_printing_service_spec.rb`

**Changes**:
- Un-skip the three pending xit tests (lines 127, 142, 155) — they don't actually test thickness logic, just job creation/materials
- Add new test group "target_thickness_mm" with:
  - `it 'uses magnetosphere presence to reduce thickness'` — body with magnetosphere gets 20mm less than without
  - `it 'enforces minimum thickness floor of 100mm'` — even with magnetosphere on airless world, thickness >= 100mm
  - `it 'persists target_thickness_mm at build time'` — verify ConstructionJob.target_thickness_mm is set

### 3. Verify factory alignment

**Current**: shell_printing trait uses `target_thickness_mm { 120.0 }`
**New range**: 80-130mm (with magnetosphere) or 100-150mm (without)
**Decision**: Factory default 120.0 is within the expected range — no change needed.

## Files to Edit

| File | Action |
|---|---|
| `galaxy_game/app/services/manufacturing/shell_printing_service.rb` | Add MINIMUM_SHELL_THICKNESS_MM constant, update calculate_target_thickness |
| `galaxy_game/spec/services/manufacturing/shell_printing_service_spec.rb` | Un-skip 3 xit tests, add new test group for thickness logic |

## Risks & Mitigations

- **Risk**: `has_magnetosphere` may not exist on all celestial body types.
  **Mitigation**: Use `respond_to?` guard: `celestial_body.respond_to?(:has_magnetosphere) && celestial_body.has_magnetosphere`
- **Risk**: Factory default (120mm) may not match new formula output for test fixtures.
  **Mitigation**: Factory is only used for generic jobs; spec tests will set explicit thickness values where needed.

## Verification Steps

1. Run shell_printing_service_spec.rb — all examples pass
2. Verify no lava-tube behavior altered (no changes to lava tube code paths)
3. Verify no schema change needed (confirmed: target_thickness_mm column exists)
