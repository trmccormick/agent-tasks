# Galaxy Game Main Project — UI Structure Research & Prototype Alignment

**Date**: 2026-10-22  
**Source**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/`  
**Purpose**: Inform galaxy-game-ui prototype design decisions

---

## What Exists in Main Galaxy Game

### 1. Landing Page (Public)
**File**: `app/views/landing/index.html.erb`

**Structure**:
- Hero section with Galaxy Game logo, tagline, auth buttons
- Galaxy Status panel with 4 metric cards:
  - 🤖 AI Manager — Daily decisions count + active settlements
  - 🌌 Star Systems — Systems discovered + wormhole connections  
  - 💰 Economy — Daily trade volume + active missions
  - 🚀 Expansion — Growth rate + prize worlds found
- Active Star Systems grid showing real-time system data
- Recent Activity section (scrollable feed)

**Design Notes**:
- Responsive grid layout
- Metric cards prominently display numbers with labels
- System cards show: name, identifier, settlement count, body count, wormhole indicator
- Call-to-action buttons (Sign In, Create Account)

### 2. Game Views (Player)
**Files**: 
- `app/views/game/index.html.erb` — main game interface
- `app/views/game/celestial_body_detail.html.erb` — body detail view
- `app/views/game/simulation.html.erb` — simulation/timeline view

**Game View Layout**:
- **Header**: Title, game controls (buttons), time display
- **Sidebar**: Navigation sections, current status info
- **Main Content**: Celestial grid showing bodies/systems
- **Style**: Retro green terminal aesthetic (lime green #00ff00 on black #000011)
  - Monospace font (Courier New)
  - Glowing text effects
  - Green borders and highlights

**Example Celestial Body Card**:
```
Body Name
PLANET TYPE
Stats (size, temp, etc.)
```

### 3. Admin Views (Control Center)
**Files**:
- `app/views/admin/dashboard/index.html.erb` — main admin dashboard
- `app/views/admin/ai_manager/index.html.erb` — AI Manager interface
- `app/views/admin/celestial_bodies/index.html.erb` — body management
- `app/views/admin/solar_systems/show.html.erb` — system detail
- `app/views/admin/resources/market.html.erb` — market monitoring
- Many more specialized admin views

**Admin Layout** (2-column):
- **Header** (full-width): "GALAXY GAME - ADMIN CONTROL CENTER" title + system status
- **Sidebar** (250px): 
  - Admin sections navigation (AI Manager, Solar Systems, etc.)
  - Quick actions (Main Game, Background Jobs, Refresh)
  - Quick stats panel (Systems, Bodies, Settlements, AI Status)
- **Main Content**: 
  - Metric cards in grid (Galaxy Overview section)
  - Detailed tables and monitoring displays
  - Status indicators

**Typical Sections**:
- Galaxy Overview with metric cards
- Detailed entity lists (systems, settlements, etc.)
- Real-time monitoring/status displays
- Action buttons and controls

---

## Design Foundation: Color Palette & Typography

### Colors (from `_variables.scss`)
```scss
// Primary theme
$primary-blue: #00d4ff;              // Main accent (from Galaxy Game logo)
$primary-blue-dark: #0099cc;
$primary-blue-glow: rgba(0, 212, 255, 0.5);

// Backgrounds
$background-dark: #0a0a0a;           // Deep black
$background-surface: #1a1a2e;        // Dark blue-black
$background-panel: #16213e;          // Panel background

// Text
$text-primary: #e0e0e0;              // Light gray
$text-secondary: #8892b0;            // Muted gray
$text-highlight: $primary-blue;      // Cyan accent

// Accent colors
$accent-cyan: #00fff9;               // Brighter cyan
$accent-purple: #9d4edd;             // Purple secondary
$accent-warning: #ffd60a;            // Gold/yellow
$accent-danger: #ff006e;             // Red
$accent-success: #06ffa5;            // Green

// Effects
$glow-primary: 0 0 10px $primary-blue-glow;
$glow-strong: 0 0 20px $primary-blue;
```

### Typography
- **Font Family**: 'Courier New', monospace (retro/terminal aesthetic)
- **Logo**: Uses "Galaxy Game" with specific color scheme
- **Headings**: Sizes from 1.25rem (h3) to 2.5rem (hero)
- **Body Text**: 1rem (16px)
- **Small Text**: 0.875rem (14px)

---

## UI Component Patterns Found

### 1. Metric Cards (Status/Overview)
```
┌─────────────────────┐
│   📊 Card Title     │
│                     │
│  [Large Number]     │
│  [Stat Label]       │
│  [Optional Detail]  │
└─────────────────────┘
```
Used in: Landing page status grid, Admin dashboard overview

### 2. System/Body Cards
```
┌─────────────────────┐
│   System Name       │
│   IDENTIFIER        │
│                     │
│ Stat: Value         │
│ Stat: Value         │
│ [Badge/Indicator]   │
└─────────────────────┘
```
Used in: Landing systems grid, Game celestial grid

### 3. Admin Sidebar Section
```
┌─ SECTION TITLE ─────┐
│ [Button]            │
│ [Button]            │
│ [Button]            │
└─────────────────────┘
```
With border, padding, distinct background

### 4. Data Tables
- Border collapse
- Small font
- Alternating row styles (implied)
- Color-coded status cells

### 5. Navigation Structure
- Full-width header with border-bottom
- Sidebar with sections and buttons
- Main content area that fills remaining space
- Footer or additional info panel

---

## What the Prototype Should Adopt

### ✅ Definitely Use

1. **Color Scheme**
   - Primary cyan #00d4ff (already in galaxy-game-ui Tailwind config)
   - Dark backgrounds (#0a0a0a, #1a1a2e)
   - Light text (#e0e0e0) on dark backgrounds
   - Secondary purple #9d4edd for accents

2. **Layout Patterns**
   - Grid-based layouts (responsive)
   - Sidebar + main content pattern (especially for admin)
   - Full-width header with status line
   - Card-based components

3. **Typography**
   - Monospace font for "technical" feel (optional, could use sans-serif)
   - Clear hierarchy with size differences
   - Light gray on dark backgrounds for readability

4. **Component Styles**
   - Metric cards with large numbers + labels
   - System/body cards with name + identifier + stats
   - Navigation buttons with hover effects
   - Status indicators (colored badges, text)

### ⚠️ Adapt / Don't Copy Directly

1. **Admin Terminal Aesthetic**
   - Main Galaxy Game uses retro green terminal (#00ff00) for game view
   - Prototype should use modern cyber-punk aesthetic instead
   - **Solution**: Use cyan (#00d4ff) + glassmorphic effects (from EVE Dashboard) instead of retro green

2. **Admin Complexity**
   - Main Galaxy Game admin is feature-rich (AI Manager, map studio, etc.)
   - Prototype should focus on **player-facing dashboard** (simplified)
   - **Solution**: Keep card/sidebar structure, but reduce data density; use MockDataHelper only

3. **Monospace Fonts**
   - Main project uses Courier New for authenticity
   - Prototype should use sans-serif for modern feel
   - **Solution**: Use standard web fonts (e.g., system fonts or Inter/Roboto)

### ❌ Don't Use

- Database queries or ActiveRecord models
- Admin-specific business logic
- Full stylesheet files (too many dependencies)
- Integration with actual game backend

---

## Prototype Design Recommendations

### For Player Dashboard (`/dashboard`)

**Structure** (based on main project + EVE Dashboard):
```
┌─ Full-width Header ─────────────────────────────┐
│ Commander Name | Location | GCC Balance | Time  │
└─────────────────────────────────────────────────┘

┌─ Grid: 2 columns, multiple panels ──────────────┐
│                                                  │
│ [Panel 1]           [Panel 2]                   │
│ Local Inventory     Ships/Fleet                  │
│                                                  │
│ [Panel 3]           [Panel 4]                   │
│ Manufacturing       Buy Orders/Missions          │
│                                                  │
│ [Panel 5 - Footer]                              │
│ Market Quick Panel / Status Indicators           │
└─────────────────────────────────────────────────┘
```

**Styling**:
- Use glassmorphic panels (EVE Dashboard aesthetic)
- Cyan borders (#00d4ff) instead of green
- Progress bars with gradient (cyan → purple)
- Metric cards for key stats
- Responsive grid (1 column on mobile, 2+ on desktop)

### For Admin Interface (`/admin`)

**Structure** (based on main admin + scaled back):
```
┌─ Header ────────────────────────────────┐
│ GALAXY GAME - ADMIN PANEL               │
└─────────────────────────────────────────┘

┌─ Sidebar ─┬─ Main Content ─────────────┐
│ Sections  │ Dashboard Metrics           │
│           │                             │
│ [Nav]     │ [Metric Cards Grid]         │
│ [Nav]     │                             │
│ [Nav]     │ [Detail Tables/Lists]       │
│           │                             │
│ [Stats]   │                             │
└───────────┴─────────────────────────────┘
```

**Styling**:
- Keep sidebar (250px) with sections
- Maintain border styling (cyan borders)
- Use metric cards prominently
- Dark backgrounds with cyan accents
- Glassmorphic panels (modern, not retro)

### For Wormholes/Network View (`/wormholes`)

**Structure**:
- Network graph/diagram (D3.js placeholder or SVG)
- Settlement list with wormhole info
- Route planner (text-based, no real routing logic)
- Status badges for wormhole stability

**Styling**:
- Use same color scheme
- Card-based layout for settlement info
- Glassmorphic effects on info panels
- Color-coded status indicators

---

## Implementation Priority

**Phase 1** (Fix CSS blocker first):
- [ ] Replace WVU branding in session view
- [ ] Add glasmorphic effects to dashboard
- [ ] Enhance borders, shadows, visual hierarchy

**Phase 2** (Complete dashboard):
- [ ] Task 3: Ships + Manufacturing panels (use card patterns from main project)
- [ ] Task 4: Orders + Market panels (adapt metric cards)

**Phase 3** (Expand to other views):
- [ ] Wormholes view (network + settlement cards)
- [ ] Admin interface (sidebar + dashboard layout)

---

## Key Files to Reference

| File | Purpose |
|------|---------|
| `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/_variables.scss` | Color palette, spacing, typography |
| `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/landing/index.html.erb` | Metric card + grid layout patterns |
| `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/admin/dashboard/index.html.erb` | 2-column layout, sidebar navigation |
| `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/admin/_dashboard_styles.scss` | Admin styling (adapt for modern aesthetic) |

---

## Summary

**Main Takeaway**: The prototype should adopt the:
- ✅ Color scheme (cyan primary, dark backgrounds)
- ✅ Layout patterns (grid, sidebar, cards)
- ✅ Component structure (metric cards, system cards, navigation)
- ❌ NOT the retro green terminal aesthetic → use EVE Dashboard (glassmorphic) instead
- ❌ NOT the admin complexity → keep player-focused and simple

**Next Steps**:
1. Fix CSS blocker (session view + dashboard styling)
2. Complete dashboard with refined card designs
3. Expand to other views using validated patterns
