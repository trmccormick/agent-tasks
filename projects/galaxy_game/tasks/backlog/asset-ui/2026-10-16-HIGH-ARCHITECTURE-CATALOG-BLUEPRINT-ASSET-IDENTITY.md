---
status: backlog
resume: false
priority: HIGH
type: architecture
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Research Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/asset-ui/2026-10-16-HIGH-ARCHITECTURE-CATALOG-BLUEPRINT-ASSET-IDENTITY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-10-16-HIGH-ARCHITECTURE-CATALOG-BLUEPRINT-ASSET-IDENTITY.md \
         projects/galaxy_game/tasks/active/2026-10-16-HIGH-ARCHITECTURE-CATALOG-BLUEPRINT-ASSET-IDENTITY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-10-16-HIGH-ARCHITECTURE-CATALOG-BLUEPRINT-ASSET-IDENTITY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and acceptance criteria.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## Objective

Establish and obtain human approval for a **general, deterministic contract** that resolves a catalog blueprint identity to the canonical `AssetRegistry` `asset_id`.

This is a **research/contract-design task only**. It must not implement production code. Its output is an approved mapping contract document that unblocks the catalog image-resolution task (`2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`).

## Problem Statement

The admin catalog index page requires resolving a catalog blueprint identity (e.g., `VEHICLE_HARVESTER_ROVER_RH400`) to an `AssetRegistry` `asset_id` before any registry-first image resolution can proceed. No existing deterministic mapping mechanism has been established or approved.

Without this contract, the catalog image-resolution task cannot implement registry-first resolution because it has no authoritative rule for identity resolution. The blocker is architectural, not implementation-level.

## Research Scope

### Required Synthesis Documentation

The Phase 1 synthesis report must document the following with exact code evidence:

1. **Known blueprint identifiers** — List all catalog blueprint identities currently used by the catalog controller or catalog data source. Cite the exact file, method, and line where each identifier appears.

2. **Canonical AssetRegistry asset_ids** — For each known blueprint identity, identify the corresponding `AssetRegistry` `asset_id` if one exists in the current codebase or registered data. Cite the exact registry entry, factory, or data source.

3. **Existing mapping mechanisms** — Document all existing code paths, data structures, and methods relevant to blueprint-to-asset identity resolution:
   - `find_asset_by_blueprint_id` (if referenced but unimplemented)
   - Any iteration over registry entries that compares `blueprint_id`
   - Any hardcoded or configuration-based mappings
   - Any factory or fixture data that establishes identity relationships

4. **Determinism assessment** — For each existing mechanism:
   - Is it deterministic? (same input → same output, always)
   - Is it general? (applies to all assets, not just one)
   - Is it code-supported? (exists in current codebase, not inferred)
   - Does it rely on filename inference, string normalization, fuzzy matching, or first-match behavior?

5. **Gap analysis** — If no existing deterministic mapping meets the requirement:
   - Propose a general mapping contract with:
     - Mapping rule (exact transformation or lookup mechanism)
     - Owner (which component is responsible for maintaining the mapping)
     - Storage location (where the mapping data lives)
     - Uniqueness rules (how one-to-one relationships are enforced)
     - Missing-mapping behavior (what happens when no match exists)
     - Ambiguous-mapping behavior (what happens when multiple matches exist)

6. **Evidence of determinism** — Demonstrate that the proposed mapping:
   - Is deterministic and not based on filename inference, normalization, fuzzy matching, first-match behavior, or an RH-400-specific exception
   - Can be verified through existing code inspection or test execution
   - Does not introduce new runtime discovery or modify B2 boundaries

### Prohibited Actions

- **No production implementation** before explicit human approval of the contract
- **No RH-400-specific mapping** or exception — the contract must be general
- **No filename inference** — string normalization as a substitute for mapping is prohibited
- **No fuzzy matching** — approximate identity resolution is not permitted
- **No first-match selection** — choosing the first registry entry that appears to match is prohibited
- **No changes to the catalog image-resolution task's implementation scope** — this task only produces the contract; it does not modify the blocked task

## Acceptance Criteria

- [ ] **AC1: Existing mapping assessment** — The report establishes whether an existing deterministic mapping already meets the requirement, with exact code evidence. If one exists, document it and recommend proceeding to the catalog image-resolution task.

- [ ] **AC2: Proposed contract (if no existing mapping)** — If no existing mapping is found, the report proposes a general mapping contract that defines:
  - The mapping rule (exact transformation or lookup mechanism)
  - The owner component responsible for maintaining the mapping
  - The storage location for mapping data
  - Uniqueness rules for one-to-one relationships
  - Missing-mapping behavior (explicit error or blocker; no fallback,
  inference, or ambiguous resolution)
  - Ambiguous-mapping behavior (how multiple matches are handled)

- [ ] **AC3: Determinism evidence** — The proposed contract is demonstrated to be deterministic through code inspection or test execution. It does not rely on filename inference, string normalization, fuzzy matching, first-match behavior, or an RH-400-specific exception.

- [ ] **AC4: Human approval gate** — The synthesis report is saved as a file and explicit human approval is obtained before any production code implementation is authorized. A chat summary does not substitute for approval of the saved contract document.

## Dependencies

**Blocked by**: none
**Blocks**: `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md` (catalog image-resolution task cannot proceed without an approved deterministic blueprint-to-asset identity mapping)
**Related tasks**: `HIGH-ARCHITECTURE-ASSET-REGISTRY-RUNTIME-METADATA`, `HIGH-ARCHITECTURE-CATALOG-REPRESENTATION-SELECTION`, `HIGH-SECURITY-CATALOG-IMAGE-PATH-VALIDATION`

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was produced
- `[synthesis report filename]` — [brief description]

### Issues discovered
[Any problems found during research that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
