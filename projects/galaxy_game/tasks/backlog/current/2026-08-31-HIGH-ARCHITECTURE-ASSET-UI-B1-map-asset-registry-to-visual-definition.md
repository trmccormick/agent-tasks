---
status: backlog
priority: HIGH
type: architecture
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md"
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

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section is complete and accurate (no placeholders).
- [ ] All Step 0-N instructions are clear and actionable.
- [ ] Synthesis report template is provided and copy/paste ready.
- [ ] No placeholder text remains in Implementation Steps.
- [ ] Repository and task paths have been verified.
- [ ] Architecture Gotchas are specific (not generic).
- [ ] Acceptance Criteria are measurable.
- [ ] Dependencies and Blocked/Blocks relationships are clear.

---

# TASK: B1 — Map Asset Registry to Visual Definition
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — architecture task, no RSpec execution required.
- **MVP Alignment**: VALID — first shared/global implementation prerequisite; catalog presentation depends on this mapping.
- **MVP Impact Note**: Defines the smallest mapping between Asset Registry and Visual Definition Template v1.0 without duplicating responsibilities.
- **Action Line**: NEEDS A1 EVIDENCE BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires understanding of both Asset Registry concepts and Visual Definition fields.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **A1 findings** — must be completed and reviewed before starting B1

---

## Context

Define the smallest mapping between Asset Registry and the existing Visual Definition Template v1.0. Do not create a parallel asset-to-visual relationship. A1 provides repository evidence. Visual Definition already defines `asset_id`, `asset_family`, `recognition_features`, `material_profiles`, `technology_level`, `manufacturing_style` and related visual attributes. The registry must not duplicate their responsibilities. This is a design-only task — no code or schema changes.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- Visual Definition Template v1.0 — the existing schema to map onto (not replace).
- Asset-generation specifications in `docs/` are development-time source specifications, not Rails runtime data.
- Visual Profiles and Render Templates are locked/out-of-scope unless a task explicitly says otherwise.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not create a parallel relationship model.
- ❌ Wrong: Propose a new registry schema that duplicates Visual Definition's `asset_id`, `asset_family`, or visual attributes.
- ✅ Right: Map existing registry concepts directly onto Visual Definition fields; only add fields that are genuinely missing.
- Why: Visual Definition already owns the asset-to-visual relationship. The registry's role is metadata/categorization, not visual definition.

⚠️ **GOTCHA 2**: Visual Profiles and Render Templates remain locked.
- ❌ Wrong: Propose changes to Visual Profiles or Render Templates as part of the mapping.
- ✅ Right: Work within the existing Visual Definition Template v1.0 schema; flag missing fields separately.
- Why: These are locked architectural decisions — any change requires separate approval.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an architecture design task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: B1 — Map Asset Registry to Visual Definition
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Define the smallest mapping between the existing Asset Registry (from A1 evidence) and the existing Visual Definition Template v1.0. Identify which registry concepts map directly to Visual Definition fields and which do not. Produce design only — no code or schema changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| A1 synthesis report | Verified registry evidence | reviewed |
| Visual Definition Template v1.0 | Target schema for mapping | reviewed |
| DECISIONS.md / GUARDRAILS.md | Locked constraints | reviewed |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed A1 findings and Visual Definition Template v1.0
- ✅ Understand architecture gotchas above

### Expected Outcomes
Mapping uses existing Visual Definition fields; missing pieces are explicitly listed; no code/schema changes; design is review-ready before implementation.

### Critical Gotchas I Will Avoid
- ❌ Creating a parallel asset-to-visual model — instead ✅ Mapping onto existing Visual Definition Template v1.0
- ❌ Modifying locked Visual Profiles or Render Templates — instead ✅ Working within existing schema constraints

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Define the smallest mapping between Asset Registry and the existing Visual Definition Template v1.0. Do not create a parallel asset-to-visual relationship.

**Current behavior**: A1 provides repository evidence of what the registry currently stores or is intended to store.
**Expected behavior**: Produce a design-only mapping report that identifies which registry concepts map directly to Visual Definition fields and which require new fields, without modifying any code or schema.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| A1 synthesis report | Verified registry evidence | N/A (input) |
| Visual Definition Template v1.0 | Target schema for mapping | N/A (input) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |
| Existing asset-generation specifications in `docs/` | Canonical asset roles and generation architecture |

### Migration
- [x] No migration needed — design only, no code/schema changes

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B1-map-asset-registry-to-visual-definition.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Read A1 findings

Use the verified A1 report and relevant canonical docs. Confirm:
- What the registry currently stores (specification vs. implementation)
- What Visual Definition fields exist (from Template v1.0)
- Where overlap and gaps are

### Step 2 — Map fields

Identify which registry concepts map directly to Visual Definition fields. Create a field-by-field mapping table:
| Registry Concept | Visual Definition Field | Mapping Type |
|---|---|---|
| [concept] | [field] | direct / missing / needs new field |

### Step 3 — Define boundary

Specify the minimum contract needed for later catalog work (B2). Specifically:
- What does the registry need to store that Visual Definition does not?
- What does Visual Definition already cover that the registry should reference?
- Where is the cleanest boundary between registry metadata and visual definition?

### Step 4 — Produce design report

No code or schema changes. The report should answer:
1. Which registry concepts map directly to Visual Definition fields?
2. Which registry concepts are missing from Visual Definition?
3. What is the minimum new field(s) needed (if any)?
4. What is the cleanest boundary between registry and visual definition?

---

## Acceptance Criteria
- [ ] Mapping uses existing Visual Definition Template v1.0 fields (not a parallel model)
- [ ] No parallel visual relationship model introduced
- [ ] Missing fields are explicitly listed with justification
- [ ] Design is review-ready before implementation (no code/schema changes)
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Registry mapping requires a new database schema (not just a design gap)
- Visual Definition itself must change (requires separate architectural approval)
- Locked Visual Profile/Render Template responsibilities are implicated

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: A1
**Blocks**: C1
**Related tasks**: ASSET_GENERATION_PIPELINE_VALIDATION.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: N/A for research/design tasks unless tests are explicitly required.

### What was changed
Research/design findings only unless implementation is explicitly authorized.

### Issues discovered
[Fill in]

### Follow-up tasks needed
[Fill in]

### Lessons learned
[Fill in]

---

## Handoff Summary
HANDOFF SUMMARY: B1 | [result] | [next action]
