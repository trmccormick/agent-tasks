---
status: backlog
priority: MEDIUM
type: data
system_domain: MANUFACTURING
mvp_alignment: ISRU_PRODUCTION
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md \
         projects/galaxy_game/tasks/active/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md"
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

# TASK: Material Production-Input Schema Normalization — Planning
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: data
**Created**: 2026-09-16
**Last Updated**: 2026-09-16

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: [FILL IN]
- **Docker Wrapper Check**: N/A — no application code executed, no writes
- **MVP Alignment**: [FILL IN]
- **MVP Impact Note**: [FILL IN]
- **Action Line**: [FILL IN]

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires grepping all 207 material JSON files and tracing consumer code — terminal access needed
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/path/to/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/path/to/agent-tasks/projects/galaxy_game/README.md`
3. **Source context**: prior material-file audit findings (`production.input_materials` populated in only 10 of 207 files; schema drift between v1.6 template and real files such as `iron.json`) and the epoxy_resin task's blocker report — both referenced in project memory/prior summaries, exact paths [FILL IN]
4. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
A prior material-file audit found `production.input_materials` populated in only 10 of 207 material JSON files, with schema drift between the v1.6 template and real files (e.g. `iron.json` uses top-level `category`/`subcategory` instead of nested `classification`, plus a separate, inconsistent `sources.natural`/`sources.synthetic` field where several entries don't resolve to real material files at all). This was raised again during the epoxy_resin task's preflight and explicitly kept separate from the geography-agnostic pricing/availability resolver question (see related task). This task investigates and plans only — it does not decide unilaterally to build a migration, and it does not touch pricing/availability logic.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Prior material-file audit findings — [FILL IN exact path/summary file]

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials
None needed — this is a read-only codebase/data investigation task.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is a planning task — do not migrate or rewrite material files.
- ❌ Wrong: normalizing even a handful of "obviously wrong" files as a proof of concept.
- ✅ Right: inventory and report only; migration options are proposed language for later, not executed here.
- Why: whether normalization is even needed depends on consumer findings this task hasn't gathered yet — acting first would prejudge the answer.

⚠️ **GOTCHA 2**: Do not touch pricing or availability resolution logic, and do not touch epoxy_resin's data.
- ❌ Wrong: "while I'm in here," adjusting `NpcPriceCalculator` or any pricing consumer, or epoxy_resin's `sourcing_strategy`/production fields.
- ✅ Right: stay entirely within input_materials/production-schema inventory and consumer analysis.
- Why: this task is intentionally scoped separately from the geography-agnostic pricing/availability resolver task — mixing them was explicitly rejected.

⚠️ **GOTCHA 3**: Absence of a normalization need is a valid, acceptable outcome.
- ❌ Wrong: assuming the task must conclude "yes, normalize" because the audit found drift.
- ✅ Right: if no current or credible prospective consumer needs `input_materials`, say so plainly — that's a complete, useful answer.
- Why: schema-drift by itself isn't harmful if nothing reads the field; consumer need is the actual bar.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or reading further, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Material Production-Input Schema Normalization — Planning
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Inventory all input_materials/production-sourcing variants across the 207 material JSON files, identify actual and prospective consumers, determine whether normalization is needed at all, and — only if warranted — propose bounded migration options. No schema writes, no migrations, no code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `data/json-data/**/*.json` (materials) | Inventory target — all 207 files | not started |
| `galaxy_game/app/services/lookup/material_lookup_service.rb:6` | Consumer trace | not started |
| `galaxy_game/app/services/manufacturing/cost_calculator.rb:5` | Prospective consumer | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read prior material-file audit findings
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A planning brief MD file in summaries/: variant inventory with counts/examples, consumer/validation-contract findings, an explicit needed/not-needed recommendation, and (only if needed) 2-3 bounded migration options with tradeoffs.

### Critical Gotchas I Will Avoid
- ❌ Migrating any files — instead ✅ inventory and report only
- ❌ Touching pricing/availability code or epoxy_resin data — instead ✅ staying within input_materials/production-schema scope
```

**SYNTHESIS COMPLETE.** Ready to proceed with investigation.

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
It is not established whether `production.input_materials` (or its inconsistent variants, e.g. `sources.natural`/`sources.synthetic`) needs normalization, because consumer status is unclear — a prior audit found the field populated in only 10 of 207 files with no confirmed application consumer at all.

**Current behavior**: multiple inconsistent representations of a material's production inputs exist across the material JSON corpus; at least one (`iron.json`'s `sources.*`) contains entries that don't resolve to real material files.
**Expected outcome of this task**: a planning brief stating whether normalization is warranted, backed by an actual consumer inventory — not an assumption.

---

## Files Involved

### Primary Files — read-only investigation targets (DO NOT EDIT)
| File | Purpose | Key Method/Section |
|---|---|---|
| All material JSON files under `data/json-data/` (207 total) | Inventory `production.input_materials` and variant fields | N/A — data inventory, not code |
| `iron.json` (`processed/metals/iron.json`) | Known example of schema drift + unresolvable `sources.*` entries | `sources.natural`, `sources.synthetic` |
| `carbon_nanotubes.json` | Known example of same drift pattern (engineered material) | `category`/`subcategory` top-level |
| Material template files `material_v1.0.json` through `material_v1.6.json` | Confirm current template shape (v1.6 latest) | top-level key list |

### Reference Files — read for context, do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/lookup/material_lookup_service.rb:6` | Confirm whether it validates/reads `production.input_materials` today |
| `galaxy_game/app/services/manufacturing/cost_calculator.rb:5` | Prospective consumer for COGS/pricing chain-walking; confirm live/dead status |

### Migration
- [x] No migration in this task — planning/inventory only. If Step 4 concludes normalization is warranted, migration options are *proposed*, not executed, here.

---

## Investigation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

This task makes NO code, schema, data, or configuration changes — every step below is read-only investigation and planning.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
```bash
git mv projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md \
       projects/galaxy_game/tasks/active/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md
```
Then update `status: backlog → status: active`, then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md"
```
**Paste the output of the find command in chat before proceeding.**

### Step 1 — Inventory all input-material representation variants
Across all 207 material files, catalog every distinct way "what this material is made from" is expressed (`production.input_materials`, `sources.natural`, `sources.synthetic`, any others found). For each variant: count of files using it, and 2-3 representative examples (including any that don't resolve to real material files, as with `iron.json`).

### Step 2 — Identify actual consumers
Trace `MaterialLookupService` (or equivalent) and any other code that reads material JSON — does anything currently read `production.input_materials` or its variants? Cite file:line for every finding, including confirming "no consumer found" if that's the result.

### Step 3 — Identify prospective consumers
Check whether `Manufacturing::CostCalculator` (or equivalent) or any COGS/pricing chain-walking code exists, even if currently dead/unwired, that would plausibly consume this data once built. Cite file:line and current live/dead status.

### Step 4 — Determine whether normalization is needed
Based on Steps 2-3, state plainly: is normalization needed now? If no current consumer and no near-term planned consumer, the answer may legitimately be "not yet" — say so.

### Step 5 — If warranted, propose migration options only
Only if Step 4 concludes normalization is needed: propose 2-3 bounded options (scope, sequencing, risk) for human review. Do not execute any of them.

### Step 6 — Write the planning brief
Save as an MD file in the summaries folder: Step 1 inventory, Step 2-3 consumer findings, Step 4 recommendation, and (if applicable) Step 5 options.

---

## Acceptance Criteria
- [ ] All input-material representation variants inventoried with counts and examples
- [ ] Actual consumer trace completed and documented (including a clean "none found" if true)
- [ ] Prospective consumer trace completed and documented
- [ ] Explicit needed/not-needed recommendation delivered, regardless of which way it lands
- [ ] If normalization is recommended, 2-3 bounded migration options proposed (not executed)
- [ ] Zero material JSON, code, schema, configuration, or test files modified
- [ ] No pricing/availability logic touched; no epoxy_resin data touched

---

## Stop Conditions — escalate to user immediately if:
- Any step would require writing a schema or migrating data to proceed
- Findings suggest this task actually needs the geography-agnostic pricing/availability resolver decision to be made first — stop and flag the dependency rather than guessing
- A file path in this task cannot be found or has clearly moved — note the correction, do not guess and proceed silently

---

## Commit Instructions
This task produces no code or data changes. Only the synthesis report and planning brief (both MD files in `summaries/`) should be committed:
```bash
git add projects/galaxy_game/summaries/[synthesis-report-filename].md
git add projects/galaxy_game/summaries/[planning-brief-filename].md
git commit -m "docs: material production-input schema normalization — planning brief"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md
git commit -m "chore: move material production-input schema normalization planning to completed/"
```

---

## Documentation
- [ ] No doc changes needed for this task itself
- [x] Flag doc gap: if normalization is recommended, the material template docs (`material_v1.6.json` conventions) will need updating — do not update now, add to backlog as a follow-up

---

## Dependencies
**Blocked by**: none
**Blocks**: none directly — informs but does not gate any future material-schema work
**Related tasks**: `2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md` (independent in scope, no blocking relationship either direction, shares the epoxy_resin blocker report as source context); the original epoxy_resin task (superseded — its production-input findings feed this task, not the resolver task)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final result**: Normalization needed — YES / NO (circle one), with brief reasoning

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

HANDOFF SUMMARY: [planning brief filename] | [needed/not needed] | [next action needed]
