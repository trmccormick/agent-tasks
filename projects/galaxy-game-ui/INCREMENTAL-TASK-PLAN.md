# Galaxy Game UI — Incremental Task Planning

**Date**: 2026-10-23  
**Goal**: Create small, well-scoped task files following 3-agent workflow (draft → review → implementation)  
**Premium Usage**: 83% — minimize file creation, maximize reuse of existing task template

---

## Current Task Status

### Active Dispatch Chain (High Priority)

**Task 1**: Seeds Cleanup (CRITICAL)
- Status: Draft, ready for review + implementation
- Work: Complete Steps 3-6 (files already deleted)
- Salvage: None needed

**Task 2**: Landing Page (CRITICAL)
- Status: Draft, ready for review + implementation  
- Work: Extract structural HTML only, strip CSS classes
- Salvage: None needed

**Task 3**: Dashboard (HIGH)
- Status: Draft, awaiting Qwen's revision (GameDataJsonLoader approach)
- Work: Service + migration + JSON data + controller + view
- Salvage: None needed (replaces overcomplicated MockDataHelper)

---

## Salvageable Work from first-attempt (Post-Task-3)

### Non-CSS Documentation (Safe to Reuse)

1. **CSS-INTEGRATION-STRATEGY.md** 
   - Phase 1 (Foundation): EVE variable setup + Galaxy Game overrides
   - Phase 2 (Integration): Apply to existing panels, cards, buttons
   - Phase 3 (Enhancement): Refine spacing, animations, responsive tweaks
   - **Status**: Strategy is sound; just needs to apply to working HTML (not broken MockDataHelper code)

2. **EVE-DASHBOARD-FOUNDATION-PLAN.md**
   - Component mapping (topbar, panels, stat cards, buttons)
   - Data structure mapping (systems, settlements, inventory, market, missions)
   - **Issue**: References 6-method MockDataHelper (superseded by GameDataJsonLoader)
   - **Salvage**: Extract component patterns, ignore MockDataHelper sections

3. **GALAXY-GAME-UI-EXTRACTION-ANALYSIS.md**
   - Component extraction from galaxyGame main app
   - Color palette verification
   - Button/card structure matching
   - **Status**: Still valid for understanding what to extract

4. **WVU-MOONSHOT-SETUP-REFERENCE.md**
   - Rails setup patterns
   - Devise integration
   - CSV loader patterns (UserCsvLoader — already adopted for Task 1)
   - **Status**: Good reference for architecture

---

## Proposed Small Incremental Tasks (Post-Task-3)

Once Tasks 1-3 are done and reviewed, stub these for review-then-implement:

### Task 4: EVE CSS Foundation (MEDIUM)
- **Description**: Import EVE Dashboard CSS, override accent color, add Galaxy Game terminology
- **Scope**: 
  - Copy EVE's style.css to `app/assets/stylesheets/`
  - Override `--accent`, `--bg`, `--panel` variables for Galaxy Game theme
  - Update component class names (Account→Player, Corp→Settlement)
  - Add to Rails asset pipeline
- **Files**: 
  - `app/assets/stylesheets/eve_dashboard.scss` (new)
  - `app/layouts/application.html.erb` (update stylesheet link)
- **Testing**: Verify no conflicts with existing Tailwind, CSS loads in browser
- **Reference**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui-first-attempt/CSS-INTEGRATION-STRATEGY.md` Phase 1

### Task 5: Topbar Navigation (MEDIUM)
- **Description**: Build EVE-style topbar with app navigation
- **Scope**: 
  - Create `app/components/topbar_component.rb` (ViewComponent pattern if available)
  - Add brand logo, nav links (Dashboard, Wormholes, Blueprints), user menu (logout)
  - Wire into application layout
  - Add to dashboard view as header
- **Files**:
  - `app/components/topbar_component.rb` (new)
  - `app/components/topbar_component.html.erb` (new)
  - `app/layouts/application.html.erb` (update to include topbar)
- **Testing**: Verify topbar renders on dashboard, login page, landing page
- **Reference**: EVE Dashboard `.topbar` structure

### Task 6: Dashboard Panels Styling (HIGH)
- **Description**: Apply EVE CSS to 6 dashboard panels, wire mock data
- **Scope**:
  - Create 6 panel partials (systems status, settlements, inventory, market, missions, quick-stats)
  - Apply `.panel` class and EVE variables
  - Iterate over `@systems`, `@settlements`, etc. from controller
  - Add color-coded badges (green=active, gold=pending, red=offline)
- **Files**:
  - `app/views/dashboard/_system_panel.html.erb` (new)
  - `app/views/dashboard/_inventory_panel.html.erb` (new)
  - `app/views/dashboard/_market_panel.html.erb` (new)
  - `app/views/dashboard/_missions_panel.html.erb` (new)
  - `app/views/dashboard/index.html.erb` (update to use partials)
- **Testing**: Verify panels render without errors, colors display correctly
- **Reference**: first-attempt EVE-DASHBOARD-FOUNDATION-PLAN.md component mapping

### Task 7: Admin Dashboard View (MEDIUM)
- **Description**: Create admin-only dashboard with system telemetry, user management quick-links
- **Scope**:
  - Create `app/views/dashboard/admin.html.erb` (role-based view)
  - Panels: system health, active users, recent activity, admin quick-actions
  - Check for `current_user.role_admin?` before rendering
  - Wire to same `@metadata` as player view
- **Files**:
  - `app/views/dashboard/admin.html.erb` (new)
  - `app/controllers/dashboard_controller.rb` (update to handle admin vs player)
  - `app/views/dashboard/index.html.erb` (update to render correct view based on role)
- **Testing**: Login as admin, verify admin view; login as player, verify player view
- **Reference**: Gemini summary mentioned "role-based view logic"

### Task 8: Responsive Layout (LOW — Phase 2)
- **Description**: Ensure topbar, panels, and navigation work on mobile
- **Scope**:
  - Add mobile breakpoints to EVE CSS (or Tailwind media queries)
  - Test topbar horizontal scroll on small screens
  - Stack panels vertically on mobile
  - Hide secondary nav on narrow screens
- **Files**:
  - `app/assets/stylesheets/eve_dashboard.scss` (extend with mobile media queries)
- **Testing**: Test in browser DevTools, iPhone/Android viewport sizes

---

## What NOT to Include Yet

❌ **Wormholes feature** — Requires more game logic research  
❌ **Blueprints feature** — Requires database schema discussion  
❌ **Item crafting** — Requires inventory system research  
❌ **Authentication improvements** — Devise vs custom session, out of scope  
❌ **Advanced CSS animations** — Save for Phase 3 (Enhancement)

---

## Workflow for Stub Task Files

Each task file should include:

1. **YAML Frontmatter**:
   ```yaml
   ---
   status: drafts
   resume: false
   priority: MEDIUM  # or CRITICAL, HIGH, LOW
   type: feature     # or refactor, docs, test, etc.
   system_domain: VIEWS  # or CONTROLLERS, MODELS, MIGRATIONS, etc.
   mvp_alignment: OTHER
   local_worker_safe: true
   ---
   ```

2. **Dispatch Interface** (from template):
   - Step 0: Move from drafts → active
   - Step 1: Read prerequisites (GUARDRAILS.md + project README)
   - Step 2: Follow implementation steps

3. **Task Description**:
   - Context (why this task)
   - Current state (what exists)
   - Implementation steps (numbered, clear)

4. **Acceptance Criteria**: 
   - Measurable checklist

5. **Dependencies**:
   - Which tasks must complete first
   - Which tasks this blocks

6. **Gotchas**:
   - Common pitfalls
   - Session auth patterns
   - Asset pipeline quirks

---

## Next Actions

**For you to decide**:
1. Which small tasks should I stub out now?
   - Just the post-Task-3 chain (Tasks 4-8)?
   - Or also research tasks (understanding wormhole/blueprint models from galaxyGame)?

2. Should I create proper task files or just outlines?

3. Want me to create a status.md for galaxy-game-ui tracking these tasks?

4. Should I flag the first-attempt docs in a salvage list so implementation agents know what's available to reference?

---

## File Locations Ready

All task files would go to:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/drafts/`

All summaries would go to:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/`

Handoffs would go to:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/handoffs/`
