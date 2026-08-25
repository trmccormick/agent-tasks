# Research: Hydrosphere Composition Schema Consistency

**Date**: 2026-08-24
**Task**: 2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md
**Status**: Research in progress

---

## STEP 1 — INVENTORY: Every `hydrosphere_attributes.composition` block

### sol-complete.json (20 bodies with hydrosphere)

| # | Body | Line | Composition Shape | Value |
|---|------|------|-------------------|-------|
| 1 | Venus | ~362 | **Flat object** | `{"H2SO4": 85.0, "H2O": 0.1}` |
| 2 | Earth | ~476 | **Array of objects** | `[{"compound":"H2O","percentage":96.5},{"compound":"dissolved_salts","percentage":3.5}]` |
| 3 | Uranus | ~709 | **Flat object** | `{"H2O": 50.0, "NH3": 25.0, "CH4": 25.0}` |
| 4 | Neptune | ~981 | **Flat object** | `{"H2O": 60.0, "NH3": 20.0, "CH4": 20.0}` |
| 5 | Europa | ~1138 | **Flat object** | `{"H2O": 100.0}` |
| 6 | Callisto | ~1198 | **Flat object** | `{"H2O": 100.0}` |
| 7 | Enceladus | ~1435 | **Flat object** | `{"H2O": 100.0}` |
| 8 | Titan | ~1530 | **Flat object** | `{"CH4": 50.0, "C2H6": 30.0, "N2": 20.0}` |
| 9 | Ganymede | ~1637 | **Flat object** | `{"H2O": 100.0}` |
| 10 | Rhea | ~1768 | **Flat object** | `{"H2O": 100.0}` |
| 11 | Mimas | ~1821 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 12 | Triton | ~1877 | **Flat object** | `{"H2O": 100.0}` |
| 13 | Eris | ~1976 | **Flat object** | `{"H2O": 90.0, "N2": 10.0}` |
| 14 | Charon | ~2032 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 15 | Makemake | ~2119 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 16 | Gonggong | ~2168 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 17 | Quaoar | ~2216 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 18 | Sedna | ~2264 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 19 | Vesta | ~2312 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 20 | Ceres | ~2360 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |

### sol.json (10 bodies with hydrosphere)

| # | Body | Line | Composition Shape | Value |
|---|------|------|-------------------|-------|
| 1 | Venus | ~173 | **Flat object** | `{"H2SO4": 85.0, "H2O": 0.1}` |
| 2 | Earth | ~267 | **Array of objects** | `[{"compound":"H2O","percentage":96.5},{"compound":"dissolved_salts","percentage":3.5}]` |
| 3 | Mars | ~364 | **Flat object** | `{"H2O": 98.0, "salts": 2.0}` |
| 4 | Titan | ~542 | **Flat object** | `{"CH4": 50.0, "C2H6": 30.0, "N2": 20.0}` |
| 5 | Ganymede | ~706 | **Flat object** | `{"H2O": 50.0, "NH3": 25.0, "CH4": 25.0}` |
| 6 | Callisto | ~741 | **Flat object** | `{"H2O": 60.0, "NH3": 20.0, "CH4": 20.0}` |
| 7 | Europa | ~779 | **Flat object** | `{"H2O": 100.0}` |
| 8 | Vesta | ~819 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 9 | Psyche | ~860 | **Empty object `{}`** | (total_hydrosphere_mass: 0) |
| 10 | Pluto | ~898 | **Flat object** | `{"H2O": 90.0, "N2": 10.0}` |

### Other star system files checked
- epsilon_eridani.json, tau_ceti.json: No `composition` field found (grep returned empty)
- Only sol-complete.json and sol.json carry hydrosphere composition data in the codebase

### Summary of shapes across both files
1. **Flat object** (compound → percentage): 16/20 in sol-complete, 8/10 in sol — **dominant shape**
2. **Array of objects** (`[{compound, percentage}]`): 1/20 in sol-complete (Earth), 1/10 in sol (Earth) — **only on Earth**
3. **Empty object** `{}`: bodies with no hydrosphere (total_hydrosphere_mass: 0)

### Third variant found? No.
Only two non-empty shapes exist: flat object and array-of-objects. The task's original claim of "two different shapes" is confirmed, but the scope is narrower than feared — only Earth uses the array format; every other body uses flat object.

---

## STEP 2 — READER ANALYSIS

### Reader #1: `system_builder_service.rb` (Ruby/Rails) — **HAS NORMALIZER**
- File: `galaxy_game/app/services/star_sim/system_builder_service.rb`, lines 537-542
- **This is the critical finding**: The `create_hydrosphere` method explicitly normalizes array-of-objects to flat object at load time.
```ruby
if hydrosphere_attrs[:composition].is_a?(Array)
  transformed_composition = {}
  hydrosphere_attrs[:composition].each do |item|
    transformed_composition[item[:compound].to_s] = item[:percentage]
  end
  hydrosphere_attrs[:composition] = transformed_composition
end
```
- **Result**: At runtime, all bodies get flat object shape. The array format is silently converted.

### Reader #2: `solid_body_concern.rb` (Ruby/Rails) — assumes Hash
- File: `galaxy_game/app/models/concerns/solid_body_concern.rb`, lines 70-72
```ruby
if hydrosphere.composition.present? && hydrosphere.composition.is_a?(Hash)
  dominant = hydrosphere.composition.max_by { |liquid, percentage| percentage.to_f }
```
- Only handles Hash (flat object). Would return nil for array format.

### Reader #3: `rocky_body_service.rb` (Ruby/Rails) — assumes Hash
- File: `galaxy_game/app/services/rocky_body_service.rb`, line 314
```ruby
body.hydrosphere.composition.max_by { |liquid, percentage| percentage.to_f }&.first
```
- Only handles Hash. Would error on array format.

### Reader #4: `ocean_planet.rb` (Ruby/Rails) — assumes Hash with specific keys
- File: `galaxy_game/app/models/celestial_bodies/planets/ocean/ocean_planet.rb`, lines 90-92
```ruby
composition = hydrosphere.composition
if composition["salts"].to_f > 10
```
- Only handles Hash. Would return 0 for array format (no "salts" key).

### Reader #5: `monitor.js` (JavaScript/Frontend) — assumes Hash
- File: `galaxy_game/app/javascript/admin/monitor.js`, lines 865-870
```javascript
if (planetData.hydrosphere && planetData.hydrosphere.composition) {
  const comp = planetData.hydrosphere.composition;
  primaryLiquid = Object.keys(comp).reduce(
    (a, b) => comp[a] > comp[b] ? a : b,
    'H2O'
  );
}
```
- Only handles Hash. Would return first key for array format (incorrect behavior).

### DECISIONS.md check
- No documented convention for hydrosphere composition schema found in `docs/new_agent/rules/DECISIONS.md`

---

## SYNTHESIS REPORT

Bodies inventoried: 30 total across sol-complete.json (20) and sol.json (10)

Reader found: YES — multiple readers exist
- Normalizer: `system_builder_service.rb:537-542` (converts array → flat object at load time)
- Consumers: `solid_body_concern.rb`, `rocky_body_service.rb`, `ocean_planet.rb`, `monitor.js` — all assume Hash

Reader behavior: **The normalizer handles both shapes correctly.** At runtime, the system_builder_service converts array-of-objects to flat object before building the hydrosphere. All consumers then see only flat object shape. The inconsistency in the JSON files is **not a bug** because the normalizer prevents it from ever reaching consumers.

---

## ROOT CAUSE (or "no bug — reader normalizes both shapes")

**No bug.** The `system_builder_service.rb:create_hydrosphere` method has an explicit normalizer that converts array-of-objects format to flat object format at load time. This means:
1. Earth's JSON data uses array format in the source files
2. At runtime, system_builder_service normalizes it to flat object before building the hydrosphere
3. All downstream consumers see only flat object format

The inconsistency exists **only in the static JSON data files** but is harmless because of the normalization layer.

---

## PROPOSED FIX (only if needed)

### Option A: No fix needed (recommended)
- The normalizer already handles both shapes correctly
- Adding more readers that assume flat object is safe — they'll all see normalized data
- Documenting the canonical format would be helpful but not urgent

### Option B: Data cleanup (nice-to-have, not required)
- Normalize Earth's composition in sol.json and sol-complete.json to flat object for consistency
- Change Earth's array format to: `"composition": {"H2O": 96.5, "dissolved_salts": 3.5}`
- This eliminates the need for the normalizer code (or allows removing it)

### Option C: Add schema validation (defensive)
- Add a JSON schema or validation step that rejects array format at data-entry time
- Forces all future data to use flat object format

---

## RISK
- If the normalizer is ever removed or bypassed, Earth's composition would break all consumers
- The normalizer code itself is a maintenance burden — it exists because someone noticed this inconsistency and patched it without documenting why
- No other star system files (epsilon_eridani, tau_ceti) carry hydrosphere composition data, so the scope is limited to sol

---

## READY TO APPLY? — waiting for approval

Recommendation: **No fix needed** unless a future design task requires a formal schema. The normalizer makes this harmless. If cleanup is desired, Option B (data normalization) is the simplest and lowest-risk approach.
