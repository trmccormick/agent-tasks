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

# Installed equipment and operational data — source audit

## Agent Dispatch Interface

**DRAFT ONLY — NOT DISPATCHABLE.** Qwen is the suggested first-pass research agent. Tracy must review and approve this draft before it is moved to backlog or assigned. No implementation is authorized.

## Objective

Establish what the current GalaxyGame code, blueprints, and operational data already define for structures, units, modules, rigs, and installed equipment. This is an evidence-gathering task, not a schema redesign.

## Scope

1. Read the current GalaxyGame project guidance, authoritative wiki pages, and relevant code before drawing conclusions.
2. Inspect structure/building blueprints, unit and module blueprints, rig/equipment definitions, and operational data in the local GalaxyGame workspace.
3. Inventory existing properties relevant to engineering and operation, including where present:
   - mass/weight and physical dimensions or capacity;
   - electrical power demand, generation, peak/idle demand, and energy inputs;
   - heat generated, waste heat, heat required, operating temperature limits, and thermal equipment capacity;
   - atmosphere/environment requirements, pressure, temperature, and sealing;
   - material/resource inputs and outputs, efficiency, throughput, and operating limits.
4. Trace how blueprint and operational-data properties are loaded, represented in code, and exposed to current structure/unit behavior.
5. Identify shared infrastructure versus equipment owned by an individual structure or unit.
6. Classify each finding as source-verified implementation, documented design intent, draft/unverified data, stale/conflicting information, or unknown.
7. Record exact file paths, field names, units, and representative examples. Do not infer units or semantics from a field name alone.

## Important source and data constraints

- The JSON data under the GalaxyGame data directory is gitignored and local to the working environment; it is not available from GitHub. Inspect the actual local files when running this task. Do not claim that a GitHub copy was inspected.
- Treat JSON values as data to audit, not automatically validated engineering values. Some values may be provisional or agent-generated. Flag questionable values rather than correcting them.
- Existing Atmosphere functionality already tracks enclosed-volume gas composition, pressure, mass, temperature, and sealing. Existing structure/habitat temperature information also exists. Reuse and report the actual fields and behavior; do not propose a parallel atmosphere or temperature model.
- The absence of a heat-flow/energy-exchange calculation is an established project finding. Do not spend this task re-proving that absence; identify the precise existing boundary and related fields.
- Code establishes what is implemented. The current GalaxyGame wiki establishes design intent. Record disagreements; do not silently reconcile them.
- Do not edit code, JSON, blueprints, wiki pages, or other task files.

## Deliverable

Create a concise source-audit report in projects/galaxyGame/summaries/ with:

- a field inventory grouped by structure, unit, module, rig, and operational data;
- verified source paths and data flow from blueprint to runtime object;
- field semantics and units where confirmed;
- existing aggregation or calculation behavior, if any;
- missing, inconsistent, duplicated, or unverified properties;
- explicit conflicts between code, wiki, and local JSON;
- a recommended evidence-based scope for the next aggregation task.

## Acceptance criteria

- [ ] Current project guidance and relevant wiki sources are read.
- [ ] Local JSON is inspected in the correct workspace; its gitignored status is respected.
- [ ] Findings cite exact paths and actual field names rather than hypothetical schemas.
- [ ] Existing atmosphere and temperature facilities are reused, not duplicated.
- [ ] No code, JSON, blueprint, or wiki changes are made.
- [ ] The report separates verified implementation from design intent and unverified values.

## Dependencies

**Blocked by:** None.  
**Blocks:** Structure/settlement aggregation research and power/thermal capacity research.

## Progress / Resume

- **Done so far:** Scope drafted from owner-confirmed design discussion and current project guidance.
- **Next step:** Human review; after approval, Qwen performs the source audit.
- **Status:** Draft; not approved for dispatch.
