# Galaxy Game — Handoff from Qwen (Planning/Coordination) to Claude

**Generated:** 2026-08-22
**Purpose:** Fresh coordination summary for Claude's work session. Combine with current repo state (status.md, active/backlog folders) — don't treat this as a replacement for checking the live repo.

---

## 🔴 Priority #1: Oxygen Fixture Chain-Tracing (Claude Handoff #1A)

**Status:** OPEN — never verified against actual code path

The oxygen fixture test now passes (20/0) after fixing material type lookup (`'type'` → `'category'`) and adding a gas storage unit. **But this fix only addresses which storage bucket O2 lands in — it does not confirm where the O2 came from.**

**The unanswered question:**
Does `HarvesterCompletionJob`'s completion path for an O2 order actually route through the real ISRU chain — harvest raw regolith → TEU (yields mixed volatiles) → PVE (yields O2 + depleted regolith) — with O2 only landing in inventory after PVE completes? **Or does the job (or the test/fixture) still short-circuit straight from "harvester deployed" to "O2 credited to inventory," treating oxygen as if it were a directly-harvestable raw material?**

Neither Luna nor Mars has free/harvestable oxygen anywhere — it is always a processed output. If the chain is short-circuited, the storage-bucket fix just made an architecturally wrong test pass cleanly, which reads as "done" and is worse than a red test.

**Action needed:** Trace the actual code path (method calls, not comments/docstrings) for the passing test and report explicitly:
1. Does completing the harvester job trigger a real, separate TEU job?
2. Does that TEU job's completion trigger a real, separate PVE job?
3. Does O2 only get written to inventory after PVE completes — or does `HarvesterCompletionJob` write O2 directly?

**Do not mark this task fully resolved until this is answered with file/line references.**

---

## ✅ Completed This Session (Update from Prior Handoff)

### Material Thermal Properties Data Gap — FIXED ✅
- **Root cause**: `refined_metals_backup/` directory had stale iron.json (missing `boiling_point`) overwriting correct cache entry in `MaterialLookupService`
- **Fix**: Removed entire `refined_metals_backup/` directory (8 duplicate IDs: iron, aluminum, copper, nickel, steel, titanium, gold, silver)
- **Test fix**: `material_management_concern_spec.rb:194` — changed expectation from `"iron"` → `"Fe"`
- **Test result**: 57 examples, 0 failures across material/geosphere/material_management specs
- **Task file**: moved to completed/ in agent-tasks repo
- **Commits**: `6d32266f` (galaxyGame), `dd5e5d9` (agent-tasks)
- **Follow-up found** (not fixed): `composite/` vs `composites/` both have `carbon_nanotubes.json`; `refined_materials/` vs `semiconductors/` both have `high_purity_silicon.json`

### Oxygen Fixture Fix — FIXED ✅
- **Root cause**: Material type lookup used wrong field (`'type'` instead of `'category'`)
- **Fix**: `inventory.rb` line 159 — changed `dig('type')` → `dig('category')`
- **Test result**: 20 examples, 0 failures (full escalation_integration_spec passes)
- **Task file**: moved to completed/ in agent-tasks repo
- **Commits**: `680b6a04` (galaxyGame), `6bbc855` (agent-tasks)

---

## 🔴 Priority #2: Verification Debt — Open Items

### B. Magnetosphere task filename discrepancy
A prior handoff said the closed task was `2026-08-14-LOW-BUG-FIX-HAS-MAGNETOSPHERE-DERIVATION.md`. The actual task that was implemented (sigmoid core-state gate, commits `dbc5c254`+`65b8f48a`) and found stale-but-unclosed was `2026-08-15-HIGH-FIX-MAGNETOSPHERE-STUB-CALCULATION.md` — different date, different priority, different name.

**Action needed:** Open `completed/2026-08/` and confirm which filename is actually there. If it's a mismatch, find out whether the real stub-calculation task file is still sitting open somewhere unaccounted for.

### C. NEEDS_REVIEW #6 (41 bodies defaulting to 0.5 magnetosphere)
Marked resolved alongside the magnetosphere task closure above. Confirm this was actually verified, not just administratively swept along with unrelated work.

### D. Phase-folder reorg Step 8 verification
The reorg (7 canonical folders, 4 legacy deleted, 4 duplicates resolved, 5 ambiguous items placed) was reported complete, but the actual file-count / no-orphans verification output from Step 8 was never shown directly — only summarized. Given this session had a real file-corruption incident earlier (a duplicated Mars JSON block during blueprint edits), this is worth a direct look before trusting the reorg is clean.

---

## 🟡 Recurring Conflation to Watch For

**Boil-off enforcement ≠ graphene_composite manufacturing chain.** Coordination summaries have repeatedly framed the missing `graphite`/`epoxy_resin`/`fabrication_plant` blueprints as blocking "boil-off enforcement." They don't. Boil-off enforcement code only needs to read the boil-off rate fields that **already exist** on the mk1/mk2/mk3 storage blueprints — it has nothing to do with whether graphene_composite can be locally manufactured (that's a separate, Phase 11+ concern, since Earth's production of mk2 units is an abstracted simplification and doesn't need the in-game blueprint chain at all). If boil-off enforcement is actually wanted, it can be dispatched independently right now — it isn't blocked on anything in the graphene_composite thread.

---

## 🟢 Mars MOXIE-Analog Design (Not Yet a Real Task)

**Status:** Design intent captured, needs to be turned into a proper design-tier task file (`backlog/design/`) before it's forgotten. Tracy is working with Qwen directly on this — don't duplicate that work.

Key points already established:
- Standard, ubiquitous baseline Mars infrastructure (like a heat pump/AC unit on Earth) — not a rare deployment.
- Pulls in Mars atmosphere → gas separator (generic multi-output device, same pattern as Luna's regolith separator / Venus HLT's CO2 splitter) → MOXIE component yields O2; separately stores N2/trace gases as habitat buffer gas.
- Fully on-site, continuous/passive production — no harvest-and-transport step, unlike Luna's model.
- Escalation trigger is different in kind from Luna's: shortfalls come from leaks/damage/population growth outpacing capacity, not "go get raw material elsewhere." Mars's fallback is "deploy/build more capacity," not "dispatch a harvester."
- Checked existing `production/extractors/atmospheric_collector_blueprint.json` and `nitrogen_harvester_blueprint.json` — both are Triton-scoped (`compatibility: ["triton_atmosphere"]`), neither produces O2, and the mobile-harvester model on the nitrogen one contradicts the Mars stationary-unit design. Confirmed new work is genuinely needed; these are at best a loose schema reference.

---

## 🟢 Current Backlog — Ready for Dispatch (Unaffected by Above)

### HIGH Priority
| Task | Location | Notes |
|------|----------|-------|
| **Epoxy Resin Blueprint** | `backlog/phase10-venus/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` | READY — same workflow as graphite task (search → create if missing) |
| **Fabrication Plant Blueprint** | `backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` | DEFERRED (Phase 11+) |
| **Orbital Mechanics Data Layer** | `backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md` | Phase 1-4 complete, Phase 5 pending |
| **Launch Window + Transit Timing Engine** | `backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` | Architecture feature |

### MEDIUM Priority
| Task | Location | Notes |
|------|----------|-------|
| **Classify 19 Blueprints** | `backlog/current/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md` | NEEDS_REVIEW #4 |
| **CNT Fabricator Collision** | `backlog/current/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md` | NEEDS_REVIEW #5 |
| **Atmosphere Generator Body Data Nil** | `backlog/current/2026-08-17-MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL.md` | Bug fix |

### LOW Priority
| Task | Location | Notes |
|------|----------|-------|
| **Financial Transaction Enum** | `review/2026-05-28-LOW-FEATURE-FINANCIAL-TRANSACTION-ENUM-AND-SPEC.md` | SUPERSEDED |

---

## 📊 Current Test Baseline
- **RSpec:** 4714 examples, 174 failures, 55 pending (from 08-13/14 pre-push audit)
- **Rake:** 17/17 ✅ all phases PASSED — verified in container

---

## Standing Guardrails Worth Re-Stating

- **Never `git add .`** in agent-tasks or any shared repo — always explicit filepaths. (Caught and fixed 08-21.)
- **A "completed" claim ≠ the task file/repo actually reflecting it.** Verify file-system state directly after any move/close/delete claim.
- **Resource/material keys use chemical formulas only** (O2, CO2, CH4, N2) — never colloquial names. This is a deliberately hardened convention; a fixture using "oxygen" instead of "O2" is the fixture being wrong, not a gap needing alias-handling code.
- **New material/component chains must terminate at something real and stay shallow** — if an agent can't say what a proposed material is made from, don't let it invent another layer to answer; stop and ground it.
- **Shared/global code changes need a Synthesis Report before committing**, not after — this has been skipped multiple times this month.
- A direct folder/file read always outranks a written summary when they disagree.

---

## Suggested Order of Work for Claude

1. **Resolve the oxygen-fixture chain-tracing question** (Priority #1 above) — this determines whether that task is actually done.
2. **Quick file-existence checks** on magnetosphere task filename (#2B) and reorg Step 8 output (#2D) — both fast, both worth confirming before building further on top of assumed-clean state.
3. **If boil-off enforcement is a priority**, it can be dispatched now — it isn't blocked on the deferred blueprint tasks.
4. **Continue the Mars MOXIE-analog design task** with Qwen when ready — not urgent, but don't let it go untracked.
5. **Everything else in the current backlog** (epoxy resin blueprint, MEDIUM bug fixes) is unaffected by the above and can proceed normally.
