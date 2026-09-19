# Phase 1 Review Extract — Governance Proposal

**Status**: Detailed review gate for exact-text approval  
**Date**: 2026-09-17  
**Against**: TASK_FILE_AUTHORITY_AND_MULTI_AGENT_ROUTING_PROPOSAL.md  

---

## Item 1: Full Exact Text of Rules 20–25

### Rule 20 — Task File as Authoritative Execution Contract

**The selected task file, including its embedded Agent Dispatch Interface, is the authoritative execution contract for an implementation agent.**

#### What This Means

1. **Dispatch prompts must be minimal.** A dispatch prompt contains ONLY:
   - Exact task-file path (e.g., `/agent-tasks/projects/galaxy_game/tasks/active/2026-09-17-TASK-XYZ.md`)
   - Instruction to read the task file and embedded handoff in full
   - Statement that the task file is authoritative
   - Optional: one concise portfolio modifier (see below)
   - Stop/report instruction for ambiguity, scope conflict, or active-work conflict

2. **Dispatch prompts MUST NOT:**
   - Duplicate implementation steps, validation instructions, or lifecycle guidance from the task file
   - Silently override task-file instructions
   - Add architectural context that belongs in Prerequisites section
   - Add implementation hints that belong in Implementation Steps

3. **Portfolio modifiers are exceptions.** A modifier may state ONLY current coordination facts not reasonably stored in a static task file:
   - Another agent or test environment is active
   - A related architecture stream is human-gated
   - A workstream is paused/deferred due to external blocker
   - A specific integration conflict must be avoided
   - Current agent portfolio state has changed

4. **Preflight verification.** Before acting, the assigned agent verifies:
   - Task location and YAML/status match the assignment
   - Relevant referenced files and paths exist
   - No active task/agent conflicts with the planned work
   - Current repository state does not already satisfy or invalidate the task
   - Any stated prerequisite is still true

5. **Exception process.** If extra dispatch context is needed beyond a portfolio modifier:
   - Document WHY in the dispatch prompt (one sentence)
   - Reference the specific gap in the task file that necessitates it
   - The exception is session-scoped; do not bake it into future task files

#### Historical Tasks

Tasks created before this rule does not apply to them retroactively. They may be dispatched as-is. When a historical task is selected for new work, update it to template compliance during the move-to-active step (not before).

---

### Rule 21 — Multi-Agent Synthesis Pipeline

**Work flows through multiple agents for reasoning, review, and refinement. This is not a single-dispatch system.**

#### Pipeline Flow

1. **Orchestrating Agent** reads task file
2. **Orchestrating Agent** evaluates capability needed + availability + constraints
3. **Orchestrating Agent** routes to **Executor Agent** (capable agent, cost-optimal)
4. **Executor Agent** produces output (research, proposal, code, findings, etc.)
5. **Orchestrating Agent** packages as **Proposal Packet** and routes to **Review Agent**
6. **Review Agent** provides feedback:
   - Does this meet requirements?
   - Any risks or considerations?
   - Request adjustments?
   - Ready for human approval?
7. **Orchestrating Agent** evaluates feedback:
   - Minor adjustments? → Re-route to Executor with specific changes
   - Major rework? → May escalate to human or change strategy
   - Ready? → Send to human for final approval
8. **Human** reviews and approves OR sends back for more work

#### Proposal Packet Format

When Orchestrating Agent sends work to Review Agent:
```yaml
To: Review Agent
From: Orchestrating Agent
Task Reference: [task file path]
Context:
  - Project: [project name]
  - Work track: [research/implementation/verification/synthesis]
  - Requirements: [task file requirements summary]
Executor: [who did the work]
Output: [research findings, proposal, code, verification results, etc.]
Review Questions:
  - Does this meet task requirements?
  - Any risks or considerations?
  - Specific adjustments needed?
  - Ready for human approval?
```

#### Feedback Loop

- Review Agent responds: "Recommend adjustments A, B, C"
- Orchestrating Agent re-routes to Executor with specific requested changes
- Executor refines output
- Review Agent re-evaluates
- Cycle continues until "Ready for human approval"

**Key property**: This is **iterative refinement through multiple agents**, not single-agent execution.

---

### Rule 22 — Capability-Optimized Agent Routing

**Route work based on capability required, cost efficiency, and resource availability. Minimize resource consumption.**

#### Routing Tiers (Ordered by Cost Efficiency)

**Tier 1: Local Agents (0x cost, unlimited sessions)**
- Qwen + local models
- **Capabilities**: Orchestration, routing decisions, task reading, local file access, repository edits, testing setup, synthesis reports
- **Role**: Decision-making, work coordination, file operations
- **Cost**: Minimal/free (local execution)
- **When to use**: Orchestration, work pairing, local edits (preferred for cost efficiency)

**Tier 2: Free-Web Agents (0x token cost to your budget, variable session constraints)**
- Agents with no-cost sessions (research, brainstorming, synthesis, review capabilities)
- Examples: Web-based free research agents, reasoning agents, multi-turn conversation agents
- **Capabilities**: Research, reasoning, synthesis, review, domain-specific analysis, extended context work
- **Role**: Primary work execution (when capability or speed needed)
- **Cost**: 0x tokens to your Copilot budget
- **Session constraints**: Varies by agent (some unlimited, some limited session duration)
- **When to use**: All work execution, review, synthesis (before considering premium resources)

**Tier 3: Premium Resources (Token-tracked, strategic use)**
- Copilot Premium agents, proprietary research APIs, special-capability services
- **Capabilities**: Complex reasoning + speed, advanced analysis
- **Role**: Used only when Tiers 1 + 2 cannot accomplish the task
- **Cost**: Tracked against daily/weekly budget
- **When to use**: Only when local + free-tier can't solve it

#### Capability-Based Routing Decision Tree

```
Does this task need local file access or repository edits?
  ├─ Yes → Use Tier 1 (local, cost-optimal)
  ├─ No → Continue to next question

Do we have sufficient token budget and time available?
  ├─ Budget < 20% remaining → Use Tier 2 (free-tier only)
  ├─ Budget > 20% → Continue to next question

Does Tier 2 have the required capability?
  ├─ Yes → Use Tier 2 (cost-efficient)
  ├─ No → Use Tier 3 (with budget approval)

Can this wait for Tier 2 availability/session window?
  ├─ Yes → Queue for Tier 2
  ├─ No (time-critical) → Use Tier 3 or Tier 1 if available
```

**Key principle**: Capability-based, not vendor-locked. Any agent capable of the work can be used; route to cost-optimal capable tier first.

#### Monitoring & Budget Awareness

- Track premium resource consumption daily
- Alert when approaching 70% of budget
- Force prioritization of Tiers 1 & 2 if over budget
- Review and adjust routing monthly
- Tier 2 session limits determine scheduling (not substitutable, real constraint)

---

### Rule 23 — Blocking Dependency Management & Non-Blocking Parallelization

**When a task is blocked (waiting for review, decision, or external event), pivot to non-blocking work.**

#### Blocking Scenarios

- Waiting for human review/decision
- Waiting for escalation review (synthesis pipeline feedback)
- Waiting for production validation before downstream work
- Waiting for external dependency (API availability, data, etc.)

#### Non-Blocking Work (Can Proceed in Parallel)

- Separate features/domains (unrelated to blocked work)
- Research tasks (can run independently)
- Testing tasks (parallel verification)
- Preparation work for blocked task (research, design docs)

#### Orchestrating Agent's Blocking Awareness

When Orchestrating Agent evaluates routing:
1. **Is this task blocked?** (Yes → note blocker, move to next task)
2. **Can we identify non-blocking work?** (Yes → route to available agent)
3. **Keep all work streams moving in parallel**
4. **When blocker clears, reconverge** and unblock dependent work

#### Example Workflow

```
Current state:
- Task A: Proposal draft for human review (BLOCKED, waiting for decision)
- Task B: Research task (independent, can proceed)
- Task C: Testing task (independent, can proceed)

Orchestrating Agent's decision:
- Task A is blocked → note "waiting for human approval" → don't idle
- Task B ready → route to available agent (cost-optimal)
- Task C ready → route to available agent (cost-optimal)
- Meanwhile: Keep waiting for human review on A

Result: Maximum throughput, no idle time, multiple workstreams progress in parallel
```

---

### Rule 24 — Agent Preferences as Guidance, Not Rules

**Projects may define agent preferences for focus and consistency. These are advisory only, never constraints.**

#### How Preferences Work

- Each project defines preferences in its `SESSION_GUIDANCE.md`
- Example: "Research work prefers Agent A (but any capable agent can do it)"
- Orchestrating Agent uses preferences when:
  - Preferred agent is available and capable → use them (maintains focus)
  - Preferred agent unavailable → use any other available capable agent (maintains throughput)
  - Blocking situation exists → pivot to other work with any available agent (throughput priority)

#### What Preferences Are NOT

- Not hard rules (never block work if preferred agent unavailable)
- Not agent assignments (agents are fungible; preference is guidance only)
- Not constraints on agent experimentation or portfolio rotation
- Not documented in universal governance (live in project-specific SESSION_GUIDANCE)

#### When to Adjust Preferences

- When experimentation shows different agent performs better
- When agent availability changes long-term
- When project scope evolves and different agent becomes better fit
- Preferences should be fluid and data-driven, not static

---

### Rule 25 — Per-Project Implementation via SESSION_GUIDANCE.md

**Each project implements the universal governance through its own `SESSION_GUIDANCE.md` file.**

#### What SESSION_GUIDANCE.md Contains

**Does contain** (project-specific coordination context):
- Agent preferences for focus (advisory only)
- Project-specific blocking dependencies (current state)
- Current active work streams and experiments
- Routing constraints (session time limits, availability windows)

**Does NOT contain** (canonical sources elsewhere):
- Task file authority or task status (lives in task files)
- Backlog/active/completed state (lives in task file structure)
- Architecture decisions (lives in project README or decision docs)
- Repeatable patterns (live in GUARDRAILS/TASK_TEMPLATE)

#### Reference Structure

```markdown
# [Project] — Session Guidance

## Agent Preferences (Advisory, Not Hard Rules)
- Domain A work: Prefer [Agent] (but any capable agent can work on it)
- Domain B work: Prefer [Agent] (but any capable agent can work on it)
- Synthesis/Review: Prefer [Agent] (but any capable agent can work on it)
- Note: Preferences guide routing when choices available; they never block work.

## Current Blocking Status
- Task X blocked on: [blocker description]
- Duration: [estimated time]
- Parallel work available: [non-blocking tasks that can proceed]

## Active Experiments
- [Experiment name]: [what we're testing]
- Status: [results so far]
- Decision date: [when to consolidate results]

## Routing Notes
- [Any project-specific considerations for orchestrating agent]
```

#### Governance Relationship

| Document | Scope | Authority | Update Frequency |
|----------|-------|-----------|------------------|
| TASK_FILE_AUTHORITY_AND_MULTI_AGENT_ROUTING_PROPOSAL.md | Universal rules (Rules 20-25) | Project-independent | Phase 1 (one-time) |
| PROJECT/SESSION_GUIDANCE.md | Project routing context | Project-specific | Active session updates |
| PROJECT/status.md | Living progress, blocking state | Project-specific | Daily/per-task updates |
| PROJECT/tasks/ | Individual work units | Project-specific, authoritative | Per-task lifecycle |

**The proposal defines HOW to work (rules). SESSION_GUIDANCE.md defines WHAT preferences and constraints exist for THIS project.**

---

## Item 2: Proposed SESSION_GUIDANCE.md Template (Minimal, Non-Duplicative)

```markdown
# [PROJECT_NAME] — Session Guidance

**Last Updated**: [DATE]  
**Maintained By**: Project Coordinator  

---

## Agent Preferences (Advisory, Not Hard Rules)

Each preference is guidance for focus and consistency, not a constraint. Any capable agent can do any work.

- **Research work**: Prefer [Agent Name] (but any agent can research)
- **Implementation work**: Prefer [Agent Name] (but any agent can implement)
- **Review/Synthesis**: Prefer [Agent Name] (but any agent can review)

*Prefer means "when available, use this agent for consistency and focus." Preferred agent unavailable? Use any available agent. No idle time for preferences.*

---

## Current Blocking Status

If a task is blocked, note it here with impact on parallelization.

- **Task or Stream Name**: Blocked on [specific blocker]
- **Duration**: [Est. days/hours until cleared]
- **Parallel Work**: [What can proceed while blocked]

*No blocking? Leave this section empty or remove it.*

---

## Active Experiments

Document any work testing new agents, capabilities, or approaches.

- **Experiment**: [Brief name of what we're testing]
- **Status**: [What we've learned so far]
- **Decision Date**: [When to consolidate or retire]

*Example: "Testing Gemini for image rendering in asset-ui. So far: good performance, good cost efficiency. Decision by [DATE]."*

---

## Routing Notes

Any project-specific routing constraints or coordination facts.

- [Project-specific fact that affects routing or scheduling]

*Example: "Session time window 9am–12pm UTC (use agents with long sessions). Production validation in progress (unblock backport work on approval)."*

---

## References

- Task backlog: `/agent-tasks/projects/[PROJECT]/tasks/`
- Project status: `/agent-tasks/projects/[PROJECT]/status.md`
- Project README: `/agent-tasks/projects/[PROJECT]/README.md`
- Universal governance: `/agent-tasks/rules/GUARDRAILS.md` (Rules 20-25)
```

**Key properties**:
- ✅ Minimal (one screen)
- ✅ Context only (no task/architecture duplication)
- ✅ Updated per session (not frozen)
- ✅ Blocks work only on true constraints (session time, external events)
- ✅ Preferences clearly advisory, not rules

---

## Item 3: Exact GUARDRAILS.md Sections for Phase 1 Modification

**File**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`

### Modification 1: Add Section After Rule 20 (Continue Model Scope Limits)

**Location**: After current Rule 22 (Continue Model Scope Limits), before Rule 23  
**Action**: Insert new section header and add Rules 20-25

```markdown

---

## Universal Governance Rules (Rules 20–25)

These rules apply to all projects and all agents conducting task-driven work.

### Rule 20 — Task File as Authoritative Execution Contract

[FULL TEXT FROM ITEM 1, Rule 20]

### Rule 21 — Multi-Agent Synthesis Pipeline

[FULL TEXT FROM ITEM 1, Rule 21]

### Rule 22 — Capability-Optimized Agent Routing

[FULL TEXT FROM ITEM 1, Rule 22 — WITH CAPABILITY-BASED LANGUAGE]

### Rule 23 — Blocking Dependency Management & Non-Blocking Parallelization

[FULL TEXT FROM ITEM 1, Rule 23]

### Rule 24 — Agent Preferences as Guidance, Not Rules

[FULL TEXT FROM ITEM 1, Rule 24]

### Rule 25 — Per-Project Implementation via SESSION_GUIDANCE.md

[FULL TEXT FROM ITEM 1, Rule 25]

```

### Modification 2: Update Rule 23 (Token Conservation) Heading

**Current heading**: `Rule 23 — Token Conservation (Core Strategy)`  
**New heading**: `Rule 26 — Token Conservation (Core Strategy)`  
**Reason**: Shift due to new Rules 20-25 insertion

**Action**: Change all references from `Rule 23` to `Rule 26` in that section.

### Modification 3: Renumber Subsequent Rules

- Current Rule 24 (Perplexity Workflow) → becomes Rule 27
- Current Rule 25 (No File Recreation) → becomes Rule 28
- Current Rule 27 (merged into Rule 10) → becomes Rule 29 (or mark as superseded)

---

## Item 4: Proposed Minimal Dispatch-Prompt Template

**Template**: Use EXACTLY this structure, modify only bracketed content.

```
You are the Implementation Agent.

Project: [PROJECT_NAME]
Task File: /absolute/path/to/task.md

Read the task file in full. The task file is authoritative for all implementation decisions.

Portfolio Modifier (if any): [OPTIONAL one-sentence constraint, or omit if none apply]

CRITICAL: Stop and report (do not proceed) if:
- Task file path does not exist
- Task file status is not "active"
- Any prerequisite stated in task file is not met
- Ambiguity exists between task file instructions and this prompt
```

**Character limits**: 
- Total: < 300 chars when no modifier
- Modifier: max 1 sentence (40 chars)

**What goes in Portfolio Modifier** (pick at most ONE):
- "Another agent is active on Feature X (avoid conflicts)"
- "Production validation in progress (unblock on completion)"
- "Test environment has limited memory (prefer smaller models)"
- "Session time window: 9am–12pm UTC (use long-session agents)"

**What NEVER goes in dispatch prompt**:
- ❌ Implementation steps (belong in task file)
- ❌ Acceptance criteria (belong in task file)
- ❌ Prerequisites (belong in task file)
- ❌ Architecture context (belongs in task file Prerequisites)
- ❌ Code examples (belong in task file)
- ❌ "First, move the task file..." (belong in task file lifecycle section)

---

## Item 5: Selected-Task Preflight Checklist

**Before accepting dispatch, the assigned agent MUST verify:**

```
✅ PREFLIGHT VERIFICATION (STOP IF ANY FAIL)

☐ Task file exists at the exact path specified
☐ Task file is in active/ directory (status: active in YAML)
☐ YAML frontmatter is valid and complete
☐ All referenced files and paths exist in current repo state
☐ No active agent is currently working on this task
☐ No active agent is currently working on a blocking/dependent task
☐ All stated prerequisites are still true (verify, don't assume)
☐ Current repository state does not already satisfy or invalidate task
☐ Acceptance criteria are testable and clear
☐ No ambiguity between task file and dispatch prompt

IF ANY CHECK FAILS:
- Report the specific failure (one sentence)
- Do not proceed to implementation
- Wait for human clarification or task file update
```

**Output format** (required before ANY work begins):

```
✅ PREFLIGHT VERIFICATION — PASS
[List each check result: ☑ or ☐]

Ready to proceed.
```

OR

```
❌ PREFLIGHT VERIFICATION — FAIL

Failed check: [which check]
Issue: [specific problem in one sentence]
Waiting for: [human clarification | task file update | prerequisite met]
```

---

## Item 6: Return-Handoff Template

**Used at task completion to hand off to next agent or back to human.**

```
You are the [NEXT ROLE] Agent.

Project: [PROJECT_NAME]
Task Completed: [TASK_NAME]
Completed By: [Agent who completed it]

Task file: /absolute/path/to/completed/task.md

Read the COMPLETION REPORT section in the task file for:
- What was accomplished
- What tests passed
- Any blockers or limitations
- What work remains (if any)

Deployment/Next Steps:
- If this is the final task for a feature: Review COMPLETION REPORT and approve for merge
- If this is a work-in-progress task: Move to next step (see task file)
- If this is blocked work: Note blocker and move task to backlog/blocked/ (if directory exists)

Reference files:
- Task: [task file path]
- Project README: [project root]/README.md
- Session Guidance: [project root]/SESSION_GUIDANCE.md
```

**Character limit**: < 500 chars total  
**Purpose**: Orient next agent/human to current state; avoid context duplication

---

## Item 7: Explicit Precedence Order for Conflicting Information

**When information exists in multiple locations, THIS ORDER determines which is authoritative:**

### Precedence Hierarchy (High to Low)

1. **Current Repository State** (filesystem facts)
   - Actual file contents (read from disk)
   - Actual file locations (verified with find/ls)
   - Actual test results (from running RSpec, build output)
   - **Why highest**: Physical reality; everything else describes it

2. **Selected Task File** (YAML + sections)
   - Task frontmatter (status, priority, type, acceptance criteria)
   - Prerequisites (must be verified against repo state)
   - Implementation Steps (authoritative for execution)
   - Acceptance Criteria (authoritative for completion)
   - COMPLETION REPORT (if task is completed)
   - **Why**: Selected for this dispatch; Tracy's explicit assignment

3. **Project Governance & Status Documents**
   - PROJECT/status.md (current blocking state, progress)
   - PROJECT/SESSION_GUIDANCE.md (routing preferences, experiments)
   - PROJECT/README.md (architecture, setup instructions)
   - **Why**: Project context; applies to all tasks in project

4. **Approved Live Portfolio Modifier**
   - One-sentence constraint from dispatch prompt
   - Example: "Another agent active on Feature X (avoid conflicts)"
   - **Why**: Real-time coordination; overrides standing preferences

5. **Prior Handoffs & Session Summaries**
   - Previous agent's handoff message
   - Session notes (if shared)
   - Completed task COMPLETION REPORTS
   - **Why**: Historical context; describes how we got here

6. **Chat Discussion & Instructions** (this conversation)
   - Messages in current session
   - Clarifications from human
   - **Why**: Lowest precedence; can be ambiguous or context-dependent

### Conflict Resolution Example

**Scenario**: Task file says "acceptance criteria: X tests pass" but status.md says "work blocked on external dependency until [DATE]."

**Resolution**: 
1. Check repo state: Are X tests passing? (No) → Continue
2. Check task prerequisites: Are they met? (Check status.md)
3. Status.md says blocked until [DATE]; task file's prerequisites should reflect this
4. If they don't: **Stop and report discrepancy to human**
5. Do not guess which is correct; report the conflict

---

## Item 8: Provider-Neutral, Capability-Based Routing Language

### Original (Provider-Specific) Language from Proposal

**Problem**: Rule 22 listed specific vendor agents (Gemini, Claude, Perplexity, ChatGPT, Grok) as if they were hard-coded choices.

```
# ORIGINAL (TOO SPECIFIC):
Tier 2: Free-Web Agents (0x token cost, variable session limits)
- Gemini (effectively unlimited sessions)
- Claude (90 min per session, preferred for synthesis)
- Perplexity (1-2+ hours per session)
- ChatGPT (limited sessions)
- Grok (limited sessions)
```

### Revised (Capability-Based) Language

```
# REVISED (CAPABILITY-BASED):
Tier 2: Free-Web Agents (0x token cost, variable session constraints)
- Free-tier agents with no-cost sessions
- Examples: Research agents, reasoning agents, brainstorming agents, multi-turn conversation agents
- Capabilities: Research, reasoning, synthesis, review, domain-specific analysis, extended-context work
- Session constraints vary by agent (some unlimited, some limited duration)
- Examples current portfolio: [Agents A, B, C] (subject to change based on testing/availability)

[ROUTING DECISION TREE — use capability-based questions, not vendor names]
```

### Capability-Based Routing Principle

**Instead of**: "Use Claude for synthesis"  
**Say**: "Use an agent with synthesis capability; if multiple available, prefer the one with longer session availability"

**Instead of**: "Gemini for brainstorming"  
**Say**: "Use an agent with brainstorming capability; prefer unlimited-session agent if available"

**Instead of**: "Three tiers" based on vendor  
**Say**: "Three tiers" based on resource consumption (local, free, premium)

---

## Item 9: Phase 3 Files — Complete List & No Mass-Edit Commitment

### Phase 3: Per-Project Implementation (Ongoing)

**Action**: Create `SESSION_GUIDANCE.md` in each project folder.

### Files to Create (One Per Project)

```
projects/[PROJECT_NAME]/SESSION_GUIDANCE.md
```

**Complete List of Projects** (12 total):

1. ✅ `/agent-tasks/projects/galaxy_game/SESSION_GUIDANCE.md`
2. ✅ `/agent-tasks/projects/hyku/SESSION_GUIDANCE.md`
3. ✅ `/agent-tasks/projects/wvu_knapsack/SESSION_GUIDANCE.md`
4. ✅ `/agent-tasks/projects/samvera_hyrax/SESSION_GUIDANCE.md`
5. ✅ `/agent-tasks/projects/wvu_auth/SESSION_GUIDANCE.md`
6. ✅ `/agent-tasks/projects/acda_portal/SESSION_GUIDANCE.md`
7. ✅ `/agent-tasks/projects/[PROJECT_7]/SESSION_GUIDANCE.md`
8. ✅ `/agent-tasks/projects/[PROJECT_8]/SESSION_GUIDANCE.md`
9. ✅ `/agent-tasks/projects/[PROJECT_9]/SESSION_GUIDANCE.md`
10. ✅ `/agent-tasks/projects/[PROJECT_10]/SESSION_GUIDANCE.md`
11. ✅ `/agent-tasks/projects/[PROJECT_11]/SESSION_GUIDANCE.md`
12. ✅ `/agent-tasks/projects/[PROJECT_12]/SESSION_GUIDANCE.md`

### Files to Modify

- ✅ `/agent-tasks/rules/GUARDRAILS.md` (add Rules 20-25, renumber subsequent rules)

### Files NOT Modified

- ❌ Existing task files (not mass-edited)
- ❌ Existing project README.md (not touched)
- ❌ Existing project status.md (not touched)
- ❌ TASK_TEMPLATE.md (no change in Phase 1)
- ❌ SIMPLE_HANDOFF_TEMPLATE.md (no change in Phase 1)
- ❌ Any backlog/ or active/ task files

### Explicit Commitment: No Historical Task File Mass-Edit

**Phase 3 does NOT**:
- ❌ Retroactively update existing task files to Rule 20 compliance
- ❌ Batch-modify task templates or status fields
- ❌ Move or reorganize historical tasks
- ❌ Require historical task files to adopt new preflight checklist

**Historical tasks** (created before Phase 1 complete):
- May be dispatched as-is (Rule 20 historical exemption applies)
- Are updated to compliance only when selected for active work (during move-to-active step)
- Never mass-edited for compliance

**Prospective compliance** (Phase 4):
- All new tasks created after Phase 1 complete MUST comply
- Planning agents verify compliance during task creation
- No retroactive requirements on historical tasks

---

## Summary Table: Phase 1 Changes

| Item | Current | Change | Location |
|------|---------|--------|----------|
| Governance Rules | Rules 1-25 (some duplicates) | Add Rules 20-25 (universal) | GUARDRAILS.md + new section |
| Rule Numbering | Rules 1-25 | Renumber downstream rules (23→26, 24→27, etc.) | GUARDRAILS.md |
| Dispatch Standard | Per-agent templates | Designate SIMPLE_HANDOFF_TEMPLATE.md + minimal prompt | README + standards |
| Task Authority | Implicit | Explicit (Rule 20) | GUARDRAILS.md |
| Synthesis Protocol | Informal | Formal (Rule 21, Proposal Packet format) | GUARDRAILS.md |
| Agent Routing | Implicit | Explicit capability-based tier system (Rule 22) | GUARDRAILS.md |
| Blocking Mgmt | Informal | Formal (Rule 23) | GUARDRAILS.md |
| Preferences | Informal | Formal advisory status (Rule 24) | GUARDRAILS.md |
| Per-project Impl | Informal SESSION_GUIDANCE.md | Standardized template (Rule 25) | Template + instruction |

---

## Constraint Confirmations

✅ **Selected task file and embedded handoff are the execution contract.**  
✅ **Tracy selects and dispatches tasks.**  
✅ **Assigned implementation agent performs task lifecycle (moves, edits, validation, commits, status updates, handoff).**  
✅ **Planning/review agents recommend, improve task readiness, identify blockers; they do NOT autonomously dispatch implementation work.**  
✅ **Agents perform preflight verification and stop/report on material mismatch (no guessing).**  
✅ **Dispatch prompts are short pointers plus optional constraint-only modifiers (no duplication).**  
✅ **Routing is capability-based, not vendor-locked (Rule 22 revised to use capability language).**  
✅ **SESSION_GUIDANCE.md is context only; no duplication of task content, task status, or canonical architecture.**

