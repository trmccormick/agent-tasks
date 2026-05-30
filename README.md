# Galaxy Game — Agent Workspace (Project-Specific)
**Last Updated**: 2026-05-30

**Important**: The shared agent infrastructure lives in a separate repository.

---

## File Organization Pattern

### Shared Infrastructure Across Projects
**Location**: `/Users/tam0013/Documents/git/agent-tasks/`  
**Scope**: Rules, task templates, routing, protocols for ALL projects (Galaxy Game, Samvera, WVU)  
**Ownership**: Agent tooling (updated when workflow rules change)

### Project-Specific Data
**Location**: Each project repo (e.g., `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/`)  
**Scope**: Project baseline, session status, blockers, domain context  
**Ownership**: Project-specific (updated during each session)  
**Key File**: `status.md` — Living document tracking RSpec baseline, completed work, in-progress tasks, blockers

### Task Files
**Location**: `/Users/tam0013/Documents/git/agent-tasks/projects/[project]/tasks/[backlog|active|completed]/`  
**Scope**: Formal feature/refactor assignments with detailed specs  
**Status**: Lifecycle tracked via YAML header (backlog → active → completed)  
**Assignment**: Agents move tasks between states and fill completion reports

---

## Where to Find Agent Guidance

### ⭐ Shared Agent Workspace (All Projects)
**Location**: `/Users/tam0013/Documents/git/agent-tasks/`  
**Purpose**: Rules, routing, guides, protocols for all projects (Galaxy Game, Samvera, WVU)

**Contains:**
- `README.md` — Main agent workspace guide (READ THIS FIRST)
- `ROUTING_LOGIC.md` — Quick-reference routing
- `rules/GUARDRAILS.md` — Execution rules
- `rules/DECISIONS.md` — Locked architectural decisions
- `rules/AGENT_ROUTING.md` — Full routing table
- `agent_guides/` — Work project guides
- `TASK_TEMPLATE.md` — Template for new tasks
- `COMMUNICATION_PROTOCOL.md` — Agent output format rules
- `SESSION_STRATEGIST.md` — Strategist role definition and workflow

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session, not per model. The same model can act as strategist in one session and executor in another. **Read your assigned role before doing anything else.**

---

### 🧭 STRATEGIST Role
**Typical agent**: Haiku (Copilot), Claude (web)  
**What it means**: You are the human's thinking partner. You triage, plan, delegate, and track. You do not implement.

| ✅ In Scope | ❌ Out of Scope — DELEGATE IMMEDIATELY |
|---|---|
| Read failure logs and triage | Write Ruby/Rails application code |
| Maintain session priority stack | Write or edit spec files |
| Create and update task files | Run docker exec commands |
| Move tasks between backlog/active/completed | Run RSpec tests |
| Assign work to Executor agents | Apply patches or fixes |
| Fill in session handoff document | Make architectural decisions alone |
| Update DECISIONS.md with locked decisions | Commit or push changes |
| Direct Executor agents with exact context | |

> ⚠️ **VIOLATION WARNING**: If you are in STRATEGIST role and find yourself writing code, editing spec files, running docker commands, or applying fixes — STOP. You are violating your session mandate. Produce a handoff for an EXECUTOR instead.

**Strategist must update these files before ending every session:**
- `projects/galaxy_game/status.md` — update baseline and current state
- Session handoff document — fill in completions, decisions, tomorrow's priorities
- Any task files moved from backlog → active → completed
- `rules/DECISIONS.md` — if any new architectural decisions were made

---

### ⚙️ EXECUTOR Role
**Typical agent**: GPT-4.1 (until June 2026), Copilot agent, Continue local agents  
**What it means**: You implement assigned tasks. You do not self-assign, plan, or make architectural decisions.

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Implement the assigned task file exactly | Self-assign new work |
| Run targeted specs inside Docker | Make architectural decisions |
| Produce synthesis reports before applying | Modify files outside task scope |
| Report blockers immediately | Override stop conditions |
| Fill in completion report | Plan or strategize |

> ⚠️ **STOP CONDITIONS**: Stop and escalate to Strategist immediately if:
> - Same failure persists after two attempts
> - Fix causes new failures in files you did not touch
> - Root cause is in a shared concern or base class
> - A migration is needed that wasn't anticipated
> - Any architectural decision is required

---

### 🔬 REVIEWER Role
**Typical agent**: Claude (web)  
**What it means**: High-level spot checks, architecture review, complex task file creation. Not sustained planning — limited messages per session.

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Architecture decisions and review | Sustained session planning |
| Complex task file creation | Repetitive implementation work |
| Backlog audit and triage | Running tests or commands |
| Design decisions requiring deep reasoning | Primary session strategist role |

---

### 🌐 DOMAIN EXPERT Role
**Typical agent**: Gemini (web)  
**What it means**: Geological/scientific domain expertise, game balance, session planning at high level. No local file access — cannot read codebase or update task files.

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Geological plausibility rules | Reading local files |
| Game balance decisions | Updating task files |
| High-level session planning | Triaging spec failures |
| Scientific research questions | Codebase-specific decisions |

---

## Current MVP Focus — Luna

**Win condition**: Get LDC (Lunar Development Corporation) running on Luna.

```
LDC established → GCC minted
→ ISRU producing (TEU/PVE bulk regolith)
→ GuaranteedMarketSale working (GCC flows to players)
→ HLT tested (Luna surface ↔ L1)
→ L1 depot construction starts
→ Luna MVP complete
```

**Do not get distracted by**: wormholes, Venus, Mars, cyclers, tugs, asteroid mining, or any post-Luna work until this loop is proven.

---

## Current Baseline & Session Status

**Location**: `projects/galaxy_game/status.md`

**What it contains:**
- RSpec baseline (example count, failure count, pending count)
- Completed work with commit hashes
- In-progress tasks and blockers
- Backlog (formal tasks, not yet active)
- Session notes and decisions

**Update rule**: Strategist updates at end of each session (mandatory).

**For other projects**: Look in `projects/[project_name]/status.md` (same structure, per-project).

---

## Galaxy Game Project-Specific Files (This Folder)

```
docs/new_agent/
├── README.md                           ← you are here
├── agent_guides/
│   └── galaxy_game.md                  ← Galaxy Game domain context
└── projects/
    └── galaxy_game/
        ├── status.md                   ← Project baseline & current state
        ├── context/
        │   ├── CODEBASE_MAP.md        ← Where code lives
        │   └── PATTERNS.md             ← Code patterns in use
        └── research/
            └── LUNAR_GEOSPHERE_BASE.md ← Lunar resource baseline
```

---

## Task Files

**Galaxy Game tasks** are in the separate `agent-tasks` repository:

```
/Users/tam0013/Documents/git/agent-tasks/galaxy_game/
├── active/           ← Current sprint work
├── backlog/          ← Planned but not started
│   └── 2026-05/      ← May 2026 backlog
├── completed/        ← Finished tasks with completion reports
├── planning/         ← Long-term strategy
└── testing/          ← Testing-focused tasks
```

---

## Before Starting Any Galaxy Game Task

### Standard Workflow: Task File + Handoff

**Task Assignment Flow:**
1. **Task file** lives in `/Documents/git/agent-tasks/galaxy_game/tasks/backlog/` (detailed specs)
2. **Handoff** is provided as text in conversation (role reminder + quick instructions)
3. **Agent reads both** before starting work
4. **Agent moves task** from backlog → active → completed as work progresses
5. **Agent delivers** completion report

**Task File Contains:**
- Executive summary
- Technical specification
- Implementation requirements
- Test expectations
- Success criteria
- References and dependencies

**Handoff Text Contains:**
- Role reminder (EXECUTOR, STRATEGIST, etc.)
- What to read first (README.md, rules)
- Task file location
- Target files to verify/modify
- Quick requirements summary
- Stop conditions
- Output format expected

### Before Starting: Checklist

1. **Read shared agent workspace**: `~/Documents/git/agent-tasks/README.md`
2. **Read your session role**: section above — STRATEGIST, EXECUTOR, REVIEWER, or DOMAIN EXPERT
3. **Check Galaxy Game status**: `projects/galaxy_game/status.md`
4. **Understand Galaxy Game context**: `projects/galaxy_game/context/CODEBASE_MAP.md`
5. **Review rules**: `~/Documents/git/agent-tasks/rules/GUARDRAILS.md`
6. **Review locked decisions**: `~/Documents/git/agent-tasks/rules/DECISIONS.md`
7. **Read your task file**: Located in handoff text, usually in `~/Documents/git/agent-tasks/galaxy_game/tasks/[status]/[TASK].md`

---

## Before Starting Any Galaxy Game Task

1. **Read shared agent workspace**: `~/Documents/git/agent-tasks/README.md`
2. **Read your session role**: section above — STRATEGIST, EXECUTOR, REVIEWER, or DOMAIN EXPERT
3. **Check Galaxy Game status**: `projects/galaxy_game/status.md`
4. **Understand Galaxy Game context**: `projects/galaxy_game/context/CODEBASE_MAP.md`
5. **Review rules**: `~/Documents/git/agent-tasks/rules/GUARDRAILS.md`
6. **Review locked decisions**: `~/Documents/git/agent-tasks/rules/DECISIONS.md`
7. **Read your task file**: `~/Documents/git/agent-tasks/galaxy_game/[status]/[TASK].md`

---

## Agent Stack Quick Reference

| Agent | Role | Cost | Local Files |
|---|---|---|---|
| Claude (web) | REVIEWER | Free tier | ❌ |
| Haiku (Copilot) | STRATEGIST | 0.33x | ✅ |
| GPT-4.1 (Copilot) | EXECUTOR | 0x until June 2026 | ✅ |
| Gemini (web) | DOMAIN EXPERT | Free | ❌ |
| Qwen3.5 27B (M4) | EXECUTOR/AUDITOR | Free/local | ✅ via Continue |
| Qwen3.5 9B (Windows) | EXECUTOR | Free/local | ✅ via Continue |
| Qwen2.5-Coder 32B (Windows) | EXECUTOR | Free/local | ✅ via Continue |
| Codestral (M4) | EXECUTOR/ARCHITECT | Free/local | ✅ via Continue |
| Perplexity (web) | RESEARCH | Free | ❌ |

> ⚠️ **June 2026 note**: GPT-4.1 moves to token-based billing June 1st. Routing table will be updated after transition impact is assessed.

---

## Hard Rules (Non-Negotiable)

- M4 must stay caffeinated (`caffeinate` / `pmset`) for stable Ollama connection
- All codebase-wide scans go to local models — never send full codebase to cloud
- Cloud agents receive only specific file snippets needed for the task
- One RSpec runner at a time — never run parallel spec execution
- Commits always from Intel Mac (host/orchestration node)
- `unset DATABASE_URL` before every RSpec run — never omit this
- Synthesis report before any code change — no exceptions

---

## Multi-System Setup

**All Systems Access:**
- Intel Mac (home) — orchestration, git commits
- M4 MacBook (portable) — Ollama M4 node, Continue
- Windows Ryzen (home) — Ollama Windows node, large models

**Shared Workspace Location:**
- `~/Documents/git/agent-tasks/` (git-based, cloned on each system)
- Always `git pull` before starting a session

---

## References

- **Agent-tasks repository**: https://github.com/trmccormick/agent-tasks
- **Session Strategist guide**: `~/Documents/git/agent-tasks/SESSION_STRATEGIST.md`
- **Shared agent workspace**: `~/Documents/git/agent-tasks/README.md`
- **Galaxy Game domain guide**: `agent_guides/galaxy_game.md`
- **Galaxy Game status**: `projects/galaxy_game/status.md`
- **Luna Base Establishment**: `projects/galaxy_game/research/LUNA_BASE_ESTABLISHMENT.md`
