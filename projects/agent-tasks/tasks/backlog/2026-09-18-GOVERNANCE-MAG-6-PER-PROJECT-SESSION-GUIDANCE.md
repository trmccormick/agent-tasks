---
status: backlog
priority: CRITICAL
type: governance
system_domain: AGENT_ORCHESTRATION
mvp_alignment: PHASE_1_GOVERNANCE
local_worker_safe: true
cross_project_impact: true
---

# TASK: MAG-6 — Per-Project Implementation via SESSION_GUIDANCE.md

**Status**: Backlog (draft for Tracy review; no Qwen dispatch yet)
**Priority**: CRITICAL
**Type**: Governance
**Repository**: agent-tasks (universal governance, not project-specific)
**Target**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (new MAG section, post-Rule 30)
**Created**: 2026-09-18

⚠️ **CROSS-PROJECT IMPACT**: This rule applies to all 12+ projects and all agent tiers (local, free-web, premium). Edits affect authorization boundaries across every dispatch and synthesis workflow.

---

## Objective

Draft an insertion-ready governance rule headed exactly:

```markdown
### MAG-6 — Per-Project Implementation via SESSION_GUIDANCE.md
```

The rule must establish that universal governance rules apply across all projects while each project may implement project-specific operating guidance through its own `SESSION_GUIDANCE.md`. It must clarify what a project `SESSION_GUIDANCE.md` provides (local, current, strategic, and advisory context such as active experiments, current blockers, project-specific routing notes, and agent preferences), state that it must implement and operationalize applicable universal governance without overriding MAG-1 through MAG-5, allow project guidance to refine how an authorized task is understood within that project only when it does not conflict with the selected task file, applicable governance rules, or an explicit human instruction, require escalation to Tracy when project guidance conflicts with a controlling source or the appropriate location for information is materially unclear, and state that the rule does not mandate `SESSION_GUIDANCE.md` for every project or task, does not duplicate task lifecycle/templating/precedence/handling details, and does not authorize any file edits as part of this drafting task.

This is a draft-only task. It does not authorize implementation in `rules/GUARDRAILS.md`, creation or editing of any project `SESSION_GUIDANCE.md` file, lifecycle/template changes, or edits to any other existing file.

---

## Required Reading

Read these artifacts in full before drafting:

1. `rules/GUARDRAILS.md`
2. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-1-TASK-FILE-EXECUTION-CONTRACT.md`
3. `projects/agent-tasks/tasks/backlog/2026-09-17-CRITICAL-GOVERNANCE-MAG-2-DISPATCH-AUTHORITY.md`
4. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-3-ROUTING.md`
5. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-4-DEPENDENCY-PARALLELIZATION.md`
6. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-5-PREFERENCES-GUIDANCE-NOT-RULES.md`
7. `projects/wvu_knapsack/tasks/backlog/research/2026-09-17-GUARDRAILS-NAMESPACE-PROPOSAL.md`
8. `projects/wvu_knapsack/tasks/backlog/governance_revision/2026-09-17-ITEM-8-SESSION-GUIDANCE-TIGHTEN.md` (if it exists)
9. `projects/wvu_knapsack/tasks/backlog/rollout_revision/2026-09-17-ITEM-12-ROLLOUT-PHASES.md` (if it exists)
10. `TASK_FILE_AUTHORITY_AND_MULTI_AGENT_ROUTING_PROPOSAL.md`, if it exists
11. Any repository task-file convention or project guidance referenced by the artifacts above

If a named artifact does not exist, state that fact in the completion report; do not invent or substitute its contents.

---

## Approved Context

Treat the following as approved policy context. Do not claim it has already been inserted into `rules/GUARDRAILS.md` unless the live file proves that it has.

### MAG-1 — Task File as Execution Contract (approved)

**Execution Contract.** Before any implementation work begins, the assigned agent reads the selected task file in full. The selected task file is the task-specific execution contract. It identifies the objective, scope, required artifacts, constraints, authorized actions, prohibited actions, acceptance criteria, validation requirements, completion-report and handoff obligations, and any applicable human approval gates. Agents execute only within the selected task file and applicable governance rules.

**No Inference of Authority.** Agents must not infer missing authority, access, acceptance criteria, or task scope from chat context, informal handoffs, provider or model preference, or another agent's suggestion. Chat instructions alone do not authorize execution when a selected task file exists. A task file does not override applicable governance rules — it operates within them. An agent that encounters a materially incomplete or ambiguous task file, a conflict with applicable governance rules, missing required access or authority, missing acceptance criteria, or conditions that cannot be completed safely within the task file's stated bounds must stop and escalate to Tracy rather than improvise.

**Routine Execution.** Bounded execution decisions within an explicitly authorized task do not require a separate human approval gate for every ordinary action. Human approval gates are determined by action risk and authority boundary as defined in MAG-2, not by agent identity or provider.

**Consistency with MAG-2.** Agents recommend and execute only within explicit authorization from the selected task file; Tracy selects tasks, approves material and risk-gated decisions, and dispatches. This rule reinforces that boundary at the task-file level.

**Consistency with MAG-3.** Task requirements and constraints identified in the execution contract are the basis for eligibility and routing recommendations prepared by agents under MAG-3.

### MAG-2 — Human-Controlled Dispatch and Synthesis (approved)

**Authority Chain.** Planning/orchestrating agents may inspect task and repository state, synthesize evidence from those artifacts, identify blockers or risks, and prepare a routing recommendation. They **may not** autonomously select an agent, dispatch work, re-dispatch, broaden scope, approve decisions, or act as their own reviewer on authorized work. The human (Tracy) selects the task, approves material decisions and risk-gated actions, and dispatches — agents recommend; Tracy dispatches. Assigned agents execute only the lifecycle and scope explicitly authorized in the selected task file and applicable governance rules. Reviewers verify work quality, recommend revisions, and escalate blockers; they do not make policy, priority, architecture, or acceptance-criteria decisions.

**Human Approval Gates.** Every action falling into the categories below requires explicit human approval before execution, regardless of agent tier, provider identity, or capability: policy decisions, architecture decisions, priority changes, scope modifications, acceptance criteria adjustments, irreversible or hard-to-reverse actions, high blast radius operations (multi-service or cross-project impact), sensitive data or access operations, external communications, material task exceptions that deviate from the selected task file, novel or low-confidence decisions with no precedent in existing artifacts, and production configuration or deployment changes. Gates are determined by **action risk and authority boundary**, not by agent identity — an approved gate applies equally to local, free-web, and premium agents.

**Synthesis.** Multi-agent synthesis iteration is supported but optional for routine, bounded, low-risk work. Independent review (synthesis) is required when the task file, project guidance, a human decision gate, or the risk profile explicitly mandates it. Synthesis is a safety feature, not an overhead constraint on predictable low-risk tasks.

### MAG-3 — Capability- and Availability-Based Agent Routing (approved)

**Task-First Eligibility.** Routing analysis begins with task requirements and constraints identified in the selected task file — not provider identity, model tier, cost, or availability. A candidate lacking a necessary requirement or current availability is ineligible, regardless of provider, nominal model strength, price, free/local status, or general availability. Cost is a secondary comparison factor only among candidates that are both eligible and currently available. Fixed provider/model ladders (local-first, free-first, premium-first, provider-first, model-size-first) are explicitly rejected. Agents prepare eligibility analyses and routing recommendations; Tracy makes final selection and dispatches. Synthesis is optional for routine bounded low-risk work and conditionally required only when the task file, applicable project guidance, a human approval gate, or task risk profile requires it. Escalation is required when no eligible, available agent can safely and reliably meet requirements.

### MAG-4 — Blocking Dependency Management & Non-Blocking Parallelization (approved)

**Dependency Identification.** Before beginning or parallelizing work, the assigned agent identifies material dependencies — unavailable prerequisite artifacts, decisions, access, approvals, or verified upstream results needed to proceed safely. A task is blocking only when such a dependency is genuinely required to continue; other tasks with no such requirement may proceed in parallel.

**Non-Blocking Parallelization.** Independent, non-conflicting, bounded, low-risk work may proceed in parallel without waiting for an unrelated blocker. Each parallel workstream must have a bounded objective, clear ownership, defined inputs and outputs, and no overlapping write targets unless Tracy explicitly approves coordination. A blocker does not authorize stopping unrelated work, duplicating another agent's assigned work, speculating about missing inputs, or silently changing task scope.

**Escalation.** If a required dependency is unclear, missing, materially ambiguous, or conflicts with a governance rule, the agent must report the blocker and escalate to Tracy — rather than manufacturing an answer. Agents must not independently dispatch parallel agents. They may identify dependencies, propose safe parallelization, and recommend sequencing; Tracy selects and dispatches under MAG-2.

**Synthesis Proportionality.** Routine independent low-risk work does not automatically require multi-agent synthesis. Synthesis is required only when task guidance, project guidance, a MAG-2 human approval gate, or the risk profile explicitly mandates it.

### MAG-5 — Agent Preferences as Guidance, Not Rules (approved)

**Advisory Only.** Agent preferences, provider/model preferences, session guidance, role labels, previous-chat context, informal handoffs, suggestions, and advisory project notes are guidance only unless incorporated into the selected task file or applicable governance rules. Advisory guidance may help an agent interpret or plan authorized work but cannot grant authority, alter scope, change acceptance criteria, override required validation, bypass an approval gate, reassign ownership, change task lifecycle state, or direct an agent to stop/continue contrary to the selected active task file.

**Conflict Resolution.** When advisory sources conflict with a selected task file, applicable governance rules, an explicit human instruction, or a risk/authority gate, the higher-precedence controlling source governs. Agents must not treat an advisory source as a reason to ignore a current task step, invent a lifecycle transition, continue past an explicit stop condition, or bypass escalation.

**Escalation Trigger.** If two controlling sources genuinely conflict or the active task's next action is materially unclear, the agent must stop and escalate to Tracy rather than choose whichever chat message is newest.

**Preservation of MAG-1 through MAG-4.** This rule preserves MAG-1 task-file authority, MAG-2 human approval/dispatch authority, MAG-3 task-first routing, and MAG-4 ownership/dependency boundaries. It allows useful preferences and session guidance without requiring escalation merely because an advisory note exists and is non-conflicting.

### MAG-6 Opening Paragraph (Exact Required Text)

Universal governance rules (MAG-1 through MAG-5) apply equally across all projects in the agent-tasks repository. A project may maintain project-specific operating guidance in its own `SESSION_GUIDANCE.md` when that guidance is useful for the project's active or anticipated work. The presence or absence of a `SESSION_GUIDANCE.md` does not itself grant authority, change task eligibility, or require escalation.

### Approved SESSION_GUIDANCE Context (from Item 8 — Tighten SESSION_GUIDANCE.md)

SESSION_GUIDANCE.md provides strategic, not duplicate-tracker context per project. Approved sections include agent preferences (advisory only), active experiments (what's being tested and results so far), routing notes (session time windows, shared constraints), references (links to task backlog, status.md, README), and brief one-line blocking status (if any). It must not duplicate task lists, durations, live availability inventories, or parallel-work inventories.

### Approved Rollout Strategy (from Item 12 — Revise Rollout Phases)

Phase 1 is governance rules and templates only — no project files created. Phase 2 pilots SESSION_GUIDANCE.md in galaxy_game only. Phase 3 creates SESSION_GUIDANCE.md prospectively for active projects on-demand. No bulk creation across all projects.

---

## Required Constraints (Existing — Must Be Preserved verbatim in Rule Prose)

The eventual MAG-6 rule prose must preserve ALL of the following constraints:

1. `SESSION_GUIDANCE.md` is **optional**, project-scoped, concise, current, strategic, local, and advisory.
2. It may contain active experiments, current blockers, project-specific routing notes, and advisory agent preferences.
3. It implements and operationalizes applicable universal governance but **cannot override**: MAG-1 through MAG-5, a selected task file, an explicit human instruction, human approval/dispatch gates, ownership/dependency boundaries, stop conditions, or escalation requirements.
4. It may refine understanding of an authorized task only when **non-conflicting** with the selected task file, applicable governance, and explicit human instruction.
5. It **cannot justify**: ignoring task steps, inventing lifecycle transitions, bypassing gates, or continuing past explicit stop conditions.
6. It **cannot become**: a duplicate task tracker, substitute for backlog/active/completed lifecycle records, replace task acceptance criteria, replace architecture-decision records, or replace durable/repeatable cross-project governance.
7. Durable universal rules belong in `rules/GUARDRAILS.md`; task-specific authority belongs in the selected task file.
8. A genuine conflict with a controlling source, or material uncertainty about the proper location for information, requires stopping and escalating to Tracy.
9. The mere presence of non-conflicting project guidance must **not** require escalation.
10. It must not require a `SESSION_GUIDANCE.md` for every project or task, and it must not authorize bulk creation or rollout of project guidance files.

---

## In Scope

Produce concise, direct, insertion-ready rule prose, targeted at 200–320 words, headed exactly:

```markdown
### MAG-6 — Per-Project Implementation via SESSION_GUIDANCE.md
```

that states the required opening paragraph (above) and incorporates each of the Required Constraints. The rule must also:

- State that universal governance rules (MAG-1 through MAG-5) apply across all projects equally.
- Authorize each project to implement project-specific operating guidance through its own `SESSION_GUIDANCE.md` when the project has active work or incoming tasks.
- Defines what a project `SESSION_GUIDANCE.md` provides: local, current, strategic, and advisory context appropriate to that project (active experiments, current blockers, project-specific routing notes, agent preferences).
- States that a project `SESSION_GUIDANCE.md` must implement and operationalize applicable universal governance rules; it must not override MAG-1 task-file authority, MAG-2 human decision/dispatch gates, MAG-3 task-first eligibility and routing, MAG-4 dependency/ownership boundaries, or MAG-5 advisory-status boundaries.
- Allows project guidance to refine how an authorized task is understood in that project only when it does not conflict with the selected task file, applicable governance rules, or an explicit human instruction.
- Prohibits project `SESSION_GUIDANCE.md` from becoming a duplicate task tracker, replacing backlog/active/completed task lifecycle records, supplanting task acceptance criteria, duplicating architecture-decision records, or replacing durable/repeatable cross-project governance patterns.
- Requires project guidance to remain concise, current, and project-scoped; durable universal rules belong in `rules/GUARDRAILS.md` and task-specific authority belongs in the selected task file.
- Requires escalation to Tracy when project guidance conflicts with a controlling source or the appropriate location for information is materially unclear — rather than silently treating `SESSION_GUIDANCE.md` as controlling authority.
- Explicitly allows projects to use `SESSION_GUIDANCE.md` productively without making it mandatory for every task, requiring escalation merely because such a file exists and is non-conflicting, or mandating creation across all projects.

---

## Out of Scope

Do not include:

- Edits to `rules/GUARDRAILS.md` or any other file
- Creation or editing of any project `SESSION_GUIDANCE.md` file
- Full `SESSION_GUIDANCE.md` template design (Item 8 domain)
- Project-by-project rollout mechanics (Item 12 domain)
- Task-lifecycle preflight, resume/restart mechanics, staging, commit gates, completed-state moves, or status-tracker operations
- A full precedence hierarchy design
- Task-file execution-contract definitions (MAG-1 domain)
- Human dispatch/approval rules (MAG-2 domain)
- Capability routing, provider/model identity, inventory, pricing, or selection tables (MAG-3 domain)
- Dependency/parallelization rules (MAG-4 domain)
- Advisory-vs-authority classification (MAG-5 domain)
- Agent dispatch mechanics, provider-specific tooling, or command syntax
- Repository changes, commits, task moves, or task-status changes
- Speculation about later MAG rules or their identifiers/content

---

## Acceptance Criteria

1. Universal governance rules are stated as applying across all projects; each project may implement project-specific operating guidance through `SESSION_GUIDANCE.md` when active.
2. A project `SESSION_GUIDANCE.md` is clearly defined as providing local, current, strategic, and advisory context (active experiments, blockers, routing notes, agent preferences).
3. It explicitly states that project guidance must not override MAG-1 through MAG-5 controlling sources.
4. It allows refinement of authorized-task understanding in a project only when no conflict exists with the task file, governance, or explicit human instruction.
5. It prohibits project guidance from becoming a duplicate tracker, task contract, architecture-decision system, or substitute for durable governance.
6. It requires concise, current, project-scoped guidance; durable rules belong in `GUARDRAILS.md`, task authority in the selected task file.
7. It requires escalation to Tracy on conflict with a controlling source or materially unclear information location — not mere presence of non-conflicting guidance.
8. It explicitly allows productive use without mandating `SESSION_GUIDANCE.md` for every project/task or requiring escalation when non-conflicting guidance exists.
9. The rule is insertion-ready and uses terminology/style consistent with the live `rules/GUARDRAILS.md`.
10. It requires no repository changes.

---

## Negative Validation (Critical)

**After drafting, verify:**

- Could a reasonable reader infer that `SESSION_GUIDANCE.md` overrides a selected task file, applicable governance rule, explicit human instruction, or approval gate? (If yes, revise.)
- Does the text turn project guidance into a backlog/active/completed tracker, task contract, architecture-decision system, or substitute for durable governance? (If yes, remove.)
- Does it make `SESSION_GUIDANCE.md` mandatory for every task or every project? (If yes, revise — it must be permissive, not mandatory.)
- Does it require escalation merely because non-conflicting project guidance exists? (If yes, revise — escalation is only for genuine conflicts or unclear information location.)
- Does it embed lifecycle/restart/commit-gate mechanics or a full precedence hierarchy? (If yes, remove.)
- Does it create or edit project guidance files or `GUARDRAILS.md` as part of the drafting task? (If yes, remove — this is draft-only.)
- Does it conflict with MAG-1's task-contract authority? (If yes, revise.)
- Does it undermine MAG-2 human dispatch/approval gates? (If yes, revise.)
- Does it conflict with MAG-3 task-first eligibility? (If yes, revise.)
- Does it override MAG-4 dependency/ownership boundaries? (If yes, revise.)
- Does it contradict MAG-5 advisory-vs-authority classification? (If yes, revise.)

---

## Completion Report

Return exactly:

```markdown
## Draft MAG-6 Rule Prose
[exact insertion-ready Markdown headed `### MAG-6 — Per-Project Implementation via SESSION_GUIDANCE.md`]

## Drafting Rationale
[2–3 concise sentences]

## Consistency Check
[alignment with MAG-1 through MAG-5; artifacts read/unavailable; precise conflicts if any]

## Ready for Review?
[YES or NO; if NO, state the specific blocker]

## No-Change Confirmation
[confirm no file edits, task moves/status changes, commits, or agent dispatches occurred]
```

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are a governance-drafting agent.

Project: agent-tasks

Read the full task file and draft the MAG-6 rule prose exactly as specified in the Completion Report section. This is DRAFT-ONLY work — no file edits, no commits, no task moves.
```
