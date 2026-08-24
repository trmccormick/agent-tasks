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
