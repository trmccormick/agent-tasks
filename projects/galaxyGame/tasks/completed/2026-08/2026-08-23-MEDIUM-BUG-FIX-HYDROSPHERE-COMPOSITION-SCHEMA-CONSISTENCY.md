---
status: completed
priority: MEDIUM
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

You are Research Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/current/2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md
projects/galaxy_game/tasks/active/2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.

READ FIRST (after Step 0): Task file contains all context and verification steps below.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: 2026-08-23-RESEARCH-HYDROSPHERE-COMPOSITION-SCHEMA.md
Chat is for questions only — never paste synthesis into chat.

This is a RESEARCH/VERIFICATION task, not an implementation task. Do not apply any fix until
the diagnosis is confirmed and reported back.


---

# TASK: Hydrosphere composition schema inconsistency in sol-complete.json and sol.json (Titan vs Earth)

**Status**: ACTIVE
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-23
**Last Updated**: 2026-08-24

---

## Context

`sol-complete.json` and `sol.json` stores each celestial body's `hydrosphere_attributes.composition` field, which will likely become a dependency for any future resource-acquisition/escalation logic that needs to know what a body's hydrosphere is actually made of (e.g. Titan's liquid methane/ethane vs Earth's liquid water) before deciding how the AI Manager can fulfill a resource request from that reservoir. Two entries observed directly in the file use two different shapes for the same field:

- Titan: `"composition": {"CH4": 50.0, "C2H6": 30.0, "N2": 20.0}` — flat object, compound name as key
- Earth: `"composition": [{"compound": "H2O", "percentage": 96.5}, {"compound": "dissolved_salts", "percentage": 3.5}]` — array of objects

This surfaced during a design discussion about generalizing `EscalationService`'s resource-acquisition routing to consult per-body composition data (regolith, hydrosphere, atmosphere) rather than routing purely on `order.resource` name. Before any such design work proceeds, this schema question needs a real answer, not an assumption in either direction.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

> If a doc doesn't exist for hydrosphere/composition data conventions, do not create one during this task. Flag the gap in your completion report instead.

---

## Problem Statement

**Current behavior (unconfirmed)**: `sol-complete.json` contains at least two different shapes for `hydrosphere_attributes.composition` across different bodies in the same file.

**Unknown / to be determined by this task**:
1. Is this inconsistency present across the whole file, or just these two bodies? Grep every `hydrosphere_attributes` block in `sol-complete.json` (and any other body-data JSON files that carry this field) and report each body's composition shape.
2. Does anything in the codebase currently *read* `hydrosphere_attributes.composition`? If so, which shape does that reader assume, and does it silently mishandle the other shape (returns nil/empty rather than erroring) or does it genuinely handle both?
3. Is there a canonical/intended schema documented anywhere (JSON schema file, a comment, DECISIONS.md, or similar), or was this never formally decided?

**Expected behavior**: One consistent schema for `composition` across all bodies, confirmed either already enforced by a normalizing reader (in which case this task closes as "verified non-issue, no fix needed") or requiring a data fix to bring all bodies to one shape.

---

## Files Involved

### Primary Files — likely relevant, confirm before editing
| File | Purpose | Key Method/Section |
|---|---|---|
| `data/json-data/.../sol-complete.json` | Body data including hydrosphere_attributes | `hydrosphere_attributes.composition`, all bodies |
| `[FILL IN]` | Whatever service/model reads hydrosphere composition, if any exists | `[FILL IN]` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `[FILL IN]` | Any geosphere/material composition reader, for comparison — same question may apply to regolith composition data |

### Migration (if needed)
- [x] No migration needed (this is a data-file consistency question, not schema/DB)

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
[Standard — see dispatch interface above]

### Step 1 — Inventory every `hydrosphere_attributes.composition` block
Grep/search `sol-complete.json` (and confirm whether other body-data files carry this field) for every instance of `composition` under `hydrosphere_attributes`. List each body and which shape it uses (flat object vs array-of-objects vs any third variant).

### Step 2 — Find the reader (if one exists)
Search the codebase for anything that accesses `hydrosphere_attributes` or `composition` in this context (e.g. a `Geosphere`/`Hydrosphere` concern, `MaterialLookupService`-equivalent, or similar). Report:
- Does it exist at all yet, or is this field currently write-only/unread?
- If it exists, which shape does it assume? Does it break, silently return nothing, or genuinely handle both shapes for the other body's format?

### Step 3 — Report, do not fix yet
This task stops at diagnosis. Do not normalize the JSON or write a reader-side fix until the synthesis report is reviewed and a fix approach is approved.

### Step 4 — Synthesis Report (before any fix, before committing anything)

SYNTHESIS REPORT
Bodies inventoried: [list, with each body's composition shape]
Reader found: [yes/no — file:line if yes]
Reader behavior: [handles both / assumes one shape / field currently unread by anything]

ROOT CAUSE (or "no bug — reader normalizes both shapes")
[one paragraph]

PROPOSED FIX (only if needed)
[data normalization to one shape, or reader-side fix, or "no fix needed"]

RISK
[any other code/data that would be affected by normalizing the JSON shape]

READY TO APPLY? — waiting for approval


---

## Acceptance Criteria
- [ ] Every body's `hydrosphere_attributes.composition` shape is inventoried, not just Titan/Earth
- [ ] Confirmed whether any reader exists and what it assumes — with file:line reference, not a guess
- [ ] Synthesis report delivered before any fix is applied
- [ ] If a fix is needed, it is proposed but NOT applied without separate approval

---

## Stop Conditions — escalate to user immediately if:
- No reader for this field exists yet anywhere in the codebase (means this is lower urgency than assumed — confirm before treating as blocking anything)
- The inconsistency turns out to be more than two shapes across the file
- Fixing the data would require touching a shared celestial-body generation service

---

## Dependencies
**Blocked by**: none
**Blocks**: none yet — feeds into a not-yet-filed design task on generalizing EscalationService resource-acquisition routing to consult body composition data (regolith/hydrosphere/atmosphere) per-material
**Related tasks**: none filed yet — see [[magnetosphere-architecture]]-adjacent data-driven-generation principle (no hardcoded per-body logic) for the broader convention this touches

---

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
*Filled in at end of session*