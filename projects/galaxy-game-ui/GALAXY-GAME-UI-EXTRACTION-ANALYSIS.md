# Galaxy Game UI Extraction Analysis

**Date**: 2026-10-20  
**Purpose**: Inventory reusable UI patterns, components, and code from the existing galaxyGame prototype that should be extracted/adapted for the galaxy-game-ui Rails 8 prototype

---

## Existing UI Components (Extractable)

### 1. Color Palette & Variables ✅ EXTRACT

**Location**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/_variables.scss`

**What to extract**:
```scss
// Neon Blue Theme (from GalaxyGame.png logo)
$primary-blue: #00d4ff;           // Bright cyan primary
$primary-blue-dark: #0099cc;      // Dark variant
$primary-blue-glow: rgba(0, 212, 255, 0.5);

// Background colors (dark space theme)
$background-dark: #0a0a0a;
$background-surface: #1a1a2e;
$background-panel: #16213e;

// Text colors
$text-primary: #e0e0e0;
$text-secondary: #8892b0;
$text-tertiary: #49587a;
$text-dim: #64748b;

// Accent colors
$accent-cyan: #00fff9;
$accent-purple: #9d4edd;
$accent-warning: #ffd60a;
$accent-danger: #ff006e;
$accent-success: #06ffa5;

// Effects
$glow-primary: 0 0 10px $primary-blue-glow;
$glow-strong: 0 0 20px $primary-blue;
$glow-accent: 0 0 15px rgba(0, 255, 249, 0.4);
```

**For galaxy-game-ui**: Convert to Tailwind `@theme` variables (similar to EVE Dashboard reference pattern) or keep as SCSS variables. These colors are excellent for the dark sci-fi aesthetic.

---

### 2. Card Component ✅ EXTRACT

**Location**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/components/_cards.scss`

**Current pattern**:
```scss
.card, .status-card, .system-card {
  @include card;
  margin-bottom: $spacing-lg;
  color: $text-primary;
  
  .card-icon { font-size: 2.5rem; margin-bottom: $spacing-sm; }
  .stat-large { font-size: 2rem; font-weight: 700; color: $primary-blue; }
  .stat-label { color: $text-secondary; font-size: 1rem; }
  .stat-detail { color: $text-dim; font-size: 0.95rem; }
}
```

**For galaxy-game-ui**: Use this structure for Dashboard stat cards, Wormhole status cards, Admin directive cards.

**View example** (existing landing page):
```erb
<div class="status-card">
  <div class="card-icon">🤖</div>
  <h3>AI Manager</h3>
  <div class="stat-large"><%= @ai_decisions_today %></div>
  <div class="stat-label">Decisions Today</div>
  <div class="stat-detail">
    <%= @settlements_count %> Active Settlements
  </div>
</div>
```

**Adaptation needed**: Modify to use MockDataHelper instead of instance variables (since galaxy-game-ui has no database).

---

### 3. Button Styles ✅ EXTRACT

**Location**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/components/_buttons.scss`

**Current pattern**:
```scss
.btn {
  padding: $spacing-sm $spacing-lg;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  text-decoration: none;
  display: inline-block;
  transition: all 0.3s ease;
  
  &.btn-large { 
    font-size: $font-size-large;
    padding: $spacing-md $spacing-xl; 
  }
  
  &.btn-primary { /* blue variant */ }
  &.btn-secondary { /* muted variant */ }
  &.btn-text { /* text-only variant */ }
}
```

**For galaxy-game-ui**: Use for action buttons in Admin Telemetry Panel ("Force Rebalance Tick", "Trigger Wormhole Event").

---

### 4. Admin Sidebar Navigation ✅ ADAPT

**Location**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/admin/_admin_sidebar.scss`

**Two styles exist**:
- **Style A (Cyan Modern)**: `#00d4ff` borders, glowing effects
- **Style B (Classic Blue)**: `#00ccff` borders, cleaner look

**For galaxy-game-ui**: Adapt the cyan style for the 3-link header navigation (Dashboard, Wormholes, Admin). Existing code handles:
- Link styling with icon support
- Active state highlighting
- Hover effects with glow
- Color transitions

**Current implementation**:
```scss
.admin-sidebar-cyan .admin-tool-button {
  background: #1a1a2e;
  color: #00d4ff;
  border: 1px solid rgba(0, 212, 255, 0.4);
  cursor: pointer;
  
  &:hover,
  &.active {
    background: #00d4ff;
    color: #1a1a2e;
    border-color: #00d4ff;
    box-shadow: 0 0 8px rgba(0, 212, 255, 0.4);
  }
}
```

---

### 5. Layout & Layout Templates ✅ REVIEW

**Landing Page** (`app/views/landing/index.html.erb`):
- Hero section with logo, title, subtitle, CTA buttons
- Status grid showing AI Manager, Star Systems, Economy, Expansion stats
- Each stat card has icon, title, large number, label, detail line

**Admin AI Manager Layout** (`app/views/admin/ai_manager/index.html.erb`):
- Left sidebar with navigation links
- Top header with status indicator
- Main content area with:
  - Stat cards section (horizontal row of 3+ cards)
  - Admin card grid (for quick actions)
  - Recent activity section (feed of timestamped items)

**For galaxy-game-ui**: 
- Dashboard can follow landing page stat card pattern
- Admin panel can follow AI Manager layout (sidebar nav, stats header, main content grid)
- Wormhole panel can adapt the layout for table + cards

---

## Patterns to Adapt (Not Direct Copy)

### 1. View Template Structure

**Existing pattern**:
```erb
<div class="page-layout">
  <aside class="sidebar"></aside>
  <header class="header"></header>
  <main class="main-content"></main>
</div>
```

**For galaxy-game-ui**: Top navigation instead of sidebar (cleaner for player UI), same 3-section structure.

### 2. Helper Methods for Rendering

**Existing**: `game_helper.rb`, `celestial_bodies_helper.rb` (mostly empty)

**For galaxy-game-ui**: Create helpers like:
- `dashboard_helper.rb` — status_badge_class(), format_quantity(), inventory_fill_percent()
- `admin_helper.rb` — directive_status_class(), event_icon(), event_type_color()
- `mock_data.rb` — Data factory methods (already planned in Task 1)

### 3. UI Manager Pattern (for Interactivity)

**Location**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/ui_manager.js`

**Current capabilities**:
- Track selected planet, view mode, toggle states
- Event logging (maintain history of 50 events max)
- Tab switching
- Initialize/attach listeners

**For galaxy-game-ui**: Use Stimulus controller pattern instead (modern Rails 8 approach):
- Admin controller for button clicks, toast notifications, event log updates
- Keep event logging idea but implement as Stimulus action

---

## What NOT to Extract

❌ **Database/Model Code**
- All controllers tie to ActiveRecord models
- galaxy-game-ui has no database; use MockDataHelper instead

❌ **Authentication/Session Code**
- User sign-in, registration, password reset views
- Not needed for prototype

❌ **Complex Admin Functionality**
- Catalog management, celestial body editing, mission tracking
- Too specific to existing game; keep prototype focused

❌ **SVG/Canvas Rendering**
- System renderer, planet detail canvas code
- Keep wormhole/route visualization simple (HTML tables, not Canvas)

---

## Recommended Extraction Process

### Phase 1: Copy Styling Foundation
1. Copy `_variables.scss` → `app/assets/tailwind/application.css` (convert to @theme)
2. Copy `components/_cards.scss` → Adapt for Tailwind classes or custom CSS
3. Copy `components/_buttons.scss` → Adapt for Tailwind classes or custom CSS
4. Copy admin sidebar styling → Adapt for top navigation

### Phase 2: Adapt View Patterns
1. Use landing page stat card layout → Dashboard panels
2. Use AI Manager layout structure → Admin panel layout
3. Create table component for Wormhole node map (study existing forms/modals)

### Phase 3: Helpers & MockData
1. Create `mock_data.rb` helper (methods already planned in Task 1)
2. Create view helpers for status badges, formatting, icons
3. No need to extract existing helpers (game_helper is empty; celestial_bodies_helper is domain-specific)

### Phase 4: Stimulus Controllers
1. Create `admin_controller.js` for button actions and toasts
2. Pattern: Use Stimulus targets and actions (simpler than UI Manager)
3. Keep event logging in-memory (no persistence needed)

---

## File References for Qwen

**If Qwen needs to study existing patterns**:
- **Colors/Variables**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/_variables.scss`
- **Card Component**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/components/_cards.scss`
- **Button Styles**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/components/_buttons.scss`
- **Admin Sidebar**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/stylesheets/admin/_admin_sidebar.scss`
- **Landing View**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/landing/index.html.erb`
- **Admin View**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/admin/ai_manager/index.html.erb`
- **UI Manager JS**: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/ui_manager.js` (Stimulus pattern preferred for new code)

---

## Summary

The existing galaxyGame UI has solid foundations:
- ✅ **Well-organized SCSS** with variables, components, and modular structure
- ✅ **Cohesive color palette** aligned with game branding (neon cyan on dark space)
- ✅ **Reusable component patterns** (cards, buttons, sidebars) suitable for player UI
- ✅ **Layout structure** (sidebar + header + main) easily adapted for top navigation
- ✅ **Helper/controller organization** that can be followed for new modules

**For galaxy-game-ui**: Don't copy wholesale, but **study the patterns** and adapt them for a focused, streamlined player-facing UI prototype. The color palette and component library are particularly valuable.

