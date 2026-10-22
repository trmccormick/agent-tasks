# CSS Styling Fix Dispatch — Galaxy Game UI (2026-10-22)

**To**: Qwen  
**From**: Planning Agent  
**Status**: Ready for dispatch (awaiting human approval)  
**Premium usage**: 73% (approved for careful use)  
**Task file**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/backlog/2026-10-22-HIGH-FIX-SESSION-LOGIN-AND-DASHBOARD-STYLING.md`

---

## Quick Summary

**Problem**: Two CSS styling issues blocking Tasks 3-4 from launching:

1. **Session/login view** uses WVU color classes (`wvu-blue`, `wvu-gold`, etc.) that don't exist in Galaxy Game Tailwind config
   - Form is broken/unreadable
   - File: `app/views/sessions/new.html.erb`

2. **Dashboard CSS incomplete** — has bare structure but lacks EVE Dashboard aesthetic (glassmorphic panels, proper borders, shadows)
   - File: `app/views/dashboard/index.html.erb`
   - Should match reference: localhost:8765 (EVE Dashboard)

---

## What to Implement

### Session View (`app/views/sessions/new.html.erb`)
- Replace "WVU Libraries Systems" → "Galaxy Game — Commander Login"
- Replace all `wvu-*` classes with Galaxy Game theme variables:
  - `wvu-blue` → `text-galaxy-accent` or `text-neon-cyan`
  - `wvu-warm-gray-*` → `text-galaxy-text*` variants
  - `wvu-gold` → `neon-gold`
- Add glassmorphic styling: `bg-glass` + `backdrop-blur-md`
- Verify form inputs have proper contrast and focus states

### Dashboard View (`app/views/dashboard/index.html.erb`)
- Panel H header: gradient `from-galaxy-secondary/40 to-galaxy-accent/20`, strong border
- Panel 1 inventory: `bg-glass backdrop-blur-md`, enhanced borders/shadows
- Progress bars: darker track (`bg-gray-900/60`), gradient fill (`from-galaxy-accent to-galaxy-secondary`)
- Labels: proper text contrast using Galaxy Game theme colors
- Remove hardcoded hex colors (e.g., `#e0e0e0`, `cyan-900`) → use theme variables

---

## Verification Checklist (Built Into Task File)

- [ ] Session view readable (no broken classes)
- [ ] Dashboard panels match EVE Dashboard visual style (screenshot comparison)
- [ ] All Galaxy Game theme colors applied
- [ ] No `wvu-*` classes remain (`grep -r "wvu-" galaxy-game-ui/app/` returns 0)
- [ ] No Tailwind compiler warnings
- [ ] Live curl tests pass: `GET /sessions/new` HTTP 200, `GET /dashboard` HTTP 200

---

## Time Estimate

~1.5-2 hours (mostly find/replace + testing styling)

---

## After Dispatch

Once Qwen completes this, Planning Agent will:
1. Proof implementation for Tailwind accuracy
2. Verify live screenshots match design intent
3. Mark complete + trigger Task 3-4 unblock

**Questions**: Ask Human if anything is unclear. Do NOT proceed beyond the scope of this task file.
