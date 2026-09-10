---
status: backlog
priority: HIGH
type: architecture
system_domain: ASSET_GENERATION
mvp_alignment: TOOLING_INFRASTRUCTURE
local_worker_safe: true
created: 2026-09-09
last_updated: 2026-09-09
---

# TASK: Define canonical visual contract for asset generation

## Agent Dispatch Interface

**Task File**: `projects/galaxy_game/tasks/backlog/architecture/2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md`
**Repo**: `/Users/tam0013/Documents/git/agent-tasks`
**Type**: architecture
**Priority**: HIGH
**System Domain**: ASSET_GENERATION
**MVP Alignment**: TOOLING_INFRASTRUCTURE
**Local Worker Safe**: true

**Synthesis Report Required**: YES — post synthesis report in chat BEFORE starting any work.

---


## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS — YAML frontmatter and Agent Dispatch Interface present.
- **Docker Wrapper Check**: N/A
- **MVP Alignment**: VALID — establishes authoritative contract for asset generation, separating canonical game data from development-time visual tooling.
- **MVP Impact Note**: Prevents future tooling changes from being made against an implicit or assumed contract; provides stable basis for PromptCompiler and blueprint/VD evolution.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Architecture/documentation task; no code or data changes; fits local planning/research workflow.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

This task is to **document that contract** in a canonical reference file.

**Relevant Files / Areas** — read as context, but do not edit:
- Perplexity's RH-400 audit (`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`).
- Existing blueprint JSONs (e.g., RH-400 — search `galaxy_game/data/blueprints/` or `data/json-data/blueprints/`).
- Existing Visual Definition files (`/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`).
- PromptCompiler and asset-generation tooling (`/Users/tam0013/Documents/git/galaxyGame/tools/asset_generation/prompt_compiler.rb`).

> If a doc or schema doesn’t exist, do not create one during this task. This task’s output (`VISUAL_CONTRACT.md`) is the new canonical contract.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is architecture/documentation only; do not modify any code or data.
- ❌ Wrong: Editing blueprints, Visual Definitions, PromptCompiler, or any tooling.
- ✅ Right: Produce a markdown contract document under `docs/reference/asset-generation/`.
- Why: We need a stable design basis before touching code or data.

⚠️ **GOTCHA 2**: Do not prescribe migration details for existing files (e.g., RH-400).
- ❌ Wrong: Saying “rename `VEHICLE_HARVESTER_ROVER_RH400.json` to `.md` and generate a `.json` derivative”.
- ✅ Right: State that the current file violates the `.json` machine-readable convention and requires migration; migration strategy is a separate task.
- Why: Migration details are implementation decisions, not part of the contract definition.

⚠️ **GOTCHA 3**: Distinguish identity resolution from file discovery.
- ❌ Wrong: Implying “shared by `asset_id`” means “PromptCompiler should search the repo for files matching `asset_id`”.
- ✅ Right: State that `asset_id` is the canonical identity; development orchestration determines which Visual Definition, Visual Profile, and Render Template apply; PromptCompiler consumes those already-resolved inputs.
- Why: Prevents later misinterpretation that tooling should infer file paths from `asset_id` alone.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or writing any content, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (post in chat before starting work):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT
**Status**: backlog → active
**Date**: 2026-09-09

### What I'm About to Do
[2-3 sentences: the goal, the research method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md` | RH-400 audit findings | pending |
| Existing blueprint JSONs | Understand current structure | pending |
| Existing Visual Definition files | Understand current format | pending |
| `/Users/tam0013/Documents/git/galaxyGame/tools/asset_generation/prompt_compiler.rb` | Understand current assumptions | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A canonical contract document at `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md` that:
- Defines the roles of Asset Registry, Blueprint, Operational Data, Visual Definition, Visual Profile, Render Template.
- Establishes `asset_id` as the shared canonical identity.
- Clarifies that PromptCompiler consumes already-resolved inputs, not inferred by `asset_id`.
- States that machine-readable Visual Definitions must be valid JSON if `.json`; Markdown+YAML+JSON is a human-readable source format, not a machine interface.
- Notes that some existing files (e.g., RH-400 VD) violate the contract and require migration, but does not prescribe how.

### Critical Gotchas I Will Avoid
- ❌ Modifying code or data — instead ✅ Documentation only.
- ❌ Prescribing migration details — instead ✅ Flagging violations and deferring migration.
- ❌ Blurring identity vs file discovery — instead ✅ Explicitly separating orchestration from compilation.

***

**SYNTHESIS COMPLETE.** Ready to proceed with architecture documentation.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Asset-generation tooling currently operates with **implicit, inconsistent assumptions** about:

- Whether blueprints must carry `visual_profile` or `visual_definition` fields.  
- Whether Visual Definition files are raw JSON or Markdown+YAML+embedded-JSON.  
- How `asset_id` relates to Visual Definitions, Visual Profiles, and Render Templates.  
- What the PromptCompiler is actually allowed to assume about its inputs.

This has led to:

- Contract mismatches (e.g., RH-400 blueprint has no `visual_profile`, but PromptCompiler expects one).  
- Format mismatches (e.g., `VEHICLE_HARVESTER_ROVER_RH400.json` is Markdown+YAML+JSON, not raw JSON).  
- Risk of future changes being made against an ill-defined contract.

**Current behavior**: Implicit, inconsistent assumptions; some files violate basic format conventions.  
**Expected behavior**: A canonical contract document that clearly defines roles, relationships, and format expectations, so future tooling changes are made against a stable, explicit contract.

---

## Files Involved

### Primary Files — you will create these
| File | Purpose | Key Section |
|---|---|---|
| `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md` | Canonical contract for asset generation visuals | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md` | RH-400 audit findings |
| Existing blueprint JSONs (e.g., RH-400) | Understand current structure |
| `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` | Understand current format |
| `/Users/tam0013/Documents/git/galaxyGame/tools/asset_generation/prompt_compiler.rb` | Understand current assumptions |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/architecture/2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md \
       projects/galaxy_game/tasks/active/2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md
```

Then open the moved file and change the YAML status field:
status: backlog → status: active

text

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Review RH-400 audit and existing artifacts

Read:

- Perplexity's RH-400 audit (`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`).  
- A few representative blueprint JSONs (search `galaxy_game/data/blueprints/` or `data/json-data/blueprints/`).
- A few Visual Definition files (including `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`).  
- PromptCompiler code (`/Users/tam0013/Documents/git/galaxyGame/tools/asset_generation/prompt_compiler.rb`, especially its public interface and assumptions).

Goal: understand the current state and mismatches, not to design fixes yet.

### Step 2 — Draft VISUAL_CONTRACT.md

Create:

`/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md`

Structure it with at least these sections:

1. **Purpose and scope**  
   - What this contract covers (asset generation visuals).  
   - What it does not cover (runtime game logic, non-visual asset relationships).

2. **Canonical identities and roles**  
   - `asset_id` as the shared canonical identity.  
   - Roles of:
     - Asset Registry.  
     - Blueprint.  
     - Operational Data.  
     - Visual Definition.  
     - Visual Profile.  
     - Render Template.  

3. **Separation of concerns**  
   - Canonical game data (Asset Registry, Blueprint, Operational Data) vs development-time visual artifacts (Visual Definition, Visual Profile, Render Template).  
   - Explicit statement: blueprints do **not** carry `visual_profile` or `visual_definition` fields.  
   - `asset_id` is the link; development orchestration determines which visual artifacts apply.

4. **PromptCompiler contract**  
   - Inputs: `asset_id`, `blueprint_path`, `operational_data_path`, `visual_definition_path`, `visual_profile_path`, `render_template_path` (or equivalent).  
   - Explicit statement: PromptCompiler consumes already-resolved inputs; it does **not** search the repo by `asset_id`.  
   - Output: frozen image prompt for image-generation models.

5. **Visual Definition format contract**  
   - Human-readable Visual Definition: Markdown (`.md`) with optional YAML frontmatter and embedded JSON.  
   - Machine-readable Visual Definition: raw, valid JSON (`.json`) with no Markdown or YAML.  
   - Explicit statement: any file named `*.json` must be valid JSON; Markdown-wrapped VDs must not use the `.json` extension.

6. **Known violations and migration**  
   - Explicitly note that some existing files (e.g., `VEHICLE_HARVESTER_ROVER_RH400.json`) violate the `.json` machine-readable convention.  
   - State that these require migration, but **do not prescribe** how (e.g., don’t say “rename to `.md` and generate `.json`”).  
   - Migration strategy is a separate task.

7. **Boundaries and non-goals**  
   - Asset-generation tooling is development-time and outside Rails runtime.  
   - Qwen is an orchestration/consumer of this tooling, not the owner of the contract.  
   - RH-400 is a fixture/example, not a hardcoded architectural dependency.

Use clear headings and bullet points; keep it readable for both designers and future implementers.

### Step 3 — Review and refine

Before finalizing:

- Ensure the document clearly separates identity resolution from file discovery.  
- Ensure it does not prescribe migration details for existing files.  
- Ensure it aligns with the architectural decisions summarized in the session handoff (blueprints do not get visual fields; `asset_id` is the shared key; PromptCompiler consumes resolved inputs).

Make any necessary refinements for clarity and precision.

---

## Acceptance Criteria
- [ ] `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md` created and committed.
- [ ] Document clearly defines roles of Asset Registry, Blueprint, Operational Data, Visual Definition, Visual Profile, Render Template.
- [ ] Document establishes `asset_id` as the shared canonical identity and separates identity resolution from file discovery.
- [ ] Document states that blueprints do not carry `visual_profile` or `visual_definition` fields.
- [ ] Document defines Visual Definition format contract (Markdown for human-readable, raw JSON for machine-readable).
- [ ] Document notes known violations (e.g., RH-400 VD file) without prescribing migration details.
- [ ] Document does not modify any code or data files.

---

## Stop Conditions — escalate to user immediately if:
- You find that the contract situation is so inconsistent that no coherent pattern can be recommended without design input.  
- You determine that additional research (beyond the RH-400 audit and existing artifacts) is required to proceed plausibly.  
- Any architectural decision is required beyond what’s already stated in this task and the RH-400 audit.

---

## Commit Instructions

Run git commands on **host only** — never inside any container:

```bash
# Add only the new contract doc
git add /Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md
git commit -m "docs: add canonical VISUAL_CONTRACT.md for asset generation"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-09-HIGH-ARCHITECTURE-VISUAL-CONTRACT.md
git commit -m "chore: move VISUAL_CONTRACT architecture task to completed/"
```

---

## Documentation
- [x] No doc changes needed (this task creates the primary doc)
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: Perplexity's RH-400 visual-profile / blueprint contract audit (`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`).
**Blocks**: Future PromptCompiler adjustments, blueprint/VD migrations, and broader asset/UI work that depends on a stable visual contract.  
**Related tasks**: 
- `2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md` (separate lane).  
- `2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md` (separate lane).

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  
**Final output**: `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md`

### What was changed
- Created canonical contract document under `/Users/tam0013/Documents/git/galaxyGame/docs/reference/asset-generation/`.

### Issues discovered
[Any problems found during documentation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: created `docs/reference/asset-generation/VISUAL_CONTRACT.md` | architecture/documentation only, no code/data changes | next: use contract to guide PromptCompiler and blueprint/VD evolution