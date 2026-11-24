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

# Player Planetary view — consume controller display data

## Objective

After the Sol/Earth lookup is complete, render the controller-provided Earth display payload in the existing Player Planetary view instead of hardcoded mock planetary values.

## Read and reference

- [Player View Architecture — Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- Existing prototype controller, view, and renderer:
  - `app/controllers/player_controller.rb`
  - `app/views/player/planetary.html.erb`
  - `app/javascript/player/planetary.js`

## Scope

- Use the focused Earth display payload supplied by the Rails controller.
- Remove hardcoded/mock Earth definition values from the existing controller/view/JavaScript data path where they duplicate source data.
- Pass the payload safely to the existing JavaScript renderer and render the available values.
- Preserve the reviewed Planetary workspace and existing renderer/layer behavior; make only data-integration changes needed for the payload.
- Represent unavailable fields as unavailable; do not invent values.

## Boundaries

- **Blocked until** `2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` is completed and the controller display payload is available.
- The frontend consumes the controller payload only. It must not read local JSON, locate Earth in Sol, know the data source, or call source-specific paths.
- The same controller-to-frontend payload must remain usable when the controller's local JSON lookup is later replaced by an API request.
- The Sol sample supplies static system/body definition values only. Terrain and live simulation state remain separate sources; do not fabricate them from the sample.
- No production API implementation, broad backend expansion, models, migrations, persistence, simulation logic, or broad visual redesign.

## Acceptance criteria

- [ ] The Planetary page renders Earth using the focused body display payload supplied by the Rails controller.
- [ ] Hardcoded/mock Earth definition values are removed from the data path where replaced by actual source values.
- [ ] JavaScript receives the controller payload and has no knowledge of JSON file locations, Sol's nested structure, or the local lookup.
- [ ] Missing source fields are not fabricated or presented as authoritative values.
- [ ] The UI contract remains independent of the source so a future API-backed controller can supply the same payload.
- [ ] Terrain remains sourced independently; no terrain or simulation-state behavior is falsely attributed to the Sol JSON sample.
- [ ] Focused tests and relevant Rails checks pass; the rendered page is reviewed against the wiki.

## Dependencies

**Blocked by:** `2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` (must be completed first) and the landing-page task/reconciliation (`tasks/hold_for_review/2026-10-23-CRITICAL-LANDING-PAGE-FOR-PUBLIC-ACCESS.md`) in the agreed sequence.  
**Blocks:** further Planetary View visual/interaction review, if any remains after this data integration.

## Dispatch note

This task is third in the agreed order: after the Sol/Earth JSON lookup/provider task and the landing-page task/reconciliation. Do not start it in parallel with either preceding item.