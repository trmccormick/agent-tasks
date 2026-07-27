---
status: backlog
priority: MEDIUM
type: data
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07/2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/2026-07/2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md \
         projects/galaxy_game/tasks/active/2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Instantiate Visual Definition v1.0 for RH-400 (pilot unit)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: data
**Created**: 2026-07-26
**Last Updated**: 2026-07-26

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — no specs run in this task
- **MVP Alignment**: VALID — not part of the current Luna MVP loop, but unblocks the asset-generation pipeline's PromptBuilder automation goal
- **MVP Impact Note**: None directly; this is asset-pipeline infrastructure work, deliberately scoped separate from Luna MVP/RSpec stabilization work.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Structured data-authoring task using an existing template — well within local worker capability, no architecture decisions required (those were already made in the prerequisite review below).
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `agent-tasks/projects/galaxy_game/README.md`
3. **Manual PromptBuilder Validation report** (2026-07-26, RH-400 gameplay-asset prompt test) — read this first, it's the direct source of every gap this task fills
4. **This Task File**: Everything below

---

## Context
A manual PromptBuilder validation test (2026-07-26) confirmed the four-layer asset architecture (Blueprint → Operational Data → Visual Definition → Design System → PromptBuilder) works, but is blocked by one weak layer: **no instantiated Visual Definition exists for any unit yet**, including RH-400, the pilot unit used throughout asset-pipeline development. Everything the test needed from Visual Definition had to be inferred from blueprint prose and Design System defaults instead of read from structured data.

This task instantiates a real Visual Definition file for RH-400 — the first one in the project — so the validation test can be rerun afterward to confirm the gap count actually drops.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/projects/galaxy_game/design/VISUAL_DEFINITION_TEMPLATE.md` — the schema to instantiate against
- `docs/new_agent/projects/galaxy_game/design/DESIGN_SYSTEM_SUMMARY.md` — source for anything Design-System-derived (see Gotcha 1)
- `docs/new_agent/projects/galaxy_game/design/ASSET_GENERATION_ARCHITECTURE.md` — Gameplay Asset requirements this Visual Definition needs to support

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Manufacturing style, technology level, and color are Design-System-derived — do NOT put raw values directly on the Visual Definition as if they were independent facts.
- ❌ Wrong: Writing `manufacturing_style: "Bootstrap/Frontier"` or literal hex colors (`#F0F0F0`) directly into the Visual Definition as freestanding values.
- ✅ Right: Reference a `minimum_technology` level (e.g. Mk1–Mk2) and semantic color roles (`industrial_primary`, `industrial_secondary`, `hazard_warning`) that the Design System resolves to actual style/RGB values. The Design System is the single owner of what "Bootstrap/Frontier" or "industrial_primary" concretely means — don't duplicate that resolution logic or those literal values into this file.
- Why: Storing resolved values per-unit means updating the Design System later requires touching every unit's Visual Definition instead of one file. This was flagged explicitly in review of the validation report — treat it as a locked decision, not a suggestion.

⚠️ **GOTCHA 2**: Do not touch the blueprint JSON or operational data files in this task.
- ❌ Wrong: Adding `recognition_features`, `technology_level`, or similar fields directly to `regolith_harvesting_rover_bp.json`.
- ✅ Right: All new structured data goes into the Visual Definition file itself (following VISUAL_DEFINITION_TEMPLATE.md), which references the blueprint by id — it does not modify the blueprint.
- Why: Keeps the layer boundary clean — Blueprint owns physical/simulation facts, Visual Definition owns appearance/recognition facts.

⚠️ **GOTCHA 3**: The Icon Bible file referenced by DESIGN_SYSTEM_SUMMARY.md, DESIGN_RESEARCH_INDEX.md, and UNIFIED_ASSET_CATALOG_ARCHITECTURE.md (`2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md`) does not exist in the workspace, per the validation report. Do not assume it's just misplaced and skip a field because "the Icon Bible probably has it" — if you can't find it after a real search, note the gap explicitly rather than leaving a field blank or guessing its contents.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Post the standard synthesis report in chat before proceeding. Confirm you've read the validation report and understand which specific gaps this task is meant to close.

---

## Problem Statement
No unit in the project has an instantiated Visual Definition, so every prompt-assembly attempt falls back to inferring appearance details from blueprint prose and Design System defaults — undermining the goal of deterministic, automatable prompt generation.

**Current behavior**: `VISUAL_DEFINITION_TEMPLATE.md` exists as a schema but has zero instances.
**Expected behavior after this task**: RH-400 has a complete, filled-in Visual Definition file that a future PromptBuilder validation can read directly instead of inferring from other documents.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose |
|---|---|
| A new Visual Definition instance file for RH-400 (naming convention TBD — check if VISUAL_DEFINITION_TEMPLATE.md specifies a path/naming pattern for instances; if not, propose one and flag it in the completion report rather than guessing silently) | The actual deliverable of this task |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/blueprints/crafts/ground/regolith_harvesting_rover_bp.json` | Source of physical specs and id — reference, don't duplicate raw values that Design System should derive |
| `docs/new_agent/projects/galaxy_game/design/VISUAL_DEFINITION_TEMPLATE.md` | The schema to fill in |
| `docs/new_agent/projects/galaxy_game/design/DESIGN_SYSTEM_SUMMARY.md` | Source for Design-System-derived concepts (manufacturing style, color roles, tech level resolution) |
| `docs/reference/asset-generation/rh400-prompt-template.md` | Existing RH-400-specific notes — useful raw material for filling fields, but confirm accuracy rather than copying uncritically |
| Manual PromptBuilder Validation report (2026-07-26) | The full list of gaps this task should address |

### Migration (if needed)
- [x] No migration needed — this creates a new documentation/data file, does not touch schema or database

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
Follow the standard Step 0 procedure from TASK_TEMPLATE.md.

### Step 1 — Fill in structured fields per VISUAL_DEFINITION_TEMPLATE.md
At minimum, address every field flagged as a gap in the validation report:
- `recognition_features` — structured array (not free-text), pulled from what's already inferred in the validation report's prompt, verified against the blueprint description
- A single unified priority field distinguishing which recognition features matter most at low detail/small scale (the validation report and a later review both proposed this under different names — pick ONE field name and use it consistently; do not create two overlapping fields)
- `visual_identity` — the "feel" of the unit (e.g. heavy, industrial, rugged, modular, repairable) as distinct from literal recognition_features
- `scale_class` — a relative size category (propose a scale if none exists yet — e.g. Human/Vehicle/Building/Megastructure or similar — flag your proposed scale in the completion report since this affects future units too)
- `minimum_technology` reference (NOT a literal manufacturing_style or hex colors — see Gotcha 1)
- Semantic color role references (`industrial_primary`, `industrial_secondary`, `hazard_warning`, etc.) — NOT literal RGB/hex values
- `silhouette` description, if the template calls for it

### Step 2 — Cross-check against the blueprint
Confirm every physical fact referenced (dimensions, category) matches the current blueprint file — don't assume the numbers from the validation report or prompt template notes are still current.

### Step 3 — Note anything still missing
If a template field can't be filled without the missing Icon Bible file or without inventing a fact not grounded in any existing document, say so explicitly in the completion report rather than guessing.

### Step 4 — Do NOT rerun the PromptBuilder validation test in this task
That's a separate follow-up step for a human/next session to trigger once this is confirmed complete — keep this task scoped to instantiating the Visual Definition only.

---

## Acceptance Criteria
- [ ] RH-400 has a complete Visual Definition instance file following VISUAL_DEFINITION_TEMPLATE.md
- [ ] recognition_features is a structured field, not inferred from prose
- [ ] Only one unified priority field exists (no duplicate primary/secondary/tertiary-style fields under different names)
- [ ] visual_identity is documented separately from recognition_features
- [ ] scale_class is defined (with the proposed scale system flagged for review if none existed)
- [ ] No literal manufacturing_style strings or hex color values written directly into the Visual Definition — semantic/reference fields only
- [ ] Any fields that couldn't be completed (e.g. due to the missing Icon Bible) are explicitly listed, not silently omitted
- [ ] Blueprint and operational data files are unmodified

---

## Stop Conditions — escalate to user immediately if:
- Filling in a field would require inventing a scale system, color role taxonomy, or naming convention that doesn't exist anywhere yet and isn't a simple direct fill from existing docs — flag it as a decision needed rather than picking one silently
- The blueprint's current dimensions/specs have changed from what the 2026-07-26 validation report assumed
- Any doc conflict surfaces (two docs disagreeing on the same Visual Definition concept)

---

## Commit Instructions
```bash
git add [visual definition file only]
git commit -m "data: instantiate Visual Definition v1.0 for RH-400"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md
git commit -m "chore: move visual-definition-v1-rh400 task to completed/"
```

---

## Documentation
- [ ] No doc changes needed beyond the new Visual Definition file itself
- [ ] Flag doc gap: if a naming/path convention for Visual Definition instances doesn't exist in VISUAL_DEFINITION_TEMPLATE.md, note the convention you used and that it should be formalized

---

## Dependencies
**Blocked by**: none
**Blocks**: rerunning the Manual PromptBuilder Validation test for RH-400 to confirm the gap count drops
**Related tasks**: 2026-07-26 Manual PromptBuilder Validation report (informal, not a task file); 2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md (unrelated domain, same session)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- [visual definition file] — [description]

### Issues discovered
[e.g. missing Icon Bible blocking specific fields, any new naming convention proposed]

### Follow-up tasks needed
[e.g. rerun PromptBuilder validation; locate/reconstruct Icon Bible; repeat this process for other units]

### Lessons learned
[what worked, what future Visual Definition instantiations should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [file created] | [fields completed vs. flagged missing] | [next action needed]
