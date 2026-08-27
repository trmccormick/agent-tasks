---
status: completed
priority: MEDIUM
type: architecture
system_domain: ASSET_PIPELINE
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/current/2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md
projects/galaxy_game/tasks/active/2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md
Then open the moved file and change: status: backlog → status: active
Paste the find command output confirming exactly one result before proceeding:
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks
-name "2026-08-23-MEDIUM-ARCHITECTURE-ASSET-PROMPT-COMPILER-CONTRACT.md"

READ FIRST (after Step 0): Task file contains the full contract content to write, below.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: 2026-08-23-ASSET-PROMPT-COMPILER-CONTRACT.md
Chat is for questions only — never paste synthesis into chat.

This is a DOCUMENTATION task — you are writing a new spec file, not implementing code.
Do not write any Ruby/Python implementation. Do not touch RH-400's actual JSON files.


---

# TASK: Write ASSET_PROMPT_COMPILER_CONTRACT.md

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: architecture
**Created**: 2026-08-23
**Last Updated**: 2026-08-23

---

## Context

Across two independent design sessions (one internal, one with ChatGPT, both converging on the same shape), the project has settled on an "Asset Compiler" architecture for turning canonical unit/blueprint data into image-generation prompts: Blueprint → Operational Data → Visual Definition → Visual Profile → Render Template → generated prompt. The intent is that this contract is model-agnostic — Qwen is today's implementer, but the contract itself should not assume Qwen specifically, so a future model swap doesn't require redesigning the pipeline.

This task is to write that contract down as a standalone spec document, before any v1 code is implemented against it — so the implementation follows the contract rather than the contract being reverse-engineered from whatever code gets written first.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md`
- `docs/new_agent/rules/GUARDRAILS.md`
- `VISUAL_DEFINITION_TEMPLATE.md` — the Visual Definition schema this contract will read from
- `ASSET_GENERATION_ARCHITECTURE.md` v1.1 — the broader asset pipeline architecture this contract is a component of
- "GalaxyGame Production Asset Render Template v1.0" — the render-template layer this contract's output feeds into
- Any existing `precision_industrial_v1` Visual Profile file — for understanding what a real dependency-chain file actually looks like

> If any of these docs can't be located, note it in your completion report — do not proceed to write the contract without confirming the schemas you're referencing actually match reality.

---

## Problem Statement

**Current state**: No standalone contract document exists. The rules for what the compiler may/may not do are scattered across chat/design-session history (this task file's Context section, and prior design docs), not written as a single authoritative reference.

**Required output**: `ASSET_PROMPT_COMPILER_CONTRACT.md`, containing the following sections (content below is the agreed direction from two converged design sessions — write it up formally, verify it against actual current schemas where you can, and flag any place where the real files don't match what's described here):

### Inputs
The compiler reads a fixed dependency chain per asset:
Blueprint (unit_blueprint.json or component_blueprint.json) → Operational Data → Visual Definition → Visual Profile → Production Asset Render Template.
References between files are resolved via IDs stored in the JSON (e.g. `"visual_profile": "precision_industrial_v1"`) — the compiler never needs to be told file paths directly; it follows the reference chain.

### Dependency Resolution
For a given asset, walk the chain in order. If a referenced ID cannot be resolved to a real file, this is a hard error (see Error Behavior), not a silent skip.

### Required Fields
[FILL IN by implementing agent: enumerate the actual required fields per layer, cross-checked against VISUAL_DEFINITION_TEMPLATE.md's schema — do not invent field names, verify against the real template file]

### Validation Rules
- Every referenced file in the chain must exist and resolve.
- Required fields (per template) must be present and non-empty.
- `recognition_features` should contain a minimum of 4 items — below that is a WARNING (not a hard failure), reported explicitly with count.

### Allowed Transformations (Compile Mode)
- Read canonical files.
- Resolve ID references into file paths.
- Substitute placeholders into the render template to produce a prompt.
- Validate required fields are present.
- Generate the output prompt file plus a provenance/metadata header.
- Report warnings for soft gaps (e.g. sparse recognition_features) without blocking compilation, unless a required field is entirely missing (hard error).

### Forbidden Transformations (Compile Mode)
- Never invent, embellish, or infer asset characteristics not present in the source data.
- Never modify Visual Profiles, Render Templates, Blueprints, or Visual Definitions.
- Never auto-correct a broken reference — report it as an error with a suggestion (see Error Behavior), but do not guess and proceed.

### Suggestion Mode (Assist Mode — separate from Compile Mode)
A distinct, explicitly separate mode: may derive CANDIDATE recognition_features or other sparse-field suggestions from the blueprint's own stated capabilities/components, presented as suggestions only. Assist Mode NEVER writes to canonical files. A human must review and manually apply any accepted suggestion. Compile Mode and Assist Mode must be clearly distinguishable in output (e.g. a suggestion is never mistaken for a validated fact).

### Output Format
Generated prompt saved to a file (not printed only), with a provenance header containing: asset_id, prompt_type, generated_from (the exact resolved file+version references used), generated_by, date.

### Version Tracking
NOT a v1 requirement. Visual Definitions, Visual Profiles, and Render Templates currently have no version field (unlike Blueprints, which already have `metadata.version`/`template_compliance`). This contract should note version tracking as a planned v2 addition, and specify the intended future field name/format (e.g. `source_versions:` block in the provenance header) so v2 implementation doesn't have to guess the intended shape, but v1 does not need to implement it.

### Error/Warning Behavior
- Missing required file/reference → hard error, includes the broken reference and a "did you mean: X?" suggestion if a close-match filename exists in the same directory.
- Missing required field → hard error, names the field and which file it was expected in.
- Sparse-but-present field (e.g. recognition_features < 4 items) → warning, not a hard failure; compilation still proceeds.
- All errors/warnings should be human-readable and actionable, not just a stack trace.

### Regeneration Rules
NOT a v1 requirement (depends on version tracking, which is v2). Note as future scope only.

---

## Acceptance Criteria
- [ ] `ASSET_PROMPT_COMPILER_CONTRACT.md` exists as a standalone file (location: alongside the other 8 canonical design docs — confirm the correct directory before creating)
- [ ] All sections above are present, with `[FILL IN]` sections resolved against actual current schema files, not guessed
- [ ] Explicitly separates Compile Mode (v1, deterministic, never modifies source) from Assist Mode (suggestion-only, human-approval-required)
- [ ] Explicitly marks version tracking and regeneration rules as v2/future scope, not v1 requirements
- [ ] Does not duplicate content already owned by another canonical doc (per this project's "one owning doc per concept" convention) — link/reference instead of restating

---

## Stop Conditions — escalate to user immediately if:
- The referenced canonical docs (VISUAL_DEFINITION_TEMPLATE.md, ASSET_GENERATION_ARCHITECTURE.md, the Render Template doc) can't be located
- The actual current schema for Visual Definitions/Profiles materially disagrees with what's described in this task's Context section (report the discrepancy, do not silently reconcile by picking one)

---

## Dependencies
**Blocked by**: none
**Blocks**: the v1 Asset Compiler build task (not yet filed) — that task should implement against this contract, not the other way around
**Related tasks**: none filed yet — see [[asset-pipeline]] for full design history

---

## Completion Report
- Contract written: 2026-08-24
- Schema verification: VISUAL_DEFINITION_TEMPLATE.md, ASSET_GENERATION_ARCHITECTURE.md, PRODUCTION_ASSET_RENDER_TEMPLATE_V1.0.md, VISUAL_PROFILE_precision_industrial_v1.md all confirmed at expected paths
- I-beam Mk1 worked example: validated against existing production assets on disk
- component_blueprint.json gap: confirmed no instances exist; contract establishes the pattern
- Contract committed as docs/reference/asset-generation/ASSET_PROMPT_COMPILER_CONTRACT.md (commit 01b53665)

**Git-hygiene note**: The task file's status change (backlog → active) was bundled inside commit `5ee70c1` labeled "Complete fabrication_plant blueprint task" — a different task's commit message. File state is correct; only the commit label is misleading. No action needed (rewriting history carries more risk than the cosmetic issue is worth).

## Handoff Summary
Task closed. Contract drafted and committed (01b53665). Ready for strategist review before v1 implementation task is filed.
Contract drafted and committed. Ready for strategist review before v1 implementation task is filed.