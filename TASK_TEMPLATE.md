# Task File Template
# Copy this file, rename it to describe the task, fill in all sections.
# Delete these instruction comments before saving.
# Place in docs/new_agent/projects/{project name}/tasks/backlog/{YYYY-MM}/ or active/ as appropriate.
#
# FILENAME CONVENTION — mandatory for all task files:
#   YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTIVE-NAME.md
#
#   YYYY-MM-DD  = date the task was created (not assigned, not completed)
#   PRIORITY    = CRITICAL | HIGH | MEDIUM | LOW
#   TYPE        = bug-fix | feature | refactor | architecture | data | documentation
#   NAME        = kebab-case, descriptive, no spaces
#
#   Examples:
#     2026-03-29-HIGH-REFACTOR-WORMHOLE-EXPANSION-SERVICE.md
#     2026-03-27-MEDIUM-FEATURE-FINANCIAL-TRANSACTION-MODEL.md
#     2026-03-30-LOW-DOCUMENTATION-BACKLOG-AUDIT-AND-RENAME.md
#
#   Why this matters:
#     - Date = recency signal. Stale tasks predate architecture decisions.
#     - Priority = triage at a glance without opening the file.
#     - No date prefix = task must be reviewed before assignment, may be obsolete.
#
# DEPTH GUIDE — how much detail to include per agent tier:
#   Local Qwen3.5-27B (primary) — explicit file paths, before/after code, exact docker commands
#   0.33x cloud fallback (Haiku/GPT-5 mini/Raptor mini) — same as above, only after two local failures
#   Claude Sonnet (premium/web) — architecture and strategy only, never implementation
#
# Rule: when in doubt, add more detail. An over-specified task wastes nothing.
# An under-specified task burns premium requests on clarification.
#
# AGENT SELECTION RULE: Always assign to local Qwen3.5-27B first.
# Only escalate to cloud after two genuine failures in fresh local sessions.
#
# HANDOFF STRATEGY:
#   The HANDOFF MESSAGE should be minimal (2-4 lines) and ONLY point to this file.
#   All critical information goes HERE in the task file:
#     - Credentials (usernames, passwords, URLs)
#     - Multi-step workflows (prerequisites, read order)
#     - Architecture gotchas (what NOT to do, why)
#     - Acceptance criteria (what "done" looks like)
#     - Synthesis requirement (agent must report before starting work)
#   DO NOT duplicate this info in the handoff. Keep handoff as minimal pointer.

---
status: backlog
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER | MANUFACTURING | TERRA_SIM | CONTROLLERS | UNITS | OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION | SPEC_HEALTH | OTHER
local_worker_safe: true | false
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: [project_name]
Task: /Users/tam0013/Documents/git/agent-tasks/projects/[project]/tasks/active/[FILENAME].md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: [Short descriptive title]
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: CRITICAL | HIGH | MEDIUM | LOW
**Type**: bug-fix | feature | refactor | architecture | data | documentation
**Created**: YYYY-MM-DD
**Last Updated**: YYYY-MM-DD

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Ollama via Continue) during backlog review*
*This section is NOT sent to agents — it's for human task management only*
*Local models read task files only — they cannot run commands or access the DB*

- **Template Conformance**: PASS | FAIL — [note missing sections]
- **Docker Wrapper Check**: PASS | FAIL | N/A — [verify RSpec strings use correct docker exec format without cd /home/galaxy_game]
- **MVP Alignment**: VALID | STALE | OBSOLETE — [does this task still apply to current codebase]
- **MVP Impact Note**: [one line on how this connects to AI Manager Luna settlement or spec health]
- **Action Line**: READY FOR LOCAL DISPATCH | NEEDS MANUAL REVIEW | OBSOLETE — ARCHIVE

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: [Qwen3.5-27B local (primary) | Qwen3.5-9B local | Claude Haiku 0.33x | GPT-5 mini 0.33x | Raptor mini 0.33x]
**Why This Agent**: [one line — if cloud agent, state why local failed]
**Local attempts before cloud**: [N/A | 1 | 2 — cloud only dispatched after 2 local failures]
**Supervision Level**: [watched carefully | standard | autonomous OK]

**Supervision Legend**:
- Watched carefully = all agents on first dispatch of a task
- Standard = local Qwen3.5-27B on well-specified repeat task types
- Autonomous OK = not currently used — all tasks require human approval before commit

> **Primary executor is always local Qwen3.5-27B (Copilot).**
> Cloud agents (Haiku, GPT-5 mini, Raptor mini) are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/path/to/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/path/to/agent-tasks/projects/[project]/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
[2-4 sentences explaining what this part of the system does and why this task exists.]

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/[path]/[file].md` — [one line on what it covers]

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials (if needed)
| Field | Value | Notes |
|-------|-------|-------|
| Username/Email | `[email]` | [shared/unique/one-time] |
| Password | `[password]` | [scope — what systems can this access] |
| API Key | `[key]` | [environment — prod/staging/local] |
| URL/Endpoint | `https://[url]` | [usage — where to test] |

> Credentials are pre-seeded. DO NOT create new users unless explicitly instructed.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: [What NOT to do]
- ❌ Wrong: [example of wrong approach]
- ✅ Right: [example of correct approach]
- Why: [explanation]

⚠️ **GOTCHA 2**: [What NOT to do]
- ❌ Wrong: [example]
- ✅ Right: [example]
- Why: [explanation]

### Multi-Domain / Multi-Tenant Routing (if applicable)

| Domain/Route | Purpose | What Features Available | What NOT Available |
|---|---|---|---|
| Admin domain | System config, tenant creation | User management, settings | NO feature work, NO content repos |
| Tenant domain | Repository operations | Works, collections, batch edit | NO system config |

> If confused, ask: "Which domain am I supposed to be testing on?" If you're getting 404 or permission errors, you may be on the wrong domain.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template**:
```
## STATUS SYNTHESIS REPORT

**Task**: [name from filename]
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `path/to/file` | [description] | [not started / pending / done] |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide  
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ [wrong approach] — instead ✅ [right approach]
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
[Exact description of what is wrong or missing.]

**Error output** (if applicable):
```
[paste exact error here]
```

**Current behavior**: [what happens now]
**Expected behavior**: [what should happen]

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/models/[file].rb` | [what it does] | `#method_name` line ~N |
| `app/services/[file].rb` | [what it does] | `#method_name` line ~N |
| `spec/[path]/[file]_spec.rb` | [what it tests] | line ~N |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/factories/[file].rb` | [e.g. factory structure for this model] |
| `data/json-data/[path]/[file].json` | [e.g. operational data structure] |

### Migration (if needed)
- [ ] No migration needed
- [ ] Migration needed: `[describe the schema change]`

**If migration needed: follow GUARDRAILS Rule 2 before proceeding.**

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: You must have completed and posted your STATUS SYNTHESIS REPORT above.
> Do not proceed with any of these steps until synthesis is posted and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 1 — [action]
[Exact description of what to do]

```ruby
# before
def example_method
  old_code
end

# after
def example_method
  new_code
end
```

### Step 2 — [action]
[Exact description]

### Step 3 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

### Step 4 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: [file:line]
Error: [exact message]
Expected: [value]
Got: [value]

ROOT CAUSE
[one paragraph]

PROPOSED FIX
[exact code change]

RISK
[any shared code affected]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] [specific measurable outcome]
- [ ] Isolation run: 0 failures
- [ ] No regressions in related specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion — use git mv, never copy:**
```bash
git mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]
git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: [task file name or "none"]
**Blocks**: [task file name or "none"]
**Related tasks**: [task file name or "none"]

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
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
