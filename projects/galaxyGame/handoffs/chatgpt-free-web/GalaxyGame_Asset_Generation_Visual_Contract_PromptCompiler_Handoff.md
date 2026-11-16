# GalaxyGame Asset-Generation — Visual Contract / PromptCompiler Handoff

**Date:** 2026-09-12  
**Status:** Architecture decision reached; implementation intentionally paused.

## Coordination

User is the final architectural/deployment authority. Qwen sessions may research, review, execute scoped tasks, and recommend sequencing, but do not independently dispatch or change task priority/state unless explicitly instructed.

This handoff consolidates the current ChatGPT-side Asset/UI discussion and should be supplied to the Qwen sessions for cross-session synthesis.

## Current architectural contract

`VISUAL_CONTRACT.md` establishes:

- `asset_id` is the canonical shared identity.
- Gameplay Blueprints do **not** own Visual Profile or Visual Definition references.
- Visual Definition describes asset appearance.
- Visual Profile provides development-time visual composition guidance.
- Render Template provides development-time prompt/output structure.
- Asset-generation tooling is development-time tooling outside the Rails runtime.
- PromptCompiler must not discover repository artifacts by `asset_id`.
- Development-time orchestration is responsible for resolving which artifacts belong together.

Current public interface remains:

```ruby
PromptCompiler.compile(
  asset_id:,
  blueprint_path:,
  operational_data_path:,
  visual_definition_path:,
  render_template_path:
)
```

Do **not** add `visual_profile_path:`.

## Confirmed implementation facts

Direct code/artifact review established:

1. `ProfileResolutionEngine.resolve` requires a valid `visual_profile_id:`.
2. There is no valid Visual-Definition-only fallback.
3. `PromptCompiler#resolve_profiles` currently reads:
   ```ruby
   blueprint_entry[:visual_profile]
   ```
   This is now architecturally invalid.
4. `CompositionRefinery.compose(profile_attributes: ...)` already accepts resolved Visual Profile attributes directly.
5. Existing RH-400 Visual Definition and Render Template contain no Visual Profile reference.
6. Do not modify Blueprints to solve this.
7. Do not hardcode RH-400.
8. Do not introduce repository discovery by `asset_id`.
9. Do not begin implementation until the design is reviewed/accepted.

## Chosen design

**Use a development-time Asset Registry/orchestration mapping from `asset_id → visual_profile_id`.**

The association belongs to the **development-time asset registry/orchestrator**, not to:

- Blueprint
- Operational Data
- Visual Definition
- Render Template
- PromptCompiler

Conceptually:

```text
asset_id
   │
   ▼
Development Asset Registry / Orchestrator
   │
   ├── blueprint
   ├── operational data
   ├── visual definition
   ├── render template
   └── visual_profile_id
             │
             ▼
   ProfileResolutionEngine
             │
             ▼
     profile_attributes
             │
             ▼
     existing composition boundary
             │
             ▼
      CompositionRefinery
             │
             ▼
       frozen image prompt
```

The important separation is:

**Identity resolution:**  
The development orchestrator determines which Visual Profile belongs to an `asset_id`.

**Profile resolution:**  
`ProfileResolutionEngine` converts the resolved `visual_profile_id` into structured `profile_attributes`.

**Prompt composition:**  
The existing compiler/refinery pipeline consumes those attributes.

## Public API decision

Keep the five existing required public keyword arguments.

Do **not** expose `visual_profile_path:`.

Do **not** add `visual_profile_id:` to the public API as part of the immediate implementation.

The preferred implementation is to pass the already-resolved profile attributes through an **internal compiler/context boundary**, using the existing `CompositionRefinery.compose(profile_attributes: ...)` interface.

Before coding, inspect the existing `PromptCompiler → CompositionRefinery` boundary and make the smallest possible change needed to inject those attributes.

## `VISUAL_CONTRACT.md` amendment

The contract should receive a small clarification stating that:

> The development-time asset registry/orchestration layer owns the association between `asset_id` and `visual_profile_id`. This association is not stored in gameplay Blueprints, Visual Definitions, Operational Data, or Render Templates. The orchestrator resolves the Visual Profile before compilation and supplies the resulting profile attributes to the PromptCompiler's internal composition boundary. PromptCompiler does not discover or infer the Visual Profile from `asset_id`.

No broader contract redesign is needed.

## Smallest implementation/test scope

Implementation should be limited to:

1. Define/use a generic development-time asset mapping mechanism.
2. Resolve `visual_profile_id` through `ProfileResolutionEngine`.
3. Remove `PromptCompiler`'s dependency on `blueprint_entry[:visual_profile]`.
4. Feed resolved `profile_attributes` into the existing composition boundary.
5. Preserve the five-keyword public API.
6. Add focused tests proving:
   - `asset_id → visual_profile_id` resolution;
   - profile resolution succeeds;
   - attributes reach composition;
   - Blueprint `visual_profile` is no longer required/read;
   - missing/invalid mappings fail explicitly;
   - implementation is generic and not RH-400-specific.

Do not expand this into a new Rails service, runtime model, API, image-generation integration, or unrelated asset-system refactor.

## PromptCompiler conformance task

The held PromptCompiler conformance task **must be corrected before dispatch** if it currently instructs the compiler to obtain `visual_profile_id` from Blueprint data.

The corrected task must state:

- Visual Profile identity comes from development-time orchestration.
- `PromptCompiler` must not read `blueprint_entry[:visual_profile]`.
- The orchestrator resolves the profile and supplies its attributes to the existing composition boundary.
- The five-keyword public interface remains unchanged.
- No `visual_profile_path:`.
- No Blueprint changes.
- No RH-400 hardcoding.
- No repository discovery by `asset_id`.

## Dependency chain

Current intended sequence:

```text
VISUAL_CONTRACT
       ↓
PromptCompiler / Visual Profile input implementation decision
       ↓
RH-400 Visual Definition normalization/migration
       ↓
Standalone asset-generation execution
       ↓
Broader Asset/UI work
```

The standalone asset-generation task remains paused until the contract/input path is settled.

## Important RH-400 caveat

RH-400 is a **fixture/example**, not the architectural source of truth.

Known existing issue:

- The RH-400 Visual Definition artifact currently has a misleading `.json` extension despite containing Markdown/YAML/fenced JSON rather than being raw valid JSON.

The Visual Contract should document this as a known violation but **must not prescribe or perform its migration as part of the contract task**.

## Immediate next step

No implementation tonight.

Have the Qwen sessions produce a **complete cross-session report** covering:

1. Visual Contract decisions.
2. PromptCompiler current implementation.
3. ProfileResolutionEngine current requirements.
4. CompositionRefinery current interface.
5. RH-400 artifact audit.
6. Existing task status/dependencies.
7. The chosen Visual Profile input design above.
8. Any remaining contradictions or unresolved implementation questions.
9. Exact tasks requiring correction before dispatch.
10. Recommended next deployment sequence.

The report should distinguish **confirmed facts, architectural decisions, recommendations, and unresolved questions** rather than silently reconciling disagreements.
