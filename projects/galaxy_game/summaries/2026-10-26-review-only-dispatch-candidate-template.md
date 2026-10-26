# Task File Template

# ⚠️ VALIDATION REQUIREMENT:
# Every task file MUST include the "Agent Dispatch Interface" section immediately after YAML frontmatter.
# If this section is missing or incomplete, the task is NOT READY FOR DISPATCH.
# This is the critical contract between human and agent — it cannot be optional or skipped.

# ⚠️ TASK CREATION WORKFLOW:
# NEW TASKS START IN tasks/drafts/ WITH status: draft
# Do NOT place tasks directly in tasks/backlog/ or tasks/active/
# Path: tasks/drafts/ → (git commit) → (review agent assessment) → (human approval) → tasks/backlog/
# See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for the complete procedure.

# Copy this file, rename it to describe the task, fill in all sections.
# Delete these instruction comments before saving.
# For NEW TASKS: Place in tasks/drafts/ with status: draft
# For APPROVED TASKS: Place in tasks/backlog/{YYYY-MM}/ or tasks/active/ as directed by human after review approval.
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
#   Local Qwen (primary, via GitHub Copilot custom agent config) — explicit file paths,
#     before/after code, exact docker commands. Has terminal/tool-use access — can run
#     commands, grep the codebase, and check state beyond just reading this file.
#   Cloud fallback (paid Copilot agents) — same depth as above, only after two local
#     failures in fresh local sessions
#   Claude Sonnet (premium/web) — architecture and strategy only, never implementation.
#     No filesystem/terminal access — works only from what's pasted into chat.
#
# Rule: when in doubt, add more detail. An over-specified task wastes nothing.
# An under-specified task burns premium requests on clarification.
#
# AGENT SELECTION RULE: Always assign to local Qwen (Copilot) first.
# Only escalate to cloud after two genuine failures in fresh local sessions.
#
# DISPATCH INTERFACE STRATEGY (CRITICAL):
#   The "Agent Dispatch Interface" section (immediately after YAML) is NOT optional scaffolding.
#   It is the CONTRACT between human and agent.
#   - Human fills it out completely BEFORE task is ready for dispatch
#   - Agent receives this exact text as their startup instructions
#   - If this section is missing or incomplete, the task is NOT READY
#   - Human must complete readiness checklist (above) before sending this to any agent
#   
#   The dispatch interface is minimal (2-4 lines in the code block) and must contain:
#     - Agent role and project name
#     - Path to task file location
#     - Step 0 instructions (move task to active/, update status)
#     - Reference to synthesis report requirement
#   (This list describes Variant A, Implementation. Review-only tasks use Variant B;
#   see "Review-Only Dispatch" below.)
#   DO NOT put implementation details in the dispatch interface.
#   All that belongs in the task file sections below.
#   The interface is ONLY the startup contract.

---
status: draft | backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER | MANUFACTURING | TERRA_SIM | CONTROLLERS | UNITS | OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION | SPEC_HEALTH | OTHER
local_worker_safe: true | false
dispatch_mode: implementation | review-only   # newly authored tasks declare this; existing tasks without it are implementation
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## � DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**Two dispatch variants exist.** The task's `dispatch_mode` field decides which one applies; the human selects it before dispatch.
- **Variant A — Implementation:** `dispatch_mode: implementation`, or the field is absent (existing task files). The text in the block below, unchanged.
- **Variant B — Review-Only:** only when `dispatch_mode: review-only`. See "Review-Only Dispatch" below.

Newly authored task files declare `dispatch_mode` explicitly and keep only the selected variant's block. If the declared mode and the dispatch block in the task file do not match, the task is not ready for dispatch.

**Allowed substitutions (these only):** before dispatch, (1) replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root); (2) fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual (Variant B: `[project_name]`, `[project]` and `[TASK_PATH]`); (3) use the variant that matches `dispatch_mode`. Change nothing else: no other role label, and no added or removed lines. Choosing a variant selects text only; it authorizes nothing beyond that text.

```
You are **Implementation Agent**.

Project: [project_name]
Task: <agent-tasks>/projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md \
         projects/[project]/tasks/active/[FILENAME].md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/[project]/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/[project]/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text of the selected variant.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

### Review-Only Dispatch (Variant B)

Use only when the task YAML has `dispatch_mode: review-only`: the deliverable is a review, audit, or documentation-recovery report. Variant B text (copy exactly; substitutions limited as above):

```
You are **Review-Task Agent**. Project: [project_name]
Task: <agent-tasks>/[TASK_PATH]
REVIEW-ONLY. Stay read-only until you have read <agent-tasks>/rules/GUARDRAILS.md, the task file, and its "Review-Only Dispatch Contract" section, which binds you. Do not edit the task file, commit, push, or open a pull request. If that section is missing, the task's `dispatch_mode` is not `review-only`, or anything is inaccessible, stop and report.
Then follow the contract's order: save the synthesis as an MD file in <agent-tasks>/projects/[project]/summaries/, wait for approval, stop at the REVIEW GATE. Closeout: SESSION_CLOSEOUT.md only.
```

#### Review-Only Dispatch Contract (mandatory; copy verbatim into every task file whose `dispatch_mode` is `review-only`; delete from all other task files)

**Declaration.** `dispatch_mode` is a declaration for the human and the agent to read. Nothing in this repository checks it automatically, and it does not detect or protect existing tasks. An existing review-only task is aligned deliberately (declare the mode and replace its dispatch block) before it is dispatched.

**Two roles, never the same session.**
- **Review-Task Agent** *produces* the task's review/recovery deliverable.
- **Independent reviewer** *assesses* that deliverable and issues GO / REVISE / STOP at the REVIEW GATE. It is another session than the one that produced the deliverable (`REVIEW_AGENT_WORKFLOW.md`: "self-review requires another reviewer"). It is not dispatched with this block.
- "Review Agent" and "Reviewer" are not valid Variant B role labels; they name the assessing role.

**Eligibility.** The role label establishes no eligibility and overrides no existing eligibility rule. Eligibility of each role is determined at dispatch under MAG-3 and `REVIEW_AGENT_GUIDE.md`, and recorded in Agent Assignment. Whether that guide's local-model restriction covers the Review-Task Agent is an open policy question; until the human resolves it, the human decides for each dispatch.

**Order of operations.**
1. Read `rules/GUARDRAILS.md`. Read-only.
2. Read the task file in full and the documents its Prerequisites list as read-first. Read-only. If `resume: true`, read the Progress / Resume block now; nothing is edited.
3. Verify, read-only: the task file is at the dispatched path and no other copy of the same task identity exists (MAG-1); its YAML `status` agrees with its folder per the lifecycle convention (Rule 12); it declares `dispatch_mode: review-only` and embeds the Variant B block and this contract; its own text names the review deliverable and the work in scope. On any failure, or if required material is inaccessible, stop and report in chat. Write no artifact.
4. Save the synthesis report as an MD file. This is the first write. The location and filename pattern come from the dispatch text and the report format from the task file's Status Synthesis Report section, both already read in steps 1–2.
5. Wait for approval of the synthesis, as that section requires.
6. Substantive task work (the task file's review steps and its named deliverable) starts only after step 5. Steps 1–3 are not substantive work.
7. Stop at the task's REVIEW GATE: report the deliverable's location in chat and request independent reviewer assessment. Complete closeout as `SESSION_CLOSEOUT.md` requires.

**Scope of this dispatch.**
- Authorized writes: the synthesis report; the deliverable the task file names; the handoff file that `SESSION_CLOSEOUT.md` requires of a review session; and, where its §1b item 6 requires one, a `status.md` entry (not staged or committed). Nothing else.
- The task file is read-only. `SESSION_CLOSEOUT.md` §2 and §2a do not move or modify it: no move, rename, `status` or `resume` change, or Progress / Completion / Handoff edits. The resume point and outcome go in the handoff file and the `status.md` entry.
- Not authorized: executing any other task, approving the task, issuing a disposition on your own deliverable, creating or dispatching follow-on tasks, or any commit, push, or pull request.
- The task file need not be in `active/` (MAG-1 allows this where the dispatch explicitly authorizes review-only work). The agent does not change its location or status.
- A task file may narrow this scope or supply its own review steps. It may not widen it, reinstate Step 0 activation, lifecycle moves, or implementation for this dispatch, or authorize a publication step in advance (MAG-5). Work that needs those is not review-only: use Variant A, or obtain a separate explicit human instruction.

**Publication prerequisites.** Each of the following, for this task, requires both prerequisites below: commit, push, pull request, marking complete, and moving the task to `completed/`.
1. An independent reviewer GO on the version of the deliverable being published. A GO covers only the version assessed (identified by path and commit or content hash in the review artifact); later changes need reassessment.
2. Explicit human authorization, in the session that performs the operation (Rule 26, MAG-2).

A separate publication instruction satisfies prerequisite 2 only. It does not imply a GO and does not waive the gate. Exceptions to a required gate remain governed by the existing MAG-2 requirements for material task exceptions; this contract adds no waiver. Reviewer GO disposes of the deliverable only: it does not extend this dispatch or authorize any operation.

**Template body in a review-only task file.** Only these adaptations apply; everything else stays as written:

| Template body | In a review-only task file |
|---|---|
| Agent Assignment, and the AGENT SELECTION RULE comment | Fill "Assigned To / Why" from the MAG-3 eligibility screen against the task's actual requirements. The local-Qwen-first and cloud-fallback sentences are a fixed routing ladder, which MAG-3 prohibits, and are not applied. |
| Prerequisites item 1 (README EXECUTOR Role), and the note "Synthesis report goes in chat BEFORE starting work" | Omit item 1: README's EXECUTOR Task Completion Workflow (task moves, commits) does not apply. The note reads: the synthesis report is saved as a file before substantive work. |
| Status Synthesis Report: "create and post … in chat", "POST THIS TO CHAT BEFORE PROCEEDING", and the two "✅ Step 0" lines in its template | Save the synthesis report as a file (dispatch text). Delete the two Step 0 lines. Chat carries only the file location and a request for approval. "Do not start actual work until synthesis is approved" stays. |
| REVIEW GATE: "implementation and verification steps", "paste your evidence in chat", and steps 7–8 (wait for authorization, then proceed to Commit Instructions) | Read "review steps". The deliverable is saved where the task names, and chat states its location and "All review work complete. Requesting independent reviewer assessment." At GO (steps 7–8): stop; the dispatch is complete. Steps 1–6, the two-condition rule, and the CAN/CANNOT table stay. |
| Implementation Steps banner ("Complete Step 0 first"), Step 0, Step 3 (Docker/RSpec), Step 4 (root-cause/proposed-fix report) | Omit. The task file lists its own review steps (may be titled "Review Steps") and checks the deliverable against its Acceptance Criteria. |
| Commit Instructions | Not part of this dispatch. Kept as the reference procedure and pre-commit check for a separately authorized publication. |
| Progress / Resume, Completion Report, Handoff Summary | Not edited by the Review-Task Agent (see Scope above). |

---

# TASK: [Short descriptive title]
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: CRITICAL | HIGH | MEDIUM | LOW
**Type**: bug-fix | feature | refactor | architecture | data | documentation
**Created**: YYYY-MM-DD
**Last Updated**: YYYY-MM-DD

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*
*Local models run via Copilot have terminal/tool-use access — they can grep the codebase,
check status.md, and run read-only research commands to verify state before triaging.
Continue is installed but is not part of the active workflow — a Continue session is
read-only (task files only, no commands, no DB access) and should not be assumed to have
the same capability.*

- **Template Conformance**: PASS | FAIL — [note missing sections]
- **Docker Wrapper Check**: PASS | FAIL | N/A — [verify RSpec strings use correct docker exec format without cd /home/galaxy_game]
- **MVP Alignment**: VALID | STALE | OBSOLETE — [does this task still apply to current codebase]
- **MVP Impact Note**: [one line on how this connects to AI Manager Luna settlement or spec health]
- **Action Line**: READY FOR LOCAL DISPATCH | NEEDS MANUAL REVIEW | OBSOLETE — ARCHIVE

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: [Qwen local via Copilot (primary) | Cloud fallback agent]
**Why This Agent**: [one line — if cloud agent, state why local failed]
**Local attempts before cloud**: [N/A | 1 | 2 — cloud only dispatched after 2 local failures]
**Supervision Level**: [watched carefully | standard | autonomous OK]

**Supervision Legend**:
- Watched carefully = all agents on first dispatch of a task
- Standard = local Qwen (Copilot) on well-specified repeat task types
- Autonomous OK = not currently used — all tasks require human approval before commit

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
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

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
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
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
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

## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

**You MAY NOT commit, push, or mark any task step as "complete" until BOTH of the following conditions are met:**

1. **Reviewer has issued a GO disposition** — a reviewer has assessed your work and disposed it as one of: `GO`, `REVISE`, or `STOP`. See REVIEW_AGENT_GUIDE.md for reviewer authority definitions.
2. **Human has authorized commit/push/completion** — even after reviewer GO, you may not proceed to commit or push without explicit human authorization.

### What This Means In Practice

| You CAN do | You CANNOT do |
|---|---|
| Implement changes per task instructions | Commit/push/mark-complete without both conditions above |
| Run tests and verify your changes locally | Assume reviewer GO is sufficient to proceed to commit |
| Create synthesis and evidence artifacts | Proceed to the next task step if review disposition is REVISE or STOP |
| Report results and request review | Override a reviewer's REVISE or STOP disposition |

### Review-Gate Workflow

1. **Complete all implementation and verification steps** listed below this section
2. **Create evidence artifacts** (test output, synthesis reports)
3. **STOP** — paste your evidence in chat and state: *"All implementation and verification complete. Requesting reviewer assessment."*
4. **Wait for reviewer disposition** (GO / REVISE / STOP)
5. If **REVISE**: address the listed issues, re-test, return to step 3
6. If **STOP**: do not proceed; await human guidance
7. If **GO**: wait for explicit human authorization to commit/push/completion
8. Once **both** conditions are satisfied: proceed to Commit Instructions below

> **This checkpoint is NOT optional.** It applies to all implementation tasks regardless of size or perceived risk. The reviewer does not commit directly; only the human authorizes commits.

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
git mv projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md \
       projects/[project]/tasks/active/[FILENAME].md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find <agent-tasks>/projects/[project]/tasks \
     -name "[FILENAME].md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

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

> ⚠️ **PRE-COMMIT CHECK**: Before running ANY of the commands below, confirm:
> - [ ] Reviewer has issued GO disposition (not just pending)
> - [ ] Human has explicitly authorized commit/push/completion
> - [ ] Both conditions documented in your completion report
>
> If either condition is unmet, DO NOT proceed. Wait for the missing authorization.

Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]
git add projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

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

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

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
