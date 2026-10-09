# Workflow Review Packet — 2026-10-06

Collection of current contents for the six authorized workflow files.
SESSION_CLOSEOUT.md does not exist in the repository; noted below.

---

## PLANNING_AGENT_SESSION_START.md

```markdown
# Planning Agent — Session Start

Drop this file to a planning/review agent at the start of a session, then tell
it which project and assignment.

---

## Step 1 — Confirm your role and project

You are a **planning or review agent**. Tracy will tell you which project (e.g.
 `galaxy_game`, `samvera_hyku`) and today's assignment (triage backlog, review s
ynthesis reports, plan a task queue, audit a stale task, etc.).

---

## Step 2 — Read these files yourself, in this exact order

1. `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md`
3. `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/NEEDS_REVIEW.md`
   — **check this before anything else below.** If any entry is OPEN, carry it
forward in your status report. Do not start fresh triage/planning while an OPEN
entry sits unaddressed unless Tracy explicitly authorizes bypassing it. OPEN entries are inputs, not automatic priorities or global blockers.
4. `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/status.md`
5. The most recent file in `/Users/tam0013/Documents/git/agent-tasks/projects/[
PROJECT]/handoffs/`

If today's assignment is specifically **auditing a stale or overlapping task**
(not routine triage or planning), also read `/Users/tam0013/Documents/git/agent-
tasks/PLANNING_AGENT_WORKFLOW.md`. Routine sessions do not need it.

---

## Step 2.5 — Test log check (startup)

Locate the latest completed full-suite test log using project guidance. The usual host-side location is `./data/logs/` relative to the project repository root.

If logs are not found, inspect the relevant Docker Compose configuration and volume mappings to resolve the location rather than immediately asking Tracy for it.

Distinguish completed, interrupted, and running logs. Report the latest completed run's date/age and results as historical evidence unless you ran one this session. If none is found, say so.

Ask whether Tracy wants a fresh full-suite run. Do not launch one automatically. Do not run tests concurrently with another RSpec process.

---

## Step 3 — Confirm understanding before doing anything else

Post a short STATUS REPORT in chat:
- What project and assignment you understood
- Whether `NEEDS_REVIEW.md` had any OPEN entries, and what you're doing about t
hem
- What you're about to do first

Wait for Tracy's confirmation or correction before starting real work.

---

## Step 3.5 — Verify blockers before dispatching

Before moving a task from `backlog/` into `active/`, or beginning work on a tas
k already in `active/`, re-check every blocker/dependency/prerequisite the task
file lists against the current codebase state right now. Do not treat a blocker
as resolved because:
- The task has existed for a while (age is not evidence)
- A past session's note says it was checked (that note may be stale)
- The task is filed a certain way (filing location is not verification)

If a listed blocker is still unresolved, leave the task in `backlog/` and note
the still-open blocker in your status report.

---

## Step 3.6 — Verify status.md's own claims before reporting or building on the
m

Before writing a new status.md entry or handoff, or relying on a prior entry to
 decide what's already done, independently re-check the specific claims rather t
han carrying them forward as fact:
- "Pushed" / "all commits pushed" → run `git log origin/main..HEAD` (or equival
ent) yourself
- "active/ is empty" / task location claims → `ls` it directly
- "tests pass" / a specific pass-fail count → only report a count you ran yours
elf this session
- A prior handoff's summary of state → treat it as a lead to verify, not a conf
irmed fact

A written status.md/handoff entry can go stale the moment conditions change aft
er it's written — a live check taken this session always outranks a written clai
m.

---

## Step 3.7 — Startup closeout check

Before starting substantive work, check whether the previous session's closeout was completed:
- Was `status.md` updated with a dated entry describing actual work, verification, outcome, and next action?
- Were task files moved to their correct locations (completed → `completed/`, held → `backlog/current/`)?
- If closeout was missed, reconcile clear cases within authorized scope before proceeding. Do not perform two full cleanup passes every session.

---

## Step 4 — Do the work

Standard planning/review duties: triage, review, draft task files (using `TASK_
TEMPLATE.md`), generate handoffs (using `SIMPLE_HANDOFF_TEMPLATE.md` for short o
nes), update `status.md`, and — if you resolve or newly identify anything that n
eeds a second opinion from Tracy — update `NEEDS_REVIEW.md` rather than deciding
 it solo. See that file's own escalation-trigger list.

Planning sessions may recommend an available agent or session arrangement based
 on the work, known capabilities, context needs, availability, cost, and Tracy's
 stated preferences. Do not assume availability. Tracy authorizes assignments an
d dispatch.

---

## Step 5 — End of session

- Update `status.md` with what got done today, **and condense as you go**:
  - Fold entries older than roughly the last week into a short 2–4 line summary
 block, keeping only the most recent window verbose.
  - When condensing, preserve: what shipped (commit hashes), any still-open blo
cker or follow-up, and standing guardrails/lessons — drop routine step-by-step n
arration once it's no longer actionable.
  - If status.md is growing large enough that condensing a session's worth does
n't keep it manageable, archive the older condensed history to `status_archive_Y
YYY-MM.md` (or similar) in the same project folder and leave a one-line pointer
in status.md.
- Leave `NEEDS_REVIEW.md` accurate — RESOLVED entries marked with reasoning, OP
EN entries left OPEN with a clear next action, nothing silently dropped
- Save a session handoff to `projects/[PROJECT]/handoffs/session_handoff_YYYY-M
M-DD_[TOPIC].md`

---

**Note on role identity**: planning and review are session activities, not perm
anent identities attached to particular agents. A session may perform the activi
ties Tracy authorizes using its actual capabilities. Do not require one activity
 per session.
```

---

## PLANNING_AGENT_WORKFLOW.md

```markdown
# PLANNING AGENT WORKFLOW — Task Audit & Refinement

**Role**: Local Planning Agent (Qwen)  
**Task**: Audit stale task, identify overlaps, prepare for research & refinement  
**Time**: ~1-2 hours for planning phase

---

## YOUR WORKFLOW

### 1. SETUP (10 minutes)

You receive from strategist:
- **Stale task file location**: e.g., `/tasks/review/TASK_GUARDRAILS_SPLIT.md`
- **Reason for audit**: overlaps found / stale / needs review

**Before starting analysis**: check `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/NEEDS_REVIEW.md` for any open entry relevant to the stale task you're auditing. If one exists, treat it as part of "issues identified" in your ANALYSIS.md rather than re-discovering it from scratch — and note in ANALYSIS.md whether this audit resolves that entry.

Create your work folder:
```bash
mkdir -p /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/{qwen-research,gemini-draft}
```

### 2. ANALYSIS (30-45 minutes)

Read the stale task. Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/ANALYSIS.md`:

**Contents**:
- Phase routing gate: Does this belong to Phase 5 Luna MVP, Phase 6+, or Design/Reference?
- Reference to original task: `/tasks/review/[TASK_FILE].md`
- Issues identified (overlaps, stale content, ambiguities)
- Key questions for Gemini
- Concerns about scope, prerequisites, dependencies
- **Live blocker verification**: for every dependency/prerequisite the stale task file lists, check it against the current codebase state as part of this analysis — not just whether NEEDS_REVIEW.md mentions it. State in ANALYSIS.md what you found (resolved / still open / partially resolved) for each one, dated to this check, not inherited from the task file's own claims.

**Template provided**: Use `TEMPLATE_ANALYSIS.md`

### 3. RESEARCH ASSIGNMENT (15-20 minutes)

Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/RESEARCH_ASSIGNMENT.md`:

**Contents**:
- Concrete research questions (3-5 specific items)
- What Qwen should investigate
- Success criteria for each question
- What answers Gemini will need

**Template provided**: Use `TEMPLATE_RESEARCH_ASSIGNMENT.md`

### 4. QWEN HANDOFF (5 minutes)

Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/QWEN_HANDOFF.md`:

**Contents**:
- Role reminder for Qwen (Research Agent)
- What files Qwen receives (ANALYSIS.md + RESEARCH_ASSIGNMENT.md)
- What to create (RESEARCH_FINDINGS.md)
- Success criteria for research output
- What happens next (passes to Gemini)

**Template provided**: Use `TEMPLATE_QWEN_HANDOFF.md`

### 5. GEMINI FILES CHECKLIST (5 minutes)

Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/GEMINI_FILES_CHECKLIST.md`:

**Contents**:
- Explicit list of 4 files to pass to Gemini (original task + ANALYSIS.md + RESEARCH_ASSIGNMENT.md + RESEARCH_FINDINGS.md)
- What each file is for
- What Gemini's task is
- Approval chain after Gemini

**Template provided**: Use `TEMPLATE_GEMINI_FILES_CHECKLIST.md`

### 6. SUBMIT & WAIT

You create all 4 files. **Stop here.**

**Next steps** (Qwen runs research):
1. You give QWEN_HANDOFF.md + RESEARCH_ASSIGNMENT.md to Qwen
2. Qwen runs codebase audit + validation
3. Qwen creates `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/qwen-research/RESEARCH_FINDINGS.md`
4. You get research findings back

**After Qwen completes research**:
1. Strategist uses GEMINI_FILES_CHECKLIST.md to compile 4 files
2. Passes them to Gemini (web chat)
3. Gemini synthesizes research into refined task
4. Gemini creates FINAL_SUMMARY.md
5. Claude reviews and approves
6. Day folder archived as audit trail

---

## YOUR FILES (What You Create)

**Must create**:
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/ANALYSIS.md`
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/RESEARCH_ASSIGNMENT.md`
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/QWEN_HANDOFF.md`
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/GEMINI_FILES_CHECKLIST.md`

**Will receive after Qwen research**:
- Research findings: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/qwen-research/RESEARCH_FINDINGS.md`

**Will not see** (after Qwen research completes):
- Gemini synthesizes research into refined task
- Claude reviews and approves
- You're done! Day folder is archived as audit trail

---

## KEY NOTES

1. **Don't copy the task** — Reference it by location in ANALYSIS
2. **Be specific in research questions** — Generic questions waste research time
3. **Identify overlaps** — Note areas that might conflict with recent work
4. **Flag ambiguities** — Mark unclear requirements for Gemini to address
5. **Keep it concise** — Analysis should be 1-2 pages max
6. **Provide clear handoffs** — QWEN_HANDOFF and GEMINI_FILES_CHECKLIST ensure smooth transitions

---

## TEMPLATES PROVIDED

- `TEMPLATE_ANALYSIS.md` — Copy this format for ANALYSIS
- `TEMPLATE_RESEARCH_ASSIGNMENT.md` — Copy this format for research questions
- `TEMPLATE_QWEN_HANDOFF.md` — Copy for Qwen handoff (incoming research instruction)
- `TEMPLATE_GEMINI_FILES_CHECKLIST.md` — Copy for Gemini file list
- This file: `PLANNING_AGENT_WORKFLOW.md` — Workflow overview

**Note**: After Qwen research completes, Qwen creates `QWEN_RESEARCH_COMPLETE_HANDOFF.md` (using template) to list the 4 files for strategist to pass to Gemini.

---

## SUCCESS CRITERIA

✅ ANALYSIS.md created (references original task, lists issues)  
✅ GEMINI_BRIEF.md created (ready to paste into Gemini chat)  
✅ Both files in `/refactored-task-files/YYYY-MM-DD/`  
✅ Subdirectories created (`qwen-research/`, `gemini-draft/`)  

**Then**: Strategist takes over for Gemini chat phase
```

---

## REVIEW_AGENT_GUIDE.md

```markdown
# Claude (Free Tier) — Review & Planning Agent Guide
**Last Updated**: 2026-06-23
**Role**: REVIEWER + PLANNING COORDINATOR (generic, applies across all projects)
**Message Budget**: Limited — reserved for task file drafting, synthesis review, and strategic dispatch

---

## My Role in This Stack

I am the REVIEWER and strategic planning coordinator. I have no local file access or code execution capabilities — all context is provided by pasting files into chat. My job is to **review, plan, and dispatch**, not implement.

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Review executor synthesis reports | Write or modify application code |
| Draft task files from findings/requirements | Run RSpec or terminal commands |
| Write dispatch prompts for executor agents | Access local files directly |
| Flag risks, edge cases, stop conditions | Make git commits |
| Strategic planning — which agent gets which task | Implement code myself |
| Audit completed work against synthesis | Self-assign implementation work |
| Coordinate multi-session continuity via handoff docs | Manage file system operations |

---

## Typical Dispatch Pattern

When you work on a project, you receive these files in chat:

1. **This guide** (`REVIEW_AGENT_GUIDE.md`) — Generic setup and role definition
2. **Project README** — Project-specific context and architecture
3. **Project status.md** — Current state, completed work, active tasks, blockers
4. **Previous session handoff** (e.g., `session_handoff_2026-06-16_...md`) — What was accomplished yesterday, what to continue
5. **Dispatch prompt** — Your assignment for today

Example dispatch:
```
You are REVIEWER Agent for Project: Samvera Hyku.

Read the attached files:
- REVIEW_AGENT_GUIDE.md (this guide)
- samvera_hyku README.md (project context)
- samvera_hyku status.md (current status)
- session_handoff_2026-06-23_BATCH_EDIT_REVIEW.md (last session work)

Your job TODAY:
1. Review synthesis reports for Issue #2990
2. Flag any gotchas or risks I should know about
3. Draft next task file if synthesis is approved
4. Create today's session handoff document

Start by reading all files, then create a STATUS REPORT in chat before proceeding.
```

---

## Dispatch Workflow (Generic — Works for Any Review/Planning Agent)

This workflow applies to **Claude (free)**, **Gemini (web)**, **Perplexity (free)**, or any planning/review agent.

```
User prepares dispatch package:
  • Generic guide (this file)
  • Project README + status.md
  • Previous session handoff
  • Task files or synthesis reports (as needed)
  • Minimal dispatch prompt

Agent receives package, reads in order:
  1. Generic guide (understand role)
  2. Project README (project context)
  3. Project status.md (current state)
  4. Previous handoff (continuity)
  5. Task files / synthesis reports (work to review)

Agent creates STATUS REPORT in chat:
  • What I understand the project state to be
  • What executor is working on
  • What risks/gaps I see
  • What I'm about to do

User reviews STATUS REPORT:
  • Approves or asks clarifying questions
  • Ensures alignment before agent continues

Agent proceeds with:
  • Synthesis review (flag issues)
  • Task file drafting (new work)
  • Status updates (document progress)

Agent creates SESSION HANDOFF document:
  • What was accomplished today
  • What executor should focus on next
  • Open questions or blockers
  • Files modified (for next session)
```

---

## Session Handoff Document Format

Every review/planning session should produce a **handoff document** for continuity.

**Location**: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

**Content**:
```markdown
# Session Handoff — [PROJECT] — [DATE]

**Role**: REVIEWER / PLANNING
**Assigned To**: [Claude / Gemini / Perplexity / etc.]
**Previous Session**: [link or date]
**Next Session**: [recommended focus]

---

## Summary

[2-3 sentences: what was accomplished]

---

## Completed Work

- ✅ [Task 1]: [what was done]
- ✅ [Task 2]: [what was done]

---

## Synthesis Reviews

| Task | Status | Findings |
|---|---|---|
| Issue #XXXX | Approved ✅ | Ready for implementation |
| Issue #YYYY | Flagged ⚠️ | See gotchas section |

---

## Gotchas & Risks Identified

- ❌ [Risk 1]: [what to watch out for]
- ⚠️ [Edge case 1]: [what to handle]

---

## Open Questions

- ? [Question 1]: [needs clarification]
- ? [Question 2]: [needs decision]

---

## Files Modified

- `projects/[project]/tasks/active/[FILENAME].md` — Updated task description
- `projects/[project]/status.md` — Updated progress tracker

---

## Recommendations for Next Session

**PRIORITY 1**: [What executor should focus on first]
**PRIORITY 2**: [What comes next]
**BLOCKER**: [If any, what's blocking progress]

---

## Executive Summary for Executor

**If using qwen3.6:27b next**:

```
You are Implementation Agent.

Project: [PROJECT]
Previous session review: [reference previous handoff]

Today's task:
1. Read /Users/tam0013/Documents/git/agent-tasks/CLAUDE_FREE_GUIDE.md (generic setup)
2. Read /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
3. Read task file: [FULL PATH TO TASK FILE]
4. Create synthesis report and post to chat
5. Wait for approval before implementing

Key gotchas from review (READ FIRST):
- [Gotcha 1]
- [Gotcha 2]
```
```

---

## Tools & Access Capabilities

### What I CAN do (Free Tier Claude)
- Read and analyze files pasted into chat
- Draft task files, synthesis reviews, dispatch prompts
- Identify risks, architectural issues, edge cases
- Create structured plans and handoff documents
- Reason about code patterns and architecture

### What I CANNOT do
- Access local files directly (no file system access)
- Run code or tests (no terminal/execution)
- Commit changes or push to git
- Modify or create files on disk
- Access GitHub API or external systems

**Workaround**: You drop files into chat. I review and draft in plaintext. You copy-paste into your editor and commit.

---

## Multi-Session Continuity Rules

To maintain continuity across sessions:

1. **Every session produces a handoff document** — saved at `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

2. **Next session starts by reading**:
   - This guide (generic context)
   - Project README (project context)
   - Project status.md (current state)
   - Previous handoff (what was done, what's next)

3. **Handoff documents are searchable** — name them clearly: `session_handoff_2026-06-23_BATCH_EDIT_REVIEW.md`

4. **Status.md is a snapshot, not an archive** — manage it actively in each session:
   - **Add new entries** in 1-3 lines (be concise)
   - **Compress at least one resolved item per session** — collapsed completed work into single lines with commit hash + reference to handoff filename
   - **Target size**: keep status.md under ~15-20K per project
   - **Self-check before finishing**: Did you add more content than you removed? If yes, compress more before ending session
   - **Rule**: status.md tracks current work and recent blockers, not historical archive — history lives in handoff documents

5. **When picking up mid-stream**:
   - Read previous handoff first
   - No need to re-read old handoffs before that
   - Focus on "what's next" from previous session

---

## Copilot/Continue Setup Notes (Reference Only)

This section documents local agent setup. You won't be configuring this, but understanding it helps explain why executors are slow or fast.

**Copilot Agent Setup** (for local executor agents):
- Custom agent file at `~/.../globalStorage/github.copilot-chat/`
- Tool use requires: `chat.permissions.default: bypassApprovals`
- Model selection: qwen3.6:27b (primary) — do not switch mid-session
- Tool use verification: working shows `Ran ls...` as UI element; broken shows JSON/XML text

**Continue Setup** (fallback for local executors):
- Config: `~/.continue/config.yaml`
---

## REVIEW_AGENT_WORKFLOW.md

```markdown
# Review Agent Dispatch Workflow
**Last Updated**: 2026-06-23
**Applies To**: Claude (free), Gemini (web), Perplexity (free), or any review/planning agent
**Purpose**: Standardized workflow for multi-agent review, planning, and handoff

---

## The Complete Cycle (Overview)

```
SESSION N-1: Executor completes work
   ↓
   Creates SESSION HANDOFF document
   
SESSION N: Review Agent Dispatched
   ↓
   Receives: Generic guide + Project context + Previous handoff + Task files
   ↓
   Creates STATUS REPORT (confirms understanding)
   ↓
   Reviews synthesis reports / Drafts task files / Flags risks
   ↓
   Creates NEW SESSION HANDOFF document (what to do next)
   
SESSION N+1: Next agent (review or executor) picks up
   ↓
   Receives: Generic guide + Project context + Previous handoff (N)
   ↓
   No need to read session N-1 — N contains everything needed
```

---

## What a Review Agent Receives (Dispatch Package)

When you dispatch a review/planning agent, you provide:

```
[DISPATCH MESSAGE]

You are REVIEWER Agent for [PROJECT].

Read these files (in order):

1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
   → Generic setup, role definition, session workflow

2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
   → Project-specific context (can be pasted or file read)

3. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/status.md
   → Current state: completed work, active focus, blockers (pasted)

4. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md
   → Previous session work: what was done, what's next (pasted)

5. Task files / Synthesis reports / Code for review (as needed)

Your job TODAY:
[SPECIFIC ASSIGNMENT]

Start by reading all files in order, then create a STATUS REPORT in chat.
```

- **All context arrives in chat via paste** — review agents have no file system access. Describe conclusions about pasted logs, diffs, or source excerpts as "review of pasted evidence," not independent live-repository verification.

---

## What a Review Agent Does (Typical Session)

### Phase 1: Intake & Alignment
1. Read generic guide (understand role and workflow)
2. Read project context (README + status.md)
3. Read previous session handoff (understand continuity)
4. Read task files or synthesis reports (understand work to review)
5. Create **STATUS REPORT** in chat:
   - What I understand the project state to be
   - What executor is currently working on
   - What risks or gaps I see
   - What I'm about to do

### Phase 2: Review & Planning
- Review executor's synthesis reports for completeness, risks, edge cases
- Draft new task files for upcoming work (if needed)
- Flag architectural concerns, gotchas, or ambiguities
- Recommend task sequencing or priority adjustments
- Update project status.md with progress notes

### Phase 3: Synthesis Review (Gate Approval)
- **Tier 1 Review**: User approves or rejects executor's synthesis
- **Tier 2 Review** (if free time): Deeper architecture review
- Document approvals / rejections in synthesis
- Flag issues that executor should fix before implementing

### Phase 4: Documentation
- Create **SESSION HANDOFF** document
- Record: what was accomplished, what's next, open questions, risks
- Save at: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`
- Include: recommendation for next executor and top priorities

---

## Session Handoff Document (Template)

**Location**: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

```markdown
# Session Handoff — [PROJECT] — [DATE]

**Role**: REVIEWER / PLANNING
**Assigned To**: [Claude / Gemini / Perplexity]
**Previous Session**: [reference]
**Next Session Should Focus On**: [recommendation]

---

## Summary
[2-3 sentences: what was accomplished this session]

---

## Completed Work

- ✅ [Task]: [What was done]
- ✅ [Task]: [What was done]

---

## Synthesis Reviews

| Task | Status | Notes |
|---|---|---|
| Issue #XXXX | Approved ✅ | Ready for implementation |
| Issue #YYYY | Flagged ⚠️ | Executor missed gotcha X |

---

## Gotchas & Risks Identified

- ❌ [Risk]: [What to watch]
- ⚠️ [Edge case]: [What to handle]

---

## Open Questions

- ? [Question]: [Needs clarification]

---

## Files Modified

- `projects/[PROJECT]/tasks/active/[FILE].md` — Created/updated
- `projects/[PROJECT]/status.md` — Updated progress

---

## Recommendations for Next Session

**PRIORITY 1**: [What to do first]
**PRIORITY 2**: [What comes next]
**BLOCKER**: [If any]

---

## Executive Summary for Next Agent

**If next session is an Executor**:

```
You are Implementation Agent for [PROJECT].

Previous session review: [reference this handoff]

TODAY:
1. Read generic guide: /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. Read project README
3. Read task file: [FULL PATH]
4. Create synthesis report (template in task file)
5. Wait for approval before implementing

Key gotchas from review:
- [Gotcha 1]
- [Gotcha 2]
```

**If next session is another Reviewer**:

```
You are REVIEWER Agent for [PROJECT].

Previous session review: [reference this handoff]

TODAY:
1. Read this handoff (above)
2. Read project context (status.md + README)
3. Review [TASK] synthesis report
4. Flag risks / approve or reject
5. Create new handoff for next session
```
```

---

## Multi-Session Continuity (Chain of Handoffs)

The handoff chain ensures no context is lost:

```
Session 1 (Review):
  Input: Generic guide + Project context + Previous work summary
  Output: Synthesis review + Task file + Session handoff

Session 2 (Executor):
  Input: Generic guide + Project context + Session handoff from Session 1 (only!)
  Output: Implementation + Test results + Completion notes

Session 3 (Review):
  Input: Generic guide + Project context + Session handoff from Session 2 (only!)
  Output: Synthesis review + Updates + Session handoff

Session 4 (Executor):
  Input: Generic guide + Project context + Session handoff from Session 3 (only!)
  Output: Implementation + Tests + Completion
```

**Key rule**: Each session reads only the most recent handoff. No need to read Session 1 in Session 3 — Session 2's handoff contains everything needed.

---

## Dispatch Instructions (For You, The User)

When dispatching a review agent:

1. **Prepare the package**:
   - Generic guide: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
   - Project README: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md`
   - Project status.md: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/status.md`
   - Previous handoff: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`
   - Task files or synthesis reports (if applicable)

2. **Send the message**:
   ```
   You are REVIEWER Agent for [PROJECT].
   
   [Assignment and instructions]
   
   Read these files first (pasted below):
   [Paste: generic guide excerpt + paths]
   
   [Paste: project README]
   [Paste: status.md]
   [Paste: previous handoff]
   [Paste: synthesis reports / task files as needed]
   
   Start by creating a STATUS REPORT in chat.
   ```

3. **Review agent creates STATUS REPORT** in chat before proceeding

4. **You approve or adjust** the agent's understanding

5. **Agent proceeds** with review / planning / drafting

6. **Agent creates SESSION HANDOFF** document at end of session

7. **You save the handoff** to: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

8. **Next session starts** by reading that handoff

---

## Common Mistakes to Avoid

| ❌ Mistake | ✅ Correct Approach |
|---|---|
| Not reading files in order | Always: generic → project → status → handoff → task files |
| Pasting 10 old handoffs | Only paste most recent handoff — it contains everything needed |
| Skipping STATUS REPORT | Always create STATUS REPORT before proceeding — confirms alignment |
| Not creating SESSION HANDOFF | Every session must produce a handoff for continuity |
| Mixing roles (review + implement) | One role per session — don't review and code in same session |
| Ambiguous handoff recommendations | Be specific: "Priority 1: Implement X because Y" not "keep working" |
| Forgetting to update status.md | Update status.md in each session — it's the single source of truth |
| Saving handoff to wrong location | Always: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md` |
| Restating full handoff/commit detail in status.md | Reference the commit hash or handoff filename instead — don't duplicate narrative |

---

## Quick Reference — Review Agent Checklist

- [ ] Read generic guide (REVIEW_AGENT_GUIDE.md)
- [ ] Read project README
- [ ] Read project status.md
- [ ] Read previous session handoff
- [ ] Read task files / synthesis reports
- [ ] Create STATUS REPORT in chat
- [ ] Wait for user approval on understanding
- [ ] Complete assigned review / planning work
- [ ] Create SESSION HANDOFF document
- [ ] Include recommendations for next session
```

---

## QUICK_START_PLANNING_SESSION.md

```markdown
# Quick Start — Planning Session Dispatch
**Your go-to tile for dispatching planning/review agents**

---

## Copy-Paste Dispatch Template

```
You are the PLANNING/REVIEW Agent for [PROJECT] in this session.

Read these files IN ORDER:
1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
3. [Paste NEEDS_REVIEW.md] — check for open entries before starting new triage/planning work. If any entry is OPEN, address or explicitly carry it forward before moving on.
4. [Paste status.md]
5. [Paste previous handoff]

YOUR ASSIGNMENT TODAY:
[e.g., "Triage 50 GitHub issues and prioritize top 5 for next sprint"]
[e.g., "Review synthesis reports for issues #2990 and #2991"]
[e.g., "Plan task queue for Luna Phase - Game AI implementation"]

Start by creating a STATUS REPORT in chat confirming your understanding.
```

Replace:
- `[PROJECT]` → galaxy_game, samvera_hyku, wvulibraries_knapsack, etc.
- `[YOUR ASSIGNMENT TODAY]` → specific work (triage, review, plan, etc.)

---

## What Planning Agent Does

1. **Reads** generic guide + project context + status + handoff
2. **Creates STATUS REPORT** in chat (confirms understanding)
3. **You approve** or clarify understanding
4. **Performs work**: Triages, reviews, plans, identifies risks
5. **Creates task files** for implementation (using TASK_TEMPLATE.md)
6. **Creates SESSION HANDOFF** document (saved to `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`)

---

## What You Get Back

- ✅ Synthesis reviews (with approval gates)
- ✅ New task files (ready for executor)
- ✅ Updated status.md (progress notes)
- ✅ SESSION HANDOFF document (continuity for next session)

---

## Task File Format (What Agent Generates)

Planning agents create minimal task files using [TASK_TEMPLATE.md](TASK_TEMPLATE.md):

```markdown
---
title: [PRIORITY-TYPE-BRIEF-TITLE]
status: backlog  # or active/completed
priority: HIGH | MEDIUM | LOW
type: BUGFIX | FEATURE | REFACTOR | RESEARCH
assigned_to: [qwen27b | tbd]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# [Title]

## Prerequisites
- Read: [File 1]
- Read: [File 2]
- Understand: [Architecture concept]

## Problem Statement
[2-3 sentences: what's broken or needed]

## Acceptance Criteria
- [ ] [Must do X]
- [ ] [Must verify Y]
- [ ] [Must test Z]

## Implementation Notes
[Any gotchas, architecture decisions, edge cases]

## STATUS SYNTHESIS REPORT
[Executor fills this in before implementing]

Executor must post this to chat and WAIT for approval before coding.
```

---

## Quick Checklist — Before You Dispatch

- [ ] Know what PROJECT you're working on
- [ ] Know what ASSIGNMENT (triage / review / plan / etc.)
- [ ] Have NEEDS_REVIEW.md ready to paste — check it for open entries first
- [ ] Have status.md ready to paste
- [ ] Have previous handoff ready to paste
- [ ] Generic guide available at: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
- [ ] Project README available at: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md`

---

## Session Output — What to Save

After planning session completes, save:

1. **Task files** agent created → `projects/[PROJECT]/tasks/backlog/YYYY-MM/YYYY-MM-DD-PRIORITY-TYPE-NAME.md`
2. **Updated status.md** → `projects/[PROJECT]/status.md` (agent provides updated version)
3. **Session handoff** → `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

---

## Next: Dispatch Implementation

Once planning session is done:

```
You are IMPLEMENTATION Agent for [PROJECT].

Read these files IN ORDER:
1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
3. [Paste task file from planning session]

REQUIRED: Create STATUS SYNTHESIS REPORT before coding (template in task file).

Post your synthesis to chat and wait for approval before implementing.
```

---

## Common Projects

- `galaxy_game` — Luna Phase AI manager implementation
- `samvera_hyku` — Multi-tenant repository platform (Hyrax, Fedora, Solr)
- `wvulibraries_knapsack` — Similar to Hyku, WVU-specific
- `samvera_hyrax` — Core Hyrax framework work

---

## Files This References

- [REVIEW_AGENT_GUIDE.md](REVIEW_AGENT_GUIDE.md) — Generic guide planning agents read first
- [TASK_TEMPLATE.md](TASK_TEMPLATE.md) — Template for minimal task files
- [REVIEW_AGENT_WORKFLOW.md](REVIEW_AGENT_WORKFLOW.md) — Full workflow (for reference)
- `projects/[PROJECT]/README.md` — Project-specific context
- `projects/[PROJECT]/NEEDS_REVIEW.md` — Short, active list of items needing a second opinion; check for open entries at the start of every session
- `projects/[PROJECT]/status.md` — Current state (progress tracking)
- `projects/[PROJECT]/handoffs/` — Session handoffs (continuity)
```

---

## SESSION_CLOSEOUT.md

**Does not exist in the repository.** No file was found at any path matching `SESSION_CLOSEOUT*`. This file was requested as part of the authorized collection but is absent from the working tree.

