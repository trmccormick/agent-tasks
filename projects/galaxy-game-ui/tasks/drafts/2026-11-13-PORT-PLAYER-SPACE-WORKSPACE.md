---
status: drafts
priority: normal
type: ui-port
project: galaxy-game-ui
---

# Port and Consolidate the Player Space Workspace

## Goal

Assemble the GalaxyGame Player Space workspace in the Rails 8.1 prototype by reusing existing GalaxyGame Space presentation/code and adapting useful EVE Dashboard JavaScript. This is not an EVE clone and does not implement live navigation or simulation.

## Read first

- Main game design authority: `galaxyGame/docs/wiki/ui/02_player_view_architecture.md`
- UI prototype direction: `galaxy-game-ui/docs/wiki/README.md`
- UI porting guidance: `galaxy-game-ui/docs/wiki/javascript-reuse-and-porting-plan.md`
- UI interaction references: `galaxy-game-ui/docs/wiki/interface-pattern-references.md`
- UI data approach: `galaxy-game-ui/docs/wiki/data-integration-and-migration.md`

## Inspect and reuse

1. Inspect the current prototype for any existing Space-related views, map code, components, fixtures, and navigation.
2. Inspect the main GalaxyGame repository for existing Space views, JavaScript, renderers, data structures, and assets.
3. Inspect the original EVE Dashboard source at `/Users/tam0013/Documents/git/eve-dashboard/` for relevant presentation modules, including:
   - `fleet_map.js`
   - `chain.js`
   - `assets.js`
   - `market.js`
   - `wealth.js`
   - `structures.js`

Verify actual source paths, dependencies, and provenance. Do not recreate a module from its description if its source can be recovered. Do not modify the EVE Dashboard source.

## Work

1. Identify the useful existing GalaxyGame Space view/presentation work and the EVE Dashboard modules that genuinely support it.
2. Consolidate or port only the useful pieces into the prototype, preserving working behavior.
3. Adapt GalaxyGame terminology, object types, and data mappings. Do not import EVE game rules, account data, branding, or backend behavior.
4. Use imported GalaxyGame JSON or clearly identified mock JSON with the same presentation code.
5. Provide a coherent Space workspace integrated into the Player navigation and existing prototype shell.
6. Make only focused CSS/layout adjustments needed for readability and consistency with the dark charcoal/navy + cyan design.
7. Record data dependencies, reused source modules, mock fields, and gaps for later API discovery.

If the source audit shows that a candidate module is unsuitable or its dependencies would require a substantial rewrite, omit it and report why. Do not force every listed module into the page.

## Acceptance criteria

- [ ] Space is reachable as a Player context in the prototype.
- [ ] The main Space visualization/workspace renders representative GalaxyGame objects/data.
- [ ] Reused modules and their original sources are identified.
- [ ] EVE-specific terminology and gameplay assumptions are not exposed as GalaxyGame behavior.
- [ ] Mock data is clearly identified and no live backend is required.
- [ ] The workspace fits the existing prototype navigation and visual system.
- [ ] The result is checked in the running prototype; report responsive or interaction limitations.
- [ ] Report exact files reused/changed, source provenance, and verification.

## Out of scope

Planetary, Surface, Settlement, Admin, AI Manager, live route calculations, orbital simulation, production APIs, EVE backend integration, wholesale import of the EVE application, and broad redesign.

Do not modify the main GalaxyGame or EVE Dashboard repositories. Do not commit or push without Tracy's explicit approval.
