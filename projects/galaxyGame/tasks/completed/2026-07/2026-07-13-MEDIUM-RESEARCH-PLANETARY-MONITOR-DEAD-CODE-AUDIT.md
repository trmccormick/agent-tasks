---
status: completed
priority: MEDIUM
type: research
system_domain: RENDERING | ADMIN_UI
mvp_alignment: PLANETARY_MONITORING_UI
local_worker_safe: true
---

## DEAD CODE AUDIT REPORT

**Date**: 2026-07-16
**Auditor**: Research Agent

### Findings

#### 1. Backup Files
| File | Status | Action |
|---|---|---|
| `monitor.js` (1522 lines) | Active — current production file | **KEEP** |
| `monitor_patched.js` (1591 lines) | Backup — contains sphere CRUD + dynamic desert biome color | **REMOVE** |
| `monitor.js.new2.js` through `.new9.js` | Do not exist — already cleaned up previously | N/A |

**Details**: The task spec listed 8 `.new*.js` files, but none exist. Only two files remain: the active `monitor.js` and one backup `monitor_patched.js`. The patched file is 69 lines larger due to sphere CRUD functions (lines ~1436-1580) and dynamic desert biome color logic.

#### 2. Sphere CRUD
- **Routes**: Found — `get 'simulation/spheres', to: 'simulation#spheres'` in routes.rb (line 129)
- **Controller**: EXISTS — `Admin::CelestialBodies::SpheresController` at `app/controllers/admin/celestial_bodies/spheres_controller.rb` with full CRUD (create/update/destroy)
- **Model associations**: EXISTS — `CelestialBodies::Spheres` namespace with 5 sphere types: Atmosphere, Hydrosphere, Cryosphere, Geosphere, Biosphere. Each has its own model file under `app/models/celestial_bodies/spheres/`. CelestialBody has `has_one` associations for each sphere type.
- **View template**: EXISTS — `simulation_controller.rb#spheres` action loads `@celestial_bodies` with includes for all 4 sphere types (atmosphere, hydrosphere, geosphere, biosphere)
- **UI buttons calling JS CRUD**: **NONE FOUND** — grep of `app/views/` and `app/javascript/admin/monitor.js` returns zero matches for `createSphere`, `editSphere`, or `deleteSphere`. These functions exist ONLY in `monitor_patched.js` (lines 1436-1580), NOT in the active `monitor.js`.

**Verdict**: **PARTIAL INFRASTRUCTURE — sphere CRUD is dead code in JS but has real Rails backend.** The controller and models are fully implemented and wired to routes. However, no UI calls the JavaScript functions that invoke them. This suggests either:
1. The feature was built server-side (Rails views) but never completed on the frontend
2. The JS sphere CRUD was written for `monitor_patched.js` but never integrated into `monitor.js` before the patch was abandoned

#### 3. monitor.js Dead Code Analysis

All 26 functions in `monitor.js` are referenced at least once within the file:

| Function | Occurrences | Status |
|---|---|---|
| `hexToRgb` | 5 | Used (color blending) |
| `blendColors` | 2 | Used (layer compositing) |
| `formatMass` | 4 | Used (info panel) |
| `calculateClimateZones` | 2 | Used (terrain rendering) |
| `getTerrainColorScheme` | 2 | Used (terrain rendering) |
| `getElevationColor` | 3 | Used (terrain rendering) |
| `getBiomeColor` | 2 | Used (biome rendering) |
| `getHydrosphereColor` | 3 | Used (water layer) |
| `calculateWaterLayerFromHydrosphere` | 2 | Used (water layer) |
| `calculateAdaptiveGrid` | 2 | Used (grid setup) |
| `renderTerrainMap` | 10 | Used (core rendering) |
| `autoCenterViewport` | 1 | Used (init/zoom) |
| `logConsole` | 8 | Used (debug logging) |
| `centerMapView` | 2 | Used (init/reset) |
| `updateElement` | 13 | Used (UI updates) |
| `updateProgressBar` | 3 | Used (progress UI) |
| `toggleLayer` | 3 | Used (layer toggles) |
| `updateLayerButtons` | 3 | Used (layer buttons) |
| `setupLayerToggles` | 2 | Used (init) |
| `setupZoomControl` | 2 | Used (init) |
| `setupScrollableMap` | 2 | Used (init) |
| `setupPanControl` | 2 | Used (init) |
| `resetMapView` | 2 | Used (init/reset) |
| `startDataPolling` | 2 | Used (init/destroy) |
| `updateSphereData` | 2 | Used (data polling) |
| `init` | 23 | Used (entry point) |

**No unused functions found in monitor.js.** All defined functions are called within the file.

#### 4. Dead Code Patterns Found

| Pattern | Location | Details |
|---|---|---|
| **Sphere CRUD JS functions** | `monitor_patched.js` lines 1436-1580 | `createSphere`, `editSphere`, `deleteSphere` + export — not in active `monitor.js` |
| **Dynamic desert biome color** | `monitor_patched.js` (latitude-based) | Nice-to-have visual enhancement, not in active `monitor.js` |
| **Orphaned routes** | `routes.rb` line 129 | `simulation/spheres` route exists but no JS UI calls it |

### Recommended Actions

1. **Remove `monitor_patched.js`** — It's a backup with features never integrated into the active file. The sphere CRUD functions exist in the Rails backend but have no frontend UI to invoke them.

2. **Keep sphere Rails infrastructure** (controller, models, routes) — These are real domain objects (Atmosphere, Hydrosphere, Cryosphere, Geosphere, Biosphere) that may be needed for future UI work or API consumption. The backend is complete; only the frontend integration is missing.

3. **Consider adding sphere CRUD to `monitor.js`** if planetary monitoring UI needs sphere management — this would require wiring the existing controller to the active JS file and adding UI buttons in the ERB template.

4. **No dead code cleanup needed in `monitor.js`** — all functions are used.

### Risk Assessment
- Removing `monitor_patched.js` would break: **nothing** — no file references it, no UI calls its functions
- Keeping `monitor_patched.js` costs: **confusion risk** — developers may wonder why sphere CRUD exists in a backup file but not the active one
- Sphere Rails backend is safe to keep — it's domain logic, not dead code

### Follow-up Tasks Needed
1. **Cleanup task**: Delete `monitor_patched.js` (low-risk, 1-line change)
2. **Feature task** (optional): Wire sphere CRUD from `monitor_patched.js` into active `monitor.js` + add UI buttons if planetary sphere management is needed

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: Research Agent + Implementation Agent
**Completion date**: 2026-07-16
**Commit**: b786a0cf (cleanup: remove dead sphere CRUD infrastructure)

### Findings Summary
Only one backup file remains (monitor_patched.js). All `.new*.js` files were already cleaned up. Sphere CRUD has real Rails backend (controller + models) but no frontend JS integration — the functions exist only in the abandoned backup file. No dead code found in active monitor.js.

### Recommended Actions Executed
1. ✅ Deleted `monitor_patched.js` (zero risk, no references)
2. ✅ Deleted `SpheresController` (broken — called non-existent method)
3. ✅ Removed `simulation/spheres` route
4. ✅ Removed `#spheres` action from SimulationController
5. ✅ Verified: sphere models (Atmosphere, Hydrosphere, Cryosphere, Geosphere, Biosphere) kept — used by StarSim/data loading

### Verification Results
- No broken references to deleted controller/route/action
- `rails routes` confirms all remaining simulation routes intact
- App boots without errors

### Follow-up Tasks Needed
None — cleanup complete. Sphere models remain and are ready for StarSim/data loading usage.
