---
status: backlog
priority: CRITICAL
type: governance
system_domain: AGENT_ORCHESTRATION
mvp_alignment: PHASE_1_GOVERNANCE
local_worker_safe: true
cross_project_impact: true
---

> **Historical status:** This is a draft-only governance specification retained for historical reference. Its corresponding MAG rule is authoritative in `rules/GUARDRAILS.md`. This draft was not dispatched as an implementation task and must not be used as an active execution contract.

# TASK: MAG-4 — Blocking Dependency Management & Non-Blocking Parallelization

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
### MAG-4 — Blocking Dependency Management & Non-Blocking Parallelization
```

The rule must establish principles for identifying material dependencies before beginning or parallelizing work, distinguishing genuine blocking dependencies from unrelated or optional work, permitting independent non-conflicting bounded low-risk work to proceed in parallel when it does not require the blocked dependency, and requiring escalation when required dependencies are unclear, missing, materially ambiguous, or conflict with a governance rule. MAG-4 must remain consistent with MAG-1 (the selected task file is the execution contract; agents execute only within it and applicable governance rules), MAG-2 (agents recommend and execute within explicit authorization; Tracy selects tasks, approves material/risk-gated decisions, and dispatches — agents may not independently dispatch parallel agents), and MAG-3 (task requirements and constraints identified in the execution contract are the basis for eligibility and routing recommendations).

This is a draft-only task. It does not authorize implementation in `rules/GUARDRAILS.md`.

---

## Required Reading

Read these artifacts in full before drafting:

1. `rules/GUARDRAILS.md`
2. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-1-TASK-FILE-EXECUTION-CONTRACT.md`
3. `projects/agent-tasks/tasks/backlog/2026-09-17-CRITICAL-GOVERNANCE-MAG-2-DISPATCH-AUTHORITY.md`
4. `projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-3-ROUTING.md`
5. `projects/wvu_knapsack/tasks/backlog/research/2026-09-17-GUARDRAILS-NAMESPACE-PROPOSAL.md`
6. Any repository task-file convention or project guidance referenced by the artifacts above

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

---

## In Scope

Produce concise, direct, insertion-ready rule prose, targeted at 200–320 words, that:

- Requires agents to identify material dependencies before beginning or parallelizing work.
- Defines a task as blocking only when it needs an unavailable prerequisite artifact, decision, access, approval, or verified upstream result to proceed safely.
- Permits independent, non-conflicting, bounded, low-risk work to proceed in parallel when it does not require the blocked dependency.
- Prohibits a blocker from becoming an excuse to stop unrelated work, duplicate another agent's assigned work, speculate about missing inputs, or silently change task scope.
- Requires each parallel workstream to have: bounded objective, clear ownership, defined inputs/outputs, and no overlapping write targets unless Tracy explicitly approves coordination.
- Prohibits agents from independently dispatching parallel agents; agents may identify dependencies, propose safe parallelization, and recommend sequencing — Tracy selects and dispatches under MAG-2.
- Requires agents to report blockers and escalate to Tracy when a required dependency is unclear, missing, materially ambiguous, or conflicts with a governance rule — rather than manufacturing an answer.
- States that synthesis/review remains proportionate: routine independent low-risk work does not automatically require multi-agent synthesis; it is required only when task guidance, project guidance, a MAG-2 gate, or risk requires it.
- Remains consistent with MAG-1: the selected task file is the execution contract; agents execute only within it and applicable governance rules.
- Remains consistent with MAG-3: task requirements and constraints identified in the execution contract are the basis for eligibility and routing recommendations.

---

## Out of Scope

Do not include:

- Edits to `rules/GUARDRAILS.md` or any other file
- Commits, staging, task moves, task-status changes, or agent dispatch
- Provider/model routing, provider pricing, or agent inventory
- Technical concurrency implementation details (Git branching mechanics, PR workflows, merge strategies)
- Task-file authority rules beyond what MAG-1 states
- Precedence hierarchy details or durable-change procedure (separate task)
- Session-management mechanics or project-specific routing tables
- Dependency/parallelization implementation tactics (branch naming conventions, merge conflict resolution procedures)
- Speculation about later MAG rules or their identifiers/content

---

## Acceptance Criteria

1. The rule distinguishes genuine blocking dependencies from unrelated or optional work.
2. It permits safe, bounded parallel work only when input/output and write-target conflicts are controlled (no overlapping write targets unless Tracy explicitly approves coordination).
3. It prohibits autonomous agent dispatch, duplicate ownership of work, and silent scope expansion by blockers.
4. It requires escalation to Tracy for unresolved material dependencies (unclear, missing, ambiguous, or governance-conflicting).
5. It preserves MAG-1 task-contract authority: agents execute only within the selected task file and applicable governance rules.
6. It preserves MAG-2 human dispatch authority: agents may not independently dispatch parallel agents; Tracy selects and dispatches.
7. It preserves MAG-3 capability/availability routing: task requirements and constraints are the basis for eligibility and routing recommendations.
8. It does not force premium agents, multi-agent synthesis, or parallel execution for ordinary low-risk work.
9. It is insertion-ready and uses terminology/style consistent with the live `rules/GUARDRAILS.md`.
10. It requires no repository changes.

---

## Negative Validation

Before reporting completion, check:

- Does any wording make all work wait on any blocker, regardless of independence or risk profile? (If yes, revise.)
- Does it allow an agent to dispatch or self-assign parallel agents? (If yes, revise.)
- Does it treat parallelization as safe despite shared write targets or unresolved dependency conflicts? (If yes, revise.)
- Does it permit copying, guessing, or inventing a missing prerequisite? (If yes, revise.)
- Does it give a task file authority to override applicable governance rules? (If yes, revise.)
- Does it turn routine independent low-risk work into mandatory multi-agent synthesis? (If yes, revise.)
- Does it include provider/model routing, pricing, Git-workflow mechanics, or implementation details? (If yes, remove them.)
- Is it inconsistent with MAG-1's task-contract authority? (If yes, revise.)
- Is it inconsistent with MAG-2's human-dispatch authority boundary? (If yes, revise.)
- Does it conflict with MAG-3's task-first eligibility principle? (If yes, revise.)

---

## Completion Report

Return exactly:

```markdown
## Draft MAG-4 Rule Prose

[Exact insertion-ready Markdown headed `### MAG-4 — Blocking Dependency Management & Non-Blocking Parallelization`]

## Drafting Rationale

[2–3 concise sentences explaining the dependency identification, blocking vs. non-blocking distinction, and safe parallelization principles, and their consistency with MAG-1, MAG-2, and MAG-3.]

## Consistency Check

[Confirm alignment with MAG-1's task-contract authority, MAG-2's human-dispatch boundary, and MAG-3's task-first eligibility principle. List required artifacts read, artifacts unavailable, and any precise conflict with repository artifacts.]

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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks/backlog/2026-09-17-GOVERNANCE-MAG-4-DEPENDENCY-PARALLELIZATION.md

READ FIRST: Task file contains all acceptance criteria, scope boundaries, and validation requirements.

CRITICAL: The output is draft governance rule prose (200–320 words) insertion-ready for GUARDRAILS.md.
No repository edits, no commits, no procedural implementation occurs until MAG-4 text is separately approved.
```

---

## Notes

- **Draft-only scope**: This task authorizes creation of proposed, insertion-ready MAG-4 rule prose only. No file edits to `rules/GUARDRAILS.md` or any other repository file are authorized by this task.
- **Governance chain**: MAG-4 is the fourth governance rule in the Multi-Agent Task Governance namespace and addresses the operational question of how agents should manage dependencies and parallelize work safely within the authorization boundaries established by MAG-1 (task contract) and MAG-2 (human dispatch).
- **Parallelization ≠ permission**: Identifying safe parallel work is an informational/escalation activity, not a self-authorizing one. Agents propose; Tracy decides.
