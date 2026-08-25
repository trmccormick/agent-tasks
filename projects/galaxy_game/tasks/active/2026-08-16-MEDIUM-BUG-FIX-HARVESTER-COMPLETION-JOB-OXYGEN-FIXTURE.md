---
status: active
priority: MEDIUM
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
created: 2026-08-16
updated: 2026-08-23
estimated_effort: 30-45 min (test-only fix)
blocker_for: []
depends_on: []
---

# TASK: Luna Oxygen Fixture — Diagnose Broken Pathway

## ✅ PARTIALLY RESOLVED (2026-08-23)

### What Was Fixed (Fixture Bug — Item #9)

**Resolution:** The test fixture had two bugs:
1. Order resource was `'oxygen'` (non-harvestable on Luna) → changed to `'raw_regolith'`
2. Storage unit used wrong key (`operational_data['storage']['type']`) → fixed to `operational_data['subcategory']`

**Commit:** `34542440` in galaxyGame — test-only fix, no production code touched.

**Test Result:** ✅ 20 examples, 0 failures (no regressions)

### What Remains Open — Priority #1: Oxygen Chain-Tracing

**This task is NOT fully resolved.** The fixture bug was a test-side issue that masked a deeper architectural question:

> **Does raw_regolith ever route through TEU → PVE to produce O2 for an oxygen-triggered escalation order?**

Per the synthesis report (`summaries/2026-08-22-BUG-FIX-OXYGEN-FIXTURE-ITEM-9.md`):
> "This fix confirms a harvester can deliver raw_regolith to a settlement with adequate storage. It does NOT confirm that raw_regolith is ever routed through TEU → PVE to produce O2 for an oxygen-triggered escalation order. That question is still open and is NOT resolved by this task. Do not mark Priority #1 (oxygen chain-tracing) as closed based on this fix."

**Ryzen diagnostic findings:**
- `HarvesterCompletionJob` has no TEU/PVE routing — it only adds `order.resource` to inventory
- `EscalationService.can_harvest_locally?` grants direct O2 credit purely from atmosphere gas presence with no ISRU-infrastructure check
- The oxygen pathway (regolith → PVE → O2) is never actually wired through the escalation system

**Next action:** Separate task needed to trace whether EscalationService correctly routes oxygen-shortage orders through ISRU production (PVE/TEU) or incorrectly credits atmosphere gas as available oxygen.

---

### Design Clarifications (2026-08-24)

The following design decisions were confirmed by Tracy during this session:

**1. AI Manager cannot bootstrap a settlement without local material extraction.**
The AI Manager won't create a settlement on Luna/Mars if it can't extract materials for human habitation. It must find local sources first — everything cannot be imported. This is the fundamental constraint that `can_harvest_locally?` serves in the escalation path.

**2. Two distinct harvesting tiers (data-driven, not hardcoded per-world):**
- **Atmospheric gases** (trivial intake machine): CO₂, N₂, O₂ if present in atmosphere. Simple gas separator/intake — no ISRU processing required.
- **Geosphere/hydrosphere** (requires mining equipment): water ice in PSRs needs mining site setup + specialized extraction + PVE processing. Regolith is different — it's "scooping" not "mining." A harvester scoops regolith and dumps it into TEU/PVE. No mine infrastructure, no special equipment beyond a basic harvester.

**3. Priority order for AI-driven escalation (cheapest path first):**
1. Atmospheric gases → trivial intake machine
2. Regolith harvesting → simple scooping (harvester)
3. Mining + ISRU processing → water ice in PSRs requires mine setup + PVE
4. Imports → only when body genuinely cannot produce

**4. CO₂ on Mars is trivially harvestable.**
95% of the atmosphere. Simple intake machine connected to gas separator. Used for MOXIE-style O₂ production, Sabatier methane synthesis (CO₂ + H₂ → CH₄ + H₂O), and greenhouse pressurization. This is fundamentally different from O2 which requires PVE splitting.

**5. Processing gate applies only to materials that genuinely can't be harvested directly.**
- CO₂ on Mars: no processing check needed (atmospheric gas)
- N₂ on any atmosphere body: no processing check needed (atmospheric gas)
- O2 from regolith/water ice: requires PVE/TEU deployed
- Metals from smelting: requires PVE/TEU deployed

**6. The fix to `can_harvest_locally?` should:**
- Add CO₂ as an atmospheric case (parallel to N2)
- Keep O2 checking atmosphere for Earth (free source)
- Add ISRU capability gate when sourcing O2 from regolith/water ice on bodies without atmospheric O2
- Stay generic and data-driven — check what the body has + equipment available, not per-world hardcoded logic

**7. No hardcoded world names anywhere in escalation logic.**
The `supplied_via_hlt_mission?` method uses a `case celestial_body.name.downcase` with hardcoded lists for 'luna' and 'mars'. This violates data-driven design — it breaks on procedurally generated bodies and stays static even if a body's properties change (e.g., terraformed Mars). The game should query the body's **current** attributes (`celestial_body.atmosphere.gases`, `celestial_body.hydrosphere`, `celestial_body.materials`) dynamically, not check world names against static lists. Any import route logic should query actual game state (is there an operational skimmer route from a colonized body that has this material?) rather than a pre-written manifest.

**8. AI Manager monitors stockpiles proactively — imports are fallback, not primary.**
The AI Manager should monitor consumption vs production rates and identify shortfalls before they become critical. For airless bodies like Luna (no atmosphere to query), the code falls through to checking geosphere/hydrosphere (regolith, water ice). The hardcoded world name check "works" by accident for Luna but would break on any procedurally generated airless body. Imports are only triggered when local production can't meet demand OR is failing (equipment breakdown, resource depletion). Travel time matters — by the time an import arrives, the settlement might have already run out of O2/water. Local production is always preferred for critical life support resources.

**9. Escalation covers equipment/maintenance, not just raw materials.**
Escalation isn't only about resource shortfalls (O2, water, CO2). It also covers **equipment parts and maintenance**. If a TEU or PVE breaks down and there are no backup parts in stockpile, that triggers its own escalation order to get replacement parts before the equipment fails. The AI Manager monitors two categories:
- **Stockpile levels** → "do we have enough O2/water?"
- **Equipment health/spare parts inventory** → "if the PVE dies right now, can we keep producing O2?"

This means `can_harvest_locally?` isn't the only escalation gate — there also needs to be a check for whether the settlement has backup parts for its critical production equipment. If not, an escalation order should fire to get replacement parts before the equipment fails.

**10. Parts escalation chain: local manufacture → partial import → special missions → urgent resupply.**
When a critical part is needed and no backup exists in stockpile, the AI Manager follows this path:
1. **Local manufacture** — check blueprints + available facilities (fabrication_plant, foundry, refinery) to determine if the part can be made on-site
2. **Partial import + local assembly** — if only some components need import, import just those and assemble locally
3. **Emergency special missions** — for urgent needs, route to player-completed missions for rewards (not AI Manager)
4. **Urgent resupply from AstroLift** — last resort, batch as much as possible into one shipment (a single-item import may not be cost-effective)

This means there should be a `can_produce_locally?` check (parallel to `can_harvest_locally?`) that queries available facilities and their input requirements before triggering an import for parts. The AI Manager's job is to keep the base running with what's available locally first, only importing when it genuinely can't be made on-site.

**11. Emergency resupply batches all pending needs — not just the emergency item.**
The AI Manager maintains a **running manifest of pending needs** (ongoing + emergency). When an emergency triggers, it doesn't just request that single item; it batches all current pending items into one urgent shipment to maximize cost-effectiveness and minimize delivery time. This is why manifests should be auto-generated by the AI Manager — they're dynamic lists that grow as needs accumulate, not static pre-written cargo lists.
