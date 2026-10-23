---
status: drafts
resume: false
priority: CRITICAL  
type: feature
system_domain: CONTROLLERS
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

# TASK: Create Landing Page and About Page for Galaxy Game UI
**Status**: DRAFTS  
**Priority**: CRITICAL  
**Type**: feature  
**Created**: 2026-10-23  
**Last Updated**: 2026-10-23  

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md`
3. **Context**: See Status & Context below

---

## Context

After Phase 4 (seeds cleanup), the app has default admin/player accounts but the root URL (`/`) still points to `sessions#new` — a direct login form with no Galaxy Game branding. This is not the entry point we want for a public-facing game site.

The galaxyGame prototype at `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/landing/` has two views:
- `index.html.erb` — Hero section with logo, tagline, Sign In/Create Account/Learn More buttons, plus a 4-panel game status grid
- `about.html.erb` — Project vision, how it works, development workflow

**The fresh app needs these pages to serve as the public entry point.** The root route must change from `sessions#new` → `landing#index`.

**IMPORTANT SCOPE NOTE**: This task is **structural only**. We create HTML templates, routes, and controllers. We do NOT:

- Remove any existing CSS (Tailwind or WVU Moonshot classes remain untouched)
- Apply EVE Dashboard styling yet (that requires a separate structural analysis first)
- Build out the dashboard view (coming in a later structural task)
- Wire any backend data (placeholder static HTML only)

We borrow **HTML structure** from the galaxyGame prototype's landing page (hero → auth buttons → status grid layout pattern) but implement it fresh. No styling decisions are made here — those require careful review of EVE Dashboard's overlapping elements first.

---

## Current State

### Root Route (BROKEN FOR PUBLIC ACCESS)
```ruby
root "sessions#new"  # Goes directly to login — not what we want
```

### Routes File (config/routes.rb)
```ruby
Rails.application.routes.draw do
  resource :session
  resources :passwords, param: :token
  get "up" => "rails/health#show", as: :rails_health_check
  root "sessions#new"
end
```

### What's Missing
- `app/controllers/landing_controller.rb` — does not exist
- `app/views/landing/` folder — does not exist
- `/about` route — does not exist
- Galaxy Game logo (`GalaxyGame.png`) asset — needs to be added to public/assets or app/assets

---

## Implementation Steps

### Step 1: Add Galaxy Game logo asset
Copy or symlink the logo from galaxyGame:
```bash
# Check if it exists in galaxyGame
ls /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/images/GalaxyGame.png

# If yes, copy to fresh app (use relative path in case of bind mount)
cp /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/assets/images/GalaxyGame.png \
   /Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui/app/assets/images/
```

> If the logo is not needed for this phase, replace the `<img>` tag with a text-based hero heading "Galaxy Game" instead. Check with human before copying large assets.

### Step 2: Create LandingController
Create `app/controllers/landing_controller.rb`:
```ruby
class LandingController < ApplicationController
  def index; end
  
  def about; end
end
```

No instance variables needed for the landing page — it's static HTML + Tailwind styling (or placeholder stats that render as "0" or "—").

### Step 3: Create views/landing/index.html.erb
Create `app/views/landing/index.html.erb` based on galaxyGame prototype. Key sections:

1. **Hero section** — Galaxy Game logo, title "Galaxy Game", tagline
2. **Auth buttons** — Sign In (→ `/session/new`), Create Account (→ `/session/new` with signup mode)
3. **Learn More link** — → `/about`
4. **Game status grid** — 4 placeholder stat cards:
   - AI Manager — "Decisions Today" — `0` or `—`
   - Star Systems — "Systems Discovered" — `0` or `—`
   - Economy — "Daily Trade Volume" — `0 GCC`
   - Expansion — "Growth Rate" — `0%`

> All stats are placeholders. No MockDataHelper needed yet. Just static HTML with placeholder values.

### Step 4: Create views/landing/about.html.erb
Create `app/views/landing/about.html.erb` based on galaxyGame prototype. Sections:
1. Project vision
2. How it works (bullet list)
3. Development workflow notes
4. External links to agent docs

> This is also static content. No controller logic needed beyond rendering.

### Step 5: Update routes.rb
Change the root route and add about:
```ruby
Rails.application.routes.draw do
  resource :session
  resources :passwords, param: :token
  
  get "about", to: "landing#about"
  root "landing#index"
  
  get "up" => "rails/health#show", as: :rails_health_check
end
```

> Do NOT change dashboard, wormholes, or admin routes — those come later.

### Step 5: Add a placeholder logo path
If the Galaxy Game PNG exists, add it to `app/assets/images/`. If not (yet), use an `<h1>` heading for "Galaxy Game" as the text-based fallback.

### Step 7: Verify
```bash
# Check landing page loads without errors
curl -s http://localhost:3000/ | grep -c "Galaxy Game"

# Check about page loads
curl -s http://localhost:3000/about | grep -c "About"

# Check sign-in link goes to /session/new
curl -s http://localhost:3000/ | grep "session/new"
```

---

## Gotchas

1. **Do NOT remove any existing CSS yet**: The fresh app's Tailwind and WVU Moonshot classes are still in place. This task creates structural HTML only — no CSS removal, no style overrides, no new stylesheets. Styling is deferred to a separate analysis task after we've mapped EVE Dashboard elements against our needs.

2. **Placeholder stats only**: All 4 stat cards show static HTML text (`0` or `—`). No data wiring — that's a separate structural task.

3. **Logo path**: If using `image_tag 'GalaxyGame.png'`, ensure the asset pipeline can find it (`app/assets/images/`). If not available, use an `<h1>` heading as fallback.

---

## Acceptance Criteria

- [ ] `LandingController` exists with `index` and `about` actions
- [ ] `app/views/landing/index.html.erb` renders without errors at `/`
- [ ] `app/views/landing/about.html.erb` renders without errors at `/about`
- [ ] Hero section shows "Galaxy Game" branding (logo or text)
- [ ] Sign In button links to `/session/new`
- [ ] About link links to `/about`
- [ ] 4 placeholder stat cards render on landing page
- [ ] Root route is `landing#index`, NOT `sessions#new`
- [ ] No WVU branding or classes remain in landing/about views
- [ ] CSS uses EVE Dashboard variable system (no hardcoded colors)
- [ ] Login/session routes still intact and functional after change
- [ ] Production database untouched

---

## Dependencies

**Depends on**: Task `2026-10-23-CRITICAL-SEEDS-CLEANUP-DEFAULT-USERS` (default accounts must exist first, even though landing page is public-facing, users need to be able to authenticate after clicking Sign In).  
**Blocks**: None directly, but this task establishes the entry point for ALL player-facing navigation. Dashboard, wormholes, and admin tasks depend on the user being able to reach them via proper navigation flow.

---

## Synthesis Report Template

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
