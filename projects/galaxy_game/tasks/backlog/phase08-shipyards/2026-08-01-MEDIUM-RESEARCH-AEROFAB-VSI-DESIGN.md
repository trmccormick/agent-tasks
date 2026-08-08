---
title: AeroFab CNC Module & Volatile Systems Integrator — design/specs needed
status: backlog
priority: MEDIUM
created: 2026-08-01
assigned_to: gemini (design session)
---

# Context

During today's blueprint naming/placement cleanup, two blueprints were
relocated and renamed for consistency but discovered to be incomplete stubs,
not finished units:

- production/fabricators/aero_fab_cnc_module_mk1_bp.json
- production/refineries/volatile_systems_integrator_mk1_bp.json

Both have placeholder values (metadata.status: "stub", production time = 1h,
cost = 1 GCC) and neither has matching operational data — confirmed missing
via direct file check, not just unreferenced.

# What's needed

A design session (Gemini) to answer, for each unit:

1. What is this unit actually for? Based on name/folder placement:
   - AeroFab CNC Module → sounds like a fabrication subsystem (CNC = computer
     numerical control), likely used inside a larger fabricator/workshop unit
   - Volatile Systems Integrator (VSI) → sounds like a refinery-tier unit
     that combines/routes volatile outputs (O2, H2O, etc.) between other
     ISRU units
2. Is it a standalone active unit (needs full blueprint + operational data,
   same tier as e.g. the PVE or TEU) or a component consumed in constructing
   a larger unit (may not need standalone operational data at all, per the
   existing convention that components-of-other-things don't get separate
   operational data files)?
3. If it's a standalone unit: real dimensions, mass, materials, production
   time/cost, power draw, required facility/technology (matching real
   tech_tree entries, not placeholders), and what it produces/does
   mechanically.
4. If it's a component: what parent unit(s) consume it, and does the parent
   unit's blueprint already account for it or need updating.

# Not urgent

No premium agent time needed on this until the design session happens —
purely a research/design gap, not a bug. Filed here so it doesn't get lost
between now and the next Gemini session.