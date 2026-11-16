# Planning Agent Workflow — Web Agent Edition (Gemini/Claude/Perplexity)

**Purpose**: Guide for web agents reviewing stale/review-folder tasks and producing research + draft task files  
**Output Destination**: `proposed new tasks/[YYYY-MM-DD-TASK-UPDATE-N]/`  
**Scope**: Research only → draft task files only (NO implementation)

---

## Core Workflow Principles (Read First)

These principles guide all planning work across this project:

1. **Surgical Audit**: Always audit stale tasks before attempting implementation
2. **Modernization**: Update stale tasks into modern, actionable versions for implementation agents (Claude/Qwen)
3. **Planner-Executor Handoff**: You (planner) research and map; implementation agents execute upon human approval
4. **No Direct Implementation**: Maintain an "audit & planning" phase until the task is fully refined and approved by the human

---

## File & Naming Standards

- **Output Folder**: All research reports + refined tasks go to `proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-[ID]/`
- **Research Report Format**: `[YYYY-MM-DD]-RESEARCH-[TOPIC].md`
- **Task File Format**: `[YYYY-MM-DD]-[PRIORITY]-[TYPE]-[DESCRIPTION].md`
  - PRIORITY: HIGH | MEDIUM | LOW | CRITICAL
  - TYPE: bug-fix | feature | refactor | architecture | documentation
  - DESCRIPTION: kebab-case, descriptive
- **Cross-Reference**: Every task file must reference the synthesis report and original task for traceability

---

## Operational Standards (All Tasks Must Meet These)

1. **Sync Requirement**: Output tasks must align with project-specific documentation (e.g., `GLOSSARY_SYSTEM_MECHANICS.md` for economic constants)
2. **Source of Truth**: Validate all references against current architecture docs — do not propagate stale assumptions
3. **Validation**: Ensure draft tasks pass the TASK_UPDATE_GUIDE.md reconciliation checklist before submitting
4. **Handoff Package**: Each refined task must include full context (synthesis report + original reference) for next agent

---

## Overview: What You're Doing Today

You are a **PLANNING AGENT**. Your job is NOT to implement — it's to:

1. **Read a stale task** (located in `tasks/review/` because it couldn't move to a phase folder)
2. **Research why it's stale** (audit the codebase/documentation to understand current state)
3. **Produce a synthesis report** (detailed findings on the original intent vs. current reality)
4. **Draft task files** (create new, refined task files based on your research)
5. **Save outputs** to `proposed new tasks/[DATE]/` for human review + Claude approval

**You do NOT:**
- Run any code, tests, or terminal commands
- Modify any codebase files
- Make git commits
- Implement anything

---

## Workflow: Three Phases

### Phase 1: Intake & Understanding

**You receive:**
- This workflow document
- Project README and status.md
- Original stale task file (in review/ folder)
- Any context documents referenced in the task

**You do:**
1. Read all files in order (this builds context)
2. Understand what the original task was trying to accomplish
3. Identify why it ended up in the review/ folder (likely: Qwen couldn't move it to a phase folder because the task scope was unclear, or the intent didn't match current code state)

### Phase 2: Research & Synthesis

**You research by:**
1. Reading relevant architecture docs
2. Tracing through code patterns described in the task
3. Identifying what HAS changed since the task was created
4. Identifying what is STILL needed/blocked
5. Checking if the task is:
   - **Stale & solved** (work was already done incidentally, task file never closed)
   - **Stale & obsolete** (original problem no longer exists or approach changed)
   - **Incomplete** (original intent is still valid but needs refinement)
   - **Blocked** (can't proceed until another task completes)

**You produce:** `[YYYY-MM-DD]-RESEARCH-[TOPIC].md` synthesis report

### Phase 3: Draft New Task Files

**Based on your synthesis report, you:**
1. Decide if this becomes ONE task or MULTIPLE tasks (if scope too broad)
2. Refactor scope to match current codebase state
3. Use the TASK_TEMPLATE.md as your blueprint
4. Create task file(s) with:
   - Clear, accurate context (no stale assumptions)
   - Explicit prerequisites (what must be done first)
   - Step-by-step implementation guidance
   - Risk/gotcha sections (what to watch for)
   - Success criteria (how to know it's done)

**You produce:** `[YYYY-MM-DD]-[PRIORITY]-[TYPE]-[NAME].md` task file(s)

---

## Mandatory Reads (In Order)

1. **Generic Agent Guide**:  
   `/Users/tam0013/Documents/git/agent-tasks/README.md`
   - Understand your role as PLANNING agent
   - Read "Session Roles" section

2. **Rules & Guardrails**:  
   `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
   - Understand execution boundaries (what you can/cannot do)

3. **Project Context**:  
   `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
   - Understand the galaxy_game project

4. **Research Template**:  
   `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/agent handoff examples/research_template.md`
   - Understand how to structure your synthesis report

5. **Task Template**:  
   `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md`
   - Understand how to structure draft task files

6. **Original Stale Task File**:  
   [Will be provided in dispatch]
   - Read what the original intent was

---

## Phase 1: Intake Checklist

Before starting research, answer these in chat:

- [ ] I understand the original task's intent (2-3 sentence summary in my own words)
- [ ] I can identify why this task is in the review/ folder
- [ ] I have read all mandatory files above
- [ ] I understand that my job is RESEARCH + DRAFT ONLY (no implementation)
- [ ] I understand that my outputs go to `proposed new tasks/[DATE]/` for human review

If any of these is unclear, STOP and ask before proceeding.

---

## Phase 2: Research & Synthesis

### Step 1: Audit Current State

Read the relevant architecture/code files to answer:

1. **Has the original problem been solved?**
   - Is the code already in place?
   - Are tests already passing?
   - Or is this still an open gap?

2. **Has the approach changed?**
   - Original task proposed Solution A — does the codebase use Solution A?
   - Or did the project pivot to Solution B while the task stayed in backlog?
   - Or is it still waiting to be done?

3. **Are there new dependencies?**
   - Does the task now require something it didn't 6 months ago?
   - Are there blocking issues that need to be resolved first?

4. **Is the scope still accurate?**
   - Does the original task still make sense?
   - Or should it be split into smaller pieces?
   - Or should it be merged with related work?

### Step 2: Document Findings

Create your synthesis report with sections:

**1. Scope Assessment**
- Summary of files/code reviewed
- Current state vs. original task intent
- Any major changes in the codebase since task was created

**2. Implementation vs. Intent**
- What the task was TRYING to accomplish (original goal)
- What the codebase ACTUALLY does now (current state)
- Discrepancies between intention and reality

**3. Task Status Classification**
- Is this: **STALE_SOLVED** | **STALE_OBSOLETE** | **INCOMPLETE** | **BLOCKED**?
- Evidence for your classification

**4. Proposed Action**
- What should happen next with this task?
  - Archive it as "already solved"?
  - Rewrite it for current codebase?
  - Split into 2-3 smaller tasks?
  - Defer pending another task?

### Step 3: Save Synthesis Report

**Format**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-N/[YYYY-MM-DD]-RESEARCH-[TOPIC].md`

**Example**: `2026-07-01-RESEARCH-SYSTEM-ORCHESTRATOR-VALIDATION.md`

---

## Phase 3: Draft New Task Files

### Step 1: Decide Task Scope

Based on synthesis, decide:
- Is this ONE task or MULTIPLE?
- If multiple, what are the dependencies between them?

### Step 2: Use the Template

Copy the task template: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md`

Fill in sections:

```yaml
status: backlog
priority: HIGH | MEDIUM | LOW
type: bug-fix | feature | refactor | architecture | documentation
```

- **Context**: What is this part of the system and why does this task exist?
- **Prerequisites**: What should be read first?
- **Critical Information**: What gotchas, risks, or credentials matter?
- **Detailed Task Steps**: Numbered list of what needs to be done
- **Acceptance Criteria**: How to know when it's done (green specs, X tests pass, etc.)
- **Stop Conditions**: When to escalate/ask for help

### Step 3: Save Draft Task Files

**Format**: `proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-N/[YYYY-MM-DD]-[PRIORITY]-[TYPE]-[NAME].md`

**Example**: `2026-07-01-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md`

---

## Deliverables (What Gets Saved)

By the end of your session, these files go into `proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-N/`:

1. **Research synthesis report** (1 file per stale task researched)
   - `[DATE]-RESEARCH-[TOPIC].md`
   - Contains: findings, classification, recommendations

2. **Draft task file(s)** (1+ files based on scope)
   - `[DATE]-[PRIORITY]-[TYPE]-[NAME].md`
   - Using TASK_TEMPLATE structure
   - Ready for a human or Claude to review before implementation

3. **Optional: Summary document** (if you're drafting multiple related tasks)
   - `TASK_REFACTOR_SUMMARY.md` — table showing task count, dependencies, priorities

---

## Success Criteria

- [ ] All synthesis reports answer: current state vs. original intent
- [ ] Task status is explicitly classified (STALE_SOLVED | INCOMPLETE | etc.)
- [ ] Draft task files use the template structure
- [ ] Each draft task has clear prerequisites and success criteria
- [ ] Files are saved to `proposed new tasks/[DATE]/`
- [ ] NO implementation or code changes (research + draft only)
- [ ] NO git commits (files are locally staged for human review)

---

## Process: Step-by-Step

1. **Tell me you're ready** — Answer the Phase 1 intake checklist in chat
2. **I confirm** — Human approves you proceeding to research
3. **You research** — Read architecture docs, trace code patterns, audit current state
4. **You draft synthesis** — Create research report with findings
5. **You draft tasks** — Create task file(s) using template
6. **You save locally** — Both go to `proposed new tasks/[DATE]/`
7. **You report completion** — Paste the file contents into chat OR just confirm saved
8. **Human reviews** — They decide whether outputs are ready for Claude approval
9. **Claude reviews** — Final architecture sign-off before implementation

---

## Example Output Structure

After your session, the folder looks like:

```
proposed new tasks/2026-07-01-TASK-UPDATE-2/
├── 2026-07-01-RESEARCH-SYSTEM-ORCHESTRATOR-VALIDATION.md
├── 2026-07-01-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md
├── 2026-07-01-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md
├── 2026-07-01-HIGH-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md
├── 2026-07-01-MEDIUM-ARCHITECTURE-STRATEGIC-PLANNER-EXTRACTION.md
├── 2026-07-01-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md
├── 2026-07-01-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md
├── 2026-07-01-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md
└── TASK_REFACTOR_SUMMARY.md (optional — overview of all tasks)
```

Each file is independently saveable by the user; Claude later reviews the whole set.

---

## Do NOT Do

❌ Run any code, tests, or terminal commands  
❌ Make git commits or push changes  
❌ Modify codebase files directly  
❌ Implement any features or bug fixes  
❌ Create databases or alter schema  
❌ Run Docker commands  

✅ Read and analyze existing files  
✅ Create research reports and task drafts  
✅ Save files locally for review  
✅ Ask clarifying questions if confused  

---

## Ready?

Read the mandatory files above, answer the Phase 1 intake checklist, then tell me you're ready to proceed.

