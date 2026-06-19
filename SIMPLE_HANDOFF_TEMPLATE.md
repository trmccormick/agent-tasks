# Simple Handoff Template — 2-4 Lines Only

**Copy-paste friendly format for chat sessions. All details belong in task file.**

---

## Template

You are **Implementation Agent**.

Project: [PROJECT_NAME]

Agent-tasks repository: /Users/tam0013/Documents/git/agent-tasks/

Task file (move from backlog to active): /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT_NAME]/tasks/backlog/[TASKFILE].md

Workflow reference: /Users/tam0013/Documents/git/agent-tasks/README.md (see "Task Completion Workflow" section)

Implement [TASK_DESCRIPTION].

---

## Example (Proven Working Format)

You are **Implementation Agent**.

Project: wvulibraries_authentication

Agent-tasks repository: /Users/tam0013/Documents/git/agent-tasks/

Task file (move from backlog to active): /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_authentication/tasks/backlog/2026-06-19-MEDIUM-MAINTENANCE-SECURITY-AUDIT-OWASP.md

Workflow reference: /Users/tam0013/Documents/git/agent-tasks/README.md (see "Task Completion Workflow" section)

Implement Seq 4 security audit.

---

## Rules

✅ **Keep handoff to 2-4 lines max** — Must be copy-paste friendly across chat sessions
✅ **All details in task file** — Comprehensive context, target files, requirements, testing instructions
✅ **If handoff gets long** — Move content to task file OR split into multiple smaller tasks
✅ **Task file is source of truth** — Executor reads full details there, not in handoff
✅ **Handoff is dispatch only** — "Read this task file and implement it"