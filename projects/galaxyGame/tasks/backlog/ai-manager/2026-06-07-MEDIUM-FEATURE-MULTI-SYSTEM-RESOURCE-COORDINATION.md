---
status: backlog
resume: false
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: POST_LUNA_MULTI_SYSTEM
local_worker_safe: true
---

## Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). The project name, project path, subfolder, and filename are already filled in. Do not change anything else.

```
You are **Implementation Agent**.

Project: galaxyGame
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md \
              projects/galaxyGame/tasks/active/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
       test ! -e projects/galaxyGame/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md && test -f projects/galaxyGame/tasks/active/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxyGame/README.md
  3. <agent-tasks>/projects/galaxyGame/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/galaxyGame/summaries/
  Filename pattern: 2026-11-23-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.** It is the startup contract.

---

## Task Readiness Checklist (Human — before dispatching)

**This task is intentionally retained in backlog and is NOT ready for implementation dispatch.** Recheck every item after independent review and before any human dispatch authorization.

- [ ] Agent Dispatch Interface is complete and matches the current canonical template
- [ ] All prerequisite capabilities are verified against current source/status evidence
- [ ] Synthesis report template and evidence requirements are complete
- [ ] All implementation boundaries and stop conditions are clear
- [ ] Referenced paths are verified
- [ ] Architecture gotchas are specific
- [ ] Acceptance criteria are measurable
- [ ] Cross-system and disconnected-route scenarios are mandatory
- [ ] Human has approved the investigated implementation approach
- [ ] Independent reviewer has issued GO on the revised task

**Do not dispatch while the operational Luna benchmark, real settlement inventory/acquisition inputs, multi-system route feasibility/cost inputs, or safe contract/reservation ownership remain unverified.**

---

# TASK: Multi-System Resource Coordination (Cross-Settlement Network Supply)

**Status**: BACKLOG — deferred network-scale capability; not approved for implementation dispatch  
**Priority**: MEDIUM  
**Type**: feature  
**Created**: 2026-06-07  
**Last Updated**: 2026-11-23

## Local Worker Triage Report

- **Template Conformance**: PASS — updated to the current canonical dispatch and synthesis structure; independent review still required.
- **Docker Wrapper Check**: NEEDS CONFIRMATION — task provides targeted commands; reconcile the app guide's older `cd /home/galaxy_game` examples with the current task-template instruction that the container working directory is already `/home/galaxy_game`.
- **MVP Alignment**: VALID — explicitly post-Luna and multi-system; not part of the current Luna operational benchmark.
- **MVP Impact Note**: Prevents premature network-scale optimization from being built on unvalidated single-settlement and transport scaffolding.
- **Action Line**: NEEDS MANUAL REVIEW — do not dispatch.

## Agent Assignment

**Assigned To**: Unassigned — local Qwen via Copilot is the primary executor when the task is eventually approved  
**Why This Agent**: Use the project's current local-worker routing rules; no executor is assigned by this draft.  
**Local attempts before cloud**: N/A  
**Supervision Level**: watched carefully

## Prerequisites — READ FIRST (Sequential Order)

1. Read the agent-tasks repository `README.md` executor guidance and `rules/GUARDRAILS.md`.
2. Read `projects/galaxyGame/README.md` and `projects/galaxyGame/SESSION_GUIDANCE.md` if present.
3. Read the GalaxyGame implementation guidance at `docs/agent/IMPLEMENTATION_AGENT_README.md`; resolve the documented Docker-wrapper discrepancy before running specs.
4. Read the linked wiki pages in the order listed below.
5. Inspect the current application source and tests at the current `main` commit. Do not rely on this task's historical source snapshot as proof that paths or behavior remain unchanged.

## Context

This is a later network-scale AI Manager capability. GalaxyGame already has a system orchestration structure that registers settlement managers, arbitrates priorities, aggregates resource state, identifies candidate transfers, and schedules transfer records. Source inspection shows that several of those inputs and operations are still mock, hard-coded, log-only, or in-memory; the existence of the classes does not establish operational coordination.

The goal is to make resource coordination across existing settlements reliable and economically correct, extending the existing AI Manager and logistics architecture rather than creating a parallel acquisition or execution system. Cross-system transfers are in scope only when real route connectivity, capacity, delivery-time, and cost inputs are available.

## Relevant Architecture Docs — read before starting

**Primary coordination specification:**
- `docs/wiki/services/multi_settlement_resource_coordination.md` — source-grounded current structure, implementation gaps, economic boundaries, safety requirements, and required scenarios.
- `docs/wiki/services/ai_manager.md` — AI Manager ownership, orchestration, strategic planning, and current code-status distinctions.

**Economic and logistics policy:**
- `docs/wiki/logistics/eap_and_ai_manager_escalation.md` — EAP and acquisition escalation policy; distinguish target design from enforced code.
- `docs/wiki/logistics/resource_and_market_logistics.md` — canonical market intent, GCC/USD, EAP, regional market knowledge, and player market rules.
- `docs/wiki/logistics/market_and_ai_bootstrapping.md` — market implementation and AI Manager integration status.
- `docs/wiki/logistics/import_order_and_cycler_models.md` — transport tiers and future cycler/import-order intent; do not assume future models are implemented.
- `docs/wiki/technical/natural_wormholes_and_em.md` — wormhole traversability, stability, and mass-throughput constraints.
- `docs/architecture/intent/WORMHOLE_NETWORK_INTENT.md` — wormhole network design intent.

**Application implementation guidance:**
- `docs/agent/IMPLEMENTATION_AGENT_README.md` — repository and Docker/RSpec constraints.
- `galaxy_game/app/services/ai_manager/system_orchestrator.rb`
- `galaxy_game/app/services/ai_manager/resource_allocator.rb`
- `galaxy_game/app/services/ai_manager/settlement_manager.rb`
- `galaxy_game/app/services/ai_manager/system_state.rb`
- `galaxy_game/app/services/ai_manager/priority_arbitrator.rb`
- `galaxy_game/app/services/ai_manager/logistics_coordinator.rb`
- `galaxy_game/app/services/logistics/contract_service.rb`
- `galaxy_game/app/services/logistics/internal_transfer_service.rb`
- `galaxy_game/app/services/logistics/import_request_generator.rb`
- `galaxy_game/app/services/logistics/transport_cost_service.rb`
- `galaxy_game/app/services/logistics/route_cost_calculator.rb`
- `galaxy_game/app/models/logistics/contract.rb`

The wiki is the design-intent reference; source code is the evidence of current behavior. Record conflicts explicitly. Do not silently rewrite one to match the other.

## Critical Information for This Task

### Intended responsibility

The network coordination capability must:
1. identify actual settlement needs and exportable surplus;
2. protect local survival, operating, maintenance, and already-committed reserves;
3. discover eligible source settlements and feasible transport routes;
4. compare delivered options against local production, destination-market acquisition, and the destination-specific EAP fallback;
5. identify supplier dependencies, single points of failure, and cascading shortage exposure;
6. provide a reviewable plan to the existing execution owner;
7. revalidate all relevant state before any contract, reservation, purchase, or shipment is committed.

The capability coordinates existing settlements and existing network infrastructure. It does not establish footholds, generate star systems, open routes, authorize expansion, or create demand to justify expansion.

### Locked economic and player-first policy

Inherit the policy in the linked canonical wiki. Do not invent a new market or currency model.

- Preserve player-visible market/contract opportunity before NPC fallback wherever the applicable market and contract policy requires it.
- Use real GCC and existing budget/funding eligibility. The AI Manager cannot spend funds it does not have or ignore existing commitments.
- Compare with the destination- and item-specific EAP fallback using current supported cost inputs; do not use a universal hard-coded EAP.
- Treat market prices and inventory as settlement-local information that can become stale during transit.
- Preserve local production/harvesting and existing per-settlement acquisition precedence.
- Use virtual-ledger treatment only within its documented DC/NPC scope; it is not the default player-market path.
- Protect settlement survival and operating reserves even when an export would improve aggregate network metrics.
- Normal shortages may wait for scheduled resupply; emergency shortages follow the existing escalation policy.
- Network optimization must not force settlement or system expansion.

### Architecture Gotchas

**GOTCHA 1 — Existing orchestration is the integration starting point, not proof of completion.**
- Wrong: Add a second top-level coordinator because the old task named `ResourceCoordinator`.
- Right: Trace and extend `AIManager::SystemOrchestrator`, `ResourceAllocator`, and `LogisticsCoordinator` only where their actual ownership and responsibilities support the capability.
- Why: The current architecture already has a system-level orchestration call sequence; duplication would fragment ownership.

**GOTCHA 2 — Do not treat mock coordination data as real inventory.**
- Wrong: Build optimization on `SettlementManager`'s hard-coded resource hash, constant health, fixed request quantity, or log-only reservation methods.
- Right: Identify and use authoritative settlement inventory, needs, reserve, and commitment data; stop if those interfaces are not available.
- Why: Fake surplus and non-atomic reservations can starve settlements or over-allocate stock.

**GOTCHA 3 — Planning and execution are separate responsibilities.**
- Wrong: A method that ranks suggestions also creates contracts, moves inventory, spends GCC, or schedules cargo.
- Right: Produce a read-only plan first; hand an approved, revalidated plan to the existing verified contract/transport execution owner.
- Why: The current code has multiple contract/transfer candidates with different behaviors. The task must establish ownership instead of adding another path.

**GOTCHA 4 — A wormhole is not automatically a cargo route quote.**
- Wrong: Treat a stable wormhole or a placeholder `EconomicConfig.wormhole_cost` as proof of an eligible, affordable, capacity-sufficient shipment.
- Right: Verify endpoint connectivity, traversability, cargo eligibility, remaining throughput, transport operator, transit time, and delivered cost at the relevant time.
- Why: Physical traversal and logistics planning are related but distinct interfaces.

**GOTCHA 5 — No aggregate optimization at the expense of local survival.**
- Wrong: Export any stock above a generic target or optimize only total network inventory.
- Right: Calculate exportable surplus after local survival/operating reserve, active reservations, inbound/outbound commitments, and the current planning batch's allocations.
- Why: Network-wide totals can conceal local shortages and competing claims.

**GOTCHA 6 — Do not assume design intent is implemented.**
- Wrong: Treat ImportOrder/cycler models, EAP enforcement, Virtual Ledger behavior, or market integration as verified runtime capabilities solely because the wiki describes them.
- Right: Verify current code and tests; label each finding as source-verified, design intent, or unresolved.
- Why: The canonical wiki intentionally distinguishes implementation from target behavior.

## Problem Statement

The current system has an orchestration scaffold, but source inspection found hard-coded/mock settlement resources and needs, simplified transport costs, hard-coded route capacity/duration, log-only reservations and allocations, and in-memory transfer scheduling. Existing logistics services offer potential execution paths, but their ownership, player-first behavior, route validation, and reservation safety must be traced.

The missing capability is an end-to-end, evidence-backed network supply process that can identify feasible resource transfers and systemic dependencies without duplicating acquisition or bypassing the player economy.

**Current behavior (source inspection, not runtime validation):**
- `SystemOrchestrator` aggregates registered managers, arbitrates requests, identifies pairwise transfer candidates, and calls `LogisticsCoordinator`.
- `SettlementManager` supplies mock values and does not mutate or reserve actual inventory.
- `LogisticsCoordinator` uses simplified hard-coded route parameters and stores scheduled transfer hashes in memory.
- `Logistics::ContractService` is a concrete contract execution candidate with a player-visible path followed by NPC fallback, but safe reservations, stale-state checks, and cross-system route integration were not established.
- The transport cost path references a route finder whose current implementation was not verified.

**Expected behavior:** a read-only, deterministic network supply plan based on live state and verified route/cost inputs, followed by execution only through a verified existing owner after atomic revalidation and policy checks.

## Files Involved

### Primary investigation and likely integration points

These are verified source paths at the wiki evidence baseline. They are not blanket authorization to edit every file; the investigation report must name the minimal edit set and explain ownership.

| File | Purpose | Key responsibility |
|---|---|---|
| `galaxy_game/app/services/ai_manager/system_orchestrator.rb` | Existing network orchestration | `orchestrate_system`, `identify_resource_transfers`, `coordinate_logistics` |
| `galaxy_game/app/services/ai_manager/resource_allocator.rb` | Existing need/surplus and allocation scaffold | `identify_transfers`, gap/surplus calculations |
| `galaxy_game/app/services/ai_manager/settlement_manager.rb` | Current settlement state adapter | resource/need/reserve inputs and reservation hooks |
| `galaxy_game/app/services/ai_manager/system_state.rb` | Aggregate state and dependencies | `update_from_settlements`, `update_dependencies` |
| `galaxy_game/app/services/ai_manager/priority_arbitrator.rb` | Request priority arbitration | `arbitrate_requests` |
| `galaxy_game/app/services/ai_manager/logistics_coordinator.rb` | Candidate transfer optimization/scheduling | `optimize_transfers`, `schedule_transfers` |
| `galaxy_game/app/services/logistics/contract_service.rb` | Candidate contract execution owner | `create_internal_transfer`, contract execution |
| `galaxy_game/app/services/logistics/transport_cost_service.rb` | Existing transport cost entry points | `calculate_cost_per_kg`, `calculate_transport_cost` |

### Reference-only unless the investigation justifies a minimal change

| File | Why |
|---|---|
| `galaxy_game/app/services/logistics/internal_transfer_service.rb` | Competing/legacy internal transfer path; determine active call sites and ownership |
| `galaxy_game/app/services/logistics/import_request_generator.rb` | Existing import-request generation; not assumed to be network routing |
| `galaxy_game/app/services/logistics/route_cost_calculator.rb` | Current simplified route-cost implementation |
| `galaxy_game/app/models/logistics/contract.rb` | Existing contract state and lifecycle |
| `galaxy_game/spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb` | Existing orchestrator integration coverage |
| `galaxy_game/spec/services/ai_manager/resource_allocator_spec.rb` | Existing allocator coverage |
| `galaxy_game/spec/services/logistics/contract_service_spec.rb` | Existing contract-service coverage |

### Edit boundary

- Do not add a new top-level coordinator class or database schema by default.
- Do not edit wormhole physics, system generation, foothold planning, market pricing, or player contract behavior as incidental cleanup.
- If a required interface is absent, stop and propose a separately scoped prerequisite rather than expanding this task silently.
- The investigation report must list exact proposed edit paths and methods before implementation approval.

### Migration

- No migration is authorized by this task at the outset.
- If durable reservations, idempotency keys, or persisted plan records require schema changes, stop and present the smallest schema proposal for human approval before editing migrations.

## Implementation Steps

**The investigation is mandatory. Implementation is conditional on verified prerequisites and explicit human approval of the proposed approach.**

### Step 0 — Dispatch lifecycle

Follow the Agent Dispatch Interface above exactly. Complete Git-state inspection, stage the source first if untracked, use `git mv`, verify a single active task copy, update YAML status, and record all evidence in the synthesis report. Do not begin investigation before the initial synthesis report exists.

### Step 1 — Establish current repository and prerequisite state

- Record the GalaxyGame `main` commit SHA and clean/dirty state; preserve all existing user changes.
- Read the linked wiki and project status sources.
- Verify whether the Luna operational benchmark, multi-settlement production/acquisition, and actual multi-system settlement presence are operationally demonstrated—not merely described or represented by task files.
- Verify whether a usable cross-system route/cargo interface exists, including endpoint reachability, traversability, remaining capacity, transit time, and cost.
- Verify the active status and ownership of the existing acquisition and contract execution paths.
- If any hard prerequisite is missing, document the evidence and stop. Do not implement a same-system-only approximation and call the multi-system task complete.

### Step 2 — Trace the existing end-to-end code path

Inspect actual call sites, signatures, inputs, outputs, persistence, and tests for the files listed above. Also trace:
- where settlement inventory and operational reserve are authoritatively read;
- how current and forecast demand, existing inbound/outbound commitments, and reservations are represented;
- how player-visible market/contract opportunities are offered and how NPC fallback is authorized;
- how GCC affordability, quotes, EAP, and emergency escalation are checked;
- how contract creation, cargo scheduling, inventory movement, and delivery are performed;
- how wormhole and non-wormhole routes are discovered and validated;
- whether `SystemOrchestrator` is instantiated and receives all eligible settlements in actual runtime flows.

Record each interface in the synthesis report with exact file, method, parameters, return shape, persistence/side effects, and evidence status. Do not infer an API from a class name or wiki diagram.

### Step 3 — Produce the architecture and integration proposal; STOP for approval

Before editing application code, propose:
1. the existing component that should own read-only planning;
2. the exact call site in the current orchestration path;
3. the existing component that should own contract/transport execution;
4. the smallest set of method/interface changes and exact edit paths;
5. a typed/structured plan result with source, destination, resource, quantity, reserves, commitments, deadline, cost breakdown, route evidence, quote timestamp, and ranking rationale;
6. how player-first market behavior, GCC affordability, EAP, local acquisition, and emergency escalation remain authoritative;
7. how reservation, idempotency, transactionality, stale-state revalidation, and cancellation are enforced;
8. what is explicitly deferred because a prerequisite interface is absent.

**STOP after updating Stage 2 of the synthesis report.** Present the proposed approach and wait for explicit human approval before implementing. If the correct approach requires a new prerequisite task, report that instead of widening scope.

### Step 4 — Implement the approved plan

Only after human approval:
- extend existing ownership boundaries; do not create a parallel acquisition or contract engine;
- keep planning read-only and separate from execution;
- protect local reserves and subtract existing commitments before computing exportable quantities;
- make competing allocation deterministic and ensure total planned/reserved quantities cannot exceed exportable stock;
- make repeated runs idempotent;
- revalidate inventory, reservations, demand, destination capacity, available funds, market quote, route, capacity, and deadline immediately before execution;
- reject stale or disconnected plans with an explicit reason and return them for replanning;
- use player-visible market/contract opportunities before NPC fallback where policy requires it;
- keep unsupported cross-system movement unavailable rather than fabricating cost, capacity, or transit time.

### Step 5 — Verify focused and regression scenarios

Build deterministic factories/fixtures using actual models and service boundaries. Required scenarios are listed below. Capture baseline results before changes and rerun affected tests after implementation.

### Step 6 — Update documentation

Update the coordination wiki page with the actual implemented interfaces, behavior, limitations, and evidence. Update `docs/wiki/services/ai_manager.md` if service ownership or code-status descriptions change. Keep design intent, source-verified behavior, and unresolved questions distinct.

## Economic comparison and ranking rules

Every candidate option must be compared for the same resource specification, usable quantity, destination, and required-by time.

A candidate is eligible only if:
- the source has sufficient exportable stock after protected reserves, active reservations, and prior allocations;
- the destination has an actual deficit after current stock and committed inbound supply;
- the route is reachable and can carry the cargo;
- the delivery can meet the required-by time;
- the relevant buyer/source/operator is eligible under the existing market and contract rules;
- the destination can accept/store the shipment;
- the required GCC or other applicable funding is available after existing commitments.

For eligible options, compare a documented delivered-cost breakdown using only verified current inputs: resource acquisition/transfer value, transport, handling/provider fees, and other applicable costs actually represented by the code. Compare network delivery with local production/acquisition and the destination-specific EAP fallback. Do not fabricate missing cost components or assume a missing quote is zero.

**Ranking precedence:**
1. Exclude ineligible, disconnected, capacity-insufficient, unaffordable, or late options.
2. Preserve survival/critical priority and the existing acquisition/player-first policy.
3. Among otherwise equivalent eligible options, prefer the lower comparable delivered cost.
4. If costs tie, prefer earlier on-time arrival, then the more reliable/less constrained verified route.
5. Apply a stable final tie-break using source settlement identifier and route identifier so equal inputs yield repeatable results.

If the data required for a fair comparison is missing or stale, mark the option unknown/ineligible and explain why. Do not silently rank it as beneficial.

## Reservation, idempotency, and stale-state rules

- Exportable quantity = authoritative current quantity − protected local reserve − active reservations − existing outbound commitments − allocations already made in the current batch.
- A destination's remaining need = current requirement − usable inventory − confirmed inbound commitments; do not count speculative or failed shipments as inbound supply.
- Reservations and contract creation must be atomic against concurrent requests. A log message or in-memory flag is not a reservation.
- Repeated planning/execution for the same outstanding need must not create duplicate active contracts. Use an existing idempotency mechanism if one is verified; otherwise propose the smallest safe mechanism.
- Revalidate all state at execution time. A plan is not an authorization to execute after inventory, funding, quote, destination capacity, route connectivity, route capacity, or delivery deadline has changed.
- Cancellation/failure must release reservations exactly once and must not duplicate or lose inventory.
- If safe reservation and idempotency cannot be provided through an existing owner or a narrowly approved extension, keep the capability planning-only and stop for human direction.

## Required deterministic scenarios

These are mandatory; cross-system cases are not optional.

1. Same-body settlement transfer with sufficient surplus and a valid local route.
2. Same-system transfer between different celestial bodies with a verified transport option.
3. Cross-system transfer through a connected, traversable, capacity-sufficient wormhole route.
4. Disconnected systems: no transfer/contract is proposed or executed.
5. Source stock appears surplus before reserve protection but is insufficient after reserve and active commitments.
6. Two or more destinations compete for one source; allocation respects priority and never exceeds exportable stock.
7. A critical supplier serves multiple settlements; dependency/risk analysis flags the single point of failure.
8. Local production or destination-market acquisition is cheaper or faster than network supply.
9. Network delivery is cheaper but misses the deadline; it is not treated as a valid fulfillment option.
10. Player-visible contract/market opportunity is offered before eligible NPC fallback.
11. Repeated coordinator runs do not create duplicate active commitments.
12. Inventory, funds, quote, destination capacity, route connectivity, or route capacity changes after planning; stale plan is rejected or recomputed before execution.
13. Emergency shortage follows existing escalation rules and records any authorized exception.
14. Existing single-settlement shortage/acquisition behavior remains unchanged.

## Verification Commands

The following targeted spec paths were verified to exist in the repository at the source-review baseline. Reconfirm them against the current branch before execution. The canonical task template currently says the Docker container working directory is already `/home/galaxy_game` and commands must not add a `cd`; the GalaxyGame implementation README contains older examples with `cd /home/galaxy_game`. Resolve this documentation discrepancy against the current project guidance before running tests; do not guess.

Run each command from the host shell. Capture output and preserve the actual exit status:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec /home/galaxy_game/spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb' > /tmp/rspec-resource-coordination-orchestrator.log 2>&1
status=$?
cat /tmp/rspec-resource-coordination-orchestrator.log
exit "$status"
```

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec /home/galaxy_game/spec/services/ai_manager/resource_allocator_spec.rb' > /tmp/rspec-resource-coordination-allocator.log 2>&1
status=$?
cat /tmp/rspec-resource-coordination-allocator.log
exit "$status"
```

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec /home/galaxy_game/spec/services/logistics/contract_service_spec.rb' > /tmp/rspec-resource-coordination-contracts.log 2>&1
status=$?
cat /tmp/rspec-resource-coordination-contracts.log
exit "$status"
```

Add focused specs for the approved planner/execution behavior at the actual verified spec paths. Do not run the full RSpec suite interactively. Before each RSpec run, perform the mandatory pre-execution check in `docs/agent/IMPLEMENTATION_AGENT_README.md` / `rules/GUARDRAILS.md`. Never pipe away or mask a failing command's exit status.

## Acceptance Criteria

- [ ] Existing AI Manager orchestration and logistics ownership are documented with exact current call sites and interfaces.
- [ ] The implementation does not add a second top-level orchestrator or duplicate acquisition/contract execution system.
- [ ] Prerequisite readiness is demonstrated from current source/status evidence; otherwise the task stops with explicit blockers.
- [ ] Real inventory, demand, local reserve, reservations, and inbound/outbound commitments are used; no mock/hard-coded values drive production decisions.
- [ ] Read-only planning produces deterministic, explainable, actionable source-to-destination options.
- [ ] Same-system and genuinely cross-system route feasibility are verified using current transport interfaces.
- [ ] Disconnected routes produce no executable transfer.
- [ ] Economic comparisons use comparable delivered quantities, verified cost components, deadlines, eligibility, and destination-specific EAP/local alternatives.
- [ ] Player-first market/contract policy, GCC affordability, survival precedence, and emergency escalation are preserved.
- [ ] Reservations and contract execution are atomic, idempotent, and revalidated against stale state.
- [ ] Cascading/single-supplier dependencies are reported with deterministic evidence.
- [ ] All 14 required deterministic scenarios pass.
- [ ] Existing single-settlement acquisition/shortage regressions pass.
- [ ] Targeted Docker-wrapped RSpec results and command outputs are captured with failure status preserved.
- [ ] Wiki accurately distinguishes implemented behavior, design intent, and remaining gaps.
- [ ] Out-of-scope boundaries are preserved.

## Out of Scope

- Replacing per-settlement acquisition, market pricing, or EAP logic.
- Rebuilding wormhole physics, wormhole topology, or system generation.
- Creating new footholds, settlements, systems, or expansion demand.
- Full real-time ship-position optimization or a new route-planning physics engine.
- TerraGen consortium shared pools or Eden-specific prioritization exceptions.
- Player mission generation from network shortages.
- Broad market, currency, contract, or logistics rewrites.
- Unrelated cleanup of legacy duplicate services or classes.

## Stop Conditions

Stop and report evidence; do not improvise or widen scope if:
- the Luna operational benchmark or required multi-settlement acquisition capabilities are not demonstrated;
- there are not at least two eligible settlements and a real cross-system scenario to validate;
- no verified cross-system cargo route/capacity/delivery-cost interface exists;
- the only available settlement inventory/need/reserve inputs are mock or hard-coded;
- player-first contract/market ownership cannot be established;
- safe atomic reservation, idempotency, or stale-state revalidation requires a broad schema/core rewrite;
- required cost, funding, deadline, or route data is missing or cannot be compared honestly;
- an architectural decision or new prerequisite task is required;
- implementation would change wormhole physics, market policy, or another system outside this task.

## Dependencies

**Blocked by:**
- A demonstrated operational Luna/single-settlement benchmark and reliable underlying resource accounting.
- Verified per-settlement need, inventory, reserve, acquisition, and financial eligibility interfaces.
- At least two operational settlements with a real cross-system scenario.
- Verified cross-system route reachability, cargo capacity, transit-time, and delivered-cost inputs.
- A verified existing contract/transport execution owner with safe reservation and stale-state handling, or explicit approval for a narrowly scoped extension.

**Blocks:** Future network-wide supply resilience and higher-level multi-settlement logistics capabilities that depend on reliable cross-system supply coordination.

**Related:** AI Manager & Autonomous Coordinators; EAP & AI Manager Escalation; Resource & Market Logistics; ImportOrder & Cycler Transport Models; Natural Wormholes and EM; foothold planning and settlement operations.

## Documentation

- [x] Add `docs/wiki/services/multi_settlement_resource_coordination.md` as the coordination architecture and implementation-gap reference.
- [x] Update `docs/wiki/services/ai_manager.md` to distinguish orchestration scaffolding from operationally validated coordination.
- [ ] During implementation, update the coordination wiki with verified APIs, behavior, test evidence, and remaining gaps.
- [ ] Update AI Manager service ownership/status tables if implementation changes them.

## Review Gate — STOP Before Implementation and Before Commit

**Architecture approval gate:** after Step 3, stop and present the complete evidence-based proposal. Do not edit application code until the human explicitly approves the proposed approach.

**Implementation review gate:** after implementation and verification, save evidence and request independent reviewer assessment. Do not commit, push, or mark the task complete until a reviewer issues GO and the human separately authorizes the specific operation. Commit and push require separate explicit human authorizations. Follow `REVIEW_AGENT_GUIDE.md` and `SESSION_CLOSEOUT.md`.

## Progress / Resume

- **Done so far:** Read the original task and Perplexity's specification review; inspected current GalaxyGame source and wiki on `main`; documented the existing orchestration scaffold and its limitations; updated the coordination wiki and AI Manager wiki on a review branch; rewrote this task on a separate review branch.
- **Next step:** Independent review of the revised wiki/task package; reconcile any reviewer findings; obtain human approval before merging or dispatching.
- **Last verified fact (with command):** Read-only GitHub source inspection at GalaxyGame commit `9fbb812df08af596e34e5e7d0c3c3055257395b9`; task/template/guide inspection at agent-tasks commit `6803b42be38b4ccd416d00e931cf37eeef949d86`. No application specs or simulation runs were executed.
- **Local-only commits (git log --oneline origin/main..HEAD):** Not applicable — changes are on GitHub review branches; no local clone inspected.
- **Owning session / window label:** ChatGPT — multi-system resource coordination modernization, 2026-11-23.

## Completion Report

**Completed by**: [implementing agent]  
**Completion date**: YYYY-MM-DD  
**Final test result**: X examples, Y failures  
**Evidence basis:** [direct source verification / runtime test / reviewer evidence / human assertion]

### What was changed
- [file] — [description]

### Issues discovered
- [document source/code discrepancies and unresolved blockers]

### Follow-up tasks needed
- [list only evidence-backed follow-up tasks; do not create them without authorization]

### Lessons learned
- [record reusable architectural findings]

## Handoff Summary

HANDOFF SUMMARY: [wiki and service ownership updates] | [coordination implementation and verification state] | [next action]
