# Perplexity Handoff — Galaxy Game Session 2026-09-14

**Prepared by**: Qwen (Planning Agent)  
**Session Date**: 2026-09-14  
**For**: Perplexity (economy/asset work lane)  

---

## Executive Summary

We've completed a full review of all handoff files from the past two weeks (09-03 through 09-13). Everything is verified and accurate. The project's highest-priority gap has been identified: **the GCC mining satellite is decoupled from the game tick loop**, which means the entire initial gameplay loop (GCC → fund Luna precursor → deploy Venus skimmer) is broken at step 1.

A task draft for this fix has been created below. It's left undispatched per convention — Tracy will review before dispatch.

**Your lane**: Economy wiki consolidation + help verify mining-rate computation fields before the GCC task is implemented. The economy docs work and the GCC capacity-driven output design are directly related (GCC generation ties to computing infrastructure capacity).

---

## What Was Done This Session

### 1. Full Handoff Review — All Verified ✅
- Read all handoffs from Claude, Gemini, Qwen across 09-03 through 09-13
- Cross-referenced every claim against the actual codebase
- **Result**: All Claude claims verified accurate; no false claims found
- Two pending tasks identified for dispatch (see below)

### 2. GCC Power/Battery Bug — Confirmed Never Dispatched
- Task file exists in `backlog/current/` since 09-03 (11 days stale)
- No synthesis report, no spec/doc in galaxyGame, no relevant commits
- The handoff trail said "awaiting results" but nobody ever picked it up

### 3. Architectural Gap Identified — GCC Satellite Decoupled from Tick Loop
**The problem**: Craft (satellites) inherit `ApplicationRecord`, not `Units::BaseUnit`.  
`advance_by_days` → `process_units` walks `Units::BaseUnit.all.each { |u| u.operate(days) }` — satellites are never reached.

**What the rake does** (gcc_mining_sat.rake):
```ruby
# Line 227: game.advance_by_days(1)     ← real production tick path
# Line 231: satellite.mine_gcc           ← manual call, separate from tick
```
Two independent calls stapled together. The rake *looks* tick-driven but mining happens entirely outside the loop.

**What the integration test proved** (09-03): GCC mining works on tick 1 when invoked inline. Did NOT prove multi-tick behavior through the game loop.

### 4. GCC Mining Satellite Task Drafted
See `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-FITTING-DRIVEN-OUTPUT-GAMELOOP-INTEGRATION.md` (below). Left undispatched for Tracy review.

---

## Pending Tasks — Ready to Dispatch

| Priority | Task | Location | Status |
|---|---|---|---|
| **P0** | GCC Mining Satellite — Fitting-Driven Output & Game-Loop Integration | Draft below, NOT dispatched | Awaiting Tracy review |
| **P1** | Missions-v2 Phase 1 validation | `backlog/current/2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md` | Dispatch-ready |
| **P2** | Wiki-sync-and-cleanup | `backlog/economy/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md` | Dispatch-ready |

---

## Your Lane: Economy Wiki + GCC Capacity Design

### Economy Docs — Current State
The economy wiki in `docs/new_agent/projects/galaxy_game/economy/` has been consolidated and corrected:
- **02-currencies-and-accounts.md** — Corrected: GCC minting is NOT "revenue conversion"; LedgerEntry/LedgerManager are real (not stubs); EAP scope restricted to Luna bootstrap only
- **03-market-and-pricing.md** — Corrected: NPC pricing uses `.evaluate_strategy` in NpcPriceCalculator; EAP×0.90/0.80 is Luna-only
- **AUDIT-ECONOMY-DOCS.md** — Audit trail documenting all corrections

**Remaining gap**: The ledger section doesn't distinguish implemented vs. skeletal components (deferred from 09-13).

### GCC Capacity Design — What You Can Help Verify
Before the GCC task is dispatched, these fields need confirmation:

1. **`crypto_mining_satellite_data.json`** has `base_mining_rate_gcc_per_hour: 1000` (flat constant)  
   → Does `Satellite#mine_gcc` actually read this field, or does it read fitted-unit hash rates?

2. **`generic_satellite_bp.json`** `compatible_units` whitelist doesn't include `satellite_battery`  
   → Is this whitelist enforced anywhere? If so, the recommended fit (which includes a battery) shouldn't install.

3. **`server_farm_bp.json` / `mainframe_computer_bp.json`** have TFLOPS/module_slots but no GCC output link  
   → These are settlement-side computing infrastructure. The design intent is that "any sufficiently-provisioned computing infrastructure can mine." This link doesn't exist yet — it's a follow-up to the satellite task, likely belonging with Gemini's economic-capacity work.

**Your help**: If you can confirm which fields `mine_gcc` actually reads today (flat constant vs. fitted-unit aggregation), that will determine whether the GCC task needs to build fitting-driven computation from scratch or just wire it into the tick loop.

---

## What Perplexity Should Do Today

### Priority 1: Verify Mining-Rate Fields (5-10 min)
Read these files and confirm what `mine_gcc` actually uses:
- `data/json-data/operational_data/craft/satellites/crypto_mining_satellite_data.json` — flat `base_mining_rate_gcc_per_hour`?
- `data/json-data/blueprints/crafts/space/satellites/generic_satellite_bp.json` — compatible_units whitelist?
- Any Ruby file defining `mine_gcc` method

### Priority 2: Continue Economy Wiki Work
The wiki-sync-and-cleanup task (`backlog/economy/`) is dispatch-ready. It handles physical file migration of economic docs into the canonical wiki tree.

### Priority 3: Review GCC Task Draft (if time)
Read the GCC task draft below and confirm:
- Scope is correct (satellite only, not settlement computing)
- Gotchas are complete
- Acceptance criteria are testable

---

## What NOT to Do Today

- Don't dispatch the GCC task yourself — leave it for Tracy review
- Don't build settlement-side computing → GCC linkage — that's a Gemini follow-up
- Don't touch `GameSimulationJob` or `advance_by_days` — those are out of scope for the GCC task

---

## Quick Reference — Key File Paths

| File | Path |
|---|---|
| GCC mining rake | `galaxy_game/lib/tasks/gcc_mining_sat.rake` |
| Integration test | `galaxy_game/spec/integration/game_loop_integration_spec.rb` |
| GameSimulationJob | `galaxy_game/app/jobs/game_simulation_job.rb` |
| Game model (advance_by_days) | `galaxy_game/app/models/game.rb` |
| GameSimulation service | `galaxy_game/app/services/game_simulation.rb` |
| Crypto mining sat data | `data/json-data/operational_data/craft/satellites/crypto_mining_satellite_data.json` |
| Generic satellite blueprint | `data/json-data/blueprints/crafts/space/satellites/generic_satellite_bp.json` |
| Server farm blueprint | `data/json-data/blueprints/units/computing/server_farm_bp.json` |
| Economy wiki | `docs/new_agent/projects/galaxy_game/economy/` |
