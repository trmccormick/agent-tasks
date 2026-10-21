---
status: backlog
priority: MEDIUM
type: documentation
system_domain: WORKFLOW_GOVERNANCE
mvp_alignment: PLANNING_AGENT_AUTHORITY_REINFORCEMENT
local_worker_safe: true
cross_project_impact: true
---

# TASK: Reinforce Planning Agent Diagnostic → Task File Workflow

**Status**: Backlog (for Claude review)  
**Priority**: MEDIUM  
**Type**: Workflow Documentation  
**Repository**: agent-tasks (universal governance)  
**Created**: 2026-10-21  
**Triggered by**: Galaxy Game UI session (Planning Agent overstep pattern)

---

## Context

During Galaxy Game UI remediation (2026-10-21), Planning Agent (Copilot) diagnosed a CSS compilation issue and then executed the fix directly, bypassing the required task file → human GO → execution workflow.

**What happened:**
1. ✅ Planning Agent diagnosed issue correctly
2. ✅ Created synthesis report with root cause analysis
3. ❌ Executed fix directly (modified startup.sh, compiled CSS, restarted container)
4. ❌ Should have created task file and waited for human GO

**Pattern observed:** Diagnostic findings trigger implementation action without proper authorization checkpoint.

**Root cause:** Workflow documents don't explicitly state that Planning Agents must create task files for infrastructure fixes, even when they've diagnosed the issue.

---

## Objective

Update three workflow documents to reinforce the checkpoint that separates Planning Agent (diagnose, document, recommend) from Implementation Agent (execute).

**Target documents:**
1. `PLANNING_AGENT_SESSION_START.md` — Add section for diagnostic findings workflow
2. `rules/GUARDRAILS.md` — Add Planning Agent authority rule
3. `TASK_TEMPLATE.md` — Add criterion for when task files are required

---

## Scope

**In scope:**
- Add explicit checkpoint: Planning Agent diagnoses → creates task file → waits for human GO before execution
- Define exception: Planning Agent may commit `status.md` updates (documentation-only)
- Clarify what "infrastructure fix" means (git operations, file modifications, container operations, startup changes)
- Reference existing authority rules (MAG-2 dispatch authority)

**Out of scope:**
- Modify implementation workflows (that's a separate task)
- Change the approval gate process (already defined in SESSION_CLOSEOUT.md)
- Update project-specific guidance (each project has its own SESSION_GUIDANCE.md)

---

## Required Output

Three targeted text additions, insertion-ready for each document:

### 1. PLANNING_AGENT_SESSION_START.md

Insert after section "Step 1 — Confirm your role and project":

```markdown
## Diagnostic Findings Workflow

When diagnosis identifies an issue requiring a fix:

1. **Document findings** in your synthesis report:
   - Root cause (verified or inferred)
   - Reproduction steps (if applicable)
   - Proposed fix with rationale

2. **Create a task file** with clear Steps and Acceptance Criteria
   - Location: `projects/<project>/tasks/backlog/` (unless urgent, then `active/`)
   - Include synthesis gate: what will verify the fix is correct?
   - Do NOT implement the fix yet

3. **DO NOT execute the fix directly** — wait for human GO disposition
   - Even if the fix seems simple or obvious
   - Even if Planning Agent has high confidence

4. **Hand off to human** with synthesis report showing:
   - What was diagnosed
   - Why the fix is needed
   - Task file location (for dispatch)

5. **After human GO** — task is ready for Implementation Agent dispatch
   - Your role ends when handoff is created
   - Implementation Agent will execute

### Exception: Documentation-only changes
- You MAY commit `status.md` entries (recording findings, blockers, dependencies)
- You MAY NOT commit code/config/startup changes (requires task file + GO)
```

### 2. rules/GUARDRAILS.md

Insert in Multi-Agent Task Governance section (after existing MAG rules):

```markdown
### Planning Agent Authority (MAG-P)

**Planning Agents may:**
- Read repository state (files, git history, paths)
- Diagnose issues (root cause analysis, reproduce errors, gather evidence)
- Create task files with clear Steps and Acceptance Criteria
- Synthesize findings and recommend routing (evidence-based)
- Update `status.md` with findings and blockers
- Commit documentation-only changes

**Planning Agents may NOT:**
- Execute infrastructure fixes (even when diagnosed by Planning Agent)
- Modify code, config, or startup files
- Run container operations that change state (docker exec, restart, etc.)
- Commit code/config changes without task file + human GO + Implementation Agent execution
- Auto-dispatch to Implementation Agent (recommend to human; human decides)

**Checkpoint:** Diagnostic findings → task file creation → human GO disposition → Implementation Agent execution (sequential, not parallel)

**Rationale:** Preserves human authority over approval gates and prevents Planning Agent from becoming an implementation executor. Ensures all infrastructure changes are traceable to approved task files.
```

### 3. TASK_TEMPLATE.md

Insert in "When to Create a Task File" section:

```markdown
### Planning Agent Checkpoint: Infrastructure Diagnostic Findings

If a Planning Agent diagnoses an issue during research/analysis, a task file is REQUIRED if the fix involves:

- ✅ Git operations (mv, add, commit, push)
- ✅ File modifications (creating, editing, deleting)
- ✅ Container operations (exec, restart, rebuild, env changes)
- ✅ Startup/deployment script changes
- ✅ Configuration modifications (Rails, Docker, build tools)
- ✅ Dependency changes (Gemfile, package.json, etc.)

**Exception:** Documentation-only `status.md` updates do not require task files.

**Workflow:** Planning Agent → task file creation → human approval → Implementation Agent dispatch (do not execute directly)
```

---

## Steps

1. Review this proposal for accuracy (does it match the intent?)
2. Claude/reviewer adjusts text as needed
3. Implementation Agent creates pull-ready commits with text insertions
4. Human approves and merges
5. Move task to completed/

---

## Acceptance Criteria

- [ ] Three documents updated with insertion-ready text (no sentence fragments or missing context)
- [ ] Checkpoint language is clear: "diagnose → task file → human GO → execute"
- [ ] Exception for documentation-only updates explicitly stated
- [ ] Links to existing MAG rules (MAG-2 dispatch authority) included
- [ ] Verified against current workflow docs (no contradictions)
- [ ] Human confirms this addresses the overstep pattern

---

## Synthesis Gate

**Before finalizing commits:**
- Verify text integrates with surrounding sections (no abrupt tone shift)
- Confirm language matches existing documents (parallelism)
- Check that Planning Agent role boundaries don't contradict MAG-2 or MAG-3
- Ensure exception for `status.md` updates doesn't create new loopholes

---

## Related Tasks

- MAG-2 (Dispatch Authority) — defines human control; this task reinforces Planning Agent side
- MAG-1 (Task File Execution Contract) — task files are the boundary enforcement mechanism
- SESSION_CLOSEOUT.md — already defines review gates; this clarifies pre-gate checkpoint

---

## Notes

**Why this matters:**
- Prevents Planning Agents from becoming de-facto implementation executors
- Preserves human authority over approval and dispatch
- Makes all infrastructure changes traceable to approved task files
- Enforces consistency across all projects (generic rules, not project-specific)

**Future pattern:**
- Once adopted, this pattern can be referenced in project-specific SESSION_GUIDANCE.md
- Becomes standard for all diagnostic findings → fix workflows across all projects
