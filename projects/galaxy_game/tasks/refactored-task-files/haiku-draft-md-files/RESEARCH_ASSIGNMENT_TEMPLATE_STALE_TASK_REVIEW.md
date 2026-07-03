# Research Assignment — Stale Task Review (Web Agent Edition)

**You are the PLANNING AGENT** (research phase).

**Project**: galaxy_game  
**Assignment**: Review stale task file(s) from review/ folder and produce synthesis report + recommendations

---

## Mandatory Reads (In This Order)

1. **Planning Agent Workflow**: `PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md` (in same folder)
   - Explains your role, phases, and deliverables

2. **Generic Agent Guide**: `/Users/tam0013/Documents/git/agent-tasks/README.md`
   - Understand PLANNING role vs. EXECUTOR role

3. **Guardrails**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
   - Understand what you can and cannot do

4. **Research Template**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/agent handoff examples/research_template.md`
   - Template for structuring your synthesis report

5. **Task Template**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md`
   - Template for structuring draft task files

6. **Stale Task File(s) to Review** (PROVIDED BELOW):
   - [Task file(s) will be inserted here by user]

---

## Your Assignment Today

### Phase 1: Intake Checkpoint

Before starting research, answer in chat:

- [ ] I have read all mandatory files above
- [ ] I understand my role is PLANNING/RESEARCH (not implementation)
- [ ] I understand the original task intent (2-3 sentence summary)
- [ ] I understand why this task is in the review/ folder
- [ ] I understand my outputs go to `proposed new tasks/[DATE]/`
- [ ] I understand I do NOT implement, commit, or run tests

**If any checkbox is unclear, STOP and ask before proceeding.**

---

### Phase 2: Research Scope

**You will:**

1. Read the stale task file(s) provided below
2. Audit relevant architecture/code to understand current state
3. Answer these key questions:
   - Has this work already been completed (incidentally, by other changes)?
   - Is the original approach still valid, or has the project pivoted?
   - Are there blocking dependencies?
   - Is the scope still accurate, or should it be split/merged?

4. Classify the task status:
   - `STALE_SOLVED` — Work was done, task file never closed
   - `STALE_OBSOLETE` — Original problem no longer exists or approach changed
   - `INCOMPLETE` — Original intent valid, needs refinement
   - `BLOCKED` — Can't proceed until another task completes

5. Create synthesis report answering:
   - What was the original intent?
   - What is the current state of the codebase?
   - What discrepancies exist between intent and reality?
   - What should happen next?

### Phase 3: Draft New Task Files

**Based on your synthesis:**

1. Decide if this becomes 1 task or multiple tasks (if scope too broad)
2. Refactor scope to match current codebase reality
3. Create draft task file(s) using `TASK_TEMPLATE.md` structure
4. Each task should have:
   - Clear, accurate context (no stale assumptions)
   - Prerequisites (what must be done first)
   - Implementation steps
   - Risk/gotcha sections
   - Success criteria

---

## Deliverables

Save to: `proposed new tasks/[YYYY-MM-DD]-TASK-UPDATE-N/`

**You produce:**

1. **Synthesis Report**: `[YYYY-MM-DD]-RESEARCH-[TOPIC].md`
   - Findings on current state vs. original intent
   - Task status classification
   - Recommendations

2. **Draft Task File(s)**: `[YYYY-MM-DD]-[PRIORITY]-[TYPE]-[NAME].md`
   - Using TASK_TEMPLATE.md structure
   - 1+ files depending on scope

3. **Optional Summary**: `TASK_REFACTOR_SUMMARY.md`
   - If drafting multiple tasks, table showing count, dependencies, priorities

---

## Success Criteria

- [ ] Synthesis report answers all Phase 2 research questions
- [ ] Task status explicitly classified (STALE_SOLVED | INCOMPLETE | etc.)
- [ ] Draft task files use template structure
- [ ] Each task has clear prerequisites and success criteria
- [ ] NO implementation, code changes, or tests run
- [ ] NO git commits (files are locally staged for human review)
- [ ] Files saved to correct folder

---

## Process

1. **Confirm intake** — Answer checklist in chat, wait for approval
2. **Research** — Audit code/docs, answer key questions
3. **Draft synthesis** — Create research report
4. **Draft tasks** — Create task file(s) using template
5. **Save locally** — Both go to `proposed new tasks/[DATE]/`
6. **Report completion** — Confirm files are saved and ready for review

---

## Important Reminders

✅ **Do:**
- Read and analyze existing files
- Research by tracing code patterns
- Ask clarifying questions
- Produce high-quality synthesis reports
- Create task files using the template

❌ **Do NOT:**
- Run any code, tests, or terminal commands
- Make git commits
- Modify codebase files
- Implement features or fixes
- Run Docker commands

---

## Ready?

1. Read the Planning Agent Workflow (same folder)
2. Read all mandatory files (links above)
3. Answer the Phase 1 intake checklist in chat
4. Tell me you're ready to proceed

After I confirm, you'll begin Phase 2 research.

