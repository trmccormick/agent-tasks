Task: Fix can_harvest_locally? — add CO2 atmospheric case + ISRU gate for O2

Per 2026-08-24-ESCALATION-SERVICE-DESIGN-CLARIFICATIONS.md's Immediate Fixes list.

STEP 1 — Add CO2 as a trivially-harvestable atmospheric case (parallel to existing N2 case)
in escalation_service.rb's can_harvest_locally?:

  when 'CO2'
    celestial_body.atmosphere&.gases&.any? { |g| g.name == 'CO2' }

STEP 2 — Add an ISRU capability gate to the existing 'O2' case: currently it grants O2
purely from atmosphere-gas-presence. Keep that path for bodies where O2 genuinely exists in
atmosphere (e.g. Earth), but for bodies WITHOUT atmospheric O2 (Luna, Mars), O2 must come
from regolith/water-ice processing — gate this on whether the settlement actually has
PVE/TEU units deployed (check settlement's deployed units against a PVE/TEU-type blueprint,
not just presence of the raw material). Do not invent a new capability-check method if one
already exists elsewhere in the codebase for checking deployed unit types — search first.

STEP 3 — Do NOT touch supplied_via_hlt_mission?, determine_escalation_strategy's
humans_present? gate, or the manifest-generation service in this task — those are
medium/long-term items from the same handoff, explicitly out of scope here.

STEP 4 — Write/run specs confirming: Mars CO2 harvests trivially; Luna/Mars O2 does NOT
grant credit without a deployed PVE/TEU; Earth O2 (if modeled with atmospheric O2) still
works via the existing atmosphere-presence path.

STEP 5 — Synthesis report, then update the still-open oxygen-fixture task
(2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md) noting Priority #1's
structural question (can_harvest_locally? has no ISRU gate) is now RESOLVED by this fix —
only then can that task move to completed/.