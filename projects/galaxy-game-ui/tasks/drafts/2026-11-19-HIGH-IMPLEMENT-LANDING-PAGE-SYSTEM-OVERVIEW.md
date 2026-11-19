---
status: draft
resume: false
priority: HIGH
type: feature
system_domain: LANDING PAGE / SYSTEM DATA
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-19
---

# Implement the landing-page system overview using copied JSON

## Objective

Complete the existing public landing page and its small Rails-side JSON reader as one coordinated slice. Render the five intended system entries from the prototype's existing copied JSON data.

## Read first

- Prototype wiki: [Public landing page and system overview](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/interface-and-workflows.md#7a-public-landing-page-and-system-overview)
- Prototype wiki: [Sol fixtures and celestial-object data](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/data-integration-and-migration.md#sol-system-fixtures-and-celestial-object-data)
- Prototype wiki: [Data integration and backend direction](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/data-integration-and-migration.md)
- Project workflow and guardrails: `projects/galaxy-game-ui/README.md` and `SESSION_GUIDANCE.md`

## Scope

- Inspect the current landing page, routes/controller, and the existing `json-data/star_systems/` and `json-data/generated_star_systems/` files.
- Reuse the existing page structure and styling; make only the changes needed to match the landing-page wiki.
- Add the smallest Rails-side reader/controller integration needed to supply the five system entries from JSON. Keep file access and source-record resolution in Rails; the browser must not read source JSON files directly.
- Use the existing Sol fixtures as documented in the wiki. Do not copy, merge, regenerate, or rewrite the JSON data.
- Keep unrelated landing-page metrics or activity illustrative and clearly non-live.
- Verify the page and data behavior with focused tests/checks.

## Exclusions

No general lookup/provider framework, production API, broad lookup-service port, database/model/migration work, terrain, map generation, StarSim execution, survey/calibration, simulation behavior, or unrelated view redesign. Do not invent unconfirmed wormhole connections or network topology.

## Acceptance criteria

- [ ] The existing public landing page renders Sol, Alpha Centauri, FR-488530, DJEW-716790 and Eden from the copied JSON records.
- [ ] Sol can use the smaller `sol.json` fixture for the normal landing-page dataset; `sol-complete.json` remains available as the fuller-data test fixture.
- [ ] Displayed system identity and any celestial-object counts are derived from the source records.
- [ ] Rails supplies the data; browser code does not fetch or parse local source JSON.
- [ ] The page follows the landing-page layout and visual guidance in the prototype wiki.
- [ ] Any connection shown is supported by confirmed data; unresolved topology is not drawn as fact.
- [ ] Non-source metrics remain clearly illustrative/mock.
- [ ] Focused verification covers successful rendering and missing/malformed source data without breaking the public landing route.

## Dependencies

None. The copied JSON data is already present in the prototype repository.

## Dispatch

Draft for review only. Do not move to active or begin implementation without Tracy's explicit approval.
