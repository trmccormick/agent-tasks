---
status: drafts
resume: false
priority: MEDIUM
type: refactor
system_domain: VIEWS / SESSIONS
mvp_alignment: OTHER
local_worker_safe: true
---

# Finalize Galaxy Game Branding on the Sign-in Screen

## Goal

Finish the visual branding of the existing sign-in page so it belongs to the Galaxy Game prototype and is consistent with the current landing page and application shell.

This is a focused sign-in-screen task, not an authentication redesign.

## Before editing

1. Read the current UI project startup guidance, `NEEDS_REVIEW.md`, `SESSION_GUIDANCE.md`, and the latest handoff.
2. Inspect the current sign-in view, layout, built CSS, and the latest state on `review/qwen-ui-prototype`.
3. Inspect the pushed sign-in work (including commit `2e10b1d` and related CSS commit `209d37a`) before changing anything. Confirm the current branch history rather than assuming these commits are missing.
4. Identify the currently approved Galaxy Game logo asset and the existing token names. Do not invent a logo filename, select a new variant, or introduce a second color system.

## Scope

Only the sign-in screen's Galaxy Game presentation and its directly associated visual states:

- Remove any remaining visible WVU Libraries / donor-check-in branding from the sign-in screen.
- Use the existing Galaxy Game brand treatment and current design tokens.
- Ensure the logo/brand placement is consistent with the current application shell.
- Verify the email/password fields, submit button, and Forgot Password link are legible and styled consistently.
- Verify the failed-sign-in/error state is readable and visually integrated, without changing how authentication errors are generated.

## Preserve

- Existing Rails session/authentication implementation.
- Form action, field names, Rails helpers, validation behavior, and redirect behavior.
- Password reset behavior and routes.
- Existing working application layout and Tailwind v4 setup.

## Acceptance criteria

- [ ] Sign-in screen shows Galaxy Game branding only; no visible WVU donor-check-in branding remains on this screen.
- [ ] The approved existing logo asset is used if the current design calls for it; otherwise retain the current text treatment and report the missing decision.
- [ ] Email and password fields, labels, submit button, and Forgot Password link are readable and consistent with current tokens.
- [ ] Failed-sign-in feedback is readable and does not break the layout.
- [ ] Authentication form semantics and behavior are unchanged.
- [ ] Tailwind/CSS is rebuilt using the project's current workflow and the page is visually checked in the running prototype.
- [ ] Report screenshots or equivalent visual evidence and list the exact files changed.

## Out of scope

Landing page changes, shared layout redesign, password-reset page redesign, authentication/controller changes, new logo creation, and changes to the main GalaxyGame app.

## Dependencies

Independent of the waypoint audit. Must be performed against the current working prototype branch. Human approval is required before implementation dispatch and before any commit.
