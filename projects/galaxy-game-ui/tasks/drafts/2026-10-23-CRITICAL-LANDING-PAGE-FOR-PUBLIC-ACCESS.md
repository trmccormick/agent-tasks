---
status: drafts
resume: false
priority: CRITICAL  
type: feature
system_domain: VIEWS (content population — scaffolding done)
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded (only landing page + about page, no dashboard)
- [ ] Dependencies listed and verified
- [ ] No contradictions in specification
- [ ] Review agent disposition: GO

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/drafts/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/drafts/[FILENAME].md \
         projects/galaxy-game-ui/tasks/active/[FILENAME].md
  Then open the moved file and change: status: drafts → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: drafts → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

## 🟡 SCAFFOLDING STATUS — ALREADY DONE (post-Haiku session 2026-10-23)

| Item | Path | Status |
|------|------|--------|
| LandingController with index action | `app/controllers/landing_controller.rb` | ✅ Created (empty scaffold) |
| `views/landing/index.html.erb` view file | `app/views/landing/index.html.erb` | ✅ Created (blank Rails scaffold — only `<h1>Landing#index</h1>`) |
| Root route (`root "landing#index"`) | `config/routes.rb` | ✅ Already set up |
| Dashboard route (`/dashboard`) | `config/routes.rb` | ✅ Already set up |

**Resume**: Start at "populate landing content" — do NOT generate or recreate the controller.

---

# TASK: Populate Landing Page Content for Galaxy Game UI
**Status**: DRAFTS  
**Priority**: CRITICAL  
**Type**: feature  
**Created**: 2026-10-23  
**Last Updated**: 2026-10-23  
**Resume From**: Content population (controller/views/routes already scaffolded)  

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md`
3. **Context**: See Status & Context below

---

## Context

After Phase 4 (seeds cleanup), the app has default admin/player accounts. The root URL (`/`) is already mapped to `landing#index` but the view file contains only a blank Rails scaffold placeholder (`<h1>Landing#index</h1>`). This needs content populated for a public-facing game site entry point.

The galaxyGame prototype at `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/landing/` has two views:
- `index.html.erb` — Hero section with logo, tagline, Sign In/Create Account/Learn More buttons, plus a 4-panel game status grid
- `about.html.erb` — Project vision, how it works, development workflow

**The fresh app's scaffolded landing view needs content populated.** The controller and routes already exist (created in Haiku session 2026-10-23).

**IMPORTANT SCOPE NOTE**: This task focuses on **content population only**. Two CSS options — pick ONE before starting:
- **Option A (minimal)**: Use existing classes only. No new stylesheets, no EVE CSS, no Tailwind class changes. Just populate HTML with placeholder content.
- **Option B (depends on T3a)**: If EVE CSS foundation (T3a) is already approved and applied, use EVE CSS variables/classes for styling.

> **Recommendation**: Go with Option A (minimal) until T3a is reviewed. Apply EVE CSS later as a separate pass.

---

### Routes (already correct)
```ruby
root "landing#index"
get "dashboard", to: "dashboard#index", as: :dashboard
```

### LandingController (already scaffolded — Haiku 2026-10-23)
```ruby
class LandingController < ApplicationController
  def index
  end
end
```

### views/landing/index.html.erb (blank scaffold — needs content)
```html
<div>
  <h1 class="font-bold text-4xl">Landing#index</h1>
  <p>Find me in app/views/landing/index.html.erb</p>
</div>
```

### What Needs to Be Populated (Implementation Steps below)
1. **Hero section** — Galaxy Game branding (text or logo)
2. **Auth buttons** — Sign In (→ `/session/new`) + optional Create Account link
3. **Optional About link** — → `/about` (if `landing#about` action is added)
4. **4 placeholder stat cards** — AI Manager / Star Systems / Economy / Expansion
5. **Post-login redirect** — After successful session creation, redirect to `dashboard_path`. Add to sessions controller: `redirect_to dashboard_path` in create action.

### Implementation Steps

### Step 1: Populate views/landing/index.html.erb with hero, auth buttons, placeholder stats
Create Galaxy Game landing content. Based on galaxyGame prototype structure but minimal HTML only (Option A). Example:

```erb
<h1 class="font-bold text-4xl mb-4">Galaxy Game</h1>
<p class="mb-6">Explore, settle, and build your galactic empire.</p>

<div class="flex gap-4 mb-8">
  <%= link_to "Sign In", session_new_path, class: "btn primary" %>
  <!-- Option B (T3a applied): use .btn.primary for styled button -->
  <!-- Option A (minimal): use existing Rails button helper -->
</div>

<div class="grid grid-cols-4 gap-4 mb-8">
  <div class="panel">AI Manager — Decisions Today: <b>0</b></div>
  <div class="panel">Star Systems — Discovered: <b>0</b></div>
  <div class="panel">Economy — Trade Volume: <b>0 GCC</b></div>
  <div class="panel">Expansion — Growth Rate: <b>0%</b></div>
</div>

<%= link_to "Learn More →", about_path %>
```

All stats are placeholders. No data wiring needed.

### Step 2 (optional): Add landing#about view and /about route
Create `views/landing/about.html.erb` with static content. Add `get "about" => "landing#about"` to routes if not already present.

### Step 3: Wire post-login redirect to dashboard_path
In `app/controllers/sessions_controller.rb`, after successful session create action, add:
```ruby
redirect_to dashboard_path
```
This ensures new users land on the dashboard, not the landing page.

### Step 4 (if using Option B CSS): Apply EVE Dashboard variables
After T3a is approved and deployed, replace any inline styles with EVE CSS classes (`@panel`, `.btn.primary`, etc.).

---

## Gotchas

1. **Controller + routes already scaffolded** — Do NOT create LandingController or modify routes.rb for basic landing/dashboard routing. Only populate view content.
2. **CSS scope choice**: Pick Option A (minimal) or Option B (depends on T3a). Do NOT apply both in the same task.
3. **Post-login redirect** — Must be wired so new users land on `/dashboard`. This is a one-line change in sessions controller create action.
4. **All stats are placeholders** — No data wiring needed for this task.

---

## Acceptance Criteria

- [ ] `LandingController#index` renders populated content (not blank scaffold)
- [ ] Hero section shows "Galaxy Game" branding (text or logo)
- [ ] Sign In link goes to `/session/new`
- [ ] Post-login redirect goes to `dashboard_path`
- [ ] 4 placeholder stat cards render on landing page
- [ ] CSS uses either existing classes only (Option A) OR EVE CSS if T3a deployed (Option B) — NOT both
- [ ] Login/session routes still intact and functional after change
- [ ] Production database untouched
- [ ] **Out of scope**: About view (optional), CSS animations, responsive layout

---

## Dependencies

**Depends on**: Task `2026-10-23-CRITICAL-SEEDS-CLEANUP-DEFAULT-USERS` (default accounts must exist so Sign In works).  
**Depends on**: T3a (EVE CSS Foundation) — only if Option B chosen. Otherwise minimal dependencies.  
**Blocks**: Dashboard content population (T3b) — users need public entry point first.

```markdown
# Task Synthesis Report — Landing Page Implementation

## Completed
| Item | Status | Notes |
|------|--------|-------|
| LandingController created | ✅ / ❌ | |
| views/landing/index.html.erb | ✅ / ❌ | |
| views/landing/about.html.erb | ✅ / ❌ | |
| Root route changed to landing#index | ✅ / ❌ | |
| About route added at /about | ✅ / ❌ | |
| EVE CSS variables applied | ✅ / ❌ | |

## Verification Results
| Check | Result |
|-------|--------|
| GET / renders without errors | ✅ / ❌ |
| GET /about renders without errors | ✅ / ❌ |
| Sign In → /session/new | ✅ / ❌ |
| Login routes intact | ✅ / ❌ |

## Issues
- [If any issues, describe here]

## Commit Hash:
```
