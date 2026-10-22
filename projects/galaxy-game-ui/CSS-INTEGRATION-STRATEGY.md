# EVE Dashboard CSS Integration Strategy

**Date**: 2026-10-22  
**Phase**: Planning for CSS Foundation Redesign  
**Approach**: EVE Dashboard CSS as primary foundation, adapted for Galaxy Game domain  
**Status**: Strategy Plan (awaiting approval to create tasks)

---

## Strategy Overview

### Primary Goal
Adopt EVE Dashboard CSS as the foundation, customize for Galaxy Game, then systematically port existing views with proper card styling.

### Three-Phase Approach

```
Phase 1: CSS FOUNDATION (BLOCKER - must complete first)
  ├─ Extract EVE Dashboard CSS system (variables, resets, layouts)
  ├─ Create Galaxy Game theme layer (override variables, add domain colors)
  ├─ Fix session/login view (WVU branding → Galaxy Game branding)
  └─ Verify dashboard CSS compiles without warnings

Phase 2: VIEW INTEGRATION (Landing + Auth + Admin)
  ├─ Update landing page (keep Galaxy Game structure, apply EVE CSS)
  ├─ Update sessions view (login form with proper styling)
  ├─ Create user profile/account view
  ├─ Create admin dashboard skeleton (sidebar + content layout)
  └─ Integrate card styles from approved design

Phase 3: PROGRESSIVE ENHANCEMENT (Panels 2-5 + Additional Views)
  ├─ Complete remaining dashboard panels (Ships, Manufacturing, Orders, Market)
  ├─ Integrate with EVE-based styling
  ├─ Port wormholes view
  └─ Polish and refinement
```

---

## EVE Dashboard CSS Foundation Analysis

### What We're Using From EVE Dashboard

**CSS Variables (Dark Theme)**:
```
--bg: #0e1114              (Page background - deep black)
--panel: #161b21           (Panel background - dark gray-blue)
--panel2: #1d242c          (Secondary panel - lighter)
--line: #2a333d            (Borders/dividers)
--text: #d8dee6            (Primary text - light gray)
--muted: #8a97a6           (Secondary text - muted)
--accent: #4aa8ff          (Primary accent - blue) ← REPLACE WITH CYAN
--green: #3fb950           (Success - green)
--red: #f85149             (Danger - red)
```

**Structural Components**:
- `.topbar` — Header with consistent height (64px min), navigation, status
- `.panel` — Card component (border, radius, padding)
- `.btn` — Button system (base, primary, sm, lg, danger, icon-btn)
- `.toolbar` — Horizontal button/control layout
- `.main-inner` — Content container with max-width and padding
- Scrollbar styling — Thin, themed, consistent

**Responsive Strategy**:
- Flex-based layouts
- Horizontal scrolling for narrow windows (no wrapping)
- Content scrolling in main area only

### What We Need to Adapt

| EVE Component | Galaxy Game Version | Rationale |
|---------------|-------------------|-----------|
| `--accent: #4aa8ff` (blue) | `--accent: #00d4ff` (cyan) | Use Galaxy Game primary color |
| `.topbar` with EVE logo | `.topbar` with "Commander Status" info | Domain terminology |
| Account/corp sections | Player character sections | Galaxy Game domain |
| Table grids | Data-driven cards (MockDataHelper) | Keep mockup-first approach |
| Login form (if present) | Custom Galaxy Game login form | Match game branding |

---

## Task Breakdown

### Phase 1: CSS Foundation ⚠️ BLOCKING

**Task 1.1**: Extract EVE Dashboard CSS → Galaxy Game Theme Layer
- **Goal**: Create a new stylesheet that:
  1. Imports EVE Dashboard base CSS (resets, layout, buttons, etc.)
  2. Overrides variables for Galaxy Game (cyan instead of blue, adjust colors)
  3. Adds domain-specific styling (player terminology, game concepts)
  4. No hardcoded hex colors in view files (all via variables)
- **Deliverables**:
  - `app/assets/stylesheets/eve-dashboard-base.css` (EVE foundation)
  - `app/assets/stylesheets/galaxy-game-theme.css` (overrides + domain)
  - `app/assets/stylesheets/components/` (card styles, buttons, panels)
- **Verification**:
  - No CSS warnings on compile
  - All views render without broken classes
  - Color swatches match Galaxy Game palette
  - Responsive test on mobile/tablet/desktop
- **Dependencies**: EVE Dashboard CSS source file
- **Estimate**: 1.5-2 hours (copy-adapt-verify)

**Task 1.2**: Fix Session/Login View
- **Goal**: Replace WVU branding with Galaxy Game branding, use proper CSS classes
- **Changes**:
  - Remove all `wvu-*` class references
  - Replace with Galaxy Game CSS classes (from theme layer)
  - Update form labels and placeholder text (WVU Libraries → Galaxy Game)
  - Apply correct button styling (primary, hover states)
  - Test login form rendering
- **Current File**: `app/views/sessions/new.html.erb`
- **Deliverable**: Properly styled login form
- **Verification**:
  - Form renders without broken classes
  - Inputs readable (proper contrast)
  - Buttons have correct hover effects
  - Screenshot matches EVE Dashboard aesthetic
- **Dependencies**: Task 1.1 (CSS foundation)
- **Estimate**: 45 min

**Task 1.3**: Verify Dashboard Styling + Polish
- **Goal**: Ensure dashboard panels use new CSS foundation, add glasmorphic effects
- **Changes**:
  - Replace hardcoded inline styles with CSS class application
  - Add backdrop-filter blur effects to panels
  - Enhance borders with proper transparency and color
  - Add shadow/glow effects (if CSS supports)
  - Verify progress bars, badge colors, text hierarchy
- **Current File**: `app/views/dashboard/index.html.erb`
- **Deliverable**: Dashboard that matches EVE Dashboard aesthetic
- **Verification**:
  - Visual comparison with EVE Dashboard reference (localhost:8765)
  - All panels have consistent styling
  - No inline style attributes (pure CSS classes)
  - Screenshot before/after showing improvement
- **Dependencies**: Task 1.1 + 1.2
- **Estimate**: 1 hour

**Phase 1 Summary**:
- **Total Estimate**: 3-3.5 hours
- **Blocker Resolution**: After completion, Tasks 2-4 can proceed
- **Deliverables**: CSS foundation + fixed session view + polished dashboard
- **Unblocks**: Tasks 2 (Ships/Manufacturing), Tasks 3 (Orders/Market)

---

### Phase 2: View Integration (Landing + Auth + Admin)

**Task 2.1**: Update Landing Page with EVE CSS
- **Goal**: Keep Galaxy Game landing structure (hero, status grid, systems list), apply EVE Dashboard CSS
- **Current Structure** (from Main Project Research):
  - Hero section with Galaxy Game logo, auth buttons
  - Galaxy Status metrics (4 cards - AI decisions, Star Systems, Economy, Expansion)
  - Active Star Systems grid
  - Recent Activity feed
- **Changes**:
  - Apply `.topbar` styling to header
  - Apply `.panel` styling to metric cards
  - Use `.btn` classes for auth buttons
  - Style systems grid with EVE card approach
  - Ensure responsive layout
- **Current File**: Create landing view if not exists, or update existing
- **Deliverable**: Landing page that matches EVE Dashboard aesthetic
- **Dependencies**: Task 1.1 (CSS foundation)
- **Estimate**: 1.5-2 hours

**Task 2.2**: Create User Profile/Account View
- **Goal**: Simple account page showing player info, settings, logout
- **Structure**:
  - `.topbar` with player name, location, balance, time
  - `.panel` with account info (character, faction, corporation)
  - `.panel` with settings (preferences, notifications)
  - Logout button
- **Styling**: EVE Dashboard CSS + Galaxy Game colors
- **Deliverable**: Account view template
- **Dependencies**: Task 1.1 (CSS foundation)
- **Estimate**: 1 hour

**Task 2.3**: Create Admin Dashboard Skeleton
- **Goal**: Set up 2-column admin layout (sidebar + main content)
- **Structure**:
  - `.topbar` header ("GALAXY GAME - ADMIN CONTROL CENTER")
  - Sidebar with navigation sections (Dashboard, Systems, Bodies, AI Manager, etc.)
  - Main content area with metric cards placeholder
  - Responsive sidebar toggle on mobile
- **Styling**: EVE Dashboard CSS + admin-specific colors
- **Deliverable**: Admin layout template with placeholder content
- **Dependencies**: Task 1.1 (CSS foundation)
- **Estimate**: 1.5 hours

**Phase 2 Summary**:
- **Total Estimate**: 4-4.5 hours
- **Deliverables**: Landing page, account view, admin skeleton
- **Dependencies**: Phase 1 complete
- **Enables**: Phase 3 (panel completion)

---

### Phase 3: Progressive Enhancement (Panels 2-5 + Wormholes)

**Task 3.1**: Complete Dashboard Panels 2-5 (Ships, Manufacturing, Orders, Market)
- **Goal**: Implement remaining dashboard panels with card styling
- **Panels**:
  - Panel 2: Ships (fleet listing with status)
  - Panel 3: Manufacturing/Research (with tabs)
  - Panel 4: Orders (Buy Orders / Missions with tabs)
  - Panel 5: Market (quick reference panel)
- **Styling**: EVE Dashboard card approach + Galaxy Game colors
- **Dependencies**: Task 1.1 (CSS), approved design spec
- **Estimate**: 3-4 hours

**Task 3.2**: Port Wormholes View
- **Goal**: Network visualization + settlement cards
- **Structure**:
  - Network graph (placeholder or D3.js)
  - Settlement list with cards
  - Route planner
- **Styling**: EVE Dashboard + Galaxy Game
- **Dependencies**: Task 1.1, Task 2.3 (admin layout patterns)
- **Estimate**: 2-3 hours

**Task 3.3**: Polish & Refinement
- **Goal**: Final pass on responsive, accessibility, edge cases
- **Activities**:
  - Mobile responsive testing
  - Dark/light theme toggle (if desired)
  - Performance tuning (CSS optimization)
  - Screenshot validation against reference
- **Dependencies**: Tasks 3.1-3.2 complete
- **Estimate**: 1-2 hours

**Phase 3 Summary**:
- **Total Estimate**: 6-9 hours
- **Deliverables**: Complete dashboard, wormholes view, polished UI
- **Enables**: Next iteration (real data integration, admin features)

---

## Task Mapping to Current Status

### What Already Exists
- ✅ Task 2 (Dashboard structure) — Complete, but needs CSS polish (Task 1.3)
- ✅ Session view — Exists but broken (Task 1.2)
- ✅ MockDataHelper — 21 methods verified and ready to use

### What Needs to Be Created/Updated
| Current Task | New Strategy | Status |
|--------------|--------------|--------|
| `2026-10-22-HIGH-FIX-SESSION-LOGIN-AND-DASHBOARD-STYLING.md` | **Task 1.1 + 1.2 + 1.3** (rename/consolidate) | Ready to update |
| `2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md` | **Task 3.1** (rename/update) | Blocked until Phase 1 complete |
| `2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md` | **Task 3.2** (rename/update) | Blocked until Phase 1 complete |
| (New) | **Task 2.1** - Landing Page with EVE CSS | Create |
| (New) | **Task 2.2** - User Account View | Create |
| (New) | **Task 2.3** - Admin Dashboard Skeleton | Create |
| (New) | **Task 3.2** - Wormholes View | Create |
| (New) | **Task 3.3** - Polish & Refinement | Create |

---

## CSS Variable Mapping

### EVE Dashboard → Galaxy Game Theme

**Override Variables** (in galaxy-game-theme.css):

```css
:root {
  /* EVE base */
  --bg: #0e1114;
  --panel: #161b21;
  --panel2: #1d242c;
  --line: #2a333d;
  --text: #d8dee6;
  --muted: #8a97a6;
  
  /* Galaxy Game overrides */
  --accent: #00d4ff;           /* Cyan instead of blue */
  --accent-dark: #0099cc;      /* Dark cyan */
  --accent-glow: rgba(0, 212, 255, 0.5);
  
  /* Additional colors (from main project) */
  --secondary: #9d4edd;        /* Purple accent */
  --success: #06ffa5;          /* Green */
  --warning: #ffd60a;          /* Gold */
  --danger: #ff006e;           /* Red */
  
  /* Background overrides (if desired) */
  --bg-dark: #0a0a0a;          /* Slightly darker than EVE */
  --bg-surface: #1a1a2e;       /* Slightly more blue than EVE */
}
```

**Domain-Specific CSS Classes**:
```css
.topbar-title { /* "GALAXY GAME" or player name */ }
.player-status { /* Displays current location, balance, time */ }
.metric-card { /* Large number + label (4 per row) */ }
.system-card { /* System name + identifier + badges */ }
.panel-header { /* Panel title + controls */ }
.progress-bar { /* Cyan gradient with animation */ }
.status-badge { /* Colored indicators (online, active, etc.) */ }
.glow-effect { /* Text-shadow or filter for "tech" feel */ }
```

---

## Acceptance Criteria (For Planning)

### Phase 1 Completion Criteria ✅
- [ ] EVE Dashboard CSS extracted and customized
- [ ] Session/login view renders without broken classes
- [ ] Dashboard panels have glasmorphic styling
- [ ] No CSS warnings on compilation
- [ ] Visual comparison with EVE Dashboard shows improvement
- [ ] Mobile responsive verified
- [ ] All color variables use Galaxy Game palette
- [ ] No WVU branding remaining

### Phase 2 Completion Criteria ✅
- [ ] Landing page uses EVE CSS foundation
- [ ] User account view created and styled
- [ ] Admin sidebar skeleton created and functional
- [ ] All views responsive on mobile/tablet
- [ ] Navigation between views working
- [ ] MockDataHelper integration verified

### Phase 3 Completion Criteria ✅
- [ ] Remaining dashboard panels implemented (2-5)
- [ ] Wormholes view complete
- [ ] All panels use consistent card styling
- [ ] Dark theme fully implemented
- [ ] Performance acceptable (CSS optimized)
- [ ] Accessibility standards met (contrast, keyboard nav)

---

## Implementation Sequencing

**Dependency Tree** (must complete in order):

```
Phase 1 (Blocking)
  ├─ Task 1.1 (CSS Foundation) ← STARTS HERE
  ├─ Task 1.2 (Session View) ← Depends on 1.1
  └─ Task 1.3 (Dashboard Polish) ← Depends on 1.1, 1.2

Phase 2 (Can start after Phase 1)
  ├─ Task 2.1 (Landing Page) ← Depends on 1.1
  ├─ Task 2.2 (Account View) ← Depends on 1.1
  └─ Task 2.3 (Admin Skeleton) ← Depends on 1.1

Phase 3 (Can start after Phase 2)
  ├─ Task 3.1 (Dashboard Panels 2-5) ← Depends on 1.1, 2.3
  ├─ Task 3.2 (Wormholes View) ← Depends on 1.1, 2.3
  └─ Task 3.3 (Polish) ← Depends on 3.1, 3.2
```

---

## Next Steps

**As Planning Agent**:
1. ✅ Created this strategy document (you're reading it)
2. 📋 Await approval to create tasks

**If Approved**:
1. Update/consolidate existing CSS blocker task (1.1 + 1.2 + 1.3)
2. Create new tasks for Phase 2 (2.1, 2.2, 2.3)
3. Create new tasks for Phase 3 (3.1, 3.2, 3.3)
4. Update status.md with new task list and dependencies
5. Create handoff documents for each phase
6. Ready for Qwen/Haiku dispatch

**Total Estimated Work**:
- Phase 1: 3-3.5 hours (CRITICAL PATH)
- Phase 2: 4-4.5 hours
- Phase 3: 6-9 hours
- **Total**: 13.5-17 hours

**Priority**: Phase 1 must complete first; Phase 2/3 can be dispatched in parallel once Phase 1 complete.

---

## Risk Assessment

| Risk | Mitigation |
|------|-----------|
| EVE CSS doesn't fully adapt to Galaxy Game | Keep Tailwind config for fallback; can mix EVE CSS + Tailwind utilities |
| Responsive layout breaks on certain views | Test at 320px, 768px, 1024px, 1320px breakpoints |
| Color variable conflicts | Document all variable overrides; no inline hex values |
| MockDataHelper methods insufficient | Already verified 21 methods; extend as needed during implementation |
| Styling regressions on new panels | Visual regression testing during Phase 2-3 |

---

## Sign-Off

**Strategy Created By**: Planning Agent  
**Date**: 2026-10-22  
**Status**: Awaiting Approval  
**Recommendation**: Approve this strategy and create Phase 1 tasks as HIGHEST PRIORITY  
**Next Action**: Create consolidated Task 1 (CSS Foundation) combining original blocker tasks
