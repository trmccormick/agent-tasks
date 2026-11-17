---
status: completed
resume: false
priority: MEDIUM
type: refactor
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: GalaxyGame UI prototype
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/backlog/2026-11-16-MEDIUM-REFACTOR-PLAYER-PLANETARY-LAYOUT.md

IMPORTANT — Repository structure (read before any commands):
  There is ONE Git repository for the prototype. The Rails application is nested inside it.
  Prototype Git root:    /Users/tam0013/Documents/git/galaxy-game-ui
  Rails app root:        /Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui
  Docker Compose files:  at the prototype Git root (docker-compose.yml, docker-compose.dev.yml)
  The nested galaxy-game-ui/ directory is NOT a separate Git repository.

STEP 0 — Verify agent-tasks repository and move task file:
  cd /Users/tam0013/Documents/git/agent-tasks
  git branch --show-current   (must return main)
  If not on main, STOP and report to Tracy.
  git mv projects/galaxy-game-ui/tasks/backlog/2026-11-16-MEDIUM-REFACTOR-PLAYER-PLANETARY-LAYOUT.md \
         projects/galaxy-game-ui/tasks/active/2026-11-16-MEDIUM-REFACTOR-PLAYER-PLANETARY-LAYOUT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

STEP 1 — Verify exactly one copy exists:
  find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks -name "2026-11-16-MEDIUM-REFACTOR-PLAYER-PLANETARY-LAYOUT.md"
  Only ONE result should exist. Paste this output before proceeding.

STEP 2 — Verify the prototype repository state:
  cd /Users/tam0013/Documents/git/galaxy-game-ui
  git branch --show-current   (must return main)
  git status                  (confirm working tree; do NOT modify any files)
  Do NOT treat the nested galaxy-game-ui/ as a separate Git repository.

STEP 3 — Scope guard:
  Commit 0594add also contains terrain-service, Docker/compose and AI Manager files.
  They are OUT OF SCOPE for this task. Do NOT edit, move, delete, stage or commit any of them.
  The only files you may edit are listed under "Allowed files" in the task.

STEP 4 — Read, in this order:
  1. /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
  2. /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md
  3. /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md
  4. The task file at the path above
  5. galaxyGame/docs/wiki/ui/02_player_view_architecture.md
  6. galaxyGame/docs/wiki/ui/04_current_ui_state_and_known_issues.md

  Base the layout on the main app's source view: galaxyGame/galaxy_game/app/views/admin/celestial_bodies/planetary.html.erb (read-only reference). Keep its arrangement (header line with body facts, toolbar of layer buttons plus zoom/reset above the map, map in the center, data panel on the right) and drop only the Admin controls (Edit Properties, Regenerate Terrain, View Public Page, Celestial Bodies link).

STEP 5 — Save a concise synthesis/implementation plan as MD to:
  /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Include: goal, files referenced, prerequisites, expected outcomes, gotchas to avoid.

STEP 6 — Implement within the approved task scope. When runtime verification is needed,
  start the galaxy_game container (confirm compose config at prototype Git root first).

Closeout: follow SESSION_CLOSEOUT.md only.
```

Dispatch block adapted from the Planetary port task: the task path is updated and STEP 3 is replaced
(the 8 AI Manager files are now committed in 0594add and are out of scope here).

# TASK: Player Planetary — view, routes and CSS organization (narrow)
**Status**: BACKLOG  **Priority**: MEDIUM  **Type**: refactor  **Created**: 2026-11-16

## Purpose (this is all it is)

Get the Player Planetary view into the prototype shell, populated with representative mock data, with correct routes, so Tracy can tune the CSS. It is a layout and organization task.

The prototype is a UI mock-up. It will consume the main app's API later. It does NOT process data.

## Hard exclusions

- No GeoTIFF work of any kind. GeoTIFF is already processed and working in the main game. Do not read, convert, recompile or mount it.
- No Docker, compose, Dockerfile or gem changes.
- No services, models, migrations or endpoints.
- No renderer rewrite. If the map or JS looks wrong, REPORT it; do not fix it.
- No AI Manager files. No Admin files.
- No world-changing controls (generate/regenerate terrain, edit, terraform, time controls). Viewing controls only: layer toggles, zoom, reset.
- No commit.

## Allowed files

- `galaxy-game-ui/app/views/player/planetary.html.erb` (layout and markup)
- `galaxy-game-ui/app/controllers/player_controller.rb`
- `galaxy-game-ui/config/routes.rb` (Player routes only)
- `galaxy-game-ui/app/views/layouts/dashboard.html.erb` (Player nav entry only)
- One page stylesheet under `galaxy-game-ui/app/assets/stylesheets/pages/` for this view, if styles are needed

## Steps

1. `git status` and `git diff --stat`. Report anything unexpected before editing.
2. Routes: keep Player routes grouped under one `player` scope (e.g. `/player/planetary`), with room for `surface`, `settlement` and `space` later. Do not add those pages.
3. Nav: put Planetary under a Player heading, not under "Galaxy". Make the active-link highlight follow the current page.
4. View: remove the page's own sidebar, nested `<main>` and `<footer>`. Use the shared shell's single sidebar. Layout: header line, a toolbar above the map (layer buttons, zoom, reset), the map in the center, the data panel on the right.
5. Mock data stays inline and clearly labeled "MOCK DATA" on the page. Do not edit the mock JSON or fix values.
6. Move inline `style=""` attributes into the page stylesheet where practical, so CSS tuning happens in one place.

## Verify

Run the prototype with its documented container workflow. Open the page from the sidebar and by hard reload. Report console errors and failed requests. Take a screenshot.

## Acceptance criteria

- [ ] Route and nav entry are organized as above; Planetary reachable from the sidebar.
- [ ] One sidebar, no nested `<main>`/`<footer>`.
- [ ] Toolbar above the map; map and data panel populated with labeled mock data.
- [ ] Styles live in one page stylesheet.
- [ ] No files changed outside the allowed list; no commit.

## Stop and report if

- The map does not render, or the page works on reload but not after a sidebar click. Report exact behavior; do not fix.
- Any file outside the allowed list seems to need editing.

## Report (shell redirection, not chat)

Write `~/scratch/planetary-layout-report.md`: changed files, diff stat, checks, console/network results, screenshot path, anything that looked wrong but was left alone.

## Separate follow-ups, not this task

- Remove terrain-service, Docker/compose and AI Manager files from commit 0594add.
- Whether the Player view needs a console strip or missions panel.
- Map draw scale and Turbo init, if the browser check shows they are real problems.
