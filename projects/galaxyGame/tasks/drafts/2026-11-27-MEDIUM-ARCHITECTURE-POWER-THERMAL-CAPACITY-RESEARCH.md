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

# Power and thermal capacity — equipment sizing research

## Agent Dispatch Interface

**DRAFT ONLY — NOT DISPATCHABLE.** Suggested Qwen research task after the data audit and aggregation review. Human review and approval are required before dispatch. No implementation is authorized.

## Objective

Research the smallest practical way for GalaxyGame to determine whether installed equipment and supporting infrastructure are correctly sized and efficiently operated for their actual power and thermal requirements.

The goal is to engineer a suitable installation from modular blueprints and operational data—not to introduce detailed thermodynamic controls or require players to operate individual valves, pumps, or temperatures.

## Prerequisites

- 2026-11-27-MEDIUM-ARCHITECTURE-INSTALLED-EQUIPMENT-DATA-AUDIT.md
- 2026-11-27-MEDIUM-ARCHITECTURE-STRUCTURE-SETTLEMENT-AGGREGATION.md

Use the verified fields and aggregation boundaries from those tasks. Do not proceed by inventing missing blueprint fields.

## Scope

1. Trace how the verified unit and structure properties contribute to electrical demand, generation, and thermal supply/demand.
2. Distinguish environmental conditioning from industrial process-temperature requirements. A comfortable habitat or laboratory can coexist with a high-temperature industrial process.
3. Research how installed equipment capacity should be evaluated at the appropriate levels:
   - individual unit or process;
   - individual structure;
   - shared settlement infrastructure.
4. Consider normal, peak, standby, and overlapping operating loads where current operational data supports them.
5. Evaluate the relationship between electrical power and heat:
   - electrical consumption and conversion efficiency;
   - usable waste heat and its temperature level;
   - direct reuse by compatible consumers;
   - heat pumps and their electrical cost;
   - thermal storage capacity, recovery limits, and standing losses;
   - heat rejection capacity and environmental constraints.
6. Define a simple automated control concept using existing temperature information and purpose-specific operating ranges. Include suitable trigger thresholds and hysteresis to prevent rapid cycling.
7. Explain how the AI Manager or infrastructure planner could identify undersized capacity, unused or stranded capacity, bottlenecks, and useful upgrades.
8. Consider timing and location: heat is useful only when transfer capacity, temperature level, location, and availability make it usable.
9. Evaluate how the same general model applies to orbital stations, surface structures, agricultural domes, robotic factories, and lava-tube installations, with environmental baselines supplied by existing planetary/environmental systems.

## Established design constraints

- Existing Atmosphere code tracks enclosed-volume temperature and other atmospheric state. Existing structures also have temperature information. Reuse these sources; do not create a duplicate temperature or atmosphere model.
- Heat-flow/energy-exchange calculation is the known missing integration. Do not re-open that as an unresolved existence question.
- The current settlement thermal-management wiki page is owner-confirmed design intent, not proof of implementation: docs/wiki/infrastructure/settlement_thermal_management.md.
- The design supports direct waste-heat reuse, finite thermal storage and recovery with losses, heat pumps where justified, and rejection of unusable surplus. A detailed pipe/duct network simulation is not required.
- Heat is energy transfer, not a material inventory. Thermal storage is stored energy, not a new material commodity.
- Ordinary human habitats have integrated HVAC; excess HVAC condensate is clean water routed to clean-water storage. Do not introduce separate humidity/condensate mechanics.
- A robotic factory does not automatically need a pressurized or breathable atmosphere. Environmental requirements follow the structure's function.
- Do not assume that all waste heat is recoverable or that adding more capacity is always efficient.

## Constraints

- No code, blueprint, JSON, model, migration, or runtime changes.
- Do not define arbitrary numerical thresholds or efficiencies without sourced engineering basis and explicit game-design approval.
- Separate established design intent, verified implementation, external engineering reference, and proposed simplification.
- Prefer aggregate capacity and efficiency metrics over detailed thermodynamic simulation.
- Player-facing controls are not the objective; routine operation should be automated. The player should be able to inspect requirements, installed capacity, and deficiencies.

## Deliverable

Create a research report in projects/galaxyGame/summaries/ containing:

- verified current power and thermal fields and their calculation boundaries;
- a simple capacity-sizing approach at unit, structure, and settlement levels;
- environmental versus process heat requirements;
- heat reuse, storage, heat-pump, and rejection trade-offs;
- proposed trigger/hysteresis logic and how it uses existing temperature information;
- example assessments for a habitat, agricultural dome, robotic factory, and station;
- missing data, unresolved design decisions, and recommended follow-up task boundaries.

## Acceptance criteria

- [ ] Prerequisite audit and aggregation results are used.
- [ ] Power and thermal requirements remain connected but distinct.
- [ ] Existing temperature and atmosphere models are reused.
- [ ] Capacity sizing covers both structure-local and shared settlement infrastructure.
- [ ] Thermal storage, recovery losses, and heat rejection are represented without detailed fluid-network simulation.
- [ ] Recommendations are evidence-based and clearly label assumptions.
- [ ] No implementation or data changes are made.

## Dependencies

**Blocked by:** Installed-equipment data audit and structure/settlement aggregation research.  
**Blocks:** Any later implementation task for engineering capacity calculation or automated thermal management.

## Progress / Resume

- **Done so far:** Research boundaries captured from owner-confirmed engineering intent and the current thermal-management wiki.
- **Next step:** Human review; dispatch only after prerequisite reports are reviewed.
- **Status:** Draft; not approved for dispatch.
