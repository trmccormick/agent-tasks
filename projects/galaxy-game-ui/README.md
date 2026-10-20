# Galaxy Game UI Prototype (Rails 8)

## Project Overview

**Galaxy Game UI** is a lightweight, standalone UI prototype for the Galaxy Game platform, built with **Ruby on Rails 8** and **Tailwind CSS**. This project serves as the design and interaction sandbox for the player-facing frontend before integration into the main Galaxy Game Rails 8 application.

### Strategic Purpose
- **Design validation**: Prototype and test UI/UX patterns without modifying the core game engine
- **Style reference**: Establish visual identity, component library, and interaction patterns
- **Decoupled development**: Frontend design can proceed in parallel with backend game logic development
- **Future migration target**: Code from this prototype will be migrated into the main Galaxy Game app after review and cleanup

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

### Constraint 2: Design vs. Domain Logic
- ✅ **Borrow ONLY**: Visual aesthetics, CSS styling, glassmorphic UI patterns, network node/route rendering concepts
- ❌ **Do NOT copy**: EVE Online gameplay mechanics, EVE terminology, or EVE-specific domain objects
- ✅ **Use exclusively**: Galaxy Game terminology (AI Manager, PVEs, EAP, Wormhole stations, ISK, GCC)
- ❌ **Do NOT reference**: EVE Online as a design authority — use Galaxy Game specs as the source of truth

**Reference Dashboard**: For styling and network visualization patterns only (see [EVE Dashboard](/Users/tam0013/Documents/git/eve-dashboard/) for CSS/layout inspiration). Study patterns from:
- `app/static/style.css` — Dark theme color palette, panel styling, glassmorphic effects
- `app/templates/base.html` — Layout structure, header/navigation patterns
- Color reference: `--bg: #0e1114`, `--panel: #161b21`, `--accent: #4aa8ff` (bright cyan)

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

### Feature 1: Settlement Command Center (`/dashboard`)
**Purpose**: Primary player interface for managing settlements and manufacturing economy

- **Player Summary Header**: Player name, primary settlement, ISK balance, manufacturing queue depth
- **Active Settlements Panel**: Card-based list of settlements under player control with production status (Growing/Stable/Crisis/Terraforming), growth rate, population, tax base in GCC
- **Manufacturing Queue**: Active production jobs showing: what's being fabricated, resource inputs (Regolith → Processed Materials → Components), time to completion, production location
- **Supply Flow Network**: Inter-settlement shipments via wormholes showing: origin settlement, destination settlement, cargo type, estimated arrival time

### Feature 2: Supply Route Planner (`/wormholes`)
**Purpose**: Inter-settlement network connectivity and logistics planning interface

- **Settlement Network Map**: List/table of connected settlements with wormhole type (Natural vs. Artificial), mass stability, decay timers, Exotic Matter (EM) potential
- **Settlement Type Indicators**: Visual distinction between Natural (decaying) and Artificial (persistent) wormholes
- **Route Planner**: Jump routes between settlements (e.g., ANTOLLARE III → Enedore → Jita Hub)
- **Shipment Tracking**: Active in-flight cargos with ETA and cargo capacity constraints

### Feature 3: Admin Digital Twin Sandbox (`/admin`)
**Purpose**: Consequence-free simulation environment for testing terraforming and settlement strategies

- **Digital Twin Management**: Create sandboxed clones of planets, manage multiple simulation scenarios
- **Terraforming Impact Planner**: Preview terraforming interventions with projected economic costs and planetary outcomes (atmosphere %, biosphere health)
- **Scenario Comparison**: Side-by-side comparison of simulation results to inform real-world strategy (Final Atmosphere %, Economic Impact, Terraform Time, Biosphere Health)
- **Sim Engine Event Log**: Scrolling mock feed of tick updates, extraction events, trade execution logs

---

## Galaxy Game Domain Reference

When building this prototype, use ONLY these domain concepts from Galaxy Game:

| Concept | Definition | Used In |
|---------|-----------|---------|
| **AI Manager** | Autonomous game economy director | Admin Panel |
| **PVE Array** | Planetary Volatiles Extractor unit | Dashboard units |
| **EAP** | Earth Anchor Price — baseline commodity pricing | Dashboard status |
| **ISK / GCC** | In-game currency (ISK = standard, GCC = premium) | Dashboard balance |
| **Wormhole Stations** | Artificial transit hubs vs. natural wormholes | Wormholes feature |
| **Exotic Matter (EM)** | Rare resource with wormhole decay interaction | Inventory & Wormholes |
| **Compressed Blue Ice, Oxygen Isotopes, Heavy Water** | Key commodity resources | Inventory grid |

---

## Development Workflow

### Local Setup
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui
rails db:setup  # Should be a no-op; no migrations expected
bin/rails s
# Visit http://localhost:3000
```

### Feature Development Order
1. **Task 1**: Project initialization (Rails 8 setup, routes, mock data module)
2. **Task 2**: Dashboard feature (templates, mock data, styling)
3. **Task 3**: Wormhole Network feature (templates, network visualization, mock routes)
4. **Task 4**: Admin Panel feature (telemetry views, mock event log, control buttons)

Each task is self-contained but builds on the foundation established in Task 1.

---

## Review & Migration Strategy

### Pre-Migration Checklist
- [ ] All three features functional with mock data
- [ ] Styling consistent across all pages (Tailwind dark theme)
- [ ] No database or model references
- [ ] No background job workers or async logic
- [ ] Accessibility baseline met (WCAG 2.1 level A)

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
