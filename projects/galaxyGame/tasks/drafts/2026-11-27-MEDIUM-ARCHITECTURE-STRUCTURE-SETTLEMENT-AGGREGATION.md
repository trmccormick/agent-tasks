---
status: draft
resume: false
priority: MEDIUM
type: architecture
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-27
---

# Structure and settlement engineering aggregation — design research

## Agent Dispatch Interface

**DRAFT ONLY — NOT DISPATCHABLE.** Suggested first-pass research assignment for Qwen after the installed-equipment data audit. Human review and approval are required before dispatch. No implementation is authorized.

## Objective

Determine how existing installed-equipment data should be aggregated into two distinct, connected engineering readouts:

1. An individual structure readout, aggregating everything installed in that structure.
2. A settlement readout, aggregating the structures and other installed assets that contribute to settlement-wide requirements and capacity.

This task defines the aggregation boundaries and outputs; it does not implement a new simulation or UI.

## Prerequisite

Complete and review 2026-11-27-MEDIUM-ARCHITECTURE-INSTALLED-EQUIPMENT-DATA-AUDIT.md first. Use its verified field inventory. If that audit is unavailable, stop after identifying the missing prerequisite; do not invent a schema.

## Scope

1. Treat a structure as an engineered container that holds installed units, modules, rigs, and other equipment. Examples include an orbital station, factory, agricultural dome, surface building, or installation within a lava tube.
2. Define the structure-level aggregation boundary. Determine how equipment contributes to that structure's:
   - total installed mass/weight;
   - electrical demand and any local generation;
   - heat generation, heat demand, and installed thermal capacity;
   - environmental requirements and operating limits;
   - material/resource inputs and outputs;
   - equipment count, capacity, availability, and operating status where supported.
3. Define the separate settlement-level roll-up across structures, vehicles, and settlement infrastructure where those assets contribute to settlement-wide requirements.
4. Distinguish locally installed capacity from services supplied by shared infrastructure. For example, a structure's electrical demand is not the same as owning its own generator.
5. Define rules to avoid double-counting shared generators, thermal stores, distribution equipment, or equipment represented at more than one hierarchy level.
6. Explain how structure-level and settlement-level results can be exposed as separate, readable, structured data for later UI use.
7. Test the conceptual model against at least an agricultural dome, a human-rated habitat, a robotic factory that may not require pressurization, and an orbital station.
8. Keep environmental requirements specific to purpose. Not every structure is human-rated or needs breathable air; biological facilities and industrial facilities can have different atmospheric and temperature targets.

## Constraints

- Reuse fields and relationships verified by the prerequisite audit. Do not invent a competing metadata or aggregation system.
- Do not conflate an individual structure with a settlement. A dome or station is one structure; the settlement is a higher-level aggregation.
- Do not assume all installed equipment is physically inside a structure if current ownership/location relationships say otherwise.
- Do not treat the lava tube or planetary surface as a structure's equipment container unless the existing domain model explicitly represents it that way. The tube may be an environmental shell/context containing structures.
- Do not create models, migrations, services, JSON fields, UI screens, or simulation behavior.
- Record unresolved ownership, location, or aggregation questions for Tracy rather than choosing silently.

## Deliverable

Create a design report in projects/galaxyGame/summaries/ describing:

- the verified equipment → structure → settlement relationships;
- structure-level aggregate values and their source fields;
- settlement-level aggregate values and their source fields;
- treatment of shared infrastructure and double-counting;
- examples for the four structure types above;
- unresolved questions and a minimal recommendation for a later implementation task.

## Acceptance criteria

- [ ] Structure and settlement are treated as separate aggregation levels.
- [ ] The structure readout includes all relevant equipment installed in that structure.
- [ ] Settlement aggregation combines structure outputs and other applicable assets without counting the same equipment twice.
- [ ] Shared infrastructure capacity is distinguished from local structure demand.
- [ ] Different structure purposes have different environmental requirements.
- [ ] Recommendations trace to the prerequisite audit and existing code/wiki.
- [ ] No implementation or data changes are made.

## Dependencies

**Blocked by:** Installed-equipment data audit.  
**Blocks:** Power/thermal capacity research and downstream UI data-contract work.

## Progress / Resume

- **Done so far:** Aggregation boundaries captured from owner-confirmed discussion.
- **Next step:** Human review; dispatch only after the prerequisite audit is complete and reviewed.
- **Status:** Draft; not approved for dispatch.
