# Dispatch Example: Planning Agent Research Assignment (SystemOrchestrator)

**This file shows what you copy-paste into a web agent (Gemini) chat.**

---

## What You Copy-Paste:

```
You are the PLANNING AGENT (Research Phase).

Project: galaxy_game
Assignment: Review stale SystemOrchestrator task scope and produce synthesis report + recommendations

READ FIRST (in this order):
1. /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md
   (Contains: core principles, workflow phases, standards, operational guardrails)
2. /Users/tam0013/Documents/git/agent-tasks/README.md (PLANNING role section)
3. /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
4. /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/agent handoff examples/research_template.md
5. /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md
6. /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/TASK_UPDATE_GUIDE.md
   (Reference: reconciliation checklist that all refined tasks must meet)

CONTEXT (stale task info):
- Original task file: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/2026-06-30-TASK-UPDATE-1/
- Previous session output: 2026-06-30-1156-GEMINI-SESSION_HANDOFF_SUMMARY.md
- Gap analysis: 2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md
- Research audit: 2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md
- 4 proposed tasks created by Gemini (in 2026-06-30-TASK-UPDATE-1 folder)

YOUR TASK TODAY:
1. Intake checkpoint — Answer the checklist in your chat (confirm you've read everything)
2. Research — Audit the 4 proposed tasks against current codebase state
3. Validate findings — Are Gemini's 9 identified gaps real or partially resolved?
4. Draft refinements — Create improved task files based on research

DELIVERABLES (save to `proposed new tasks/2026-07-01-TASK-UPDATE-2/`):
1. Synthesis report: `2026-07-01-RESEARCH-SYSTEM-ORCHESTRATOR-VALIDATION.md`
   - Confirms/refutes Gemini's 9 gaps
   - Classifies each gap as: CONFIRMED | REFUTED | NEEDS_CLARIFICATION
   - Provides evidence (code quotes, file paths)

2. Draft task file(s): `2026-07-01-[PRIORITY]-[TYPE]-[NAME].md`
   - Refined versions of Gemini's 4 tasks (incorporating your research)
   - Any NEW tasks discovered during research
   - Using TASK_TEMPLATE.md structure

3. Optional summary: `TASK_REFACTOR_SUMMARY.md`
   - Shows task count (still 4? or 5-7?), dependencies, priorities

OUTPUT:
- NO implementation or code changes
- NO git commits
- NO Docker/test commands
- Just research + draft task files

FILES YOU'LL NEED TO READ (pasted below or linked):
[User pastes the stale tasks, gap analysis, and proposed tasks here]

START:
1. Answer the intake checklist in chat (confirm you understand your role)
2. Tell me when you're ready to begin research
3. I'll provide the detailed task context
```

---

## What Happens Next

1. **Web agent reads the dispatch** in chat
2. **Answers the intake checklist** — confirms they understand:
   - Their role is PLANNING/RESEARCH (not implementation)
   - Outputs go to `proposed new tasks/2026-07-01-TASK-UPDATE-2/`
   - They do NOT run code/tests/commits
3. **You confirm** — "Looks good, proceed with research"
4. **Agent researches** — Reads Gemini's work, audits codebase, creates synthesis + draft tasks
5. **Agent saves locally** — Files go to the folder
6. **Agent reports completion** — "Research complete, synthesis report + 5 draft tasks saved to [path]"
7. **You review locally** — Check synthesis report and draft tasks
8. **Claude reviews** — Final approval/refinement before implementation agents pick up

---

## Key Differences vs. Implementation Agent

| Aspect | Planning Agent | Implementation Agent |
|--------|---|---|
| **Reads** | Architecture docs, existing code | Same + detailed specs, factories, fixtures |
| **Produces** | Synthesis reports, draft tasks | Working code, passing tests |
| **Runs** | Nothing (read-only) | RSpec, rake, Rails commands in Docker |
| **Commits** | No (files staged locally for review) | Yes (after human approval) |
| **Duration** | 1-2 hours | 4-8 hours per task |
| **Output Type** | Strategy + plans | Production code |

---

## Example: What Web Agent Sees in Chat

The web agent receives this workflow in a single chat message:

```
You are the PLANNING AGENT.

[Full dispatch text above — all in one message]

[Gemini's previous work pasted or linked]

Start by reading files 1-5 above, then answer the intake checklist in chat.
```

Agent responds:
```
✅ Intake Checkpoint Complete:

- [x] Read PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md
- [x] Read README.md (PLANNING role section)
- [x] Read GUARDRAILS.md
- [x] Read research_template.md
- [x] Read TASK_TEMPLATE.md
- [x] Understand role is PLANNING/RESEARCH, not implementation
- [x] Understand outputs go to proposed new tasks/2026-07-01-TASK-UPDATE-2/
- [x] Understand I do NOT implement, commit, or run tests

I'm ready to begin research.
```

Then you confirm:
```
Great! Now please:
1. Read Gemini's 4 proposed tasks (I'll paste them below)
2. Research each against current codebase
3. Validate the 9 identified gaps
4. Create synthesis report + refined draft tasks
5. Save to proposed new tasks/2026-07-01-TASK-UPDATE-2/

[Paste Gemini's previous work]
```

Agent researches and reports back:
```
Research complete. Saved to proposed new tasks/2026-07-01-TASK-UPDATE-2/:

✅ 2026-07-01-RESEARCH-SYSTEM-ORCHESTRATOR-VALIDATION.md
   - All 9 gaps validated/refuted with evidence
   - 4 gaps CONFIRMED, 3 REFUTED, 2 NEED_CLARIFICATION

✅ 2026-07-01-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md
✅ 2026-07-01-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md
✅ 2026-07-01-HIGH-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md
✅ 2026-07-01-MEDIUM-ARCHITECTURE-STRATEGIC-PLANNER-EXTRACTION.md
✅ 2026-07-01-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md
✅ 2026-07-01-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md
✅ 2026-07-01-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md

Ready for Claude review.
```

Then you review locally, and Claude does final approval.

---

## Files You Need to Provide to Agent

When dispatching to web agent, also paste or link:

1. **Gemini's session handoff**: `2026-06-30-1156-GEMINI-SESSION_HANDOFF_SUMMARY.md`
2. **Gap analysis**: `2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`
3. **Research audit**: `2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **Proposed tasks** (all files from `2026-06-30-TASK-UPDATE-1/`):
   - `2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md`
   - `2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md`
   - `2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md`
   - `2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md`
   - `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md`
   - `2026-06-30-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md`
   - `2026-06-30-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md`
   - `2026-06-30-LOW-PHASE-8B-DEFERRAL.md`

---

## Saving Outputs Locally

When agent completes and says "files saved," you navigate to:

```
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed\ new\ tasks/2026-07-01-TASK-UPDATE-2/
```

And you'll find:
- Research synthesis report
- Refined task files
- Optional summary

Then Claude reviews all of those before you move them to `active/` for implementation.

