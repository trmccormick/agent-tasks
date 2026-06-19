# Agent Workspace — Generic Guides for Multi-Project Workflow
**Last Updated**: 2026-06-02

**Purpose**: Generic workflow guidance for ANY project (Galaxy Game, Samvera, WVU Libraries, etc.)  
**Shared Infrastructure**: Lives in `/Users/tam0013/Documents/git/agent-tasks/` (rules, routing, task templates for all projects)

---

## File Organization Pattern

### Shared Infrastructure Across Projects
**Location**: `/Users/tam0013/Documents/git/agent-tasks/`  
**Scope**: Rules, task templates, routing, and core execution protocols for ALL projects.  
**Ownership**: Agent tooling (updated when core workflow rules change)[cite: 3].

### Project-Specific Data (Managed Inside Individual Repositories)
**Location**: Look inside the respective project's documentation folder.  
**Scope**: Project baseline, session status, blockers, and domain-specific context.  
**Ownership**: Project-specific (updated during each development session).  
**Key File**: `status.md` — Living document tracking test baselines, completed work, in-progress tasks, and blockers[cite: 3].

### Task Files (Shared Backlog Repository)
**Location**: `/Users/tam0013/Documents/git/agent-tasks/projects/[project_name]/tasks/[backlog|active|completed]/`[cite: 3]  
**Scope**: Formal feature or refactor assignments containing detailed technical specifications[cite: 3].  
**Status**: Lifecycle tracked via YAML header (`status: backlog` ➔ `status: active` ➔ `status: completed`)[cite: 3].

## Symlinked Task Repo — Managing Task Files
NOTE: In this workspace `docs/new_agent` is a filesystem symlink that points to the shared task repository at `/Users/tam0013/Documents/git/agent-tasks`. Editing files under `docs/new_agent` is fine, but commits and pushes must be performed from the `agent-tasks` repository on the host.

Recommended workflow for executors and strategists:

- Edit or create task files in `docs/new_agent/projects/galaxy_game/tasks/...` (this updates the symlinked location).  
- When ready to commit, operate from the `agent-tasks` repo root to ensure correct Git metadata and remotes are used. Use quoted paths to avoid shell globbing. Example commands:

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git add 'projects/galaxy_game/tasks/completed/2026-06/2026-06-03-HIGH-BUGFIX-EXAMPLE.md'
git commit -m "Add completed task: 2026-06-03 spec health"
git push
```

- When moving a task between `backlog` → `active` → `completed`, update the YAML header (`status:`) in the task file before committing.
- After committing task file changes, update `docs/new_agent/projects/galaxy_game/status.md` in the repo where you edited the file (the symlink target) to reflect progress and decisions.

Why this matters:
- Committing from the `agent-tasks` repo root preserves the correct repository metadata and remote configuration. Committing from the symlinked workspace path can result in files being added to the wrong repository or failing due to path mismatches.


---

## Where to Find Agent Guidance

### ⭐ Shared Agent Workspace (All Projects)
**Location**: `/Users/tam0013/Documents/git/agent-tasks/`[cite: 3]  
**Contains:**
- `README.md` — Main agent workspace guide (READ THIS FIRST)[cite: 3]
- `ROUTING_LOGIC.md` — Quick-reference routing table and model stack
- `rules/GUARDRAILS.md` — Core execution and security rules[cite: 3]
- `rules/DECISIONS.md` — Locked cross-project architectural decisions[cite: 3]
- `rules/AGENT_ROUTING.md` — Full routing table matrices[cite: 3]
- `TASK_TEMPLATE.md` — Template blueprint for constructing new tasks[cite: 3]
- `COMMUNICATION_PROTOCOL.md` — Agent output formatting rules[cite: 3]
- `SESSION_STRATEGIST.md` — Detailed strategist role definition and workflow[cite: 3]

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session, not per model[cite: 3]. The same model can act as strategist in one session and executor in another[cite: 3]. **Read your assigned role before doing anything else.**[cite: 3]

---

### 🧭 STRATEGIST Role
**Typical Agent**: Qwen3.5-9B (Copilot Endpoint), Gemini (web)[cite: 3]  
**What it means**: You are the human's thinking partner[cite: 3]. You triage failures, inspect logs via terminal tools, plan, delegate, and track status. You do not implement application code files in this role[cite: 3].

| ✅ In Scope | ❌ Out of Scope — DELEGATE IMMEDIATELY |
|---|---|
| Use `run_in_terminal` to read failure logs and triage | Write or modify application production code[cite: 3] |
| Maintain the session priority stack[cite: 3] | Apply direct patches or active fixes to codebases[cite: 3] |
| Create and update project task files[cite: 3] | Move tasks without updating the tracking index[cite: 3] |
| Move tasks between backlog/active/completed[cite: 3] | Make architectural decisions alone[cite: 3] |
| Assign formal work to Executor agents via task files[cite: 3] | Commit or push version control changes[cite: 3] |

---

### ⚙️ EXECUTOR Role
**Typical Agent**: GPT-5-mini / Raptor mini (Cloud 0x Tier), Qwen3.5-9B / 27B (Local Implementation)  
**What it means**: You implement assigned tasks exactly[cite: 3]. You do not self-assign new work, plan, or make structural architectural changes[cite: 3].

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Implement the assigned task file exactly[cite: 3] | Self-assign new work or cross boundaries[cite: 3] |
| Run **targeted tests ONLY** via terminal for your feature[cite: 3] | Run full, project-wide RSpec/test suites[cite: 3] |
| Produce an accurate technical synthesis report before editing[cite: 3] | Override strict stop conditions[cite: 3] |
| Report blockers and file errors immediately[cite: 3] | Scaffold unapproved migrations or database specs[cite: 3] |

#### Task Completion Workflow (Executor Only)

When you finish implementing a task, follow this **exact sequence**:

**Step 1: Move task from active/ → completed/**
```bash
cd /Users/tam0013/Documents/git/agent-tasks/projects/[project]/tasks/
mv active/[TASKFILE].md completed/[TASKFILE].md
# Update YAML header in the file: change status: active to status: completed
```

**Step 2: Commit task file move**
```bash
git add completed/[TASKFILE].md
git commit -m "task: Complete [TASK_TITLE]

[2-3 bullet points of what was accomplished]"
git push origin main
```

**Step 3: Update project status.md** (located in the project repo, not agent-tasks)
Navigate to: `[project_repo]/doc/status.md`
- Add completed task summary
- Update "Last Updated" timestamp
- Note any deferred work or Phase 2 items

**Step 4: Commit status.md update**
```bash
cd /Users/tam0013/Documents/git/[project]
git add doc/status.md
git commit -m "docs: Update status.md — [TASK] complete"
git push origin [working_branch]
```

**Step 5: Report to Strategist**
Provide brief summary:
- ✅ What was completed
- ⚠️ What (if anything) was deferred and why
- 📋 Recommendation on next sequence (if applicable)

---

### 🔬 REVIEWER Role
**Typical Agent**: Claude (web)[cite: 3]  
**What it means**: High-level spot checks, architecture review, and complex task file validation[cite: 3]. This role utilizes limited messages per session for high-reasoning audits[cite: 3].

---

### 🌐 DOMAIN EXPERT Role
**Typical Agent**: Gemini (web)[cite: 3]  
**What it means**: Deep geological, astronomical, or game-balance domain expertise. No local file access — operates entirely as a high-level theoretical advisor.

---

## Agent Stack Quick Reference (Updated June 2026)

**Primary Workflow**: Single-model sessions using Qwen3.5-27B on M4 Mac to avoid token limit errors when switching models in VS Code extension.

| System | Model | Role | Cost | Local File/Terminal Access | Notes |
|---|---|---|---|---|---|
| **M4 Mac (Primary)** | Qwen3.5-27B | Planning Agent / Executor / Auditor | Free/local | ✅ Yes | Default for 90% of tasks; fast inference, good reasoning/speed balance |
| M4 Mac (Fallback) | Qwen3.5-9B | Quick config/JSON edits only if needed | Free/local | ✅ Yes | Use sparingly — model switching causes "Response too long" errors in VS Code extension due to context accumulation |
| **Ryzen 7 System (Secondary)** | Llama 3.1 / Mixtral or larger models with extended context windows | Complex synthesis & large-context analysis when M4 hits token limits | Free/local | ✅ Via SSH or separate session | Use for: multi-file architectural reviews, full RSpec suite output triage (>8K tokens), research-heavy tasks requiring external docs + codebase in single pass; slower inference but handles larger contexts without truncation errors |
| Cloud (Fallback Only) | GPT-5-mini / Raptor mini | Two-local-failure fallback only | 0x Tier | ✅ Yes (IDE Workspace Only) | Reserved for when both M4 and Ryzen systems fail on same task — token billing applies June 2026+ |

### Why Single-Model Workflow?
**Problem**: VS Code's Copilot Chat extension has ~8K token output limit. Switching between local Ollama models accumulates context from previous conversations, hitting "Response too long" errors mid-session (see error: `Copilot Request id: 2bc8c144-... Reason: Response too long`).

**Solution**: Use Qwen3.5-27B as primary workhorse for entire sessions without model switching. Only switch to Ryzen system when task complexity exceeds M4's token capacity (e.g., analyzing full RSpec suite output with 4K examples).

---

## Project-Specific Configuration Notes

Each project may have unique configuration requirements. Refer to the project-specific documentation in:
- `/docs/new_agent/projects/[project_name]/` for project-specific guides

For example, Galaxy Game has a dedicated Docker command guide at:
- `/docs/new_agent/projects/galaxy_game/DOCKER_COMMAND_GUIDE.md`

---

## Agent Handoff Generation Process (Updated June 2026)

**Core Pattern**: Handoffs must be **2-4 lines max** (copy-paste friendly). All details belong in task files.

### Why This Pattern?
- Handoffs get copied across chat sessions and long text causes issues
- Task files are persistent, detailed repositories of work
- Handoff is dispatch only: "Read this task and implement it"
- Planning agent documents everything in task file, not handoff

### Handoff Template (Proven Working Format)
```
You are **Implementation Agent**.

Project: [PROJECT_NAME]

Agent-tasks repository: /Users/tam0013/Documents/git/agent-tasks/

Task file (move from backlog to active): /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT_NAME]/tasks/backlog/[TASKFILE].md

Workflow reference: /Users/tam0013/Documents/git/agent-tasks/README.md (see "Task Completion Workflow" section)

Implement [TASK_DESCRIPTION].
```

### Key Elements That Must Be Explicit
✅ **Project name** — Tells agent which project folder to use  
✅ **Full task file path** — Points to backlog location (agent moves to active)  
✅ **Agent-tasks repository path** — Anchor point for all relative paths  
✅ **Workflow reference** — Directs agent to README.md task completion section  
✅ **Plain text, no links** — Copy-paste friendly for cross-session use  

### When to Split Tasks
If the handoff would be longer than the template above, don't expand it — instead:
- **Move content to task file** (comprehensive details there)
- **OR split into multiple smaller tasks** (Phase 1, Phase 2, etc.)
- **Never make handoffs long** — they must remain copy-paste friendly

### Task File Checklist
✅ Clear objective and success criteria  
✅ Complete list of target files  
✅ Implementation scope (detailed bullets)  
✅ Testing strategy and expectations  
✅ Known blockers or dependencies  
✅ Phase structure (if multi-phase work)  

Task files are the source of truth. Executors read full context there.

---

## 📋 PLANNING Agent Role (You)

**When you act as the Planning Agent:**
- You are responsible for generating agent handoffs after task decisions are made
- **Handoffs must be 2-4 lines max** (copy-paste friendly across chat sessions)
- **All details belong in task files** — task files are persistent knowledge repositories
- Most task files are generated by Claude (web) via research and analysis
- Refactored tasks may be reviewed by various reviewing agents before implementation

**Handoff Generation Workflow:**
1. **Task Decision Made**: After task decisions finalized
2. **Comprehensive Task File**: Write full details there (scope, targets, requirements, testing, phases)
3. **Minimal Handoff**: Generate 2-4 line dispatch message using SIMPLE_HANDOFF_TEMPLATE.md
4. **If handoff gets long**: Move content to task file OR split into multiple tasks
5. **Copy-paste friendly**: Handoff must work across chat sessions without truncation

**What NOT to do:**
- ❌ Don't expand handoffs beyond 4 lines
- ❌ Don't assume Implementation Agent will figure it out without task file
- ❌ Don't put complex context in handoff (use task file)
- ❌ **Don't perform synthesis reports yourself** — that's Implementation Agent's job (reads task file, does synthesis before coding)
- ❌ **Don't read target files and analyze code unless writing task file** — let Implementation Agent inspect code

**Your Job**: Write comprehensive task files. Keep handoffs minimal.

---

## Hard Rules (Non-Negotiable)

- **No Continue Sidebar**: All local models must be invoked directly inside the editor via GitHub Copilot custom agent commands.
- **The Verified Execution Rule**: Local models utilizing terminal privileges MUST use them to verify file paths and run targeted container specs instead of fabricating or assuming test outcomes.
- **Targeted Testing Only**: Executors must never run a project's full test suite. Only run specific, targeted specs relevant to the task file.
- **Host-Only Supervised Git Commits**: Because Git is not accessible inside the Docker containers, all version control operations must be executed directly on the host node (Intel Mac). Agents may prepare and execute staging and commits under direct human supervision on the host, but they are strictly prohibited from attempting Git commands inside Docker.
- **No JSON Commits**: Do not stage or commit raw JSON data files at this time. Version control is strictly reserved for application code, tests, and markdown documentation files.
- **No Multi-Project Cross-Pollination**: Keep context isolated to the active target project repo directory. Do not reference directories from other systems unless explicitly outlined in a task blueprint.

---

## Planning Agent / Session Strategist Role Convergence (June 2026)

**Status: LIVING DOCUMENT — Update as workflow evolves.**

### Why Roles Converge
In this workspace, the **Planning Agent** and **Session Strategist** roles have converged into a single agent function. This document clarifies how these duties work together going forward.

| Responsibility | Planning Agent Focus | Session Strategist Focus | Combined Execution |
|---|---|---|---|
| Task file creation | Draft task files from research/analysis or backlog review | — | Same output: structured task files in agent-tasks repo |
| Handoff generation | Generate handoffs for Implementation Agents using templates | Produce executor assignment messages with exact context | Identical process; same template selection logic (SIMPLE_HANDOFF_TEMPLATE.md vs HANDOFF_TEMPLATE.md) |
| Triage & prioritization | Assess backlog, identify blockers during planning phase at session start/transition points | Maintain priority stack during active sessions when Executors are working | Same triage table output; Planning Agent does this first thing at startup and after task completion events |
| Failure analysis | Review logs when new tasks are scoped for investigation or architectural decisions needed | Interpret root causes from error output before delegating to Executor agents | Both read logs via `run_in_terminal`; same triage rules apply (flaky specs ignored, regressions flagged first) |
| Status tracking | Update status.md with baseline and completed work during planning phases | Maintain session handoff document at end of each active implementation session | Combined: update both files as needed; Planning Agent handles this when no active implementation is running or after sessions complete |

### When Each "Mode" Activates

**Planning Mode:**
- At session startup, before any tasks are assigned to Executors
- Reviewing backlog for new task opportunities (Luna Phase 3+, future features) from `agent-tasks/galaxy_game/tasks/backlog/` or status.md notes
- Analyzing architectural decisions or research notes from Domain Expert agents when scoping NEW work
- Drafting initial task files based on MVP focus and project status — **only read code if you need to write accurate target file paths in the task itself** (e.g., "app/services/logistics/shortage_detector.rb" as a reference)
- Generating handoffs once a task is ready to be implemented using SIMPLE_HANDOFF_TEMPLATE.md or HANDOFF_TEMPLATE.md

**Session Strategist Mode:**
- During active implementation sessions when Executor agents are working on tasks assigned via handoff messages
- Triage table generation for failures that appear during the session (regressions, new errors from patches)
- Priority stack maintenance as work completes or blockers emerge from task completion events
- Directing Executors with exact context: task file path, full error output, target files to inspect — **read code/logs via terminal tools only when triaging active failures**
- Ending session and producing handoff document before closing

**Combined Workflow:**
In most sessions, a single agent operates in both modes sequentially:
1. **Start**: Planning mode — review status.md for baseline, check `agent-tasks/galaxy_game/tasks/backlog/` or `/active/`, identify next task(s) based on MVP focus (Luna), select existing task file OR draft new one if backlog is empty; generate handoff using appropriate template
2. **Handoff generation**: Create structured message for Implementation Agent — include exact paths, scope bullets from task file, testing requirements; present to user for approval before sending to implementation agent
3. **During implementation** (if session continues): Session Strategist mode begins once handoff is sent and Executor starts work — monitor progress via terminal logs with `get_terminal_output`, handle regressions that appear during their coding phase, update priority stack if blockers emerge from task completion events; use `run_in_terminal` to read failure logs and triage as needed
4. **End of session**: Update status.md (baseline, completed work summary) and produce handoff document in `agent-tasks/galaxy_game/planning/session_handoff_YYYY-MM-DD.md`; return to Planning mode at next startup

**Critical Boundary:** Once you generate a handoff message for an Implementation Agent, STOP. Do not read their target files or analyze code unless:
- You're drafting the task file itself (need accurate paths)
- The session continues and Executor reports failures requiring triage in Session Strategist mode

### Key Distinction from Traditional "Strategist" Role

Traditional AI agent workflows often separate planning (LLM reasoning without tool access) from execution (code generation with tools). In this workspace:

- The same local model (**Qwen3.5-9B** or **Qwen3.5-27B on M4 Mac**) performs both Planning and Session Strategist duties
- No architectural separation is needed because the agent has full terminal access to verify file paths, run targeted specs via `docker exec`, and read logs before delegating fixes
- Human oversight remains at handoff approval gate — agents never self-deploy new tasks without human review using the templates provided

### Documentation Updates Needed (June 2026)

The following documents should be updated or created to ensure consistency across sessions:

1. **SESSION_STRATEGIST.md** — Already exists; add section clarifying Planning Agent convergence and combined workflow patterns
2. **NEW_PLANNING_AGENT_GUIDE.md** — New document for agents being assigned to this role, covering task file drafting patterns (backlog review vs research-driven), handoff generation workflow using templates, triage rules at session start/end points
3. **WORKFLOW_TRANSITION_PROTOCOLS.md** — Document how the agent transitions between planning mode (task scoping) and session strategist mode (active triage during implementation), including when to switch focus based on task completion events or new failure reports

These updates will ensure consistency across sessions and provide clear guidance for new agents joining this workspace.
