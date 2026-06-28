# Simple Handoff Template
**Use this for straightforward task assignments — copy and paste into chat**

---

```
You are the [ROLE: Research/Architecture | Implementation | Planning | Review] Agent.

Project: [project_name]

Task file (backlog): /Users/tam0013/Documents/git/agent-tasks/projects/[project_name]/tasks/backlog/[FOLDER_IF_ANY]/[TASKFILE].md

READ FIRST:
1) /Users/tam0013/Documents/git/agent-tasks/README.md
2) /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
3) The task file above, then move it to active/ and update status to active.

REQUIRED: Create the STATUS SYNTHESIS REPORT in the task file before making any changes, then wait for human approval.
```

---

## When to Use This Template

✅ **Use SIMPLE_HANDOFF_TEMPLATE when:**
- Task scope is clear and well-defined in the task file
- No additional context needed beyond the task file
- Agent role is straightforward (Research, Implementation, Review, Planning)
- Handoff fits in one screen without scrolling

❌ **Use HANDOFF_TEMPLATE.md (advanced) when:**
- Task requires multi-step coordination or sequencing
- Complex architectural decisions or gotchas not covered in task file
- Multiple prerequisite files or external context needed
- Risk of confusion about scope or approach

---

## Template Fields

| Field | Example | Notes |
|-------|---------|-------|
| `[ROLE]` | Research/Architecture, Implementation, Planning, Review | Must match a role in README.md |
| `[project_name]` | galaxy_game, samvera_hyku, wvulibraries_acda_portal | Project folder name |
| `[FOLDER_IF_ANY]` | phase5+, backlog | Subfolder if task is organized; omit if flat |
| `[TASKFILE]` | 2026-06-26-HIGH-RESEARCH-STRATEGY-SELECTOR-ESCALATION.md | Full filename with date prefix |

---

## Critical Rules

1. **No extra information** — Agent reads full context from task file and README
2. **No duplication** — Everything except role/project/path goes IN the task file
3. **Concise** — Fits on one screen (mobile-friendly)
4. **Copy-paste ready** — Works across chat sessions without editing
5. **Synthesis gate mandatory** — Agent creates STATUS SYNTHESIS REPORT before doing work and waits for approval

---

## Example

```
You are the Implementation Agent.

Project: galaxy_game

Task file (backlog): /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-06-28-HIGH-FEATURE-CYCLER-HITCHHIKER-OPTIMIZATION.md

READ FIRST:
1) /Users/tam0013/Documents/git/agent-tasks/README.md
2) /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
3) The task file above, then move it to active/ and update status to active.

REQUIRED: Create the STATUS SYNTHESIS REPORT in the task file before making any changes, then wait for human approval.
```
