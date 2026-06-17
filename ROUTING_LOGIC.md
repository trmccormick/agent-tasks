# Routing Logic — Quick Reference
<<<<<<< Updated upstream
**Last Updated**: 2026-06-16
=======
**Last Updated**: 2026-06-15
>>>>>>> Stashed changes

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.
> This file is a quick-reference summary only.

---

## ⚠️ CURRENT STATE NOTICE (June 2026)

This document reflects a transitional setup following new hardware addition (Ryzen 7 16GB GPU) and GitHub Copilot model changes. The Pi 4 home server reload and Open WebUI aggregator deployment are **pending** — routing will be revisited once that infrastructure is live.

---

## Hardware Topology

| Machine | RAM | GPU | Role |
|---|---|---|---|
| **Intel MacBook** | — | — | Primary Galaxy Game dev surface. VS Code + GitHub Copilot. No local models — accesses M4 and Ryzen 7 remotely via Ollama. |
| **M4 Mac** (48GB unified) | 48GB | Apple Silicon | Primary WVU/Samvera work machine (wvu_knapsack/Hyku). Runs qwen3.5:27b concurrently with full Docker stack — validated stable with 12 containers (Solr, Fedora, PostgreSQL, Redis, Zookeeper, Selenium, Traefik + Rails web/worker). |
| **Ryzen 7** (128GB RAM) | 128GB | 16GB (primary) + 4GB (DVI display) | Gaming rig and model server. 16GB GPU handles model inference; 4GB card drives secondary display. Available during the day; gaming and model inference can run simultaneously. |
| **Pi 4** | — | — | Home server (Jellyfin, Samba via Docker). **Pending reload** — will host Open WebUI as unified Ollama aggregator. |

---

## The One Rule

**Route by task complexity, not by habit. 27B handles most things. Only reach for a larger or cloud model when there's a genuine reason.**

---

<<<<<<< Updated upstream
## Dual Workstream Setup
=======
## Hardware Topology (as of 2026-06-15)

### Ryzen 7 Windows 11 — Primary Ollama Host (evenings/weekends)
- **GPU**: XFX RX 9060 XT, 16GB VRAM (RDNA 4)
- **RAM**: 128GB
- **Model**: Qwen3.5-35B (primary heavy executor)
- **Role**: Heavy implementation, multi-file reasoning, full RSpec analysis, complex architecture
- **Ollama**: Exposed on LAN (`OLLAMA_HOST=0.0.0.0`, port 11434)
- **Access**: Direct (Windows) or remote from M4 via `http://[ryzen-ip]:11434`

### M4 MacBook — Primary Dev Machine (daytime work hours)
- **Role during work hours**: Non-galaxyGame work — do NOT compete for resources
- **Role during galaxyGame sessions**: Fast triage, planning, doc updates, light edits
- **Models**: Qwen3.5-9B (planning/triage), Qwen3.5-27B (fallback executor)
- **Copilot**: Can connect to Ryzen Ollama remotely for heavy tasks
- **Constraint**: During work hours, limit agent work to Ryzen remote or defer to evening

### Intel MacBook — Host Operations Only
- **Role**: Git commits, file management, no Ollama
- **All git operations run here** — never inside Docker

### Raspberry Pi 4 — Home Server
- **Services**: Jellyfin, Samba, Open WebUI (Ollama aggregator — pending reload)
- **Open WebUI**: Will aggregate Ryzen + M4 Ollama endpoints once Pi is reloaded
- **Status**: Reload pending — low priority until Ryzen stable

---

## Time-of-Day Routing

### Daytime (work hours — M4 in use for non-galaxyGame work)
- **Light tasks only** — triage, doc updates, planning
- Route to Ryzen via remote Ollama if heavy work needed
- Do NOT run full RSpec suites or long sessions on M4 during work hours
- Defer implementation tasks to evening session

### Evening/Weekend (galaxyGame sessions)
- Ryzen 7 Qwen3.5-35B = primary heavy executor
- M4 Qwen3.5-9B = planning, triage, fast edits
- Full sessions, implementation tasks, suite runs — all available

---

## Session Roles (Behavior-Based, Not Model-Specific)
>>>>>>> Stashed changes

The agent-tasks repo coordinates two concurrent workstreams across machines:

<<<<<<< Updated upstream
| Workstream | Primary Machine | Primary Agent | Repo |
|---|---|---|---|
| **Galaxy Game** | Intel MacBook (VS Code + Copilot) | qwen3.5:27b via M4 or qwen3.5:35b via Ryzen 7 | galaxygame |
| **WVU/Samvera** | M4 Mac (VS Code + Copilot) | qwen3.5:27b local | wvu_knapsack |
=======
| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Qwen3.5-9B (M4 Copilot) | Triage, plan, delegate, update task files — never execute code in this role |
| **EXECUTOR (Heavy)** | Qwen3.5-35B (Ryzen Copilot) | Complex implementation, multi-file work, full RSpec analysis — primary executor |
| **EXECUTOR (Light)** | Qwen3.5-27B (M4 Copilot) | Targeted edits, single file fixes — fallback when Ryzen unavailable |
| **EXECUTOR (Cloud)** | Claude Haiku / GPT-5 mini / Raptor mini | Only after two local failures across both systems |
| **REVIEWER** | Claude (web) | Architecture decisions, complex task creation — spot checks only |
| **DOMAIN EXPERT** | Gemini (web) | Geological/game balance expertise — no local file access |
| **RESEARCHER** | Perplexity | External documentation lookup |
| **DOCUMENTATION AGENT** | Gemini (Web - Context/Intent) / Qwen3.5-35B (Local - Tech Structure) | Parse repositories, maintain markdown guides, sync code realities with gameplay design rules |
>>>>>>> Stashed changes

These workstreams are independent — task files in agent-tasks are namespaced by repo. Agents working on Galaxy Game tasks should never touch wvu_knapsack files and vice versa.

---

## Active Agent Stack (June 2026)

| Agent | Where | Role | Cost |
|---|---|---|---|
| **Claude (web)** | claude.ai | Planning, architecture, task file creation, session strategy | Free tier |
| **Perplexity** | perplexity.ai | Agent handoff documents, external research | Free |
| **qwen3.5:27b** | M4 (local Ollama) | Primary implementation executor | Free/local |
| **qwen3.5:9b** | M4 (local Ollama) | Fast lightweight tasks, planning support | Free/local |
| **qwen3.5:35b-a3b** | Ryzen 7 (local Ollama) | Heavy execution — **under validation as of 2026-06-16** | Free/local |
| **Claude Haiku 4.5** | GitHub Copilot (0.33x) | Escalation when local gets stuck | 0.33x tier |

---

## Session Roles

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Claude (web) | Planning, architecture decisions, task file drafting, session strategy |
| **HANDOFF WRITER** | Perplexity (free) | Agent handoff documents between sessions |
| **EXECUTOR (Primary)** | qwen3.5:27b (M4) | Implementation, multi-file reasoning, spec work, terminal verification |
| **EXECUTOR (Light)** | qwen3.5:9b (M4) | Fast tasks — STATUS.md updates, task file edits, quick triage |
| **EXECUTOR (Heavy)** | qwen3.5:35b-a3b (Ryzen) | Complex implementation when 27B gets stuck — pending full validation |
| **ESCALATION** | Claude Haiku 4.5 (Copilot) | When local models fail after fresh session retry |

<<<<<<< Updated upstream
---

## Quick Routing Table
=======
### 1. The State-Tracking Triad
Every active development cycle requires updating three core files simultaneously to maintain project telemetry:
- **Task Files**: Must be built using `TASK_TEMPLATE.md` exactly, clearing out all old template suffixes. Every assignment must clearly define the scope, target specific RSpec files, and state explicit **Escalation Criteria**.
- **`STATUS.md` Updates**: The STRATEGIST must update the global tracking log whenever a task shifts from active to completed or when a block is cleared by a verified RSpec pass.
- **`DECISIONS.md` Logging**: Any architectural pivot, database model adjustment, or game balancing deviation from the master `galaxy_game.md` context must be logged as a permanent entry in `DECISIONS.md` before work is handed off.

### 2. Executor Handoff Mechanics
When assigning a blueprint to an EXECUTOR model, the STRATEGIST must append a **Simple Handoff Template** to the active task file. This template must explicitly map:
1. **Priority Stacking**: Tasks must be ordered sequentially (Task 1, Task 2). Executors are strictly banned from working out-of-order.
2. **Context Injection**: Explicit instructions on which domain guides the executor must pull into their session before writing code.
3. **The Sandbox Constraint**: A reminder that Git is unavailable in the container and raw JSON changes are prohibited.
>>>>>>> Stashed changes

| Task | Agent | Notes |
|---|---|---|
| Architecture decisions / design | Claude (web) | Current session role |
| Task file creation | Claude (web) | Primary task author |
| Agent handoff documents | Perplexity (free) | Or Claude (web) if complex |
| STATUS.md / DECISIONS.md updates | qwen3.5:9b | Fast, low complexity |
| Session handoff summaries | qwen3.5:9b | Lightweight markdown |
| Code implementation | qwen3.5:27b | Primary executor |
| Multi-file reasoning | qwen3.5:27b | Proven reliable |
| Spec writing / debugging | qwen3.5:27b | With terminal verification |
| Complex implementation (27B stuck) | qwen3.5:35b-a3b (Ryzen) | Only when Ryzen available |
| Any task after two local failures | Claude Haiku 4.5 (Copilot) | 0.33x escalation |

---

## Ollama Configuration Rules

**Base tags only for Copilot routing.** Do not bake `num_ctx` into Modelfiles — Copilot negotiates context length dynamically and explicit `num_ctx` settings cause response length errors on remote connections.

### Permitted Modelfile customization (collision avoidance only):
```
ollama run qwen3.5:27b
>>> /set nothink
>>> /save qwen3.5-27b-m4
>>> /bye
```

```
ollama run qwen3.5:35b-a3b
>>> /set nothink
>>> /save qwen3.5-35b-ryzen
>>> /bye
```

```
ollama run qwen3.5:9b
>>> /set nothink
>>> /save qwen3.5-9b-m4
>>> /bye
```

**Rules:**
- `/set nothink` only — suppresses `<think>` token bloat in responses
- No `num_ctx` — causes Copilot remote access failures
- No system prompt baking — Copilot injects its own
- Machine-suffix tags prevent naming collisions when Intel MacBook routes to both M4 and Ryzen 7
- Ollama Desktop app on M4: context length set to max in app settings (not Modelfile)

---

## Escalation Protocol

### Type 1 — Tool Execution Failure
Symptoms: JSON leaking to output, no response, tool calls not completing.

<<<<<<< Updated upstream
1. **Fresh local session first** — open new session with same task file. Do NOT retry in same session.
2. If fresh session also fails → escalate to Claude Haiku 4.5 (Copilot).
=======
**Escalation steps (in order):**
1. **Fresh local session on same machine** — tool failures in long sessions often resolve in clean context
2. **Try other local machine** — if M4 failing, try Ryzen. If Ryzen failing, try M4
3. If both local machines fail → escalate to 0.33x cloud agent (Haiku, GPT-5 mini, or Raptor mini)
>>>>>>> Stashed changes

### Type 2 — Task Complexity Failure
Symptoms: Wrong output, logic errors, spec failures, misunderstood requirements.

<<<<<<< Updated upstream
1. **Tighten the task file** — add explicit before/after examples, narrow scope, add stop conditions. Retry once in fresh local session.
2. If second attempt fails → try qwen3.5:35b-a3b on Ryzen if available.
3. If still failing → escalate to Claude Haiku 4.5 (Copilot).

**Never retry a tool execution failure in the same session. Never escalate a complexity failure without first trying a tighter task file.**
=======
**Escalation steps (in order):**
1. **Tighten the task file** — add explicit before/after code examples, narrow scope, add stop conditions
2. **Retry on Ryzen 35B** — larger model handles complexity better than 27B
3. If Ryzen 35B also fails → escalate to 0.33x cloud agent

**Rule: Never retry a tool execution failure in the same session. Never escalate a complexity failure without first trying the Ryzen 35B.**
>>>>>>> Stashed changes

---

## Models Under Evaluation

<<<<<<< Updated upstream
The following models are installed but not yet validated for Galaxy Game routing:
=======
### Qwen3.5 (all sizes) — git mv
Qwen does not reliably use `git mv` for task file moves. It creates a copy in the destination and leaves the original in place, resulting in duplicate task files.
>>>>>>> Stashed changes

| Model | Machine | Potential Role | Status |
|---|---|---|---|
| `deepseek-coder-v2:16b` | M4 | Code-focused tasks? | Untested |
| `deepseek-r1:14b` | Ryzen 7 | Complex reasoning? | Untested |
| `deepseek-r1:8b` | Ryzen 7 | Light reasoning? | Untested |
| `qwen3:8b` | Ryzen 7 | Fast strategist alternative? | Untested |
| `codestral:latest` | M4 | Code completion? | Untested in current setup |

Routing table will be updated as models are validated against real Galaxy Game tasks.

<<<<<<< Updated upstream
---
=======
### Qwen3.5 (all sizes) — Long session instability
Sessions running longer than one major implementation task accumulate context and become prone to tool execution failures.
>>>>>>> Stashed changes

## ⚠️ Known Agent Behavior Issues

<<<<<<< Updated upstream
### qwen3.5:27b — git mv
Does not reliably use `git mv` for task file moves. Creates a copy in destination, leaves original in place.
=======
### All agents — spec file recreation
Agents under pressure (repeated failures, syntax errors) may attempt to recreate spec files from scratch via Python or heredoc rather than editing in place.
>>>>>>> Stashed changes

**Mitigation:** Task files must include explicit `git mv` with full source and destination paths. Human must run `git status` after every session that moves task files. If duplicate found: `git rm -f` the stale copy, commit manually on host.

<<<<<<< Updated upstream
### qwen3.5:27b — Long session instability
Sessions running longer than one major implementation task accumulate context and become prone to tool execution failures.
=======
### Qwen3.5-35B (Ryzen) — New, behavior under observation
First session confirmed working (2026-06-15). Monitor for:
- git mv behavior (likely same copy-instead-of-move pattern as 27B)
- Context window behavior on very large files
- ROCm/RDNA 4 stability during long sessions

---
>>>>>>> Stashed changes

**Mitigation:** One task per session. If session produced large implementation (100+ lines), start fresh for next task.

<<<<<<< Updated upstream
### All agents — Spec file recreation
Agents under pressure may attempt to recreate spec files from scratch rather than editing in place.

**Mitigation:** Task files must include explicit stop condition: "Do not recreate this file from scratch. Edit in place only." If agent attempts recreation, halt and escalate.
=======
**⚠️ TESTED FINDINGS (2026-06-15):**
- **Ryzen + Qwen3.5-35B (16GB VRAM)**: ✅ Confirmed working — primary heavy executor
- **Copilot + Qwen3.5 (both 9B & 27B on M4)**: ✅ Tools work correctly
- **Copilot + Qwen2.5 (14B & 7B)**: ❌ Tool format incompatibility (outputs JSON, Copilot doesn't execute)
- **Continue**: ❌ All tools disabled by design (text-only mode)
- **DeepSeek-R1 8B (Ryzen, old card)**: ⚠️ Retired — 8192 token context limit too small for workflow tasks
- **Claude Haiku 4.5 (GitHub Copilot)**: ✅ Confirmed working as 0.33x cloud fallback

| Agent | System | Cost | When to Use | Terminal Access |
|---|---|---|---|---|
| **Qwen3.5-35B (Copilot)** | Ryzen 7 | Free/local | **Primary Heavy Executor** — complex implementation, multi-file work, full RSpec analysis | ✅ Yes |
| **Qwen3.5-9B (Copilot)** | M4 | Free/local | **Fast Strategist / Planner** — triage, planning, doc updates, task file creation | ✅ Yes |
| **Qwen3.5-27B (Copilot)** | M4 | Free/local | **Light Executor** — fallback when Ryzen unavailable, single file targeted edits | ✅ Yes |
| ~~Qwen2.5 variants~~ | Either | Free/local | ~~Code implementation~~ **DO NOT USE — TOOL FORMAT BROKEN** | ❌ |
| **Claude Haiku 4.5 (Copilot)** | Cloud | 0.33x Tier | **Cloud fallback** — focused fixes after two local failures | ✅ IDE only |
| **GPT-5-mini (Copilot)** | Cloud | 0.33x Tier | **Cloud fallback** — mechanical code & migrations after two local failures | ✅ IDE only |
| **Raptor mini (Copilot)** | Cloud | 0.33x Tier | **Cloud fallback** — quick logic blocks and specs after two local failures | ✅ IDE only |
| Claude (web) | Cloud | Free tier | Session strategy, architecture decisions, task file drafting | ❌ No local |
| Gemini (web) | Cloud | Free | Macro planning, game-balance advice, domain expertise | ❌ No |
| Perplexity (web) | Cloud | Free | Research, task validation | ❌ No |
>>>>>>> Stashed changes

---

## Hard Rules

<<<<<<< Updated upstream
- **Base tags + `/set nothink` only** for Copilot routing. No `num_ctx` in Modelfiles.
- **Machine-suffix tags** (`-m4`, `-ryzen`) to prevent naming collisions.
- **One task per local session** — do not chain implementation tasks in a single session.
- **No in-place file recreation** — agents must edit files in place, never recreate from scratch.
- **git mv required** for task file moves. Human verifies with `git status` after every session.
- **Fresh session before escalating** — tool execution failures almost always resolve in clean context.
- **Cloud agents after two local failures** — not as default routing.
- **No codebase-wide scans to cloud agents** — snippets only.
- **One RSpec runner at a time** — never run parallel spec execution inside Docker.
- **Supervised host-only version control** — all Git operations on Intel MacBook host, not inside Docker.
- **No raw JSON data commits** — version control for source code, specs, and docs only.
=======
| Task | System | Agent | Role | Notes |
|---|---|---|---|---|
| **Heavy implementation / multi-file work** | Ryzen | Qwen3.5-35B | **EXECUTOR (Heavy)** | Primary executor — use first for complex tasks |
| **Full RSpec suite analysis** | Ryzen | Qwen3.5-35B | **EXECUTOR (Heavy)** | 128GB RAM handles full output without truncation |
| **Triage overnight failures / build task files** | M4 | Qwen3.5-9B | **STRATEGIST** | Fast, proven tool support |
| **Verify workspace paths** | M4 | Qwen3.5-9B | **STRATEGIST** | Terminal-verified only |
| **Syntax fix / single file edit** | M4 | Qwen3.5-9B or 27B | **EXECUTOR (Light)** | Fresh session if prior session ran long |
| **Tool execution failure in long session** | Either | Fresh session same machine, then other machine | **EXECUTOR** | Do NOT retry in same session |
| **Architecture decision / multi-file design** | Ryzen | Qwen3.5-35B | **REVIEWER / EXECUTOR** | Larger context window advantage |
| **Mechanical code (after 2 local failures)** | Cloud | Haiku / GPT-5-mini / Raptor mini | **EXECUTOR (Cloud)** | Both systems must have failed first |
| **Fix a single spec — cause known** | M4 | Qwen3.5-9B | **EXECUTOR (Light)** | Verified working |
| **Fix a single spec — cause unknown** | Ryzen | Qwen3.5-35B | **EXECUTOR (Heavy)** | Deep reasoning + terminal tools |
| **Write docs / update task tracking** | M4 | Qwen3.5-9B | **EXECUTOR (Light)** | Use Qwen3.5, not Qwen2.5 |
| **Macro planning / game balance** | Web | Gemini | **DOMAIN EXPERT** | No local access needed |
| **Session strategy / task file drafting** | Web | Claude (web) | **REVIEWER** | This session role |
| **Daytime heavy task (M4 in use for work)** | Ryzen (remote) | Qwen3.5-35B via M4 Copilot | **EXECUTOR (Heavy)** | Connect M4 Copilot to Ryzen Ollama remotely |

---

## Remote Ollama Connection (M4 → Ryzen)

When M4 needs to use Ryzen Ollama during work hours:

**On Ryzen (Windows):** Ensure Ollama is configured with:
```
OLLAMA_HOST=0.0.0.0
```
Ollama listens on port 11434 on all interfaces.

**On M4 Copilot:** Point to Ryzen IP:
```
http://[ryzen-local-ip]:11434
```

**Open WebUI (Pi — when reloaded):** Aggregates both endpoints:
- Ryzen: `http://[ryzen-ip]:11434`
- M4: `http://[m4-ip]:11434`
Configure in Open WebUI admin → Connections after Pi reload.
>>>>>>> Stashed changes

---

## Pending Infrastructure

<<<<<<< Updated upstream
- **Pi 4 reload** — will host Open WebUI as unified Ollama aggregator
- **Open WebUI** — will provide cleaner multi-backend routing (M4 + Ryzen 7), resolving naming collision concerns permanently
- **Multi-model parallel execution** — 27B on M4 + 35B on Ryzen simultaneously, untested, deferred until Pi/WebUI deployed
- **35B full validation** — results from 2026-06-16 unattended test run pending review
=======
- **⚠️ Qwen2.5 Tool Incompatibility (Copilot)**: Qwen2.5 models (7B, 14B, 32B) output tool-calls in a format that Copilot doesn't recognize/execute. Do NOT use in active routing.
- **Qwen3.5 Only for Copilot Terminal Tools**: Qwen3.5-9B, 27B, and 35B support terminal execution in Copilot. These are the ONLY local Copilot agents confirmed working with `run_in_terminal` tool.
- **Ryzen First for Heavy Tasks**: Complex implementation, multi-file work, and full RSpec analysis go to Ryzen 35B first — not M4 27B.
- **Both Systems Must Fail Before Cloud**: Do not escalate to 0.33x cloud agents until both Ryzen and M4 local sessions have failed on the same task.
- **Daytime M4 Constraint**: During work hours, M4 is reserved for non-galaxyGame work. Route heavy galaxyGame tasks to Ryzen remote or defer to evening.
- **Continue is Experimental/Deprecated**: Continue disables terminal execution for all models. Keep for architecture analysis only.
- **The Verified Execution Rule**: Local models MUST use terminal tools to verify files and run live tests — never fabricate output.
- **Role Isolation**: The **STRATEGIST** generates and coordinates the plan. The **EXECUTOR** acts on existing task blueprints and never self-assigns tasks.
- **One Task Per Local Session**: Do not chain implementation tasks in a single Qwen session regardless of which machine. Long sessions cause tool execution instability.
- **No In-Place File Recreation**: Agents must edit files in place. Recreating spec or service files from scratch is prohibited.
- **git mv Required for Task File Moves**: Agents must use `git mv` to move task files. Human must verify with `git status` after every session that moves task files.
- **Scrub Your Templates**: Ensure all generated task files use `TASK_TEMPLATE.md` exactly.
- All codebase-wide scans go to local models — never send the full codebase to cloud agents.
- One RSpec runner at a time — never run parallel spec execution inside Docker.
- **Supervised Host-Only Version Control**: All git operations run on Intel MacBook host. Never inside Docker.
- **Data Commit Restriction**: Staging or committing raw JSON data files is strictly banned. Version control is reserved for source code, tests, and documentation.
- **Docker Working Directory**: The `web` container working directory is already `/home/galaxy_game`. Do NOT prefix docker exec commands with `cd /home/galaxy_game &&` — it is redundant.
>>>>>>> Stashed changes
