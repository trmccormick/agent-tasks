---
date: 2026-11-23
updated: 2026-11-23
status: backlog
priority: HIGH
type: feature + architecture
system_domain: ai-manager / task-execution
mvp_alignment: Luna operational benchmark — task definitions must produce real, verifiable game operations
agent_dispatch_interface: |
  You are the Implementation Agent.

  Project: Galaxy Game
  Task: AI Manager — Complete, Authoritative Task Execution

  Read in order:
  1. This task file.
  2. `projects/galaxyGame/SESSION_GUIDANCE.md`, `README.md`, current `status.md`, and the latest relevant handoff.
  3. `docs/wiki/services/ai_manager.md`, especially the 2026-11-23 task-execution audit.
  4. `galaxy_game/lib/tasks/luna_mission.rake` — exact Luna phase/task selection and integration harness.
  5. `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` — V2 dispatcher and handlers.
  6. The four phase files and the exact task JSON files referenced by the local Luna profile/mission plan. These live under the local, gitignored `data/json-data/missions_v2/` tree; do not substitute the broader `missions/tasks_v2/` draft library.
  7. Existing domain services, models, jobs, and specs for each operation before proposing new implementation.
  8. Relevant task-engine and Luna integration specs.

  Before implementation, produce a source-backed synthesis mapping every effect used by the selected Luna task set to its current handler, authoritative domain operation, persistence result, and verification method. Stop for review before coding.

# Objective

Make the AI Manager capable of executing the selected Luna task definitions as complete, authoritative game operations—not merely creating some database records or printing success messages.

The AI Manager is responsible for planning and carrying out work through reusable task definitions. It must be able to invoke the game's real construction, deployment, connection, resource movement, production, equipment operation, and verification capabilities through their authoritative models/services. It must report success only when the requested outcome actually occurred in game state.

The Luna Rake is the first integration benchmark for this capability. It selects the intended Luna task references through its four existing phase IDs:
- `power_comms`
- `isru_deployment`
- `gas_processing`
- `robot_logistics`

Preserve that selection and sequence unless source evidence demonstrates a defect. Do not independently select tasks from the 163-file draft library, and do not expand this task to migrate the entire draft library.

# Current verified problem

A source inspection of `luna_mission:execute` and `AIManager::TaskExecutionEngineV2` found a mixed execution model.

**Persisted operations already present:**
- The Rake finds/creates a Luna settlement and location context.
- Manifest setup creates inventory item records and, in its launch-staging branch, a HeavyLander record with cargo inventory; it also copies cargo quantities into settlement inventory.
- `deploy_unit` checks and consumes settlement inventory, then creates `Units::BaseUnit` records using blueprint data where available.
- `connect_units`, `register_to_bus`, `set_unit_state`, `set_settlement_state`, and deployment-stage advancement persist selected connection/state metadata.

**Known incomplete or false-success behavior in the V2 executor:**
- `construct_structure_from_effect` is a logging placeholder and returns true.
- `transfer_resource_from_effect` logs a transfer/configuration but does not move resources or establish an operating transfer.
- `manufacture_from_effect` logs production but does not invoke production or add output inventory.
- `check_unit_state_from_effect` logs success without checking state.
- Unknown effect actions are skipped and return true.
- Some handlers return true when the settlement is absent.
- Deployment-stage metadata changes do not prove the represented physical operation occurred.
- The Rake's separate verifier marks unsupported effects as unverified, but unverified-only results currently count as passed.
- The Rake's task-derived deploy-inventory pre-seeding helper resolves refs through the older `MISSIONS_PATH`, unlike its main V2 task resolver.
- The Rake's launch-staging branch creates records and copies inventory; it does not by itself prove a launch, transit, arrival, or cargo offload occurred.

Do not assume these are the only gaps. The selected Luna task JSON may exercise additional effects, and existing domain services may already implement some of the required behavior.

# Required work

## 1. Build the execution/effect coverage map

For every effect action used by the exact Luna-selected task files, record:
- Task file and task/effect identifier.
- Dispatcher action and current handler.
- Whether the handler is real, partial, placeholder, unsupported, or verification-only.
- Existing authoritative model/service/job that should perform the operation.
- Database/game-state changes expected.
- How the result can be independently verified.

Inspect the task JSON from the local V2 Luna fixture directory. Do not infer the selected set from filenames or from the broader draft task library.

## 2. Reuse authoritative game operations

For each required effect:
- Find and reuse existing game-domain services/models/jobs where they exist.
- Route construction through the actual construction/building workflow rather than treating a log line or generic flag as completed construction.
- Route transfers through real inventory/resource-flow mechanisms, including quantity, source/target validation, and persistence.
- Route manufacturing through existing production/material-processing operations and persist actual outputs and consumed inputs.
- Ensure deployed equipment is recognized by the normal simulation/operational systems where that is the intended behavior. A `Units::BaseUnit` row with descriptive `operational_data` alone is not proof that the unit operates.
- Preserve real sequencing, material availability, infrastructure dependencies, ownership, and failure behavior.
- Do not create parallel versions of existing domain services.

If a required domain operation does not exist, document the missing capability and stop/escalate rather than implementing a fake success path or inventing an unrelated subsystem.

## 3. Make task execution outcomes truthful

- Unknown or unsupported actions must fail explicitly (or return a structured unsupported result); they must never silently succeed.
- A missing settlement or required target must fail explicitly.
- A handler may return success only after the operation has been accepted/completed according to its contract.
- Separate command acceptance, asynchronous/in-progress work, and completed outcomes where existing job/workflow patterns require it.
- Verification must inspect authoritative persisted state or operation results, not echo the requested effect.
- The Rake verifier must not treat `unverified` as success. Unsupported verification must be visible and fail the task/benchmark until an explicit verification contract exists.
- Preserve useful error context (task, effect, target, cause) for AI Manager decisions and recovery.

## 4. Validate with the Luna benchmark

- Keep the Rake's existing four-phase selection and task references intact.
- Correct the V2 task-derived inventory pre-seeding path so it resolves the same selected task references as the main execution loop, if that helper remains necessary.
- Use a disposable/test database for destructive Rake validation. The current harness clears settlement inventory before reseeding; do not run it against valuable development data without explicit confirmation.
- Add targeted tests for each implemented effect handler, including success, missing prerequisites, insufficient materials, unsupported actions, and persisted outcomes.
- Add or update an integration test proving the selected Luna tasks create the expected persisted objects and real operation outcomes.
- Verify that the normal game operational/tick path recognizes and operates deployed units where the task claims to establish operating equipment.
- Report exactly what the Rake proves and what it does not prove. Do not label the settlement fully operational unless the tests establish ongoing operation.

## 5. Preserve AI Manager autonomy

The Rake is a benchmark/integration harness, not the AI Manager's permanent operating script. Do not move its hardcoded four-phase sequence into generic AI Manager planning as the solution.

The AI Manager must ultimately be able to reason from objectives, conditions, available assets, task definitions, dependencies, resources, and constraints; choose/compose tasks; execute through authoritative services; observe results; and adapt or recover. This task establishes trustworthy execution semantics for the Luna-selected task set. It does not authorize hardcoding a Luna recipe into the generic planner.

# Acceptance criteria

1. Every effect action in the selected Luna task set has an explicit, source-backed execution contract and is implemented through the appropriate existing domain operation, or is explicitly blocked/escalated as a missing capability.
2. No unsupported, unknown, missing-target, or log-only effect can be reported as successfully completed.
3. Deployment creates actual game unit records and consumes the correct inventory; connections and bus registration persist valid relationships and enforce capacity.
4. Construction, transfers, and manufacturing produce their intended authoritative game-state changes—not only operational-data flags or console output.
5. Equipment claimed to be operational is demonstrably recognized by the normal simulation/operational path; tests distinguish deployed from operational.
6. Verification checks actual persisted outcomes. An unverified effect cannot make a task or phase pass.
7. The Rake continues to execute the exact Luna-selected task references through its existing four phases and reports accurate per-task and per-phase results.
8. Targeted specs cover success and failure behavior; an integration test validates the Luna task execution path using a safe test database.
9. Documentation is updated with the implemented effect contracts and remaining limitations.
10. The implementation report clearly separates persisted object creation, completed operations, asynchronous/in-progress operations, and sustained simulation behavior.

# Scope boundaries and guardrails

- Scope the first implementation to the task files selected by the local Luna V2 profile/mission plan.
- Do not migrate or normalize the full `missions/tasks_v2/` draft library.
- Do not rewrite the Rake's mission design or replace its phase/task references.
- Do not treat the Rake as a physical transit or full simulation driver; it is an integration harness. Actual transport and game-loop behavior must be verified through their own authoritative systems.
- Do not add a new task/job/production/construction framework if an existing one already owns the operation.
- Do not create task records or a persistent task queue unless the existing architecture requires them; the JSON task definitions are currently execution instructions.
- Do not change JSON data files in git; the V2 Luna fixtures are local/gitignored. Do not commit raw JSON.
- Do not run the full RSpec suite. Follow GalaxyGame's `unset DATABASE_URL` and single-runner rules for targeted specs.
- No commits or pushes without explicit human approval.

# Likely target files (confirm from source before editing)

| File | Purpose |
|---|---|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Effect dispatch and execution handlers |
| `galaxy_game/lib/tasks/luna_mission.rake` | Luna integration harness, selected-task loading, outcome verification |
| `galaxy_game/spec/services/ai_manager/task_execution_engine_v2_spec.rb` | Targeted executor behavior specs |
| `galaxy_game/spec/services/ai_manager/luna_settlement_integration_spec.rb` | Existing Luna integration coverage |
| Existing construction, logistics, inventory, manufacturing, resource-processing, and unit-operation services | Authoritative execution paths to reuse |
| `docs/wiki/services/ai_manager.md` | Current architecture and implementation status |

# Stop conditions

Stop and report findings before implementation if:
- The selected task fixtures, profile, phase files, or manifest cannot be resolved in the local checkout.
- A task's intended physical/domain outcome is ambiguous or conflicts with an established wiki decision.
- An existing domain service's ownership or contract is unclear.
- Completing the operation requires a schema or architectural change outside the AI Manager/task-execution boundary.
- The Rake's destructive inventory reset cannot be isolated safely in tests.
- A selected task depends on an operation that does not exist and implementing it would require a new subsystem.

# Required synthesis report

Before coding, save a synthesis report under `projects/galaxyGame/summaries/` containing:
1. Exact Luna profile → mission plan/phase → task reference chain.
2. Effect coverage matrix for every selected task.
3. Existing service/model/job ownership for each intended operation.
4. Persisted state and verification contract for each effect.
5. Gaps that can be fixed within this task versus blockers requiring owner/review.
6. Targeted test plan and safe Rake validation plan.

Stop after the synthesis and wait for review/approval before implementation.
