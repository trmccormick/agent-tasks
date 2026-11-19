---
status: draft
resume: false
priority: MEDIUM
type: feature
system_domain: PLAYER / PLANETARY VIEW
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-19
---

# Player Planetary view — consume loaded body data

## Objective

After the Sol JSON lookup task is complete, update the existing Player Planetary view to render Earth from the controller-provided `@planetary_data` payload instead of hardcoded mock planetary values.

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Existing prototype controller, view, and renderer:
  - `galaxy-game-ui/app/controllers/player_controller.rb`
  - `galaxy-game-ui/app/views/player/planetary.html.erb`
  - `galaxy-game-ui/app/javascript/player/planetary.js`

## Scope

- Replace hardcoded/mock Earth definition values in the controller, ERB, and JavaScript with values supplied through `@planetary_data`.
- Pass the controller payload safely to the existing JavaScript renderer and have the UI render the selected body's available data.
- Preserve the reviewed Planetary workspace and existing renderer/layer behavior except where data binding requires a focused change.
- Show unavailable fields as unavailable; do not substitute invented values.

## Boundaries

- **Blocked until** `2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` is completed and its controller payload is available.
- The frontend must not read local JSON, know the provider implementation, or call a provider-specific path. It consumes the controller's stable payload only.
- `sol.json` supplies system/body definition data, not terrain maps or live simulation state. Keep terrain sourcing separate and do not fabricate terrain or dynamic environmental values from static body definitions.
- No production API implementation, new general backend, models, migrations, persistence, or simulation logic.
- Do not expand this task into a broad visual redesign or unrelated Planetary renderer repairs.

## Acceptance criteria

- [ ] The Planetary page renders Earth using the actual body data supplied by the Rails controller.
- [ ] Hardcoded/mock Earth definition values are removed from the controller, view, and JavaScript data path.
- [ ] JavaScript receives data from the view/controller payload and has no knowledge of JSON file locations or the local provider.
- [ ] Missing source fields are not fabricated or presented as authoritative values.
- [ ] Terrain remains sourced independently of `sol.json`; no terrain or simulation-state behavior is falsely attributed to the system JSON.
- [ ] Focused tests and relevant Rails checks pass; the rendered page is reviewed against the wiki.

## Dependencies

**Blocked by:** `2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` (must be completed first)  
**Blocks:** further Planetary View visual/interaction review, if any remains after this data integration.

## Dispatch note

This task is second in the agreed order. Do not start it in parallel with the lookup/provider task.
