# PHASE 1: CSS Foundation — EVE Dashboard Integration

**Priority**: 🔴 **CRITICAL PATH** (blocks all other work)  
**Status**: Ready for Dispatch  
**Dispatch Target**: Qwen (implementation)  
**Estimate**: 3-3.5 hours  
**Date Created**: 2026-10-22

---

## Overview

Establish CSS foundation for galaxy-game-ui by adopting EVE Dashboard CSS system and customizing for Galaxy Game domain. This is the **blocking dependency** for all subsequent dashboard panels (Tasks 2-3) and UI views (landing, admin, wormholes).

**High-Level Work**:
1. Extract EVE Dashboard CSS into reusable base layer
2. Create Galaxy Game theme overrides (color variables, domain styling)
3. Fix session/login view (remove WVU branding, apply proper CSS)
4. Polish dashboard panels (glasmorphic effects, borders, shadows)
5. Verify no CSS warnings, responsive layout works, visual improvement evident

---

## Detailed Tasks

### Task 1.1: CSS Foundation Setup (1.5-2 hours)

**Goal**: Create CSS layer structure that EVE Dashboard CSS can be adapted from Galaxy Game.

**Deliverables**:

1. **`app/assets/stylesheets/eve-dashboard-base.css`** (NEW)
   - Copy relevant sections from EVE Dashboard CSS (`/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`)
   - Include:
     - CSS variable definitions (EVE default)
     - Reset/normalize styles
     - Layout framework (.topbar, .panel, .btn, .toolbar, .main-inner)
     - Scrollbar styling
     - Typography base styles
     - Table/grid utilities
   - **Exclude**: EVE-specific components (account sections, fleet/star-map palette, theme toggle)
   - Keep all EVE color variable names as-is (--bg, --panel, --accent, etc.)

2. **`app/assets/stylesheets/galaxy-game-theme.css`** (NEW)
   - Override EVE CSS variables for Galaxy Game:
     ```css
     :root {
       /* From EVE base (keep as-is) */
       --bg: #0e1114;
       --panel: #161b21;
       --panel2: #1d242c;
       --line: #2a333d;
       --text: #d8dee6;
       --muted: #8a97a6;
       
       /* GALAXY GAME OVERRIDES */
       --accent: #00d4ff;           /* Cyan instead of EVE blue */
       --accent-dark: #0099cc;      /* Dark cyan */
       --accent-glow: rgba(0, 212, 255, 0.5);
       
       /* Additional Galaxy Game colors */
       --secondary: #9d4edd;        /* Purple accent */
       --success: #06ffa5;          /* Green */
       --warning: #ffd60a;          /* Gold */
       --danger: #ff006e;           /* Red */
       
       /* Domain-specific styling */
       --bg-dark: #0a0a0a;          /* Slightly darker than EVE */
       --bg-surface: #1a1a2e;       /* Slightly more blue than EVE */
     }
     ```
   - Add Galaxy Game-specific component classes:
     ```css
     .player-status { /* Header showing location, balance, time */ }
     .metric-card { /* Large number + label cards */ }
     .progress-bar-galaxy { /* Cyan gradient progress bars */ }
     .status-badge { /* Colored status indicators */ }
     .glow-text { /* Glow effect for emphasis */ }
     .glassmorphic-panel { /* EVE panel + galaxy Game glassmorphic effects */ }
     ```
   - Add backdrop-filter effects:
     ```css
     .glassmorphic-panel {
       background: rgba(22, 27, 33, 0.85);
       backdrop-filter: blur(10px);
       border: 1px solid rgba(0, 212, 255, 0.2);
       border-radius: 10px;
       padding: 24px;
       box-shadow: 0 0 20px rgba(0, 212, 255, 0.1);
     }
     ```

3. **Update `config/tailwind.config.js`** (MODIFY)
   - Ensure Galaxy Game colors are still available as Tailwind utilities (fallback)
   - Add CSS variable references for new EVE-based setup
   - No conflicts between Tailwind and CSS variables

4. **Update `app/assets/stylesheets/application.css`** (MODIFY)
   - Import order:
     ```css
     @import 'eve-dashboard-base.css';
     @import 'galaxy-game-theme.css';
     @import 'components/*';  /* Any component-specific styles */
     ```

**Acceptance Criteria**:
- ✅ `eve-dashboard-base.css` exists and contains EVE CSS foundation
- ✅ `galaxy-game-theme.css` exists with Galaxy Game variable overrides
- ✅ Tailwind config still compiles without warnings
- ✅ All CSS variables are properly defined (no undefined fallbacks)
- ✅ Backup of original EVE Dashboard CSS available (for reference)
- ✅ No hardcoded hex colors in new CSS files (all via variables or utilities)
- ✅ Responsive breakpoints work (test at 320px, 768px, 1024px, 1320px)

**Verification**:
```bash
# From galaxy-game-ui directory
bundle exec rails assets:precompile
# Should compile without warnings

# Check for undefined colors
grep -E '#[0-9a-f]{6}' app/assets/stylesheets/galaxy-game-theme.css
# Should return only CSS comments or intentional hex values (minimal)
```

---

### Task 1.2: Fix Session/Login View (45 min)

**Current State**: `app/views/sessions/new.html.erb` uses WVU branding with 12+ non-existent CSS classes (wvu-blue, wvu-warm-gray-light, wvu-accent-blue, wvu-gold, etc.)

**Goal**: Replace WVU branding with Galaxy Game branding, use proper CSS classes from new foundation.

**Changes**:

1. **Update file header** (lines 1-8):
   ```erb
   <!-- BEFORE -->
   <% content_for :title, "Sign in — WVU Libraries Systems" %>
   <div class="mx-auto max-w-md">
     <div class="rounded-lg border border-wvu-warm-gray-light bg-white p-8 shadow-sm">
       <h1 class="text-2xl font-bold text-wvu-blue">Sign in</h1>
       <p class="mt-1 mb-6 text-sm text-wvu-warm-gray-medium">
         WVU Libraries Systems — Donor Check-In
       </p>

   <!-- AFTER (use CSS variables instead of Tailwind utilities) -->
   <% content_for :title, "Commander Login — Galaxy Game" %>
   <div class="panel" style="margin: 40px auto; max-width: 640px;">
     <h1 style="color: var(--accent); font-size: 24px; font-weight: 600;">Commander Login</h1>
     <p style="color: var(--muted); font-size: 14px; margin-top: 8px; margin-bottom: 24px;">
       Enter your credentials to access Galaxy Game
     </p>
   ```

2. **Update form labels & inputs** (lines 9-25):
   ```erb
   <!-- BEFORE -->
   <%= form.label :email_address, class: "mb-1.5 block text-sm font-semibold text-wvu-warm-gray-dark" %>
   <%= form.email_field :email_address, required: true, autofocus: true, autocomplete: "username",
         placeholder: "Enter your email address", value: params[:email_address],
         class: "w-full rounded-lg border border-wvu-warm-gray-light px-4 py-3.5 text-base focus:border-wvu-accent-blue focus:ring-2 focus:ring-wvu-accent-blue/30 outline-none" %>

   <!-- AFTER (use CSS styling, minimal Tailwind) -->
   <%= form.label :email_address, "Email Address", class: "block mb-2" %>
   <%= form.email_field :email_address, required: true, autofocus: true, autocomplete: "username",
         placeholder: "commander@galaxy.com", value: params[:email_address],
         class: "form-input w-full rounded-lg px-4 py-3 mb-4",
         style: "border: 1px solid var(--line); background: var(--panel2); color: var(--text); font-size: 14px;" %>
   ```

3. **Update submit button** (lines 26-27):
   ```erb
   <!-- BEFORE -->
   <%= form.submit "Sign in",
         class: "w-full rounded-lg bg-wvu-accent-blue px-6 py-3.5 text-base font-semibold text-white shadow hover:bg-wvu-gold hover:text-wvu-blue transition-colors" %>

   <!-- AFTER (use .btn.primary from EVE CSS) -->
   <%= form.submit "Sign In",
         class: "btn primary lg w-full" %>
   ```

4. **Update forgot password link** (lines 28-31):
   ```erb
   <!-- BEFORE -->
   <%= link_to "Forgot password?", new_password_path, class: "text-sm font-medium text-wvu-accent-blue hover:underline" %>

   <!-- AFTER -->
   <%= link_to "Forgot password?", new_password_path, style: "color: var(--accent); text-decoration: none; font-size: 14px;" %>
   ```

5. **Add CSS for form inputs** (add to `app/assets/stylesheets/galaxy-game-theme.css`):
   ```css
   .form-input {
     border: 1px solid var(--line) !important;
     background: var(--panel2) !important;
     color: var(--text) !important;
     font-size: 14px;
     padding: 12px;
     border-radius: 6px;
     transition: border-color 0.2s;
   }
   
   .form-input:focus {
     border-color: var(--accent) !important;
     outline: none;
     box-shadow: 0 0 0 3px rgba(0, 212, 255, 0.1);
   }
   
   .form-input::placeholder {
     color: var(--muted);
   }
   ```

**Acceptance Criteria**:
- ✅ No `wvu-*` class references in file
- ✅ Form uses CSS variables (--accent, --text, --panel2, --muted, --line)
- ✅ Submit button uses `.btn.primary` class from EVE CSS
- ✅ Form renders without broken styling
- ✅ Inputs are readable (proper contrast)
- ✅ Focus states work (border highlight, glow effect)
- ✅ Link colors match accent color

**Verification**:
```bash
# Live test
curl http://localhost:3000/sessions/new | grep -c 'wvu-'
# Should return: 0

# Screenshot comparison
# Visit http://localhost:3000/sessions/new in browser
# Compare against EVE Dashboard login aesthetic
# Should see: dark panel, cyan accents, proper form styling
```

---

### Task 1.3: Dashboard Styling Polish (1 hour)

**Current State**: Dashboard is structurally complete but visually flat. Minimal styling; lacks glasmorphic effects and proper visual hierarchy vs. EVE Dashboard reference.

**Goal**: Apply glasmorphic styling, enhance borders/shadows, improve visual hierarchy to match EVE Dashboard aesthetic.

**File**: `app/views/dashboard/index.html.erb`

**Changes**:

1. **Panel Container** (wrap all panels in glasmorphic class):
   ```erb
   <!-- BEFORE: Each panel has inline styles -->
   <div class="bg-[rgba(26,26,46,0.85)] border border-cyan-900/30 rounded-lg p-4">

   <!-- AFTER: Use CSS class -->
   <div class="glassmorphic-panel">
   ```

2. **Add CSS for dashboard panels** (`app/assets/stylesheets/galaxy-game-theme.css`):
   ```css
   .dashboard-panel {
     background: rgba(22, 27, 33, 0.95);
     backdrop-filter: blur(12px);
     border: 1px solid rgba(0, 212, 255, 0.25);
     border-radius: 12px;
     padding: 24px;
     box-shadow: 
       0 0 1px rgba(0, 212, 255, 0.1),
       0 0 20px rgba(0, 212, 255, 0.05),
       inset 0 0 1px rgba(255, 255, 255, 0.1);
     transition: border-color 0.3s, box-shadow 0.3s;
   }
   
   .dashboard-panel:hover {
     border-color: rgba(0, 212, 255, 0.4);
     box-shadow: 
       0 0 1px rgba(0, 212, 255, 0.2),
       0 0 30px rgba(0, 212, 255, 0.1),
       inset 0 0 1px rgba(255, 255, 255, 0.2);
   }
   
   .panel-header {
     color: var(--accent);
     font-size: 18px;
     font-weight: 600;
     margin-bottom: 16px;
     padding-bottom: 12px;
     border-bottom: 1px solid rgba(0, 212, 255, 0.2);
   }
   
   .progress-bar-galaxy {
     background: rgba(0, 212, 255, 0.1);
     border-radius: 4px;
     overflow: hidden;
     height: 8px;
   }
   
   .progress-bar-galaxy::after {
     content: '';
     display: block;
     height: 100%;
     background: linear-gradient(90deg, #00d4ff, #9d4edd);
     border-radius: 4px;
   }
   ```

3. **Update ERB template** (replace inline styles with classes):
   ```erb
   <!-- Panel structure template -->
   <div class="dashboard-panel">
     <div class="panel-header">Panel Title</div>
     <div class="panel-content">
       <!-- Content here -->
     </div>
   </div>
   ```

4. **Progress bars** (update any progress bar styling):
   ```erb
   <!-- BEFORE: Hardcoded gradient -->
   <div class="bg-gradient-to-r from-cyan-600 to-purple-600 h-2 rounded" style="width: <%= inventory_percent %>%;"></div>

   <!-- AFTER: Use CSS class with proper styling -->
   <div class="progress-bar-galaxy" style="width: 100%;">
     <div style="width: <%= inventory_percent %>%; height: 100%; background: linear-gradient(90deg, #00d4ff, #9d4edd);"></div>
   </div>
   ```

5. **Status badges** (add consistent styling):
   ```css
   .status-badge {
     display: inline-block;
     padding: 4px 12px;
     border-radius: 4px;
     font-size: 12px;
     font-weight: 600;
     text-transform: uppercase;
   }
   
   .status-badge.active {
     background: rgba(6, 255, 165, 0.2);
     color: #06ffa5;
   }
   
   .status-badge.inactive {
     background: rgba(139, 149, 166, 0.2);
     color: var(--muted);
   }
   
   .status-badge.warning {
     background: rgba(255, 214, 10, 0.2);
     color: #ffd60a;
   }
   ```

6. **Text hierarchy** (ensure colors use CSS variables):
   ```css
   .text-primary { color: var(--text); }
   .text-secondary { color: var(--muted); }
   .text-accent { color: var(--accent); }
   .text-success { color: var(--success); }
   .text-warning { color: var(--warning); }
   .text-danger { color: var(--danger); }
   ```

**Acceptance Criteria**:
- ✅ All panels have `.dashboard-panel` class (glasmorphic styling)
- ✅ Panel headers use `.panel-header` class
- ✅ Progress bars use `.progress-bar-galaxy` class
- ✅ Status badges use `.status-badge` with color variants
- ✅ No hardcoded hex colors in ERB file (all in CSS variables)
- ✅ No inline `style=` attributes except for dynamic values (width, etc.)
- ✅ Visual comparison with EVE Dashboard shows significant improvement
- ✅ Hover effects work (border/shadow changes)
- ✅ Mobile responsive (panels stack properly on mobile)

**Verification**:
```bash
# Screenshot comparison
# Visit http://localhost:3000/dashboard in browser
# Compare against EVE Dashboard (localhost:8765)
# Checklist:
# - [ ] Panels have glassmorphic appearance (blur effect visible)
# - [ ] Cyan borders are subtle but visible
# - [ ] Glow effects on hover
# - [ ] Text hierarchy clear (headers are prominent, secondary text muted)
# - [ ] Progress bars have gradient
# - [ ] No flat/plain appearance (improvement from before)
```

---

## Integration Checklist

Before marking complete:

- [ ] No CSS warnings from `bundle exec rails assets:precompile`
- [ ] No browser console errors when visiting views
- [ ] Session view (`/sessions/new`) renders correctly
- [ ] Dashboard (`/dashboard`) renders correctly
- [ ] Responsive layout tested on:
  - [ ] 320px (mobile)
  - [ ] 768px (tablet)
  - [ ] 1024px (desktop)
  - [ ] 1320px (wide)
- [ ] Screenshot comparison with EVE Dashboard shows visual improvement
- [ ] All forms use proper CSS styling (inputs, buttons)
- [ ] Color contrast meets accessibility standards (WCAG AA minimum)
- [ ] Hover/focus states work on interactive elements

---

## Dependencies

**Before starting**:
- ✅ Ruby on Rails 8 environment running
- ✅ Tailwind CSS v4 configured
- ✅ MockDataHelper available with 21 methods verified
- ✅ EVE Dashboard CSS available for reference (`/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`)

**Blocks**:
- Phase 2 (Landing page, account view, admin skeleton)
- Phase 3 (Dashboard panels 2-5, wormholes view)
- Any new feature work requiring CSS foundation

---

## Files to Modify/Create

| File | Action | Purpose |
|------|--------|---------|
| `app/assets/stylesheets/eve-dashboard-base.css` | CREATE | EVE Dashboard CSS foundation |
| `app/assets/stylesheets/galaxy-game-theme.css` | CREATE | Galaxy Game theme overrides + domain styles |
| `app/assets/stylesheets/application.css` | MODIFY | Update import order |
| `config/tailwind.config.js` | VERIFY | Ensure Galaxy Game colors still available |
| `app/views/sessions/new.html.erb` | MODIFY | Fix WVU branding, apply CSS classes |
| `app/views/dashboard/index.html.erb` | MODIFY | Apply glasmorphic styling, remove inline styles |

---

## Verification Steps (For Planning Agent Review)

After Qwen completes work:

1. ✅ **CSS Compilation**
   ```bash
   cd galaxy-game-ui
   bundle exec rails assets:precompile 2>&1 | grep -i warn
   # Should return: (no warnings)
   ```

2. ✅ **Session View Test**
   ```bash
   curl http://localhost:3000/sessions/new | grep -c 'wvu-'
   # Should return: 0
   ```

3. ✅ **Visual Check** (screenshot comparison)
   - Session view: Should match EVE Dashboard login aesthetic
   - Dashboard: Should have glasmorphic panels, cyan accents, visual depth

4. ✅ **Responsive Test**
   - Open in browser DevTools
   - Test each breakpoint: 320px, 768px, 1024px, 1320px
   - Verify panels stack correctly, text readable, no overflow

5. ✅ **No Regressions**
   - MockDataHelper still works (check `http://localhost:3000/dashboard`)
   - All panels render with data
   - No console errors

---

## Success Criteria

Phase 1 is complete when:

✅ EVE Dashboard CSS extracted and customized  
✅ Session/login view renders with Galaxy Game branding (no WVU references)  
✅ Dashboard panels have glasmorphic styling  
✅ CSS compiles without warnings  
✅ Visual comparison shows significant improvement over current state  
✅ All responsive breakpoints work  
✅ Planning Agent verifies and approves completion  

**THEN**: Phase 2 (Landing page, account view, admin skeleton) can begin  
**THEN**: Phase 3 (Dashboard panels 2-5, wormholes) can begin

---

## Notes for Qwen

- This is **blocking work** — many other tasks depend on CSS foundation being solid
- Prioritize visual polish over code perfection; this is a UI prototype
- EVE Dashboard aesthetics are reference only; adapt as needed for Galaxy Game
- Keep MockDataHelper approach (no database); all styling is CSS/layout only
- Use CSS variables consistently — makes future theming/rebranding easier
- Test responsive layout frequently; mobile users are important
- Don't overthink; this is iterative — refinement can happen in Phase 3

---

## Handoff Reference

**Quick Reference for Qwen**:  
See `/memories/session/` or `handoffs/qwen(planning)/` for session context  
See `CSS-INTEGRATION-STRATEGY.md` for full planning document  
See `MAIN-PROJECT-UI-RESEARCH.md` for Galaxy Game patterns  
See EVE Dashboard (`/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`) for reference CSS  

**Time Budget**: 3-3.5 hours (1.5-2h + 45min + 1h)  
**Priority**: CRITICAL PATH — blocks all other work  
**Approval**: Requires Planning Agent verification before Phase 2 dispatch
