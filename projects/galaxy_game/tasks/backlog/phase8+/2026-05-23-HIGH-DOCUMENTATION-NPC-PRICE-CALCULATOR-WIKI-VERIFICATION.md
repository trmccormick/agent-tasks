---
status: backlog
priority: HIGH
type: documentation
system_domain: MANUFACTURING
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: false
---

# TASK: Verify NpcPriceCalculator Source and Resolve Wiki Flags
**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-05-23
**Last Updated**: 2026-05-23

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — documentation task, no RSpec execution required
- **MVP Alignment**: VALID — NpcPriceCalculator drives all NPC market pricing; wiki accuracy blocks AI Manager market integration design
- **MVP Impact Note**: Accurate pricing documentation is required before AI Manager ↔ Market integration task can be specified
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: Claude Sonnet 1x
**Why This Agent**: Requires cross-file reasoning between source code and existing wiki page to resolve [VERIFY AGAINST SOURCE] flags accurately
**Supervision Level**: standard

---

## Context

`Market::NpcPriceCalculator` is the price oracle for all NPC buy/sell interactions in the market layer. It is called by `Market::Marketplace#find_matching_orders`, `Market::Marketplace.get_price`, and `Market::Order#price_per_unit`. The source file has not been reviewed during the Phase 3 documentation sprint.

`docs/wiki/Market-and-AI-Bootstrapping.md` and `docs/wiki/Resource-and-Market-Logistics.md` both contain multiple `[VERIFY AGAINST SOURCE]` flags covering the pricing formula, local facility detection logic, scarcity scaling, and the 0.80 warehouse cap. These flags must be resolved before either wiki page is treated as authoritative.

**Relevant Architecture Docs** — read before starting:
- `docs/GUARDRAILS.md` — market and GCC integrity rules
- `docs/wiki/Market-and-AI-Bootstrapping.md` — current market wiki with unresolved flags
- `docs/wiki/Resource-and-Market-Logistics.md` — pricing rules wiki with unresolved flags

---

## Problem Statement

`Market::NpcPriceCalculator` source has not been reviewed. The following behaviours are documented from design intent only and must be confirmed or corrected against actual source:

**Current behavior**: Wiki pages contain `[VERIFY AGAINST SOURCE]` placeholder flags for all NpcPriceCalculator mechanics.

**Expected behavior**: All flags resolved with confirmed field names, method signatures, and formula logic from actual source.

Specific flags to resolve:

- Does `calculate_ask` use `INITIAL_TRANSPORTATION_COST_PER_KG = 1320.00` as the Earth import ceiling?
- Does local facility detection exist? Is `water_mining` the actual facility key used?
- Does a scarcity/abundance scaling model exist? What is the formula?
- Does `calculate_bid` return `0` when inventory exceeds 0.80 of warehouse capacity? What is the actual constant name?
- What are the full method signatures for `calculate_ask` and `calculate_bid`?

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `docs/wiki/Market-and-AI-Bootstrapping.md` | Market architecture wiki | NpcPriceCalculator section |
| `docs/wiki/Resource-and-Market-Logistics.md` | Pricing rules wiki | Sections 2, 3, 4 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/market/npc_price_calculator.rb` | Primary source to verify against |
| `app/models/market/marketplace.rb` | Shows how calculator is called |
| `app/models/market/order.rb` | Shows price_per_unit delegation |
| `config/initializers/game_constants.rb` | Confirms constant values |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Read NpcPriceCalculator source in full
Read `app/services/market/npc_price_calculator.rb` completely. Record:
- Full method signatures for `calculate_ask` and `calculate_bid`
- The pricing formula for each path (Earth import vs local production)
- Whether local facility detection exists and what the facility key strings are
- Whether a scarcity/abundance scaling model exists and what the formula is
- Whether a warehouse cap check exists, what the threshold value is, and what constant name it uses

### Step 2 — Resolve flags in `Market-and-AI-Bootstrapping.md`
Replace every `[VERIFY AGAINST SOURCE]` flag in `docs/wiki/Market-and-AI-Bootstrapping.md` with confirmed values from Step 1. If a behaviour does not exist in source, replace the flag with `[NOT IMPLEMENTED — confirmed absent as of Phase 3]`.

### Step 3 — Resolve flags in `Resource-and-Market-Logistics.md`
Repeat for `docs/wiki/Resource-and-Market-Logistics.md` Sections 2, 3, and 4. Update the pricing formula structure block in Section 2 with the actual confirmed formula. Update the warehouse cap calculation in Section 3 with the confirmed constant name. Update the local facility mapping table in Section 4 with confirmed facility key strings.

### Step 4 — Flag any new gaps discovered
If the source reveals behaviours not currently documented in either wiki page, add them. If the source reveals that documented behaviours are wrong (not just unverified), correct them and note the correction in the completion report.

---

## Acceptance Criteria
- [ ] `app/services/market/npc_price_calculator.rb` read in full
- [ ] Zero `[VERIFY AGAINST SOURCE]` flags remaining in `Market-and-AI-Bootstrapping.md`
- [ ] Zero `[VERIFY AGAINST SOURCE]` flags remaining in `Resource-and-Market-Logistics.md`
- [ ] All confirmed values sourced from actual file — no design intent substituted
- [ ] Any behaviour absent from source marked `[NOT IMPLEMENTED]` not removed silently
- [ ] No new wiki pages created — only the two listed files updated

---

## Stop Conditions — escalate to user immediately if:
- Source file does not exist at expected path
- Calculator logic is significantly more complex than documented — flag for architecture review before updating wiki
- Source reveals the 0.80 warehouse cap or Earth import ceiling is not implemented — this is a design gap requiring human decision before documenting

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add docs/wiki/Market-and-AI-Bootstrapping.md docs/wiki/Resource-and-Market-Logistics.md
git commit -m "documentation: resolve NpcPriceCalculator [VERIFY AGAINST SOURCE] flags"
git push
```

---

## Documentation
- [ ] Update `docs/wiki/Market-and-AI-Bootstrapping.md` — resolve all source flags
- [ ] Update `docs/wiki/Resource-and-Market-Logistics.md` — resolve all source flags

---

## Dependencies
**Blocked by**: none — requires only source file access
**Blocks**: AI Manager ↔ Market integration task (cannot be specified until pricing mechanics confirmed)
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: N/A — documentation task

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | NpcPriceCalculator flags resolved | next: AI Manager market integration task
