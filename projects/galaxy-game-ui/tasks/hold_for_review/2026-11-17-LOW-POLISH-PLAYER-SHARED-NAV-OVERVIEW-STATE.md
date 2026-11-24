---
status: draft
priority: LOW
type: polish
system_domain: UI
---

# Player prototype — correct Overview navigation state

## Objective

Make the shared dashboard shell's Overview link active only when the Overview page is the current page. The link is currently hard-coded as active, so it remains highlighted while navigating to Player Planetary.

## Allowed files

- `galaxy-game-ui/app/views/layouts/dashboard.html.erb`

Keep this a small shared-layout change. Do not edit page views, stylesheets, JavaScript, routes, or controllers unless the existing implementation proves the one-file change is impossible; if so, stop and report before expanding scope.

## Changes

- Replace the hard-coded `active` class on the Overview link with a condition for the actual Overview route: `DashboardController#index`.
- Use the explicit controller/action check (for example, `controller.is_a?(DashboardController) && action_name == "index"`). `DashboardController` does not share a parent with `Admin::DashboardController`, so this check does not accidentally activate Overview in Admin.
- Do not copy the AI Manager links' action-name-only pattern; that is insufficient to distinguish the Overview page from other controllers' `index` actions.
- Do not add a MOCK DATA badge. The shared layout is used by Admin as well as Player, and the existing footer already identifies the prototype as mock data.
- Leave all other navigation links and their behavior unchanged.

## Explicit exclusions

- Do not fix the separate `#galaxy` fragment/Turbo Frame navigation issue. It remains its own task.
- Do not change the Planetary workspace, data panel, map, or any page-specific behavior.
- Do not add JavaScript or change routes/controllers for a cosmetic active-state correction.
- Do not add badges or other shared-layout visual changes.

## Verification and review gate

1. This task is independent of both Player Planetary tasks and may be dispatched first as a small warm-up.
2. Inspect the working tree and relevant diff; stop if unrelated or uncommitted changes make the one-file edit unsafe.
3. Make only the allowed shared-layout file change.
4. Run static checks available in the repository and inspect the rendered ERB condition for correctness. Do not claim browser verification.
5. Write the task report to `~/scratch/player-shared-nav-overview-state-report.md` using shell redirection. Include the exact diff summary, checks run/results, and anything not verified. Keep this report for Tracy's review; remove only temporary files created during the task.
6. Stop for Tracy to inspect Overview, Player Planetary, and Admin navigation states in a browser. Do not commit or push without Tracy's explicit approval (GUARDRAILS Rule 26).

## Acceptance criteria

- [ ] Overview is highlighted only on `DashboardController#index`.
- [ ] Existing Player and Admin active-state behavior remains intact.
- [ ] No MOCK DATA badge or unrelated visual change is introduced.
- [ ] The `#galaxy` navigation issue remains untouched.
- [ ] Only the allowed shared-layout file is changed; static verification and report file are provided.
- [ ] Browser review is left to Tracy; no browser verification is claimed by Qwen.
