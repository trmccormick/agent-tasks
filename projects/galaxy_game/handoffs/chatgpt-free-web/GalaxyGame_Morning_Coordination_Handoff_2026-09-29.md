# GalaxyGame — Morning Coordination Handoff Summary
Date: 2026-09-29

## Purpose
Current coordination checkpoint for the GalaxyGame multi-agent workflow. Intended for Claude and/or the Qwen planning agent to compile the current project state and next steps.

## 1. Agent Roles / Coordination Model
- User is final architectural/deployment authority and coordinator.
- Claude is the primary backend/coordinating implementation agent.
- ChatGPT handles Asset/UI architecture, review, visual/prompt architecture, and related planning/handoffs.
- Qwen planning agent reviews task files, researches, reconciles dependencies, and recommends sequencing; it does not independently deploy/dispatch implementation work.
- Qwen implementation agent executes scoped tasks using each canonical task file's embedded Agent Dispatch Interface.
- Task lifecycle: `backlog → active → completed`.
- Tracked task moves use `git mv`; never copy/recreate tracked task files.
- Before closeout, verify exactly one copy exists.
- Update `projects/galaxy_game/status.md` and commit closeout in `agent-tasks`.
- Synthesis reports are saved as `.md` under `projects/galaxy_game/summaries/` before implementation work; chat is for questions.

## 2. Asset/UI Task Organization
Asset/UI tasks are consolidated under:
`projects/galaxy_game/tasks/backlog/asset-ui/`

A1 was completed before reorganization.

### Completed Visual Contract
Task:
`2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md`

Output:
`docs/reference/asset-generation/VISUAL_CONTRACT.md`

Do not dispatch this task again. Treat `VISUAL_CONTRACT.md` as the completed canonical architecture reference.

Settled decisions:
- `asset_id` is the canonical shared identity across artifact types.
- Blueprint is the canonical physical/manufacturing/gameplay definition.
- Blueprint does NOT own Visual Profile or Visual Definition references.
- Visual Definition describes how the canonical asset looks.
- Visual Profile is development-time visual composition guidance.
- Render Template is development-time output structure.
- Development-time orchestration / Asset Registry resolves artifact relationships.
- PromptCompiler consumes already-resolved inputs and does not repository-search by `asset_id`.
- Machine-readable Visual Definition `.json` must be valid JSON.
- Human-readable Visual Definition documentation must not be mislabeled as `.json`.
- Asset-generation tooling is development-time tooling outside the Rails runtime.
- RH-400 is a fixture/example, not a hardcoded architectural dependency.

## 3. Current Asset/UI Dispatch Decision — B1
Canonical task:
`projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md`

Latest Qwen planning review: **B1 READY FOR DISPATCH**.

B1 has one hard prerequisite: A1 findings.
A1 is completed via:
`projects/galaxy_game/summaries/2026-09-03-RESEARCH-ASSET-REGISTRY-REALITY-CHECK.md`

### B1 scope guardrails
B1 is narrowly:
**Asset Registry → Visual Definition identity/mapping boundary**

It should:
- map Asset Registry concepts onto existing Visual Definition Template v1.0;
- establish the smallest mapping needed;
- preserve `asset_id` as canonical identity;
- avoid a parallel asset-to-visual relationship model.

It should NOT:
- modify Blueprints to add visual fields;
- add Visual Profile or Render Template relationships to Blueprints;
- modify Visual Profiles or Render Templates;
- implement PromptCompiler conformance;
- redesign the Visual Definition contract;
- hardcode RH-400;
- create a second registry/relationship model.

Dependency direction:
`A1 completed → B1 → B2 + B3 → C-series`

A2 is a B2 prerequisite; A3 is B2/B3; A4 is B2; A5 is B3; A6 is B2.

## 4. Visual Profile / PromptCompiler Decision
The public PromptCompiler interface remains:

`PromptCompiler.compile(asset_id:, blueprint_path:, operational_data_path:, visual_definition_path:, render_template_path:)`

Ownership:
- development-time Asset Registry/orchestration owns `asset_id → visual_profile_id`;
- Blueprint, Visual Definition, Operational Data, and Render Template do not own this mapping;
- PromptCompiler consumes already-resolved profile attributes through its internal composition boundary;
- PromptCompiler must not read `blueprint_entry[:visual_profile]`;
- PromptCompiler must not repository-discover artifacts by asset_id;
- do not add `visual_profile_path:` to the public API at this point.

Eventual implementation scope includes generic mapping, profile resolution, propagation through the existing composition boundary, preserving the five-keyword API, and tests for mapping/resolution/propagation/missing mapping/no Blueprint visual ownership/generic non-RH-400 behavior.

Use the existing task workflow rather than inventing a parallel task structure.

## 5. Standalone Asset-Generation Work — Held / Separate
Task:
`2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md`

Purpose: make `tools/asset_generation/` genuinely runnable as development-time tooling and demonstrate actual end-to-end prompt generation.

Important:
- This is development-time tooling, not a new GalaxyGame runtime/application.
- “Standalone” means no Rails runtime dependencies such as `GalaxyGame::Paths` or `CatalogService`.
- Do not add Rails runtime services/controllers/models/image-generation APIs.
- Do not add Docker infrastructure merely to force the tool into the Rails container.
- RSpec is available in the `web` Docker container, but `tools/asset_generation` is not mounted there.
- There is no Gemfile under `tools/asset_generation`; the Rails Gemfile is elsewhere.
- Do not install global RSpec as a workaround.
- Eventual proof requires both actual spec execution and actual end-to-end prompt generation with real RH-400 data.

This work is currently held because RH-400 contract audit found:
1. PromptCompiler expected Blueprint `visual_profile`, contrary to the settled Visual Contract.
2. `VEHICLE_HARVESTER_ROVER_RH400.json` is Markdown/YAML/prose/fenced-JSON despite `.json` extension.
3. Prior schema did not formally define these boundaries.

Do not fabricate data or work around the mismatch.

## 6. Current RSpec Baseline
Claude is currently running/reviewing the full RSpec baseline to investigate a setup failure.

Current baseline:
- **4,764 examples**
- **142 failures**
- **54 pending**

Top-level:
- `services`: 107 failures
- `models`: ~1
- other folders: ~34

Known service concentrations:
- `mission/`: 8
- `tileset/`: 6
- `lookup/`: 4
- `ai_manager/`: 3
- `manufacturing/`: 2
- `generators/`: 1
- `unit_module_assembly_service_spec.rb`: 8
- `luna_operations_simulation_service_spec.rb`: 2

There are roughly 72–76 additional service failures not yet meaningfully clustered by the current folder summary.

Interpretation:
- This is a failure inventory, not root-cause analysis.
- Do NOT create 142 remediation tasks from the count.
- The 54 pending specs are separate from failures.
- `full_baseline.log` should be grouped by actual failure/error signature before remediation tasks are created.
- Desired flow:
  `failure inventory → unique signatures → shared root causes → remediation clusters → future task files`
- RSpec remediation is a parallel stream and should not block B1 unless detailed evidence establishes a direct dependency.

Earlier baseline was 4,734 examples / 167 failures / 53 pending. The newer 4,764 / 142 / 54 baseline is the current one.

## 7. Future RSpec Remediation Direction
Do not create remediation tasks solely from the folder-level baseline.

Candidate clusters previously identified from the older failure inventory:
- Biome Renderer Asset Integrity
- Terrain Tile Renderer Asset Integrity
- Manufacturing / Component Asset Integration
- Asset/Data Lookup Infrastructure

These are only candidates. Validate them against `full_baseline.log` and actual failure signatures before creating tasks.

Future remediation tasks should require the implementation agent to inspect actual errors and establish root cause before changes. Failure names are discovery evidence, not assumed root causes.

## 8. Immediate Coordination State
Claude is currently occupied with the full RSpec run/review of the setup failure.

Immediate goal: receive Claude's report before changing the remediation backlog.

B1 has already been reviewed by Qwen planning and is approved for dispatch. When dispatched, use the canonical Agent Dispatch Interface embedded in B1 unchanged.

Do not reopen the B1 readiness question unless new evidence contradicts the current state.

## 9. Key Non-Negotiables
- User remains final architectural/deployment authority.
- Do not invent task files when a canonical task already exists.
- Do not duplicate/copy task files.
- Use `git mv` for tracked task lifecycle transitions.
- Verify exactly one task copy before closeout.
- Save synthesis reports as `.md` before implementation work.
- Do not let RSpec aggregate counts drive architecture.
- Do not let unrelated RSpec failures block B1 without evidence.
- Do not add visual fields to Blueprints.
- Do not make Visual Definition own Visual Profile relationships.
- Do not expand PromptCompiler's five-keyword public API without a separate architectural decision.
- Do not hardcode RH-400 into generic tooling.
- Treat `VISUAL_CONTRACT.md` as completed canonical reference.
- Use existing task files and embedded Agent Dispatch Interfaces as the source of truth for dispatch.

## 10. What the Planning Agent Should Compile After Claude's Report
1. Current repository/task lifecycle state.
2. Completed architecture decisions, especially `VISUAL_CONTRACT.md`.
3. Asset/UI dependency chain and B1 dispatch state.
4. Standalone asset-generation status and held issues.
5. Current RSpec baseline and detailed root-cause clusters once available.
6. Any setup-investigation changes required by Claude's findings.
7. Recommended next dispatches based on actual dependencies, without parallel task structures.
8. Explicit separation of:
   - active implementation work,
   - approved-but-not-yet-dispatched work,
   - held work,
   - future remediation work.

This is a coordination checkpoint, not a replacement for canonical task files. Canonical task files, `status.md`, completed summaries, and `VISUAL_CONTRACT.md` remain authoritative.
