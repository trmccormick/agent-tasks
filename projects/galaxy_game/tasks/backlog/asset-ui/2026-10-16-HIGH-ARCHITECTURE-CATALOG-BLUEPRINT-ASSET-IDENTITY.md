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

- [ ] Agent Dispatch Interface section is complete and accurate, with only the documented machine-specific path substitution required
- [ ] Research procedure and stop conditions are explicit and actionable
- [ ] Phase 1 synthesis report requirements are complete and copy/paste ready
- [ ] No production implementation steps or implied implementation authorization remain
- [ ] All repository paths and referenced task paths have been verified by the human before dispatch
- [ ] Architecture constraints explicitly preserve the existing AssetRegistry and B2 boundaries
- [ ] Acceptance criteria are measurable and distinguish research completion from contract approval
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all applicable readiness checks are completed.**

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
  - Verify with: find projects/galaxy_game/tasks -name "2026-10-16-HIGH-ARCHITECTURE-CATALOG-BLUEPRINT-ASSET-IDENTITY.md"
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

The admin catalog index page requires resolving the catalog's actual blueprint identity to the canonical `AssetRegistry` `asset_id` before any registry-first image resolution can proceed. The authoritative source of the catalog blueprint identity has not yet been established, and no deterministic mapping mechanism has been established or approved. `VEHICLE_HARVESTER_ROVER_RH400` is an example identifier to investigate, not an assumed authoritative input identity or canonical `asset_id`.

Research must first establish where the catalog obtains its blueprint identity, then trace whether and how that identity relates to an `AssetRegistry` `asset_id`. Do not assume the input identity and canonical output are equal or derive one from the other based on naming similarity. Without this contract, the catalog image-resolution task has no authoritative rule for identity resolution. The blocker is architectural, not implementation-level.

## Research Scope

### Phase 1 Procedure and Mandatory Stop Conditions

Phase 1 is read-only repository research and contract design. Follow this sequence:

1. Identify the catalog's actual blueprint identity source and enumerate the identities in scope.
2. Trace all existing code and data paths that could associate those identities with canonical `AssetRegistry` `asset_id` values.
3. Assess each existing path for determinism, generality, ownership, uniqueness, and compatibility with the existing runtime and B2 boundaries.
4. If an existing mapping satisfies the required contract, document the evidence and its limitations. Do not modify it.
5. If no existing mapping satisfies the requirement, prepare a proposed general mapping contract for human review. Label it as a proposal, not as an existing capability or an implemented solution.
6. Save the synthesis report to the required summaries path. Include the evidence, proposed contract if needed, unresolved questions, test strategy, and recommendation.
7. Stop and request explicit human review of the saved report.

**Mandatory stop conditions:**

- Do not create or modify application code, tests, registry entries, mapping data, task scope, routes, or runtime configuration.
- Do not claim that a proposed contract is implemented or verified by tests unless existing code or tests actually demonstrate that behavior.
- Do not choose an identity mapping based solely on naming similarity or a single asset example.
- If identity ownership, uniqueness, or cardinality cannot be established, record the gap rather than inventing a resolution.
- Do not begin implementation after saving the report. Contract approval and authorization of implementation are separate human decisions.

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

6. **Evidence and verification of determinism** — Separate evidence for existing behavior from reasoning and enforcement requirements for any proposed mapping:
   - For existing behavior, cite repository code or existing tests that directly demonstrate the behavior; do not treat a proposed contract as existing capability.
   - For a proposed mapping, explain why its specified rule would be deterministic and unambiguous, identify the mechanism that future implementation must use to enforce that rule, and describe concrete future tests or checks. Label these as design reasoning, enforcement requirements, and future verification—not as behavior verified by current code or tests.
   - Explain how the proposed contract avoids filename inference, normalization as a substitute for mapping, fuzzy matching, first-match behavior, or an RH-400-specific exception.
   - Explain how the proposal preserves the existing AssetRegistry and B2 boundaries without introducing runtime discovery. Cite existing code for current boundaries and label any future enforcement as proposed.

### Prohibited Actions

- **No production implementation** before explicit human approval of the contract
- **No RH-400-specific mapping** or exception — the contract must be general
- **No filename inference** — string normalization as a substitute for mapping is prohibited
- **No fuzzy matching** — approximate identity resolution is not permitted
- **No first-match selection** — choosing the first registry entry that appears to match is prohibited
- **No changes to the catalog image-resolution task's implementation scope** — this task only produces the contract; it does not modify the blocked task

## Acceptance Criteria

- [ ] **AC1: Existing mapping assessment** — The report establishes whether an existing deterministic mapping already meets the requirement, with exact code evidence. If one exists, document it and recommend proceeding to the catalog image-resolution task, subject to the other independent blockers documented in that task.

- [ ] **AC2: Proposed contract (if no existing mapping)** — If no existing mapping is found, the report proposes a general mapping contract that defines:

  - **Authoritative input:** The exact source of the catalog blueprint identity and the conditions under which that identity is considered valid.
  - **Canonical output:** The authoritative source and validation rules for the target `AssetRegistry` `asset_id`.
  - **Mapping mechanism:** An explicit lookup or other precisely defined rule. Filename inference, string normalization as a substitute for a mapping, fuzzy matching, and first-match selection are prohibited.
  - **Ownership:** The component or data owner responsible for creating, validating, and maintaining mappings.
  - **Storage and lifecycle:** The authoritative mapping location, how entries are added or changed, and how stale or conflicting mappings are detected.
  - **Cardinality and uniqueness:** Whether the relationship is one-to-one or supports a defined alternative; how uniqueness is enforced; and how duplicates or conflicting assignments are handled.
  - **Missing-mapping behavior:** An explicit unresolved result, error, or blocker. No inferred fallback identity.
  - **Ambiguous-mapping behavior:** An explicit failure or review-required result. No automatic candidate selection.
  - **Runtime boundary:** How the proposed contract respects the existing AssetRegistry and B2 architecture without introducing runtime discovery or an unapproved metadata-generation mechanism.
  - **Verification strategy:** Concrete proposed tests or checks covering valid mappings, unknown identities, duplicate mappings, conflicting mappings, and invalid canonical asset IDs.

  The report must distinguish requirements supported by existing code from proposed behavior that would require future implementation.

- [ ] **AC3: Determinism and design evidence** — The report provides repository evidence for all claims about existing behavior and explicitly identifies which contract requirements are proposed rather than implemented. For a proposed contract, the report explains why the specified rule would produce an unambiguous result, identifies the enforcement mechanism, and supplies a concrete future test plan. It does not claim that the proposed behavior has been implemented or tested. The contract must not rely on filename inference, string normalization as a substitute for mapping, fuzzy matching, first-match behavior, or an RH-400-specific exception. It must preserve the existing AssetRegistry and B2 boundaries unless a separately approved architecture decision changes them.

- [ ] **AC4: Human review and implementation separation** — The Phase 1 synthesis report is saved as a file and presented for explicit human review. The agent stops after producing the report and does not implement the mapping contract. Human approval must identify the approved contract and any conditions or limitations. Approval of the contract is not, by itself, authorization to implement it; implementation requires a separate explicit human decision and an appropriately scoped task. If the proposed contract is rejected or requires further design, the task remains blocked pending that decision. A chat summary does not substitute for approval of the saved contract document.

## Dependencies

**Blocked by:** None.

**Blocks:** `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`

The catalog image-resolution task remains blocked until a deterministic blueprint-to-asset identity mapping contract has been explicitly approved and any separately authorized implementation needed to make that contract available has been completed and verified.

Approval of this task's mapping contract does **not** resolve the separate runtime metadata, primary-representation selection, or safe-serving blockers. Those prerequisites must be addressed and approved independently before the catalog image-resolution task can proceed to implementation.

**Related prerequisite tasks:**
- `HIGH-ARCHITECTURE-ASSET-REGISTRY-RUNTIME-METADATA`
- `HIGH-ARCHITECTURE-CATALOG-REPRESENTATION-SELECTION`
- `HIGH-SECURITY-CATALOG-IMAGE-PATH-VALIDATION`

Do not assume that a related task is completed or approved merely because it is listed here.
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
