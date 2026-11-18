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

- Replace the hard-coded `active` class on the Overview link with a condition based on the actual Overview route/controller/action.
- Follow the existing active-state conventions already used by the Player Planetary and Admin links; verify the correct controller/action rather than guessing.
- Optional: add a small visible `MOCK DATA` badge only if an appropriate existing badge style/component is already available in the shared layout or loaded styles. Do not create a new styling system or expand this into a visual redesign. If no suitable existing style exists, skip the badge and report that choice.
- Leave all other navigation links and their behavior unchanged.

## Explicit exclusions

- Do not fix the separate `#galaxy` fragment/Turbo Frame navigation issue. It remains its own task.
- Do not change the Planetary workspace, data panel, map, or any page-specific behavior.
- Do not add JavaScript or change routes/controllers for a cosmetic active-state correction.

## Verification and review gate

1. Confirm the two preceding Player Planetary tasks have been reviewed and accepted.
2. Inspect the working tree and relevant diff; preserve unrelated local changes.
3. Verify the Overview link is active on the Overview page and not active on Player Planetary.
4. Verify the existing Player/Admin active states still work and no other navigation behavior changed.
5. Capture browser evidence and report the exact file/diff and verification results.

Stop for Tracy's review. Do not commit or push without Tracy's explicit approval (GUARDRAILS Rule 26).

## Acceptance criteria

- [ ] Overview is highlighted only on its own page.
- [ ] Existing Player and Admin active-state behavior remains intact.
- [ ] Optional MOCK DATA badge is included only if an existing suitable style is available; otherwise it is explicitly skipped.
- [ ] The `#galaxy` navigation issue remains untouched.
- [ ] Only the allowed shared-layout file is changed; browser verification is reported.
