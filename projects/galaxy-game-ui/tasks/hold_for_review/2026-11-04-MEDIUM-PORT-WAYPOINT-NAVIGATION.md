---
status: drafts
resume: false
priority: MEDIUM
type: feature
system_domain: JAVASCRIPT / WAYFINDING
mvp_alignment: OTHER
local_worker_safe: true
---

# Reuse Existing Waypoint Navigation in the Galaxy Game UI Prototype

## Goal

Integrate the smallest reusable waypoint-navigation interaction from the EVE Dashboard/first-attempt research into the current Galaxy Game UI prototype, **only if the preceding recovery audit identifies a suitable existing implementation**.

This is a prototype interaction task. It does not implement game navigation, route simulation, travel-time calculations, or EVE gameplay.

## Mandatory prerequisite

Do not start this task until Tracy has reviewed the report from:

`2026-11-04-HIGH-AUDIT-WAYPOINT-JS-RECOVERY.md`

The audit must identify the exact source file, commit or local backup path, dependencies, expected data, and recommended reusable unit. If no usable implementation is found, this task must be revised and re-approved before work begins.

## Scope

After the audit is approved:

- Reuse the identified waypoint interaction code where practical.
- Adapt only the necessary JavaScript and view integration for the current Rails 8 prototype.
- Use Galaxy Game system/link/waypoint data already available to the prototype; do not invent route data or game rules.
- Keep the behavior demonstrative and clearly prototype-level.
- Keep styling consistent with the current Galaxy Game tokens and shell.

The implementation agent must name the exact files to add or change in its pre-work synthesis. If the audit suggests changes outside a small JavaScript/view integration, stop and request a revised task.

## Acceptance criteria

- [ ] The implementation source is the one approved from the recovery audit; its origin is recorded.
- [ ] The feature is integrated into the current branch only; first-attempt sources remain untouched.
- [ ] Waypoint selection and display work with the prototype's existing data shape.
- [ ] Missing or unknown route/link data is shown as unknown rather than fabricated.
- [ ] No EVE-specific game logic, API, account data, or gameplay rules are imported.
- [ ] The interaction is verified in the running prototype at desktop and narrow viewport sizes.
- [ ] Dependencies and any adaptation from the original source are documented.
- [ ] Exact files changed and visual/behavioral evidence are reported.

## Out of scope

Autopilot execution, route optimization, travel-time calculations, live EVE API integration, backend game simulation, new data models, broad dashboard redesign, or rewriting the waypoint feature from scratch without Tracy's approval.

## Dependencies

Blocked by the read-only waypoint recovery audit and Tracy's approval of its recommendation. Do not dispatch before then.
