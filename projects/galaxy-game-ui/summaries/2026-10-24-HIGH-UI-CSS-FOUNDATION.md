# Synthesis Report: T3a — UI CSS Foundation

**Task**: `2026-10-23-HIGH-UI-CSS-FOUNDATION.md`
**Date**: 2026-10-24
**Agent**: Implementation Agent (Qwen)
**Status**: In Progress (synthesis complete, awaiting implementation)

---

## Pre-Implementation Inspection Summary

### Layout Analysis (application.html.erb)
- **Line 20**: `<%= stylesheet_link_tag :app, "data-turbo-track": "reload" %>`
- **Line 31**: `<body class="min-h-screen flex flex-col bg-wvu-warm-gray-light">` — uses WVU classes
- The layout currently loads `stylesheet_link_tag :app` which maps to Propshaft → finds `app/assets/stylesheets/application.css` (the manifest file, not the tailwind build)

### Key Finding ⚠️
The task says `stylesheet_link_tag :app` resolves via Propshaft to the built tailwind file, but:
- `stylesheet_link_tag :app` maps to **Propshaft** → finds `app/assets/stylesheets/application.css` (the 11-line empty manifest)
- It does **NOT** map to `app/assets/tailwind/application.css` automatically

The tailwind gem builds from `app/assets/tailwind/application.css` → outputs to `app/assets/builds/tailwind/` directory. The built CSS must be linked explicitly OR the layout's stylesheet_link_tag must reference the correct asset path.

### Current State
- **Tailwind entry**: `galaxy-game-ui/app/assets/tailwind/application.css` — has @theme block with WVU brand palette (11 color definitions) + one utility class `.woodburn-hall`
- **CSS manifest**: `galaxy-game-ui/app/assets/stylesheets/application.css` — empty (11 lines of boilerplate comments only)
- **Built output**: `galaxy-game-ui/app/assets/builds/tailwind.css` — exists, contains WVU tokens and Tailwind v4 utilities
- **WVU class occurrences**: 35 files total (26 in application.html.erb, 9 in session views)

### Container Info
- UI app container: `galaxy_game` (NOT the main game server's `web` container)
- Rails commands must run in: `docker exec galaxy_game bash -c 'unset DATABASE_URL && RAILS_ENV=...'`

---

## Implementation Plan

1. **Modify** `app/assets/tailwind/application.css`: Add Galaxy Game tokens as CSS custom properties in :root
2. **Rebuild** via `bin/rails tailwindcss:build` inside container
3. **Verify** built output contains all 10 tokens
4. **Screenshot** `/` and `/session/new` pages

---

## Synthesis Gate

- [x] Prerequisites read (GUARDRAILS.md, README.md)
- [x] Pre-inspection completed
- [x] Gotchas documented
- [ ] Implementation started
- [ ] Implementation verified
- [ ] Screenshots saved
- [ ] Awaiting human approval

---

## Notes for Next Agent Session

If the `stylesheet_link_tag :app` resolution is broken (maps to empty manifest instead of tailwind build), this task may need escalation to determine correct approach:
- Option A: Change layout to link built file directly (`asset_path("builds/tailwind")`)
- Option B: Keep :app and ensure Propshaft resolves correctly
- This MUST be resolved before proceeding with implementation
