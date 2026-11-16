# Galaxy Game — Handoff from Claude (Planning) to Haiku

**Generated:** 2026-08-22
**Purpose:** Give Haiku what it needs to produce a fresh, accurate development-state summary for today's work session. Combine this with current repo state (status.md, active/backlog folders) — don't treat this as a replacement for checking the live repo.

---

## 🔴 #1 Priority: Verify Before Trusting — Open Verification Debt

These are claims made in prior sessions that were **never independently re-verified**. Don't build on top of them until checked.

### A. Oxygen fixture "fix" — real but incomplete
Haiku's last fix (inventory.rb: material type lookup used `'type'` instead of `'category'`) is plausible and the target test + full spec (20/0) reportedly pass. **But this fix only addresses which storage bucket O2 lands in — it does not confirm where the O2 came from.**

**The unanswered question, asked twice, never traced against actual code:**
Does `HarvesterCompletionJob`'s completion path for an O2 order actually route through the real ISRU chain — harvest raw regolith → TEU (yields mixed volatiles) → PVE (yields O2 + depleted regolith) — with O2 only landing in inventory after PVE completes? **Or does the job (or the test/fixture) still short-circuit straight from "harvester deployed" to "O2 credited to inventory," treating oxygen as if it were a directly-harvestable raw material?**

Neither Luna nor Mars has free/harvestable oxygen anywhere — it is always a processed output. If the chain is short-circuited, the storage-bucket fix just made an architecturally wrong test pass cleanly, which reads as "done" and is worse than a red test.

**Action needed:** Trace the actual code path (method calls, not comments/docstrings) for the passing test and report explicitly:
1. Does completing the harvester job trigger a real, separate TEU job?
2. Does that TEU job's completion trigger a real, separate PVE job?
3. Does O2 only get written to inventory after PVE completes — or does `HarvesterCompletionJob` write O2 directly?

**Do not mark this task fully resolved until this is answered with file/line references.**

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

## 🟢 New Design Capture — Not Yet a Real Task, Don't Dispatch Yet

**Mars MOXIE-analog atmospheric O2/buffer-gas unit** — design intent captured this session, needs to be turned into a proper design-tier task file (`backlog/design/`) before it's forgotten. Key points already established:
- Standard, ubiquitous baseline Mars infrastructure (like a heat pump/AC unit on Earth) — not a rare deployment.
- Pulls in Mars atmosphere → gas separator (generic multi-output device, same pattern as Luna's regolith separator / Venus HLT's CO2 splitter) → MOXIE component yields O2; separately stores N2/trace gases as habitat buffer gas.
- Fully on-site, continuous/passive production — no harvest-and-transport step, unlike Luna's model.
- Escalation trigger is different in kind from Luna's: shortfalls come from leaks/damage/population growth outpacing capacity, not "go get raw material elsewhere." Mars's fallback is "deploy/build more capacity," not "dispatch a harvester."
- Checked existing `production/extractors/atmospheric_collector_blueprint.json` and `nitrogen_harvester_blueprint.json` — both are Triton-scoped (`compatibility: ["triton_atmosphere"]`), neither produces O2, and the mobile-harvester model on the nitrogen one contradicts the Mars stationary-unit design. Confirmed new work is genuinely needed; these are at best a loose schema reference.

Tracy is working with Qwen directly on the final version of this task file — don't duplicate that work, just be aware it's happening.

---

## Standing Guardrails Worth Re-Stating for Today

- **Never `git add .`** in agent-tasks or any shared repo — always explicit filepaths. (Caught and fixed 08-21.)
- **A "completed" claim ≠ the task file/repo actually reflecting it.** Verify file-system state directly after any move/close/delete claim.
- **Resource/material keys use chemical formulas only** (O2, CO2, CH4, N2) — never colloquial names. This is a deliberately hardened convention; a fixture using "oxygen" instead of "O2" is the fixture being wrong, not a gap needing alias-handling code.
- **New material/component chains must terminate at something real and stay shallow** — if an agent can't say what a proposed material is made from, don't let it invent another layer to answer; stop and ground it.
- **Shared/global code changes need a Synthesis Report before committing**, not after — this has been skipped multiple times this month.
- A direct folder/file read always outranks a written summary when they disagree.

---

## Suggested Order of Work for Today

1. Resolve the oxygen-fixture chain-tracing question (1A above) — this determines whether that task is actually done.
2. Quick file-existence check on the magnetosphere task filename (1B) and the reorg Step 8 output (1D) — both fast, both worth confirming before building further on top of assumed-clean state.
3. If boil-off enforcement is a priority, it can be dispatched now — it isn't blocked on the deferred blueprint tasks.
4. Continue the Mars MOXIE-analog design task with Qwen when ready — not urgent, but don't let it go untracked.
5. Everything else in the current backlog (epoxy resin blueprint, MEDIUM bug fixes) is unaffected by the above and can proceed normally.
