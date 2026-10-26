# Perplexity Summary — 2026-09-17 GCC Issuance + HLT/Launch-Window Evidence Session

## Purpose
Summary of evidence checks performed on 2026-09-17 for two parallel workstreams. Planning-only (no code changes). All findings cited with file:line references.

---

## Workstream 1: GCC Issuance-Recipient + Authorization Contract

### Task
Review and synthesize the draft implementation contract (`2026-09-17-HIGH-ARCHITECTURE-GCC-ISSUANCE-RECIPIENT-AUTHORIZATION-IMPLEMENTATION-CONTRACT.md`) — Steps 0/1 only.

### Key Findings

**Citation Discrepancies Resolved:**
- `base_satellite.rb:304` and `:311` — two mine_gcc calls in different power-balance branches (earlier reports cited 306/311 or 304/315 — all wrong)
- `base_satellite.rb:326` — owner_gcc_account.deposit call (earlier report cited 321 — wrong)
- `cryptocurrency_mining.rb:55` — account.deposit(total_mined, "GCC Mining Operation") (earlier reports cited 62 or ~78 — both wrong)
- **Pattern**: Third distinct set of line numbers across three passes. Code has been edited multiple times without tracking. Treat any single citation as provisional.

**GameState.running Gate: CONFIRMED**
- `game_simulation_job.rb:13` — `return unless game_state.running` is a hard gate on the entire GameSimulationJob
- GameState.running defaults to false (line 20 of game_state.rb)
- Without this gate passing, no process_tick fires and no GCC deposits execute

**Three Independent Deposit Paths Confirmed:**
1. `cryptocurrency_mining.rb:55` — mine_gcc deposits to self.account (always happens when mine_gcc is called)
2. `base_satellite.rb:326` — owner_gcc_account.deposit (live-tick path, stacked on top of #1)
3. **NEW**: `mission_task_runner_service.rb:28-30` — `'mine_gcc'` task type calls satellite.mine_gcc directly (deposits to self.account), then separately deposits same amount to accounts[:ldc]

**Claude's Additional Analysis:**
- The third deposit path already uses LDC as an *additional* credit stacked on top of self.account, not a replacement for it
- mine_gcc should be renamed to `mine_currency(currency:)` for generic crypto-asset mining setup
- **Critical**: Venus skimmer is owned/operated by AstroLift (not player/LDC). Arrival should trigger docking + market activity (list gases for sale, fill buy orders, buy fuel via sell orders), not direct resource crediting. The JSON offload_requirements and can_offload_n2? pattern may be the *wrong* mechanism entirely — a direct-transfer stand-in for what should be market-order creation through NpcPriceCalculator/EscalationService/ResourceAcquisitionService
- can_offload_n2? should be generalized to `can_offload_resource?(resource_type:, ...)` to cover CO2, CH4, and future gases

**7 Decision Items — Status:**
1. Canonical recipient = LDC: CONFIRMED by evidence
2. Dual-deposit not intentional: CONFIRMED (three-path bug pattern)
3. Scope = all craft types: NEEDS grep verification (uncited assertion)
4. LDC as canonical issuer: PENDING HUMAN DECISION
5. Generic currency→issuer mapping: CONFIRMED as architecture direction
6. Enforce at Financial::Account#deposit: NEEDS Financial::Account existence verification
7. Consequences for existing paths: Items (a)(b) sound; (c) needs preflight

**Loose Threads Before Step 3:**
- Verify Financial::Account exists as deposit target
- Grep to confirm mine_gcc included in multiple craft types
- Clarify AstroLift market-order vs direct-offload design intent

---

## Workstream 2: HLT / Launch-Window Evidence Checks

### Q1: Does HLT override process_tick? Where does resource crediting happen?
**Answer:** HeavyLander does NOT override process_tick. It doesn't participate in GCC mining at all. Resource crediting happens in three paths (see Workstream 1 above).

### Q2: Does Venus skimmer arrival check Luna infrastructure readiness?
**Answer:** No. `has_arrived?` (`transit_engine.rb:148-150`) is a pure elapsed-time check: `sim_day >= transit_record[:transit_days]`. The `can_offload_n2?` method exists at `transit_engine.rb:163-178` and validates correctly but is never called from has_arrived? — it's an orphaned safety check.

### Q3: Does TransitEngine model delta-v vs departure-date cost curve?
**Answer:** No. It computes delta-v for one date at a time via `calculate_transfer_window(from_body, to_body, launch_date)`. No iteration over multiple dates exists. Both mission profiles (`precursor_mission_profile_v1.json` and `task_venus_harvest_arrival_v2.json`) hardcode transit_days values (7 and 400 respectively) with zero departure_date field or dynamic sourcing from TransitEngine.

### Q3b: Off-optimal departure fuel cost?
**Answer:** Not calculated anywhere. Fixed transit_days values appear based on optimal windows with no mechanism to adjust fuel costs for suboptimal departures.

---

## Deliverables Produced This Session

1. **Evidence/synthesis report** (this document) — covering both workstreams
2. **Session handoff** at `projects/galaxy_game/handoffs/qwen(planning agent)/2026-09-17-GCC-ISSUANCE-HLT-EVIDENCE-SESSION-HANDOFF.md`
3. **Checkpoint respected** — stopped before Step 3 (implementation contract drafting) as instructed

## What's Pending

- Workstream 1 Step 2: Full evidence-checked recommendations for all 7 decision items + mine_currency generalization
- Workstream 1 Step 3: Implementation contract draft — awaiting Tracy's approval
- Financial::Account existence verification
- AstroLift market-order vs direct-offload design clarification
- Grep confirmation of mine_gcc multi-craft-type scope
