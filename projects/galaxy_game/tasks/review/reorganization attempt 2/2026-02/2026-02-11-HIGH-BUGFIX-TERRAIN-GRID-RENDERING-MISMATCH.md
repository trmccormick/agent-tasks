---
status: backlog
priority: HIGH
type: bugfix
system_domain: FRONTEND_VISUALS
mvp_alignment: LUNA_SURFACE_GRID
local_worker_safe: true
---

# TASK: 2026-02-11-HIGH-BUGFIX-TERRAIN-GRID-RENDERING-MISMATCH
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: bugfix  
**Created**: 2026-02-11  
**Last Updated**: 2026-05-18  

---

## Local Worker Triage Report
*Filled in by local model during backlog review*
- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A - Pure Frontend JS validation
- **MVP Alignment**: VALID - Required for clean Luna surface grid representation
- **Action Line**: READY FOR LOCAL ASSIGNMENT

---

## Agent Assignment

**Assigned To**: Qwen2.5-Coder 14B (M4) or Copilot Local  
**Why This Agent**: Excellent at front-end coordinate math and asset rendering logic  
**Supervision Level**: standard  

---

## Context
The canvas rendering system exhibits a mismatch between backend structural models and frontend canvas grid cell calculation. When generating terrain profiles for variable-sized entities (like Luna vs. Earth), the JavaScript layer ignores the concrete dimensions passed from the backend `terrainData`, defaulting to a hardcoded layout that causes clipping, pixelation, and visual feature loss.

**Relevant Architecture Docs**:
- `docs/frontend/canvas-rendering.md` — [Grid coordinate mapping mechanisms]

---

## Problem Statement
The frontend grid calculation does not scale dynamically with different planet/moon body models.

**Current behavior**: JavaScript grid cell dimensions are static, ignoring actual `terrainData` width boundaries.
**Expected behavior**: JavaScript utilizes dynamic aspect mapping via `calculateAdaptiveGrid` to guarantee uniform rendering scales regardless of body diameter.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/javascript/packs/renderers/terrain_grid.js` | Core canvas renderer | `calculateAdaptiveGrid` |
| `spec/javascript/renderers/terrain_grid.spec.js` | Frontend asset testing | Dimension checking suite |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/concerns/terrain_generator.rb` | Verifies backend payload dimension strings |

---

## Implementation Steps

### Step 1 — Adapt Calculation Method
Modify `calculateAdaptiveGrid` inside `terrain_grid.js` to:
- Extract explicit `width` and `height` properties from the incoming `terrainData` JSON payload.
- Eliminate hardcoded fallback scalars.

### Step 2 — Implement Low-Mass Scaling Guards
Add conditional scaling constraints specifically for lower-mass astronomical bodies (e.g., Luna, Ceres) to preserve visual feature definition on tighter coordinate maps.

### Step 3 — Performance & Resize Testing
Verify that canvas bounding rect resize listeners dynamically execute grid recalculations without generating memory leaks or redundant frame re-renders.

---

## Acceptance Criteria
- [ ] `calculateAdaptiveGrid` dynamically computes step increments from real `terrainData`.
- [ ] Moons and small asteroid bodies scale smoothly without dropping grid rows or causing pixelation.
- [ ] Canvas element matches resolution context across Luna, Mars, and Earth test fixtures.
- [ ] JavaScript tests execute with zero assertions failing.

---

## Stop Conditions — escalate immediately if:
- Fixing the layout requires modifying downstream WebGL rendering packages or changing backend database schemas.
- Rescale calculations severely impact client-side frame-rendering budgets.

---

## Commit Instructions
Run git commands on **host**:
```bash
git add app/javascript/packs/renderers/terrain_grid.js spec/javascript/renderers/terrain_grid.spec.js
git commit -m "fix: resolve frontend terrain grid rendering dimension mismatch"
git push