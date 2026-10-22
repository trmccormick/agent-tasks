# Task: Fix Session/Login View & Dashboard CSS Styling

**Date Created**: 2026-10-22  
**Priority**: HIGH  
**Type**: BUG FIX + ENHANCEMENT  
**Estimated Scope**: ~2-3 hours  
**Context**: WVU branding leakage in session view; dashboard CSS incomplete vs. EVE Dashboard reference

---

## Overview

The galaxy-game-ui prototype has two interconnected styling issues:

1. **Session/Login View**: Contains stale WVU Libraries branding (title, form labels, color classes) that don't exist in the Galaxy Game Tailwind theme. Form is currently unreadable due to non-existent `wvu-*` CSS classes.

2. **Dashboard CSS Incomplete**: Task 2 implemented the structure (header + inventory panels) but missed the visual polish that was designed. Panels lack glassmorphic styling, proper borders, shadows, and visual hierarchy that the EVE Dashboard reference establishes.

**Design Intent**: 
- Session/login should match Galaxy Game domain (no WVU branding)
- Dashboard panels should adopt EVE Dashboard aesthetic (glassmorphic cards, cyan accents, proper visual separation)
- Entire flow should feel like a cohesive prototype using Galaxy Game terminology and EVE-inspired styling

**Current State (VERIFIED by screenshot)**: 
- Session view: broken/unreadable (WVU classes missing from Tailwind config)
- Dashboard: functional but minimal (bare structure, no visual polish)

---

## Acceptance Criteria

### Session/Login View Fix

- [ ] **A1**: Title changed from "WVU Libraries Systems" to "Galaxy Game — Commander Login"
- [ ] **A2**: Subtitle changed from "WVU Libraries Systems — Donor Check-In" to "Enter your credentials to access the galaxy"
- [ ] **A3**: All `wvu-*` CSS classes replaced with Galaxy Game theme classes:
  - `wvu-blue` → `text-galaxy-accent` or `text-neon-cyan`
  - `wvu-warm-gray-light` → `border-galaxy-text-dim` or `border-eve-line`
  - `wvu-warm-gray-medium` → `text-galaxy-text-dim`
  - `wvu-warm-gray-dark` → `text-galaxy-text`
  - `wvu-accent-blue` → `galaxy-accent` or `neon-cyan`
  - `wvu-gold` → `neon-gold`
- [ ] **A4**: Form container has glassmorphic styling:
  - `bg-glass` or `bg-[rgba(26,26,46,0.85)]`
  - `border-glass` or `border-[rgba(74,168,255,0.2)]`
  - Backdrop blur effect via inline style or CSS class
- [ ] **A5**: Form inputs have proper focus states with galaxy theme colors
- [ ] **A6**: Submit button uses `bg-galaxy-accent` hover effect; no WVU colors
- [ ] **A7**: "Forgot password?" link uses galaxy theme colors
- [ ] **A8**: GET /sessions/new returns HTTP 200 with properly styled form (no broken classes)

### Dashboard CSS Enhancement

- [ ] **D1**: Panel H (header) has improved visual hierarchy:
  - Gradient background using Galaxy Game colors: `from-galaxy-secondary/40 to-galaxy-accent/20`
  - Stronger border: `border-b border-galaxy-accent/40`
  - Proper text contrast with `text-galaxy-text`
- [ ] **D2**: Panel 1 (inventory) has glassmorphic styling:
  - Background: `bg-glass` + `backdrop-blur-md` (or inline `rgba(26,26,46,0.85)` + `style="backdrop-filter: blur(10px)"`)
  - Border: `border-galaxy-accent/30` (stronger than current `border-cyan-900/30`)
  - Rounded corners: `rounded-lg` (already present, verify)
  - Hover effect: `hover:border-galaxy-accent/60` (more pronounced than current)
  - Box shadow: `shadow-lg` or `shadow-[0_0_20px_rgba(0,212,255,0.1)]` for subtle glow
- [ ] **D3**: Progress bars have enhanced visibility:
  - Track background: `bg-gray-900/60` (darker, more contrast)
  - Gradient fill: `from-galaxy-accent to-galaxy-secondary` or `from-neon-cyan to-neon-purple`
  - Height: `h-2` (current) or increase to `h-3` for visibility
- [ ] **D4**: Resource labels have proper contrast:
  - Item name: `text-galaxy-text`
  - Quantity: `text-galaxy-text-dim` or `text-eve-muted`
- [ ] **D5**: Heading styling improved:
  - Panel title (e.g., "Local Bay Inventory"): `text-lg font-semibold text-galaxy-accent` (change from `text-cyan-400`)
- [ ] **D6**: Spacing and padding consistent:
  - Outer grid gap: `gap-6` (or `gap-4`, verify against design)
  - Panel padding: `p-6` (or `p-4`, verify)
  - Item spacing: `mb-3` or `mb-4` (consistent within panel)

### Verification

- [ ] **V1**: curl -s http://localhost:3000/sessions/new | grep -c 'wvu-' returns 0 (no WVU classes remain)
- [ ] **V2**: curl -s http://localhost:3000/dashboard | grep -c 'wvu-' returns 0 (dashboard clean)
- [ ] **V3**: Live screenshot of /sessions/new shows readable login form with Galaxy Game branding
- [ ] **V4**: Live screenshot of /dashboard shows polished panels with glassmorphic styling matching EVE Dashboard reference
- [ ] **V5**: Form inputs on /sessions/new are visually distinct from background (proper contrast)
- [ ] **V6**: Focus states on form inputs show cyan highlight (proper accessibility)
- [ ] **V7**: No Tailwind warnings in browser console (all classes resolved)

---

## Implementation Details

### Files to Modify

#### 1. `app/views/sessions/new.html.erb`
**Current State**: Uses WVU branding and non-existent CSS classes  
**Changes Required**:
- Replace page title: "WVU Libraries Systems" → "Galaxy Game — Commander Login"
- Replace subtitle text and page heading
- Replace all `wvu-*` class references with Galaxy Game theme equivalents
- Add glassmorphic container styling
- Update button/link colors

**Diff preview** (illustrative):
```erb
<% content_for :title, "Commander Login — Galaxy Game" %>

<div class="mx-auto max-w-md">
  <div class="rounded-lg border border-galaxy-accent/30 bg-glass backdrop-blur-md p-8 shadow-lg shadow-galaxy-accent/10">
    <h1 class="text-2xl font-bold text-galaxy-accent">Commander Login</h1>
    <p class="mt-1 mb-6 text-sm text-galaxy-text-dim">
      Enter your credentials to access the galaxy
    </p>

    <%= form_with url: session_path do |form| %>
      <div class="space-y-4">
        <div>
          <%= form.label :email_address, class: "mb-1.5 block text-sm font-semibold text-galaxy-text" %>
          <%= form.email_field :email_address, required: true, autofocus: true, autocomplete: "username",
                placeholder: "Enter your email address", value: params[:email_address],
                class: "w-full rounded-lg border border-galaxy-accent/20 bg-gray-900/40 px-4 py-3.5 text-base text-galaxy-text placeholder-galaxy-text-dim focus:border-galaxy-accent focus:ring-2 focus:ring-galaxy-accent/30 outline-none" %>
        </div>
        <div>
          <%= form.label :password, class: "mb-1.5 block text-sm font-semibold text-galaxy-text" %>
          <%= form.password_field :password, required: true, autocomplete: "current-password",
                placeholder: "Enter your password", maxlength: 72,
                class: "w-full rounded-lg border border-galaxy-accent/20 bg-gray-900/40 px-4 py-3.5 text-base text-galaxy-text placeholder-galaxy-text-dim focus:border-galaxy-accent focus:ring-2 focus:ring-galaxy-accent/30 outline-none" %>
        </div>
        <%= form.submit "Sign in",
              class: "w-full rounded-lg bg-galaxy-accent px-6 py-3.5 text-base font-semibold text-galaxy-dark shadow-lg hover:bg-neon-cyan hover:shadow-neon-cyan/40 transition-all" %>
      </div>
    <% end %>

    <div class="mt-5 text-center">
      <%= link_to "Forgot password?", new_password_path, class: "text-sm font-medium text-galaxy-accent hover:text-neon-cyan transition-colors" %>
    </div>
  </div>
</div>
```

#### 2. `app/views/dashboard/index.html.erb`
**Current State**: Minimal styling, some hardcoded colors (cyan-900, cyan-400, purple-900)  
**Changes Required**:
- Replace hardcoded color classes with Galaxy Game theme variables
- Add glassmorphic container styling with backdrop-filter
- Enhance borders and shadows for visual hierarchy
- Improve progress bar visibility and styling

**Diff preview** (illustrative for Panel H + Panel 1):
```erb
<!-- Panel H: Location & Character Header -->
<div class="bg-gradient-to-r from-galaxy-secondary/40 to-galaxy-accent/20 border-b border-galaxy-accent/40 px-6 py-4 mb-6">
  <h1 class="text-3xl font-bold text-galaxy-text"><%= @system_status[:player_name] %></h1>
  <h2 class="text-lg font-semibold text-galaxy-accent">Location: <%= @system_status[:system_name] %></h2>
  <p class="text-sm text-galaxy-text-dim">GCC <%= number_with_delimiter(@system_status[:player_gcc]) %></p>
  <p class="text-xs text-gray-600"><%= @system_status[:timestamp] %></p>
</div>

<!-- Panel 1: Local Bay Inventory -->
<div class="grid grid-cols-1 lg:grid-cols-2 gap-6 p-6">
  <div class="bg-glass backdrop-blur-md border border-galaxy-accent/30 rounded-lg p-6 hover:border-galaxy-accent/60 transition-colors shadow-lg shadow-galaxy-accent/5">
    <h3 class="text-lg font-semibold text-galaxy-accent mb-4">Local Bay Inventory</h3>
    <% @bay_items.each do |item| %>
      <div class="mb-4 pb-4 border-b border-galaxy-accent/10 last:border-b-0">
        <div class="flex justify-between text-sm mb-2">
          <span class="text-galaxy-text font-medium"><%= item[:resource] %></span>
          <span class="text-galaxy-text-dim"><%= number_with_delimiter(item[:quantity]) %>/<%= number_with_delimiter(item[:max_qty]) %> <%= item[:unit] %></span>
        </div>
        <div class="w-full bg-gray-900/60 rounded-full h-3 overflow-hidden border border-galaxy-accent/10">
          <div class="bg-gradient-to-r from-galaxy-accent to-galaxy-secondary h-3 rounded-full transition-all duration-300" style="width: <%= [item[:quantity].to_f / item[:max_qty] * 100, 100].min %>%"></div>
        </div>
      </div>
    <% end %>
  </div>
  <!-- Panel 2 placeholder for Task 3 -->
</div>
```

#### 3. `config/tailwind.config.js` (Already correct, NO CHANGES NEEDED)
- Verify that all Galaxy Game colors are properly defined
- Verify that `glass` background class exists
- Verify that `glass` border color class exists

---

## Research Notes

### EVE Dashboard Reference
- **File**: `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`
- **Color Palette**:
  - Main background: `#0e1114` (eve-dark)
  - Panel background: `#161b21` (eve-panel)
  - Text: `#d8dee6` (eve-text)
  - Accent: `#4aa8ff` (eve-accent)
- **Key patterns**:
  - Glassmorphic cards with `backdrop-filter: blur(10px)`
  - Subtle borders using accent color with low opacity (0.2-0.3)
  - Gradient fills for progress and visual elements
  - Shadow effects with accent color glow for depth

### Galaxy Game Theme (Already in tailwind.config.js)
- Primary accent: `galaxy-accent: #00d4ff` (cyan)
- Secondary accent: `galaxy-secondary: #9d4edd` (purple)
- Dark backgrounds: `galaxy-dark: #0a0a0a`, `galaxy-panel: #1a1a2e`
- Text: `galaxy-text: #e0e0e0`, `galaxy-text-dim: #8892b0`

### Tailwind Utilities for Glassmorphic Effect
- Backdrop blur: `backdrop-blur-md` or `style="backdrop-filter: blur(10px)"`
- Transparency: `rgba(26,26,46,0.85)` via inline or `bg-glass` helper class
- Borders with transparency: `border-galaxy-accent/30` (30% opacity)
- Shadows with color: `shadow-lg shadow-galaxy-accent/5`

---

## Gotchas & Notes

1. **Backdrop-filter browser support**: All modern browsers support it; inline `style` attribute is most reliable in Rails templates
2. **Tailwind opacity syntax**: Use `border-galaxy-accent/30` (not `border-galaxy-accent-30`); this is Tailwind 3+ syntax
3. **WVU class purge**: Ensure no `wvu-*` classes remain in templates or stylesheets; verify with `grep -r "wvu-" galaxy-game-ui/app/`
4. **Color contrast**: WCAG AA requires 4.5:1 contrast for small text; cyan on dark background meets this; test with eyedropper
5. **Responsive**: Mobile view should stack panels; grid breakpoint `lg:` already in place; verify on small screen
6. **Preview**: Test changes live at http://localhost:3000/ and http://localhost:3000/sessions/new before commit

---

## Dispatch & Review

**Dispatch to**: Qwen (implementation)  
**Review by**: Planning Agent (proof for Tailwind class accuracy, Galaxy Game terminology consistency)  
**Final approval**: Human (visual/accessibility check via live screenshots)

**Estimated time**: 2-3 hours (mostly finding/replacing, testing styling)  
**Blocking**: Tasks 3-4 can proceed in parallel (they use same patterns once this is established)

---

## Definition of Done

1. Session view displays with Galaxy Game branding ✓
2. All form inputs readable with proper contrast ✓
3. Dashboard panels have glassmorphic polish ✓
4. Progress bars visible and prominent ✓
5. No WVU branding or classes remain ✓
6. No Tailwind compiler warnings ✓
7. Live comparison with EVE Dashboard shows similar aesthetic quality ✓
8. Commit message references this task and screenshot verification ✓
