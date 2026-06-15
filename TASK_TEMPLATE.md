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

---
status: backlog
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER | MANUFACTURING | TERRA_SIM | CONTROLLERS | UNITS | OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION | SPEC_HEALTH | OTHER
local_worker_safe: true | false
---

# TASK: [Short descriptive title]
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: CRITICAL | HIGH | MEDIUM | LOW
**Type**: bug-fix | feature | refactor | architecture | data | documentation
**Created**: YYYY-MM-DD
**Last Updated**: YYYY-MM-DD

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only — they cannot run commands or access the DB*

- **Template Conformance**: PASS | FAIL — [note missing sections]
- **Docker Wrapper Check**: PASS | FAIL | N/A — [verify RSpec strings use correct docker exec format without cd /home/galaxy_game]
- **MVP Alignment**: VALID | STALE | OBSOLETE — [does this task still apply to current codebase]
- **MVP Impact Note**: [one line on how this connects to AI Manager Luna settlement or spec health]
- **Action Line**: READY FOR LOCAL DISPATCH | NEEDS MANUAL REVIEW | OBSOLETE — ARCHIVE

---

## Agent Assignment

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

## Context
[2-4 sentences explaining what this part of the system does and why this task exists.]

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/[path]/[file].md` — [one line on what it covers]

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

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

> All agents: follow these steps exactly in order.
> Do not skip steps or reorder them.
> Do not proceed to the next step if the current step has not produced a clean result.

**Debug prints OK for complex callbacks** — add temporary `puts` statements, remove after verification.

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
> Never run bare local test commands. Never fabricate test results.

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
