---
status: draft
resume: false
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

# Player Planetary view — review and define follow-up

**Draft state:** Placeholder only. This file records an existing prototype page; it is not an implementation authorization or a dispatch-ready task.

## Existing page

- **Prototype repository:** `trmccormick/galaxy-game-ui`
- **View:** `galaxy-game-ui/app/views/player/planetary.html.erb`
- **Current purpose (from the existing view):** Player planetary observation workspace with map canvas, layer controls and planetary data panel.

## Objective

Review this page against the current GalaxyGame UI goals, the authoritative GalaxyGame wiki where relevant, and the current prototype implementation. Determine whether it should be retained, revised, simplified, split, or left as a placeholder.

## Scope to define during review

- Confirm the page's intended audience and role in the overall UI.
- Compare the existing page with current design intent and adjacent navigation/workspaces.
- Identify what is already complete and must not be recreated.
- Record specific remaining UI work, if any.
- Identify unresolved design questions rather than inventing answers.
- Determine whether a separate implementation task is warranted.

## Constraints

- This is a Rails 8.1 UI prototype, separate from the Rails 7 main GalaxyGame application.
- Do not add backend, API, model, migration, persistence, or simulation work.
- Do not duplicate existing GalaxyGame data definitions or lookup behavior.
- Reuse existing prototype work; do not rebuild a page simply because this placeholder exists.
- Do not assume current mock values or displayed controls represent approved game behavior.
- No implementation, commit, or dispatch is authorized by this placeholder.

## Review references

- Prototype page: `galaxy-game-ui/app/views/player/planetary.html.erb`
- Project guidance: `projects/galaxy-game-ui/README.md` and `SESSION_GUIDANCE.md`
- Relevant prototype wiki and main GalaxyGame wiki pages: to be identified during review based on this page's purpose.

## Acceptance criteria for this draft review

- [ ] Current page state and existing behavior are accurately summarized.
- [ ] Goals and audience are confirmed against authoritative design intent.
- [ ] Completed work is excluded from any follow-up scope.
- [ ] Remaining work or reason for no further task is recorded.
- [ ] Tracy reviews and directs the next step.

## Dependencies

**Blocked by:** Human review and direction.  
**Blocks:** None established.

## Progress / Resume

- **Done so far:** Placeholder created from the existing prototype view inventory.
- **Next step:** Review this page with Tracy; do not dispatch.
- **Status:** Draft placeholder; not ready for implementation.
