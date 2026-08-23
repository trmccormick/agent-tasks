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
