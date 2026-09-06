---
status: backlog
priority: HIGH
type: feature
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md"
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

# TASK: C2 — Implement Catalog Data Wiring
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: VALID — catalog data wiring is prerequisite for C3/C4 vertical slices.
- **MVP Impact Note**: Implements the approved B2 data contract wiring for catalog presentation without building the full page.
- **Action Line**: NEEDS B2 APPROVED DESIGN + C1 COMPLETED BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires service/controller code changes and focused test implementation.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B2 approved design** — must be reviewed and approved before starting C2
5. **C1 completed** — Asset Registry mapping must be implemented first

---

## Context

Implement the approved B2 data contract wiring for catalog presentation without building the full page. B2 defines the required presentation inputs for Components and Units/Structures/Vehicles. Add only the wiring required to expose the contract to the catalog presentation layer. Cover RH-400 Unit and I-beam Component cases in focused tests. Do not solve unresolved architecture outside B2.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B2 approved design document — the source of truth for what to implement.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not solve unresolved architecture outside B2.
- ❌ Wrong: Decide the Production/Presentation split or propose broader architectural changes.
- ✅ Right: Implement only the wiring specified by B2; flag unresolved decisions for separate handling.
- Why: The Production/Presentation split is an intentional architectural decision point — not a C2 concern.

⚠️ **GOTCHA 2**: Do not load docs/ as runtime data.
- ❌ Wrong: Add Docker mounts for docs/ or treat documentation as application data.
- ✅ Right: Use existing canonical game-data lookup architecture for production data.
- Why: The Production/Presentation split is intentional — production runtime must not depend on docs/.

⚠️ **GOTCHA 3**: Components (I-beam) have no Operational Data.
- ❌ Wrong: Generate fake Operational Data for the Component case.
- ✅ Right: Components = Blueprint + Visual only; Units/Structures/Vehicles = Blueprint + Operational Data + Visual.
- Why: Not every catalog entry has Operational Data. The I-beam case must not produce empty/fake Unit sections.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an implementation task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: C2 — Implement Catalog Data Wiring
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Implement the approved B2 data contract wiring for catalog presentation. Add only the wiring required to expose the contract to the catalog presentation layer. Cover RH-400 Unit and I-beam Component cases in focused tests. No full page build, no docs/ runtime dependencies.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B2 approved design | Implementation source of truth | reviewed |
| [Files named by B2] | Target files for wiring | pending |
| spec/[path]/[file]_spec.rb | Focused tests for RH-400 + I-beam | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B2 approved design
- ✅ Understand architecture gotchas above

### Expected Outcomes
RH-400 Unit data assembled correctly; I-beam Component data assembled correctly; no docs runtime dependency; focused tests pass.

### Critical Gotchas I Will Avoid
- ❌ Solving unresolved Production/Presentation architecture — instead ✅ Implementing only B2-specified wiring
- ❌ Loading docs/ as runtime data — instead ✅ Using existing game-data lookup architecture
- ❌ Generating fake Operational Data for Components — instead ✅ Components = Blueprint + Visual only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Implement the approved B2 data contract wiring for catalog presentation without building the full page.

**Current behavior**: B2 defines the required presentation inputs for Components and Units/Structures/Vehicles.
**Expected behavior**: Implement only the wiring specified by B2. Cover RH-400 Unit and I-beam Component cases in focused tests. Stop before expanding into full catalog UI.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| [Files named by B2 design] | Data wiring implementation | As specified by B2 |
| spec/[path]/[file]_spec.rb | Focused tests for RH-400 + I-beam | New or modified |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| B2 approved design | Implementation source of truth |
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |

### Migration
- [x] No migration needed — wiring only, no schema changes

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them. Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Read B2 approved design

Confirm approved inputs and object-class branches. Specifically:
- What fields does each object class need?
- Which files need wiring?
- What is the expected data flow?

### Step 2 — Implement data assembly

Add only the wiring required to expose the contract to the catalog presentation layer. Specifically:
- Wire Blueprint data retrieval for both Components and Units
- Wire Operational Data retrieval for Units (not Components)
- Wire Visual Definition/asset references for both

### Step 3 — Add focused tests

Cover RH-400 Unit and I-beam Component cases. Tests should verify:
- RH-400 has Blueprint + Operational Data + Visual
- I-beam has Blueprint + Visual only (no fake Operational Data)
- Asset paths resolve correctly for both

### Step 4 — Verify

Run focused tests and report exact results:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

---

## Acceptance Criteria
- [ ] RH-400 Unit data is assembled correctly (Blueprint + Operational Data + Visual)
- [ ] I-beam Component data is assembled correctly (Blueprint + Visual only, no fake Operational Data)
- [ ] No docs runtime dependency
- [ ] Focused tests pass with exact results recorded
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- B2 requires a new shared data model (beyond wiring scope)
- Existing catalog architecture cannot support the contract without a broader decision
- Tests reveal a contradiction in B2

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: B2, C1
**Blocks**: C3, C4
**Related tasks**: A4

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: C2 | [result] | [next action]
