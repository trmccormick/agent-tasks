---
status: backlog
priority: MEDIUM
type: bugfix
system_domain: UI_ADMIN_MONITOR
mvp_alignment: NONE_UI_INFRASTRUCTURE
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Bug Fix Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ui/2026-07-06-MEDIUM-BUGFIX-ADMIN-MONITOR-TURBO-CANVAS-INIT.md

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv to new folder
  - New/untracked file: move with filesystem (mv), then git add the final path
  - Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# BUGFIX: Admin Monitor Canvas — Fix Turbo/Hotwire First-Load Rendering

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bugfix
**Created**: 2026-07-06 — Planning Agent (Qwen3.6-35b)
**Original stale task**: `deprecated/2026-02-11-CRITICAL-BUGFIX-ADMIN-MONITOR-TURBO-LOADING.md`

---

## Context

Admin planetary view (`app/views/admin/celestial_bodies/planetary.html.erb`) displays a blank canvas on initial page load. The canvas element exists in the DOM but no rendering occurs until manual page refresh. This blocks terraforming visualization and GGMap development.

**Current behavior**: Canvas loads blank on first visit, requires refresh to display planetary map
**Expected behavior**: Canvas renders planetary map immediately on page load without manual refresh

**Root cause**: JavaScript initialization timing conflict with Turbo/Hotwire lifecycle events

**Phase alignment**: UI infrastructure task — not urgent for any current phase. Becomes relevant for Phase 14+ terraforming simulation work when admin visualization is needed for observation. Placed in `backlog/ui/` as non-blocking UI improvement.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (BUGFIX Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Phase Structure**: `docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` — UI infrastructure, not phase-gated
4. **This Task File**: Everything below

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: Partial fix already applied — `turbo:load` listener exists but may not fire correctly
- ❌ Wrong: Assume no Turbo hooks exist and start from scratch
- ✅ Right: Audit existing `turbo:load` + `DOMContentLoaded` listeners (lines 1364-1366 of monitor.js) to understand why first-load still fails
- Why: The stale task was created in Feb 2026; partial fix may have been applied since then

⚠️ **GOTCHA 2**: Monitor.js is included via `javascript_include_tag` with `data-turbo-track: reload`
- ❌ Wrong: Assume importmap-based loading (it's not pinned in importmap.rb)
- ✅ Right: The script is loaded as a traditional asset, not via importmap — Turbo tracking attribute may affect reload behavior
- Why: `config/importmap.rb` does NOT pin `admin/monitor` — it uses `javascript_include_tag`

⚠️ **GOTCHA 3**: Multiple views share the same monitor.js
- ❌ Wrong: Fix only `planetary.html.erb` in isolation
- ✅ Right: Test fix across ALL views that include monitor.js (planetary, regional, monitor)
- Why: `admin/celestial_bodies/regional.html.erb` and `monitor.html.erb` also include the same script

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: Fix Admin Monitor Canvas Turbo/Hotwire First-Load Rendering
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Audit monitor.js initialization (lines 1364-1366) to understand why turbo:load listener
exists but canvas still renders blank on first load. Fix initialization timing so canvas
renders immediately without manual refresh.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/javascript/admin/monitor.js` | Canvas init logic — audit turbo:load vs DOMContentLoaded | not started |
| `app/views/admin/celestial_bodies/planetary.html.erb` | Primary view with blank canvas issue | pending |
| `app/views/admin/celestial_bodies/regional.html.erb` | Secondary view sharing same script | pending |
| `config/importmap.rb` | Verify monitor.js is NOT importmap-pinned | pending |

### Prerequisites Completed
- ✅ Read README.md BUGFIX section
- ✅ Read project guide
- ✅ Read phase structure (UI infrastructure, not phase-gated)
- ✅ Understand architecture gotchas above

### Expected Outcomes
- Canvas renders on first load without manual refresh
- Turbo/Hotwire lifecycle integration working correctly
- Fix verified across all views that include monitor.js

### Critical Gotchas I Will Avoid
- ❌ Starting from scratch — instead ✅ Audit existing turbo:load listener (line 1364)
- ❌ Assuming importmap loading — instead ✅ Work with javascript_include_tag pattern
- ❌ Testing only planetary view — instead ✅ Verify fix across all monitor.js consumers

---

**SYNTHESIS COMPLETE.** Ready to proceed with audit.
```

---

## Problem Statement

### Current State — What Exists
- ✅ `app/javascript/admin/monitor.js` — 1364+ lines, full canvas rendering module
- ✅ `turbo:load` listener exists (line 1364): `document.addEventListener('turbo:load', AdminMonitor.init)`
- ✅ `DOMContentLoaded` fallback exists (line 1366): `document.addEventListener('DOMContentLoaded', AdminMonitor.init)`
- ✅ `planetary.html.erb` includes monitor.js via `javascript_include_tag` with `data-turbo-track: reload`
- ⚠️ **Unknown**: Why does canvas still render blank on first load despite both listeners?

### Bug Scenarios to Investigate

**Scenario 1 — Turbo fires before data is ready**
```ruby
# The #monitor-data JSON element may not be parsed when turbo:load fires
# Check if AdminMonitor.init reads from DOM synchronously or waits for data
```

**Scenario 2 — Double initialization conflict**
```ruby
# Both turbo:load AND DOMContentLoaded fire on first load
# If init() is not idempotent, second call may reset canvas state
```

**Scenario 3 — Script loading order**
```ruby
# javascript_include_tag with data-turbo-track may reload script after turbo:load fires
# Check if init() runs before monitor.js is fully loaded
```

### Expected Behavior After Fix
- ✅ Canvas renders planetary map on first page load (no refresh needed)
- ✅ Works for both traditional navigation AND Turbo frame navigation
- ✅ No JavaScript errors in browser console
- ✅ Loading indicator displays while data loads (if async)
- ✅ Error handling for missing/invalid data

---

## Implementation Steps

### Step 1 — Audit Existing Initialization (Research)
**Goal**: Understand why current turbo:load listener doesn't fix the blank canvas.

```bash
# Check init function signature and idempotency:
grep -n "init\|AdminMonitor.init" galaxy_game/app/javascript/admin/monitor.js | head -20

# Check data injection timing:
grep -n "monitor-data\|JSON.parse" galaxy_game/app/javascript/admin/monitor.js | head -10

# Verify script loading order in view:
grep -n "javascript_include_tag\|script" galaxy_game/app/views/admin/celestial_bodies/planetary.html.erb
```

**Deliverable**: Audit report documenting why current listeners don't prevent blank canvas.

### Step 2 — Fix Initialization Timing (Implementation)
**Goal**: Ensure canvas renders on first load by fixing Turbo lifecycle integration.

**Likely fixes** (choose based on audit findings):
1. Make `AdminMonitor.init()` idempotent (guard against double calls)
2. Add data-readiness check before rendering (wait for #monitor-data to be parsed)
3. Replace dual listeners with single Turbo-aware pattern
4. Add loading indicator while data loads

**Target files**:
- `app/javascript/admin/monitor.js` — primary fix location
- `app/views/admin/celestial_bodies/planetary.html.erb` — may need data-turbo attributes

### Step 3 — Verify Across All Views (Validation)
**Goal**: Confirm fix works for all views that include monitor.js.

```bash
# Test each view:
# 1. admin/celestial_bodies/planetary.html.erb
# 2. admin/celestial_bodies/regional.html.erb  
# 3. admin/celestial_bodies/monitor.html.erb (if active)
```

**Deliverable**: Manual verification log confirming fix across all consumer views.

---

## Acceptance Criteria
- [ ] Canvas renders on initial page load (no manual refresh required)
- [ ] JavaScript initialization fires correctly with Turbo `turbo:load` events
- [ ] Canvas renders on both traditional page loads and Turbo frame navigation
- [ ] Loading indicator displays while data loads (if async)
- [ ] Error handling for missing/invalid data
- [ ] No JavaScript errors in browser console
- [ ] Fix verified across all views that include monitor.js

---

## Stop Conditions — escalate to user immediately if:
- Canvas rendering depends on external API call that's not mocked (blocks local testing)
- Turbo version incompatibility discovered (may require Rails upgrade)
- Any architectural decision is required before fix can proceed

---

## Dependencies

**Blocked by**: None — this task is ready to dispatch immediately
**Blocks**: GGMap visualization work, terraforming views (both need functional canvas first)
**Related tasks**: `current/2026-07-06-HIGH-BUGFIX-MONITOR-CANVAS-FEATURE-SPEC-GAP.md` (missing spec creation)

---

## Completion Report
*Filled in by the implementing agent after completion*
