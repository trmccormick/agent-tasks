# Model Selection Guide

**Effective**: 2026-10-20 (Workflow Update)

## Summary

This guide clarifies which agent model is appropriate for each workflow role.

---

## Agent Roles & Model Selection

### Planning Agent
- **Primary Role**: Task creation, refinement, status tracking, continuation coordination
- **Model**: Copilot (default agent)
- **Authority**: Can create/modify tasks, prepare review context, track status
- **Cannot**: Validate acceptance criteria, issue GO/REVISE/STOP, approve implementation

### Implementation Agent (Qwen)
- **Primary Role**: Code changes, testing, synthesis, closeout
- **Model**: Qwen (specified in runSubagent)
- **Authority**: Implements changes, verifies code, creates synthesis, runs closeout
- **Cannot**: Commit/push without human authorization, issue review decisions

### Reviewer Agent (Domain Expert - Optional)
- **Primary Role**: Evaluate implementation against acceptance criteria
- **Model**: Specified by domain (e.g., python-fact-grounded-coding, pylance-refactoring)
- **Authority**: Issues GO/REVISE/STOP decisions
- **Cannot**: Implement code, authorize final release actions

### Human (User)
- **Primary Role**: Authorization and final release actions
- **Authority**: GO after reviewer approval, authorize commit/push, authorize task completion
- **Cannot**: Bypassed for review gates, must respect authority chain

---

## Authority Flow

```
Implementation Agent → Verify
                    → Synthesize
                    → STOP (await human authorization)
                    ↓
Human (with Reviewer input) → GO or REVISE
                            ↓
Implementation Agent → Commit
                    → Push
                    → Task lifecycle completion
                    → Final closeout
                    → STOP
```

---

## File Governance

- **Synthesis reports**: Evaluate per PLANNING_AGENT_CLOSEOUT.md § Synthesis Report Handling
- **Task files**: Commits require human authorization after implementation
- **Guide documents**: Can be committed without additional review (workflow documentation)
- **Status updates**: Requires planning closeout coordination

---

## Session Lifecycle

1. **Planning Session**: Create/refine tasks, prepare context → Hand off to implementation
2. **Implementation Session**: Code, test, synthesize → Stop before commit/push
3. **Human Authorization**: Review findings, approve GO/REVISE
4. **Final Implementation**: Commit, push, close task → End session
5. **Planning Closeout**: Update status, create handoff → End planning session

---

## When to Switch Models

- **Same task, different skill**: Use skill invocation (e.g., python-fact-grounded-coding within Qwen)
- **Validation needed**: Stop and request reviewer (don't continue with implementation agent)
- **New task dispatch**: Plan in planning session, dispatch to new implementation session
- **Cross-project**: Use runSubagent with appropriate model for that project's context

---

**References**:
- PLANNING_AGENT_CLOSEOUT.md - Synthesis report handling rules
- REVIEW_AGENT_WORKFLOW.md - Review gate details
- IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md - Implementation closeout steps
