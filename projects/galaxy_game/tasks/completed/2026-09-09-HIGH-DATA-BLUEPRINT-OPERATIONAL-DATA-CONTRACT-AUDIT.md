---
status: completed
priority: HIGH
type: data
system_domain: UNITS
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
completed_date: 2026-09-10
completion_note: Comprehensive audit completed. Research note generated with FACTS/OBSERVATIONS/UNKNOWNS/RECOMMENDATIONS. Critical issues identified (CAR-300 path mismatch and schema inconsistency). Output: 2026-09-10-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md
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

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/data/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/data/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md \
         projects/galaxy_game/tasks/active/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md"
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

# TASK: Audit blueprint/operational-data contract integrity for Luna settlement simulation
**Status**: BACKLOG
**Priority**: HIGH
**Type**: data
**Created**: 2026-09-09
**Last Updated**: 2026-09-09

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*
*Local models run via Copilot have terminal/tool-use access — they can grep the codebase,
check status.md, and run read-only research commands to verify state before triaging.
Continue is installed but is not part of the active workflow — a Continue session is
read-only (task files only, no commands, no DB access) and should not be assumed to have
the same capability.*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A
- **MVP Alignment**: VALID — directly supports AI Manager Luna settlement data health.
- **MVP Impact Note**: Establishes factual baseline for blueprint/operational-data integrity before any cleanup or migration.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Research-only data audit; no code changes; fits local planning/research workflow.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

**Supervision Legend**:
- Watched carefully = all agents on first dispatch of a task
- Standard = local Qwen (Copilot) on well-specified repeat task types
- Autonomous OK = not currently used — all tasks require human approval before commit

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The Luna settlement simulation depends on a coherent set of unit blueprints, operational-data records, visual definitions, and material production-chain references. Preliminary investigation (RH-400 case study) found duplicate/legacy blueprint records, inconsistent schema versions, stale physical dimensions, and incompatible operational-data file-path conventions.

This task produces a factual, evidence-based audit of the entire in-scope data population. It does not modify any files. It separates verified facts, observations, and recommendations so future cleanup/migration tasks have a reliable baseline.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions (including the rule that blueprints must NOT carry visual_profile/visual_definition fields).
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- Existing three-layer view architecture and any blueprint/operational-data specs, if present.

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials (if needed)

Not applicable — this is a read-only data audit.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Blueprints must NOT have visual_profile or visual_definition fields.
- ❌ Wrong: Flagging a blueprint as “missing visual_profile” or “missing visual_definition”.
- ✅ Right: Treat absence of visual fields as correct; flag presence as a regression.
- Why: This is a settled architecture decision; visual definitions, not blueprints, own visual relationships.

⚠️ **GOTCHA 2**: File existence ≠ simulation load.
- ❌ Wrong: Assuming a blueprint/operational-data file is “in use” just because it exists on disk.
- ✅ Right: Tie inclusion in the audit population to actual loaders, seeds, scenarios, registries, or documented manifests.
- Why: The simulation may ignore or override some files; the audit must reflect runtime reality, not just repository contents.

⚠️ **GOTCHA 3**: Reference resolution must match actual loader behavior.
- ❌ Wrong: Treating a reference as “resolved” if it works only after manually adding/removing an `operational_data/` prefix.
- ✅ Right: Record such cases as path-convention mismatches; only count exact, loader-consistent resolution as success.
- Why: Inconsistent prefixes indicate schema/data drift that future tasks must address.

⚠️ **GOTCHA 4**: Do not assume duplicate identity without evidence.
- ❌ Wrong: Declaring two blueprints “duplicates” based solely on similar names or roles.
- ✅ Right: Record duplicate/legacy concerns only when supported by strong evidence (e.g., same unit type, overlapping IDs, visual-definition relationships, known rename patterns).
- Why: Premature consolidation decisions can break references or lose intended variants.

### Multi-Domain / Multi-Tenant Routing (if applicable)

Not applicable — this task does not interact with web domains or tenants.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT
**Status**: backlog → active
**Date**: 2026-09-09

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-RH400-VISUAL-PROFILE-CONTRACT-AUDIT.md` | RH-400 case study context | pending |
| `data/json-data/blueprints/**/*.json` | Blueprint population | pending |
| `data/json-data/operational_data/**/*.json` | Operational-data population | pending |
| `docs/reference/asset-generation/visual_definitions/*.json` | Visual-definition population | pending |
| Loader/registry/seed code (Ruby/JS) | Determine actual Luna load set | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A markdown research note under `summaries/` that:
- Defines the actual Luna simulation load set (or best approximation).
- Audits blueprints for visual-field regressions, operational-data references, schema versions, and duplicate/legacy concerns.
- Audits operational-data records for reference/identity/property consistency.
- Audits visual definitions for blueprint_ref resolution.
- Audits material production-input chains for resolvability and classification.
- Documents template and path-convention findings against actual loader behavior.
- Separates facts, observations, and recommendations.

### Critical Gotchas I Will Avoid
- ❌ Flagging missing visual_profile/visual_definition as defects — instead ✅ flag presence as regressions.
- ❌ Treating file existence as proof of simulation load — instead ✅ tie to loaders/seeds/manifests.
- ❌ Counting prefix-normalized references as resolved — instead ✅ record as mismatches.
- ❌ Declaring duplicates without evidence — instead ✅ record as hypotheses with supporting facts.

***

**SYNTHESIS COMPLETE.** Ready to proceed with research.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Preliminary investigation (RH-400 case study) found:

- Two blueprint files that appear to represent one intended resource-harvester rover:
  - `regolith_harvester_rover` — older-style ID, correct/updated physical dimensions, no reference fields.
  - `hrv_400_resource_harvester_mk1` — standard robot naming convention, operational_data_reference present, stale pre-update dimensions.
- A Visual Definition whose `blueprint_ref` points to the older ID.
- A third robot (`car_300_lunar_deployment_robot_mk1`) on a newer schema version (`unit_blueprint_v1.3`), with metadata fields and populated operational/physical properties, and a different `operational_data_reference.file` path prefix.

These issues may not be isolated. The Luna simulation’s data contract integrity is unknown across the full set of blueprints, operational-data records, visual definitions, and material production inputs.

**Current behavior**: Unknown; mixed schema versions, possible duplicate/legacy records, inconsistent path conventions, and uncertain loader behavior.
**Expected behavior**: A coherent, well-understood data population with known schema versions, reference resolution rules, and documented inconsistencies.

---

## Files Involved

### Primary Files — you will read these (no edits)

| File | Purpose | Key Section |
|---|---|---|
| `data/json-data/blueprints/**/*.json` | Unit blueprints for Luna simulation | Entire files |
| `data/json-data/operational_data/**/*.json` | Operational-data records referenced by blueprints | Entire files |
| `docs/reference/asset-generation/visual_definitions/*.json` | Visual definitions with blueprint_ref relationships | Entire files |
| Loader/registry/seed/scenario code (Ruby/JS) | Determine actual Luna load set | Relevant methods/configs |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Locked decision: blueprints must NOT have visual_profile/visual_definition |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |
| Any existing blueprint/operational-data specs | Context for intended schema |

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
git mv projects/galaxy_game/tasks/backlog/data/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md \
       projects/galaxy_game/tasks/active/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md
```

Then open the moved file and change the YAML status field:
status: backlog → status: active

Then verify only one copy exists:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Define the Luna simulation load set

1. Locate how the Luna settlement simulation selects/loads:
   - blueprints,
   - operational-data records,
   - materials,
   - visual definitions (if applicable).
2. Identify loaders, registries, seeds, scenarios, configuration files, or documented manifests that define the intended population.
3. Build the audit population from actual loading evidence.
4. If a complete load set cannot be proven, separate:
   - confirmed Luna-loaded files,
   - likely Luna-relevant files,
   - files with unknown relevance.

Record your method and any uncertainty in the final report.

### Step 2 — Blueprint audit

For every in-scope blueprint:

1. Record:
   - File path.
   - Blueprint ID.
   - Unit category/type, if present.
   - `template_compliance` value.
   - Whether template compliance is plain/unversioned, versioned, missing, or unrecognized.
2. Visual-field status:
   - Whether `visual_profile` exists.
   - Whether `visual_definition` exists.
   - Classify as:
     - Correctly absent
     - Regression: visual_profile present
     - Regression: visual_definition present
     - Regression: both present
3. Operational-data reference:
   - Whether `operational_data_reference` exists.
   - Referenced operational-data file path/value.
   - Whether that referenced file resolves on disk exactly as written.
   - Whether resolution succeeds only after adding/removing `operational_data/` or another prefix (record as path-convention mismatch).
4. Operational-data content:
   - Whether `operational_data_reference` embeds physical/operational values, leaves objects empty, or uses another structure.
5. Metadata/version markers:
   - Presence of `metadata.designation` and `metadata.mk_version`, where relevant.
6. Physical dimensions/mass/volume values if present.
7. Possible duplicate/legacy identity relationships, but only when supported by strong evidence.

Do not make any changes; collect facts.

### Step 3 — Operational-data audit

For every operational-data file referenced by an in-scope blueprint:

1. Record:
   - File path.
   - Actual relative path from the correct operational-data root.
   - Referencing blueprint(s).
   - Whether the file is orphaned, singly referenced, or multiply referenced.
2. Identity/property alignment:
   - Whether its identifier/designation matches the blueprint identity.
   - Whether its physical and operational properties agree with blueprint-side information when both exist.
3. Back-references:
   - Whether it contains a back-reference, and whether that back-reference resolves.
4. Schema/version marker present, if any.

### Step 4 — Visual-definition relationship audit

For every visual-definition file relevant to an in-scope blueprint:

1. Record:
   - File path.
   - `blueprint_ref` value.
   - Whether `blueprint_ref` resolves to a real in-scope blueprint.
2. Current/legacy concern:
   - Whether the reference points to an apparent legacy/duplicate blueprint when a current-standard ID also exists.
3. Notes on any additional relationships or irregularities.

Do not recommend moving visual fields onto blueprints; that would violate the settled architecture decision.

### Step 5 — Material production-input audit

For every material actually used by production definitions in the Luna simulation load set:

1. Record:
   - Material file path and material ID.
   - Whether `production.input_materials` exists.
   - Whether it is populated, empty, malformed, or absent.
2. For every listed input:
   - Exact input identifier.
   - Whether it resolves to a real material file.
3. Classify issues as:
   - missing input definition,
   - grouping/category used as a material input,
   - stale/renamed identifier,
   - path/format issue,
   - empty chain by deliberate raw-material design,
   - empty chain with unknown intent.

Do not assume `iron_ore` is a valid concrete material. The established design direction is that terms like “iron ore” are geological/UI groupings, while deposits should be multi-mineral assemblages of concrete minerals such as hematite or magnetite.

### Step 6 — Template and reference-path convention analysis

Determine, from actual loader behavior and file locations:

1. Which `template_compliance` values are accepted today.
2. Whether plain `unit_blueprint` is a recognized legacy schema, a current schema, or merely inconsistent data.
3. Whether versioned schemas such as `unit_blueprint_v1.3` are required or optional.
4. Which `operational_data_reference.file` convention is correct:
   - `units/robots/...`
   - `operational_data/units/robots/...`
   - another root-relative convention
5. Whether the loader normalizes prefixes or expects exact paths.
6. Whether files should be upgraded, migrated, or merely documented as inconsistent.

Do not make the migration decision as a fact; place it in Recommendations with evidence and uncertainty.

### Step 7 — Write the research note

Produce a markdown research note suitable for:

`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md`

Use the structure specified in the Acceptance Criteria section.

---

## Acceptance Criteria

- [ ] Research note saved as `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md`.
- [ ] Audit population is tied to actual or best-effort Luna simulation loading evidence.
- [ ] Every blueprint in scope is evaluated for:
  - visual-field regressions,
  - operational-data-reference presence,
  - exact reference resolution,
  - template_compliance version,
  - metadata/version markers,
  - duplicate/legacy concerns where supported by evidence.
- [ ] Every relevant operational-data record is checked for:
  - reference/identity/property consistency,
  - orphan/multiple-reference status,
  - schema/version markers.
- [ ] Every relevant visual definition is checked for:
  - `blueprint_ref` resolution,
  - current/legacy concern.
- [ ] Relevant material production inputs are classified as:
  - populated/resolvable,
  - empty,
  - malformed,
  - unresolved,
  - with issue classification.
- [ ] Template and path-convention findings are verified against real loader behavior or best available evidence.
- [ ] RH-400 is documented as a case study without prematurely selecting which record to retain.
- [ ] The report cleanly separates facts, observations, unknowns, and recommendations.

---

## Stop Conditions — escalate to user immediately if:

- The loader/manifest mechanism is completely undocumented and cannot be inferred from code.
- The data population is far larger than expected and cannot be audited within reasonable time.
- Any architectural decision is required (e.g., “which blueprint is canonical?”).
- You discover that critical references depend on external services or databases you cannot inspect.

---

## Commit Instructions

Run git commands on **host only** — never inside any container:

```bash
# Add only the new research note
git add /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md
git commit -m "research: add Luna blueprint/operational-data contract audit"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-09-HIGH-DATA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md
git commit -m "chore: move blueprint/operational-data audit task to completed/"
```

---

## Documentation

- [x] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies

**Blocked by**: none
**Blocks**: future tasks to clean up duplicate/legacy blueprints, normalize operational-data paths, and migrate schema versions.
**Related tasks**: 
- `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/architecture/2026-09-09-ECONOMIC-DOCUMENTATION-AUDIT-WIKI-COMPLETION.md`
- `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/completed/2026-09/2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md`

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final output**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-09-09-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md`

### What was changed
- Created research note under `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/`.

### Issues discovered
[Any problems found during research that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: created `summaries/2026-09-09-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md` | research-only, no code/data changes | next: use audit to plan blueprint/operational-data cleanup and schema-migration tasks