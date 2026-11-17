---
status: active
task: 2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-PLANETARY-VIEW
created: 2026-11-14
---

# Synthesis Report: Port Player Planetary View

## What I'm About to Do

Port the existing Admin Planetary view (`galaxyGame/galaxy_game/app/views/admin/celestial_bodies/planetary.html.erb`) into a **Player-facing, self-contained** planetary page in the Rails 8.1 prototype. Earth renders with terrain plus **both hydrosphere and temperature overlays**, matching the November 11 screenshots of working layered visualization. All admin controls, polling timers, and backend dependencies are removed. Data is embedded as inline JSON mock matching the inspected payload shape.

### First Slice
Create the view file, embed Earth data inline, port the renderer JS (without polling), add a Player navigation link in the Galaxy sidebar section, and verify in the running prototype.

### Success Criteria
- Planetary page reachable via Player navigation
- Earth renders with terrain + one overlay
- No polling timer or main-app endpoint calls
- No admin controls/links visible
- Mock data visibly distinguishable from source data

## Files I'll Reference

| File | Purpose |
|------|---------|
| `galaxyGame/galaxy_game/app/views/admin/celestial_bodies/planetary.html.erb` | Source view — data payload shape, layout structure, layer buttons, data panels |
| `galaxyGame/galaxy_game/app/javascript/admin/monitor.js` | Source renderer — canvas rendering, layer overlays (terrain, hydrosphere/water, biomes, features, temperature, rainfall, resources, civilization), viewport pan/zoom, color schemes. **Strip polling; keep all rendering logic and layer system.** |
| `galaxy-game-ui/galaxy-game-ui/config/routes.rb` | Current routes — add a new player route (e.g., `/player/planetary`) |
| `galaxy-game-ui/galaxy-game-ui/app/views/layouts/dashboard.html.erb` | Dashboard layout with sidebar nav — add Planetary link in Galaxy section |
| `galaxy-game-ui/galaxy-game-ui/app/assets/tailwind/application.css` | Tailwind tokens — ensure dark charcoal/navy + cyan styling consistency |
| `galaxy-game-ui/galaxy-game-ui/app/javascript/application.js` | Importmap entry point — add planetary renderer module |

## Prerequisites Completed

- [x] Startup reading: GUARDRAILS.md, README.md, SESSION_GUIDANCE.md
- [x] Task file read in full (active status)
- [x] Source view inspected: `planetary.html.erb` (data payload, layout, panels)
- [x] Source renderer inspected: `monitor.js` (rendering logic, polling mechanism, layer system)
- [x] Prototype routes inspected: simple resource-based routes
- [x] Prototype layout inspected: `dashboard.html.erb` with sidebar nav
- [x] Tailwind tokens confirmed in `application.css`
- [x] Task moved from backlog → active (Step 7)
- [x] Single copy verified (Step 8)

## Expected Outcomes

### Observable Acceptance Outcomes

1. **Planetary reachable in Player context** — Click "Planetary" link in Galaxy sidebar section → navigates to the planetary page
2. **Earth renders with terrain + both hydrosphere and temperature overlays** — Canvas shows Earth terrain map; layer selector toggles between Terrain, Hydrosphere, and Temperature (all three working)
3. **No polling timer or main-app endpoint** — Browser DevTools Network tab shows zero XHR/fetch calls to `/admin/celestial_bodies/*`; no `setInterval` in console
4. **No admin controls** — No "Generate Terrain", "Regenerate Terrain", "Edit Properties", or "Admin" links visible
5. **Mock data visibly distinguishable** — A comment at the top of the embedded JSON block states `<!-- MOCK DATA: ported from main app source, Earth body -->` or similar

### Verification Method

1. Start prototype container (if not running): confirm compose config at prototype Git root
2. Navigate to `/player/planetary` in browser
3. Open DevTools → Network tab → verify no admin endpoint calls
4. Open DevTools → Console → verify no errors, no setInterval polling
5. Click layer buttons (Terrain, Hydrosphere, Temperature) → verify all three render correctly
6. Capture screenshot as evidence

## Critical Gotchas I Will Avoid

1. **GOTCHA 1 — No Admin controls**: Do NOT port "Generate/Regenerate Terrain", "Edit Properties", or any admin-only links/buttons from the source view. These are explicitly out of scope.

2. **GOTCHA 2 — No polling**: The source `monitor.js` polls `/admin/celestial_bodies/:id/sphere_data` every 5 seconds. The prototype must NOT call this endpoint. Use inline embedded JSON mock data instead. Strip the `updateInterval` / `setInterval` logic entirely.

3. **GOTCHA 3 — Coarse/blurry map rendering is a known issue**: Do NOT diagnose or repair terrain map quality in this task. Report it as a limitation in the completion report.

4. **GOTCHA 4 — Earth only**: Mars, Venus, and Titan are NOT acceptance requirements. Focus on Earth.

5. **GOTCHA 5 — Container paths**: All Rails/RSpec commands run inside `galaxy_game` container via `docker exec -it galaxy_game bash -c '...'`. Never on host.

6. **GOTCHA 6 — No commit without approval**: Do not commit or push. Stop for review after implementation and verification.

7. **GOTCHA 7 — Tailwind v4 @theme variables**: New design tokens go in `@theme static { ... }` block. Leave existing WVU `@theme` block alone until layout task replaces it.

8. **GOTCHA 8 — Protected AI Manager files**: Do NOT modify, stage, or commit any of the eight untracked AI Manager files.

## Data/Mock Strategy

The source view injects monitor data via `<script type="application/json" id="monitor-data">`. I will create an equivalent inline JSON block with Earth-specific mock data matching the inspected payload shape:

```json
{
  planet_id: 1,
  planet_name: "Earth",
  planet_type: "terrestrial",
  surface_temperature: 288,
  atmosphere_temperature: 288,
  atmosphere_pressure: 1.0,
  atmospheric_composition: { N2: 78.0, O2: 21.0, Ar: 0.93 },
  terrain_data: "<base64 or array data>",
  planet_data: { ... },
  geosphere_present: true,
  biosphere_present: true,
  hydrosphere_present: true,
  atmosphere_present: true,
  terrain_map_present: true,
  available_layers: { terrain: true, water: true, biomes: true, temperature: true, ... },
  civilization_features: []
}
```

Mock fields will be clearly labeled in a comment. Missing data (e.g., full `terrain_data` array) will use placeholder values with comments noting what future API discovery should provide.

## Remaining Limitations to Report

- Terrain map rendering quality (coarse/blurry) — known issue, not fixed
- Only Earth body supported (Mars/Venus/Titan deferred)
- Mock data replaces live simulation data — future API integration needed
- No Surface View drill-down interaction in this slice
