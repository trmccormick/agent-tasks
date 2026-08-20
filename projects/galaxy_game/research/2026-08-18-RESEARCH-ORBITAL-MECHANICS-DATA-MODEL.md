# Research Request: Orbital Mechanics Data Model + File Path Verification

**Generated:** 2026-08-18
**For:** Qwen (planning agent)
**Type:** Research-only, no code changes
**Time estimate:** 30-45 minutes

---

## Context

A task file was drafted (`2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md`) that proposes a `Mission::TransitEngine` service class for calculating launch windows and transit times. The draft has several claims about existing code/data that need verification, and the orbital mechanics approach needs scoping based on what already exists.

---

## Research Items (in order)

### 1. Confirm transfer_calculator.rb findings still match

**Why:** Already reviewed directly — confirm nothing has changed rather than re-summarize from scratch.

**Action:**
```bash
grep -n 'def get_celestial_body_data\|def calculate_transfer_duration\|def find_optimal_transfer_window\|rand(' /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/orbital_mechanics/transfer_calculator.rb
```

**Confirm these 4 known issues still hold:**
- `get_celestial_body_data` hardcodes only earth/venus/mars/luna (no Titan, no generalized lookup)
- `find_optimal_transfer_window`'s `target_phase` case statement compares symbols against string literals (`[origin_data[:name], dest_data[:name]].sort` vs `when ['earth','mars']`) — never matches, always falls to opposition default
- `calculate_transfer_duration` uses `rand()` for the actual transfer-time return value instead of the delta-v it just computed
- `ORBITAL_PERIODS[:luna]` (27.32d, sidereal month) and `SEMI_MAJOR_AXES[:luna]` (1.0 AU, heliocentric) mix reference frames

**Return:** Still accurate? Anything changed?

### 2. Check `CelestialBody` model — Does it store orbital data?

**Why:** TransitEngine needs semi-major axis, orbital period, current position/epoch to compute real Hohmann transfers. If this data already exists in the celestial body model (from the data-driven generation refactor), we wire to it. If not, we need a data-model gap task first.

**Action:**
```bash
grep -rn 'semi_major_axis\|orbital_period\|orbital_elements\|epoch\|current_position' /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/celestial_bodies/ --include="*.rb" | head -20
```

**Return:** Which CelestialBody models store orbital data? What fields are available? Is it populated from JSON or computed?

Also confirm: did the magnetosphere/celestial-body-generation refactor (magnetosphere_strength, magnetic_field, mass, rotation_period, age) touch orbital elements (semi-major axis, orbital period, position/epoch) at all, or was that refactor scoped to physical fields only? Don't assume — check the actual model/migration history.

### 3. Confirm WormholeCoordinator stub status still matches

**Why:** Already reviewed directly — confirm nothing has changed.

**Action:**
```bash
grep -n 'def find_wormhole_connection\|def extract_route_wormholes\|def calculate_resource_overlap\|def calculate_transport_dependency\|def check_dependencies_satisfied' /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/ai_manager/wormhole_coordinator.rb
```

**Confirm:** these methods are still placeholder stubs (nil / mock IDs / 0.0 / always-true returns), real BFS routing logic elsewhere in the file is unaffected.

**Return:** Still accurate?

### 4. Verify file path claims in the task draft

The task draft asserts these paths exist — verify each:

```bash
# Claim 1: cycler transit preparation task exists
find /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions_v2/tasks/ -name '*cycler*transit*' 2>/dev/null

# Claim 2: Venus harvester old-format files exist
ls /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions/venus_harvester_mission/ 2>/dev/null

# Claim 3: Gas separator task exists
find /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions_v2/tasks/ -name '*gas_separator*' 2>/dev/null

# Claim 4: Precursor manifest/profile exist
ls /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json 2>/dev/null
ls /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json 2>/dev/null

# Claim 5: Solar system pipeline rake exists and gates on N2 delivery
grep 'first_n2_delivery_completed' /Users/tam0013/Documents/git/galaxyGame/galaxy_game/lib/tasks/solar_system_mission_pipeline.rake 2>/dev/null
```

**Return:** Which claims are TRUE, which are FALSE or STALE.

### 5. Check: Is `data/json-data/missions_v2/tasks/` the correct path for new JSON files?

**Why:** The "data lands in wrong nested directory" bug has recurred multiple times. Need to confirm this is the right location before writing any new files.

**Action:**
```bash
# Check if this path is gitignored
cd /Users/tam0013/Documents/git/galaxyGame && git check-ignore -v data/json-data/missions_v2/tasks/task_deploy_gas_separator_v2.json 2>/dev/null || echo "NOT IGNORED"

# Check what the existing v2 tasks look like (format reference)
head -20 /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions_v2/tasks/task_deploy_gas_separator_v2.json
```

**Return:** Is this path correct? What's the format convention for v2 task files?

---

---

## Research Results (Completed 2026-08-19)

**Full results:** See `summaries/2026-08-19-SUMMARY-ORBITAL-MECHANICS-DATA-MODEL.md`

### Key Findings Summary

| Item | Result |
|------|--------|
| transfer_calculator.rb issues | All 4 confirmed — case comparison bug, rand() usage, hardcoded bodies, Luna reference frame mixup |
| CelestialBody orbital data | Has `orbital_period` but NO `semi_major_axis`, `current_position`, or `epoch` on main model |
| WormholeCoordinator stubs | All 4 methods still stubs (nil/0.0/true) — real BFS routing exists elsewhere |
| Cycler transit task claim | ❌ FALSE — no files matching `*cycler*transit*` exist |
| Venus harvester old-format claim | ❌ FALSE — `missions/venus_harvester_mission/` not found |
| Gas separator task claim | ✅ TRUE |
| Precursor manifest/profile claim | ✅ TRUE |
| Solar system pipeline N2 gate claim | ✅ TRUE (3 occurrences) |
| data/json-data path gitignored? | ✅ YES — `.gitignore:29:/data/` |

### Implications for TransitEngine Task

1. **Use fixed orbital elements** — no real-time position tracking available on CelestialBody model
2. **Do NOT depend on transfer_calculator.rb** — 4 confirmed bugs make it unreliable
3. **Do NOT depend on WormholeCoordinator** — incomplete, separate task needed
4. **Task draft needs corrections** — remove references to non-existent cycler transit and Venus harvester files

---

## What to Return

For each item above, return:
1. **Result**: What you found (file exists/doesn't exist, content summary, etc.)
2. **Implication**: How it affects the TransitEngine task scoping
3. **Recommendation**: Wire to existing code vs build new vs blocked on data model

---

## What NOT to Do

- **No code changes** — research only
- **No dispatch** — this is a fill-in request for the strategist (Claude)
- **No orbital mechanics implementation** — that comes after we know what exists
- **No interstellar routing work** — WormholeCoordinator is separate and incomplete

---

*End of research request.*
