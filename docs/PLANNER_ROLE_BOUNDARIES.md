# Planner Role Boundaries

**Purpose**: Define what planning agents do, what they must NOT do, and where they hand off authority.

**Target Audience**: Planning agents, implementation agents, reviewers, human authority (Tracy).

**Why This Matters**: The workflow's independence depends on clear role separation. If planner begins assessing implementation, the workflow collapses into self-validation instead of independent review.

---

## The Three Critical Distinctions

### Preparing Review Evidence ≠ Performing Review

| Preparing Evidence | Performing Review |
|---|---|
| ✅ Planner gathers facts | ✅ Reviewer assesses facts |
| ✅ Planner summarizes changes | ✅ Reviewer validates criteria |
| ✅ Planner links files/commits | ✅ Reviewer issues GO/REVISE/STOP |
| ❌ Planner does NOT validate criteria | ❌ Reviewer does NOT tell human to commit |

### Planning ≠ Approving

| Planning | Approving |
|---|---|
| ✅ Planner creates task scope | ✅ Reviewer validates completion |
| ✅ Planner refines acceptance criteria | ✅ Human authorizes next action |
| ✅ Planner monitors progress | ❌ Planner does NOT approve work |
| ❌ Planner does NOT decide if work is done | ❌ Reviewer does NOT commit code |

### Authority ≠ Coordination

| Coordination (Planner) | Authority (Reviewer/Human) |
|---|---|
| ✅ Planner recommends reviewers | ❌ Planner cannot override reviewer |
| ✅ Planner tracks cross-agent work | ❌ Planner cannot approve on behalf of reviewer |
| ✅ Planner prepares handoff context | ❌ Planner cannot authorize commits |
| ✅ Planner escalates blockers | ✅ Human makes final decisions |

---

## Planner Responsibilities (MUST DO)

### Task Lifecycle Management
- Create tasks from specifications
- Refine scope, acceptance criteria, and gotchas
- Update task status (backlog → active → completed)
- Move task files through git lifecycle (`git mv` backlog/ → active/ → completed/)
- Verify all tasks use current TASK_TEMPLATE.md

### Verification (Before Handoff to Reviewer)
- ✅ Confirm implementation agent stopped at review-gate (did NOT commit/push)
- ✅ Confirm synthesis report was created
- ✅ Confirm handoff file was created
- ✅ Confirm all changed files exist and are accessible
- ✅ Verify task file YAML status is current (active)

**Do NOT**:
- ❌ Validate whether changes meet acceptance criteria (reviewer job)
- ❌ Check code quality or logic correctness (reviewer job)
- ❌ Decide if architecture is sound (reviewer job)
- ❌ Issue GO/REVISE/STOP (reviewer authority only)

### Coordination
- Monitor parallel agent work (multiple implementation sessions)
- Track cross-agent dependencies
- Identify blockers and escalate to human
- Recommend execution order for Tasks 3, 4, 5...
- Manage premium budget allocation (which model for which work)
- Update status.md with session summaries (from handoffs)

### Preparation for Review
- Link to changed files (don't paste entire files into chat)
- List which acceptance criteria are listed in the task
- Point out any gotchas the implementation agent flagged
- Note any synthesis report findings
- Prepare a "review context package" but do NOT author the review

---

## What Planner MUST NOT Do

| Activity | Why Not | Who Does It |
|---|---|---|
| Validate acceptance criteria | Planner lacks independent judgment authority | Reviewer |
| Approve implementation | Planner designed the task, not independent | Reviewer |
| Issue GO / REVISE / STOP | Authority belongs to designated reviewer only | Reviewer |
| Commit/push code | Authority belongs to human only | Human (after reviewer GO) |
| Override reviewer assessment | Workflow requires independent validation | Human (not planner) |
| Assume implementation is ready without review | Defeats the purpose of separation | Reviewer must assess |
| Mark task complete in status.md | Requires reviewer GO + human authorization | Human or designated agent |
| Author reviewer reports | Compromises independence | Reviewer only |

---

## The Corrected Workflow

```
┌─────────────────────────────────────────────────────┐
│  IMPLEMENTATION AGENT (Qwen or other)              │
│  1. Move task backlog/ → active/                   │
│  2. Create synthesis report                        │
│  3. Implement changes                              │
│  4. Verify changes                                 │
│  5. Create closeout handoff                        │
│  6. ⏸️  STOP (cannot commit/push)                   │
└─────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────┐
│  PLANNER (Timothy or designated planner)           │
│  1. Confirm agent stopped at review-gate           │
│  2. Verify synthesis report exists                 │
│  3. Verify handoff file exists                     │
│  4. Prepare review context:                        │
│     - Link to changed files                        │
│     - List acceptance criteria from task           │
│     - Note any gotchas/findings                    │
│  5. ⏸️  STOP (no approval language)                │
└─────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────┐
│  REVIEWER (Copilot or designated reviewer)         │
│  1. Read planner context package                   │
│  2. Assess implementation against criteria         │
│  3. Validate synthesis report findings             │
│  4. Check for contradictions/risks                 │
│  5. Issue: GO | REVISE | STOP                      │
└─────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────┐
│  HUMAN (Tracy)                                     │
│  1. Review: Implementation + Reviewer assessment   │
│  2. Authorize: "Proceed with commit/push"          │
│  3. Or: Block/request changes (REVISE flow)        │
└─────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────┐
│  IMPLEMENTATION AGENT (Resume)                     │
│  6. Stage changes: git add [files]                 │
│  7. Commit: git commit -m "..."                    │
│  8. Push: git push                                 │
│  9. Move task: git mv active/ → completed/         │
│  10. Verify on main ✅                             │
└─────────────────────────────────────────────────────┘
```

**Critical Handoff Points**:
- After Step 5 (Implementation): Agent → Planner
- After Prep (Planner): Planner → Reviewer
- After Assessment (Reviewer): Reviewer → Human
- After Authorization (Human): Human → Implementation Agent

---

## Review Context Package Template

**What Planner Prepares** (NOT approving, just organizing):

```markdown
## Review Context — [Task Name]

**Implementation Status**: Complete (Qwen Steps 0-5)

**Changed Files**:
- [Link to file 1](path/to/file1.md)
- [Link to file 2](path/to/file2.md)
- [New file: path/to/file3.md](path/to/file3.md)

**Synthesis Report**: 
- [Link to synthesis](projects/agent-tasks/summaries/YYYY-MM-DD-SYNTHESIS-[TOPIC].md)

**Acceptance Criteria** (from task file):
- [ ] Criterion 1
- [ ] Criterion 2
...

**Gotchas Noted in Synthesis**:
- [Gotcha 1 from agent's synthesis report]
- [Gotcha 2 from agent's synthesis report]

**Questions for Reviewer**:
1. Do changes meet all acceptance criteria?
2. Any contradictions with existing guidance?
3. Are examples realistic and actionable?

**Your Assessment Needed**: GO | REVISE | STOP
```

**What Planner Does NOT Do**:
- ❌ Check each criterion themselves
- ❌ Issue GO/REVISE/STOP
- ❌ Approve work
- ❌ Author reviewer language

---

## Common Boundary Violations (How to Avoid)

### Violation 1: "The implementation looks good to me"
**Problem**: Planner assessing quality  
**Solution**: Planner says "Implementation is staged and ready for your review" (no quality judgment)

### Violation 2: "Criterion 1 is satisfied because..."
**Problem**: Planner validating criteria  
**Solution**: Planner points to evidence ("Criterion 1 requires X; file shows Y at line Z")

### Violation 3: "GO — approved for commit"
**Problem**: Planner issuing reviewer disposition  
**Solution**: Planner says "Ready for Copilot review" (stop before issuing disposition)

### Violation 4: "I approve this change"
**Problem**: Planner taking approval authority  
**Solution**: Planner says "Requesting your review and authorization"

---

## When to Escalate (Planner's Responsibility)

Planner MUST escalate to human if:
1. Implementation agent never reaches review-gate (bypasses workflow)
2. Implementation never produces synthesis report (missing evidence)
3. Reviewer and human disagree on authorization
4. Blocker prevents reviewer assessment (missing files, etc.)
5. Task appears abandoned (no closeout handoff created)

---

## Reference Files

- **TASK_TEMPLATE.md**: Template all planners use to verify task structure
- **SESSION_CLOSEOUT.md**: Procedure for implementation agents creating handoffs
- **REVIEW_AGENT_GUIDE.md**: Reviewer authority and responsibility
- **REVIEW_AGENT_WORKFLOW.md**: Reviewer assessment procedure
- **PLANNING_AGENT_SESSION_START.md**: Planner setup and planning procedure

---

## Validation Checklist for Planning Agents

Before handing off to reviewer, confirm:

- [ ] Task file is in `active/` with status: active
- [ ] Synthesis report was created and saved to `summaries/`
- [ ] Handoff file was created and saved to `handoffs/`
- [ ] Implementation agent confirmed: "STOP at review-gate (no commit/push)"
- [ ] All changed files are readable and accessible
- [ ] No partial implementation (all Steps 0-5 complete)
- [ ] Planner prepared review context (links, criteria, questions)
- [ ] Planner did NOT issue GO/REVISE/STOP
- [ ] Planner did NOT author approval language

---

**Last Updated**: 2026-10-20  
**Validated With**: Task 2 workflow (Review Artifact Standardization)  
**Next Validation**: Task 3 execution (Stop Condition Enhancement)
