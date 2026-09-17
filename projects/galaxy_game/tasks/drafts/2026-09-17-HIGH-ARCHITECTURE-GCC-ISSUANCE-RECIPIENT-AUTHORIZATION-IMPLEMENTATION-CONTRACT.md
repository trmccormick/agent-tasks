---
title: "GCC Issuance-Recipient + Authorization Implementation Contract"
status: draft
priority: high
created: 2026-09-17
task_type: architecture-decision-producing-implementation-contract
---

# GCC Issuance-Recipient + Authorization Implementation Contract

**Task Type**: Architecture decision producing an implementation contract (not documentation-only)  
**Priority**: HIGH  
**Status**: DRAFT — not dispatched  
**Created**: 2026-09-17  

---

## Task Readiness Checklist

- [x] YAML frontmatter present with status: draft
- [x] Agent Dispatch Interface code-block wrapped
- [x] All file paths absolute or confirmed relative to galaxy_game/
- [x] Prerequisites verified against current codebase state
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
  step_1: |
    Synthesis report — read-only analysis of existing draft + primary-source evidence
  step_2: |
    Draft implementation contract with all 7 decision items resolved
  step_3: |
    Return handoff report with commit-ready artifacts
synthesis_report_location: "summaries/2026-09-17-GCC-ISSUANCE-RECIPIENT-AUTHORIZATION-IMPLEMENTATION-CONTRACT.md"
acceptance_criteria: |
  - All 7 decision items resolved with explicit answers (not open questions)
  - Implementation contract includes exact file paths, method signatures, decision rationale
  - Reconciliation with existing 09-15 draft documented (retained/revised/superseded)
  - Classification of confirmed evidence vs inference vs unverified clearly separated
  - Downstream implementation consequences mapped
stop_conditions: |
  - Do NOT modify any code, data, configuration, or tests
  - Do NOT select a recipient without explicit human approval
  - Do NOT implement any containment strategy
  - Do NOT dispatch this task to an executor until Tracy approves the draft
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

### Primary-Source Evidence (Confirmed)
| Source | Path | Finding |
|---|---|---|
| Production loop entry | `galaxy_game/app/jobs/game_simulation_job.rb:7-50` | GameSimulationJob → Game#advance_by_days |
| Settlement/craft processing | `galaxy_game/app/models/game.rb:52-54,82-89` | process_settlements + process_free_crafts (craft.process_tick) |
| Dual-deposit source | `galaxy_game/app/models/craft/satellite/base_satellite.rb:304,315` | mine_gcc → satellite.account; owner_gcc_account.deposit → owner.account |
| Mining calculation | `galaxy_game/app/models/concerns/cryptocurrency_mining.rb:62` | account.deposit(total_mined, "GCC Mining Operation") via with_lock |
| Scheduler job (broken) | `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb:1-45` | Self-schedules MineGccJob; fails before mutation (Integer#mine_gcc) |
| Dead rate calculation | `galaxy_game/app/models/craft/base_craft.rb:372-395` | recalculate_stats → current_mining_rate_gcc_per_hour (no confirmed callers) |

---

## Architecture Gotchas

### Critical Contradiction to Preserve
The existing 09-15 draft has a narrative/data inconsistency: its prose says "canonical recipient = existing LDC GCC account" but its own Human Decision Gates leave open every mechanism needed to implement that routing. This is not a bug to silently fix — it's evidence that the LDC routing was never fully specified. The new task must explicitly resolve this gap, not inherit it.

### Dual-Deposit Is Confirmed Live
The two-deposit pattern in BaseSatellite#process_tick (satellite.account + owner_gcc_account) executes in production when GameState.running is true and a power-positive satellite exists. This is NOT an artifact of testing or speculation — it's the confirmed runtime behavior. Any recipient policy must account for whether this dual-deposit is ever intentional.

### Scheduler Activation Is Unverified
The SatelliteMiningSchedulerJob has self-scheduling code but no confirmed cron/boot trigger, production execution evidence, or scheduler history. Do not treat it as "running" or "not running" — treat it as "candidate pending activation evidence."

### process_units Is NOT a Satellite Trigger
`process_units` is retained only for read-only historical-assumption reconciliation. No current caller of `process_units` has been identified in production code. Do not treat it as an equal unresolved trigger candidate.

---

## Investigation Steps

### Step 0 — Prerequisite Reading (MANDATORY)
Read all files listed in Agent Dispatch Interface before any analysis. Confirm each file exists and matches the findings above.

### Step 1 — Existing Draft Analysis (Synthesis Report)
Analyze `2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md`:
- Map each of its proposed decisions against the primary-source evidence from the containment plan
- Identify where it is consistent with confirmed evidence, where it relies on inference, and where it contradicts evidence
- Document the narrative/data inconsistency (LDC routing prose vs. unresolved Human Decision Gates)
- Produce a synthesis report classifying each section as: confirmed / inferred / contradicted / underspecified

### Step 2 — Decision Item Resolution
Resolve all seven decision items with explicit answers. For each item, provide:
- **Answer**: The specific resolution (not an open question)
- **Evidence**: Which primary-source file(s) support this answer
- **Rationale**: Why this answer is correct given the game design context
- **Downstream consequences**: What changes in downstream tasks (Task 2 duplicate credit, Task 4 cadence/rate, etc.)

The seven decision items:

1. **Canonical recipient for satellite-mining issuance** — Which account receives GCC from mining: `self.account` (satellite), `owner_gcc_account`, LDC, or conditional routing?
2. **Dual-deposit intent** — Is the two-deposit pattern in `BaseSatellite#process_tick` ever intentional? If not, establish an explicit one-event/one-credit invariant.
3. **Scope of recipient policy** — Does the recipient rule apply to satellite-mined GCC only, or to all newly-mined GCC across all craft types and paths?
4. **LDC role** — Is LDC the canonical issuer (recipient), directional intent only, or conditional policy based on authorization?
5. **Canonical account-resolution mechanism** — If LDC is selected, how is the LDC account resolved at runtime?
6. **Issuer/authorization enforcement boundary** — Where in the call chain is issuance authorized? Before `mine_gcc`, inside `Account#deposit`, or at a new service layer?
7. **Consequences for existing paths** — What happens to: (a) existing `self.account` and `owner_account` deposits, (b) the broken `MineGccJob` path, (c) later rate/cadence work that depends on mining semantics?

### Step 3 — Implementation Contract Drafting
Produce a one-page implementation contract with:
- Exact file paths for every code change location
- Method signatures for every new/modified method
- Decision rationale for each of the seven items
- Clear separation of: confirmed evidence / inference / unverified assumptions / required human decisions
- Downstream implementation consequences mapped (Task 2, Task 4, scheduler containment)

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

---

## Acceptance Criteria

- [ ] All 7 decision items resolved with explicit answers (not open questions)
- [ ] Implementation contract includes exact file paths and method signatures
- [ ] Reconciliation with existing 09-15 draft documented (retained/revised/superseded/hybrid)
- [ ] Classification of confirmed evidence vs inference vs unverified clearly separated
- [ ] Downstream implementation consequences mapped for Task 2, Task 4, scheduler containment
- [ ] No code, data, configuration, or tests modified
- [ ] Synthesis report and implementation contract committed to agent-tasks repo

---

## Stop Conditions

- Do NOT modify any code, data, configuration, or tests in galaxy_game
- Do NOT select a recipient without explicit human approval
- Do NOT implement any containment strategy (Options A/B/C)
- Do NOT dispatch this task to an executor until Tracy approves the draft
- Do NOT silently resolve the LDC narrative/data inconsistency — document it explicitly
- Do NOT treat process_units as an established secondary issuance trigger

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
- **Architecture review**: Claude — if any decision item requires architectural judgment beyond evidence

---

## Handoff Summary

This task produces a binding implementation contract for GCC mining issuance recipient and authorization — not documentation-only. It reconciles with the existing 09-15 draft, resolves all seven human decision items, and clearly separates confirmed evidence from inference from unverified assumptions. No code changes are made in this task; it is planning-only until Tracy approves the draft.
