---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as their startup contract. Every word matters.

```
You are **Planning Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md \
         projects/galaxy_game/tasks/active/2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md
  Then open the moved file and change: status: backlog → status: active
  Paste the `find` verification output in chat and confirm the YAML status was updated to `active` before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: After Step 0 and prerequisite reading, save the synthesis report as the specified Markdown file in `summaries/` before Step 1 or any substantive planning work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-16-ARCHITECTURE-GCC-MINING-SCHEDULER-CONTAINMENT-PLAN.md
  Use chat only for the concise status and approval request required by the workflow; do not paste the full synthesis report into chat.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **GCC Mining Flow-Map Audit**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/handoffs/qwen(planning agent)/2026-09-15-SESSION-HANDOFF-GCC-MINING-ECONOMIC-POLICY-DRAFTS.md` (contains the complete flow-map evidence)
4. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. After Step 0, save the required
> synthesis report as the specified Markdown file in `summaries/` before Step 1.
> Use chat only for the concise status and approval request required by the
> workflow; do not paste the full synthesis report into chat.

---

# TASK: GCC Mining Scheduler Containment and Issuance-Flow Decision
**Status**: ACTIVE
**Priority**: HIGH
**Type**: architecture (planning-only)
**Created**: 2026-09-16
**Last Updated**: 2026-09-16
**Agent Session**: 2026-09-16 — Step 0 complete, task file moved to active/

---

## Context

GCC mining has a verified tick-capable/direct mining path and a separately self-scheduling job path. The job path is confirmed broken before mutation. Whether `BaseSatellite#process_tick` is invoked by the current production game loop remains to be verified. When the tick-capable path is invoked, it contains two sequential positive credit mutations. No canonical trigger, recipient policy, or idempotency mechanism exists. This task produces a containment plan and decision matrix without implementing any changes.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/projects/galaxy_game/handoffs/qwen(planning agent)/2026-09-15-SESSION-HANDOFF-GCC-MINING-ECONOMIC-POLICY-DRAFTS.md` — complete GCC mining flow-map evidence

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not propose fixing `MineGccJob` receiver as a containment measure.
- ❌ Wrong: "Change MineGccJob to accept satellite_id and resolve it" — this would activate an uncontrolled minting route before recipient policy is settled.
- ✅ Right: Contain by disabling the scheduler or gating it behind a feature flag; fix the receiver only after canonical trigger/recipient are established.
- Why: The job-driven path, if made functional without resolving dual-deposit and idempotency, would add a second uncontrolled GCC issuance route.

⚠️ **GOTCHA 2**: Do not treat `mine_gcc` deposit to satellite account and `process_tick` deposit to owner account as "separate intentional accounting" without verifying account identity and call order.
- ❌ Wrong: "These are separate accounts so dual deposit is fine."
- ✅ Right: Trace call order — `mine_gcc` deposits first (to satellite.account), then `process_tick` deposits the same amount to owner.account. Both execute for every power-positive tick when the tick-capable path is invoked.
- Why: The verified call order creates two positive GCC deposit operations from the same returned `mined_amount` when `process_tick` invokes `mine_gcc`. Treat this as a confirmed dual-credit path and a financial-integrity risk. The final classification—duplicate issuance, intentional internal accounting, or another ledger relationship—requires recipient-policy and transaction-semantics review.

⚠️ **GOTCHA 3**: Do not conflate `recalculate_stats` output with `mine_gcc` rate calculation.
- ❌ Wrong: "Use current_mining_rate_gcc_per_hour as the authoritative rate."
- ✅ Right: Document that these are two disconnected calculations; recommend reconciliation only after canonical trigger is established.
- Why: `recalculate_stats` computes base + computer_boost + rigged_computer_boost; `mine_gcc` independently extracts base_rate via MiningUnitAdapter. `MiningUnitAdapter` is the current calculation path used by `mine_gcc`; its formula correctness is outside this planning task unless a direct contradiction is found.

---

## Problem Statement

GCC mining has a verified tick-capable/direct mining path and a separately self-scheduling job path with overlapping financial side effects. The job path is confirmed broken before mutation. Whether `BaseSatellite#process_tick` is invoked by the current production game loop remains to be verified. When the tick-capable path is invoked, it contains two sequential positive credit mutations. No canonical trigger, recipient policy, or idempotency mechanism exists.

1. **Tick-driven path**: `BaseSatellite#process_tick` → `mine_gcc` (concern) → deposits to satellite account, then deposits same amount to owner account. Dual-credit confirmed when invoked.
2. **Job-driven path**: `SatelliteMiningSchedulerJob` → `MineGccJob(satellite.id)` → fails via nil-receiver (Integer#mine_gcc). Scheduler self-schedules hourly; dedup guard always returns false. Path is broken but wastes Sidekiq queue capacity.
3. **No idempotency**: No mining-level elapsed-time checkpoint, last-mined timestamp, or event record exists in either path.
4. **Disconnected rates**: `BaseCraft#recalculate_stats` persists `current_mining_rate_gcc_per_hour`; `mine_gcc` independently derives amount via `MiningUnitAdapter`. No data flows between them.

The goal is a containment plan and decision matrix — not implementation.

---

## Files Involved

### Primary Files — read for this planning task
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/jobs/satellite_mining_scheduler_job.rb` | Hourly scheduler that queues MineGccJob | `#perform` lines 7-45, `mining_job_queued?` line 38 |
| `galaxy_game/app/jobs/mine_gcc_job.rb` | Failing job — receives satellite.id (integer) but calls .mine_gcc | `#perform(colony)` lines 6-8 |
| `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` | Mining concern — mine_gcc, can_mine_gcc?, MiningUnitAdapter | `#mine_gcc` lines 10-95, `MiningUnitAdapter` lines 293-330 |
| `galaxy_game/app/models/craft/satellite/base_satellite.rb` | process_tick — calls mine_gcc then deposits to owner account | `#process_tick` lines 291-315 |
| `galaxy_game/app/models/financial/account.rb` | Account deposit mechanism with with_lock | `#deposit` lines 52-60 |
| `galaxy_game/app/models/craft/base_craft.rb` | recalculate_stats — persists current_mining_rate_gcc_per_hour | `#recalculate_stats` lines 372-395 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/jobs/game_simulation_job.rb` | Game loop cadence (1 min) — does NOT directly trigger satellite mining |
| `galaxy_game/app/models/game.rb:104-108` | process_units calls Units::BaseUnit.operate — not mine_gcc |
| `galaxy_game/spec/integration/game_loop_integration_spec.rb:196-247` | Integration test for mine_gcc via game loop |
| `galaxy_game/spec/models/concerns/cryptocurrency_mining_spec.rb:59-65` | Unit spec for mine_gcc concern |

---

## Implementation Steps (Planning Only)

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/current/2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md \
       projects/galaxy_game/tasks/active/2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-16-HIGH-PLANNING-GCC-MINING-SCHEDULER-CONTAINMENT-AND-ISSUANCE-FLOW.md"
```

**Paste the `find` verification output in chat and confirm the YAML status was updated to `active` before proceeding.**
Expected: exactly one result, at the `active/` path.

### Step 1 — Scheduler Containment Analysis

Produce a containment recommendation for `SatelliteMiningSchedulerJob` with evidence from current repository patterns:

1. **Hard-containment option**: Evaluate an explicit early return, removal from an existing recurrence registration, or another repository-standard non-enabling mechanism. Assess impact on Sidekiq queue, existing queued jobs, and rollback complexity.
2. **Feature-flag gate option**: Add a configuration flag (e.g., `mining_scheduler_enabled`) read from `config/application.yml` or `economic_parameters.yml`. Assess whether such a config pattern exists in the codebase.
3. **Sidekiq queue disable option**: Move scheduler to a disabled queue or remove from Sidekiq cron config. Assess whether Sidekiq cron is used and how it's configured.

> This is planning only. Do not modify scheduler, job, configuration, queue registration, source, tests, or runtime behavior. Return a proposal only.

For each option, report:
- Exact file(s) and line(s) that would be modified
- Rollback complexity (1-line revert vs multi-step)
- Risk of activating uncontrolled minting if containment fails
- Alignment with existing repository patterns

### Step 2 — Upstream Liveness Trace (MANDATORY EVIDENCE REQUIREMENT)

Before producing any decision matrix, trace every caller of `GameService#process_free_crafts` and `GameService#process_settlements` to determine whether these methods are reached from:
- GameSimulationJob's real production cycle;
- Another live runtime path (test/development-only code); or
- An unresolved entry point.

Require exact file:line citations proving reachability. The final report must label the double-credit condition as one of: **confirmed live**, **candidate-live pending upstream trace**, **latent**, **test-only**, or **unresolved**—without overstating available evidence.

### Step 3 — Trigger Ownership Decision Matrix

Produce a decision matrix for canonical mining trigger with evidence from current codebase:

| Candidate | Current Status | Activation Condition | Financial Side Effects | Overlap With Other Paths | Recommendation |
|---|---|---|---|---|---|
| `process_tick` (tick-driven) | TICK-CAPABLE — dual-credit path confirmed when invoked; direct caller routes confirmed: GameService#process_free_crafts → craft.process_tick(time_skipped), GameService#process_settlements → craft.process_tick(time_skipped, settlement:) | Power-positive tick or sufficient battery | Two deposits per event (satellite + owner) | YES — primary overlap zone | [RECOMMENDATION] |
| `SatelliteMiningSchedulerJob` → `MineGccJob` | BROKEN — nil-receiver | Hourly Sidekiq schedule; configured with self-rescheduling behavior but activation/execution unverified. Do not state that cron/boot registration, external activation, or production execution has been confirmed. | None reached (fails before mutation) | POTENTIAL if receiver fixed without idempotency | [RECOMMENDATION] |
| `GameSimulationJob` → `process_units` → `Units::BaseUnit.operate` | UNVERIFIED caller chain | Every 1 minute real time | UNVERIFIED | POTENTIAL overlap if operate triggers mining | [RECOMMENDATION] |

For each candidate, cite exact file paths and line numbers. Mark UNVERIFIED stages clearly.

### Step 3 — Credit Authority and Recipient Policy Framework

Produce a decision framework for canonical GCC credit mutation:

1. **Identify all current mutation targets**: satellite.account (mine_gcc), owner.account (process_tick). Locate and verify any LDC-account mutation path, including `MissionTaskRunnerService` if present and relevant; do not assume applicability.
2. **For each target**, report: how it's reached, what amount source is used, transaction type, and locking behavior.
3. **Produce a decision matrix** with options: satellite-only, owner-only, LDC-only, or conditional routing. For each option, list required code changes (without implementing them).
4. **Identify the human decision required**: who decides, what evidence they need, and what the decision unlocks.
> **LDC Policy State — Directional Intent Only**
> The LDC account is documented as directional intent for GCC issuance routing only. It is NOT a settled controlling recipient rule. The following decision gates remain open:
> - Per-path recipient selection (satellite vs owner vs LDC)
> - Satellite-path applicability to LDC routing
> - Whether LDC routing supersedes existing `self.account`/`owner_gcc_account` deposits
> - Authorization expression (ownership / facility role / auth record / combination)
> - Canonical LDC account resolution mechanism
> 
> The upstream decision artifact that must resolve these gates is the **Issuance Authorization & LDC Recipient Routing** draft (`2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md`).
### Step 4 — Rate/Cadence/Idempotency Framework

Produce a framework for rate and idempotency governance:

1. **Rate authority**: Document the two disconnected calculations (`recalculate_stats` vs `MiningUnitAdapter`) with exact file paths and line numbers. Produce candidate options for reconciliation (use persisted rate, use adapter rate, or document intentional disconnect) — these are for human review only; do not imply any option is approved.
2. **Cadence alignment**: Map game-time cadence (GameSimulationJob every 1 min → N game days) vs wall-clock cadence (SatelliteMiningSchedulerJob every 1 hour). Identify whether they can produce overlapping credits for the same elapsed interval.
3. **Idempotency options**: Produce bounded candidate options with repository pattern alignment:
   - Last-mined timestamp check before each deposit
   - Candidate uniqueness/event-key strategy: propose key fields only after establishing canonical trigger, time basis, and mining-event identity (do not assume daily granularity is correct)
   - Transaction-level lock scoped to mining event
   - Document why no idempotency is acceptable (if that's the recommendation)

### Step 5 — Portfolio Placement and Existing-Work Preflight

This task coordinates evidence and proposed ordering. It does not move, supersede, revise, dispatch, or implement any other task.

Before recommending any next task, inventory and verify the path, YAML status, scope, dependencies, and likely touched code for all related work in:

- `projects/galaxy_game/tasks/active/`
- `projects/galaxy_game/tasks/backlog/current/`
- `projects/galaxy_game/tasks/drafts/`
- relevant summaries and handoffs

At minimum, verify these known related items:

#### Current/backlog work

- `2026-09-03-MEDIUM-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY.md`
- `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-FITTING-DRIVEN-OUTPUT-GAMELOOP-INTEGRATION.md`
- `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-UNIFIED-HARDWARE-CAPACITY-AND-SIMULATION-LOOP-INTEGRATION.md`
- `2026-09-14-INVESTIGATION-SATELLITE-BATTERY-COMPATIBILITY.md`
- `2026-09-14-ARCHITECTURE-EXCHANGE-RATE-RECONCILIATION.md`
- `2026-09-14-INVESTIGATION-VIRTUAL-LEDGER-EXCHANGE-RATE-100.0.md`

#### Draft work

- `2026-09-15-HIGH-BUG-FIX-BOOTSTRAP-USD-GCC-CONVERSION-CORRECTION.md`
- `2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md`
- `2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md`
- `2026-09-15-HIGH-ARCHITECTURE-GCC-MINING-CADENCE-RATE-SEMANTICS-ALIGNMENT.md`

For every related task, classify it as:

- independent;
- upstream evidence/dependency;
- downstream/blocked;
- overlapping and requiring revision;
- duplicate candidate requiring human disposition; or
- insufficient evidence.

No new GCC-mining implementation task may be proposed without identifying its unique responsibility and overlap with current/draft work.

### Step 6 — Containment Recommendation and Next-Task Proposal

Produce:
1. A single containment recommendation (hard disable / feature flag / Sidekiq queue disable) with exact file paths and rollback plan.
2. A prioritized list of human decisions required before any implementation.
3. The smallest recommended next task (one bounded proposal only).
4. A clear statement of what this task does NOT decide.
5. If evidence is insufficient to recommend between containment options, return "blocked/no containment recommendation" — do not force a single implementation recommendation when evidence is insufficient.

---

## Acceptance Criteria

- [ ] Containment recommendation identifies exact file(s), line(s), and rollback complexity
- [ ] Trigger ownership matrix cites exact file paths and line numbers for every candidate
- [ ] Credit authority framework identifies all mutation targets with call order and amount sources
- [ ] Rate/cadence framework documents both disconnected calculations with reconciliation options
- [ ] Portfolio preflight completed with all related tasks inventoried, classified, and dispositioned
- [ ] No implementation change is proposed as already approved
- [ ] No currency/gameplay redesign is recommended
- [ ] Mining hardware simulation is kept separate from issuance plumbing
- [ ] Any duplicate-credit conclusion identifies both mutation paths and shared event/interval
- [ ] Any job-failure conclusion identifies the actual enqueue argument and job receiver behavior
- [ ] All recommendations are evidence-qualified (not assumptions)
- [ ] No repository files or Git state changed

---

## Stop Conditions — escalate to user immediately if:

- Evidence contradicts a finding in the flow-map audit (re-verify before reporting)
- A required file path does not exist in current repository state
- The task requires making an architectural decision that belongs to human/product
- Containment would touch files beyond scheduler configuration/job registration, alter mining/accounting behavior, or require an unresolved human product or architecture decision. Stop and request a separately scoped task.
- Evidence is insufficient to recommend between two containment options

---

## Dependencies

**Blocked by:**

- The GCC mining flow-map audit and any contradictions found during current source re-verification.
- Completion of the portfolio preflight in this task.
- Human decisions on canonical trigger ownership, recipient/routing policy, rate authority, and event/cadence/idempotency contract before a downstream implementation task is dispatched.

**Blocks:**

- New or existing tasks that alter satellite mining trigger ownership, `mine_gcc`, `BaseSatellite#process_tick`, `MineGccJob`, `SatelliteMiningSchedulerJob`, mining recipient routing, rate authority, mining cadence, or mining event idempotency.

**Does not automatically block:**

- Independent USD→GCC exchange/conversion correction work, unless portfolio preflight identifies a shared account/ledger or policy dependency.
- Non-mining battery, documentation, material-data, or unrelated simulation work, unless it touches a listed mining/financial execution path.

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was produced
- Containment recommendation with exact file paths and rollback plan (or "blocked/no containment recommendation" if evidence insufficient)
- Trigger ownership decision matrix
- Credit authority framework
- Rate/cadence/idempotency framework
- Portfolio preflight: related-task inventory, overlap classification, and disposition
- Recommended global deployment order
- Explicit human decision gates
- Smallest next-task proposal only
- No-change confirmation and initial Git status
- All files and task directories searched (with paths)
- Verified mining-flow evidence with call-order tracing
- Scheduler containment option comparison
- Statement that no implementation was performed

### Issues discovered
[Any problems found during planning that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future planning tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [containment recommendation] | [trigger decision matrix status] | [human decisions pending] | [next action needed]

