# Qwen Planning Agent Handoff — Galaxy Game UI Post-Snap MVP

**Purpose**: Comprehensive architecture overview for Qwen planning agent to spawn subagents for Tasks 1-8 implementation

**Status**: Ready for handoff | **Scope**: Rails 7 UI, post-Snap game state, wormhole physics, admin controls

---

## 1. Game Context (Why This Matters)

### Post-Snap Game State

Players enter Galaxy Game **after** a catastrophic Snap event that destroyed most interstellar travel infrastructure:

**Before Snap**:
- Three systems connected: Sol, System B (procedural), Eden
- Network stable, natural wormholes provided transit

**The Snap (2026-10-01 14:32 UTC)**:
- Natural Sol ↔ System B wormhole *destabilized* (no longer safe for transit)
- Artificial AWS wormhole Sol ↔ Eden *destroyed*, Eden isolated
- 18 hours of complete isolation (Eden orphaned)

**After Snap (2026-10-02 09:15 UTC — game start)**:
- AWS engineers rebuilt artificial wormhole Sol ↔ Eden (stable, AWS-powered)
- Natural Sol ↔ System B wormhole *still unstable* but usable (harvest EM energy to stabilize)
- Players inherit this **living crisis state** — not a tutorial, an active emergency

### Why This Architecture Matters

This isn't just window-dressing. The post-Snap state drives **every UI feature**:

- **Wormhole Navigator**: Must show instability decay, EM harvesting mechanics, Snap risk escalation
- **EM Economy Dashboard**: Must show deficit (consumption > harvest) — players must manage reserves carefully
- **Hammer Protocol**: Must show when snap risk threatens to break natural WH again
- **Admin Dashboard**: Must show AI Manager working to stabilize Eden reconnection, monitor Snap eligibility

If you build this without understanding the Snap context, features will appear arbitrarily complex. With context, every feature is a response to post-Snap constraints.

---

## 2. Wormhole Physics Foundation

### Mass Limit Formula

All transit across wormholes is governed by **mass limit**:

```
Mass_Limit = (System_Mass × Stability_Index) / EM_Surplus
```

**Example (Natural WH Sol ↔ System B)**:
- Sol mass: 500,000 tons
- System B mass: 250,000 tons (procedural)
- Stability Index: 0.68 (post-Snap decay)
- EM Surplus: high
- Mass Limit Sol → System B: `(500K × 0.68) / high_em` ≈ 37.5 tons per transit

**AWS Wormhole Sol ↔ Eden**:
- AWS counterbalance **doubles** the mass limit (because two stabilizers maintain it)
- Stability fixed at 0.95 (AWS-managed)
- Never decays (AWS sensors adjust in real-time)
- Never Snap-eligible (artificial link, not natural phenomenon)

### Natural WH Physics

**Natural Sol ↔ System B**:
- Stability decays 2% daily (affects mass limit)
- Recapture satellites harvest EM: ~150 EM per 1000 tons transited (passive income)
- Maintenance cost: 250 EM/day (keeps it open)
- Snap Risk Assessment: As cumulative transit mass approaches 94% of mass limit, snap_risk_level escalates:
  - `safe` (0-50%)
  - `caution` (51-75%)
  - `danger` (76%+)
- **Snap Event**: If transit exceeds mass limit, wormhole fractures (Snap), ejecting all in-transit cargo

### AWS Wormhole Physics

**Artificial Sol ↔ Eden**:
- Fixed stability 0.95 (AWS sensors compensate)
- No decay (constant EM input from AWS system)
- No harvest (AWS connection doesn't produce EM)
- No Snap risk (artificial link, controlled)
- 2 stabilizers actively maintain (status must display)
- Higher maintenance: 400 EM/day (AWS power demand)

### EM Economy

EM (electromagnetic energy) is the currency of expansion:

**Daily EM Flow** (post-Snap):
- Harvest (natural WH): +150 EM per 1000 tons
- Consumption (maintenance): -250 EM/day (natural) -400 EM/day (AWS) = -650 EM/day total
- **Net Daily Flow**: Deficit (-500 EM/day worst case)
- **Days Until Critical**: reserves / deficit = roughly 20 days (at game start, reserves ~10K)

**Strategic Implication**: Players must either:
1. Increase transit (more harvest)
2. Decrease consumption (abandon wormholes — not viable, need both)
3. Find EM trade routes (economic indices track this)
4. Trigger Snap and sacrifice one wormhole (Hammer Protocol)

---

## 3. Data Architecture (Tier System)

### StarSim Data Tiers

Galaxy Game uses a three-tier system for celestial data:

**Tier 1: Complete JSON (Sol Only)**
- `/databases/sol.json` contains full celestial hierarchy
- All planets, moons, settlements, orbital details
- Used for player operations (landing, mining, etc.)
- Serves as reference for data structure

**Tier 2: Hybrid (Local Bubble, 35+ Systems)**
- `/data/json-data/star_systems/` contains partial JSON for nearby systems
- Key data present: system mass, stability potential, planet count
- Metadata includes `generation_mode` flag indicating what's procedurally filled
- Used for discovery, navigation, market data
- Examples: Sirius, Vega, Arcturus, Alpha Centauri

**Tier 3: Procedural (Anything Beyond Local Bubble)**
- Fully generated on first discovery
- StarSim algorithm creates system mass, celestial bodies, settlement count
- Metadata: generation_timestamp, seed_value for reproducibility
- Used for exploration content

### MockDataHelper: The Data Bridge

`app/services/mock_data_helper.rb` provides all UI data without backend API. 7 core methods:

```ruby
MockDataHelper.wormhole_network()        # Returns post-Snap network
MockDataHelper.em_status()               # EM economy tracking
MockDataHelper.ai_manager_status()       # Directives + priorities
MockDataHelper.hammer_protocol_status()  # Snap readiness
MockDataHelper.blueprints(limit)         # Craft catalog
MockDataHelper.items(limit, filter)      # Resources catalog
MockDataHelper.economic_indices()        # Market data
```

**Critical Point**: These methods return **complete data structures** (nested hashes/arrays). Tasks 2-7 consume this data without modification.

### Post-Snap Network Structure

```ruby
{
  systems: [
    { id: "SOL", name: "Sol", mass: 500000, stability_index: 0.95, ... },
    { id: "SYS_B", name: "Vega", mass: 250000, stability_index: 0.68, ... },  # Random Local Bubble
    { id: "EDEN", name: "Eden", mass: 300000, stability_index: 0.95, ... }
  ],
  wormholes: [
    {
      id: "WH_NATURAL_SOL_SYSB",
      natural: true,
      system_a: "SOL",
      system_b: "SYS_B",
      stability_index: 0.68,  # Decays 2% daily
      em_potential: "high",
      em_harvest_rate: 150,   # Per 1000 tons
      em_maintenance_cost: 250,
      mass_limit: 37.5,
      cumulative_transit_mass_a_to_b: 28500,  # Approaching limit
      snap_risk_level: "danger",  # 76%+ of limit
      recapture_satellites: 3,
      snap_eligible: true
    },
    {
      id: "WH_AWS_SOL_EDEN",
      natural: false,
      system_a: "SOL",
      system_b: "EDEN",
      stability_index: 0.95,
      aws_operational: true,
      stabilizer_count: 2,
      em_maintenance_cost: 400,
      mass_limit: 75.0,  # Doubled by AWS counterbalance
      counterbalance_quality: "excellent",
      snap_eligible: false
    }
  ],
  post_snap_status: {
    snap_event_date: "2026-10-01T14:32:00Z",
    original_system_b_coordinates: "...",
    eden_orphaned_duration_hours: 18,
    aws_reconstruction_date: "2026-10-02T09:15:00Z",
    ai_manager_status: "active"
  }
}
```

---

## 4. UI Component Architecture

### Data Flow (MockDataHelper → Controllers → Views)

```
MockDataHelper (7 methods)
    ↓
Controllers (load data, calculate stats)
    ↓
Views (render with CSS variables, responsive grid)
    ↓
User sees post-Snap game state
```

### Component Hierarchy

```
Landing Page (Task 2)
├── Hero Section
├── System Selector (3 cards)
└── Enter Game → Sets session[:current_system] = "SOL"

Dashboard (Task 3)
├── Topbar (nav, system info)
├── Panel H (player/fleet overview)
├── Panel 1 (inventory/resources)
├── Panel 2 (fleet/ships)
├── Panel 3 (production/industry)
└── Panel 4 (wormhole quick-view)

Wormhole Navigator (Task 6) ← CRITICAL INTEGRATION
├── SVG Visualization (Sol, System B, Eden; natural + AWS WH)
├── EM Economy Status Bar (harvest/consumption/deficit)
├── Pathfinding Panel
├── Route Display (with physics)
├── Traversal (updates session location)
└── Hammer Protocol Interface (admin-only snap controls)

Admin Dashboard (Task 7) ← SECURITY CRITICAL
├── System Overview Cards (3 cards with post-Snap context)
├── EM Economy Dashboard
├── AI Manager Control Panel
├── Wormhole Monitoring Table
├── Hammer Protocol Interface (RED warning styling)
└── Economic Indices Display

Catalogs (Tasks 4-5)
├── Blueprints Grid (searchable)
├── Items Grid (filterable)
└── Detail Pages
```

### CSS Variable Integration

All UI uses EVE Dashboard theme variables:

```scss
--bg:      #0a0e27    // Dark background
--panel:   #0f1419    // Card/panel background
--panel2:  #151a2d    // Hover/nested background
--text:    #c0c0c0    // Primary text
--muted:   #666666    // Secondary text
--accent:  #00d4ff    // Cyan (links, highlights, AWS)
--green:   #00ff00    // Success (natural WH, stable)
--red:     #ff0000    // Danger (snap risk, errors)
--line:    #222233    // Borders
```

**Golden Rule**: No hardcoded colors in SCSS files. All color decisions derived from these 8 variables.

---

## 5. Task Sequencing & Dependencies

### Critical Path

```
Task 1: MockDataHelper
    ↓ (data provider)
    ├→ Task 2: Landing Page
    ├→ Task 3: Dashboard
    ├→ Task 4: Blueprints Catalog
    ├→ Task 5: Items Catalog
    ├→ Task 6: Wormhole Navigator (uses em_status, ai_manager_status, hammer_protocol_status)
    └→ Task 7: Admin Dashboard (uses all methods, adds auth check)
         ↓
    Task 8: Polish & Finalization (integration testing)
```

### Implementation Sequence (Recommended)

1. **Task 1** (MockDataHelper) — 4-6 hours
   - Implement all 7 methods with complete data structures
   - NO CHANGES after this; used by all subsequent tasks
   - Acceptance: All methods callable, correct structures, no nil values

2. **Tasks 2-5** (Parallel, 4-5 hours each)
   - Landing, Dashboard, Blueprints, Items
   - No cross-dependencies, all use Task 1 data read-only
   - Can spawn 4 subagents simultaneously

3. **Task 6** (Wormhole Navigator) — 8-10 hours
   - Depends: Task 1 (wormhole network, EM status, AI manager, Hammer Protocol)
   - Most complex: SVG visualization + physics display + admin controls
   - Acceptance: SVG renders, physics accurate, natural vs AWS WH styled differently, EM harvesting shown

4. **Task 7** (Admin Dashboard) — 6-8 hours
   - Depends: Task 1 (all methods), Task 6 (Hammer Protocol design)
   - Adds auth check, restricts Hammer Protocol to admins
   - Acceptance: All 6 sections load, admin auth enforced, Hammer Protocol admin-only

5. **Task 8** (Polish) — 2-3 hours
   - Depends: Tasks 1-7 complete
   - Responsive testing, CSS audit, browser compatibility, user flow validation
   - Acceptance: All 4 breakpoints pass, zero console errors, user flows work end-to-end

### Parallelization Strategy

**Parallel Groups**:
- Group A (Sequential): Task 1 → Tasks 2-5 (wait for 1, then spawn 2-5 together)
- Group B (Sequential): Task 1 → Task 6 → Task 7 (each depends on prior)
- Group C (Sequential): Tasks 1-7 complete → Task 8 (integration testing)

**Spawn Pattern for Qwen**:
1. Spawn Subagent-1 for Task 1 (MockDataHelper)
2. Wait for Task 1 completion
3. Spawn Subagents 2-5 in parallel (Landing, Dashboard, Blueprints, Items)
4. Spawn Subagent-6 (Wormhole Navigator)
5. Wait for Tasks 2-6 complete
6. Spawn Subagent-7 (Admin Dashboard)
7. Wait for Task 7 complete
8. Spawn Subagent-8 (Polish & Finalization)

---

## 6. Critical Integration Points

### Integration Point 1: Wormhole Physics Display (Task 6)

**What must be integrated**:
- Natural WH must show:
  - EM harvesting rate (150 per 1000 tons)
  - Daily maintenance cost (250 EM/day)
  - Recapture satellite count
  - Stability decay (2% daily)
  - Snap risk level (color-coded)
  - Cumulative transit mass approaching limit

- AWS WH must show:
  - AWS operational status
  - Stabilizer count (2)
  - Fixed stability (0.95, never decays)
  - No harvest (AWS-powered)
  - Higher maintenance (400 EM/day)
  - No snap risk (artificial)

**Acceptance Criteria**:
- SVG renders 2 wormholes with different visual styling (dashed natural, solid AWS)
- Colors correct (natural green, AWS cyan)
- EM flow animation visible on natural WH only
- All physics values display in route details
- Pathfinding works: Sol → System B direct, Sol → Eden direct

### Integration Point 2: EM Economy Dashboard (Tasks 3, 6, 7)

**What must be consistent**:
- Task 3 (Dashboard Panel 4): Quick EM status (reserves, days to critical)
- Task 6 (Wormhole Navigator): EM status bar showing harvest/consumption/net/deficit
- Task 7 (Admin Dashboard): Full EM economy panel with reserves bar

**Critical Values** (all tasks must show identical numbers):
- Total EM reserves: from `em_status()[:total_em_reserves]`
- Daily harvest: `em_status()[:daily_harvest_rate]` (only from natural WH transits)
- Daily consumption: `em_status()[:daily_consumption]` (all WH maintenance)
- Net daily flow: harvest - consumption (negative in post-Snap state)
- Days until critical: reserves / absolute(net_daily_flow)

**Acceptance Criteria**:
- All tasks show identical EM values
- Negative daily flow clearly indicated (red warning)
- Days until critical color-coded (green >60, orange 30-60, red <30)
- Formulas consistent across tasks

### Integration Point 3: Admin Access Control (Tasks 6, 7)

**What must be integrated**:
- Task 6 (Wormhole Navigator): Hammer Protocol visible only if `session[:admin]`
- Task 7 (Admin Dashboard): Entire dashboard requires admin auth

**Security Critical**:
- Non-admin access to `/admin` → redirect to root_path
- Hammer Protocol controls (Trigger Snap button) only functional for admins
- No HTML leakage (admin-only content not in source for non-admins)
- Session persistence across page loads

**Acceptance Criteria**:
- session[:admin] = true → Hammer Protocol visible in Task 6, admin dashboard accessible in Task 7
- session[:admin] = false or missing → Both hidden/inaccessible
- HTML contains no admin-only content for non-admins (use view conditional)

### Integration Point 4: Post-Snap Context Consistency (All Tasks)

**What must be consistent**:
- Snap event date: 2026-10-01 14:32 UTC (appears in Admin Dashboard)
- AWS reconstruction date: 2026-10-02 09:15 UTC (appears in Admin Dashboard)
- Eden orphaned duration: 18 hours (calculated as reconstruction - snap)
- System B: Random Local Bubble system (appears in Landing, Dashboard, Wormhole Navigator)

**Acceptance Criteria**:
- Landing page: System B procedurally selected or random Local Bubble choice
- Dashboard: System B identifier displayed consistently
- Wormhole Navigator: System B in network visualization
- Admin Dashboard: Post-Snap timestamps displayed with human-readable format (e.g., "Snap occurred 21 days ago")

---

## 7. Admin & Security Requirements

### Hammer Protocol (Red-Styled Admin Interface)

**Purpose**: Snap the natural Sol ↔ System B wormhole intentionally (last-resort option to save Eden)

**When Available**:
- Natural WH snap risk reaches "danger" (76%+ of mass limit)
- OR admin manually triggers (for testing/admin override)

**What Happens on Trigger**:
1. Natural WH fractures (Snap event)
2. All in-transit cargo lost
3. System B becomes unreachable
4. Eden remains connected (AWS stable)
5. Solar system isolated from System B
6. Players must use alternate trade routes

**UI Requirements**:
- Red section header (#ff3333 or --red styling)
- Warning icon and text ("DANGEROUS")
- Snap readiness indicator (% of mass limit)
- Projected exit coordinates (where cargo would eject)
- Orphan risk systems list ([System B])
- Estimated EM field value calculation
- Trigger Snap button (disabled until threshold, shows confirmation modal)

**Security**:
- Admin-only (session[:admin] check)
- Confirmation modal with consequences ("This will sever connection to System B")
- No accidental triggers (button disabled until ready)

### Admin Dashboard Access Control

**Current Implementation**:
- `app/controllers/admin_controller.rb` has `before_action :require_admin_access`
- `require_admin_access` checks `session[:admin]`
- Non-admins redirected to root_path

**For Qwen**:
- Maintain existing auth pattern (session-based, no database auth yet)
- Extend to all admin routes
- Hammer Protocol section uses same check

**Testing**:
- Set `session[:admin] = true` in browser console (for demo)
- Verify admin features visible
- Clear session or set to false
- Verify admin features hidden/redirected

---

## 8. Testing Strategy (Task 8 Specifics)

### Responsive Testing Matrix

Test on 4 breakpoints across all 6 UI tasks:

| Task | 375px Mobile | 768px Tablet | 1200px Desktop | 1600px Wide |
|------|---|---|---|---|
| Landing | 1 col, hero stacked | 1-2 col | 3 col cards | 3 col centered |
| Dashboard | Stacked panels | 2x2 grid | 2x2 grid | 2x2 grid |
| Blueprints | 1 col | 2 col | 3 col | 4 col |
| Items | 1 col | 2 col | 3 col | 4 col |
| Wormholes | Viz scrollable | Viz + table stacked | Viz left, panel right | Full layout |
| Admin | Stacked panels | Stacked panels | Grid layout | Grid layout |

### CSS Audit

Before deployment, verify:
- ✅ Zero hardcoded colors (all EVE variables)
- ✅ Typography consistent (font-family, size, weight, line-height)
- ✅ Spacing consistent (padding, margin, gaps follow grid rhythm)
- ✅ Buttons: 44px minimum height, consistent styling
- ✅ Hover states: all interactive elements have :hover, :active, :focus states
- ✅ Borders: all use --line variable
- ✅ SVG: icons scale properly, no distortion

### User Flow Validation

**Critical Flows**:
1. **Flow 1: Landing → Dashboard**
   - Hero section loads with system cards
   - System selector works (click, select, confirm)
   - Dashboard loads with correct system
   - Verify session[:current_system] persists

2. **Flow 2: Dashboard → Wormholes**
   - Wormhole Navigator link accessible
   - SVG network displays (Sol, System B, Eden; natural + AWS)
   - Pathfinding panel works (select source/dest, find route)
   - Route displays with physics details
   - Confirm traversal, verify location updates

3. **Flow 3: Dashboard → Catalogs**
   - Blueprints and Items grids load
   - Search/filter functional
   - Click detail, view full information
   - Images display or fallback shows

4. **Flow 4: Admin Features**
   - session[:admin] = true
   - Navigate to admin dashboard
   - All sections load (systems, EM, AI, wormholes, Hammer Protocol, indices)
   - Hammer Protocol visible and functional
   - Non-admin: features hidden/redirected

### Browser & Device Testing

**Browsers** (latest versions):
- Chrome 120+ (primary)
- Firefox 121+ (alt)
- Safari 17+ (compatibility)
- Chrome Android (mobile)

**Devices**:
- iPhone 12 mini (375px) — strict responsive test
- iPad 9.7" (768px) — tablet layout
- Desktop 1920x1080 — standard desktop
- Desktop 2560x1440 — ultrawide

### Performance Validation

- Landing page: <2s load (mock data)
- Dashboard: <1s load (session persists)
- Wormhole SVG: 60 FPS smooth animation
- No memory leaks in DevTools Profiler
- No unnecessary re-renders (React would check diff; Rails views re-render on every load, acceptable)

---

## 9. File Structure & Naming Conventions

### Controllers
```
app/controllers/
├── pages_controller.rb        (Landing, static pages)
├── dashboard_controller.rb    (Dashboard)
├── blueprints_controller.rb   (Blueprints grid + detail)
├── items_controller.rb        (Items grid + detail)
├── wormholes_controller.rb    (Wormhole Navigator, pathfinding, traversal)
├── admin_controller.rb        (Admin Dashboard, Hammer Protocol)
└── public_pages_controller.rb (Root, about, help)
```

### Views
```
app/views/
├── pages/
│   └── landing.html.erb       (Hero + system selector)
├── dashboard/
│   ├── index.html.erb
│   └── _panels.html.erb       (Panel H, 1-4)
├── blueprints/
│   ├── index.html.erb         (Grid)
│   └── show.html.erb          (Detail)
├── items/
│   ├── index.html.erb         (Grid)
│   └── show.html.erb          (Detail)
├── wormholes/
│   ├── index.html.erb
│   ├── _network_viz.html.erb  (SVG)
│   └── _hammer_protocol.html.erb
└── admin/
    ├── index.html.erb
    ├── _system_cards.html.erb
    ├── _em_status.html.erb
    ├── _ai_manager.html.erb
    ├── _wormhole_table.html.erb
    ├── _hammer_protocol.html.erb
    └── _economic_indices.html.erb
```

### Stylesheets
```
app/assets/stylesheets/
├── application.scss           (Global EVE variables, base styles)
├── landing.scss
├── dashboard.scss
├── blueprints.scss
├── items.scss
├── wormholes.scss
└── admin_dashboard.scss
```

### Services
```
app/services/
└── mock_data_helper.rb        (7 core methods, post-Snap data)
```

---

## 10. Known Constraints & Gotchas

### Gotcha 1: System B Selection

**Problem**: System B must be random but consistent within a session (same System B for entire play session).

**Solution**:
```ruby
# In dashboard controller
@system_b = session[:system_b] ||= select_random_local_bubble_system
```

**Files Affected**: Landing (selector), Dashboard, Wormhole Navigator

### Gotcha 2: EM Economy Must Show Deficit

**Problem**: Post-Snap EM consumption > harvest. UI must clearly show players are in crisis.

**Solution**:
- Days until critical: red if <30, orange if 30-60, green if >60
- Daily flow always negative (shown as red)
- EM reserves depicted as dwindling bar (visual urgency)

**Files Affected**: Dashboard Panel 4, Wormhole Navigator header, Admin Dashboard EM panel

### Gotcha 3: Hammer Protocol Red Warning Styling

**Problem**: Players must understand Snap trigger is dangerous, last-resort action.

**Solution**:
- Red section background (#ff3333 or --red)
- Warning icon + text
- Button disabled until threshold reached
- Confirmation modal shows consequences
- Only visible to admins (hidden in HTML for non-admins)

**Files Affected**: Task 6 (Wormhole Navigator), Task 7 (Admin Dashboard)

### Gotcha 4: SVG Rendering Performance

**Problem**: SVG with animations can be expensive; must run 60 FPS.

**Solution**:
- Use CSS transforms for animations (GPU accelerated)
- Limit animation complexity (EM flow pulse, not full redraw)
- Test on older devices (iPhone 11, iPad 6th gen)

**Files Affected**: Task 6 (Wormhole Navigator SVG visualization)

### Gotcha 5: Admin Auth Must Not Leak HTML

**Problem**: Non-admins can view source and see Hammer Protocol in HTML comments/hidden elements.

**Solution**:
```erb
<% if session[:admin] %>
  <%= render "hammer_protocol_interface", ... %>
<% end %>
```
DO NOT use CSS `display: none` to hide (creates XSS risk).

**Files Affected**: Task 6, Task 7

### Gotcha 6: MockDataHelper Must Return Complete Structures

**Problem**: If any method returns partial data (missing keys), downstream views crash with nil errors.

**Solution**:
- Test every method returns full hash/array structure
- Use sensible defaults (0 for numbers, empty string for text, [] for arrays)
- Acceptance Criteria: "No nil values displayed"

**Files Affected**: Task 1 (MockDataHelper)

---

## 11. Deployment Checklist

### Pre-Deployment (Task Completion)

- [ ] Task 1 complete: All 7 MockDataHelper methods callable, correct structures
- [ ] Tasks 2-5 complete: All UI renders without errors, data flows correctly
- [ ] Task 6 complete: SVG visualization shows post-Snap network, physics accurate
- [ ] Task 7 complete: Admin dashboard loads, auth enforced, Hammer Protocol admin-only
- [ ] Task 8 complete: Responsive tests pass all 4 breakpoints, zero console errors, user flows validated

### Pre-Deployment (QA Sign-Off)

- [ ] CSS audit: Zero hardcoded colors, all EVE variables used
- [ ] Responsive: Tested on 375px / 768px / 1200px / 1600px in-browser
- [ ] Browser compatibility: Chrome, Firefox, Safari all pass
- [ ] Asset validation: No 404 errors, fallbacks work
- [ ] User flows: Complete landing → dashboard → wormholes → admin
- [ ] Security: Admin auth enforced, Hammer Protocol hidden from non-admins
- [ ] Performance: All pages load <2s, SVG smooth 60 FPS

### Deployment

- [ ] Commit all 8 task files to git
- [ ] Push to main branch
- [ ] Deploy Rails app (Docker container or traditional)
- [ ] Verify all routes accessible
- [ ] Smoke test: landing → dashboard → wormholes → admin

### Post-Deployment

- [ ] Create follow-up task for missing asset generation (if any)
- [ ] Document known issues (if any)
- [ ] Plan next feature iteration (wormhole economics mini-game, AI Manager simulator)

---

## 12. Next Steps (After MVP)

### Phase 2: Wormhole Economics

- Add market simulation for EM trading
- Player-driven trade routes affect indices
- Economic consequences for Snap triggers

### Phase 3: AI Manager Simulation

- Directives become player-observable actions
- AI repair attempts, fuel management, crew assignments
- Player can influence priorities

### Phase 4: Asset Generation

- Generate missing blueprint icons (ChatGPT + Dall-E)
- Generate missing item icons
- Generate craft icons for player fleet

### Phase 5: Real Backend

- Migrate from MockDataHelper to real database
- Persist player progress, trade history, location
- Multi-player support (player interactions, trade agreements)

---

## Summary for Qwen

**What You're Building**: Post-Snap Galaxy Game UI — a Rails 7 dashboard showing a living crisis (natural wormhole unstable, need EM harvesting to prevent Snap) with admin controls to trigger intentional Snap events.

**Why It Matters**: Players aren't starting fresh; they inherit catastrophic emergency state. Every UI feature — EM economy, stability decay, Hammer Protocol, AI Manager — responds to post-Snap constraints.

**Your Job**: Spawn 8 subagents to implement tasks sequentially:
1. Task 1 (MockDataHelper) — data provider
2. Tasks 2-5 (Landing, Dashboard, Catalogs) — UI consumers (parallel OK)
3. Task 6 (Wormholes) — complex visualization + admin controls
4. Task 7 (Admin) — auth-restricted comprehensive control panel
5. Task 8 (Polish) — integration testing across all tasks

**Critical Success Criteria**:
- ✅ Wormhole physics accurate (mass limit formula working)
- ✅ EM economy shows deficit (harvest < consumption)
- ✅ Admin auth enforced (non-admins can't access Hammer Protocol)
- ✅ Post-Snap context consistent (snap event date, AWS reconstruction, Eden orphaning)
- ✅ Responsive on all breakpoints (375px / 768px / 1200px / 1600px)
- ✅ Zero console errors
- ✅ User flows work end-to-end

**File Locations**:
- Tasks: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/active/`
- Code: `/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui/`
- Storyline context: `/Users/tam0013/Documents/git/galaxyGame/docs/storyline/`

**Contact**: If subagents encounter ambiguity (e.g., "Should System B be fixed or random?"), refer to this document. All decisions documented here.

Good luck! 🚀
