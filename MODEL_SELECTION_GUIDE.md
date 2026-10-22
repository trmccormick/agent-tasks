# Model Selection Guide

**Effective**: 2026-10-22 (Updated for draft review workflow, web agent selection)  
**Status**: Living document — updated as model capabilities evolve

## Summary

This guide clarifies which agent model is appropriate for each workflow role, and documents current state of available web models for reference and evolution tracking.

---

## Available Web Models for Review Work

**Use this table to recommend review agents and track capability changes.**

| Agent | GitHub Access | Code Review | Task Review | Availability | Best For | Notes |
|-------|---|---|---|---|---|---|
| **Claude** | ✅ Yes (direct) | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Free web | Primary code review agent; can read repos directly | Strong reasoning, detailed analysis |
| **Grok** | ✅ Yes (direct) | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Free web | Primary code review agent; quick turnaround | Can drift slightly; less structured than Claude |
| **ChatGPT** | ✅ Yes (direct) | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Free web | Code/task review with GitHub context | Good visual understanding; moderate context |
| **Gemini** | ❌ No | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Free web | Long-context task decomposition, complex reviews | Excellent context length; needs copy-paste |
| **Perplexity** | ❌ No | ⭐⭐⭐ | ⭐⭐⭐⭐ | Free web (limited uploads) | Task synthesis, cross-agent coordination | Long context; file uploads after quota exhausted |
| **Office 365 Copilot** | ❌ No | ⭐⭐⭐ | ⭐⭐⭐⭐ | Included with O365 | General reasoning, fallback option | No budget constraint; copy-paste only |

**Legend**:
- ⭐⭐⭐⭐⭐ = Excellent for this use case
- ⭐⭐⭐⭐ = Very good
- ⭐⭐⭐ = Good, acceptable
- ✅ Yes (direct) = Can read GitHub repos via URL link
- ❌ No = Requires copy-paste of file contents

**Current Recommendations**:
- **Draft task review** (scope, clarity, dependencies): Claude or Grok (GitHub access for architecture verification)
- **Long/complex tasks** (multiple components): Gemini (no GitHub needed, long context handles complexity)
- **Quick reviews** (straightforward scope): Office 365 Copilot (included, no overhead)
- **Fallback** (primary agent unavailable): Use agent from same row that is available

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

### Reviewer Agent (Web Agent for Draft Tasks)
- **Primary Role**: Assess draft task files for scope clarity, dependencies, architecture alignment
- **Model**: Web agents — Claude or Grok (primary); Gemini for complex/long tasks; Office 365 Copilot for quick reviews
- **Read access**: Reads draft tasks from git (public repo)
- **Authority**: Issues GO/REVISE/STOP disposition on draft task readiness
- **Output**: Assessment saved to `projects/[project]/summaries/` as MD file
- **Cannot**: Move tasks, authorize implementation, approve final release

**Selection**: Planning agent recommends which web model (see "Available Web Models" table) based on task needs; human approves dispatch.

### Reviewer Agent (Domain Expert — Optional, for Implementation)
- **Primary Role**: Evaluate implementation against acceptance criteria (for complex/specialized code)
- **Model**: Specified by domain (e.g., python-fact-grounded-coding, pylance-refactoring, WVU Copilot)
- **Authority**: Issues GO/REVISE/STOP decisions on implementation work
- **Cannot**: Implement code, move tasks, authorize final release

**Note**: Most tasks use draft review (web agent). Implementation review (domain expert) is optional for specialized domains.

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

---

## Maintaining This Document

**This is a living document.** Update the "Available Web Models for Review Work" table as:
- New web models become available
- Existing models gain/lose GitHub access
- Capabilities change (e.g., context length, reasoning quality, new features)
- Recommendations shift based on experience

**When to update**:
- After each major release or capability change in a model
- When trying a new model for the first time (document capability in table)
- When availability or access changes (e.g., model sunset, new pricing tiers)

**Versioning**: Update the "Effective" date at the top of this file when making changes. Brief changelog in git commit message (e.g., "add Grok GitHub access capability", "Claude context length increased").

This ensures planning agents always have current, accurate information for recommending review agents.
