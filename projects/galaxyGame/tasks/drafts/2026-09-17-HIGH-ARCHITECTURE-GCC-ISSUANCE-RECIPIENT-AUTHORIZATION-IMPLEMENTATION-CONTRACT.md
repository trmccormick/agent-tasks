---
title: "GCC Issuance-Recipient + Authorization Implementation Contract"
status: draft
priority: high
created: 2026-09-17
last_updated: 2026-09-17
task_type: architecture-decision-producing-implementation-contract
---

# GCC Issuance-Recipient + Authorization Implementation Contract

**Task Type**: Architecture decision producing an implementation contract (not documentation-only)
**Priority**: HIGH
**Status**: DRAFT — not dispatched
**Created**: 2026-09-17
**Last Updated**: 2026-09-17 (Claude review pass — citation flags, unverified-claim softening, multi-currency design note, recommended answers added)

---

## Task Readiness Checklist

- [x] YAML frontmatter present with status: draft
- [x] Agent Dispatch Interface code-block wrapped
- [x] All file paths absolute or confirmed relative to galaxy_game/
- [ ] Prerequisites verified against current codebase state — **two citation discrepancies flagged below, need re-verification before Step 3 is finalized**
- [x] Architecture gotchas tied to primary-source evidence
- [x] Implementation steps bounded and specific
- [x] Acceptance criteria concrete and testable
- [x] Stop conditions defined
- [x] No [FILL IN] markers remaining

---

## Agent Dispatch Interface (Required)

```yaml
task_file: "2026-09-17-HIGH-ARCHITECTURE-GCC-ISSUANCE-RECIPIENT-AUTHORIZATION-IMPLEMENTATION-CONTRACT.md"
agent_assignment: "Qwen (planning agent)"
dispatch_sequence:
  step_0: |
    Read these files BEFORE any planning:
    - /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-16-ARCHITECTURE-GCC-MINING-SCHEDULER-CONTAINMENT-PLAN.md (parent containment plan)
    - /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/drafts/2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md (existing draft to reconcile)
    - /Users/tam0013/Documents/git/galaxy_game/app/models/craft/satellite/base_satellite.rb (dual-deposit source)
    - /Users/tam0013/Documents/git/galaxy_game/app/models/concerns/cryptocurrency_mining.rb (mine_gcc source)
    - /Users/tam0013/Documents/git/galaxy_game/app/jobs/game_simulation_job.rb (production loop entry)
    - /Users/tam0013/Documents/git/galaxy_game/app/models/game.rb (advance_by_days, process_free_crafts)
    ALSO re-verify the two citation discrepancies flagged in "Prerequisites" below before treating either line
    number as final — do not silently pick one over the other.
  step_1: |
    Synthesis report — read-only analysis of existing draft + primary-source evidence.
    Also confirm or refute whether a `GameState.running` gate (or equivalent) actually exists on the
    process_tick call path — this claim appears in Architecture Gotchas below but has not been independently cited.
  step_2: |
    Draft implementation contract with recommended answers for all 7 decision items (Claude's recommendations
    are provided below as a starting point — confirm, revise, or challenge each with evidence). These are
    RECOMMENDATIONS awaiting Tracy's explicit approval, not settled decisions to implement.
  step_3: |
    Return handoff report with commit-ready artifacts
synthesis_report_location: "summaries/2026-09-17-GCC-ISSUANCE-RECIPIENT-AUTHORIZATION-IMPLEMENTATION-CONTRACT.md"
acceptance_criteria: |
  - All 7 decision items have explicit recommended answers (not open questions), each clearly marked as
    pending Tracy's approval, not already authorized
  - Implementation contract includes exact file paths, method signatures, decision rationale
  - Reconciliation with existing 09-15 draft documented (retained/revised/superseded)
  - Classification of confirmed evidence vs inference vs unverified clearly separated
  - Downstream implementation consequences mapped
  - The two flagged citation discrepancies are resolved with a single confirmed line number each
  - The multi-currency design intent (GCC/LDC as first, not only, currency/issuer pairing) is stated explicitly
    in the contract, not left implicit
stop_conditions: |
  - Do NOT modify any code, data, configuration, or tests
  - Do NOT select a recipient without explicit human approval
  - Do NOT implement any containment strategy
  - Do NOT dispatch this task to an executor until Tracy approves the draft
  - Do NOT treat the recommended answers below as pre-approved — they require Tracy's sign-off like any other
    decision item
```

---

## Prerequisites (Verify Before Starting)

### Parent Containment Plan
- **File**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-16-ARCHITECTURE-GCC-MINING-SCHEDULER-CONTAINMENT-PLAN.md`
- **Status**: Approved (commit `ce5b597`) — complete for evidence/option mapping, implementation blocked
- **Key constraint**: No containment option approved or recommended; dual-credit via process_tick confirmed live

### Existing Draft to Reconcile
- **File**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/drafts/2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md`
- **Status**: draft — must determine whether it is retained/revised, used as source material, or formally superseded
- **Known issue**: Internally inconsistent — narrative says "canonical recipient = existing LDC GCC account" but Human Decision Gates leave open per-path recipient, satellite mining scope, existing satellite/owner deposit supersession, LDC authorization expression, and LDC account-resolution mechanism

### Primary-Source Evidence (Confirmed) — ⚠️ two citation discrepancies flagged, not yet reconciled
| Source | Path | Finding |
|---|---|---|
| Production loop entry | `galaxy_game/app/jobs/game_simulation_job.rb:7-50` | GameSimulationJob → Game#advance_by_days |
| Settlement/craft processing | `galaxy_game/app/models/game.rb:52-54,82-89` | process_settlements + process_free_crafts (craft.process_tick) |
| Dual-deposit source | `galaxy_game/app/models/craft/satellite/base_satellite.rb:304,315` | mine_gcc → satellite.account; owner_gcc_account.deposit → owner.account — **⚠️ discrepancy: an earlier evidence report (2026-09-16) cited lines 306/311 for the mine_gcc calls and line 321 for the owner deposit. These do not match. Either the code changed between reports or one citation is wrong — re-verify before Step 3 finalizes the implementation contract's file:line references.** |
| Mining calculation | `galaxy_game/app/models/concerns/cryptocurrency_mining.rb:62` | account.deposit(total_mined, "GCC Mining Operation") via with_lock — **⚠️ discrepancy: an earlier report cited this deposit at "approximately line ~78." Re-verify the exact line before treating either as final.** |
| Scheduler job (broken) | `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb:1-45` | Self-schedules MineGccJob; fails before mutation (Integer#mine_gcc) |
| Dead rate calculation | `galaxy_game/app/models/craft/base_craft.rb:372-395` | recalculate_stats → current_mining_rate_gcc_per_hour (no confirmed callers) |

---

## Architecture Gotchas

### GCC/LDC Is the First Currency/Issuer Pairing, Not the Only One
`CryptocurrencyMining`/`mine_gcc` was architected to allow minting currencies other than GCC. GCC was created for the first settlement run, authorized to the LDC — that was never meant to be a permanent one-to-one binding. GCC itself behaves more like a fiat currency than a fixed-supply/commodity-backed one (consistent with the "Bitcoin is lore analogy only" stance and the 1:1 USD:GCC peg as an accounting standard, not a market rate). Any recipient/authorization mechanism built in this task must be generic over currency, with GCC→LDC as its first populated case — not special-cased in a way that has to be unwound the moment a second currency exists.

### Critical Contradiction to Preserve
The existing 09-15 draft has a narrative/data inconsistency: its prose says "canonical recipient = existing LDC GCC account" but its own Human Decision Gates leave open every mechanism needed to implement that routing. This is not a bug to silently fix — it's evidence that the LDC routing was never fully specified. The new task must explicitly resolve this gap, not inherit it.

### Dual-Deposit Is Confirmed Live — one claim inside this still needs its own citation
The two-deposit pattern in BaseSatellite#process_tick (satellite.account + owner_gcc_account) is confirmed to execute given a power-positive satellite reaching `process_tick` via the `GameSimulationJob → Game#advance_by_days → process_free_crafts` chain. **The additional claim that this requires `GameState.running` to be true has not been independently cited anywhere in the evidence gathered so far — treat this as an assumption, not confirmed evidence, until Step 1's synthesis either finds the actual gate check or removes the claim.** Any recipient policy must account for whether this dual-deposit is ever intentional.

### Scheduler Activation Is Unverified
The SatelliteMiningSchedulerJob has self-scheduling code but no confirmed cron/boot trigger, production execution evidence, or scheduler history. Do not treat it as "running" or "not running" — treat it as "candidate pending activation evidence."

### process_units Is NOT a Satellite Trigger
`process_units` is retained only for read-only historical-assumption reconciliation. No current caller of `process_units` has been identified in production code. Do not treat it as an equal unresolved trigger candidate.

---

## Investigation Steps

### Step 0 — Prerequisite Reading (MANDATORY)
Read all files listed in Agent Dispatch Interface before any analysis. Confirm each file exists and matches the findings above — including resolving the two flagged citation discrepancies to a single confirmed line number each, and confirming or refuting the `GameState.running` gate claim.

### Step 1 — Existing Draft Analysis (Synthesis Report)
Analyze `2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md`:
- Map each of its proposed decisions against the primary-source evidence from the containment plan
- Identify where it is consistent with confirmed evidence, where it relies on inference, and where it contradicts evidence
- Document the narrative/data inconsistency (LDC routing prose vs. unresolved Human Decision Gates)
- Produce a synthesis report classifying each section as: confirmed / inferred / contradicted / underspecified

### Step 2 — Decision Item Resolution
Resolve all seven decision items with explicit **recommended** answers — each a starting point for Tracy's approval, not a settled decision to implement. For each item, provide:
- **Recommended answer**: The specific resolution proposed
- **Evidence**: Which primary-source file(s) support this answer
- **Rationale**: Why this answer is correct given the game design context
- **Downstream consequences**: What changes in downstream tasks (Task 2 duplicate credit, Task 4 cadence/rate, etc.)

Claude's recommended answers, provided as a starting point (confirm, revise, or challenge each with evidence — do not treat these as pre-approved):

1. **Canonical recipient for satellite-mining issuance** — **Recommended: LDC**, not `self.account` or `owner_gcc_account`. Issuance is already LDC-controlled by design; capacity alone is never mint authority. Neither current deposit destination is correct — this isn't a choice between the two existing ones.
2. **Dual-deposit intent** — **Recommended: not intentional.** Establish a hard one-event/one-credit invariant.
3. **Scope of recipient policy** — **Recommended: applies to all newly-mined GCC across all craft types**, and the mechanism should be built currency-agnostic from the start (see the multi-currency gotcha above) — GCC/LDC is the first populated case, not the only one the design supports. State this explicitly in the contract.
4. **LDC role** — **Recommended: canonical issuer**, binding now, not merely directional intent.
5. **Canonical account-resolution mechanism** — **Recommended: a generic currency → authorized-issuer mapping** (not a hardcoded "find the LDC account" lookup), with GCC → LDC as its first entry, reusing `MissionTaskRunnerService`'s `accounts[:ldc]` pattern as the concrete implementation reference for that one entry.
6. **Issuer/authorization enforcement boundary** — **Recommended: enforce at `Financial::Account#deposit`** (or a thin wrapper immediately around it) — the actual point where money is created regardless of caller. The guard's logic should check "is this issuer authorized to mint this specific currency" against the item-5 mapping, not a blanket "must be LDC" check, so it doesn't need to be torn out when a second currency arrives.
7. **Consequences for existing paths** — **Recommended:** (a) existing `self.account`/`owner_account` deposits get redirected to LDC as part of the downstream duplicate-credit fix (Task 2); (b) `MineGccJob` stays untouched — its containment decision is separate and comes later; (c) cadence/rate work (Task 4) proceeds only after an independent preflight confirms zero interaction with recipient/mint semantics.

### Step 3 — Implementation Contract Drafting
Produce a one-page implementation contract with:
- Exact file paths for every code change location (with the two flagged citation discrepancies resolved)
- Method signatures for every new/modified method
- Decision rationale for each of the seven items, building on (or overriding, with evidence) the recommended answers above
- Clear separation of: confirmed evidence / inference / unverified assumptions / required human decisions
- Downstream implementation consequences mapped (Task 2, Task 4, scheduler containment)
- Explicit statement of the multi-currency design intent (GCC/LDC as first, not only, pairing)

### Step 4 — Reconciliation Report
Document how this task relates to the existing 09-15 draft:
- **Retained**: The existing draft is substantially correct and needs only minor corrections
- **Revised**: The existing draft provides a foundation but needs significant changes
- **Superseded**: The existing draft is fundamentally incompatible; this task replaces it entirely
- **Hybrid**: Specific sections retained/revised/superseded (document per-section)

### Step 5 — Handoff Report
Return:
- Commit-ready artifacts (implementation contract + reconciliation report)
- Classification of each decision item as confirmed/inferred/unverified/needs-human-decision
- Any ambiguity or conflict that prevents the task from being decision-ready
- Confirmation that no code, data, configuration, tests, or runtime behavior was modified
- Confirmation that the two flagged citation discrepancies were resolved, and the `GameState.running` claim was either cited or removed

---

## Acceptance Criteria

- [ ] All 7 decision items have explicit recommended answers, clearly marked as pending Tracy's approval
- [ ] Implementation contract includes exact file paths and method signatures
- [ ] Reconciliation with existing 09-15 draft documented (retained/revised/superseded/hybrid)
- [ ] Classification of confirmed evidence vs inference vs unverified clearly separated
- [ ] Downstream implementation consequences mapped for Task 2, Task 4, scheduler containment
- [ ] No code, data, configuration, or tests modified
- [ ] Synthesis report and implementation contract committed to agent-tasks repo
- [ ] The two flagged citation discrepancies resolved to single confirmed line numbers
- [ ] The `GameState.running` gate claim confirmed with a citation or removed
- [ ] The multi-currency design intent stated explicitly in the contract

---

## Stop Conditions

- Do NOT modify any code, data, configuration, or tests in galaxy_game
- Do NOT select a recipient without explicit human approval
- Do NOT implement any containment strategy (Options A/B/C)
- Do NOT dispatch this task to an executor until Tracy approves the draft
- Do NOT silently resolve the LDC narrative/data inconsistency — document it explicitly
- Do NOT treat process_units as an established secondary issuance trigger
- Do NOT treat Claude's recommended answers above as pre-approved — they need Tracy's sign-off like any other decision item
- Do NOT silently pick one of the two conflicting citations without flagging which was chosen and why

---

## Dependencies

| Dependency | Status | Notes |
|---|---|---|
| Parent containment plan | Complete (ce5b597) | Evidence-based, no implementation |
| Existing 09-15 draft | Draft — needs reconciliation | Internally inconsistent; must be resolved |
| Human decision on GCC ownership model | BLOCKING | Required before any downstream implementation |
| Task 2 (duplicate credit prevention) | Blocked | Downstream on this task's output |
| Task 4 (cadence/rate alignment) | Conditional | May proceed early only after preflight proves no impact on recipient/mint/rate/cadence |

---

## Exclusions

- No gameplay/economics redesign
- No Mars or player-facing expansion beyond Luna-first NPC simulation scope
- No scheduler repair or activation
- No rate-authority reconciliation (separate task)
- No idempotency implementation (separate from recipient decision)

---

## Required Reviewer/Approver

- **Reviewer**: Planning agent (Qwen) — synthesis report + draft contract
- **Approver**: Tracy (human) — must approve all 7 decision items before any executor work
- **Architecture review**: Claude — provided recommended answers to all 7 items above; final judgment on any contested item still available on request

---

## Handoff Summary

This task produces a binding implementation contract for GCC mining issuance recipient and authorization — not documentation-only. It reconciles with the existing 09-15 draft, resolves all seven human decision items (Claude's recommended starting answers included above), and clearly separates confirmed evidence from inference from unverified assumptions. Two citation discrepancies and one uncited claim from the original draft are flagged for resolution before Step 3 finalizes. No code changes are made in this task; it is planning-only until Tracy approves the draft.
