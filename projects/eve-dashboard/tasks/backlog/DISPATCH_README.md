# Eve Dashboard Task Dispatch Guide

**Purpose**: Guide for Planning Agent to dispatch Phase 2-6 implementation tasks to Qwen (local implementation model) and verify completion via synthesis reports.

**Cost Optimization**: Premium usage at 16% for month — prioritize synthesis report verification over re-running validation steps (read synthesis = cheaper than re-executing).

---

## 🚀 Quick Dispatch Sequence

```
READY NOW → PHASE 2 (OAuth validation)
           ↓ (after synthesis report approved)
READY → PHASE 3 (mining + market data)
           ↓ (after synthesis report approved)
READY → PHASE 3B (price tracking)
READY → PHASE 4 (inventory)
READY → PHASE 4B (logistics)
READY → PHASE 5 (efficiency metrics)
READY → PHASE 6 (supply chain analysis)
```

---

## Phase Dependencies

```
Phase 2 (OAuth)
    ↓
Phase 3 (Mining + Market) ← MUST PASS before Phases 3B-6
    ↓
Phase 3B (Prices) + Phase 4 (Inventory) ← Can parallelize
    ↓
Phase 4B (Logistics)
    ↓
Phase 5 (Efficiency)
    ↓
Phase 6 (Supply Chain)
```

**Critical Blockers:**
- Phase 2 MUST pass before any Phase 3+ work starts
- Phase 3 MUST pass before Phase 3B/4 can proceed
- If Phase 2 FAILS: Stop. Fix OAuth issues. Re-dispatch Phase 2.
- If Phase 3 FAILS: Stop. Fix mining ledger logic. Re-dispatch Phase 3.

---

## Phase 2 Dispatch (OAuth Integration & Functional Testing)

**File**: `2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md`
**Status**: ✅ **READY TO DISPATCH NOW**
**Blocked By**: Nothing (foundation phase)

### Step 1: Send to Qwen

Copy the ENTIRE task file and paste to Qwen with this preamble:

```
You are the Implementation Agent for Eve Dashboard Phase 2.
Read the full task file below.
Follow the Agent Dispatch Interface section exactly.
Save synthesis report to: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-FUNCTIONAL-TEST-SYNTHESIS.md

[PASTE ENTIRE TASK FILE CONTENT HERE]
```

### Step 2: Verify Qwen Completed Step 0

Qwen should output:
```
$ git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md \
       projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md
$ # Changed status: backlog → status: active in moved file
```

**Do NOT proceed** until both commands shown.

### Step 3: Read Synthesis Report

Once Qwen completes, ask them to post synthesis report link. **Cost optimization**: Read the report, don't ask them to re-run tests.

**Check these sections** (Critical — verify all present):

- [ ] OAuth Flow Completion
  - [x] Client ID/Secret correct (037bf01a1d25458099784221ef52a1cd / eat_2IHoK4iZ3FKSiNnBWMG5hN6exSmCufpMw_2JFWAa)
  - [x] All 7 accounts authenticated successfully
  - [x] All 11 characters loaded into database
  - [x] Access tokens stored and refreshing

- [ ] Database Verification
  - [x] `characters` table contains exactly 11 rows
  - [x] `access_tokens` table has entries for all 7 accounts
  - [x] Character names match user's fleet (7 Neon + 4 support alts)

- [ ] ESI API Connectivity
  - [x] Mining ledger endpoint responding (at least 1 character returned data)
  - [x] Wallet journal endpoint responding
  - [x] Assets endpoint responding
  - [x] No rate limit errors encountered

- [ ] Logging Verification
  - [x] `data/logs/dashboard.log` created
  - [x] OAuth flow logged with timestamps
  - [x] No `ERROR` or `EXCEPTION` entries
  - [x] Access token refresh events logged

- [ ] Homefront Backward Compatibility
  - [x] Existing homefront data structure preserved
  - [x] No breaking changes to character table schema

- [ ] WAL Mode Persistence
  - [x] SQLite WAL mode enabled
  - [x] Database persists data correctly across app restarts
  - [x] No data corruption in WAL logs

### Step 4: Decision Point

**If ALL checks pass**:
- ✅ Phase 2 APPROVED
- Move to Phase 3 dispatch
- Store synthesis report as historical record

**If ANY checks fail**:
- ❌ Phase 2 BLOCKED
- Ask Qwen: "What failed? What's the error in logs?"
- Request debug output from `data/logs/dashboard.log`
- Re-dispatch with specific instructions to fix issue

---

## Phase 3 Dispatch (Mining & Market Sales)

**File**: `2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md`
**Status**: READY (depends on Phase 2 PASSED)
**Blocked By**: Phase 2 synthesis report approval

### Prerequisites

- [ ] Phase 2 synthesis report shows ALL checks passed
- [ ] All 11 characters loaded in database
- [ ] ESI API connectivity verified
- [ ] logging working

### Dispatch Process

1. Copy task file to Qwen (same format as Phase 2)
2. Verify Step 0 completion (git mv command)
3. Read synthesis report: `2026-09-04-PHASE3-MINING-MARKET-SYNTHESIS.md`

### Verification Checklist

- [ ] Mining Ledger Data
  - [x] `mining_ledger` table created
  - [x] ESI `/characters/{id}/mining/` queried for all 7 pilots
  - [x] At least 1 ore type returned per pilot
  - [x] Timestamps recorded correctly

- [ ] Market Order Tracking
  - [x] `market_orders` table created
  - [x] Jita market prices retrieved
  - [x] Daily market snapshot stored
  - [x] Price data valid (>0 ISK)

- [ ] Daily ISK Generation Calculation
  - [x] `production_metrics` table created
  - [x] ISK totals calculated per pilot
  - [x] Fleet totals aggregated correctly
  - [x] At least 1 day of data in table

- [ ] Dashboard Display
  - [x] Mining data visible on dashboard
  - [x] Market data visible
  - [x] No missing data for any pilot
  - [x] Page loads without errors

- [ ] Logging & Error Handling
  - [x] No `EXCEPTION` entries in logs for mining/market operations
  - [x] Rate limit handling working (slow queries down if needed)

**Decision**:
- ✅ If ALL pass: Phase 3 APPROVED → Dispatch Phases 3B + 4 (can parallelize)
- ❌ If ANY fail: Phase 3 BLOCKED → Debug and re-dispatch

---

## Phases 3B & 4 Dispatch (PARALLELIZABLE)

After Phase 3 PASSES, dispatch both simultaneously:

### Phase 3B: Price Tracking

**File**: `2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md`
**Verify**: `2026-09-04-PHASE3B-PRICE-TRACKING-SYNTHESIS.md`

**Checklist**:
- [ ] Daily price snapshots stored (1x per 24 hours max)
- [ ] 30-day trend calculated (avg, high, low)
- [ ] Jita region prices used (not aggregated ESI prices)
- [ ] Sell signals generated (price > avg + 5%)
- [ ] No duplicate daily prices

### Phase 4: Inventory Management

**File**: `2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md`
**Verify**: `2026-09-04-PHASE4-INVENTORY-SYNTHESIS.md`

**Checklist**:
- [ ] Inventory table populated with ore/refined materials
- [ ] Fleet totals match aggregation logic
- [ ] ISK values calculated using Phase 3B prices
- [ ] Per-location breakdown accurate
- [ ] Storage capacity alerts working
- [ ] Data updates on sync

**Proceed When**: Both 3B AND 4 PASS → Dispatch Phase 4B

---

## Phase 4B Dispatch (Logistics Optimization)

**File**: `2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md`
**Status**: READY (depends on Phase 4 PASSED)
**Verify**: `2026-09-04-PHASE4B-LOGISTICS-SYNTHESIS.md`

**Checklist**:
- [ ] Haul recommendations generated
- [ ] Priority logic correct (critical/high/medium/low)
- [ ] Minimum batch sizes calculated
- [ ] Hauler assignments optimized
- [ ] Cost estimates reasonable
- [ ] Dashboard displays recommendations

**Proceed When**: Phase 4B PASSES → Dispatch Phase 5

---

## Phase 5 Dispatch (Production Efficiency)

**File**: `2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md`
**Status**: READY (depends on Phase 4B PASSED)
**Verify**: `2026-09-04-PHASE5-EFFICIENCY-SYNTHESIS.md`

**Checklist**:
- [ ] Daily ISK/hour calculated per pilot
- [ ] Fleet totals aggregated correctly
- [ ] Leaderboard ranked by efficiency
- [ ] PLEX countdown calculated
- [ ] 7-day trend visible
- [ ] No exceptions in logs

**Proceed When**: Phase 5 PASSES → Dispatch Phase 6

---

## Phase 6 Dispatch (Supply Chain & Profitability)

**File**: `2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md`
**Status**: READY (depends on Phase 5 PASSED)
**Verify**: `2026-09-04-PHASE6-SUPPLY-CHAIN-SYNTHESIS.md`

**Checklist**:
- [ ] Ore profitability ranking calculated
- [ ] Refining yields accurate (52% efficiency)
- [ ] Fee breakdown correct (Jita standard: 0.5% broker + 1% market)
- [ ] Recommendation logic working
- [ ] Top ore type identified
- [ ] No exceptions in logs

**Proceed When**: Phase 6 PASSES → All implementation complete ✅

---

## Cost Optimization Notes

**Premium Usage at 16% for Month:**

1. **Read Synthesis Reports, Don't Re-Run Tests**
   - Qwen already ran the tests and saved results
   - Reading report = 1-2 tokens
   - Re-running tests = 100+ tokens
   - **Always read synthesis first**

2. **Use Checklists, Not Custom Verification**
   - Each task has acceptance criteria
   - Synthesis report confirms which ones passed
   - Don't ask for additional testing
   - **Trust the synthesis report**

3. **Batch Related Phases**
   - Phases 3B + 4 can run in parallel (no cost for waiting)
   - Don't dispatch them sequentially
   - **Parallelize = less total time**

4. **Keep Planning Sessions Brief**
   - Check synthesis: ✅ Pass / ❌ Fail
   - One-sentence approval or debug request
   - **Don't over-discuss**

---

## Handoff to Next Planning Session

When stopping work, create `PLANNING_SESSION_HANDOFF.md`:

```markdown
# Planning Session Handoff — [DATE]

## Current Status
- Phase 2: ✅ COMPLETED + APPROVED (synthesis: 2026-09-04-FUNCTIONAL-TEST-SYNTHESIS.md)
- Phase 3: ⏳ IN PROGRESS with Qwen (dispatch date: YYYY-MM-DD)
- Phases 3B-6: READY (awaiting Phase 3 completion)

## Next Action
1. Check if Phase 3 synthesis report posted
2. If YES: Read report, verify against Phase 3 checklist
3. If PASS: Dispatch Phases 3B + 4 simultaneously
4. If FAIL: Debug with Qwen, re-dispatch Phase 3

## Key Files
- Task files: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/`
- Synthesis reports: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/`
- Active tasks: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/active/`

## Cost Tracking
- Started at: 16% premium usage
- Check monthly to avoid overage
```

---

## Troubleshooting

### Problem: Qwen Didn't Complete Step 0

**Solution**: Remind them:
```
Your first task is Step 0 from the Agent Dispatch Interface section.
Run these two commands and paste output:
  git mv projects/eve_dashboard/tasks/backlog/[filename] \
         projects/eve_dashboard/tasks/active/[filename]
  (then change status: backlog → status: active in the moved file)
```

### Problem: Synthesis Report Missing

**Solution**: Ask Qwen:
```
Where did you save the synthesis report? 
It should be at: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/[phase]-SYNTHESIS.md
```

### Problem: Acceptance Criteria Failed

**Solution**:
1. Ask Qwen: "Which acceptance criteria failed?"
2. Get error output from `data/logs/dashboard.log`
3. Re-dispatch with specific instructions to fix
4. **Don't ignore failed criteria**

### Problem: Phase Blocked But User Wants to Proceed

**Response**: "We must complete Phase X first. Current blocker is: [reason]. Once Phase X synthesis report passes, Phase Y can proceed."

---

## File Locations Reference

```
Task Files (backlog):
  /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/
  - 2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md
  - 2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md
  - 2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md
  - 2026-09-04-HIGH-FEATURE-PHASE4-INVENTORY-MANAGEMENT.md
  - 2026-09-04-HIGH-FEATURE-PHASE4B-LOGISTICS-OPTIMIZATION.md
  - 2026-09-04-MEDIUM-FEATURE-PHASE5-PRODUCTION-EFFICIENCY.md
  - 2026-09-04-MEDIUM-FEATURE-PHASE6-SUPPLY-CHAIN.md

Active Tasks (moved after Step 0):
  /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/active/

Synthesis Reports (output from Qwen):
  /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/
  - 2026-09-04-FUNCTIONAL-TEST-SYNTHESIS.md
  - 2026-09-04-PHASE3-MINING-MARKET-SYNTHESIS.md
  - 2026-09-04-PHASE3B-PRICE-TRACKING-SYNTHESIS.md
  - 2026-09-04-PHASE4-INVENTORY-SYNTHESIS.md
  - 2026-09-04-PHASE4B-LOGISTICS-SYNTHESIS.md
  - 2026-09-04-PHASE5-EFFICIENCY-SYNTHESIS.md
  - 2026-09-04-PHASE6-SUPPLY-CHAIN-SYNTHESIS.md

Project Context:
  /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md
  /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/status.md

Dashboard Source Code:
  /Users/tam0013/Documents/git/eve-dashboard/
  - app/main.py (FastAPI routes)
  - app/db.py (Database)
  - app/logging_config.py (Logging setup)
  - config/credentials.env (OAuth credentials — user-provided)
```

---

## Summary

1. **Dispatch Phase 2** → Read synthesis → Approve or debug
2. **On Phase 2 PASS**: Dispatch Phase 3
3. **On Phase 3 PASS**: Dispatch Phases 3B + 4 (parallel)
4. **Sequential** 4B → 5 → 6 (each depends on previous)
5. **Stop on FAIL**: Fix issue, re-dispatch
6. **Read syntheses** to save premium tokens (cost optimization)

**You are the Planning Agent. Your job is to dispatch, verify, and coordinate. The Implementation Agent (Qwen) builds the code.**
