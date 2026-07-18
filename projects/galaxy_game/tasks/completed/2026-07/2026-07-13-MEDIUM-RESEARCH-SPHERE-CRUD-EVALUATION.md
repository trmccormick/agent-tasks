---
status: completed
priority: MEDIUM
type: research
system_domain: ADMIN_UI | PLANETARY_MODEL
mvp_alignment: PLANETARY_MONITORING_UI
local_worker_safe: true
---

## TASK: Sphere CRUD Functions — Evaluate Necessity for MVP

**Status**: COMPLETED
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-07-13
**Last Updated**: 2026-07-17

---

## Context

`monitor_patched.js` (now deleted) contained three sphere management functions:

```javascript
AdminMonitor.createSphere(sphereType)  // POST /admin/celestial_bodies/:id/spheres
AdminMonitor.editSphere(sphereId, sphereType)  // Navigate to edit page
AdminMonitor.deleteSphere(sphereId, sphereType)  // DELETE /admin/celestial_bodies/:id/spheres/:id
```

These manage the three planetary spheres per celestial body:
- **Atmosphere** — pressure, temperature, composition
- **Hydrosphere** — water coverage, ocean/ice mass
- **Cryosphere** — ice thickness

The functions were prototyped but never wired to UI buttons or controller endpoints.

---

## Research Findings

### 1. MVP Requirement: NOT NEEDED
No Phase 1-4 task files reference sphere creation/editing requirements. Sphere data is managed by other services (StarSim, TerraSim) — not via the planetary monitor UI.

### 2. Controller Endpoints: NON-EXISTENT / DEAD CODE
- No active `spheres_controller.rb` exists (was deleted as dead code)
- No nested routes for `/admin/celestial_bodies/:id/spheres` in `routes.rb`
- The ERB template (`planetary.html.erb`) has no sphere management UI

### 3. Sphere Data Management: OTHER SERVICES
- Sphere models remain in the codebase — used by StarSim/data loading services
- Sphere data is created via rake tasks, migrations, and other services (not admin UI)
- The planetary model does not have built-in sphere management for the monitor

### 4. Dead Code Cleanup (2026-07-16)
The following dead code was identified and removed:
- `monitor_patched.js` — contained orphaned sphere CRUD functions
- `SpheresController` — controller with no routes or UI wiring
- Simulation/spheres route/action — never connected to anything

---

## Verdict: DISCARD

**Sphere CRUD is not needed for MVP.** The three sphere management functions were prototype code that was never wired to the UI or controller endpoints. All sphere data is managed by other services (StarSim, TerraSim).

The dead code has been cleaned up:
- `monitor_patched.js` deleted
- `SpheresController` deleted
- Orphaned route/action removed
- Sphere models retained (used by StarSim/data loading)
- Zero broken references, all routes verified working

**galaxyGame commit**: b786a0cf

---

## Stop Conditions — Met
- Research was read-only investigation ✓
- Dead code cleanup completed separately (not part of this task scope)
