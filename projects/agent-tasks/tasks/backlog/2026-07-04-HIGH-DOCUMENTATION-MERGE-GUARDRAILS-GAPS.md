---
status: backlog
priority: HIGH
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
cross_project_impact: true
---

# TASK: Merge Two Generic Agent Rules Gaps into agent-tasks/rules/GUARDRAILS.md

**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-07-04
**Last Updated**: 2026-07-04
**Author**: STRATEGIST (session strategy)

**⚠️ CROSS-PROJECT IMPACT**: This task edits `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`, the authoritative generic agent rules file read by every project (Galaxy Game, Samvera, WVU Libraries). Changes affect ALL projects — review carefully.

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: agent-tasks
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/agent-tasks/tasks/backlog/2026-07-04-HIGH-DOCUMENTATION-MERGE-GUARDRAILS-GAPS.md

READ FIRST: Task file contains exact diffs for both gaps. Present diff to human before committing.
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

During the GUARDRAILS consolidation (2026-07-03), two generic agent rules were identified as genuinely MISSING from `agent-tasks/rules/GUARDRAILS.md` — they exist in galaxyGame's docs/GUARDRAILS.md but have no corresponding rule in the cross-project authoritative file. These gaps must be merged to prevent future agents from losing critical safety requirements.

The consolidation task (`2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md`) intentionally left these unwritten because they affect all projects, not just galaxyGame. This task exists solely to execute those two merges.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Source Rules File**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (authoritative, 425 lines)
3. **This Task File**: Everything below

---

## Gap A: `unset DATABASE_URL && RAILS_ENV=test` Mandatory Prefix

### What's Missing
`agent-tasks/rules/GUARDRAILS.md` mentions `RAILS_ENV=test` in examples but does NOT have an explicit rule stating `unset DATABASE_URL` is mandatory for ALL test commands. galaxyGame's docs/GUARDRAILS.md has this as a critical safety requirement with incident precedent (2026-01-24).

### What to Add — Exact Diff

**Location**: Enhancement to Rule 2 and Rule 3 in `agent-tasks/rules/GUARDRAILS.md`

#### Change 1: Enhance Rule 2 "After generating" block
Find this text in Rule 2:
```markdown
After generating, run in both environments:
```bash
docker exec -it web bash -c 'rails db:migrate && RAILS_ENV=test rails db:migrate'
```
```

Replace with:
```markdown
After generating, run in both environments (MANDATORY `unset DATABASE_URL`):
```bash
docker exec -it web bash -c 'unset DATABASE_URL && rails db:migrate && RAILS_ENV=test rails db:migrate'
```
```

#### Change 2: Enhance Rule 3 "Correct form" example
Find this text in Rule 3:
```markdown
**Correct:**
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/shortage_detector_spec.rb 2>&1 | tail -20'
```
```

Add a new sub-bullet under Rule 3 after the "Correct/Wrong" examples:
```markdown
> **MANDATORY PREFIX**: ALL test commands MUST include `unset DATABASE_URL && RAILS_ENV=test` as the first part of the bash -c argument. This prevents dev database corruption from environment bleed. See incident precedent [2026-01-24].
```

#### Change 3: Add to Rule 3a (Pre-execution check)
Find this text in Rule 3a:
```markdown
**If ANY answer is NO or unclear, STOP and ask the human before proceeding.**
```

Add after it:
```markdown
> **DATABASE_URL CHECK**: The PRE-RSPEC EXECUTION CHECK must verify `unset DATABASE_URL` appears in the command. If it does not, STOP — this is a dev database corruption risk.
```

### Why This Is a Gap
- agent-tasks/rules/GUARDRAILS.md Rule 2 mentions `RAILS_ENV=test` but does NOT mention `unset DATABASE_URL` as mandatory
- galaxyGame's docs/GUARDRAILS.md has this as a critical safety requirement with incident precedent (2026-01-24: RSpec executed via docker-compose without RAILS_ENV=test, corrupting development database)
- This is not covered by any other rule in agent-tasks/rules/GUARDRAILS.md

---

## Gap B: Container Lifecycle Prohibition ("Containers Always Running")

### What's Missing
`agent-tasks/rules/GUARDRAILS.md` Rule 1 says "use `docker exec -it web`" but does NOT explicitly prohibit starting/stopping containers. galaxyGame's docs/GUARDRAILS.md has this as a critical rule with incident precedent (2026-01-23: unintended configuration changes during file review session disrupted workflow).

### What to Add — Exact Diff

**Location**: Enhancement to Rule 1 Docker section in `agent-tasks/rules/GUARDRAILS.md`

Find the end of the "Correct/Wrong" examples in Rule 1 (after the Wrong example):
```markdown
**Wrong:**
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/shortage_detector_spec.rb 2>&1 | tail -20'
```
```

Add a new sub-heading after the Wrong example:
```markdown
### Containers Are Always Running — DO NOT Start, Stop, or Restart

The full stack (web, db, and all services) is **assumed to be running at all times** during a development session. 

- **NEVER** run `docker-compose up`, `docker-compose restart`, `docker-compose stop`, `docker-compose down`, or `docker-compose build` without explicit user instruction
- **NEVER** check container state as a precursor to starting them — they are already running
- **NEVER** use `docker-compose exec` — use `docker exec -it web` to access the already-running web container
- If a command fails due to a container issue, **report it to the user** — do not attempt to restart anything

**Incident Precedent [2026-01-23]**: Unintended configuration changes during file review session disrupted user workflow and required manual reversion. Added explicit prohibition on config changes without permission.
```

### Why This Is a Gap
- agent-tasks/rules/GUARDRAILS.md Rule 1 says "use `docker exec -it web`" but does NOT explicitly prohibit starting/stopping containers
- galaxyGame's docs/GUARDRAILS.md has this as a critical rule with incident precedent (2026-01-23)
- This is not covered by any other rule in agent-tasks/rules/GUARDRAILS.md

---

## Acceptance Criteria

- [ ] **Gap A merged**: `unset DATABASE_URL && RAILS_ENV=test` mandatory prefix added to Rule 2, Rule 3, and Rule 3a of agent-tasks/rules/GUARDRAILS.md
- [ ] **Gap B merged**: Container lifecycle prohibition clause added as new sub-heading under Rule 1 in agent-tasks/rules/GUARDRAILS.md
- [ ] **Line count verified**: agent-tasks/rules/GUARDRAILS.md line count confirmed (expect ~425 + additions)
- [ ] **Diff presented to human**: Full diff of changes presented for explicit approval before committing
- [ ] **No other files edited**: Only agent-tasks/rules/GUARDRAILS.md is modified

---

## Stop Conditions — escalate to user immediately if:
- The file has been reorganized since this task was created (line numbers may have shifted)
- Any of the three gaps appear to already be covered by existing rules
- You cannot find the exact text to replace in agent-tasks/rules/GUARDRAILS.md

---

## Commit Instructions
Run git commands on **host only**:
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git add rules/GUARDRAILS.md
git commit -m "docs: merge two generic agent rules gaps from galaxyGame consolidation

- Gap A: Add mandatory `unset DATABASE_URL && RAILS_ENV=test` prefix to Rule 2, 3, 3a
- Gap B: Add container lifecycle prohibition clause to Rule 1
- Source: galaxyGame docs/GUARDRAILS.md incident precedents [2026-01-24] and [2026-01-23]"
```

**Task file move on completion — use git mv, never copy:**
```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent
git mv projects/agent-tasks/tasks/backlog/2026-07-04-HIGH-DOCUMENTATION-MERGE-GUARDRAILS-GAPS.md projects/agent-tasks/tasks/completed/2026-07/2026-07-04-HIGH-DOCUMENTATION-MERGE-GUARDRAILS-GAPS.md
git commit -m "chore: move GUARDRAILS gaps merge task to completed/"
```

---

## Dependencies
**Blocked by**: None — this task is independent and can be executed immediately after human approval of the diffs.
**Blocks**: None — this is a standalone documentation merge.
**Related tasks**: `2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md` (the parent consolidation task that identified these gaps)

---

## Completion Report Template (For Executor to Fill)

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `rules/GUARDRAILS.md` — Added Gap A (DATABASE_URL mandatory prefix) and Gap B (container lifecycle prohibition)

### Diff presented to human?
- [ ] Yes — human approved before commit
- [ ] No — skipped (explain why)

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: rules/GUARDRAILS.md updated with 2 gaps | diff approved by human | task ready to move to completed/
