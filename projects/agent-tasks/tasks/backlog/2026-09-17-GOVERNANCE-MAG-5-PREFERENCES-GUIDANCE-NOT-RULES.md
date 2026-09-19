---
status: backlog
priority: CRITICAL
type: governance
system_domain: AGENT_ORCHESTRATION
mvp_alignment: PHASE_1_GOVERNANCE
local_worker_safe: true
cross_project_impact: true
---

# TASK: MAG-5 — Agent Preferences as Guidance, Not Rules

**Status**: Backlog (draft for Tracy review; no Qwen dispatch yet)  
**Priority**: CRITICAL  
**Type**: Governance  
**Repository**: agent-tasks (universal governance, not project-specific)  
**Target**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (new MAG section, post-Rule 30)  
**Created**: 2026-09-17

⚠️ **CROSS-PROJECT IMPACT**: This rule applies to all 12+ projects and all agent tiers (local, free-web, premium). Edits affect authorization boundaries across every dispatch and synthesis workflow.

---

## Objective

Draft an insertion-ready governance rule headed exactly:

```markdown
### MAG-5 — Agent Preferences as Guidance, Not Rules
```

The rule must establish that agent preferences, provider/model preferences, session guidance, role labels, previous-chat context, informal handoffs, suggestions, and advisory project notes are guidance only unless incorporated into the selected task file or applicable governance rules. Advisory guidance may help an agent interpret or plan authorized work, but it cannot grant authority, alter scope, change acceptance criteria, override required validation, bypass an approval gate, reassign ownership, change task lifecycle state, or direct an agent to stop/continue contrary to the selected active task file. When advisory sources conflict with a selected task file, applicable governance rules, an explicit human instruction, or a risk/authority gate, the higher-precedence controlling source governs. The agent must not treat an advisory source as a reason to ignore a current task step, invent a lifecycle transition, continue past an explicit stop condition, or bypass escalation. If two controlling sources genuinely conflict or the active task's next action is materially unclear, the agent must stop and escalate to Tracy rather than choose whichever chat message is newest. MAG-5 must preserve MAG-1 task-file authority, MAG-2 human approval/dispatch authority, MAG-3 task-first routing, and MAG-4 ownership/dependency boundaries. The rule must allow useful preferences and session guidance without requiring escalation merely because an advisory note exists.

This is a draft-only task. It does not authorize implementation in `rules/GUARDRAILS.md`.

---

## Required Reading

Read these artifacts in full before drafting:

1. `rules/GUARDRAILS.md`
2. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-1-TASK-FILE-EXECUTION-CONTRACT.md`
3. `projects/agent-tasks/tasks/backlog/2026-09-17-CRITICAL-GOVERNANCE-MAG-2-DISPATCH-AUTHORITY.md`
4. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-3-ROUTING.md`
5. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-4-DEPENDENCY-PARALLELIZATION.md`
6. `projects/wvu_knapsack/tasks/backlog/research/2026-09-17-GUARDRAILS-NAMESPACE-PROPOSAL.md`
7. `projects/wvu_knapsack/tasks/backlog/governance_revision/2026-09-17-ITEM-8-SESSION-GUIDANCE-TIGHTEN.md` (if it exists)
8. Any repository task-file convention or project guidance referenced by the artifacts above

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

**Human Approval Gates.** Every action falling into the categories below requires explicit human approval before execution, regardless of agent tier, provider identity, or capability: policy decisions, architecture decisions, priority changes, scope modifications, acceptance criteria adjustments, irreversible or hard-to-reverse actions, high blast radius operations (multi-service or cross-project impact), sensitive data or access operations, external communications, material task exceptions that deviate from the task file, novel or low-confidence decisions with no precedent in existing artifacts, and production configuration or deployment changes. Gates are determined by **action risk and authority boundary**, not by agent identity — an approved gate applies equally to local, free-web, and premium agents.

**Synthesis.** Multi-agent synthesis iteration is supported but optional for routine, bounded, low-risk work. Independent review (synthesis) is required when the task file, project guidance, a human decision gate, or the risk profile explicitly mandates it. Synthesis is a safety feature, not an overhead constraint on predictable low-risk tasks.

### MAG-3 — Capability- and Availability-Based Agent Routing (approved)

**Task-First Eligibility.** Routing analysis begins with task requirements and constraints identified in the selected task file — not provider identity, model tier, cost, or availability. A candidate lacking a necessary requirement or current availability is ineligible, regardless of provider, nominal model strength, price, free/local status, or general availability. Cost is a secondary comparison factor only among candidates that are both eligible and currently available. Fixed provider/model ladders (local-first, free-first, premium-first, provider-first, model-size-first) are explicitly rejected. Agents prepare eligibility analyses and routing recommendations; Tracy makes final selection and dispatches. Synthesis is optional for routine bounded low-risk work and conditionally required only when the task file, applicable project guidance, a human approval gate, or task risk profile requires it. Escalation is required when no eligible, available agent can safely and reliably meet requirements.

### MAG-4 — Blocking Dependency Management & Non-Blocking Parallelization (approved)

**Dependency Identification.** Before beginning or parallelizing work, the assigned agent identifies material dependencies — unavailable prerequisite artifacts, decisions, access, approvals, or verified upstream results needed to proceed safely. A task is blocking only when such a dependency is genuinely required to continue; other tasks with no such requirement may proceed in parallel.

**Non-Blocking Parallelization.** Independent, non-conflicting, bounded, low-risk work may proceed in parallel without waiting for an unrelated blocker. Each parallel workstream must have a bounded objective, clear ownership, defined inputs and outputs, and no overlapping write targets unless Tracy explicitly approves coordination. A blocker does not authorize stopping unrelated work, duplicating another agent's assigned work, speculating about missing inputs, or silently changing task scope.

**Escalation.** If a required dependency is unclear, missing, materially ambiguous, or conflicts with a governance rule, the agent must report the blocker and escalate to Tracy — rather than manufacturing an answer. Agents must not independently dispatch parallel agents. They may identify dependencies, propose safe parallelization, and recommend sequencing; Tracy selects and dispatches under MAG-2.

**Synthesis Proportionality.** Routine independent low-risk work does not automatically require multi-agent synthesis. Synthesis is required only when task guidance, project guidance, a MAG-2 human approval gate, or the risk profile explicitly mandates it.

**Consistency with MAG-1.** The selected task file is the execution contract; agents execute only within it and applicable governance rules. Dependency identification and parallelization proposals operate within that boundary.

**Consistency with MAG-3.** Task requirements and constraints identified in the execution contract are the basis for eligibility and routing recommendations prepared by agents under MAG-3.

---

## In Scope

Produce concise, direct, insertion-ready rule prose, targeted at 200–320 words, that:

- Establishes that agent preferences, provider/model preferences, session guidance, role labels, previous-chat context, informal handoffs, suggestions, and advisory project notes are guidance only unless incorporated into the selected task file or applicable governance rules.
- Clarifies that advisory guidance may help an agent interpret or plan authorized work but cannot grant authority, alter scope, change acceptance criteria, override required validation, bypass an approval gate, reassign ownership, change task lifecycle state, or direct an agent to stop/continue contrary to the selected active task file.
- States that when advisory sources conflict with a selected task file, applicable governance rules, an explicit human instruction, or a risk/authority gate, the higher-precedence controlling source governs.
- Prohibits agents from treating an advisory source as a reason to ignore a current task step, invent a lifecycle transition, continue past an explicit stop condition, or bypass escalation.
- Requires agents to stop and escalate to Tracy when two controlling sources genuinely conflict or the active task's next action is materially unclear — rather than choosing whichever chat message is newest.
- Preserves MAG-1 task-file authority: the selected task file is the execution contract; advisory sources operate within that boundary.
- Preserves MAG-2 human approval/dispatch authority: advisory notes do not constitute approval gates or dispatch authorization.
- Preserves MAG-3 task-first routing: task requirements and constraints are the basis for eligibility and routing recommendations; advisory preferences are secondary comparison factors only among eligible candidates.
- Preserves MAG-4 ownership/dependency boundaries: advisory context does not override bounded workstream ownership, dependency identification, or escalation requirements.
- Allows useful preferences and session guidance without requiring escalation merely because an advisory note exists and is non-conflicting.

---

## Out of Scope

Do not include:

- Edits to `rules/GUARDRAILS.md` or any other file
- Commits, staging, task moves, task-status changes, or agent dispatch
- Definition of the full precedence hierarchy (separate task)
- Durable-change mechanics or procedure
- Task-file content requirements beyond what MAG-1 states
- Routing/eligibility criteria (MAG-3 domain)
- Provider pricing policy or model inventory rules
- Dependency/parallelization rules (MAG-4 domain)
- Terminal commands, task-template edits, VS Code/Copilot/Ollama behavior, or session restart mechanics
- Specification of SESSION_GUIDANCE.md template structure (separate task)
- Speculation about later MAG rules or their identifiers/content

---

## Acceptance Criteria

1. The rule clearly categorizes agent preferences, provider/model preferences, session guidance, role labels, previous-chat context, informal handoffs, suggestions, and advisory project notes as guidance only unless incorporated into the selected task file or applicable governance rules.
2. It explicitly lists what advisory guidance cannot do: grant authority, alter scope, change acceptance criteria, override required validation, bypass an approval gate, reassign ownership, change task lifecycle state, or direct a stop/continue contrary to the active task file.
3. It states that when advisory sources conflict with a selected task file, governance rules, explicit human instruction, or risk/authority gate, the higher-precedence source governs.
4. It prohibits treating an advisory source as justification to ignore task steps, invent lifecycle transitions, continue past stop conditions, or bypass escalation.
5. It requires escalation to Tracy when two controlling sources genuinely conflict or the next action is materially unclear.
6. It preserves MAG-1 task-file authority without requiring escalation for non-conflicting guidance.
7. It preserves MAG-2 human approval/dispatch authority without redefining gates.
8. It preserves MAG-3 task-first routing, treating advisory preferences as secondary only among eligible candidates.
9. It preserves MAG-4 ownership/dependency boundaries against advisory override.
10. It is insertion-ready and uses terminology/style consistent with the live `rules/GUARDRAILS.md`.
11. It requires no repository changes.

---

## Negative Validation

Before reporting completion, check:

- Does any wording let preferences, chat, or advisory notes override a selected task file, governance rule, explicit human instruction, or approval gate? (If yes, revise.)
- Does it treat the newest message as automatically authoritative? (If yes, revise.)
- Does it treat advisory context as permission to dispatch, alter lifecycle state, edit files, broaden scope, or skip validation? (If yes, revise.)
- Does it require escalation whenever guidance is merely present and non-conflicting? (If yes, revise.)
- Does it define a full precedence hierarchy, restart protocol, or implementation procedure? (If yes, remove them.)
- Does it edit `rules/GUARDRAILS.md` as part of its own execution? (If yes, clarify that this is draft-only.)
- Is it inconsistent with MAG-1's task-contract authority? (If yes, revise.)
- Is it inconsistent with MAG-2's human-dispatch authority boundary? (If yes, revise.)
- Does it conflict with MAG-3's task-first eligibility principle? (If yes, revise.)
- Does it conflict with MAG-4's ownership/dependency boundaries? (If yes, revise.)

---

## Completion Report

Return exactly:

```markdown
## Draft MAG-5 Rule Prose

[Exact insertion-ready Markdown headed `### MAG-5 — Agent Preferences as Guidance, Not Rules`]

## Drafting Rationale

[2–3 concise sentences explaining the guidance-vs-authority distinction and its consistency with MAG-1 through MAG-4.]

## Consistency Check

[Confirm alignment with MAG-1 through MAG-4. List required artifacts read, artifacts unavailable, and any precise conflict with repository artifacts.]

## Ready for Review?

[YES or NO. If NO, state the specific blocker.]

## No-Change Confirmation

[Confirm no file edits, task moves/status changes, commits, or agent dispatches occurred.]
```

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are [ASSIGNED_ROLE].

Project: agent-tasks
Task: /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-5-PREFERENCES-GUIDANCE-NOT-RULES.md

READ FIRST: Task file contains all acceptance criteria, scope boundaries, and validation requirements.

CRITICAL: The output is draft governance rule prose (200–320 words) insertion-ready for GUARDRAILS.md.
No repository edits, no commits, no procedural implementation occurs until MAG-5 text is separately approved.
```

---

## Notes

- **Draft-only scope**: This task authorizes creation of proposed, insertion-ready MAG-5 rule prose only. No file edits to `rules/GUARDRAILS.md` or any other repository file are authorized by this task.
- **Governance chain**: MAG-5 is the fifth governance rule in the Multi-Agent Task Governance namespace and addresses the operational question of how advisory sources (preferences, session guidance, previous context, informal notes) interact with the controlling authority established by MAG-1 through MAG-4. It fills the gap between task-contract authority and the practical reality that agents receive multiple information sources during a session.
- **Guidance is not override**: The distinction between useful advisory guidance and controlling authority is essential — allowing agents to use preferences for interpretation without letting those preferences become de facto rules preserves both efficiency and governance integrity.
