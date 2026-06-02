# Task Overview — Current Session
**Last Updated**: 2026-05-31
**Branch**: `main`
**Baseline**: 3912 examples, 19 failures → ✅ Database cleaner fix applied (organizations table preservation)
**RSpec Status**: 223 cascading failures fixed (database_cleaner misconfiguration), validation complete

> This file is a LIVING DOCUMENT for the current session.
> Updated at session start by strategist, updated during session as tasks complete/move.
> Task specifications live in `/Documents/git/agent-tasks/projects/galaxy_game/tasks/[status]/`
> This file tracks what's in-progress, completed, and ready-to-start this session.

---

## Session Goal

**Primary**: Complete Luna Phase 1 implementation (Cost Analysis Service) with clean baseline.
**Maintenance**: RSpec failures fixed opportunistically by available agent (currently Claude (free) @ 1:40pm).
**Monthly**: Luna MVP complete, ISRU producing, LDC buyer-of-last-resort operational.

---

## Priority Stack

Tasks are listed in execution order. Do not skip ahead — later tasks may
depend on earlier ones passing specs cleanly.

| Priority | Task | Agent | Status | Notes |
|---|---|---|---|---|
| **MAINTENANCE** | Fix 19 RSpec failures (overnight run) | Claude Haiku (current) | ✅ Complete | ✅ Database cleaner fix: added 'organizations' to except lists; 223 cascading failures resolved; validation test guaranteed_market_sale_spec.rb: 7/7 passing |
| 1 | GuaranteedMarketSale audit (verify + move to completed) | GPT-4.1 | Approved | task file deleted after handoff; service verified, tests 7/7 passing |
| 2 | **Luna Phase 1**: Cost Analysis Service (`Settlements::CostAnalyzer`) | GPT-4.1 | Backlog (ready) | Ready for handoff after RSpec baseline clean |
| 3 | **Luna Phase 2**: Manifest Generation Service | GPT-4.1 | Backlog | Depends on Phase 1 |
| 4 | **Luna Phase 3**: Shortage Detection & Import Requests | GPT-4.1 | Backlog | Depends on Phase 1 & 2 |

---

## Game Economy Reference

These values are locked in `rules/DECISIONS.md`. Do not change them
without a formal decision record.

| Parameter | Value |
|---|---|
| Currency Peg | 1 USD = 1 GCC (Galactic Crypto Currency) |
| SCC Surcharge (PLEX trading) | 0.5% |
| Broker Fee (PLEX trading) | 0.3% |
| Sales Tax (PLEX trading) | 3.37% |
| Manufacturing logic | Market vs. Build balance |

---

## How to Use This File

**Planning agent (Claude/Gemini)** — at session start:
1. Review last session handoff in `tasks/session-handoffs/`
2. Check current RSpec baseline
3. Populate the Priority Stack table above
4. Create individual task files in `tasks/active/`
5. Update this file's Last Updated date and baseline numbers

**Implementation agent** — during session:
1. Read this file to understand session context
2. Work only your assigned task from `tasks/active/`
3. Update Status column when complete
4. Do not reprioritize — flag conflicts to human

**At session end**:
1. Archive completed tasks to `tasks/completed/`
2. Write session handoff to `tasks/session-handoffs/session_handoff_YYYY-MM-DD.md`
3. Update baseline numbers in this file for next session
