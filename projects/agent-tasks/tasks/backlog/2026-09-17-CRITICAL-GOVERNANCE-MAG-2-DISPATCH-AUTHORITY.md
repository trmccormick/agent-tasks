---
status: backlog
priority: CRITICAL
type: governance
system_domain: AGENT_ORCHESTRATION
mvp_alignment: PHASE_1_GOVERNANCE
local_worker_safe: true
cross_project_impact: true
---

# TASK: MAG-2 — Human-Controlled Dispatch and Synthesis Authority

**Status**: Backlog (draft for Tracy review; no Qwen dispatch yet)  
**Priority**: CRITICAL  
**Type**: Governance  
**Repository**: agent-tasks (universal governance, not project-specific)  
**Target**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (new MAG section, post-Rule 30)  
**Created**: 2026-09-17

⚠️ **CROSS-PROJECT IMPACT**: This rule applies to all 12+ projects and all agent tiers (local, free-web, premium). Edits affect authorization boundaries across every dispatch and synthesis workflow.

---

## Objective

Author the exact governance text for `MAG-2 — Human-Controlled Dispatch and Synthesis Authority` to be inserted into the approved "Multi-Agent Task Governance" section of `GUARDRAILS.md`. Define human authority over dispatch, agent role boundaries, reviewer scope, and synthesis requirements without including routing logic, precedence, preflight, templates, SESSION_GUIDANCE, or repository procedures.

---

## Scope

**In scope:**
- Authority chain: planning/orchestrating agents (inspect, synthesize, recommend); Tracy (select, approve, dispatch); assigned agent (execute authorized work); reviewers (verify, recommend escalation)
- Explicit agent role boundaries:
  - Planning/orchestrating agents **may**: inspect task/repo state, synthesize evidence, identify blockers, prepare routing recommendation
  - Planning/orchestrating agents **may not**: autonomously select agent, dispatch, re-dispatch, broaden scope, or approve decisions
  - Assigned agents: execute only lifecycle/work authorized in the selected task and applicable governance rules
  - Reviewers: verify work, recommend revisions, escalate blockers; they do not make policy, priority, or architecture decisions
- Human approval gates: policy, architecture, priority, scope, acceptance criteria, irreversible/hard-to-reverse actions, high blast radius, sensitive data/access, external communications, material task exceptions, novel/low-confidence decisions, production configuration/deployment changes
- Gates determined by action risk and authority boundary, not agent/provider identity
- Iterative multi-agent synthesis: supported but optional for bounded, low-risk work; required when task, project guidance, human decision gate, or risk profile explicitly requires independent review

**Out of scope:**
- Routing capability filtering or availability evaluation (MAG-3)
- Precedence hierarchy or conflict resolution (separate task)
- Preflight validation procedures (separate task)
- Dispatch templates, SESSION_GUIDANCE.md, or project-level implementation
- Agent dispatch mechanics, provider-specific tooling, or command syntax
- Repository changes, commits, or file operations
- Historical task activation or exception handling (separate tasks)

---

## Required Output Format

Write governance rule text as insertion-ready for `GUARDRAILS.md`:

```
### MAG-2 — Human-Controlled Dispatch and Synthesis

[Exact prose to appear in GUARDRAILS.md, 200–300 words]
```

Characteristics:
- Present tense, imperative where appropriate
- Explicit role boundaries (planning agents, Tracy, assigned agents, reviewers)
- Risk-based approval triggers, not provider-based
- Synthesis as optional for routine work, required for specified cases
- Direct insertion ready; avoid cross-references to MAG-3, MAG-4, etc. unless necessary
- Clear statement: agents recommend; Tracy dispatches; assigned agents execute

---

## Acceptance Criteria

1. ✅ Human dispatch authority is unambiguous (agents cannot autonomously dispatch)
2. ✅ Tracy's role as approval authority is explicit
3. ✅ Agent role boundaries are stated (planning agents cannot dispatch; assigned agents only execute authorized work; reviewers do not make decisions)
4. ✅ Approval gate triggers are risk-based, not agent-identity-based
5. ✅ Synthesis iteration is supported but not mandatory for low-risk bounded work
6. ✅ No routing, precedence, preflight, template, or implementation content
7. ✅ Text is ready for direct insertion into GUARDRAILS.md

---

## Negative Validation (Critical)

**After drafting, verify:**
- Could a reasonable reader infer that a planning or review agent may autonomously dispatch, approve a material decision, or broaden scope? (If yes, revise.)
- Is it clear that "synthesis iteration" is optional for routine work, not a required gate on every task?
- Would a new agent or reviewer reading this understand they cannot make final policy/architecture decisions?

---

## Inputs & Context

- **MAG namespace/placement decision**: Approved (MAG-1–6 in new "Multi-Agent Task Governance" section after Rule 30; existing Rules 0–30 untouched)
- **Perplexity research**: "Agent recommends, human selects and dispatches" pattern (external validation)
- **User's Item 2 revision request**: Explicit Tracy dispatch authority
- **Existing GUARDRAILS.md**: Rules 0–30 remain unchanged; MAG section does not yet exist in repository

---

## Post-Approval Process

**Not part of this task** (separate workflow):
- Create/commit MAG section to GUARDRAILS.md
- Dispatch Policy Cluster B (MAG-3 routing logic)
- Dispatch Policy Cluster C (precedence + durable-change rule)

Return exact draft MAG-2 rule text for Tracy review. No `GUARDRAILS.md` change, no Qwen dispatch, no repository commit occurs until exact MAG-2 text is separately approved and an authorized implementation task is selected.

---

## Notes

- **No agent dispatch mechanics**: This rule defines authority boundaries, not provider-specific routing code, syntax, or tool integration.
- **Synthesis as feature, not constraint**: Keep synthesis optional to avoid over-constraining routine low-risk work.
- **Authority over identity**: Gates apply equally regardless of agent tier, provider, or capability; identity does not bypass approval requirements.

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are [ASSIGNED_ROLE].

Project: agent-tasks
Task: /Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks/backlog/2026-09-17-CRITICAL-GOVERNANCE-MAG-2-DISPATCH-AUTHORITY.md

READ FIRST: Task file contains all acceptance criteria, scope boundaries, and validation requirements.

CRITICAL: The output is governance rule text (200–300 words) insertion-ready for GUARDRAILS.md. 
No repository edits, no commits, no procedural implementation occurs until MAG-2 text is separately approved.
```
