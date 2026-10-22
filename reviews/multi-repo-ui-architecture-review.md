# Multi-Repository UI Architecture Review

**Date**: 2026-10-21
**Agent**: Planning Agent (analysis only)
**Scope**: Three repositories compared; architectural assessment for Galaxy Game vision

---

## 1. Repository Comparison

### 1.1 galaxyGame (`/Users/tam0013/Documents/git/galaxyGame`)

| Aspect | Details |
|--------|---------|
| **Framework** | Rails (Ruby) |
| **Styling** | Inline CSS; green-on-black terminal/RPG aesthetic (#00ff00 on #000011) |
| **JS** | JavaScript_importmap_tags; sidebar.js for collapsible panels |

**Layout Structure:**
- `app/views/layouts/application.html.erb` — Minimal body wrapper, no nav, no footer. Bare `<%= yield %>`

**View Structure (27 subdirectories under `views/`):**
- `admin/` — 10 sections: AI Manager, Solar Systems, Celestial Bodies, Map Studio, Organizations, Resources & Market, Simulation, Settlements, Digital Twins, Galaxies, Census
- `landing/` — index.html.erb (hero + status cards + activity feed), about.html.erb
- `game/` — index.html.erb (main game console), simulation.html.erb, celestial_body_detail.html.erb
- `simulation/` — index.html.erb
- `celestial_bodies/`, `solar_systems/`, `terrestrial_planets/`, `stars/` — Rails scaffold views (index/show/new/edit)

**Landing Page (`landing#index`):**
- Hero section: logo + title + auth buttons (Sign In / Create Account — currently both link to landing#index as placeholders)
- Status grid: 4 cards (AI Decisions, Star Systems, Economy, Expansion) with real game data
- Active systems grid: system cards with settlement/body counts
- Recent AI activity feed: type-filtered items with icons
- CTA section

**Admin Dashboard (`admin/dashboard/index.html.erb`):**
- Dense admin control center layout: header + sidebar + main content area
- Sidebar sections: Admin Sections (7 links), Quick Actions (3 buttons), Quick Stats
- Main content: galaxy overview stats, recent events, system metrics, active plugins
- 2-column CSS grid: `grid-template-columns: 250px 1fr`
- Dark theme with accent colors

**Game View (`game#index`):**
- Terminal/RPG aesthetic: black background, green monospace text (#00ff00)
- Header bar: game title + time display + control buttons (play/pause, fast-forward)
- Three-panel layout: sidebar (stats/controls), main content (SOL system view), right panel
- Full-screen game canvas area

**Route Structure:**
```
GET  /                      → landing#index         (public)
GET  about                  → landing#about         (public)
GET  users/sign_in          → landing#index (placeholder)
GET  users/sign_up          → landing#index (placeholder)
GET  simulation             → simulation#index      (game play)
GET  game                   → game#index            (main game)
GET  game/simulation/:id    → game#simulation       (detail)
GET  game/celestial_body/:id → game#celestial_body_detail
POST game/toggle_running    → game#toggle_running   (time control)
POST game/set_speed         → game#set_speed        (time control)
POST game/jump_time         → game#jump_time        (time control)
GET  game/state             → game#state            (AJAX)
GET  materials/:name        → materials#show        (resource lookup)
GET  /solar_systems         → solar_systems#index   (public list)
GET  /solar_systems/:id     → solar_systems#show    (public detail)
GET  /celestial_bodies      → celestial_bodies#index
GET  /celestial_bodies/:id  → celestial_bodies#show + map/geological_features
GET  /stars                 → stars#index/show
GET  /terrestrial_planets   → terrestrial_planets#index/show
GET  /sidekiq               → Sidekiq web UI
```

**Intended Audience:**
- Public visitors: landing page, about, solar system listings
- Players: game simulation, game control, celestial body exploration
- Admins: full admin control center (10 sections)

---

### 1.2 eve-dashboard (`/Users/tam0013/Documents/git/eve-dashboard`)

| Aspect | Details |
|--------|---------|
| **Framework** | Python FastAPI |
| **Templates** | Jinja2 |
| **Styling** | Custom `static/style.css` with multiple themes (including stealth mode) |
| **Data Sources** | ESI API (EVE Online), local SQLite DB, zKillboard, PLEX price APIs |
| **Name** | "Pixel Nexus" (generic branding, not EVE-specific) |

**Layout Structure:**
- `base.html` with Jinja2 blocks: `content`, `title`, `toolbar`, `refresh`, `head`
- Topbar navigation: brand/logo + toolbar (page-specific) + refresh button + alerts bell
- Collapsible sidebar navigation with feature toggles
- Body classes: `navpos-{top/left/right}` for configurable nav position
- Auto-refresh system: `<span data-live="last-sync">` compares client vs server sync times
- Theme support via `data-theme` attribute on `<html>` element

**Template Structure (30+ pages):**
- **Dashboard** (`dashboard.html`): overview stats, calendar strip, wealth chart, top net worth rankings, notifications by corporation, character table with ISK/assets/SP/training data
- **Kanban** (`kanban.html`): mission/task board with columns
- **Fleet** (`fleet.html`, `fleet_destinations.html`): fleet tracking and map with destination planning
- **Market** (`market.html`): PLEX pricing, commodity market data
- **PI Planner** (`pi.html`, `pi_colonies.html`, `pi_cards.html` etc.): Planetary Interaction planner
- **Zkillboard** (`zkill.html`): kill feed with advanced search
- **Homefronts** (`homefronts.html`): site completion tracker with SVG charts
- **Wealth** (`wealth.html`): net worth breakdown with multi-account support
- **Notes/Mail/Calendar**: EVE-specific organization tools
- **Structures** (`structures.html`): structure management

**Navigation Features:**
- Draggable notification columns (reorderable corp alerts)
- Drag-and-drop UI elements
- Feature toggle system via `prefs.ordered_features()`
- Contextual sub-navigation per page
- "Customise" buttons on panels for content selection

**Dashboard Layout (`dashboard.html`):**
- Panel-row architecture: wide panels, side-by-side charts
- SVG-based charts (homefront bar chart, wealth line chart)
- Character table with sortable columns
- Notification grid: 3-column flex layout with corp logos
- Auto-sync badge and refresh countdown timer

**Route Structure (FastAPI):**
```
/                              → dashboard.html          (player overview)
/add                           → add.html                 (add account/character)
/settings                      → settings.html            (preferences/theme/nav)
/guide                       → guide.html + guides/*     (documentation)
/fleet, /fleet_destinations   → fleet tracking pages
/market                      → market data page
/zkill, /zkill_query           → zKillboard integration
/pi, /pi_colonies, /pi_cards  → PI planner pages
/kanban, /kanban/calendar     → kanban board
/homefronts                   → site completion tracker
/wealth                       → wealth breakdown
/structures                   → structure management
/watcher                      → watcher client page
/mail                         → mail organizer
/notes                        → EVE notes (in-game + EVE universe)
/agenda                       → mission agenda
/events                       → in-game events
/wallet_archive               → wallet history archive
```

**Intended Audience:**
- EVE Online players: everything is player-facing
- Multi-account support with character-level stats
- Corporate corp-aligned alert notifications

---

### 1.3 galaxy-game-ui (`/Users/tam0013/Documents/git/galaxy-game-ui`)

| Aspect | Details |
|--------|---------|
| **Framework** | Rails (Ruby) |
| **Styling** | Tailwind CSS + custom theme tokens (`bg-galaxy-dark`, `text-galaxy-accent` etc.) |
| **JS** | Importmap; Vite/ESM modules |
| **Auth** | Devise planned: `resource :session`, `resources :passwords`; `require_authentication` before_action |

**Layout Structure:**
- `app/views/layouts/application.html.erb`:
  - Fixed top nav bar (border-border-galaxy-accent/30, bg-galaxy-panel)
  - Nav links: Dashboard, Wormholes, Admin
  - Main content wrapper: `<main class="max-w-7xl mx-auto px-4 py-8">` with yield
  - Body class: `bg-galaxy-dark text-galaxy-text`

**Controller Structure:**
- `DashboardController#index` — requires auth (mock data)
- `WormholesController#index` — skip_auth (public transit view)
- `AdminController#index` — standard auth required
- `SessionsController` — login flow
- `PasswordsController` — password reset

**View Structure (minimal):**
```
app/views/
  dashboard/
    index.html.erb              (player command center)
  wormholes/
    index.html.erb              (transit/logistics view)
  admin/
    index.html.erb              (administration overview)
  sessions/
    new.html.erb                (login form)
  passwords/
    new.html.erb, edit.html.erb (password reset flow)
```

**Dashboard (`dashboard#index`):**
- Mock data for: system status, bay inventory, player ships, NPC buy orders, active missions, market quick panel
- `skip_before_action :require_authentication` set but likely overridden by middleware
- Intended audience: logged-in players only (comment says "player-only command center")

**Wormholes (`wormholes#index`):**
- Mock data for: wormhole network, transit routes
- No auth required (`skip_before_action`)
- Intended audience: all users including public

**Admin (`admin#index`):**
- Mock data for: AI manager status, economic indices, simulation event log
- Standard auth required
- Intended audience: administrators only

**Route Structure:**
```
GET  /                  → dashboard#index (redirects to dashboard)
GET  /dashboard         → dashboard#index (player command center)
GET  /wormholes         → wormholes#index (public transit view)
GET  /admin             → admin#index     (admin interface)
POST /session           → sessions#create (login)
DELETE /session         → sessions#destroy (logout)
GET  /passwords/new     → passwords#new   (forgot password)
PUT  /passwords/:id     → passwords#update (reset password)
GET  /up                → health check
```

**Intended Audience:**
- Public: wormholes transit view
- Players: dashboard, wormholes
- Admins: admin interface

---

## 2. Cross-Repository Comparison Matrix

### 2.1 Layout Structure

| Dimension | galaxyGame | eve-dashboard | galaxy-game-ui |
|-----------|-----------|---------------|----------------|
| Nav pattern | None (bare yield) | Topbar + collapsible sidebar | Fixed top nav bar |
| Sidebar | Game view: left panel; Admin: left 250px | Configurable (top/left/right) via prefs | No sidebar |
| Panel system | Inline CSS grid/float per page | `pane-row`, `pane`, side-by-side panels | No panel system yet |
| Theme | Terminal green/black only | Multi-theme with stealth mode | Tailwind galaxy tokens |
| Branding | "GalaxyGame" in title only | "Pixel Nexus" in topbar | Title: "Galaxy Game UI Prototype" |

**Assessment:** galaxy-game-ui lacks a proper navigation pattern. A fixed top nav (like current) is insufficient for complex multi-section apps. Sidebar or tabbed navigation needed for scalability.

### 2.2 View Structure

| Dimension | galaxyGame | eve-dashboard | galaxy-game-ui |
|-----------|-----------|---------------|----------------|
| Scope | 27 view directories, broad coverage | 30+ Jinja2 templates, deep coverage | 3 views total (index-only per controller) |
| Depth | Admin has full CRUD; game has simulation/detail | Each page has rich data panels + charts | All mock data placeholders |
| Organization | Feature-siloed under views/ | Flat (all in app/templates/) | One-view-per-controller convention |

**Assessment:** galaxy-game-ui is at initial scaffolding stage. galaxyGame has the most complete Rails view structure. eve-dashboard has the richest per-page content depth.

### 2.3 Navigation Structure

| Dimension | galaxyGame | eve-dashboard | galaxy-game-ui |
|-----------|-----------|---------------|----------------|
| Nav type | None on main pages; admin sidebar links only | Topbar + fully navigable sidebar with feature toggles | Top nav bar (3 links) |
| Dynamic behavior | Collapsible sidebar in game view | Toggle-able features, draggable columns, configurable nav position | Static HTML nav |
| Context switching | URL-based; no persistent nav state | Per-user prefs persist nav configuration | No persistence |

**Assessment:** eve-dashboard has the most sophisticated navigation with per-user feature toggles and configurable layouts. galaxy-game-ui's 3-link top nav is adequate for current scope but won't scale.

### 2.4 Information Architecture

| Dimension | galaxyGame | eve-dashboard | galaxy-game-ui |
|-----------|-----------|---------------|----------------|
| Primary hierarchy | Game world data (systems → bodies → details) | Player portfolio (accounts → characters → stats) | Undeveloped (placeholder pages only) |
| Secondary | AI decisions, simulation state, settlements | ISK wealth, market prices, fleet positions, kill records | System status, missions, wormholes |
| Audience segmentation | Public / player / admin clearly separated | Player-facing only (multi-account is feature, not audience) | Public / player / admin intent stated but unimplemented |

**Assessment:** galaxy-game-ui's IA is aspirational. No information hierarchy exists yet beyond the route names. galaxyGame separates audiences well; eve-dashboard has deep player-centric IA.

### 2.5 Landing Page Design

| Dimension | galaxyGame | eve-dashboard | galaxy-game-ui |
|-----------|-----------|---------------|----------------|
| Exists? | Yes (landing#index) | No — root goes to dashboard | No — root redirects to /dashboard |
| Hero section | Logo + tagline + auth CTA | N/A (no public page) | N/A |
| Status display | 4-card grid with live game data | Dashboard is the landing for authenticated users | Redirects away immediately |
| Activity feed | Recent AI activity by type | Calendar strip, notifications, top charts | None |
| Systems showcase | Active systems grid with counts | N/A | None |

**Assessment:** galaxy-game-ui should adopt galaxyGame's landing page pattern: hero + status cards + activity feed. This is the strongest public-facing design of all three repos.

### 2.6 Dashboard Design

| Dimension | galaxyGame (admin) | eve-dashboard (dashboard.html) | galaxy-game-ui (placeholder) |
|-----------|-------------------|-------------------------------|------------------------------|
| Layout | 2-col grid (250px sidebar + main) | Panel-row: side-by-side SVG charts, character tables, notification grid | Undeveloped |
| Content depth | High: galaxy overview + events + metrics + plugins | Very high: wealth/ISK/assets, top rankings, calendar, notifications, characters | Placeholder with mock data references |
| Data visualization | Stats cards, event timeline | SVG charts (bar for homefront, line for wealth), rank tables | None yet |
| Admin/player split | Admin-only dashboard | Player-only dashboard | Both are separate pages but both empty |

**Assessment:** eve-dashboard's dashboard is the gold standard for data density and visualization quality. galaxyGame's admin dashboard has good structural organization (sidebar → sections → stats). galaxy-game-ui needs a complete dashboard design from scratch.

### 2.7 Admin Design

| Dimension | galaxyGame | eve-dashboard | galaxy-game-ui |
|-----------|-----------|---------------|----------------|
| Dedicated admin? | Yes (10-section admin) | No (all player-facing) | Yes (admin#index) |
| Admin scope | AI Manager, solar systems, celestial bodies, map studio, organizations, settlements, resources, simulation, digital twins, galaxies, census | N/A | AI manager status, economic indices, event log |
| Visual complexity | Dense: sidebar + header + main content area with stats and events | N/A | Minimal (3 mock data blocks) |

**Assessment:** galaxyGame's admin has the most comprehensive admin panel concept. It should inform what goes into galaxy-game-ui's `/admin` in the future.

### 2.8 Reusable UI Concepts

| Concept | Source | Applicability to Galaxy Game |
|---------|--------|------------------------------|
| Landing page: hero + status cards + activity feed | galaxyGame | **HIGH** — Directly fits Galaxy Game public-facing needs |
| Panel-row architecture for dashboard layouts | eve-dashboard | **HIGH** — Side-by-side data panels are ideal for command center |
| SVG chart patterns (bar, line) for wealth/status visualization | eve-dashboard | **MEDIUM** — Would be useful but requires JS investment |
| Admin sidebar + sections + stats pattern | galaxyGame admin | **HIGH** — Scalable admin navigation |
| Terminal/RPG game aesthetic | galaxyGame game view | **LOW-MEDIUM** — Stylistic choice; may not fit Galaxy Game vision |
| Topbar + collapsible sidebar nav | eve-dashboard | **MEDIUM** — Good for complex apps; may be overkill initially |
| Auto-refresh sync badges | eve-dashboard | **LOW** — Nice-to-have but not architectural |
| Feature toggle system | eve-dashboard prefs | **LOW** — Advanced UX pattern, not needed in prototype |
| Galaxy-themed dark UI (Tailwind custom tokens) | galaxy-game-ui | **HIGH** — Already aligned with vision; keep and extend |

---

## 3. Galaxy Game Gap Analysis

### 3.1 Route Structure Assessment

#### Question 1: Should "/" be a public landing page?

**Current:** Redirects to `/dashboard` (player-only, requires auth)
**Recommendation:** **YES — "/" should be the public landing page.**

Evidence from galaxyGame shows this pattern works well:
- Hero section establishes game identity and setting
- Status cards show live world state (encourages engagement)
- Activity feed demonstrates dynamism ("other players are active")
- Auth CTA converts visitors to accounts

galaxy-game-ui currently has `root "dashboard#index"` — this creates a login wall at the root URL, which is incorrect for a game that needs public visibility.

**Gap:** No landing page exists. The app has no public face. This must be built.

#### Question 2: Should "/dashboard" be a player-only command center?

**Current:** Yes (routes to `DashboardController#index` with mock data)
**Recommendation:** **YES — but the current implementation is empty.**

galaxyGame's game view demonstrates what a player command center could look like (simulation controls, celestial body info). eve-dashboard demonstrates data density patterns (charts, stats grids, notifications).

**Gaps:**
- Dashboard has mock data references but no rendered UI content
- No panel system for organizing player information
- No sidebar for navigation between player sub-sections
- No visualization components (charts, status indicators)

#### Question 3: Should "/admin" be a separate administration interface?

**Current:** Yes (routes to `AdminController#index` with mock data)
**Recommendation:** **YES — admin should be separate and protected.**

galaxyGame has the most mature admin concept with 10 distinct sections. galaxy-game-ui currently has only 3 mock data blocks on admin#index.

**Gaps:**
- Admin lacks sidebar navigation for its own sub-sections
- No admin panel system for status cards/metrics
- No visual distinction between admin and player views (they look identical)
- Missing auth middleware enforcement

#### Question 4: Should "/wormholes" be a transit/logistics view?

**Current:** Yes (routes to `WormholesController#index`, public, no auth)
**Recommendation:** **YES — this is well-conceived but empty.**

galaxyGame has celestial body detail pages and map views that could inform the wormholes transit concept. The route placement as a top nav link is appropriate for a player utility view.

**Gaps:**
- No visual layout (just an index.html.erb placeholder)
- No network visualization (transit maps, route cards)
- No interactive elements (filtering, sorting, detail views)
- No distinction between wormhole types/connections

### 3.2 EVE-Style Companion Dashboard Assessment

**Does galaxy-game-ui currently resemble an EVE-style companion dashboard?**

**NO.** The current implementation is:
- A bare Rails scaffold with placeholder controllers
- Three index pages with no visual content (just mock data setup in controllers)
- A generic top nav bar with no styling identity beyond dark theme tokens
- No panel system, no charts, no visualization
- No sidebar navigation for either admin or player sections

**What would make it EVE-style:**
1. **Data density**: Side-by-side panels with real-time status indicators
2. **Visualization**: SVG/canvas charts for resource trends, fleet positions, market data
3. **Character/account management**: Multi-character dashboard (if Galaxy Game supports party systems)
4. **Notification system**: Event-driven alerts in the topbar
5. **Auto-refresh patterns**: Background sync with visual indicators

**What should NOT be EVE-style:**
1. EVE-specific APIs (ESI, zKillboard, market data)
2. EVE-specific terminology (ISK, PLEX, PI, SDE)
3. Corporate alliance tracking
4. Character skill point management

The *pattern* is good; the *content* and *terminology* are not.

### 3.3 Key UI Concepts Missing from galaxy-game-ui

| Missing Concept | Source Reference | Priority | Why Needed |
|----------------|-----------------|----------|------------|
| Public landing page with hero + status cards | galaxyGame `landing#index` | **CRITICAL** | No public face; root redirects to auth wall |
| Panel-row dashboard layout | eve-dashboard | **CRITICAL** | Current dashboard has zero visual content |
| Admin sidebar navigation | galaxyGame admin | **HIGH** | Admin needs its own section organization |
| SVG data visualization patterns | eve-dashboard `wealth.html` + `homefronts.html` | **HIGH** | Dashboard needs charts for status reporting |
| Navigation state management | eve-dashboard `prefs.rb` | **MEDIUM** (later) | For scalable nav beyond 3 links |
| Notification system with badges | eve-dashboard topbar | **MEDIUM** | Event-driven alerts for player/admin |
| Auth-aware view rendering | galaxyGame auth placeholders | **HIGH** | Some pages need auth, some don't |
| Admin/player visual distinction | Both admin dashboards differ from game views | **HIGH** | Users must visually know which mode they're in |
| Wormhole network visualization | galaxyGame `celestial_bodies/map.html.erb` pattern | **HIGH** | Transit view needs maps/networks, not tables |
| Responsive/mobile layouts | Not explicitly checked but implied by viewport meta tags | **LOW** (later) | Accessibility requirement for modern apps |

---

## 4. Recommended Route Structure

Based on analysis of all three repos and the Galaxy Game vision:

```
# === PUBLIC ROUTES (no auth required) ===
GET  /                    → landing#index         # Public landing page (hero + world status)
GET  about                → landing#about         # About Galaxy Game
GET  wormholes            → wormholes#index        # Public transit network view
GET  galaxy               → galaxy#index           # Galaxy overview/explorer (public catalog)

# === PLAYER ROUTES (auth required) ===
GET  /dashboard           → dashboard#index        # Player command center (primary entry point)
GET  /dashboard/fleet     → dashboard/fleet#index  # Fleet/crew management
GET  /dashboard/inventory → dashboard/inventory#index  # Bay/shipping inventory
GET  /dashboard/market    → dashboard/market#index   # NPC market orders
GET  /dashboard/missions  → dashboard/missions#index # Active missions
GET  /wormholes/:id       → wormholes#show         # Individual wormhole detail

# === ADMIN ROUTES (admin auth required) ===
GET  /admin               → admin#index            # Admin overview hub
GET  /admin/ai-manager    → admin/ai_manager#index # AI Manager configuration
GET  /admin/solar-systems → admin/solar_systems#index   # System management
GET  /admin/bodies        → admin/celestial_bodies#index  # Celestial body management
GET  /admin/settlements   → admin/settlements#index     # Settlement management
GET  /admin/simulation    → admin/simulation#index      # Simulation controls
```

### Route Design Principles:
1. **`/` is public** — establishes game identity, shows world state, converts visitors
2. **`/dashboard` is the player hub** — all player activities accessible from here (sub-panels)
3. **`/wormholes` has both public + private states** — public for network overview; authenticated for detail/trading
4. **`/admin` is a separate mode** — visually distinct from player views, admin-only routes under it
5. **Sub-resources are paths not controllers** — `/dashboard/fleet` not `fleet#index` (keeps nav hierarchy clear)

---

## 5. Recommended Layout/View Structure

### 5.1 Layout Architecture

```
app/views/layouts/
  application.html.erb          # Shared wrapper with nav bar
  admin.html.erb               # Admin-specific layout (different visual style)
  mailer.*                     # Email templates (existing)
```

#### `application.html.erb` (player/public):
```
┌─────────────────────────────────────────────┐
│ NAV BAR: Logo | Dashboard | Wormholes | Galaxy │
├─────────────────────────────────────────────┤
│                                             │
│              MAIN CONTENT (yield)           │
│                                             │
└─────────────────────────────────────────────┘
```

- Top nav bar: dark galaxy theme, fixed width max-w-7xl
- Nav links highlight active section
- Right side: user avatar/menu (when authenticated)

#### `admin.html.erb` (admin):
```
┌─────────────────────────────────────────────┐
│ ADMIN HEADER: Admin Control Center | Logout  │
├──────────┬──────────────────────────────────┤
│ SIDEBAR  │            MAIN CONTENT          │
│          │                                  │
│ Sections │     Yield (cards, tables, etc)   │
│ - AI Mgr │                                  │
│ - Systems│                                  │
│ - Bodies │                                  │
│ ...      │                                  │
├──────────┴──────────────────────────────────┤
│ FOOTER: System status | version | uptime    │
└─────────────────────────────────────────────┘
```

- 2-column grid (250px sidebar + fluid main) — pattern from galaxyGame admin
- Different color palette (admin purple vs player blue accent)
- Quick stats panel in sidebar footer

### 5.2 View Directory Structure

```
app/views/
  layouts/
    application.html.erb          ← Player/public wrapper
    admin.html.erb               ← Admin wrapper
  landing/
    index.html.erb               ← Hero + status cards + activity feed
    about.html.erb               ← About page
  dashboard/
    index.html.erb               ← Player command center (panel grid)
    _status_panel.html.erb       ← System status card partial
    _bay_inventory_panel.html.erb← Bay/ships inventory panel
    _missions_panel.html.erb     ← Active missions panel
    _market_panel.html.erb       ← Market quick panel
  wormholes/
    index.html.erb               ← Wormhole network overview (map visualization)
    show.html.erb                ← Individual wormhole transit details
  admin/
    index.html.erb               ← Admin hub (sidebar + stats grid)
    ai_manager/
      index.html.erb
    solar_systems/
      index.html.erb
    celestial_bodies/
      index.html.erb
  sessions/
    new.html.erb                 ← Login form
  passwords/
    new.html.erb, edit.html.erb  ← Password reset flow
  galaxy/
    index.html.erb               ← Public galaxy explorer/catalog
```

### 5.3 Dashboard Panel System (Prototype)

The player dashboard should use a panel grid pattern (inspired by eve-dashboard):

```html
<!-- app/views/dashboard/index.html.erb -->
<div class="dashboard-grid">
  
  <!-- Top row: World status overview (full width) -->
  <div class="panel status-panel" data-panel="world-status">
    <h2>Galaxy Status</h2>
    <div class="status-cards">
      <div class="card"><strong><%= @system_status[:star_systems] %></strong> Systems</div>
      <div class="card"><strong><%= @system_status[:settlements] %></strong> Settlements</div>
      <div class="card"><strong><%= @system_status[:ai_decisions] %></strong> AI Decisions Today</div>
    </div>
  </div>

  <!-- Middle row: Side-by-side panels -->
  <div class="panel-row">
    <div class="panel half" data-panel="bay-inventory">
      <h2>Fleet Bay Inventory</h2>
      <% @bay_items.each do |item| %>
        <!-- placeholder item row -->
      <% end %>
    </div>
    <div class="panel half" data-panel="missions">
      <h2>Active Missions</h2>
      <% @missions.each do |mission| %>
        <!-- placeholder mission row -->
      <% end %>
    </div>
  </div>

  <!-- Bottom row: Secondary panels -->
  <div class="panel-row">
    <div class="panel third" data-panel="player-ships">
      <h2>Your Ships</h2>
      <!-- placeholder ship list -->
    </div>
    <div class="panel third" data-panel="market">
      <h2>Market Overview</h2>
      <!-- placeholder market summary -->
    </div>
    <div class="panel third" data-panel="notifications">
      <h2>Recent Events</h2>
      <!-- placeholder event list -->
    </div>
  </div>

</div>
```

**Panel CSS pattern:**
- `panel` = bordered container with header (bg-galaxy-panel, border-galaxy-accent/30)
- `panel-row` = flex or grid for horizontal arrangement
- `half` / `third` = fractional widths
- `data-panel-*` = identifies panels for future JS interactivity

---

## 6. Recommended Prototype Architecture

### 6.1 Core Principle

**The prototype consists entirely of correct structure + placeholder content.** No real game logic, no API integrations, no database queries. Every panel shows mock data or static text.

### 6.2 What the Prototype Establishes (Not Implements)

| Layer | What It Defines | Not What It Does |
|-------|----------------|------------------|
| Layouts | Visual hierarchy for each mode (player/public/admin) | Connect to data sources |
| Routes | Information architecture and navigation flow | Provide real functionality |
| Navigation | How users move between sections | Handle auth state |
| Panels | Data grouping, card layouts, grid patterns | Display live data |
| Placeholders | Visual structure of every page | Process or modify data |

### 6.3 Prototype Deliverable Checklist

#### Layout Files (3 files):
- [x] `layouts/application.html.erb` — Player/public layout with top nav
- [x] `layouts/admin.html.erb` — Admin layout with sidebar
- [ ] *(New)* No additional layouts needed if these two cover all modes

#### Route Structure (in `routes.rb`):
- [x] `root "landing#index"` — Public landing page
- [x] `/dashboard` → dashboard controller (auth required)
- [x] `/wormholes` → wormholes controller (public)
- [x] `/admin` → admin controller (admin auth)
- [ ] Add sub-panels: `/dashboard/fleet`, `/dashboard/missions`, etc.
- [ ] Add `galaxy` index for public galaxy catalog

#### View Files (placeholder content):
**Landing:**
- [ ] `landing/index.html.erb` — Hero section, 4 status cards, activity feed, CTA
- [ ] `landing/about.html.erb` — Static about page

**Dashboard:**
- [ ] `dashboard/index.html.erb` — Panel grid with placeholder panels
- [ ] Partial: `_status_panel.html.erb`, `_bay_inventory_panel.html.erb`, etc.

**Wormholes:**
- [ ] `wormholes/index.html.erb` — Network overview with placeholder map/links
- [ ] `wormholes/show.html.erb` — Individual wormhole detail panel

**Admin:**
- [ ] `admin/index.html.erb` — Admin hub with sidebar + stats grid (placeholder data)

**Galaxy (new):**
- [ ] `galaxy/index.html.erb` — Public galaxy catalog/listing of star systems

### 6.4 Placeholder Content Guidelines

All placeholder content should follow these rules:

1. **Mock data only**: Use existing `MockDataHelper` or hardcoded arrays
2. **No real calculations**: Status values are static strings or mock integers
3. **No form submissions**: Links and buttons have `href="#"` or `data-placeholder="true"`
4. **Visual fidelity over function**: Panels must look like the final version structurally, but show "Loading..." or "5 items" style text
5. **Class-based identification**: Every panel has `data-panel-*` attributes for future JS hooks

### 6.5 Visual Hierarchy (Prototype)

```
VISUAL PRIORITY (top to bottom):
┌──────────────────────────────────────┐
│ LANDING PAGE:                        │
│   1. Hero section (full width, dark) │ ← First impression
│   2. Status cards (4-column grid)    │ → World state
│   3. Activity feed (scrollable list) │ → Engagement signal
│   4. CTA button                      │ → Conversion
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│ PLAYER DASHBOARD:                    │
│   1. Top row: world status (full)    │ → Immediate awareness
│   2. Middle: fleet + missions (half) │ → Primary actions
│   3. Bottom: ships + market + events  │ → Secondary info
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│ WORMHOLES:                           │
│   1. Network overview (map/visual)   │ → Pattern recognition
│   2. Filter bar (placeholder)        │ → Interaction affordance
│   3. Route cards (grid/list)         │ → Selection
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│ ADMIN:                               │
│   1. Sidebar navigation              │ → Section selection
│   2. Stats row (top of main)         │ → Quick awareness
│   3. Content area (tables/cards)     │ → Detail management
└──────────────────────────────────────┘
```

---

## 7. Suggested Task Breakdown for Future Implementation

### Phase 1: Foundation (Prototype — Structure Only)
1. **Create landing page** — Hero + status cards + activity feed + CTA (static/mock data)
2. **Expand dashboard panels** — Panel grid system with all placeholder panels
3. **Complete wormholes views** — Network overview + detail view (visual structure only)
4. **Build admin layout** — Sidebar + stats grid + content area (mock data)
5. **Add galaxy index page** — Public star system catalog (static list/grid)

### Phase 2: Structure Completion (Still No Real Logic)
6. **Implement sub-panel navigation** — Dashboard → Fleet, Missions, Market, Inventory views
7. **Add admin sub-sections** — AI Manager, Solar Systems, Celestial Bodies, etc.
8. **Create panel partial system** — Reusable status card, table, and chart container components
9. **Add navigation state** — Active link highlighting + breadcrumb support

### Phase 3: Integration Foundation (Pre-Data)
10. **Connect routes to real controllers** — Replace mock data with model lookups (still static)
11. **Add form structures** — Login forms, filter UIs, detail action buttons (non-functional)
12. **Create notification system UI** — Topbar bell badge + dropdown panel (hardcoded items)

### Phase 4: Functionality Implementation (Future)
*Not part of this review scope — these come AFTER prototype structure is validated.*

---

## 8. Summary of Key Findings

### What Is Good (Keep):
- ✅ **Galaxy-themed dark UI** with Tailwind custom tokens — aligned with vision
- ✅ **Route separation** (dashboard/player, wormholes/transit, admin) — correct concept
- ✅ **Controller structure** — clean separation of concerns
- ✅ **Auth architecture** (`require_authentication`, `skip_before_action`) — properly planned

### What Needs to Change:
- ❌ **`/` should NOT redirect to `/dashboard`** — root must be a public landing page
- ❌ **Dashboard has zero visual content** — needs panel grid system with placeholder panels
- ❌ **No admin-specific layout** — admin and player views look identical currently
- ❌ **Wormholes is just a controller skeleton** — needs network visualization structure
- ❌ **No galaxy explorer page** — public catalog of star systems should exist

### What Should Be Reused from Other Repos:
- From **galaxyGame**: landing page hero+cards pattern, admin sidebar+stats pattern, celestial body detail structure
- From **eve-dashboard**: panel-row architecture, SVG chart patterns for data visualization, notification system structure

### What Should NOT Be Reused:
- ❌ EVE-specific terminology (ISK, PLEX, character SP)
- ❌ EVE-specific APIs (ESI, zKillboard endpoints)
- ❌ Corporate alliance tracking UI
- ❌ Terminal/RPG green-on-black aesthetic for player UI (save for simulation/game mode only)

### Critical Priority Items:
1. **Landing page** is the single highest-priority gap — without a public face, the app has no visibility
2. **Dashboard panel system** must be built before any real functionality can be meaningfully tested
3. **Admin layout differentiation** is needed so admin mode is visually distinct from player mode

---

*End of Review — No code changes made. Analysis and recommendations only.*
