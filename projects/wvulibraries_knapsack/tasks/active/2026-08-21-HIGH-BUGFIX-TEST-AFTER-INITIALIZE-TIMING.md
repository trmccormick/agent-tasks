---
status: active
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Test Decorator Timing with after_initialize
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-08-21
**Last Updated**: 2026-08-21

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — verify RSpec strings use correct docker exec format without cd /home/galaxy_game
- **MVP Alignment**: VALID — this task still applies to current codebase
- **MVP Impact Note**: Facet limiting fix connects to spec health for catalog search functionality
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Local worker has terminal/tool-use access needed for Docker commands
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
The CatalogControllerDecorator uses a decorator pattern to prepend facet limiting behavior. Previous attempts using `to_prepare` broke Wings initialization (background jobs failed). A new approach using `after_initialize` callback in a lightweight initializer has been committed and needs testing.

**Relevant Architecture Docs** — read before starting:
- `/Users/tam0013/Documents/git/agent-tasks/rules/DECISIONS.md` — locked architectural decisions
- `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` — execution rules

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do NOT use `to_prepare` block — it breaks Wings initialization
- ❌ Wrong: `config.to_prepare { ... }` in initializer
- ✅ Right: `after_initialize` callback in lightweight initializer
- Why: `to_prepare` runs during Rails boot before Wings is ready, causing background job failures

⚠️ **GOTCHA 2**: Do NOT run bare local test commands — always use Docker wrapper
- ❌ Wrong: `bundle exec rspec spec/...`
- ✅ Right: `docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'`
- Why: The app runs inside Docker containers; local commands won't find gems or connect to DB

### Multi-Domain / Multi-Tenant Routing (if applicable)

| Domain/Route | Purpose | What Features Available | What NOT Available |
|---|---|---|---|
| Admin domain | System config, tenant creation | User management, settings | NO feature work, NO content repos |
| Tenant domain | Repository operations | Works, collections, batch edit | NO system config |

> If confused, ask: "Which domain am I supposed to be testing on?" If you're getting 404 or permission errors, you may be on the wrong domain.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Test Decorator Timing with after_initialize
**Status**: [backlog → active → completed]
**Date**: 2026-08-21

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `config/initializers/999_catalog_controller_decorator.rb` | New initializer with after_initialize | not started |
| `app/controllers/catalog_controller_decorator.rb` | Has search_builder_class override | pending |
| `app/search_builders/catalog_search_builder_wrapper.rb` | Core facet limiting fix | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
Facets show max 5 items, "More" link appears for facets with 5+ items, no Wings errors in logs, background jobs process normally.

### Critical Gotchas I Will Avoid
- ❌ Using `to_prepare` block — instead ✅ use `after_initialize` callback
- ❌ Running bare local test commands — instead ✅ use Docker wrapper for all commands

---

**SYNTHESIS COMPLETE.** Ready to proceed with testing decorator timing.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
The CatalogControllerDecorator facet limiting was not applying correctly due to Rails initialization timing. Facets were showing all items (36+) instead of the intended 5-item limit with a "More" link.

**Current behavior**: All facet items displayed, no "More" link
**Expected behavior**: Max 5 facet items displayed with "More" link for facets with more items

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `config/initializers/999_catalog_controller_decorator.rb` | New initializer with after_initialize callback | `Rails.application.config.after_initialize` |
| `app/controllers/catalog_controller_decorator.rb` | Has search_builder_class override | `search_builder_class` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/search_builders/catalog_search_builder_wrapper.rb` | Core facet limiting fix logic |
| `spec/**/*catalog*spec.rb` | Related test specs |

### Migration (if needed)
- [x] No migration needed

**If migration needed: follow GUARDRAILS Rule 2 before proceeding.**

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md \
       projects/wvulibraries_knapsack/tasks/active/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md
```

Then open the moved file and change the YAML status field:
```
status: active  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks \
     -name "2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Restart stack and verify Wings initialization

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
sh down.sc.local.sh && sleep 5 && sh up.sc.local.sh

# Wait 2-3 minutes for web container startup
# Verify: docker logs wvu_knapsack-web-1 2>&1 | tail -20
# Should show: "Listening on [IP]:3000"
```

### Step 2 — Check Rails Logs for Wings Errors

```bash
docker logs wvu_knapsack-web-1 2>&1 | grep -i "Wings::ModelRegistry\|DeserializationError" | head -5
```
**Expected**: No errors (Wings should initialize properly)

### Step 3 — Navigate to Catalog and Verify Facets

URL: `https://demo-wvu-knapsack.localhost.direct/catalog?search_field=all_fields&q=`

**Verify each**:
1. **Facets visible on left side** - Yes/No?
2. **People Represented facet** - How many items showing? (5 or 36+?)
3. **"More" link** - Does it appear below facet items?
4. **Search results** - Do results display in main content area?

### Step 4 — Test Other Facets

Click and expand:
- Creator → How many items? (should be 5 max)
- Subject → How many items? (should be 5 max)
- Keyword → How many items? (should be 5 max)

### Step 5 — Verify Background Jobs Still Work

```bash
# Check GoodJob queue for new errors
# Navigate to: https://admin-wvu-knapsack.localhost.direct/jobs/jobs?locale=en
# Look for FileSetAttachedEventJob or ContentDepositEventJob
```
**Expected**: Jobs processing normally, no Wings::ModelRegistry errors

### Step 6 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Test: after_initialize decorator timing
Result: [PASS/FAIL on each criterion]

ROOT CAUSE
[one paragraph if failing]

PROPOSED FIX
[exact code change if applicable]

RISK
[any shared code affected]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] Facets show max 5 items (not 36+)
- [ ] "More" link appears for facets with 5+ items
- [ ] Search results display correctly
- [ ] No Wings::ModelRegistry errors in logs
- [ ] Background jobs continue processing normally
- [ ] Isolation run: 0 failures
- [ ] No regressions in related specs

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

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/wvulibraries_knapsack/tasks/active/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md \
         projects/wvulibraries_knapsack/tasks/completed/2026-08/2026-08-21-HIGH-BUGFIX-TEST-AFTER-INITIALIZE-TIMING.md

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/wvulibraries_knapsack/tasks/active/[FILENAME] projects/wvulibraries_knapsack/tasks/completed/[YYYY-MM]/[FILENAME]
git add projects/wvulibraries_knapsack/tasks/completed/[YYYY-MM]/[FILENAME]

git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: fix/hide-type-facet-add-show-more-facets branch

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
