# Perplexity Handoff — GCC Mining Economic Classification Verification

**Date**: 2026-09-15  
**Agent**: Perplexity  
**Prepared by**: Qwen (Planning Agent)  
**Context**: Continuation of Tracy's GCC mining economic classification session (started 09-14, Claude reviewed 09-15)

---

## What Has Been Completed (Do Not Redo)

### ✅ Economic Classification — Settled
- **GCC is fiat-style virtual ledger currency**, not a material/commodity. LDC is the initial authorized issuer/mint under UN-mandated Development Corporation role.
- **USD = GCC 1:1** is the documented initial peg, described as "bootstrap price calibration" — NOT universal or permanently guaranteed. Phases 2-3 decoupling are exploratory/deferred.
- **Fitted processing hardware establishes potential throughput; LDC authorization governs actual credits.** Satellite capacity does NOT independently authorize currency creation.

### ✅ Source-Trace Evidence — Verified
- `recalculate_stats` (base_craft.rb:372) and `mine_gcc` (cryptocurrency_mining.rb:10) are **two disconnected code paths** — no data flows between them.
- Satellite `base_mining_rate_gcc_per_hour: 1000` is dead/unconsumed design data for mining output.
- Two exchange-rate systems exist but are NOT connected: ExchangeRateService (in-memory, default 1:1) vs ExchangeRate model (PostgreSQL DB, used by bonds only).
- VirtualLedgerService.exchange_rate_to_gcc returns hardcoded 100.0 (test artifact, never cleaned up).

### ✅ Time Model — Verified
- `seconds_per_game_day` at speed=3 = **60 seconds** (1 real minute = 1 game day). Source: `game_state.rb` lines 49-60.
- The "86400/game_speed" formula does NOT exist in the codebase — only in one stale test comment (`game_loop_integration_spec.rb` line 28).
- GameSimulationJob fires every **1 minute** via self-scheduling, not 6 hours as wiki claims.

### ✅ Claude Answers Applied (Do Not Re-Ask)
- Q1-Q3 (append-only ledger, virtual-ledger visibility, NPC deficit thresholds): **Removed as dispatch gates.** Deferred to future ledger-architecture task.
- Q4 (capacity unification): Existing `mine_gcc`/`MiningUnitAdapter` chain is the single canonical calculation.
- Q5 (`base_mining_rate_gcc_per_hour: 1000`): Deprecate/remove.
- Q6 (`0.18` multiplier): Document as per-operation unit-conversion constant, NOT tied to simulation tick interval.

### ✅ Wiki Corrections Committed
- Commit `0e4f67be`: GCC identity, USD=GCC scope, virtual-ledger role, mining terminology, implementation gap callout.
- "Emission" → "issuance" in GAPS.md. Gap I added for recalculate_stats/mine_gcc disconnection.

### ✅ Satellite Battery Compatibility — Resolved (Not a Blocker)
- `recommended_fit` is NPC/testing config; `compatible_units` whitelist is documentation only, not enforced as construction gate.
- Integration tests actively use satellite_battery; runtime logs show charging works.

---

## What Perplexity Needs to Verify

### 1. GCC Economic Classification — Confirm Against Codebase
Verify the settled classification against actual code:
- Does any code path treat GCC as a physical material/commodity (cargo, inventory, extraction output)?
- Is LDC the only entity that can create/issue GCC? Check for any other minting paths (bond creation, NPC earning, etc.).
- Confirm `VirtualLedgerService.exchange_rate_to_gcc` returns 100.0 and is a test artifact.

### 2. Exchange-Rate Infrastructure — Map Both Systems
Trace both exchange-rate systems:
- **ExchangeRateService**: Where is it called? What values does it return? Is the default 1:1 ever changed at runtime?
- **ExchangeRate model (DB)**: Who reads from it? Used by bonds only? Any admin UI that writes to it?
- Are there any code paths that sync between them?

### 3. Mining Rate Calculation — Verify Disconnection
Trace both mining rate paths:
- `recalculate_stats` in base_craft.rb:372 — who calls it? What does it store?
- `mine_gcc` in cryptocurrency_mining.rb — what does it actually read? Does it ever reference `current_mining_rate_gcc_per_hour`?
- MiningUnitAdapter — how does it extract mining rate from fitted units?

### 4. Game Simulation Cadence — Verify Against Code
- Confirm `game_state.rb` seconds_per_game_day values match the case statement (300/120/60/30/10).
- Confirm GameSimulationJob self-schedules at 1-minute intervals.
- Check if any other code references "8 hours" or "86400" for game day calculation.

### 5. Wiki Pages — Identify Remaining Terminology Issues
Review all economy wiki pages (`docs/wiki_reorganization/economy/`) for:
- Any remaining "emission" language (should be "issuance")
- Any "backed by" language implying commodity backing
- Any "GCC Mining Bonds" collateral language that conflates GCC with physical commodity
- Any other terminology that could mislead readers about GCC's fiat nature

---

## Scope Boundaries — What NOT to Do

- **Do NOT** propose code changes or runtime behavior modifications.
- **Do NOT** redesign the exchange-rate architecture or virtual-ledger system.
- **Do NOT** answer Gemini's 7 economic-design questions or Tracy's 4 decisions — those require human review.
- **Do NOT** re-ask Claude's 6 questions — already resolved per Claude's answers above.
- **Do NOT** modify any committed wiki pages without explicit Tracy approval.

---

## Deliverable Expected from Perplexity

A verification report covering:
1. Confirmation or correction of the settled economic classification
2. Map of both exchange-rate systems (callers, values, sync status)
3. Verification of mining rate disconnection with specific file/line references
4. Confirmation of game simulation cadence values
5. List of remaining wiki terminology issues (if any)

**Format**: Structured report with source-trace evidence for each finding. Flag any findings that contradict the settled classification above.

---

## P0 Task Status

**Title**: GCC Mining Satellite — Unified Hardware Capacity and Simulation-Loop Integration  
**Location**: `projects/galaxy_game/tasks/backlog/current/2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-UNIFIED-HARDWARE-CAPACITY-AND-SIMULATION-LOOP-INTEGRATION.md`  
**Status**: HELDED — Requires Gemini review + Tracy approval. Claude answers applied.  
**Not dispatched.** No implementation work should begin until all three reviews complete.

---

## Wiki-Lockdown Plan

A consolidated wiki-lockdown plan exists at: `projects/galaxy_game/summaries/2026-09-15-GCC-ECONOMY-WIKI-LOCKDOWN-PLAN.md`  
**Status**: AWAITING Tracy + Gemini review before applying. Not yet committed to wiki.

---

## Full Session Context

For complete session history, read: `projects/galaxy_game/handoffs/qwen(planning agent)/2026-09-14-SESSION-HANDOFF.md`  
For reconciliation of all artifacts: `projects/galaxy_game/summaries/2026-09-14-GCC-MINING-DOCUMENTATION-TASK-ARTIFACT-RECONCILIATION.md`
