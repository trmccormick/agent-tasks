# Routing Logic — Quick Reference
**Last Updated**: 2026-06-16

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
| **M4 Mac** (48GB unified) | 48GB | Apple Silicon | Primary WVU/Samvera work machine. Runs Ollama agents concurrently but memory is shared with work tasks. |
| **Ryzen 7** (128GB RAM) | 128GB | 16GB (primary) + 4GB (DVI display) | Gaming rig and model server. 16GB GPU handles model inference; 4GB card drives secondary display. Available during the day; gaming and model inference can run simultaneously. |
| **Pi 4** | — | — | Home server (Jellyfin, Samba via Docker). **Pending reload** — will host Open WebUI as unified Ollama aggregator. |

---

## The One Rule

**Route by task complexity, not by habit. 27B handles most things. Only reach for a larger or cloud model when there's a genuine reason.**

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

---

## Quick Routing Table

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

1. **Fresh local session first** — open new session with same task file. Do NOT retry in same session.
2. If fresh session also fails → escalate to Claude Haiku 4.5 (Copilot).

### Type 2 — Task Complexity Failure
Symptoms: Wrong output, logic errors, spec failures, misunderstood requirements.

1. **Tighten the task file** — add explicit before/after examples, narrow scope, add stop conditions. Retry once in fresh local session.
2. If second attempt fails → try qwen3.5:35b-a3b on Ryzen if available.
3. If still failing → escalate to Claude Haiku 4.5 (Copilot).

**Never retry a tool execution failure in the same session. Never escalate a complexity failure without first trying a tighter task file.**

---

## Models Under Evaluation

The following models are installed but not yet validated for Galaxy Game routing:

| Model | Machine | Potential Role | Status |
|---|---|---|---|
| `deepseek-coder-v2:16b` | M4 | Code-focused tasks? | Untested |
| `deepseek-r1:14b` | Ryzen 7 | Complex reasoning? | Untested |
| `deepseek-r1:8b` | Ryzen 7 | Light reasoning? | Untested |
| `qwen3:8b` | Ryzen 7 | Fast strategist alternative? | Untested |
| `codestral:latest` | M4 | Code completion? | Untested in current setup |

Routing table will be updated as models are validated against real Galaxy Game tasks.

---

## ⚠️ Known Agent Behavior Issues

### qwen3.5:27b — git mv
Does not reliably use `git mv` for task file moves. Creates a copy in destination, leaves original in place.

**Mitigation:** Task files must include explicit `git mv` with full source and destination paths. Human must run `git status` after every session that moves task files. If duplicate found: `git rm -f` the stale copy, commit manually on host.

### qwen3.5:27b — Long session instability
Sessions running longer than one major implementation task accumulate context and become prone to tool execution failures.

**Mitigation:** One task per session. If session produced large implementation (100+ lines), start fresh for next task.

### All agents — Spec file recreation
Agents under pressure may attempt to recreate spec files from scratch rather than editing in place.

**Mitigation:** Task files must include explicit stop condition: "Do not recreate this file from scratch. Edit in place only." If agent attempts recreation, halt and escalate.

---

## Hard Rules

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

---

## Pending Infrastructure

- **Pi 4 reload** — will host Open WebUI as unified Ollama aggregator
- **Open WebUI** — will provide cleaner multi-backend routing (M4 + Ryzen 7), resolving naming collision concerns permanently
- **Multi-model parallel execution** — 27B on M4 + 35B on Ryzen simultaneously, untested, deferred until Pi/WebUI deployed
- **35B full validation** — results from 2026-06-16 unattended test run pending review
