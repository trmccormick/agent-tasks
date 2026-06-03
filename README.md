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

---

### 🔬 REVIEWER Role
**Typical Agent**: Claude (web)[cite: 3]  
**What it means**: High-level spot checks, architecture review, and complex task file validation[cite: 3]. This role utilizes limited messages per session for high-reasoning audits[cite: 3].

---

### 🌐 DOMAIN EXPERT Role
**Typical Agent**: Gemini (web)[cite: 3]  
**What it means**: Deep geological, astronomical, or game-balance domain expertise. No local file access — operates entirely as a high-level theoretical advisor.

---

## Agent Stack Quick Reference

| Agent | Role | Cost | Local File/Terminal Access |
|---|---|---|---|
| **Qwen3.5-9B (Local)** | STRATEGIST / EXECUTOR | Free/local | ✅ Yes (Custom Copilot Endpoint w/ Terminal Tool) |
| Codestral (M4) | EXECUTOR / ARCHITECT | Free/local | ✅ Yes (Custom Copilot Endpoint) |
| Qwen3.5-27B (Local) | EXECUTOR / AUDITOR | Free/local | ✅ Yes (Custom Copilot Endpoint w/ Terminal Tool) |
| Qwen2.5-3B (Local) | TEXT / CONFIG WORKER | Free/local | ✅ Yes (Custom Copilot Endpoint) |
| **GPT-5-mini (Cloud)** | EXECUTOR | 0x Tier | ✅ Yes (IDE Workspace Only) |
| **Raptor mini (Preview)** | EXECUTOR | 0x Tier | ✅ Yes (IDE Workspace Only) |
| Claude (web) | REVIEWER | Free tier | ❌ No |
| Gemini (web) | DOMAIN EXPERT / STRATEGIST | Free | ❌ No |
| Perplexity (web) | RESEARCHER | Free | ❌ No |

---

## Project-Specific Configuration Notes

Each project may have unique configuration requirements. Refer to the project-specific documentation in:
- `/docs/new_agent/projects/[project_name]/` for project-specific guides

For example, Galaxy Game has a dedicated Docker command guide at:
- `/docs/new_agent/projects/galaxy_game/DOCKER_COMMAND_GUIDE.md`

---

## Hard Rules (Non-Negotiable)

- **No Continue Sidebar**: All local models must be invoked directly inside the editor via GitHub Copilot custom agent commands.
- **The Verified Execution Rule**: Local models utilizing terminal privileges MUST use them to verify file paths and run targeted container specs instead of fabricating or assuming test outcomes.
- **Targeted Testing Only**: Executors must never run a project's full test suite. Only run specific, targeted specs relevant to the task file.
- **Host-Only Supervised Git Commits**: Because Git is not accessible inside the Docker containers, all version control operations must be executed directly on the host node (Intel Mac). Agents may prepare and execute staging and commits under direct human supervision on the host, but they are strictly prohibited from attempting Git commands inside Docker.
- **No JSON Commits**: Do not stage or commit raw JSON data files at this time. Version control is strictly reserved for application code, tests, and markdown documentation files.
- **No Multi-Project Cross-Pollination**: Keep context isolated to the active target project repo directory. Do not reference directories from other active systems unless explicitly outlined in a task blueprint.
