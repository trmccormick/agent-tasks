---
status: drafts
priority: normal
type: ui-port
project: galaxy-game-ui
---

# Port the Player Planetary View

## Goal

Bring the existing GalaxyGame Planetary view into the separate Rails 8.1 UI prototype. This is a reuse-and-adapt task, not a redesign or a running-game implementation.

## Read first

- Main game design authority: `galaxyGame/docs/wiki/ui/02_player_view_architecture.md`
- UI prototype direction: `galaxy-game-ui/docs/wiki/README.md`
- UI porting guidance: `galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md`
- UI data approach: `galaxy-game-ui/docs/wiki/data-integration-and-migration.md`

## Inspect and reuse

In the main Rails 7 application, start with:

- `app/views/admin/celestial_bodies/planetary.html.erb`
- `app/javascript/admin/monitor.js`
- Related renderer, styles, and Planetary data-loading code used by that view.

Inspect the current UI prototype before copying anything. Reuse any existing prototype work and avoid duplicate implementations.

## Work

1. Port the Planetary page and its useful rendering behavior into the prototype.
2. Reuse the existing renderer and supporting assets where practical; make only the compatibility and path changes needed for the Rails 8.1 prototype.
3. Use the existing confirmed Planetary data for Earth, Mars, Venus, and Titan where available. Clearly identified mock JSON is acceptable for gaps.
4. Feed imported or mock JSON through the same presentation code.
5. Remove dependencies on main-app routes, live polling, or backend services. The page must render independently in the prototype.
6. Fit the page into the prototype's existing navigation, layout, and dark charcoal/navy + cyan visual direction. Refine typography and information density without redesigning the whole shell.
7. Record the data/assets used, any mock values, and any missing data the screen appears to need. These are observations for later API discovery, not an API specification.

Use the player-facing name **Planetary**. Do not create a separate Player view called Monitor based only on legacy filenames.

## Acceptance criteria

- [ ] Planetary is reachable through the prototype's Player navigation/context.
- [ ] The primary visualization renders with representative data.
- [ ] Existing useful rendering behavior is reused rather than needlessly rewritten.
- [ ] No main-app backend endpoint, polling loop, or live simulation is required.
- [ ] Data and assets load through the prototype's existing conventions.
- [ ] Mock data is clearly distinguishable from confirmed/imported data.
- [ ] The result is checked in the running prototype at desktop size; report any responsive issues found.
- [ ] Report the files reused/changed, verification performed, and remaining limitations.

## Out of scope

Surface, Space, Settlement, Admin, AI Manager, production APIs, simulation behavior, new data infrastructure, unrelated cleanup, and broad navigation redesign.

Do not modify the main game repository. Do not commit or push without Tracy's explicit approval.
