---
status: backlog
priority: MEDIUM
type: research
system_domain: ADMIN_UI | PLANETARY_MODEL
mvp_alignment: PLANETARY_MONITORING_UI
local_worker_safe: true
---

## TASK: Sphere CRUD Functions — Evaluate Necessity for MVP

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-07-13
**Last Updated**: 2026-07-13

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

## Research Questions

1. **Does the MVP require sphere management in the planetary monitor?**
   - Check Phase 1-4 task files for any sphere creation/editing requirements
   - Check if TerraSim::AtmosphericTransferService or other services need sphere data

2. **Are there controller endpoints for `/admin/celestial_bodies/:id/spheres`?**
   - Search for `spheres_controller.rb` or nested routes in `routes.rb`
   - Check if the ERB template (`planetary.html.erb`) references sphere management

3. **Is sphere data already managed elsewhere?**
   - Check if spheres are created via rake tasks, migrations, or other services
   - Check if the planetary model has built-in sphere management

4. **If needed, what's the implementation scope?**
   - Controller actions (CRUD)
   - Routes configuration
   - ERB template buttons/forms
   - RSpec tests

---

## Deliverable

Create a synthesis report answering:
- **KEEP**: Sphere CRUD is needed for MVP → create implementation task
- **DISCARD**: Sphere CRUD is not needed → no further action
- **DEFER**: Sphere CRUD is needed but not for MVP → add to backlog with priority

---

## Stop Conditions — escalate if:
- Research requires writing code (should be read-only investigation)
- Controller endpoints exist but are incomplete (note in report, don't fix)
