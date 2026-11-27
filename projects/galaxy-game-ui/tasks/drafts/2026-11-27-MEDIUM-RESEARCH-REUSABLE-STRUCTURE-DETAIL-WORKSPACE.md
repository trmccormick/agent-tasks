---
status: draft
resume: false
priority: MEDIUM
type: architecture
system_domain: PLAYER / STRUCTURE DETAIL
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-27
---

# Prototype — reusable structure detail and equipment drill-down workspace research

## Objective

Research how the separate Rails 8.1 GalaxyGame UI prototype can present a reusable Structure Detail experience that works for both orbital and surface installations.

A structure is a container for installed equipment. A space station, surface factory, agricultural dome, habitat, or structure within a lava-tube installation should use the same basic detail and equipment drill-down interaction. Their properties and environmental requirements differ; the interface hierarchy should not be reinvented for each location.

## Read and reference

- agent-tasks/projects/galaxy-game-ui/README.md and SESSION_GUIDANCE.md.
- galaxyGame/docs/wiki/ui/02_player_view_architecture.md — canonical Player view and Space context boundaries.
- galaxyGame/docs/wiki/ui/04_current_ui_state_and_known_issues.md — dated current-state observations; verify before relying on them.
- galaxy-game-ui/docs/wiki/README.md.
- galaxy-game-ui/docs/wiki/interface-and-workflows.md.
- galaxy-game-ui/docs/wiki/screen-concepts-and-wireframes.md.
- galaxy-game-ui/docs/wiki/prototype-roadmap.md.
- Existing prototype routes, controllers, views, JavaScript, CSS, navigation, fixtures, and reusable components.
- Existing main-app Structure, Settlement, Surface, and Space presentation as read-only references.

## Scope

1. Inventory the prototype's current Player Surface, Settlement placeholder, Space workspace, navigation, and reusable detail/inspector patterns.
2. Review the existing approved/reviewed Surface-view port task and Space workspace task. Do not recreate their scope or change their status.
3. Research a shared Structure Detail workspace or inspector that can be opened from the appropriate Surface, Settlement, or Space context.
4. Define a consistent drill-down hierarchy:
   - selected structure overview and properties;
   - equipment installed in the structure;
   - selected unit/module/rig details;
   - return path to the containing structure and higher-level context.
5. Define how the structure-level readout differs from the settlement-wide readout. The structure aggregates its own installed equipment; the settlement separately aggregates all applicable structures and shared infrastructure.
6. Consider how the same detail experience handles:
   - an orbital station with life support, power, docking, and industrial equipment;
   - a surface factory or habitat;
   - an agricultural dome with crop-specific environmental requirements;
   - an installation located inside a lava tube, where the tube is surrounding context rather than a substitute for the structure/equipment hierarchy.
7. Identify which information can be shown from existing main-app data, which can use clearly labeled mock fixtures, and which remains unavailable. Do not invent authoritative values or a new game data contract.
8. Recommend reusable presentation components and navigation/selection behavior without prescribing an unapproved backend or API architecture.
9. Reconcile any conflict between prototype wiki wording and the canonical main-game wiki. In particular, do not let the prototype redefine the number or role of Player views or make Space a Planetary/Surface subview.
10. Produce a small follow-up task breakdown only if distinct implementation work is justified by the inventory.

## Constraints

- This is research and prototype design only. Do not modify application code, routes, views, CSS, JavaScript, fixtures, or assets.
- Do not implement the existing Surface port task again or assume that its current review/hold location authorizes dispatch.
- Do not add a new Settlement or Structure domain model. Use existing game concepts and mark missing source data as unavailable.
- The prototype is a presentation/workflow workspace, not a second simulation or backend. Mock interactions must remain clearly mock.
- The main GalaxyGame wiki and code govern game intent and behavior. The prototype wiki governs presentation implications but cannot redefine game-domain rules.
- Keep Player, Admin, and AI Manager workflows separate.

## Deliverable

Create a report in projects/galaxy-game-ui/summaries/ containing:

- current prototype screen and component inventory;
- reuse opportunities and existing work that must be preserved;
- recommended structure/equipment drill-down interaction;
- distinction between structure and settlement readouts;
- how the same experience works in orbital and surface contexts;
- data availability and mock-data boundaries;
- wiki conflicts or unresolved design questions;
- a short list of implementation tasks, if warranted, with dependencies and recommended order.

## Acceptance criteria

- [ ] Existing prototype work and task records are inspected before proposing new screens.
- [ ] Surface, Settlement, and Space remain distinct contexts with appropriate navigation.
- [ ] One reusable structure/equipment detail experience supports orbital and surface structures.
- [ ] Structure-level and settlement-level information remain separate.
- [ ] No new game mechanics, domain models, API contracts, or authoritative mock values are invented.
- [ ] No prototype code or assets are changed.

## Dependencies

**Blocked by:** None for prototype inventory. Cross-check the main-app Surface/structure research and engineering aggregation findings when available.
**Blocks:** A later, separately approved prototype implementation task, if Tracy chooses to proceed.

## Progress / Resume

- **Done so far:** Research scope drafted from current prototype guidance and owner-confirmed structure hierarchy.
- **Next step:** Human review; no implementation or dispatch until approved.
- **Status:** Draft; not approved for dispatch.
