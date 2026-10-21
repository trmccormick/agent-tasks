# Galaxy Game UI — Items Needing Review

**Last Updated**: 2026-10-21

---

## Documentation Tasks (Workflow Patterns Discovered)

### Task: Update Planning Agent Dispatch Documentation

**Priority**: MEDIUM  
**Category**: Workflow Documentation  
**Context**: Discovered that Planning Agent can spawn Implementation Agent subagent sessions directly for read-only remediation tasks.

**What needs updating**:

1. **PLANNING_AGENT_SESSION_START.md**
   - Add section: "Spawning Implementation Agent Subagents"
   - Document: when appropriate, how to call runSubagent(), what to pass
   - Example: remediation tasks, read-only audits, parallel discovery work
   - Note: synthesis gates still apply to subagent results

2. **TASK_TEMPLATE.md**
   - Add section: "Subagent Dispatch Suitability"
   - Criteria for tasks suitable for subagent spawn vs. human dispatch
   - Read-only audit ✅ | Code implementation ⚠️ | User interaction ❌

3. **IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md**
   - Clarify: procedure applies whether session spawned by human, Planning Agent, or Copilot chat
   - Same synthesis gate pattern applies regardless of origin

4. **New doc** (optional): `AGENT_DISPATCH_PATTERNS.md`
   - Comprehensive guide to agent spawning patterns
   - When Planning Agent should spawn vs. defer to human
   - Parallel vs sequential task dispatch
   - Synthesis gate requirements for subagent work

**Evidence**:
- Commit: e8c57df (remediation tasks created)
- Qwen Planning Agent (2026-10-21): "I'll execute both tasks directly via separate subagent sessions"
- Both tasks executed in parallel, handoffs created, synthesis gates applied

**Action**: Review after remediation tasks complete (expect handoffs by end of 2026-10-21).

---

## Code Issues (Post-Audit)

### Task: Reconcile MockDataHelper Method Discrepancies

**Priority**: HIGH  
**Category**: Code  
**Blocked by**: Task `2026-10-21-HIGH-AUDIT-MOCKDATA-METHODS.md` (in progress)

**What needs deciding**:
- Dashboard synthesis references `player_contracts`, `pending_operations` → NOT in MockDataHelper
- Admin synthesis references `ai_manager_priorities`, `digital_twins`, `terraforming_scenarios` → NOT in code
- Admin method is `digital_twin_scenarios` not `digital_twins`

**Decision needed**:
- Add missing methods to MockDataHelper?
- Rename existing methods for clarity?
- Reword synthesis reports to match actual methods?
- Combination of above?

**Action**: Review Qwen's audit report. Make reconciliation decision. Update mock data or synthesis reports accordingly.

---

## Design Session (Blocked Gate)

### Task: Design Session — Clarify Dashboard Scope

**Priority**: HIGH  
**Category**: UX/Design  
**Blocks**: Tasks 2 (Dashboard), 2a–2d (subtasks)

**What needs deciding**:
- Which panels should Dashboard display? (4? 9? custom selection?)
- Task Context lists 9 sections | Task Steps list 4 panels | Qwen Synthesis lists 3 panels (conflicting)
- Design must reference Galaxy Game prototype + EVE Dashboard for inspiration

**Decision needed**:
- Schedule Gemini design session (or manual review)
- Approve specific panel list and layout
- Document output: screen mockup descriptions, data flow, acceptance criteria

**Action**: Schedule after remediation tasks complete and user approves method reconciliation.

---

## Process/Governance

### Task: Task 1 File Remediation Complete — Move to Completed

**Priority**: HIGH  
**Category**: Process  
**Blocked by**: Task `2026-10-21-HIGH-REMEDIATE-TASK-1-CLEANUP.md` (in progress)

**What needs doing**:
- Qwen completes Task 1 cleanup
- Creates handoff file
- Human reviews + gives GO disposition
- Move task file from `active/` → `completed/2026-10/`
- Commit with explicit paths (per GUARDRAILS)

**Action**: Execute after remediation GO.

---

## Summary

| Item | Status | Blocker? | ETA |
|------|--------|----------|-----|
| Workflow doc update | 🔴 NOT STARTED | No | Post-2026-10-21 |
| MockDataHelper reconciliation | 🟡 IN PROGRESS | Yes (Task 2) | 2026-10-21 |
| Design session scheduling | 🔴 NOT STARTED | Yes (Tasks 2–4) | Post-2026-10-21 |
| Task 1 file completion | 🟡 IN PROGRESS | No (Task 1) | 2026-10-21 |

---

# WORKFLOW INVESTIGATION: Agent Dispatch Evolution Proposal

**Investigation Date**: 2026-10-21  
**Scope**: Multi-agent orchestration patterns emerging across projects  
**Status**: Documentation & proposal (no modifications to core workflow yet)  
**Intended Review**: Future Claude workflow analysis session

---

## Executive Summary

During Galaxy Game UI remediation phase, Qwen Planning Agent autonomously spawned two parallel Implementation Agent subagent sessions for read-only discovery tasks (task file audit, code audit). Both sessions executed successfully with synthesis gates applied. This pattern has efficiency implications for the planning phase and suggests potential evolution toward a specialized Dispatch Agent role.

Concurrent independent review by Grok confirmed that agent-tasks repository already contains substantial dispatch-related infrastructure (routing logic, governance frameworks, authority boundaries, multi-agent task governance rules). A Dispatch Agent would extend existing patterns, not introduce new concepts.

**Recommendation**: Preserve findings and evidence; defer implementation proposal until additional Claude review can validate risk/benefit tradeoffs.

---

## Observed Pattern: Galaxy Game Orchestration (2026-10-21)

### What Happened

1. **Planning Agent Decision**: After creating two remediation task files, Qwen Planning Agent stated: *"I'll execute both tasks directly via separate subagent sessions"* (not waiting for human dispatch)
2. **Subagent Execution**: Two parallel Implementation subagent sessions started autonomously:
   - **Subagent A**: Task 1 cleanup (read-only file audit + synthesis)
   - **Subagent B**: MockDataHelper audit (read-only code verification + report)
3. **Synthesis Gates**: Both subagents created handoff files before committing
4. **Human Review Point**: Planning Agent awaited human GO/REVISE/STOP on both before execution proceeded

### Evidence

| Item | Value |
|------|-------|
| Commits created | e8c57df (tasks created), 45e520c, da51cca (remediation execution) |
| Planning Agent model | Qwen 3.6 (via Copilot) |
| Subagent model | Implementation Agent (model unspecified) |
| Execution pattern | Parallel, read-only discovery |
| Handoff pattern | One per subagent; both awaited user GO |
| Time efficiency | Two parallel discovery sessions vs sequential dispatch |

### Why This Matters

- **Discovery phase acceleration**: Parallel reads reduce planning latency
- **Synthesis gate preservation**: Handoffs created before any commits
- **Authority maintained**: Human approval still required; no autonomous commits
- **Pattern repeatability**: Observed twice with same result (e.g., Galaxy C sessions mentioned in AGENT_NOTES.md)

---

## Existing Dispatch Infrastructure in agent-tasks

Repository review confirms agent-tasks already contains comprehensive dispatch-related frameworks:

### Governance & Authority Structures

| Artifact | Purpose | Status |
|----------|---------|--------|
| `GUARDRAILS.md` (Rule 28–30) | Multi-Agent Task Governance, Authority Chain | ACTIVE |
| `rules/AGENT_ROUTING.md` | Agent capability routing decisions | ACTIVE (dated June 2026, needs reconciliation) |
| `ROUTING_LOGIC.md` | Quick-reference routing summary | ACTIVE (advisory, not binding) |
| `rules/DECISIONS.md` | Historical routing decisions | ACTIVE |
| `notes/AGENT_NOTES.md` | Model-specific facts (currently most accurate) | ACTIVE |

### Task Governance (Backlog)

| Task File | Domain | Status |
|-----------|--------|--------|
| `2026-09-17-GOVERNANCE-MAG-1-TASK-FILE-EXECUTION-CONTRACT.md` | Authority chain, execution contract | BACKLOG (draft) |
| `2026-09-17-CRITICAL-GOVERNANCE-MAG-2-DISPATCH-AUTHORITY.md` | Human-controlled dispatch, synthesis authority | BACKLOG (draft) |
| `2026-09-17-GOVERNANCE-MAG-3-ROUTING.md` | Capability-based routing logic | BACKLOG (draft) |
| `2026-09-17-GOVERNANCE-MAG-4-DEPENDENCY-PARALLELIZATION.md` | Parallel task execution patterns | BACKLOG (draft) |
| `2026-09-17-GOVERNANCE-MAG-5-PREFERENCES-GUIDANCE-NOT-RULES.md` | Preferences as guidance, not binding rules | BACKLOG (draft) |
| `2026-09-18-GOVERNANCE-MAG-6-PER-PROJECT-SESSION-GUIDANCE.md` | Project-specific session guidance | BACKLOG (draft) |

### Multi-Agent Routing Concepts (Already Present)

- ✅ Agent role hierarchy (planning, implementation, review, strategic)
- ✅ Authority boundaries (what each role may/may not do)
- ✅ Synthesis gates (output must be reviewed before commit)
- ✅ Human approval checkpoints (policy, architecture, irreversible actions)
- ✅ Task execution contracts (scope, lifecycle, authorized mode)
- ✅ Evidence classification (verified vs reported vs assumed)
- ✅ Dispatch routing decisions (capability-based, not model-based)
- ✅ Cross-project routing (multiple projects, same workflow)

---

## Grok Repository Assessment Summary

**Finding**: Grok's independent review of agent-tasks confirmed that:

1. **Dispatch infrastructure already exists** — routing logic, governance frameworks, and authority boundaries are documented
2. **Concepts are mature** — existing patterns include evidence labeling, synthesis gates, role boundaries, authority chains
3. **Natural evolution** — a Dispatch Agent would extend existing patterns, not introduce novel governance concepts
4. **Risk consideration required** — implementation should validate tradeoffs before modification to core workflow documents

**Grok's Conclusion**: A Dispatch Agent may be a natural evolution of existing patterns, but additional validation is required before modifying workflow documents.

---

## Proposal: Dispatch Agent Role Evolution

### What a Dispatch Agent Might Do

A specialized Dispatch Agent could encapsulate current planning-phase orchestration logic:

**Phase 1: Pre-Dispatch Analysis**
- Read task files and project status
- Verify task prerequisites (blockers, dependencies)
- Synthesize evidence (verified vs reported facts)
- Classify task suitability for subagent spawn (vs human dispatch)

**Phase 2: Agent Selection & Routing**
- Evaluate task against current agent capabilities
- Recommend appropriate agent (model, tier, local vs cloud)
- Surface risks (complexity, scope, authority gates)

**Phase 3: Orchestration Decision**
- Recommend: dispatch to human, spawn subagent(s), or defer
- Explain reasoning (capability match, cost, risk, dependencies)
- Flag synthesis gate requirements

**Phase 4: Execution Monitoring**
- Track subagent session status (if spawned)
- Alert on blockers or synthesis gate violations
- Consolidate handoffs into summary report

### Potential Benefits

| Benefit | Impact | Evidence |
|---------|--------|----------|
| **Parallel discovery acceleration** | Reduces planning latency for multi-task phases | Galaxy Game 2026-10-21: 2 parallel audits vs sequential dispatch |
| **Consistent orchestration** | Standardizes routing decisions across projects | Existing patterns in GUARDRAILS already define authority boundaries |
| **Authority gate preservation** | No change to human approval checkpoints | Synthesis gates still required before any commits |
| **Cost optimization** | Dispatch Agent reasoning (local) vs implementation work (cloud cost) | Similar to current Planning Agent model (free web) + Qwen executor (local) |
| **Evidence preservation** | Systematic classification of verified/reported/assumed facts | Infrastructure already in PHASE_1_REVIEW_EXTRACT.md |

### Potential Risks & Concerns

| Risk | Concern | Mitigation Strategy |
|------|---------|---------------------|
| **Authority ambiguity** | Dispatch Agent recommendations might blur with approval | Ensure task file language clearly states "recommendation" not authorization; human always approves |
| **Over-orchestration** | Simple tasks might go through unnecessary routing analysis | Define minimal-task threshold; skip analysis for routine work |
| **Vendor lock-in to Planning model** | Planning Agent model changes might require Dispatch Agent redesign | Document Dispatch Agent expectations (role, capabilities) independent of model choice |
| **Subagent cycle cost** | Spawning many small subagents might consume more tokens than intended | Profile with real task workloads; set spawn criteria carefully |
| **Integration complexity** | Dispatch Agent adds layer between human and execution | Clear handoff patterns; transparent reasoning in synthesis reports |

---

## Open Questions for Future Review

1. **Authority Scope**: Should Dispatch Agent have authority to spawn subagents, or only recommend? Does this require new MAG rule?
2. **Subagent Criteria**: What task characteristics make subagent spawn appropriate? (read-only? ≤2hr? <150 lines? specific domain?)
3. **Cost Model**: Is Dispatch Agent reasoning cost offset by parallelization savings, or does it increase overhead?
4. **Integration Point**: Should Dispatch Agent run at session start, task creation, or dispatch time?
5. **Model Choice**: Which agent models are suitable for Dispatch Agent role? (local Qwen? free Claude? Copilot?)
6. **Error Handling**: What happens if subagent fails/exceeds budget/creates invalid handoff?
7. **Cross-Project Scalability**: Does Dispatch Agent handle all 6+ projects uniformly, or need per-project customization?

---

## Recommended Proposal Artifacts (For Future Review)

| Artifact | Purpose | Created? |
|----------|---------|----------|
| **Investigation Summary** (this section) | Document findings, questions, risks, benefits | ✅ In NEEDS_REVIEW.md |
| **Dispatch Agent Specification Draft** | Define role, capabilities, authority boundaries | ⏳ Pending (defer until additional review) |
| **Orchestration Pattern Guide** | Document when to spawn subagents vs dispatch | ⏳ Pending (complements existing AGENT_DISPATCH_PATTERNS.md proposal) |
| **Cost-Benefit Analysis** | Model token usage, time savings, complexity tradeoffs | ⏳ Pending (requires real-world profiling) |
| **Authority Boundary Update (MAG-7?)** | New rule for Dispatch Agent role and subagent spawn authority | ⏳ Pending (defer until questions resolved) |

---

## Recommended Action

**DO NOT** modify core workflow documents or implement Dispatch Agent changes yet.

**DO**:
1. Preserve this investigation document in NEEDS_REVIEW.md
2. Flag for Claude workflow review session (when premium budget allows)
3. Continue monitoring if pattern repeats in other sessions
4. Collect real-world evidence of cost/time tradeoffs
5. Answer open questions before proposing implementation

**Timeline**: This investigation is preserved for future review. No action required until Claude analysis validates approach.

---

**Investigation Prepared By**: GitHub Copilot (Planning Agent role)  
**Based on**: Galaxy Game UI session observations + Grok repository assessment + existing agent-tasks infrastructure  
**Status**: Ready for future Claude workflow review
