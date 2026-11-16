
---
status: backlog
priority: MEDIUM
type: architecture
system_domain: TERRA_SIM
mvp_alignment: OTHER
local_worker_safe: true
---

> **[FILL IN — Tracy]**: `system_domain` and `mvp_alignment` above are Claude's best-guess categorization. Please confirm or correct before dispatch.

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Investigation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md \
         projects/galaxy_game/tasks/active/2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md"
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

Everything else (details, gotchas, acceptance criteria, investigation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Audit StarSim hydrosphere composition generation and importer pipeline
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: architecture
**Created**: 2026-08-23
**Last Updated**: 2026-09-16

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS — all required sections present after 2026-09-16 rewrite
- **Docker Wrapper Check**: N/A — no application code executed, no writes
- **MVP Alignment**: VALID — hydrosphere composition gap affects ISRU production chain (liquid materials determine what ISRU can extract)
- **MVP Impact Note**: Procedural bodies get NO hydrosphere composition data; this means any future ISRU/liquid-extraction logic on procedurally-generated planets has no input to work with
- **Action Line**: READY FOR LOCAL DISPATCH — all [FILL IN] resolved against verified codebase state

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires grep tracing through StarSim services and cross-referencing prior research findings
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **Prior research**: `summaries/2026-08-23-RESEARCH-HYDROSPHERE-COMPOSITION-SCHEMA.md` — sibling task already inventoried all composition shapes in sol-complete.json and sol.json (flat object vs array of objects inconsistency)
4. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
A prior material-file audit found `hydrosphere_attributes.composition` in sol-complete.json uses two inconsistent shapes: flat object (`{"H2O": 100.0}`) for most bodies, and array of objects (`[{"compound":"H2O","percentage":96.5}]`) only on Earth. The sibling task (`2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md`) audited the schema consistency across Sol data. This task audits the **production side**: how does that data get produced — both for hand-authored Sol bodies (via the importer/loader) and for procedurally-generated bodies (via StarSim's generation logic).

**Verified findings from codebase audit (2026-09-16):**
- **Importer**: `SystemBuilderService#create_hydrosphere` at `galaxy_game/app/services/star_sim/system_builder_service.rb:236` passes composition through with zero normalization — uses fallback chain `body_data[:hydrosphere_attributes] || body_data["hydrosphere_attributes"] || body_data[:hydrosphere] || body_data["hydrosphere"]`, no schema enforcement
- **Procedural generator**: `PlanetBuilder#generate_hydrosphere_data` at `galaxy_game/app/services/star_sim/planet_builder.rb:114` emits only `total_water_mass` + `surface_coverage` — NO composition field at all. Same gap in `ProceduralGenerator#generate_procedural_terrestrial` at line 737
- **Canonical schema**: None exists. `docs/architecture/simulation/hydrosphere_system.md` describes composition format but nothing enforces it

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/architecture/simulation/hydrosphere_system.md` — hydrosphere design docs (describes composition format, does not enforce it)
- Prior research: `summaries/2026-08-23-RESEARCH-HYDROSPHERE-COMPOSITION-SCHEMA.md`

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Problem Statement

**What is known (from codebase audit):**
1. **Importer side**: `SystemBuilderService` passes `hydrosphere_attributes.composition` through unchanged — no validation, no normalization. Two inconsistent shapes exist in Sol data (flat object vs array of objects).
2. **Procedural generation gap**: Neither `PlanetBuilder#generate_hydrosphere_data` nor `ProceduralGenerator` populates a `composition` field at all. Procedurally-generated bodies get only `total_water_mass` and `surface_coverage` with hardcoded stub values (`0.001 * mass`, `0.1`).
3. **No canonical schema**: The hydrosphere design doc describes composition format but nothing enforces it.

**What this task determines:**
- Whether the importer gap (passthrough of inconsistent shapes) is a real problem or acceptable
- Whether the procedural generation gap (no composition at all) needs to be filled, and if so, what calculation approach matches the data-driven-generation principle established for magnetosphere_strength
- What the minimal schema decision would look like

**Expected behavior**: Both importer and generator should converge on one agreed schema, and procedurally-generated bodies should receive real (not stubbed) composition data consistent with their generated hydrosphere makeup.

---

## Files Involved

### Primary Files — read-only investigation targets (DO NOT EDIT)
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/star_sim/system_builder_service.rb` | Importer/loader for sol-complete.json into running system | `create_hydrosphere` at line 236 — passes composition through with zero normalization |
| `galaxy_game/app/services/star_sim/planet_builder.rb` | Procedural planet generation (non-terrestrial) | `generate_hydrosphere_data` at line 114 — emits no composition field |
| `galaxy_game/app/services/star_sim/procedural_generator.rb` | Procedural terrestrial body generation | Line 737 — hydrosphere_attributes with hardcoded stubs, no composition |
| `data/json-data/star_systems/sol-complete.json` | Source data for hand-authored bodies | `hydrosphere_attributes.composition` — two inconsistent shapes confirmed |

### Reference Files — read for context, do not edit
| File | Why You Need It |
|---|---|
| `summaries/2026-08-23-RESEARCH-HYDROSPHERE-COMPOSITION-SCHEMA.md` | Sibling task's composition inventory (16 flat objects, 1 array, 3 empty objects across sol-complete.json) |
| `docs/architecture/simulation/hydrosphere_system.md` | Hydrosphere design docs — describes composition format but does not enforce it |
| Magnetosphere refactor code (`calculate_magnetosphere_strength`, `SystemBuilderService`) | Established data-driven generation pattern — hydrosphere should follow same principle |

### Migration
- [x] No migration in this task — planning/inventory only. If Step 4 concludes composition needs to be added to procedural generation, that's a separate implementation task.

---

## Investigation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

This task makes NO code, schema, data, or configuration changes — every step below is read-only investigation and planning.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
```bash
git mv projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md \
       projects/galaxy_game/tasks/active/2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md
```
Then update `status: backlog → status: active`, then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-23-MEDIUM-ARCHITECTURE-STARSIM-HYDROSPHERE-COMPOSITION-GENERATION-AUDIT.md"
```
**Paste the output of the find command in chat before proceeding.**

### Step 1 — Verify importer passthrough behavior
Confirm `SystemBuilderService#create_hydrosphere` (line 236) passes composition through unchanged. Check whether any downstream consumer (model, service, view) normalizes or validates the shape. Cite file:line for every finding.

### Step 2 — Verify procedural generation gap
Confirm `PlanetBuilder#generate_hydrosphere_data` (line 114) and `ProceduralGenerator` (line 737) do NOT populate composition. Check whether any other code path (Civ4 importer, FreeCiv importer, Earth map generator) adds composition to procedurally-generated bodies. Cite file:line.

### Step 3 — Determine if the importer passthrough gap is a real problem
The sibling task found two shapes in Sol data. Does anything actually READ composition in a way that would break on shape mismatch? Or is composition purely documentation/visual data with no functional consumer?

### Step 4 — Determine if procedural composition needs to be added
If Step 3 finds a functional consumer, procedural bodies are missing required data. If no consumer exists yet, assess whether adding composition now is premature vs. whether it should be added proactively (same principle as magnetosphere_strength: calculate and store in JSON, don't hardcode).

### Step 5 — If warranted, propose minimal schema decision
Only if Step 3 or Step 4 concludes action is needed: propose the minimal schema format (flat object vs array), what calculation approach matches the data-driven-generation principle, and sequencing/risk. Do not execute any of it.

---

## Acceptance Criteria
- [ ] Importer-side passthrough behavior confirmed with file:line reference
- [ ] Procedural generation gap confirmed with file:line reference (composition field absent)
- [ ] Functional consumer status determined (is composition read by anything, or is it documentation-only?)
- [ ] Explicit recommendation on whether procedural composition needs to be added, with reasoning
- [ ] If action needed, minimal schema proposal included
- [ ] Zero files modified

---

## Stop Conditions — escalate to user immediately if:
- Step 3 finds that composition IS functionally consumed and the shape mismatch causes real bugs (this would be a higher-priority bug-fix task)
- Adding procedural composition would require modifying the same shared generation service the magnetosphere refactor already touched (risk of conflicting with that work)
- The investigation reveals this is actually two separate problems that should be split into independent tasks

---

## Dependencies
**Blocked by**: none, but should cross-reference `2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md` findings
**Blocks**: none yet — feeds any future procedural hydrosphere composition implementation
**Related tasks**: `2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md` (sibling — schema audit); magnetosphere_strength data-driven generation refactor (established the pattern/precedent)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final result**: Procedural composition needed — YES / NO (circle one), with brief reasoning

### What was changed
- None — investigation and planning only, brief saved to `summaries/`

### Issues discovered
[Any problems found during investigation that weren't anticipated]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files verified] | [composition gap confirmed/unclear] | [next action needed]