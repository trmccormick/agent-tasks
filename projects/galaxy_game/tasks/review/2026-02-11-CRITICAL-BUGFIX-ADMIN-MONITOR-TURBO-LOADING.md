[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-02-11-CRITICAL-BUGFIX-ADMIN-MONITOR-TURBO-LOADING.md]
[RATIONALE: UI/monitoring or investigation task — not Luna sim prerequisite, cosmetic/post-MVP]
---
status: not-started
priority: CRITICAL
type: bugfix
created: 2026-02-11
last-updated: 2026-06-22
phase: 5+
---

# Admin Monitor Canvas — Fix Turbo/Hotwire First-Load Rendering

## Context
Admin planetary view (`app/views/admin/celestial_bodies/planetary.html.erb`) displays a blank canvas on initial page load. The canvas element exists in the DOM but no rendering occurs until manual page refresh. This blocks terraforming visualization and GGMap development.

**Current behavior**: Canvas loads blank on first visit, requires refresh to display planetary map  
**Expected behavior**: Canvas renders planetary map immediately on page load without manual refresh

**Root cause**: JavaScript initialization timing conflict with Turbo/Hotwire lifecycle events

## Problem Statement
Canvas element present in DOM but does not render on initial page load. Requires manual refresh to display map. No browser console errors; issue is JavaScript-Turbo timing conflict.

## Scope
- Fix Turbo/Hotwire lifecycle integration for monitor.js
- Ensure canvas renders on first load without manual refresh
- Add loading indicators and error handling
- Support both traditional page loads and Turbo frame navigation
- No changes to canvas rendering logic itself (only initialization timing)

## Target Files
- `app/javascript/admin/monitor.js`
- `app/views/admin/celestial_bodies/planetary.html.erb`
- `config/importmap.rb`
- `spec/features/admin/planetary_monitor_spec.rb`
---

## Acceptance Criteria
- [ ] Canvas renders on initial page load (no manual refresh required)
- [ ] JavaScript initialization fires correctly with Turbo `turbo:load` events
- [ ] Canvas renders on both traditional page loads and Turbo frame navigation
- [ ] Loading indicator displays while data loads
- [ ] Error handling for missing/invalid data
- [ ] No JavaScript errors in browser console
- [ ] RSpec: All monitor specs pass (0 failures)
- [ ] Related admin view specs unaffected

---

## Implementation Steps
1. **Pin monitor.js in importmap** — Add `pin "admin/monitor"` to `config/importmap.rb`
2. **Add JS include to view** — Include monitor.js in planetary.html.erb with Turbo-aware attributes
3. **Replace DOMContentLoaded with Turbo hooks** — Implement `turbo:load` event listener in monitor.js
4. **Handle data readiness** — Check for monitor data in DOM or wait for API response via event dispatch
5. **Add Turbo compatibility fallback** — Support traditional page loads when Turbo not present
6. **Add error handling** — Graceful error display if canvas initialization fails
7. **Test all navigation paths** — Verify direct load, Turbo frame navigation, and refresh scenarios
8. **Run RSpec** — Execute `bundle exec rspec spec/features/admin/planetary_monitor_spec.rb`

## Dependencies
- Requires: Existing planetary.html.erb view and monitor.js script
- Requires: Canvas rendering functions (renderCelestialLayers, initializeCanvas) already defined in monitor.js
- Blocks: GGMap visualization work (needs working canvas first)
- Blocks: Terraforming views (needs functional monitor rendering)

## Notes
- **Critical blocker**: Canvas rendering is foundational for all planetary visualization work
- Prioritize above other admin/UI work in phase5+
- Root cause likely Turbo/Hotwire lifecycle conflict — JS executes before DOM elements available
- Consider async data loading if celestial body data is fetched asynchronously
- Add console logging to trace initialization order for debugging if needed
