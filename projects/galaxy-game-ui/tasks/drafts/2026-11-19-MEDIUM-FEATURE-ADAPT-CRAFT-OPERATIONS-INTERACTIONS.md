---
status: draft
resume: false
priority: MEDIUM
type: feature
system_domain: PLAYER / ADMIN OPERATIONS
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-19
---

# Adapt EVE craft-management interactions for GalaxyGame

## Objective

Reuse selected interaction patterns from the supplied EVE Dashboard JavaScript to demonstrate craft-centered asset selection and optional operational grouping in the GalaxyGame Player and Admin interfaces.

## Read and reference

- [JavaScript Reuse, Porting, and Module Plan](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/javascript-reuse-and-porting-plan.md)
- [Interface Pattern References](https://github.com/trmccormick/galaxy-game-ui/blob/main/docs/wiki/interface-pattern-references.md)
- [Player View Architecture](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md)
- User-supplied EVE Dashboard references: fleet_builder.js, fleet.js, and fleet_map.js; recover/verify the exact source and dependencies before implementation.
- Current Player and Admin prototype views, navigation, and JavaScript.

## Scope

- Inspect the current prototype and the supplied fleet_builder.js before deciding what interaction behavior can be reused cleanly.
- Adapt only useful presentation/interaction mechanics such as craft selection, filtering, ordering, favourites, contextual status, and optional grouping.
- Use GalaxyGame ship/craft names and actual GalaxyGame craft identity. Do not model EVE characters or pilots.
- Keep a craft's current location, operational state, assigned objective, and any communication/freshness information distinct; show only fields supported by the selected data source or clearly labeled mock data.
- Support craft operating independently as well as being grouped for a specific task/operation. Do not assume a standing fleet is mandatory.
- Preserve the distinction between Player asset management and Admin/AI Manager operational oversight. Share generic interaction code only where it genuinely fits both workflows.
- Record which interactions were reused, adapted, omitted, or blocked by missing GalaxyGame data.
- Obtain craft records and operational state from Rails controller/service payloads or Rails endpoints. Rails owns craft lookup, source JSON access, permissions, and authoritative state; JavaScript consumes the prepared data and handles presentation/transient interactions only.

## Boundaries

- GalaxyGame craft are autonomous operational assets. Players and authorized Admin/AI Manager workflows assign objectives remotely; craft execute through onboard automation and the AI Manager according to their capabilities and available communications.
- Do not imply direct piloting, a player-character/pilot identity, or a permanently connected operator.
- Do not import EVE fleet rules, character/team schemas, browser localStorage preferences, EVE URLs/endpoints, or EVE backend behavior.
- Do not fetch or parse GalaxyGame craft/system JSON files in browser JavaScript or implement client-side source lookup. The EVE universe.min.json is never a runtime data source.
- A requested objective is not a confirmed execution result. Do not present local UI state as an authoritative operation.
- No production API, backend operations, simulation logic, persistence, models, migrations, or changes to the main GalaxyGame/EVE source repositories.
- Do not broaden this into a general fleet-management system or a full port of fleet_builder.js.

## Acceptance criteria

- [ ] The selected Player or Admin workflow is named and its audience/purpose is clear before implementation.
- [ ] Craft are represented by GalaxyGame ship/craft names and identities, never by pilot or character records.
- [ ] The UI can select and inspect individual craft using data supplied by Rails (or clearly labeled mock data supplied through the same Rails boundary).
- [ ] Browser JavaScript does not load source JSON files or perform authoritative craft lookup.
- [ ] Optional task/operational grouping does not prevent independent craft operation.
- [ ] Current location, operational status, assigned objective, and data freshness are not conflated.
- [ ] Player asset management and Admin/AI Manager oversight are not collapsed into one indistinguishable workflow.
- [ ] Reused JavaScript behavior is documented with source provenance and EVE-specific dependencies removed.
- [ ] The running prototype is checked with focused interaction tests and visual evidence.

## Dependencies

**Blocked by:** Tracy's selection of the first Player or Admin workflow and review of the available craft data/source modules.  
**Related:** Player Space workspace first slice (2026-11-13-MEDIUM-FEATURE-PORT-PLAYER-SPACE-WORKSPACE.md); this is a separate, later interaction task and is not required for the initial Sol map.

## Dispatch note

Draft only. This task is not part of the current JSON lookup → landing page → Player Planetary sequence and is not approved for dispatch. Review it after the initial data/landing/Planetary work and decide which concrete Player or Admin craft workflow should be the first implementation slice.
