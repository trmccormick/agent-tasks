# Player Planetary Layout Refactor — Implementation Plan

**Created**: 2026-11-16  **Type**: MEDIUM refactor  **Scope**: narrow layout/organization

## Goal

Refactor the Player Planetary view to use the shared dashboard shell (single sidebar, topbar, main content area) instead of its own nested sidebar/main/footer. Populate with labeled mock data. Organize CSS into a page stylesheet. Keep routes and nav entry correct so Tracy can tune styling.

## Files Referenced (read-only)

- `galaxyGame/galaxy_game/app/views/admin/celestial_bodies/planetary.html.erb` — layout reference (arrangement only; drop Admin controls)
- `galaxyGame/docs/wiki/ui/02_player_view_architecture.md` — three primary views established intent
- `galaxyGame/docs/wiki/ui/04_current_ui_state_and_known_issues.md` — known issues context

## Files to Edit (allowed list)

1. `galaxy-game-ui/app/views/player/planetary.html.erb` — layout and markup
2. `galaxy-game-ui/app/controllers/player_controller.rb` — add mock data instance variables
3. `galaxy-game-ui/config/routes.rb` — verify player scope grouping (no changes needed if already correct)
4. `galaxy-game-ui/app/views/layouts/dashboard.html.erb` — move Planetary nav entry under Player heading, add active-link highlight
5. `galaxy-game-ui/app/assets/stylesheets/pages/player_planetary.css` — new page stylesheet

## Prerequisites

- Task file moved to `active/`, status set to `active`.
- Prototype repo on `main`, clean working tree.
- Container `galaxy_game` running (assumed always running per GUARDRAILS).

## Expected Outcomes

1. **Routes**: Player routes grouped under one `player` scope; `/player/planetary` works.
2. **Nav**: Planetary link under a "Player" heading in the sidebar, not under "Galaxy". Active-link highlights on current page.
3. **View**: One sidebar (shared shell). No nested `<main>` or `<footer>`. Layout: header line → toolbar above map → map center → data panel right.
4. **Mock data**: Inline and clearly labeled "MOCK DATA" on the page. Values not changed.
5. **Styles**: Inline `style=""` attributes moved to page stylesheet where practical.

## Gotchas to Avoid

- Do NOT edit terrain-service, Docker/compose, or AI Manager files (commit 0594add — out of scope).
- Do NOT rewrite the map renderer or fix JS issues — report them.
- Do NOT add surface/settlement/space pages yet.
- Do NOT commit without explicit human approval.
- Do NOT remove mock data values — they are ported from main app and labeled.
- The dashboard layout already wraps `yield` in `<main class="flex-1 overflow-y-auto p-6">` with a `max-w-6xl mx-auto` div. The planetary content needs to fit inside that container, not replace it.

## Implementation Order

1. **Controller** — Add mock data instance variables (`@atmosphere`, `@biosphere`, `@geosphere`, `@hydrosphere`, `@planet_facts`).
2. **Routes** — Verify grouping (likely no changes needed).
3. **Nav** — Move Planetary under "Player" heading in dashboard layout; add active-link class for current page.
4. **View** — Rewrite to use shared shell: header line with body facts, toolbar above map, map canvas center, data panel right. Remove own sidebar/main/footer. Use instance variables for mock data display. Move inline styles to stylesheet.
5. **Stylesheet** — Create `player_planetary.css` with all layout classes.

## Verification

- Start container workflow, open `/player/planetary` from sidebar and by URL.
- Report console errors, failed requests. Take screenshot.
- Write report to `~/scratch/planetary-layout-report.md`.
