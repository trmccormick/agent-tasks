# agent-tasks: Intent and Repository Review Guidance

Prepared: October 5, 2026
Audience: Claude acting as a repository reviewer
Purpose: Explain the intent of the prior coordination work and guide an evidence-based review of the current repository.

## Direct instruction to Claude

Review the agent-tasks repository to determine whether its current documentation, templates, and task workflow consistently implement the intent below. This is a read-only review, not authorization to implement changes, dispatch agents, move tasks, or commit anything.

Start with the repository's own README and applicable governance documents. Discover the actual file layout; do not assume the historical paths or document names below still exist. Inspect relevant templates and representative project task artifacts, including embedded handoffs, status tracking, and any session guidance. Report what is implemented, what remains proposed, and any concrete contradictions.

## Why this work exists

agent-tasks is the shared coordination and task-management repository across multiple software projects. The goal is a lightweight, durable workflow that makes planning sessions smoother, prevents duplicate or conflicting work, and routes bounded tasks to suitable available agents while preserving human authority.

This is not an effort to build an autonomous dispatcher, impose a rigid model hierarchy, or replace existing project conventions with a large new framework. The intended approach is to understand existing behavior first, then recommend the smallest justified documentation or template correction.

## Intended operating principles

### 1. Task files are execution contracts

The selected task file and its embedded handoff are the authoritative task-scoped execution contract. They should contain enough information to establish scope, constraints, dependencies, acceptance criteria, verification expectations, and completion requirements without relying on a parallel chat brief.

Dispatch messages should normally identify the role, project, task artifact, and any necessary current-context modifier. They should not restate the entire task or create a competing source of instructions. Material scope changes or newly approved requirements should be written into the appropriate durable artifact before substantive implementation.

This does not mean a task file overrides universal governance, explicit human instructions, or approval gates. Review the actual precedence language for consistency rather than interpreting “execution contract” as unlimited authority.

### 2. Humans control dispatch and material decisions

Planning and coordination agents inspect, synthesize, prepare, and recommend. The human decides whether and when to dispatch a target task and resolves material approval questions.

An approved task can authorize ordinary bounded execution without repeated approval for every routine step. Do not confuse human dispatch authority with requiring the human to perform task-file moves, status updates, or other agent-owned lifecycle actions. The executing agent performs the lifecycle actions authorized by the actual workflow and task contract.

### 3. Roles are not permanently bound to models

Planning, implementation, review, and session strategy are roles. Model selection depends on current availability, capability, task complexity, risk, cost, and verification needs.

Local Qwen, Claude, Haiku, and other agents have been used in these roles, but their historical use is not a universal ownership rule. Higher-cost reasoning should be reserved where it adds value; escalation should be available when a local agent is struggling or a task requires stronger review.

### 4. Handoffs preserve continuity

A session handoff summarizes completed work, decisions, verification evidence, unresolved items, and recommended next steps. It is readable by any subsequent agent or human, not exclusively by Claude or Qwen.

Task-embedded handoffs and session-completion handoffs serve different purposes. The first guides task execution; the second preserves session outcomes and context. Status tracking provides the recorded project state. Review how the repository distinguishes these artifacts and avoids unnecessary duplication.

### 5. Blocked work does not authorize bypassing gates

When a priority track is blocked by a dependency or architecture decision, identify an independent, ready task if one exists. Do not force a decision, expand scope, or infer approval simply to keep an implementation agent busy.

Readiness depends on the task contract, dependencies, repository state, verification requirements, and conflict status—not its priority label alone.

### 6. Preflight should be proportional

Agents should inspect the local files relevant to their role and task, verify that task assumptions match current repository state, and report consequential drift or ambiguity before proceeding.

The goal is sufficient evidence, not mandatory rereading of every document on every task. A planner choosing the next task needs backlog and project-state context; an executor working a prepared task needs its contract and the applicable supporting evidence.

### 7. Project guidance is optional and subordinate

The proposed MAG-6 approach used SESSION_GUIDANCE.md as optional, project-scoped, advisory guidance. It must not become an independent dispatch authority or override task contracts, governing rules, explicit human instructions, or approval gates.

Verify the repository's current rule before treating this historical proposal as installed policy. Lifecycle mechanics and restart procedures should not be silently folded into this rule if they belong in separate policy work.

## Historical checkpoints, not current-state claims

The following recollections explain prior intent. They must be checked against repository evidence.

- September 17, 2026: MAG-1, “Task File as Execution Contract,” had finalized draft wording. A routing reference was corrected from MAG-2 to MAG-3. Finalizing the wording did not establish that it had been inserted into governance files.
- September 17, 2026: MAG-2 concerned the human-controlled dispatch and synthesis authority boundary; MAG-3 concerned eligibility and capability-based routing. Check exact current titles and text locally.
- September 17, 2026: The next identified policy gap after MAG-1 was MAG-4, “Blocking Dependency Management & Non-Blocking Parallelization.” This recollection does not establish its final implementation status.
- September 18, 2026: MAG-6 addressed per-project guidance through SESSION_GUIDANCE.md. A narrowly scoped task-artifact correction was reported complete. Staging, committing, and implementation were separate authorization questions.
- The prior process deliberately separated proposal, draft review, approved wording, task-artifact edits, governance insertion, and rollout. Do not collapse these stages into “completed.”

No complete MAG-5 text or later rollout status is supplied by this brief. Obtain those from the repository rather than reconstructing them from assumptions.

## Review questions

1. Where are the controlling governance rules, and what precedence do they establish?
2. Which MAG rules are actually installed, and which exist only as draft or backlog artifacts?
3. Do task templates make the execution contract self-contained enough for bounded work?
4. Do dispatch templates remain minimal without omitting necessary approval or scope information?
5. Is human dispatch authority clearly distinguished from agent-owned lifecycle actions?
6. Are routing rules capability-based, or do documents unnecessarily lock roles to named models?
7. Do handoff and status conventions preserve evidence and continuity without duplicating full logs?
8. Do preflight rules detect consequential state drift without imposing disproportionate ceremony?
9. Can independent ready work advance while blocked work remains gated?
10. Is optional project guidance clearly subordinate and free of unintended authority?
11. Do README instructions, governance rules, templates, and actual task examples agree?
12. Are there remaining changes that need an explicit human decision before implementation?

## Required review output

Return a concise, evidence-based report with these sections:

### Current implementation state

For each relevant rule or workflow component, identify its actual file path, status, and supporting evidence. Distinguish installed policy, approved but uninserted wording, draft proposals, and unresolved decisions. Use line references where practical.

### Intent alignment

Explain where the existing repository already supports the intended workflow. Do not recommend changes merely because you would personally design the system differently.

### Concrete gaps or contradictions

For each consequential issue, provide the conflicting passages or artifacts, practical effect, and smallest proposed correction. Distinguish correctness problems from optional improvements.

### Recommended next action

Recommend one bounded next step, or report that no change is needed. Identify approval dependencies and anything that should remain on hold. Do not prepare a broad migration or mass-edit plan unless explicitly requested.

### No-change confirmation

Confirm that the review did not edit files, create artifacts in the repository, move tasks, change status, stage changes, commit, push, or dispatch agents. Report any pre-existing working-tree changes without modifying them.

## Review boundaries

- Do not implement recommendations during this review.
- Do not interpret this brief as approval of historical drafts or current pending tasks.
- Do not assume remembered state is current repository state.
- Do not impose a new architecture before understanding the existing system.
- Do not generate parallel dispatch instructions that compete with task-file handoffs.
- Do not treat Claude availability as a prerequisite for all productive work.
- Preserve project-specific exceptions where they are intentional and compatible with governing rules.
- If the repository contradicts this brief, report the discrepancy for human resolution rather than silently rewriting either side.

## Provenance

This brief synthesizes recalled coordination discussions, especially September 17–18, 2026. It is an explanation of intent and a proposed review brief, not a live audit or proof of implementation. Relevant recalled records: [memory:13], [memory:14], [memory:15], [memory:16], [memory:17], [memory:20], [memory:21], [memory:22], and [memory:23]. These identifiers refer to conversation-memory records, not repository files.
