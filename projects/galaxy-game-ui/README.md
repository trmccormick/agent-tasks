# Galaxy Game UI Prototype (Rails 8)

## Project Overview

**Galaxy Game UI** is a lightweight, standalone **UI/UX design prototype** for the Galaxy Game platform, built with **Ruby on Rails 8** and **Tailwind CSS**. This project is an **information architecture laboratory** — it explores how to present complex game systems (terrain, economy, logistics, character management) in a coherent player interface.

### Strategic Purpose
- **Information Architecture Exploration**: How should terrain, economy, and character data be presented? What views do players need?
- **UI/UX Validation**: Test layout patterns, interaction models, and information density using design mockups with placeholders
- **Inspiration-Driven Design**: Study EVE Online (network/logistics UI), Civilization (terrain/production UI), SimEarth (world management), and SimCity (settlement building)
- **Future Integration Target**: Validated UI patterns will guide main game development; this prototype can eventually pull live game data for tuning
- **Design-First Approach**: Placeholders and mockups are acceptable — we're answering "how should this look?" not "does this work perfectly?"

---

## Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Framework** | Ruby on Rails 8 | Web application framework |
| **Styling** | Tailwind CSS | Utility-first CSS framework for dark sci-fi aesthetic |
| **Frontend interactivity** | Hotwire (Turbo + Stimulus JS) | Client-side interactions and mock data updates |
| **Data mocking** | In-memory controllers/helpers + static JSON | No database models, migrations, or workers |
| **Asset pipeline** | Importmaps / Propshaft | Rails 8 default module system |

---

## Architecture & Constraints

### Constraint 1: No Backend Dependencies
- ❌ NO database models, migrations, or ActiveRecord
- ❌ NO game engine workers or background jobs
- ✅ All data mocked in-memory within controller methods
- ✅ Static JSON files for reference data structures
- ✅ Mock data regenerated on each request

**Why**: This is a frontend prototype. Game logic and persistence live in the main Galaxy Game app.

### Constraint 2: Design Inspiration (Borrow Patterns, Not Domain)
- ✅ **Study EVE Dashboard** for: glassmorphic panels, dark theme colors, dense information tables, network visualization patterns, badge styling
- ✅ **Study Civilization/FreeCiv** for: terrain grid UI, resource display, production queue visualization, unit controls
- ✅ **Study SimCity** for: building placement interface, production chains, metrics dashboards
- ✅ **Use Galaxy Game terminology exclusively**: AI Manager, Facilities, Research, Blueprints, Settlements, Wormholes, ISK, AstroLift
- ✅ **Use existing assets** from galaxyGame: corporation logos (AstroLift, LDC, MDC, VDC), unit sprites, terrain tilesets

**Reference Materials**:
- EVE Dashboard: `app/static/style.css` (color palette, panels) + `/eve_dashboard/` (layout patterns)
- Corporation logos: `/galaxyGame/data/images/logos/` (AstroLift, LDC, MDC, VDC, Vector Hauling)
- Terrain tilesets: `/galaxyGame/docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` + `/TILESET_README.md`
- Galaxy Game domain: `/galaxyGame/docs/architecture/` (AI Manager, manufacturing, logistics systems)
- Color palette: Primary #00d4ff (cyan), Secondary #9d4edd (purple), Backgrounds #0a0a0a, #1a1a2e, Text #e0e0e0

---

## Project Structure

```
galaxy-game-ui/
├── app/
│   ├── controllers/
│   │   ├── dashboard_controller.rb       # Player command center
│   │   ├── wormholes_controller.rb       # Network topology
│   │   └── admin_controller.rb           # Telemetry & control
│   ├── views/
│   │   ├── dashboard/
│   │   ├── wormholes/
│   │   └── admin/
│   ├── helpers/
│   │   └── mock_data.rb                  # In-memory data factory
│   └── javascript/controllers/           # Stimulus controllers for interactivity
├── config/
│   ├── routes.rb                         # Route definitions
│   └── tailwind.config.js                # Tailwind customization (dark theme)
├── app/assets/stylesheets/               # Global styles + component library
├── spec/                                 # Tests (optional for prototype)
├── db/                                   # EMPTY — no migrations
└── README.md (this file)
```

---

## Core Features

### Feature 1: Player Dashboard (`/dashboard`)
---

## Three-View Architecture (Reference)

Galaxy Game features three distinct views of worlds — this prototype explores UI/UX for all three:

| View | Inspiration | Purpose | UI Status |
|------|-------------|---------|----------|
| **World View** | SimEarth | Planet-level overview, climate, global resources | Placeholder (deferred) |
| **Surface View** | Civilization/FreeCiv | Regional terrain, deposits, units, bases (main gameplay) | Task 2 mockup |
| **TerrainForge** | SimCity | Settlement detail building placement | Task 2 submenu |

---

## Core Features (UI Mockups + Placeholders)

### Feature 1: Player Dashboard (`/dashboard`)
**Purpose**: Character status screen — research, manufacturing, inventory, missions, trading

**Sections** (all mockups with placeholder data):
- **Location Header**: Settlement name, character name, ISK balance
- **Research Queue**: Blueprints in progress with progress bars and ETAs
- **Local Facilities**: Production buildings available at this settlement
- **Local Inventory**: Resources stored here with capacity bar
- **Manufacturing Queue**: Active production jobs with time remaining
- **Ships Panel**: Docked vs. In-Transit vessels
- **NPC Buy Orders**: What NPCs need (with profit potential)
- **Active Missions**: Escalated buy orders (optional player fulfillment with rewards)
- **Player Contracts**: Active agreements player has accepted

### Feature 2: Surface/Terrain View (`/wormholes` → navigation to Surface)
**Purpose**: Terrain exploration and resource network planning

**Sections** (all mockups, canvas placeholders OK):
- **Settlement Network Map**: Connected settlements with wormhole stability, decay timers, EM potential
- **Settlement Type Indicators**: Natural vs. Artificial wormholes (visual distinction)
- **AstroLift Routes**: Inter-settlement cycler network with pricing/capacity/ETAs
- **Route Planner**: Planned supply chains (player → settlement → AstroLift → destination)
- **Surface View Canvas**: Grid mockup showing terrain, resources, units, layer toggles (placeholder rendering OK)
- **TerrainForge Overlay**: Detail building placement interface (submenu)

### Feature 3: AstroLift Logistics + Admin Suite (`/admin`)
**Purpose**: Logistics coordination and system administration

**Player-Visible Sections**:
- **AstroLift Interface**: Book shipments, track in-flight cargo, pricing comparison
- **Market Trading**: View NPC buy/sell orders, post player offers

**Admin-Only Sections**:
- **AI Manager Control Panel**: Priority tuning (Harvester %, Market Import %, Expansion %), active directives
- **NPC Corporation Dashboard**: Status of AI-controlled entities (what they're harvesting, trading, producing)
- **Buy Order Management**: Status display (unfulfilled/mission/AI-fulfilling), creation and escalation timing
- **AstroLift Tuning**: Route pricing, capacity allocation, cycler scheduling
- **Digital Twin Sandbox**: Create scenarios, run simulations, compare outcomes (consequence-free terraforming)
- **Metrics Dashboard**: System-wide statistics, economic indicators, NPC performance

---

## Galaxy Game Domain Reference

Use ONLY Galaxy Game terminology and concepts:

| System | Concept | Definition | UI Display |
|--------|---------|-----------|-----------|
| **Character** | Settlement Location | Where player is physically located | Dashboard header |
| | Research Queue | Blueprints being researched (real-time progress) | Dashboard panel |
| | Blueprint | Crafting recipe (must be researched before manufacturing) | Research queue |
| | Manufacturing | 3-layer: Blueprint + Materials + Facility | Manufacturing queue |
| | Facility | Production building (Factory, Refinery, Lab, etc.) | Local facilities list |
| **Economy** | AI Manager | Autonomous controller of NPC corporations | Admin panel |
| | NPC Corporation | AI-controlled entity (AstroLift, LDC, MDC, VDC, Vector Hauling) | Corporation badges |
| | Buy Order | NPC request for items (player can fulfill) | Dashboard opportunities |
| | Mission | Escalated unfulfilled buy order (higher rewards) | Dashboard missions |
| | ISK | Standard currency | Account balance |
| **Logistics** | AstroLift | NPC logistics company operating cycler network | Shipping interface |
| | Cycler Route | Scheduled inter-settlement transport | Route planner |
| | Settlement | Base of operations with facilities, resources | Location selector |
| **Terrain** | Wormhole | Inter-settlement connection (Natural or Artificial) | Surface view |
| | Resource Deposit | Ore, crystal, rare materials awaiting harvest | Terrain overlay |
| | Unit | Harvester, Scout, Transport, etc. | Terrain visualization |
| | Exotic Matter (EM) | Rare resource with wormhole decay interaction | Wormhole stability |

**Note**: Use corporation logos from `/galaxyGame/data/images/logos/` (AstroLift.png, LDC.png, MDC.png, VDC.png, Vector Hauling.png) to make NPC economy visually distinct.

---

## Placeholder Design (This Is OK!)

For this UI prototype, **placeholders and mockups are acceptable outcomes**. Examples:

- **Surface View Canvas**: Simple HTML grid or canvas with placeholder terrain colors + layer toggles (no complex FreeCiv rendering required)
- **Terrain Details**: Static table of settlements, resources, units (no real map rendering)
- **Interactive Elements**: Stimulus JS buttons that show success toasts (no backend integration required)
- **Charts/Graphs**: Mock SVG/HTML displays with static data
- **Admin Tuning**: Priority sliders with mock update handlers (visual only)

**The goal is**: "How should information be organized and presented?" — not "Does this work perfectly?"

Later, when patterns are validated, live game data can be plugged in for real tuning.

---

## Development Workflow

### Local Setup
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui
rails db:setup  # Should be a no-op; no migrations expected
bin/rails s
# Visit http://localhost:3000
```

### Feature Development Order (Each Adds to Prior)
1. **Task 1**: Rails 8 initialization + enhanced MockData module (character, corporation, buy orders, missions, routes, admin data)
2. **Task 2**: Dashboard feature (research, facilities, inventory, manufacturing, buy orders, missions, contracts, ships)
3. **Task 3**: Wormhole/Surface feature (settlement network, AstroLift routes, supply planning, terrain view canvas mockup)
4. **Task 4**: Admin Suite feature (AI Manager tuning, NPC management, buy order tracking, Digital Twin sandbox)

Each task builds UI for one major system; all use MockDataHelper for data structure.

---

## Future: Live Game Data Integration

Once UI/UX patterns are validated, this prototype can gradually import live data from main Galaxy Game:

```ruby
# Example (future): Replace mock data with live queries
def character_data
  # MockDataHelper.character_at_settlement(:antollare)
  Character.find(session[:character_id])  # ← Eventually replace with this
end
```

This allows UI patterns to be tuned in a controlled sandbox before full integration.

---

## Success Criteria for Each Task

### Task 1 (Setup)
- ✅ Rails 8 app runs on localhost:3000
- ✅ Routes defined for all features
- ✅ MockDataHelper provides complete data structures
- ✅ Navigation bar renders (links to all features)

### Task 2 (Dashboard)
- ✅ All dashboard panels render with mock data
- ✅ Panels display research, facilities, inventory, manufacturing, buy orders, missions, ships
- ✅ Consistent styling using EVE Dashboard patterns
- ✅ Responsive layout (works on desktop)

### Task 3 (Wormhole/Surface)
- ✅ Settlement network displays as table/grid
- ✅ AstroLift routes visible with pricing/capacity
- ✅ Canvas/placeholder for terrain view (grid or simple rendering OK)
- ✅ Layer toggle mockups work (show/hide resource, unit overlays)

### Task 4 (Admin Suite)
- ✅ AI Manager priorities visible with slider mockups
- ✅ NPC corporation status displayed
- ✅ Buy order dashboard shows status
- ✅ Digital Twin scenario interface mockup (create/view scenarios)
- ✅ Styled consistently with Dashboard

### Overall
- ✅ All pages use Tailwind dark theme
- ✅ No database, migrations, or background jobs
- ✅ MockDataHelper is the single source of mock data
- ✅ Stimulus JS handles UI interactivity (mocked buttons, toasts, toggles)

### Migration Plan
After this prototype is complete and reviewed:
1. Extract Tailwind component library into main Galaxy Game app
2. Extract view templates and adapt them to main app structure
3. Replace mock data with actual API/service layer calls
4. Integrate with real game engine and database models
5. Archive this prototype repository

---

## Reference Docs
- **Galaxy Game Spec**: `../../notes/GALAXY_GAME_INTEGRATION_SPEC.md`
- **Workflow Guide**: `../../README.md` (Agent task workflow)
- **EVE Dashboard Styling Reference**: `/Users/tam0013/Documents/git/eve-dashboard/` (CSS patterns only)
  - Main stylesheet: `app/static/style.css` — color palette, panel effects, typography
  - Base layout: `app/templates/base.html` — topbar, navigation, responsive structure
- **CSS Reference Guide** (this project): `./EVE-DASHBOARD-CSS-REFERENCE.md` — Quick reference for Qwen on which CSS patterns to study and how to adapt them for Galaxy Game UI
- **WVU Moonshot Setup Reference** (this project): `./WVU-MOONSHOT-SETUP-REFERENCE.md` — Existing Rails 8 prototype showing what Docker, Gemfile, controllers, and layout structure can be reused/adapted. Study this for implementation patterns Qwen can leverage.
- **Galaxy Game UI Extraction Analysis** (this project): `./GALAXY-GAME-UI-EXTRACTION-ANALYSIS.md` — Existing galaxyGame repo UI patterns (colors, components, views, helpers, JavaScript) that can be extracted/adapted. Includes which files to study and what to copy vs. what to skip.

---

## Status Tracking
See [status.md](./status.md) for current progress, active tasks, and blocking issues.
