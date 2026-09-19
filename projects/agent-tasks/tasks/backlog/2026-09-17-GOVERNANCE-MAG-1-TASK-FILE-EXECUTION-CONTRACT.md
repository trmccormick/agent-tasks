---
status: backlog
priority: CRITICAL
type: governance
system_domain: AGENT_ORCHESTRATION
mvp_alignment: PHASE_1_GOVERNANCE
local_worker_safe: true
cross_project_impact: true
---

# TASK: MAG-1 — Task File as Execution Contract

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
### MAG-1 — Task File as Execution Contract
```

The rule must establish the selected task file as the task-specific execution contract at the start of every implementation session, identify what the task file specifies (objective, scope, artifacts, constraints, authorized/prohibited actions, acceptance criteria, validation requirements, completion-report/handoff obligations, and any applicable human approval gates), state that agents execute only within the selected task file and applicable governance rules, and prohibit agents from inferring missing authority/access/acceptance criteria/scope, broadening scope, replacing acceptance criteria, or treating chat context, informal handoffs, provider/model preference, or another agent's suggestion as authority that overrides the selected task file or applicable governance rules. MAG-1 must remain consistent with MAG-2 (agents recommend and execute within explicit authorization; Tracy selects, approves material/risk-gated decisions, and dispatches) and MAG-3 (task requirements and constraints are the basis for eligibility and routing recommendations).

This is a draft-only task. It does not authorize implementation in `rules/GUARDRAILS.md`.

---

## Required Reading

Read these artifacts in full before drafting:

1. `rules/GUARDRAILS.md`
2. `projects/agent-tasks/tasks/backlog/2026-09-17-CRITICAL-GOVERNANCE-MAG-2-DISPATCH-AUTHORITY.md`
3. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-3-ROUTING.md`
4. `projects/wvu_knapsack/tasks/backlog/research/2026-09-17-GUARDRAILS-NAMESPACE-PROPOSAL.md`
5. Any repository task-file convention or project guidance referenced by the artifacts above

If a named artifact does not exist, state that fact in the completion report; do not invent or substitute its contents.

---

## Approved Context

Treat the following as approved policy context. Do not claim it has already been inserted into `rules/GUARDRAILS.md` unless the live file proves that it has.

### MAG-2 — Human-Controlled Dispatch and Synthesis (approved)

**Authority Chain.** Planning/orchestrating agents may inspect task and repository state, synthesize evidence from those artifacts, identify blockers or risks, and prepare a routing recommendation. They **may not** autonomously select an agent, dispatch work, re-dispatch, broaden scope, approve decisions, or act as their own reviewer on authorized work. The human (Tracy) selects the task, approves material decisions and risk-gated actions, and dispatches — agents recommend; Tracy dispatches. Assigned agents execute only the lifecycle and scope explicitly authorized in the selected task file and applicable governance rules. Reviewers verify work quality, recommend revisions, and escalate blockers; they do not make policy, priority, architecture, or acceptance-criteria decisions.

**Human Approval Gates.** Every action falling into the categories below requires explicit human approval before execution, regardless of agent tier, provider identity, or capability: policy decisions, architecture decisions, priority changes, scope modifications, acceptance criteria adjustments, irreversible or hard-to-reverse actions, high blast radius operations (multi-service or cross-project impact), sensitive data or access operations, external communications, material task exceptions that deviate from the task file, novel or low-confidence decisions with no precedent in existing artifacts, and production configuration or deployment changes. Gates are determined by **action risk and authority boundary**, not by agent identity — an approved gate applies equally to local, free-web, and premium agents.

**Synthesis.** Multi-agent synthesis iteration is supported but optional for routine, bounded, low-risk work. Independent review (synthesis) is required when the task file, project guidance, a human decision gate, or the risk profile explicitly mandates it. Synthesis is a safety feature, not an overhead constraint on predictable low-risk tasks.

---

## In Scope

Produce concise, direct, insertion-ready rule prose, targeted at 200–320 words, that:

- Establishes that before execution begins, the selected task file is the task-specific execution contract and the assigned agent reads it in full.
- Requires the task file to identify: objective, scope, required artifacts, constraints, authorized actions, prohibited actions, acceptance criteria, validation requirements, completion-report/handoff requirements, and any applicable human approval gates.
- States that agents execute only within the selected task file and applicable governance rules.
- Prohibits agents from inferring missing authority, access, acceptance criteria, or task scope; broadening scope; replacing acceptance criteria; or treating chat context, informal handoffs, provider/model preference, or another agent's suggestion as authority that overrides the selected task file or applicable governance rules.
- Requires agents to stop and escalate to Tracy if a task file is materially incomplete or ambiguous, conflicts with applicable governance rules, lacks required access/authority/acceptance criteria, or cannot be completed safely within its stated bounds — rather than improvising.
- Allows routine, bounded execution decisions within an explicitly authorized task without imposing a human approval gate for every ordinary action (consistent with MAG-2 synthesis guidance).
- Remains consistent with MAG-2: agents recommend and execute within explicit authorization; Tracy selects the task, approves material/risk-gated decisions, and dispatches.
- Remains consistent with MAG-3: task requirements and constraints are the basis for eligibility and routing recommendations.

---

## Out of Scope

Do not include:

- Edits to `rules/GUARDRAILS.md` or any other file
- Commits, staging, task moves, task-status changes, or agent dispatch
- Provider/model routing, provider pricing, or agent inventory
- Dependency/parallelization rules (MAG-4 domain)
- Precedence hierarchy details or durable-change procedure (separate task)
- Session-management mechanics or project-specific routing tables
- Task-file-convention design or acceptance-criteria design beyond what this rule states
- Speculation about later MAG rules or their identifiers/content

---

## Acceptance Criteria

1. The rule establishes the selected task file as the execution contract at session start, before any implementation work begins.
2. It explicitly enumerates what the task file identifies: objective, scope, required artifacts, constraints, authorized/prohibited actions, acceptance criteria, validation requirements, completion-report/handoff obligations, and applicable human approval gates.
3. It states that agents execute only within the selected task file and applicable governance rules.
4. It prohibits inferring missing authority, access, acceptance criteria, or scope; broadening scope; replacing acceptance criteria; or treating chat context, informal handoffs, provider/model preference, or another agent's suggestion as overriding authority.
5. It requires agents to stop and escalate to Tracy when the task file is materially incomplete/ambiguous, conflicts with governance rules, lacks required access/authority/criteria, or cannot be completed safely within its bounds.
6. It allows routine bounded execution decisions without a human approval gate for every ordinary action (consistent with MAG-2).
7. It is explicitly consistent with MAG-2 (Tracy's selection/dispatch/approval authority; agents execute within explicit authorization).
8. It is explicitly consistent with MAG-3 (task requirements as the basis for eligibility and routing recommendations).
9. It is insertion-ready and uses terminology/style consistent with the live `rules/GUARDRAILS.md`.
10. It requires no repository changes.

---

## Negative Validation

Before reporting completion, check:

- Could a reasonable reader infer that chat instructions alone authorize execution when a selected task file exists? (If yes, revise.)
- Does the text imply that a task file can override applicable governance rules? (If yes, revise.)
- Could an agent infer missing permissions, access, criteria, or scope from context rather than stopping and escalating? (If yes, revise.)
- Does it silently resolve material ambiguity or a governance conflict instead of requiring escalation? (If yes, revise.)
- Does it require a human approval gate for routine bounded work absent a material/risk-based gate trigger? (If yes, revise.)
- Could the text be interpreted as editing `rules/GUARDRAILS.md` or any other file as part of its own execution? (If yes, clarify that this is draft-only.)
- Is it inconsistent with MAG-2's human-dispatch authority boundary? (If yes, revise.)
- Does it conflict with MAG-3's task-first eligibility principle? (If yes, revise.)

---

## Completion Report

Return exactly:

```markdown
## Draft MAG-1 Rule Prose

[Exact insertion-ready Markdown headed `### MAG-1 — Task File as Execution Contract`]

## Drafting Rationale

[2–3 concise sentences explaining the task file as execution contract principle and its consistency with MAG-2 and MAG-3.]

## Consistency Check

[Confirm alignment with MAG-2's human-authority boundary and MAG-3's task-first eligibility principle. List required artifacts read, artifacts unavailable, and any precise conflict with repository artifacts.]

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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-1-TASK-FILE-EXECUTION-CONTRACT.md

READ FIRST: Task file contains all acceptance criteria, scope boundaries, and validation requirements.

CRITICAL: The output is draft governance rule prose (200–320 words) insertion-ready for GUARDRAILS.md.
No repository edits, no commits, no procedural implementation occurs until MAG-1 text is separately approved.
```

---

## Notes

- **Draft-only scope**: This task authorizes creation of proposed, insertion-ready MAG-1 rule prose only. No file edits to `rules/GUARDRAILS.md` or any other repository file are authorized by this task.
- **Governance chain**: MAG-1 is the numerically-first governance rule in the Multi-Agent Task Governance namespace and establishes the foundational principle that the selected task file — not chat, inference, or informal communication — is the execution contract for every implementation session.
- **Authority boundaries**: MAG-1 must reinforce, not replace or contradict, MAG-2's human-dispatch authority and MAG-3's task-first eligibility principle. It operates at the task-file level; routing decisions remain under MAG-2 + MAG-3.
