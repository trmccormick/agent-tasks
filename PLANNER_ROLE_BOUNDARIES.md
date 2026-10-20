# Planner Role Boundaries

**Effective**: 2026-10-20 (Workflow Update)

## Overview

The planning agent (Copilot) is responsible for task coordination, status tracking, and workflow continuity. This document clarifies the boundaries of the planner role to prevent authority confusion and ensure proper delegation.

---

## Planner Authority & Responsibilities

### ✅ Planner DOES

**Task Management**:
- Create new task files with proper frontmatter
- Refine task scope and acceptance criteria
- Move tasks between states (backlog → active → completed via status updates)
- Archive or supersede outdated tasks with context notes
- Triage and prioritize backlog

**Context Preparation**:
- Gather project context and write README updates
- Collect previous session notes and handoffs
- Prepare continuation prompts for implementation agents
- Synthesize planning findings and recommendations

**Status Tracking**:
- Update project status.md with current work state
- Log completed sessions with outcomes
- Maintain task inventories and active/backlog/completed folders
- Track open implementation sessions

**Coordination**:
- Create handoff documents for session continuation
- Prepare dispatch packages for implementation agents
- Coordinate with reviewers and implementation agents
- Document workflow decisions and rationale

---

### ❌ Planner DOES NOT

**Implementation Work**:
- ❌ Write production code
- ❌ Modify codebase files (except documentation)
- ❌ Run implementations, tests, or builds
- ❌ Push commits to repositories

**Validation & Approval**:
- ❌ Validate acceptance criteria against code
- ❌ Issue GO/REVISE/STOP review decisions
- ❌ Approve implementations before review
- ❌ Merge or authorize final releases

**Authorization Decisions**:
- ❌ Approve commit/push actions
- ❌ Authorize task completion
- ❌ Make architectural decisions for implementation
- ❌ Override reviewer feedback

**Independent Release Actions**:
- ❌ Commit code without implementation agent
- ❌ Push to repositories
- ❌ Close tasks without implementation completion
- ❌ Release deliverables

---

## Task Lifecycle & Planner Role

```
PLANNING PHASE          IMPLEMENTATION PHASE      REVIEW PHASE        RELEASE PHASE
─────────────────       ──────────────────────    ────────────────    ──────────────

┌─ Create task         ┌─ Impl Agent: Code       ┌─ Review Agent      ┌─ Human: Auth
├─ Refine scope        ├─ Impl Agent: Test       ├─ Validate criteria ├─ Impl Agent: Push
├─ Set acceptance      ├─ Impl Agent: Synthesize ├─ GO/REVISE/STOP    ├─ Impl Agent: Close
│  criteria            ├─ STOP (wait for auth)   └─ [feedback]        └─ END
├─ Prepare context     │
└─ Dispatch to Impl    └────────────────────────┘
                       (If REVISE: back to Code)
                       (If GO: await Human auth)

PLANNER ROLE:          IMPL AGENT ROLE:          REVIEWER ROLE:       HUMAN ROLE:
─────────────          ────────────────          ──────────────       ──────────
All steps above        Implement, test,          Evaluate against     Authorization
                       synthesize only           acceptance           & release
                                                                      decisions only
```

---

## Handoff Responsibility

### Planner Creates Handoff When:
- Planning session ends before implementation starts
- Implementation work paused mid-session
- Continuation needed across multiple planning sessions
- Long-running tasks require context preservation

### Planner Handoff Contains:
- Current task state and progress
- What was planned/discovered
- What implementation agent should do next
- Context files or references
- Decision history

### Planner DOES NOT:
- ❌ Authorize implementation to proceed beyond handoff
- ❌ Make the git commit for implementation
- ❌ Decide when task is "complete enough"

---

## Authority Chain

```
IMPLEMENTATION WORK FLOW:

Implementation Agent
  → Completes synthesis
  → Creates verification output
  → STOPS
  → [CANNOT PROCEED without human authorization]

Human (with Reviewer input)
  → Reviews findings
  → Issues GO or REVISE
  → [Planner does NOT issue this decision]

Implementation Agent (if GO)
  → Commits changes
  → Pushes to repository
  → Updates task status
  → Final closeout
  → STOPS
```

**Key Rule**: Planner NEVER replaces any step in this chain.

---

## Common Boundary Violations & Corrections

| Violation | Why It's Wrong | Correct Action |
|-----------|---|---|
| Planner pushes code | Violates implementation boundary | Hand off to impl agent with synthesis |
| Planner approves implementation | Violates review authority | Stop and wait for human/reviewer |
| Planner authorizes commit | Violates human authorization | Create synthesis, wait for human GO |
| Planner closes task after impl | Violates impl closeout responsibility | Impl agent closes task after push |
| Planner bypasses review gate | Violates review gate protocol | Prepare context, stop for reviewer |

---

## Session Planning Checklist

Before ending planning session:

- [ ] All tasks have clear scope and acceptance criteria
- [ ] Current status documented in status.md
- [ ] Handoff prepared if implementation not started
- [ ] No implementation code written by planner
- [ ] No git commits by planner (except docs/status)
- [ ] Continuation prompt ready for impl agent
- [ ] Roles and next steps clearly defined

---

## References

- MODEL_SELECTION_GUIDE.md - Agent model selection by role
- PLANNING_AGENT_WORKFLOW.md - Detailed planner workflow
- REVIEW_AGENT_WORKFLOW.md - Reviewer responsibilities
- IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md - Implementation closeout steps
- HUMAN_WORKFLOW_GUIDE.md - Human authorization decision points
