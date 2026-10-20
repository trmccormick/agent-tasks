# Galaxy Game UI Prototype — Status & Progress Tracking

**Last Updated**: 2026-10-20  
**Project Phase**: UI/UX DESIGN EXPLORATION  
**Overall Status**: READY FOR TASK 1 DISPATCH

**Strategic Context**: This is an **information architecture laboratory** for exploring UI/UX patterns using mockups and placeholders. Inspiration drawn from EVE Dashboard (styling), Civilization (terrain UI), SimCity (building/production UI), and SimEarth (world management). Patterns validated here will guide main game development; live data can be gradually integrated for tuning.

---

## Quick Status

| Component | Status | Purpose | ETA |
|-----------|--------|---------|-----|
| Project setup + MockData | 🔴 NOT STARTED | Foundation for all features | 2026-10-21 |
| Dashboard UI mockup | 🔴 NOT STARTED | Research, manufacturing, inventory, missions | 2026-10-22 |
| Surface/Terrain UI mockup | 🔴 NOT STARTED | Settlement network, AstroLift routes, terrain grid | 2026-10-23 |
| Admin Suite UI mockup | 🔴 NOT STARTED | AI Manager tuning, NPC management, Digital Twin | 2026-10-24 |

---

## Active Tasks

### Currently In Progress
- None — awaiting Qwen dispatch for Task 1

### Next Up (Ready for Dispatch)
1. **2026-10-20-HIGH-FEATURE-RAILS8-PROJECT-SETUP.md** — Initialize Rails 8 app, enhanced MockData module (character, corporations, buy orders, missions, routes, admin data)
2. **2026-10-20-HIGH-FEATURE-DASHBOARD-COMMAND-CENTER.md** — Build Player Dashboard UI mockup (research, facilities, inventory, manufacturing, buy orders, missions, ships)
3. **2026-10-20-HIGH-FEATURE-WORMHOLE-NETWORK-TRANSIT.md** — Build Surface/Terrain View UI mockup (settlement network, AstroLift routes, terrain canvas placeholder)
4. **2026-10-20-MEDIUM-FEATURE-ADMIN-TELEMETRY-PANEL.md** — Build Admin Suite UI mockup (AI Manager, NPC management, buy order tracking, Digital Twin)

### Completed
- None yet

---

## Design Approach

**UI/UX Prototyping Philosophy:**
- ✅ Placeholders and mockups are acceptable outcomes
- ✅ Use MockDataHelper for all data (no database required)
- ✅ Focus on information architecture: "What should players see?" and "How should it be organized?"
- ✅ Styling borrowed from EVE Dashboard (glassmorphic panels, dark theme, cyan accents)
- ✅ Gameplay concepts from Galaxy Game (AI Manager, NPC corporations, AstroLift logistics, manufacturing, research)
- ✅ Layout inspiration from Civilization (terrain grids), SimCity (production chains), SimEarth (world management)
- ❌ NOT trying to port full game logic or implement complex physics
- ❌ NOT trying to create production-ready code (refactoring allowed later)

**Future Evolution:**
1. After UI patterns validated, live game data can replace MockDataHelper
2. This allows controlled sandbox testing of economy/UI before main game integration
3. Validated patterns guide main game development

---

## Blocking Issues
None currently.

---

## Known Gotchas & Constraints

⚠️ **Placeholder-First Design**: Canvas rendering can be simple HTML table/grid. Mission is information architecture, not pixel-perfect graphics.

⚠️ **No Database Models**: All data via MockDataHelper in-memory. No migrations, no ActiveRecord.

⚠️ **MockDataHelper Structure** (for Task 1): Must provide:
- Character location info + ISK balance
- Research queue (blueprints in progress)
- Local facilities (production buildings)
- Local inventory (resources at location)
- Ships (docked/in-transit with ETAs)
- NPC buy orders (corporation, what they need, price, quantity)
- Player missions (escalated buy orders with bounties)
- Player contracts (fulfillments in progress)
- AstroLift routes (with pricing/capacity/ETAs)
- Settlement network (wormholes, stability, EM potential)
- NPC corporation status (current activity, inventory, operations)
- Admin data (AI priorities, Digital Twin scenarios, metrics)

⚠️ **Galaxy Game Terminology Only**: AI Manager, Facilities, Research, Blueprints, Settlements, Wormholes, ISK, AstroLift, NPC Corporations (use real corp names: LDC, MDC, VDC, AstroLift, Vector Hauling). Do NOT use EVE Online concepts.

⚠️ **Styling Reference**: 
- EVE Dashboard: `/eve-dashboard/app/static/style.css` (color palette, panel effects, glassmorphism)
- Existing guide: `./EVE-DASHBOARD-CSS-REFERENCE.md` (quick extraction)
- Color palette: Primary #00d4ff, Secondary #9d4edd, Backgrounds #0a0a0a/#1a1a2e, Text #e0e0e0

⚠️ **Rails 8 Setup Reference**: `./WVU-MOONSHOT-SETUP-REFERENCE.md` (Docker, Gemfile, Tailwind config, Stimulus patterns). Skip: database, auth, complex admin logic.

⚠️ **Asset Reference**: 
- Corporation logos: `/galaxyGame/data/images/logos/` (AstroLift, LDC, MDC, VDC, Vector Hauling)
- Terrain tilesets: `/galaxyGame/docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md`
- Galaxy Game UI patterns: `./GALAXY-GAME-UI-EXTRACTION-ANALYSIS.md`

⚠️ **Three-View Architecture** (for reference): World View (SimEarth), Surface View (Civ/FreeCiv), TerrainForge (SimCity detail). Task 2 focuses on Surface View mockup.

---

## Handoff Queue
- **Next handoff**: Task 1 completion → Task 2 dispatch
- **Session owner**: TBD

---

## Archive & History
- **Project created**: 2026-10-20
- **Latest update**: 2026-10-20 (UI/UX prototype reframing + MockData expansion + NPC system integration)
- **Project tracking**: This file (`status.md`)
