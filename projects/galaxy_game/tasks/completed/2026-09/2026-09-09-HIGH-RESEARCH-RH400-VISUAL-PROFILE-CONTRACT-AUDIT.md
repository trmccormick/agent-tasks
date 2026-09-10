---
status: active
priority: HIGH
type: architecture
system_domain: MANUFACTURING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.
You are Research Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/research/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/research/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md
projects/galaxy_game/tasks/active/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
- Tracked file: git mv (never cp or plain mv)
- New/untracked file: mv then git add the final path
- Never leave stale copies in the source folder
- Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md"
Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
Chat is for questions only — never paste synthesis into chat (formatting breaks).

text

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Audit RH-400 visual-profile / blueprint contract for asset-generation tooling
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-09
**Last Updated**: 2026-09-09

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: FAIL — missing YAML frontmatter and Agent Dispatch Interface; both added in this review.
- **Docker Wrapper Check**: N/A
- **MVP Alignment**: VALID — clarifies canonical contract for blueprints, visual profiles, and Visual Definitions before any asset-generation changes.
- **MVP Impact Note**: Prevents tooling from being “fixed” against an incorrect or assumed contract; ensures wiki and implementation are based on reality.
- **Action Line**: READY FOR LOCAL DISPATCH

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

Recent testing of the asset-generation tooling against canonical RH-400 inputs revealed a **contract mismatch**:

- The current **PromptCompiler** expects the blueprint to provide a `visual_profile` reference.  
- The apparent canonical **RH-400 blueprint** currently appears to have **no `visual_profile` field**.  

Separately, the current **RH-400 Visual Definition file**:

- `VEHICLE_HARVESTER_ROVER_RH400.json`  

is **not raw JSON** despite the `.json` extension. It is a **Markdown document** containing YAML frontmatter, prose, and an embedded JSON code block. The current PromptCompiler attempts to `JSON.parse` the entire file.

The goal of this task is to **establish the authoritative contract** for:

- Blueprints (including operational data).  
- Visual Definitions.  
- Visual Profiles.  
- Render Templates.  
- How these relate to each other and to asset generation.

**Do NOT assume either side is wrong and do NOT modify anything to make the tooling pass.** This is a research/audit task only.

**Relevant Files / Areas** — inspect as needed, but do not edit:
- Blueprint JSON files (e.g., RH-400 and related).  
- Visual Definition files (e.g., `VEHICLE_HARVESTER_ROVER_RH400.json`).  
- PromptCompiler and any asset-generation tooling.  
- Any visual-profile or render-template artifacts.  
- Any canonical documentation or schemas that define these contracts.

> If a doc or schema doesn’t exist, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is research-only; do not modify any files.
- ❌ Wrong: Editing blueprints, Visual Definitions, PromptCompiler, or any tooling.
- ✅ Right: Produce a markdown research note under `summaries/` only.
- Why: We need a stable design basis before touching code or data.

⚠️ **GOTCHA 2**: Do not assume “file exists” or “tooling runs” means the contract is correct.
- ❌ Wrong: Concluding the contract is fine because the tooling “mostly works”.
- ✅ Right: Inspect actual file contents, schemas, and code assumptions; note mismatches explicitly.
- Why: The whole point is to surface hidden contract mismatches, not paper over them.

⚠️ **GOTCHA 3**: Distinguish clearly between:
- What canonical documentation/schema **explicitly establishes**.  
- What the current files **actually contain**.  
- Any **recommendations/inferences** you make (must be clearly labeled as such).

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or writing any content, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT
**Status**: backlog → active
**Date**: 2026-09-09

### What I'm About to Do
[2-3 sentences: the goal, the research method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| RH-400 blueprint JSON(s) | Inspect visual_profile and related fields | pending |
| VEHICLE_HARVESTER_ROVER_RH400.json (Visual Definition) | Inspect structure (Markdown+YAML+JSON) | pending |
| PromptCompiler and asset-generation code | Inspect assumptions about visual_profile and Visual Definition format | pending |
| Any visual-profile / render-template artifacts | Map relationships | pending |
| Canonical docs/schemas (if any) | Establish intended contract | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A markdown research note under `summaries/` that:
- Establishes the authoritative contract for blueprints, Visual Definitions, visual profiles, and render templates.
- Clearly separates: what docs/schema say, what files actually contain, and any recommendations.
- Answers the six key questions (see Problem Statement) for RH-400 specifically.
- Provides actionable guidance for reconciling tooling with the real contract.

### Critical Gotchas I Will Avoid
- ❌ Modifying code or data — instead ✅ Research-only, output to `summaries/`.
- ❌ Assuming tooling assumptions = canonical contract — instead ✅ Verify against docs/schemas and actual file contents.
- ❌ Blurring lines between fact and recommendation — instead ✅ Label recommendations explicitly.

***

**SYNTHESIS COMPLETE.** Ready to proceed with research.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Asset-generation tooling was tested against canonical RH-400 inputs, and a **contract mismatch** was found:

- The **PromptCompiler** expects the blueprint to provide a `visual_profile` reference.  
- The apparent canonical **RH-400 blueprint** currently appears to have **no `visual_profile` field**.  

Separately, the **RH-400 Visual Definition file**:

- `VEHICLE_HARVESTER_ROVER_RH400.json`  

is **not raw JSON** despite the `.json` extension. It is a **Markdown document** containing YAML frontmatter, prose, and an embedded JSON code block. The current PromptCompiler attempts to `JSON.parse` the entire file.

This task must answer the following questions for RH-400 specifically:

1. Is `visual_profile` supposed to be owned by the blueprint?  
2. If not, which canonical artifact owns the relationship between an asset and its visual profile?  
3. What is the authoritative machine-readable Visual Definition contract?  
4. Is `VEHICLE_HARVESTER_ROVER_RH400.json` intentionally a Markdown-wrapped Visual Definition, or is that itself a data-contract problem?  
5. Does the current PromptCompiler reflect the intended canonical contract, or did the Phase 1 asset-generation migration make assumptions that were never reconciled with the actual data model?  
6. For RH-400 specifically, identify the canonical blueprint, operational-data, visual-definition, visual-profile, and render-template sources and their relationships.

**Most importantly**, distinguish:

- What the existing canonical documentation/schema **explicitly establishes**.  
- What the current files **actually contain**.  
- Any **recommendations/inferences** you make (must be clearly labeled as such).

**Current behavior**: Unclear whether the PromptCompiler’s assumptions match the intended canonical contract; RH-400 Visual Definition is Markdown+YAML+JSON, not raw JSON.  
**Expected behavior**: A research note that establishes the authoritative contract and clearly separates fact from recommendation, so future implementation can reconcile tooling correctly.

---

## Files Involved

### Primary Files — you will create these
| File | Purpose | Key Section |
|---|---|---|
| `summaries/YYYY-MM-DD-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md` | Research note on RH-400 visual-profile / blueprint contract | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| RH-400 blueprint JSON(s) (e.g., under `data/json-data/blueprints/` or similar) | Inspect `visual_profile` and related fields |
| `VEHICLE_HARVESTER_ROVER_RH400.json` (Visual Definition) | Inspect structure (Markdown+YAML+JSON) |
| PromptCompiler and asset-generation code | Inspect assumptions about `visual_profile` and Visual Definition format |
| Any visual-profile or render-template artifacts | Map relationships |
| Any canonical docs/schemas that define these contracts | Establish intended contract |

Exact paths will vary; search for relevant files by name/pattern if needed.

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
git mv projects/galaxy_game/tasks/backlog/research/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md \
       projects/galaxy_game/tasks/active/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md
```

Then open the moved file and change the YAML status field:
status: backlog → status: active

text

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Inventory relevant artifacts

Search the codebase for:

- RH-400 blueprint JSON(s).  
- `VEHICLE_HARVESTER_ROVER_RH400.json` (Visual Definition).  
- PromptCompiler and any asset-generation tooling.  
- Any visual-profile or render-template files/modules.  
- Any canonical docs/schemas that define blueprints, Visual Definitions, visual profiles, or render templates.

For each, note:

- Path and date (if available).  
- High-level structure (e.g., raw JSON vs Markdown+YAML+JSON).  
- Whether it appears canonical, derived, or experimental.

Do not modify anything; just build a mental map of what exists and where.

### Step 2 — Inspect RH-400 blueprint(s)

Examine the RH-400 blueprint JSON(s) to determine:

- Whether a `visual_profile` field exists.  
- If not, what fields relate to visual/asset generation (e.g., `visual_definition`, `render_template`, `asset_id`, etc.).  
- How operational data is structured and whether it references visual artifacts.

Record:

- Exact field names and types.  
- Any references to external visual files or profiles.  
- Any discrepancies with what the PromptCompiler expects.

### Step 3 — Inspect RH-400 Visual Definition

Examine `VEHICLE_HARVESTER_ROVER_RH400.json` to determine:

- Whether it is raw JSON or Markdown+YAML+embedded-JSON.  
- What the YAML frontmatter contains (if present).  
- What the embedded JSON block defines (visual properties, geometry, materials, etc.).  
- Whether this structure appears intentional or like a data-contract bug.

Record:

- Exact structure (Markdown sections, YAML keys, JSON schema).  
- Any comments or metadata that indicate intent.  
- How (or whether) this matches any canonical Visual Definition schema.

### Step 4 — Inspect PromptCompiler and asset-generation assumptions

Examine the PromptCompiler and related asset-generation code to determine:

- What it expects from the blueprint (e.g., `visual_profile` field).  
- How it loads and parses Visual Definition files (e.g., `JSON.parse` on the entire file).  
- Any assumptions about file formats (raw JSON vs Markdown+YAML+JSON).  
- Whether these assumptions match any canonical docs/schemas or are purely implementation-driven.

Record:

- Exact expectations and parsing logic.  
- Any mismatches with actual blueprint/Visual Definition contents.  
- Any comments or docs in the code that indicate intended contract.

### Step 5 — Map canonical sources and relationships

For RH-400 specifically, identify:

- The canonical **blueprint** source.  
- The canonical **operational-data** source (if separate).  
- The canonical **visual-definition** source.  
- The canonical **visual-profile** source (if it exists as a distinct artifact).  
- The canonical **render-template** source (if it exists).  

Map their relationships:

- Which references which?  
- Which is authoritative for what?  
- Where are the mismatches or ambiguities?

### Step 6 — Write the research note

Produce a markdown research note suitable for:

`summaries/YYYY-MM-DD-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`

Include:

- Executive summary of findings.  
- Answers to the six key questions (see Problem Statement), clearly separated into:
  - What canonical docs/schema explicitly establish.  
  - What current files actually contain.  
  - Any recommendations/inferences (clearly labeled as such).  
- Detailed breakdown of RH-400 blueprint, Visual Definition, PromptCompiler assumptions, and relationships.  
- Recommendations for reconciling tooling with the real contract (clearly labeled as recommendations).

Use clear headings and bullet points; keep it readable for both designers and future implementers.

---

## Acceptance Criteria
- [ ] Research note saved as `summaries/YYYY-MM-DD-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`.
- [ ] Answers all six key questions for RH-400.
- [ ] Clearly separates: what docs/schema say, what files contain, and recommendations.
- [ ] Does not modify any code or data files.
- [ ] Provides actionable guidance for reconciling tooling with the real contract.

---

## Stop Conditions — escalate to user immediately if:
- You find that the contract situation is so inconsistent that no coherent pattern can be recommended without design input.  
- You determine that additional research (beyond code inspection) is required to proceed plausibly.  
- Any architectural decision is required (e.g., “should we treat X as canonical?”).

---

## Commit Instructions

Run git commands on **host only** — never inside any container:

```bash
# Add only the new research note
git add summaries/YYYY-MM-DD-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md
git commit -m "research: add RH-400 visual-profile / blueprint contract audit"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-09-HIGH-RESEARCH-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md
git commit -m "chore: move RH-400 visual-profile contract audit to completed/"
```

---

## Documentation
- [x] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none  
**Blocks**: future asset-generation tooling reconciliation and related implementation tasks.  
**Related tasks**: 
- `2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md` (separate lane).  
- `2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md` (separate lane).

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  
**Final output**: `summaries/YYYY-MM-DD-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md`

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

HANDOFF SUMMARY: created `summaries/YYYY-MM-DD-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md` | research-only, no code/data changes | next: use audit to reconcile asset-generation tooling with canonical contract