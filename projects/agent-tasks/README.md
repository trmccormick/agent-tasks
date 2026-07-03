# Agent-Tasks — Workflow Infrastructure Guide
**Last Updated**: July 3, 2026
**Populated By**: GitHub Copilot (Patched)
**Source**: Internal project knowledge

> This file provides domain context for any agent working on agent-tasks repository infrastructure.
> `@file` this into your Continue session before starting any agent-tasks workflow task.
> The agent-tasks repo is the shared workflow system for all projects (Galaxy Game, Samvera, WVU Libraries).

---

## What Agent-Tasks Is
The agent-tasks repository is **not a game project** — it is the shared infrastructure that governs how agents create, execute, review, and complete work across all projects. It contains:

- **Task templates** (TASK_TEMPLATE.md, SIMPLE_HANDOFF_TEMPLATE.md, HANDOFF_TEMPLATE.md)
- **Workflow rules** (GUARDRAILS.md, DECISIONS.md, AGENT_ROUTING.md)
- **Routing logic** (ROUTING_LOGIC.md, COMMUNICATION_PROTOCOL.md)
- **Per-project task tracking** (projects/galaxy_game/, projects/samvera_hyku/, etc.)
- **Workflow infrastructure tasks** (projects/agent-tasks/) — tasks that improve the workflow system itself

**Key distinction**: Work on `projects/agent-tasks/` modifies the workflow system, not game code. These are meta-tasks about how work gets done.

---

## Core Workflow Rules (Hard Rules)
All agents working on agent-tasks infrastructure must follow these rules:

- **Read-Only Audit First**: Any task that audits or reviews existing workflow docs starts as a read-only audit. No changes to docs, templates, or structure without explicit human approval after the audit identifies what needs fixing.
- **Canonical Paths**: Always use `/Users/tam0013/Documents/git/agent-tasks/` as the source of truth for workflow docs. `docs/new_agent/` in galaxyGame is a symlink — it reflects agent-tasks but is not the canonical repo.
- **Task File Conformance**: All task files must follow TASK_TEMPLATE.md filename convention (`YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTIVE-NAME.md`), include YAML frontmatter, and have required sections (gotchas, synthesis gate, stop conditions).
- **No Self-Validation**: No agent both does and validates its own work. Audit tasks produce recommendations; implementation tasks require separate review approval before changes.
- **Project-Agnostic Workflow**: Workflow docs must apply to all projects (Galaxy Game, Samvera, WVU Libraries). Project-specific overrides live in per-project READMEs, not in shared workflow docs.

---

## File Organization

```
agent-tasks/
├── README.md                    ← Main workflow guide (read first)
├── TASK_TEMPLATE.md             ← Task file standard
├── SIMPLE_HANDOFF_TEMPLATE.md   ← Concise handoff format
├── HANDOFF_TEMPLATE.md          ← Advanced handoff format
├── COMMUNICATION_PROTOCOL.md    ← Output formatting rules
├── rules/
│   ├── GUARDRAILS.md            ← Execution rules (hard rules)
│   ├── DECISIONS.md             ← Locked architectural decisions
│   └── AGENT_ROUTING.md         ← Routing tables
├── projects/
│   ├── galaxy_game/             ← Galaxy Game task tracking
│   ├── samvera_hyku/            ← SamHyku task tracking
│   ├── samvera_hyrax/           ← Hyrax task tracking
│   ├── wvulibraries_*/          ← WVU Libraries task tracking
│   └── agent-tasks/             ← Workflow infrastructure tasks (NEW)
│       └── tasks/
│           ├── backlog/
│           └── active/
└── archive/                     ← Retired workflow docs
```

---

## Workspace & Testing Protocols (Hard Rules)
To maintain workflow integrity, all agent actions on agent-tasks infrastructure must adhere to these rules:

- **Host-Only Git Operations**: All git operations happen on the host. Never attempt git inside Docker containers. Use `cd /Users/tam0013/Documents/git/agent-tasks` as the working directory for commits and pushes.
- **Symlink Awareness**: `docs/new_agent/` in galaxyGame is a symlink to agent-tasks. Editing files there is fine, but commits/pushes must be from the agent-tasks repo root to preserve correct metadata.
- **No Workflow Changes Without Approval**: Audit tasks produce recommendations only. Implementation of workflow changes requires separate task with explicit human approval.
- **Task File Naming Enforcement**: All new task files MUST follow `YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTIVE-NAME.md`. No date prefix = task must be reviewed before assignment, may be obsolete.

---

## What Not To Do
- **Never modify workflow docs during an audit** — produce recommendations only; changes require separate approved task
- **Never use galaxyGame/docs/new_agent paths for reading workflow docs** — use `/Users/tam0013/Documents/git/agent-tasks/` as canonical source
- **Never assume workflow docs are project-specific** — shared workflow docs apply to all projects; project overrides go in per-project READMEs
- **Never skip the synthesis gate** — any task that modifies workflow infrastructure requires a STATUS SYNTHESIS REPORT before changes
- **Never commit JSON data files** — version control is reserved for application code, tests, and markdown documentation only

---

## Task File Conformance Checklist
Before dispatching any task file from agent-tasks, verify:

- [ ] Filename follows `YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTIVE-NAME.md` convention
- [ ] YAML frontmatter present (`status`, `priority`, `type`, `system_domain`, `mvp_alignment`, `local_worker_safe`)
- [ ] Minimal handoff section included (copy-paste dispatch template)
- [ ] Prerequisites with sequential read order and exact paths
- [ ] Architecture Gotchas section (what NOT to do, why)
- [ ] Required Status Synthesis Report gate (before any work)
- [ ] Stop Conditions section (escalation triggers)
- [ ] Dependencies section (blocked by / blocks / related)
- [ ] Completion Report template (structured output format)

---

## Projects Under Agent-Tasks

| Project | Purpose | Task Scope |
|---------|---------|-----------|
| `galaxy_game/` | Galaxy Game development tasks | Game code, specs, architecture, data |
| `samvera_hyku/` | SamHyku project tasks | Hyku application work |
| `samvera_hyrax/` | Hyrax project tasks | Hyrax application work |
| `wvulibraries_*` | WVU Libraries tasks | Library system work |
| `agent-tasks/` | **Workflow infrastructure** | Task templates, guardrails, routing, handoff standards |

**When to use `projects/agent-tasks/`**: When the task modifies workflow docs, task templates, routing logic, or other agent-tasks infrastructure. This is NOT for game code tasks — those stay in their respective project folders.
