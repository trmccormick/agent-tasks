---
status: backlog
type: governance
---

> **Historical status:** This is a draft-only governance specification retained for historical reference. Its corresponding MAG rule is authoritative in `rules/GUARDRAILS.md`. This draft was not dispatched as an implementation task and must not be used as an active execution contract.

# MAG-3 — Capability- and Availability-Based Agent Routing

## Objective

Draft an insertion-ready governance rule headed exactly:

```markdown
### MAG-3 — Capability- and Availability-Based Agent Routing
```

The rule must define a task-driven method for preparing agent-routing recommendations. It must preserve human selection and dispatch authority under approved MAG-2 policy.

This is a draft-only task. It does not authorize implementation in `rules/GUARDRAILS.md`.

## Required Reading

Read these artifacts in full before drafting:

1. `rules/GUARDRAILS.md`
2. `DECISIONS.md`, if it exists
3. The approved MAG-2 wording supplied below
4. `2026-09-17-GUARDRAILS-NAMESPACE-PROPOSAL.md`, if it exists
5. `MODEL_SYSTEM_SELECTION_GUIDE.md`, if it exists
6. Any repository task-file convention or project guidance referenced by the artifacts above

If a named artifact does not exist, state that fact in the completion report; do not invent or substitute its contents.

## Approved MAG-2 Context

Treat the following as approved policy context. Do not claim it has already been inserted into `rules/GUARDRAILS.md` unless the live file proves that it has.

```markdown
### MAG-2 — Human-Controlled Dispatch and Synthesis

**Authority Chain.** Planning/orchestrating agents may inspect task and repository state, synthesize evidence from those artifacts, identify blockers or risks, and prepare a routing recommendation. They **may not** autonomously select an agent, dispatch work, re-dispatch, broaden scope, approve decisions, or act as their own reviewer on authorized work. The human (Tracy) selects the task, approves material decisions and risk-gated actions, and dispatches — agents recommend; Tracy dispatches. Assigned agents execute only the lifecycle and scope explicitly authorized in the selected task file and applicable governance rules. Reviewers verify work quality, recommend revisions, and escalate blockers; they do not make policy, priority, architecture, or acceptance-criteria decisions.

**Human Approval Gates.** Every action falling into the categories below requires explicit human approval before execution, regardless of agent tier, provider identity, or capability: policy decisions, architecture decisions, priority changes, scope modifications, acceptance criteria adjustments, irreversible or hard-to-reverse actions, high blast radius operations (multi-service or cross-project impact), sensitive data or access operations, external communications, material task exceptions that deviate from the task file, novel or low-confidence decisions with no precedent in existing artifacts, and production configuration or deployment changes. Gates are determined by **action risk and authority boundary**, not by agent identity — an approved gate applies equally to local, free-web, and premium agents.

**Synthesis.** Multi-agent synthesis iteration is supported but optional for routine, bounded, low-risk work. Independent review (synthesis) is required when the task file, project guidance, a human decision gate, or the risk profile explicitly mandates it. Synthesis is a safety feature, not an overhead constraint on predictable low-risk tasks.
```

## In Scope

Produce concise, direct, insertion-ready rule prose, targeted at 200–320 words, that:

- Begins routing analysis with task requirements—not provider identity, model tier, cost, or availability
- Evaluates required scope, repository and tool access, task-relevant capabilities, adequate context capacity, authority limits, verification requirements, task risk profile, and current availability
- States that a candidate lacking a necessary requirement or current availability is ineligible, regardless of provider, nominal model strength, price, free/local status, or general availability
- Treats cost as a secondary comparison factor only after identifying candidates that are both eligible and currently available
- May include expected reliability and proportionate verification burden in comparison among eligible, available candidates
- Explicitly rejects fixed provider/model ladders, including local-first, free-first, premium-first, provider-first, and model-size-first routing
- Preserves MAG-2: agents prepare eligibility analyses and routing recommendations; Tracy makes final selection and dispatches
- Makes synthesis optional for routine, bounded, low-risk work and conditionally required only when the task file, applicable project guidance, a human approval gate, or task risk profile requires it
- Requires escalation when no eligible, available agent can safely and reliably meet requirements; it must not permit lowering requirements, inventing authority, broadening scope, or selecting an ineligible agent

## Out of Scope

Do not include:

- Edits to `rules/GUARDRAILS.md` or any other file
- Commits, staging, task moves, task-status changes, or agent dispatch
- Provider names, current model inventory, pricing figures, or pricing formulas
- Live availability-check mechanics or session-specific routing procedures
- Command syntax, implementation plans, or tool-configuration instructions
- Project/session routing tables
- Task-file-convention design, acceptance-criteria design, or task execution-contract policy
- Speculation about later MAG rules or their identifiers/content

## Acceptance Criteria

1. The rule begins with task requirements and applies an eligibility screen before comparing cost.
2. It explicitly covers repository/tool access, task capability, context sufficiency, authority constraints, verification needs, risk profile, and availability.
3. It treats current availability as an eligibility constraint, not only a preference signal.
4. It makes cost secondary to task fit, required access, authority, verification, and availability.
5. It rejects fixed provider/model/default-tier routing ladders.
6. It preserves Tracy’s final selection and dispatch authority under MAG-2.
7. It does not require premium models or multi-agent synthesis for ordinary routine work.
8. It distinguishes capability eligibility, technical authorization, and human approval rather than treating them as interchangeable.
9. It is insertion-ready and uses terminology/style consistent with the live `rules/GUARDRAILS.md`.
10. It requires no repository changes.

## Negative Validation

Before reporting completion, check:

- Does any wording make provider, model tier, cost, free/local status, or availability the first routing filter? If yes, revise.
- Could an unavailable or otherwise unqualified agent be selected because it is cheaper, free, local, or nominally stronger? If yes, revise.
- Does the text imply a provider/model hierarchy or automatic default? If yes, revise.
- Does it give agents final selection, approval, or dispatch authority? If yes, revise.
- Does it make routine routing a mandatory human gate when MAG-2 does not require one? If yes, revise.
- Does it require premium models or synthesis for ordinary low-risk work? If yes, revise.
- Does it conflate technical access/authorization, task capability, and human approval? If yes, revise.
- Does it include implementation mechanics or unapproved future-policy assumptions? If yes, remove them.

## Completion Report

Return exactly:

```markdown
## Draft MAG-3 Rule Prose

[Exact insertion-ready Markdown headed `### MAG-3 — Capability- and Availability-Based Agent Routing`]

## Drafting Rationale

[2–3 concise sentences explaining task-first eligibility, availability as a hard eligibility condition, and cost as a secondary comparison factor.]

## Consistency Check

[Confirm alignment with MAG-2’s human-authority boundary. List required artifacts read, artifacts unavailable, and any precise conflict with repository artifacts.]

## Ready for Review?

[YES or NO. If NO, state the specific blocker.]

## No-Change Confirmation

[Confirm no file edits, task moves/status changes, commits, or agent dispatches occurred.]
```