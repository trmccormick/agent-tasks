---
status: backlog
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md \
         projects/galaxy_game/tasks/active/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md"
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

# TASK: C5 — Implement Surface Asset Integration
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: AI_MANAGER_LUNA_SETTLEMENT — surface asset integration connects generated sprites to the surface renderer.
- **MVP Impact Note**: Implements only the approved B3 surface asset representation contract for the RH-400 surface sprite.
- **Action Line**: NEEDS B3 APPROVED DESIGN BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires connecting generated surface asset to existing renderer.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B3 approved design** — surface asset representation contract (must be reviewed and approved before starting C5)

---

## Context

Implement only the approved B3 surface asset representation contract for the RH-400 surface sprite. This connects the static generated surface asset to existing surface rendering without changing catalog presentation. Transparent surface sprite and animation assets are separate from catalog renders. Do not introduce baked terrain backgrounds. Do not generalize to every asset family in this task.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B3 approved design document — the source of truth for surface asset integration.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Transparent surface sprite and animation assets are separate from catalog renders.
- ❌ Wrong: Use catalog render as surface sprite or introduce baked terrain backgrounds into generated surface images.
- ✅ Right: Keep surface sprites as transparent PNGs; background/composition is the renderer's responsibility.
- Why: The surface renderer composes assets dynamically based on terrain, lighting, and game state.

⚠️ **GOTCHA 2**: Do not generalize to every asset family in this task.
- ❌ Wrong: Build a universal surface asset system that handles all asset families.
- ✅ Right: Connect only the RH-400 surface sprite through the approved identity path; generalize later if needed.
- Why: This is a narrow integration task — validating the B3 contract with one asset family first.

⚠️ **GOTCHA 3**: Catalog assets must remain unaffected.
- ❌ Wrong: Modify catalog render/icon files or paths as part of surface asset integration.
- ✅ Right: Surface asset integration uses separate asset paths and does not touch catalog presentation assets.
- Why: Catalog and surface have different consumers, different technical contracts, and different update cycles.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an implementation task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: C5 — Implement Surface Asset Integration
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Implement only the approved B3 surface asset representation contract for the RH-400 surface sprite. Connect the existing surface sprite identity/path to the renderer. Verify rendering. Catalog assets remain unaffected. No generalization to other asset families.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B3 approved design | Surface asset contract | reviewed |
| A5 findings | Surface renderer behavior | reviewed |
| A3 findings | RH-400 surface sprite path | reviewed |
| [Renderer files to modify] | Wire RH-400 surface representation | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B3 design and A5 findings
- ✅ Understand architecture gotchas above

### Expected Outcomes
RH-400 surface sprite is discoverable through the approved identity; renderer consumes transparent sprite correctly; catalog assets remain unaffected; focused checks pass.

### Critical Gotchas I Will Avoid
- ❌ Using catalog render as surface sprite — instead ✅ Using exact surface sprite file from A3
- ❌ Introducing baked terrain backgrounds — instead ✅ Keeping transparent PNG format
- ❌ Modifying catalog assets — instead ✅ Surface integration uses separate paths only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Implement only the approved B3 surface asset representation contract for the RH-400 surface sprite.

**Current behavior**: B3 defines how surface representation relates to `asset_id`/`asset_family`. A5 establishes the current surface renderer.
**Expected behavior**: Connect the existing surface sprite identity/path to the renderer. Verify rendering. Stop before generalizing.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| [Renderer files per B3] | Wire RH-400 surface representation | As specified by B3 |
| [Verification tests] | Surface-layer checks | New or modified |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| B3 approved design | Surface asset contract |
| A5 findings | Surface renderer behavior |
| A3 findings | RH-400 surface sprite path |

### Migration
- [x] No migration needed — integration only, no schema changes

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them. Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md \
       projects/galaxy_game/tasks/active/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-MEDIUM-FEATURE-ASSET-UI-C5-implement-surface-asset-integration.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Read B3 and A5

Confirm the approved integration contract. Specifically:
- What identity/path does B3 specify for surface representation?
- How does the current renderer consume sprites (A5)?
- What is the RH-400 surface sprite file path (A3)?

### Step 2 — Wire RH-400 surface representation

Connect the existing surface sprite identity/path to the renderer. Specifically:
- Use the exact surface sprite file identified by A3
- Wire it through the identity mechanism specified by B3
- Do not modify catalog render/icon files

### Step 3 — Verify rendering

Run the narrowest available surface-layer checks. Specifically:
- Does the RH-400 surface sprite render correctly (transparent background)?
- Is the sprite discoverable through the approved identity path?
- Are catalog assets unaffected?

---

## Acceptance Criteria
- [ ] RH-400 surface sprite is discoverable through the approved identity
- [ ] Renderer consumes transparent sprite correctly
- [ ] Catalog assets remain unaffected
- [ ] Focused checks pass
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Renderer requires a new global asset system beyond B3
- Existing surface architecture cannot consume the representation without redesign
- Generated sprite format is incompatible with renderer requirements

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
**Blocked by**: B3
**Blocks**: D3
**Related tasks**: A5

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
HANDOFF SUMMARY: C5 | [result] | [next action]
