# Task File Authority & Multi-Agent Routing Governance Proposal

**Created**: 2026-09-17  
**Author**: Planning Agent (Qwen)  
**Status**: PROPOSAL — Awaiting Tracy's three key decisions  
**Scope**: Universal governance for all managed projects (12 total)  

---

## Executive Summary

This proposal establishes **universal governance rules** for task-driven, cost-optimized multi-agent coordination across all projects. It formalizes existing practices into repeatable, scalable patterns.

**Core principle**: Maximize free resources (local Qwen 0x + free-web agents 0x) before using Copilot premium (token-tracked). Route work based on blocking dependencies and capability, not fixed assignments.

**Key outcome**: Systematic approach to orchestrating multiple agents (Qwen local, Gemini, Claude, Perplexity, ChatGPT, Grok, and experimental free-tier agents) while keeping costs low and throughput high.

---

## 1. Current-State Map & Validation Against Actual Workflow

### Existing Infrastructure (Already In Place)

| Component | Location | Purpose | Status |
|-----------|----------|---------|--------|
| TASK_TEMPLATE.md | `/agent-tasks/TASK_TEMPLATE.md` | Execution contract template | Active, used in galaxy_game and others |
| SIMPLE_HANDOFF_TEMPLATE.md | `/agent-tasks/SIMPLE_HANDOFF_TEMPLATE.md` | Minimal dispatch format | Active |
| GUARDRAILS.md | `/agent-tasks/rules/GUARDRAILS.md` | Operational guardrails (Rules 1-19) | Active |
| Projects folder | `/agent-tasks/projects/` | Per-project structure | 12 projects active |
| Project README.md | `projects/[PROJECT]/README.md` | Project context | Per-project |
| Project status.md | `projects/[PROJECT]/status.md` | Living progress tracking | Per-project |
| Project SESSION_GUIDANCE.md | `projects/[PROJECT]/SESSION_GUIDANCE.md` | Routing preferences (to be standardized) | Partial/informal |

### Validated Against Tracy's Actual Workflow

This proposal has been refined through discussion of:
- ✅ **Galaxy Game**: Multi-domain work (ai-manager, asset-ui, research) with agent preferences as guidance
- ✅ **WVU Knapsack**: Production validation blocking upstream backport work
- ✅ **Multi-agent pairing**: Qwen local edits + Free-web agent reasoning
- ✅ **Blocking dependency management**: Pivot to non-blocking work while waiting for reviews
- ✅ **Cost optimization in practice**: Using Gemini for unlimited brainstorming, Perplexity for extended research, Claude for escalation review
- ✅ **Free-tier experimentation**: Testing Gemini image rendering alongside ChatGPT in asset-ui

---

## 2. The Problem & Why This Matters

### Current Gaps

1. **No explicit rule making task files authoritative** — Templates suggest it, but no formal governance
2. **No standardized dispatch protocol** — Per-agent handoff templates create ambiguity
3. **No cost-awareness in routing** — Premium agents used when free agents could work
4. **No formalized blocking-dependency management** — Risk of idle time while waiting for reviews
5. **No standardized escalation protocol** — Review/decision workflow implicit, not explicit
6. **No per-project implementation guidance** — SESSION_GUIDANCE exists informally, not standardized

### Why Formalizing This Helps

- **Scalability**: Same system works for all 12 projects without modification
- **Cost control**: Explicit routing logic keeps Copilot use strategic and minimal
- **Throughput**: Blocking dependency awareness eliminates idle time
- **Consistency**: All projects follow same governance, differ only in preferences
- **Experimentation**: Safe way to test new free-tier agents and consolidate winners
- **Transparency**: Tracy (human) retains final authority, agents are orchestrators/advisors

---

## 3. Proposed Governance Rules

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

1. **Qwen (Local Orchestrator)** reads task file
2. **Qwen** evaluates capability needed + availability + session time
3. **Qwen** routes to **Executor** (free-web agent, local work, or premium if necessary)
4. **Executor** produces output (research, proposal, code, findings, etc.)
5. **Qwen** packages as **Proposal Packet** and routes to **Review Agent**
6. **Review Agent** provides feedback:
   - Does this meet requirements?
   - Any risks or considerations?
   - Request adjustments?
   - Ready for Tracy approval?
7. **Qwen** evaluates feedback:
   - Minor adjustments? → Re-route to Executor with specific changes
   - Major rework? → May escalate to Tracy or change strategy
   - Ready? → Send to Tracy for final approval
8. **Tracy** reviews and approves OR sends back for more work

#### Proposal Packet Format

When Qwen sends work to Review Agent:
```yaml
To: Review Agent (Claude/Perplexity/Grok/Gemini)
From: Qwen (Orchestrator)
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
  - Ready for Tracy approval?
```

#### Feedback Loop

- Review Agent responds: "Recommend adjustments A, B, C"
- Qwen re-routes to Executor with specific requested changes
- Executor refines output
- Review Agent re-evaluates
- Cycle continues until "Ready for Tracy approval"

**Key property**: This is **iterative refinement through multiple agents**, not single-agent execution.

---

### Rule 22 — Cost-Optimized Three-Tier Agent Routing

**Maximize free resources (local + free-web), use Copilot premium strategically.**

#### Three Tiers (In Priority Order)

**Tier 1: Local (0x cost, unlimited sessions)**
- Qwen + ollama models
- **Capabilities**: Orchestration, routing decisions, task reading, local file access, repository edits, testing setup
- **Role**: Decision-making, work pairing with free-web agents
- **Cost**: 0x (unlimited)
- **When to use**: Orchestration, pairing, local edits (always preferred for coordination)

**Tier 2: Free-Web Agents (0x token cost, variable session limits)**
- Gemini (effectively unlimited sessions)
- Claude (90 min per session, preferred for synthesis)
- Perplexity (1-2+ hours per session)
- ChatGPT (limited sessions)
- Grok (limited sessions)
- **Experimental**: Any new free-tier agents discovered
- **Capabilities**: Research, reasoning, synthesis, review, brainstorming, domain-specific work
- **Role**: Primary work execution (when speed/capability needed)
- **Cost**: 0x token cost to your budget
- **When to use**: All work execution, review, synthesis (before considering Copilot)

**Tier 3: Copilot Premium (Token-tracked, strategic use)**
- Haiku (.33) — Lightweight, file access, fast
- Other Copilot agents (varying costs)
- **Capabilities**: Complex reasoning + speed, special capabilities
- **Role**: Used only when Tier 1 + Tier 2 can't do it
- **Cost**: Tracked against daily/weekly budget
- **When to use**: Only when local + free-web can't solve it

#### Routing Decision Tree

```
Does this task need local file access?
  ├─ Yes → Use Qwen (local, 0x)
  ├─ No → Continue to next question

Does this task need immediate speed?
  ├─ No (can wait) → Use free-web agent (0x)
  ├─ Yes → Continue to next question

Do we have token budget available?
  ├─ No (< 20% remaining) → Use free-web agent anyway (0x)
  ├─ Yes → Consider Copilot if capability gap exists
  
Do free-web agents have the capability?
  ├─ Yes → Use them (0x)
  ├─ No → Use Copilot (with budget approval)
```

#### Monitoring

- Track daily/weekly Copilot token consumption
- Alert when approaching 70% of budget
- Force prioritization of free agents if over budget
- Review and adjust routing monthly

---

### Rule 23 — Blocking Dependency Management & Non-Blocking Parallelization

**When a task is blocked (waiting for review, decision, or external event), pivot to non-blocking work.**

#### Blocking Scenarios

- Waiting for Tracy review/decision
- Waiting for escalation review (Claude/Perplexity feedback)
- Waiting for production validation before downstream work
- Waiting for external dependency (API availability, etc.)

#### Non-Blocking Work (Can Proceed in Parallel)

- Separate features/domains (unrelated to blocked work)
- Research tasks (can run independently)
- Testing tasks (parallel verification)
- Preparation work for blocked task (research, design docs)

#### Qwen's Blocking Awareness

When Qwen evaluates routing:
1. **Is this task blocked?** (Yes → note blocker, move to next task)
2. **Can we identify non-blocking work?** (Yes → route to available agent)
3. **Keep all work streams moving in parallel**
4. **When blocker clears, reconverge** and unblock dependent work

#### Example Workflow

```
Current state:
- Task A: Proposal draft for Tracy review (BLOCKED, waiting for decision)
- Task B: Asset-UI work (independent, can proceed)
- Task C: Research for galaxy_game (independent, can proceed)

Qwen's decision:
- Task A is blocked → note "waiting for Tracy" → don't idle
- Task B ready → route to Gemini (free-web, good for UI research)
- Task C ready → route to Perplexity (free-web, good for extended research)
- Meanwhile: Keep waiting for Tracy review on A

Result: Maximum throughput, no idle time, multiple workstreams progress in parallel
```

---

### Rule 24 — Agent Preferences as Guidance, Not Rules

**Projects may define agent preferences for focus and consistency. These are advisory, not hard constraints.**

#### How Preferences Work

- Each project defines preferences in its `SESSION_GUIDANCE.md`
- Example: "AI-Manager work prefers Grok (but any agent can do it)"
- Qwen uses preferences when:
  - Preferred agent is available → use them (maintains focus)
  - Preferred agent unavailable → use any other available agent (maintains throughput)
  - Blocking situation exists → pivot to other work with any available agent

#### What Preferences Are NOT

- Not hard rules (don't block work if preferred agent unavailable)
- Not agent assignments (agents are fungible across projects)
- Not constraints on free-agent experimentation
- Not documented in governance (live in project-specific SESSION_GUIDANCE)

#### When to Adjust Preferences

- When experimentation shows different agent performs better
- When agent availability changes long-term
- When project scope evolves and different agent becomes better fit
- Preferences should be fluid and data-driven

---

### Rule 25 — Per-Project Implementation via SESSION_GUIDANCE.md

**Each project implements the universal governance through its own `SESSION_GUIDANCE.md` file.**

#### What SESSION_GUIDANCE.md Contains

**Not in this proposal** (project-specific):
- Agent preferences for focus (advisory only)
- Project-specific blocking dependencies
- Current active work streams
- Testing/experimentation status (Gemini + ChatGPT for asset-ui, etc.)

**Reference structure** (each project maintains its own):

```markdown
# [Project] — Session Guidance

## Agent Preferences (Advisory, Not Hard Rules)
- Domain A work: Prefer Agent X (but any agent can do it)
- Domain B work: Prefer Agent Y (but any agent can do it)
- Synthesis/Review: Prefer Claude (but any agent can do it)

## Current Blocking Status
- Task X blocked on: [blocker]
- Duration: [estimated]
- Parallel work: [what can proceed while blocked]

## Active Experiments
- Gemini testing: [what we're testing]
- Status: [results so far]
- Decision date: [when to consolidate]

## Routing Notes
- [Any project-specific considerations]
```

#### Governance Relationship

- **Proposal** (this document): Universal rules, project-independent
- **PROJECT/SESSION_GUIDANCE.md**: Implementation of rules, project-specific
- **PROJECT/status.md**: Living progress, blocking state, active work
- **PROJECT/tasks/**: Individual work units, task files

**The proposal defines HOW to work. SESSION_GUIDANCE defines WHAT preferences exist for THIS project.**

---

## 4. Simplification: How This Replaces Complexity

### What Goes Away

- ❌ Per-agent handoff templates (use SIMPLE_HANDOFF_TEMPLATE + task file)
- ❌ Multiple project-specific templates (use universal SESSION_GUIDANCE structure)
- ❌ Ambiguity about "is this task authoritative?" (task file is always authoritative)
- ❌ Agent assignment locks (preferences are advisory, work flows to available agents)
- ❌ Idle time (blocking dependency awareness keeps work moving)
- ❌ Cost surprises (explicit three-tier routing with budget tracking)

### What Stays

- ✅ TASK_TEMPLATE.md (improved clarity with Rule 20)
- ✅ SIMPLE_HANDOFF_TEMPLATE.md (now THE standard for dispatch)
- ✅ GUARDRAILS.md (extended with Rules 20-25)
- ✅ Project folder structure (enhanced with SESSION_GUIDANCE.md standard)
- ✅ Task lifecycle (backlog → active → completed)

### What's New (Minimal)

- ✨ Four new governance rules (20-25)
- ✨ Standardized SESSION_GUIDANCE.md template per project
- ✨ ACTIVE_AGENT_PORTFOLIO.md (optional, centralized portfolio tracking)
- ✨ Explicit proposal packet format (Rule 21)

---

## 5. Implementation Roadmap

### Phase 1: Repository-Level Governance (Immediate)

- [ ] Add Rules 20-25 to GUARDRAILS.md
- [ ] Designate SIMPLE_HANDOFF_TEMPLATE.md as THE standard dispatch format
- [ ] Update TASK_TEMPLATE.md header to emphasize Rule 20 (task file authority)
- [ ] Create SESSION_GUIDANCE.md template in `/agent-tasks/templates/`
- [ ] Document Rule 21 proposal packet format in this proposal

**Timeline**: 1 session  
**No task files modified**

### Phase 2: Create ACTIVE_AGENT_PORTFOLIO.md (Optional)

- [ ] Create `/agent-tasks/ACTIVE_AGENT_PORTFOLIO.md` (centralized state tracking)
- [ ] Include: agent availability, session time remaining, current assignments, blocking status
- [ ] Qwen can reference this when routing decisions

**Timeline**: 1 session  
**Value**: Enables sophisticated routing decisions; optional for smaller portfolios

### Phase 3: Implement Per-Project (Ongoing)

- [ ] Each project creates `projects/[PROJECT]/SESSION_GUIDANCE.md`
- [ ] Populate with current preferences, blocking status, experiments
- [ ] Update as project evolves

**Timeline**: As projects become active  
**Per-project effort**: 30 min

### Phase 4: Prospective Compliance (Automatic)

- [ ] All new tasks created after Phase 1 automatically comply
- [ ] Planning agents verify compliance during task creation
- [ ] No retroactive changes to existing tasks

**Timeline**: Ongoing  
**Effort**: Built into workflow

---

## 6. Decision Points for Tracy

### Decision 1: Adopt Rules 20-25 as Standing Governance?

**What this means**: Formalize task file authority, multi-agent synthesis, cost optimization, blocking dependency management, and agent preferences as official rules.

**Alternatives**:
- A) **Adopt as written** — Minimal rules, explicit protocols, no constraints
- B) **Adopt with modifications** — Tracy proposes specific changes
- C) **Do not adopt** — Continue with informal practices only

**Recommendation**: A — The rules formalize what's already working in practice.

---

### Decision 2: Create Centralized ACTIVE_AGENT_PORTFOLIO.md?

**What this means**: Maintain a real-time portfolio file showing agent availability, session time, current work, blocking status. Qwen references this for routing decisions.

**Alternatives**:
- A) **Yes** — Creates sophisticated routing capability, explicit portfolio awareness
- B) **No** — Qwen handles portfolio informally (still works, less optimization)

**Recommendation**: B for now — Can add later if routing becomes constrained. SIMPLE dispatch works without it.

---

### Decision 3: Standardize SESSION_GUIDANCE.md Per Project?

**What this means**: Each project maintains a standard SESSION_GUIDANCE.md file documenting:
- Agent preferences (advisory)
- Blocking dependencies
- Current experiments
- Routing notes

**Alternatives**:
- A) **Yes** — Standardize structure, but keep content flexible per project
- B) **No** — Continue informal project documentation

**Recommendation**: A — Minimal overhead, significant clarity. One template fits all projects.

---

## 7. Success Metrics

Once implemented, you should see:

- ✅ **Reduced Copilot token consumption** — Free agents used for eligible work
- ✅ **Faster project delivery** — Blocking dependency awareness eliminates idle time
- ✅ **Clearer routing decisions** — Qwen can make principled choices (tier 1 → tier 2 → tier 3)
- ✅ **Easier onboarding** — New projects use same governance immediately
- ✅ **Cost visibility** — Explicit tracking of premium agent use
- ✅ **Experiment safety** — Can test new agents (e.g., Gemini for images) without governance risk

---

## 8. Appendix: Validated Against Actual Workflow

This proposal has been refined through analysis of:

**Galaxy Game**:
- Multi-domain work (ai-manager, asset-ui, research)
- Agent preferences used for focus, not constraints
- Blocking on decisions → parallel work on unrelated domains
- Gemini unlimited sessions for brainstorming

**WVU Knapsack**:
- Production validation blocks upstream backport
- Free agents used for review/synthesis (Claude, Perplexity)
- Qwen local for orchestration, edits, file access

**WVU Hyku**:
- Backport task queued, blocking on production validation
- Demonstrates blocking dependency management
- Shows how to pivot to other work while blocked

**Multi-agent pairing pattern**:
- Qwen (local) + Free-web agent pair for research → review → adjust
- Cost-optimized: 0x local + 0x free = zero token cost for planning

**Cost optimization in practice**:
- Free agents: Gemini (unlimited), Perplexity (extended), Claude (focused)
- Local: Qwen for orchestration and local work
- Premium: Minimal use, strategic only

---

## References

- TASK_TEMPLATE.md — Already mandates Agent Dispatch Interface as execution contract
- SIMPLE_HANDOFF_TEMPLATE.md — Already forbids extra context beyond task file
- GUARDRAILS.md (Rules 1-19) — Existing operational guardrails
- Project structure — 12 active projects ready for SESSION_GUIDANCE adoption

