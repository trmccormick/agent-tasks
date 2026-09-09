---
status: backlog
priority: MEDIUM
type: architecture
system_domain: MANUFACTURING
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/research/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/research/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md \
         projects/galaxy_game/tasks/active/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Research iron/steel production chain to inform material JSON schema
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: architecture
**Created**: 2026-09-09
**Last Updated**: 2026-09-09

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: FAIL — missing YAML frontmatter and Agent Dispatch Interface; both added in this review.
- **Docker Wrapper Check**: N/A
- **MVP Alignment**: VALID — supports future ISRU/manufacturing cost modeling and material-schema repair.
- **MVP Impact Note**: Provides realistic industrial basis for `production.input_materials` and multi-stage production chains.
- **Action Line**: READY FOR LOCAL DISPATCH (after template fixes applied)

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Research-only task; no code changes; fits local planning/research workflow.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

We are designing an in-game economy where local production (e.g., a lunar LOX plant or steel mill) must recover high upfront capital costs while competing against Earth-imported goods. A realistic production-chain model for materials is a prerequisite for:

- Future cost-from-inputs pricing (e.g., a `Manufacturing::CostCalculator` that walks material chains).
- Repairing our material JSON definitions, most of which currently have empty `production.input_materials`.
- Informing how we model ISRU, refining, and manufacturing in a frontier/space economy.

Only ~10 of ~207 material JSON files currently have populated `production.input_materials`. Iron is a concrete example: its production inputs are empty, while its `sources` fields reference `iron_ore` and `magnetite`, which do not exist as material files.

This task produces a research note that defines what a plausible real-world production chain for iron and steel should look like in our data model, using iron/steel as a template for other materials.

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/economy/PRICE_DISCOVERY_LIFECYCLE.md` — EAP concept and price formation.
- `docs/architecture/economy/GCC_MINTING_AND_PRESEEDING.md` — early-economy context.
- `docs/wiki_reorganization/economy/CURRENCIES_AND_ACCOUNTS.md` — currency/ledger context.

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is research-only; do not modify any files.
- ❌ Wrong: Editing material JSON, blueprints, or code.
- ✅ Right: Produce a markdown research note under `summaries/` only.
- Why: We need a stable design basis before touching data or code.

⚠️ **GOTCHA 2**: Do not assume our current material schema is complete or consistent.
- ❌ Wrong: Assuming `production.input_materials` is already meaningful for most materials.
- ✅ Right: Treat the current schema as incomplete; recommend how it *should* work.
- Why: Most materials currently have empty or inconsistent production-chain fields.

⚠️ **GOTCHA 3**: Keep recommendations data/model-focused, not code-focused.
- ❌ Wrong: Designing a specific Ruby service or method signature.
- ✅ Right: Describe what fields/concepts the data model should support.
- Why: Implementation details will be decided later; this task informs the model.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or writing any content, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL
**Status**: backlog → active
**Date**: 2026-09-09

### What I'm About to Do
[2-3 sentences: the goal, the research method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `docs/architecture/economy/PRICE_DISCOVERY_LIFECYCLE.md` | EAP concept and price formation context | pending |
| `docs/wiki_reorganization/proposals/BOOTSTRAP_PRICING.md` | Bootstrap pricing functional form context | pending |
| Existing material JSON examples (e.g., iron, steel if present) | Understand current schema shape | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A markdown research note under `summaries/` that:
- Describes the real-world iron/steel production chain.
- Proposes an in-game material set and high-level input/output relationships.
- Describes a generalization pattern for other materials.
- Recommends how `production.input_materials` should support multi-stage chains and byproducts.

### Critical Gotchas I Will Avoid
- ❌ Modifying material JSON or code — instead ✅ Research-only, output to `summaries/`.
- ❌ Assuming current schema is complete — instead ✅ Treat as incomplete and recommend improvements.
- ❌ Designing specific code interfaces — instead ✅ Keep recommendations data/model-focused.

***

**SYNTHESIS COMPLETE.** Ready to proceed with research.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Only ~10 of ~207 material JSON files have populated `production.input_materials`. Iron is a concrete broken example: its production inputs are empty, while its `sources` fields reference `iron_ore` and `magnetite`, neither of which exist as material files. This blocks any pricing model that wants cost derived from a real input chain and makes it hard to design plausible ISRU/manufacturing mechanics.

**Current behavior**: Most materials have no meaningful production chain; iron references non-existent inputs.
**Expected behavior**: A research note that defines a plausible iron/steel production chain and data-model pattern that can guide future JSON/schema repair.

---

## Files Involved

### Primary Files — you will create these
| File | Purpose | Key Section |
|---|---|---|
| `summaries/YYYY-MM-DD-MATERIAL-CHAIN-IRON-STEEL.md` | Research note on iron/steel production chain and data-model implications | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/materials/iron.json` (if exists) | See current schema shape and gaps |
| `data/json-data/materials/steel.json` (if exists) | See current schema shape and gaps |
| `data/json-data/materials/*.json` (a few others) | Understand schema conventions and variability |
| `docs/architecture/economy/PRICE_DISCOVERY_LIFECYCLE.md` | EAP and price-formation context |

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
git mv projects/galaxy_game/tasks/backlog/research/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md \
       projects/galaxy_game/tasks/active/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md
```

Then open the moved file and change the YAML status field:
status: backlog → status: active

text

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Gather real-world iron/steel production knowledge

Research (using your existing knowledge; no web access required) the real-world iron and steel production chain:

- Mining and ore types (e.g., hematite, magnetite).
- Beneficiation/concentration (crushing, grinding, magnetic separation, flotation).
- Reduction/smelting (blast furnace with coke, direct reduction with gas/hydrogen).
- Steelmaking (BOF, EAF, secondary metallurgy).
- Casting/rolling into semi-finished products (slabs, blooms, billets).
- Major inputs: energy, reductants, fluxes, consumables.
- Major outputs and byproducts: pig iron, crude steel, refined steel, slag, off-gas, dust, scrap.

You do not need to be exhaustively technical; focus on the main stages and inputs/outputs that matter for a game-economic model.

### Step 2 — Map to in-game materials

Propose a minimal but plausible set of game materials to represent this chain. For example:

- `iron_ore`
- `iron_concentrate` (optional, if you want to model beneficiation)
- `pig_iron`
- `crude_steel`
- `steel`
- Possibly `slag`, `steel_scrap`

For each, suggest:

- Whether it should be a distinct material JSON or folded into another.
- What its `production.input_materials` should roughly contain (conceptually, not exact JSON).
- Where the game can reasonably abstract away process detail without breaking plausibility.

### Step 3 — Define a generalization pattern

Describe how this iron/steel pattern could generalize to other materials (e.g., aluminum, copper, titanium, silicon):

- Which aspects are likely universal (ore → concentrate → metal → alloy/semis)?
- Which aspects are material-specific (e.g., electrolysis for aluminum, chlorination for titanium)?
- How might energy intensity, reductants, or fluxes differ?

### Step 4 — Recommend data-model structure

Recommend how `production.input_materials` should be structured to support:

- Multi-stage production chains.
- Alternative routes (e.g., BF-BOF vs EAF for steel).
- Byproducts and yield losses.

Keep this conceptual; do not write actual JSON. Focus on:

- What fields are needed (e.g., input material, quantity, yield, byproducts).
- How to represent alternative processes.
- How to represent energy or non-material inputs if needed.

### Step 5 — Write the research note

Produce a markdown research note suitable for:

`summaries/YYYY-MM-DD-MATERIAL-CHAIN-IRON-STEEL.md`

Include:

- A concise narrative of the real-world iron/steel production chain.
- A proposed in-game material set and high-level input/output relationships.
- A generalization pattern for other materials.
- Recommendations for how `production.input_materials` should support multi-stage chains and byproducts.

Use clear headings and bullet points; keep it readable for both designers and future implementers.

---

## Acceptance Criteria
- [ ] Research note saved as `summaries/YYYY-MM-DD-MATERIAL-CHAIN-IRON-STEEL.md`.
- [ ] Covers real-world chain, in-game material mapping, generalization pattern, and data-model recommendations.
- [ ] Does not modify any code or data files.
- [ ] Clearly distinguishes established industrial practice from game-specific abstractions.

---

## Stop Conditions — escalate to user immediately if:
- You find that existing material JSON is so inconsistent that no coherent pattern can be recommended without design input.
- You determine that additional real-world research (beyond general knowledge) is required to proceed plausibly.
- Any architectural decision is required (e.g., “should we model energy as a separate material?”).

---

## Commit Instructions

Run git commands on **host only** — never inside any container:

```bash
# Add only the new research note
git add summaries/YYYY-MM-DD-MATERIAL-CHAIN-IRON-STEEL.md
git commit -m "research: add iron/steel production-chain note to inform material schema"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md
git commit -m "chore: move 2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md to completed/"
```

---

## Documentation
- [x] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: future tasks to repair material JSON and design `production.input_materials` schema.
**Related tasks**: 
- `2026-09-09-ECONOMIC-DOCUMENTATION-AUDIT-WIKI-COMPLETION.md`
- Bootstrap-pricing research (Perplexity/Claude session 2026-09-09).

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final output**: `summaries/YYYY-MM-DD-MATERIAL-CHAIN-IRON-STEEL.md`

### What was changed
- Created research note under `summaries/`.

### Issues discovered
[Any problems found during research that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: created `summaries/YYYY-MM-DD-MATERIAL-CHAIN-IRON-STEEL.md` | research-only, no code/data changes | next: use note to guide material-schema repair and production-chain modeling