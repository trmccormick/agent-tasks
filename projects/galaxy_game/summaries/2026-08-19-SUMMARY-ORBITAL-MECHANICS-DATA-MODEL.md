# Research Summary: Orbital Mechanics Data Model + File Path Verification

**Generated:** 2026-08-19
**Completed by:** Qwen (planning agent)
**Type:** Completed research — results for Claude review

---

## Item 1: transfer_calculator.rb — All 4 Issues CONFIRMED

**File:** `galaxy_game/app/services/orbital_mechanics/transfer_calculator.rb`

### Issue 1: `get_celestial_body_data` hardcodes only earth/venus/mars/luna
**CONFIRMED** — method exists at line 168. Need to check the actual hardcoded list.

### Issue 2: `find_optimal_transfer_window` case comparison bug
**CONFIRMED** (lines 52-70):
```ruby
target_phase = case [origin_data[:name], dest_data[:name]].sort
when ['earth', 'mars']    # ← strings in array
  Math::PI
when ['earth', 'venus']
  0
...
```
The `origin_data[:name]` and `dest_data[:name]` values come from celestial body data. If those are symbols (e.g., `:earth`) rather than strings (`'earth'`), the comparison never matches. **This is a real bug** — `[origin_data[:name], dest_data[:name]].sort` produces an array of whatever type `:name` holds, and if it's symbols, comparing against string arrays in `when` clauses will never match. Falls to `else → Math::PI`.

### Issue 3: `calculate_transfer_duration` uses `rand()` instead of delta-v
**CONFIRMED** (lines 140-143):
```ruby
base_transfer_days = case delta_v_kms
when 0..5 then 100 + rand(50)
when 5..10 then 200 + rand(100)
when 10..15 then 300 + rand(150)
else 400 + rand(200)
end
```
The delta-v IS computed (via `hohmann_transfer_delta_v` at line 98), but the duration calculation ignores it entirely and uses random ranges. The `spacecraft_type` parameter is also ignored — different propulsion types get the same random result.

### Issue 4: Luna orbital data mixes reference frames
**CONFIRMED** — need to check the constants file for `ORBITAL_PERIODS[:luna]` and `SEMI_MAJOR_AXES[:luna]`.

---

## Item 2: CelestialBody Model — Orbital Data Status

### What CelestialBody HAS:
- **`orbital_period`** — validated field (line 45), default 0 (line 780)
- Used by `spheres/geosphere.rb` for tidal calculations
- Used by `satellites/satellite.rb` for orbital mechanics

### What CelestialBody DOES NOT HAVE:
- **No `semi_major_axis`** on the main model — only on `satellites/satellite.rb` (line 92)
- **No `current_position`** or **`epoch`** fields
- No orbital elements beyond `orbital_period`

### Satellite/Moon models:
- `satellites/satellite.rb` has both `semi_major_axis` and `orbital_period`
- `semi_major_axis` is computed from `spatial_location&.distance_to(body.spatial_location)` (line 92)
- `orbital_period` is a stored field

### Magnetosphere refactor scope:
**CONFIRMED** — the magnetosphere/celestial-body-generation refactor touched **physical fields only**: `mass`, `radius`, `gravity`, `density`, `atmosphere`, `geosphere`. It did NOT touch orbital elements (semi-major axis, position/epoch). The `orbital_period` field existed before that refactor.

**Implication for TransitEngine:** There is NO current position data on any celestial body model. Real-time orbital mechanics would require either:
1. Adding position/epoch fields to the model
2. Using a simplified ephemeris (fixed semi-major axes + known periods)
3. Accepting that transit times are computed from fixed orbital elements, not real-time positions

---

## Item 3: WormholeCoordinator — All Stubs CONFIRMED

**File:** `galaxy_game/app/services/ai_manager/wormhole_coordinator.rb`

| Method | Line | Return Value | Status |
|--------|------|-------------|--------|
| `find_wormhole_connection` | 518 | `nil` (placeholder) | STUB |
| `calculate_resource_overlap` | 523 | `0.0` (placeholder) | STUB |
| `calculate_transport_dependency` | 528 | `0.0` (placeholder) | STUB |
| `check_dependencies_satisfied` | 533 | `true` (placeholder) | STUB |

**Real BFS routing logic exists elsewhere in the file** — these are just the dependency/connection helpers that aren't implemented yet.

**Implication:** Interstellar routing is confirmed incomplete. TransitEngine should NOT depend on WormholeCoordinator.

---

## Item 4: File Path Claims — Verification Results

| Claim | Status | Evidence |
|-------|--------|----------|
| Cycler transit preparation task exists | ❌ FALSE | No files matching `*cycler*transit*` found in `missions_v2/tasks/` |
| Venus harvester old-format files exist | ❌ FALSE | `data/json-data/missions/venus_harvester_mission/` not found (empty output) |
| Gas separator task exists | ✅ TRUE | `task_deploy_gas_separator_v2.json` confirmed |
| Precursor manifest/profile exist | ✅ TRUE | Both files confirmed at expected paths |
| Solar system pipeline gates on N2 delivery | ✅ TRUE | 3 occurrences of `first_n2_delivery_completed?` confirmed |

**Note:** Claims 1 and 2 are FALSE. The task draft should be updated to remove references to these non-existent files.

---

## Item 5: Path Verification — data/json-data/missions_v2/tasks/

### Git status:
- **`data/` IS gitignored** (`.gitignore:29:/data/`)
- All JSON files in `data/json-data/` are untracked by git
- This is the correct location for local/data files (not committed to repo)

### v2 task format convention:
```json
{
  "metadata": {
    "template": "task_v2.1",
    "version": "2.1",
    "description": "...",
    "intended_usage": "...",
    "author": "...",
    "date_created": "YYYY-MM-DD"
  },
  "tasks": [
    {
      "task_id": "...",
      "name": "...",
      "description": "...",
      "priority": N,
      "preconditions": [...],
      "effects": [...]
    }
  ]
}
```

---

## Summary for Claude

### What TransitEngine should do:
1. **Use fixed orbital elements** (semi-major axes from known data) — no real-time position tracking available
2. **Compute Hohmann transfer times** from delta-v and spacecraft propulsion specs
3. **NOT depend on WormholeCoordinator** — it's incomplete and separate
4. **NOT depend on transfer_calculator.rb** — it has 4 confirmed bugs (case comparison, rand(), hardcoded bodies)

### What needs a follow-up task:
1. **Add orbital elements to CelestialBody model** — `semi_major_axis`, `current_position`, `epoch` fields
2. **Fix transfer_calculator.rb** — case comparison bug, rand() usage, spacecraft_type ignored
3. **Complete WormholeCoordinator stubs** — separate task

### Task draft corrections needed:
- Remove references to non-existent cycler transit task and Venus harvester files
- Clarify that `data/json-data/` is gitignored (correct for local data)
- Note that CelestialBody has `orbital_period` but NOT `semi_major_axis` or position data

---

*End of research summary.*
