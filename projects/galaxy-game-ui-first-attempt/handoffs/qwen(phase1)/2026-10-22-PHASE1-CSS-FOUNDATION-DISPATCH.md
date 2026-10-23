# QWEN DISPATCH — Phase 1: CSS Foundation (CRITICAL PATH)

**Priority**: 🔴 **CRITICAL — Blocks all other work**  
**Status**: Ready for Dispatch  
**Date**: 2026-10-22  
**Estimate**: 3-3.5 hours  
**Reference**: `2026-10-22-CRITICAL-PHASE1-CSS-FOUNDATION-EVE-DASHBOARD-INTEGRATION.md`

---

## TL;DR — What You're Doing

**Adopt EVE Dashboard CSS as foundation, fix Galaxy Game branding issues, add glasmorphic styling.**

This unblocks:
- Phase 2 (landing page, account view, admin interface)
- Phase 3 (remaining dashboard panels, wormholes view)
- All subsequent feature work

**Three Sub-Tasks**:

### 1.1: CSS Foundation Setup (1.5-2h)
- Extract EVE Dashboard CSS into `eve-dashboard-base.css`
- Create Galaxy Game theme overrides in `galaxy-game-theme.css`
- Update CSS variables (cyan accent instead of blue, add galaxy colors)
- Update application.css import order

### 1.2: Fix Session/Login View (45 min)
- Remove all `wvu-*` class references from `app/views/sessions/new.html.erb`
- Replace with Galaxy Game branding ("Commander Login" instead of "WVU Libraries")
- Apply proper CSS classes from EVE Dashboard foundation
- Use `.btn.primary` for submit button, CSS variables for colors

### 1.3: Dashboard Styling Polish (1h)
- Apply `.dashboard-panel` glasmorphic styling to all panels
- Add backdrop-filter blur, proper borders, shadows
- Style progress bars with gradient (`--accent` to `--secondary`)
- Ensure hover effects work, visual hierarchy clear

**Total**: 3-3.5 hours  
**Complexity**: Medium (copy-paste foundation + styling updates)

---

## Key Details

### EVE Dashboard Foundation
**Reference**: `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`

Copy these sections:
- CSS variables (`:root { --bg, --panel, --panel2, --line, --text, --muted, --accent, --green, --red }`)
- Layout framework (`.topbar`, `.panel`, `.btn`, `.toolbar`, `.main-inner`)
- Scrollbar styling
- Typography
- Table/grid utilities

**DO NOT** copy:
- EVE-specific components (account sections, fleet palette, theme toggle)

### Galaxy Game Color Overrides
```css
:root {
  --accent: #00d4ff;         /* Cyan instead of EVE blue #4aa8ff */
  --accent-dark: #0099cc;
  --accent-glow: rgba(0, 212, 255, 0.5);
  
  /* Additional (from main Galaxy Game project) */
  --secondary: #9d4edd;      /* Purple */
  --success: #06ffa5;        /* Green */
  --warning: #ffd60a;        /* Gold */
  --danger: #ff006e;         /* Red */
}
```

### Session View Branding Changes
```erb
<!-- CHANGE: WVU branding → Galaxy Game -->
WVU Libraries Systems → Galaxy Game
Donor Check-In → Commander Login
Enter your email address → commander@galaxy.com

<!-- CHANGE: CSS classes -->
class: "... wvu-blue" → style: "color: var(--accent)"
class: "... border-wvu-warm-gray-light" → style: "border: 1px solid var(--line)"
class: "... bg-wvu-gold hover:text-wvu-blue" → class: "btn primary"
```

### Dashboard Glasmorphic Styling
```css
.dashboard-panel {
  background: rgba(22, 27, 33, 0.95);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(0, 212, 255, 0.25);
  box-shadow: 0 0 20px rgba(0, 212, 255, 0.05);
}

.panel-header {
  color: var(--accent);
  border-bottom: 1px solid rgba(0, 212, 255, 0.2);
}

.progress-bar-galaxy {
  background: linear-gradient(90deg, #00d4ff, #9d4edd);
}
```

---

## Acceptance Criteria (For You to Verify)

### Task 1.1 ✅
- [ ] `app/assets/stylesheets/eve-dashboard-base.css` exists
- [ ] `app/assets/stylesheets/galaxy-game-theme.css` exists with overrides
- [ ] `bundle exec rails assets:precompile` runs without warnings
- [ ] All CSS variables defined (--bg, --panel, --accent, --secondary, etc.)

### Task 1.2 ✅
- [ ] `curl http://localhost:3000/sessions/new | grep -c 'wvu-'` returns 0
- [ ] Form renders without broken styling
- [ ] Inputs are readable (proper contrast)
- [ ] Submit button says "Sign In" (not WVU branding)
- [ ] Title says "Commander Login — Galaxy Game"

### Task 1.3 ✅
- [ ] All `.dashboard-panel` divs have glasmorphic styling
- [ ] Panel headers styled with `.panel-header` (cyan, underline)
- [ ] Progress bars have gradient (cyan → purple)
- [ ] Status badges have color coding
- [ ] Mobile responsive (test at 320px, 768px, 1024px, 1320px)
- [ ] Hover effects work (border/shadow change on panel hover)

### Final Verification ✅
- [ ] Screenshot comparison with EVE Dashboard shows visual improvement
- [ ] No console errors or CSS warnings
- [ ] All panels render with MockDataHelper data
- [ ] Responsive layout tested and approved

---

## Files You'll Touch

| File | Action | What |
|------|--------|------|
| `app/assets/stylesheets/eve-dashboard-base.css` | CREATE | EVE CSS foundation |
| `app/assets/stylesheets/galaxy-game-theme.css` | CREATE | Galaxy Game overrides |
| `app/assets/stylesheets/application.css` | MODIFY | Import order |
| `config/tailwind.config.js` | VERIFY | No conflicts |
| `app/views/sessions/new.html.erb` | MODIFY | Fix WVU branding |
| `app/views/dashboard/index.html.erb` | MODIFY | Apply glasmorphic CSS |

---

## Testing Commands

```bash
# Navigate to project
cd /Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui

# Verify CSS compilation
bundle exec rails assets:precompile 2>&1 | grep -i warn

# Check for WVU references (should be 0)
curl http://localhost:3000/sessions/new | grep -c 'wvu-'

# Visual test (open in browser)
http://localhost:3000/sessions/new    # Login form
http://localhost:3000/dashboard       # Dashboard panels
```

---

## Reference Documents

- **Full Task**: `2026-10-22-CRITICAL-PHASE1-CSS-FOUNDATION-EVE-DASHBOARD-INTEGRATION.md`
- **Strategy**: `CSS-INTEGRATION-STRATEGY.md` (complete planning with Phase 2/3)
- **Research**: `MAIN-PROJECT-UI-RESEARCH.md` (Galaxy Game UI patterns)
- **EVE Dashboard**: `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`

---

## Notes

- This is **blocking work** — don't rush, make it solid
- EVE Dashboard is reference, not copy-paste; adapt as needed
- CSS variables make future theming easy
- Keep MockDataHelper approach (UI/layout only, no DB changes)
- Test mobile responsive frequently (not an afterthought)
- Visual polish matters here — this is UI prototype showpiece

---

## When Complete

After you finish:
1. Run verification commands above
2. Take screenshots (login form, dashboard panels)
3. Push to feature branch with descriptive commit
4. Planning Agent will review and verify
5. If approved: Phase 2 (landing page, account, admin) unblocks
6. If needs fixes: We iterate quickly

---

**Ready? Let's go!**

See full task file: `2026-10-22-CRITICAL-PHASE1-CSS-FOUNDATION-EVE-DASHBOARD-INTEGRATION.md`
