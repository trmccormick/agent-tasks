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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md"
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

# TASK: C1 — Implement Asset Registry Mapping
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: VALID — first shared/global implementation; catalog presentation depends on this mapping.
- **MVP Impact Note**: Implements the reviewed B1 Asset Registry-to-Visual Definition mapping — the first shared implementation in this workstream.
- **Action Line**: NEEDS B1 APPROVED DESIGN BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires code changes and focused test implementation.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B1 approved design** — must be reviewed and approved before starting C1

---

## Context

Implement only the reviewed B1 Asset Registry-to-Visual Definition mapping. This is the first shared/global implementation task in this workstream. Modify only the files named by the approved B1 design. Add focused tests to verify the mapping without introducing unrelated behavior. Do not expand into catalog UI (that's C2-C4).

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B1 approved design document — the source of truth for what to implement.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not redesign Visual Definition.
- ❌ Wrong: Propose changes to Visual Definition beyond what B1 specifies.
- ✅ Right: Implement only the mapping fields explicitly named by the approved B1 design.
- Why: Visual Definition is a locked schema — any change requires separate approval.

⚠️ **GOTCHA 2**: Do not modify Visual Profiles or Render Templates.
- ❌ Wrong: Include Visual Profile or Render Template changes in this implementation.
- ✅ Right: These are locked architectural decisions — they remain out of scope.
- Why: Any change to these requires separate architectural approval.

⚠️ **GOTCHA 3**: Do not add runtime dependencies on docs/.
- ❌ Wrong: Add Docker mounts for docs/ or treat documentation as application data.
- ✅ Right: Asset-generation documentation remains development-time source material only.
- Why: The Production/Presentation split is intentional — production runtime must not depend on docs/.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an implementation task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: C1 — Implement Asset Registry Mapping
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Implement only the reviewed B1 Asset Registry-to-Visual Definition mapping. Modify only the files named by the approved B1 design. Add focused tests to verify the mapping. No catalog UI, no Visual Profile/Render Template changes, no docs/ runtime dependencies.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B1 approved design | Implementation source of truth | reviewed |
| [Files named by B1] | Target files for modification | pending |
| spec/[path]/[file]_spec.rb | Focused tests for the mapping | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B1 approved design
- ✅ Understand architecture gotchas above

### Expected Outcomes
B1 mapping implemented exactly as designed; focused tests pass; no Visual Profile/Render Template changes; no docs Docker mount/runtime dependency; no unrelated files changed.

### Critical Gotchas I Will Avoid
- ❌ Redesigning Visual Definition — instead ✅ Implementing only B1-specified fields
- ❌ Modifying locked Visual Profiles or Render Templates — instead ✅ Working within existing schema
- ❌ Adding docs/ as runtime dependency — instead ✅ Treating all docs/ as development-time source only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Implement only the reviewed B1 Asset Registry-to-Visual Definition mapping.

**Current behavior**: B1 has been reviewed and approved with a specific design.
**Expected behavior**: Implement exactly what B1 specifies — no more, no less. Add focused tests. Stop before expanding into catalog UI.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| [Files named by B1 design] | Mapping implementation | As specified by B1 |
| spec/[path]/[file]_spec.rb | Focused tests for the mapping | New or modified |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| B1 approved design | Implementation source of truth |
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |

### Migration
- [ ] No migration needed
- [ ] Migration needed: [describe the schema change — only if B1 specifies one]

**If migration needed: follow GUARDRAILS Rule 2 before proceeding.**

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them. Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Read B1 approved design

Verify the approved mapping and current code state. Confirm:
- Which files need to be modified (per B1)
- What fields are being added/mapped
- What tests need to be added/modified

### Step 2 — Implement the smallest mapping

Modify only the files named by the approved B1 design. Do not add fields, models, or services beyond what B1 specifies.

### Step 3 — Add focused tests

Test the mapping without introducing unrelated behavior. Tests should cover:
- The specific fields being mapped
- Edge cases identified in B1
- No broader catalog or UI behavior

### Step 4 — Run the required tests

Use the repository's required Docker wrapper where applicable:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

### Step 5 — Stop

Do not expand into catalog UI. Do not modify files beyond what B1 specifies.

---

## Acceptance Criteria
- [ ] B1 mapping implemented exactly as designed (no additions, no omissions)
- [ ] Focused tests pass with exact results recorded
- [ ] No Visual Profile/Render Template changes
- [ ] No docs Docker mount/runtime dependency
- [ ] No unrelated files changed
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Shared schema change becomes necessary (beyond B1 scope)
- Tests reveal a contradiction in B1
- More files than B1 specifies must change
- Implementation diverges materially from B1 design

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
**Blocked by**: B1
**Blocks**: D1
**Related tasks**: A1

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
HANDOFF SUMMARY: C1 | [result] | [next action]
