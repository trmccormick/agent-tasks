# Planning Agent Dispatch Template — Stale Task Audit & Update

**Use this template to dispatch a web agent (Gemini, Claude, Perplexity) for stale task review and refinement.**

---

## Copy-Paste This Dispatch Message

```
You are the PLANNING AGENT tasked with reviewing and refining a stale task from the review folder.

---

## MANDATORY READS (In This Order)

1. **PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md**
   Path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md
   Contains: Core workflow principles, 3-phase workflow, operational standards

2. **RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md**
   Path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md
   Contains: Research assignment structure, deliverables checklist

3. **TASK_UPDATE_GUIDE.md** (Reference during drafting)
   Path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/TASK_UPDATE_GUIDE.md
   Contains: Reconciliation checklist that all refined tasks must meet

---

## STALE TASK TO REVIEW

[PASTE THE STALE TASK FILE CONTENT HERE — the task you're asking the agent to audit and refine]

---

## YOUR ASSIGNMENT

**Phase 1: Intake Checkpoint**
Before starting any research, answer this checklist in chat:
- [ ] I have read all three mandatory files above
- [ ] I understand my role is PLANNING/RESEARCH (not implementation)
- [ ] I understand the original stale task intent
- [ ] I understand why this task needs audit/refinement
- [ ] I understand outputs go to: proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-[ID]/
- [ ] I understand I do NOT implement, commit, or run tests

If ANY checkbox is unclear, STOP and ask before proceeding.

**Phase 2: Research & Validation**
1. Audit the stale task against current codebase state
2. Validate each claim/assumption in the original task
3. Answer key questions:
   - Has this work already been completed?
   - Is the original approach still valid?
   - Are there blocking dependencies?
   - Should scope be split, merged, or left as-is?

**Phase 3: Draft Refined Tasks**
1. Create synthesis report: `[YYYY-MM-DD]-RESEARCH-[TOPIC].md`
2. Create refined task file(s): `[YYYY-MM-DD]-[PRIORITY]-[TYPE]-[NAME].md`
3. Ensure all outputs pass TASK_UPDATE_GUIDE.md checklist
4. Save to: proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-[ID]/

---

## DELIVERABLES

You will produce:

1. **Synthesis Report** (`[DATE]-RESEARCH-[TOPIC].md`)
   - Current state vs. original intent
   - Task status classification (STALE_SOLVED | STALE_OBSOLETE | INCOMPLETE | BLOCKED)
   - Recommendations for refinement

2. **Refined Task File(s)** (`[DATE]-[PRIORITY]-[TYPE]-[NAME].md`)
   - Using TASK_TEMPLATE.md structure
   - Clear prerequisites and dependencies
   - Success criteria
   - No stale assumptions

3. **Optional: Summary** (`TASK_REFACTOR_SUMMARY.md`)
   - If drafting multiple tasks, summary table showing count/dependencies/priorities

---

## SUCCESS CRITERIA

- [ ] Synthesis report answers all research questions with evidence
- [ ] Task status is explicitly classified
- [ ] Refined task files use proper template structure
- [ ] All outputs meet TASK_UPDATE_GUIDE.md reconciliation checklist
- [ ] NO implementation, code changes, or tests run
- [ ] Files saved to correct folder

---

## START HERE

1. Tell me you're ready (confirm you've read all 3 mandatory files)
2. Answer the intake checklist in chat
3. I'll give you approval to begin research
4. Execute phases 2 and 3
5. Report completion and file locations
```

---

## How to Customize This Template

**Before pasting to an agent, update these placeholders:**

1. **[PASTE THE STALE TASK FILE CONTENT HERE]**
   → Replace with the actual stale task you want the agent to review

2. **[YYYY-MM-DD]-TASK-UPDATE-[ID]**
   → Use today's date and increment ID (e.g., `2026-07-01-TASK-UPDATE-3`)
   → Or agent will suggest the date/ID based on when they're executing

3. **[TOPIC]** (in file paths)
   → Replace with the actual topic (e.g., `SYSTEM-ORCHESTRATOR-VALIDATION`, `GUARDRAILS-REFACTORING`)

---

## Example Usage

**Before:**
```
[PASTE THE STALE TASK FILE CONTENT HERE — the task you're asking the agent to audit and refine]
```

**After:**
```
# Original Stale Task: SystemOrchestrator Resource Allocation Refactor

**Status**: stale (created 2026-04-15, pre-dates Phase C decisions)
**Reason in Review Folder**: Scope unclear, 4 proposed tasks seem to contradict each other

## Original Intent
Refactor SystemOrchestrator to handle 3+ simultaneous settlements with competing resource demands...

[Full task content...]
```

---

## When to Use This Template

✅ **Use this when:**
- A task has been moved to review/ folder (meaning it couldn't move to a phase folder)
- You need a planning agent to audit why it's stale
- You want refined, modern task files before implementation
- You're working with web agents (Gemini, Claude, Perplexity)

❌ **Don't use this when:**
- Task is already in active/ folder (use EXECUTOR dispatch instead)
- You want implementation (this is research/planning only)
- Stale task is trivial/obvious (use TASK_UPDATE_GUIDE.md manually)

---

## File Locations

**Workflow Files** (reference in every dispatch):
- PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md
- RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md
- TASK_UPDATE_GUIDE.md

**Output Locations** (where agent saves refined work):
- `proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-[ID]/[RESEARCH].md`
- `proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-[ID]/[REFINED_TASKS].md`

**Next Steps** (after agent completes):
1. You review synthesis + refined tasks locally
2. Claude does final approval
3. Move approved tasks to active/ folder
4. Implementation agents pick up

