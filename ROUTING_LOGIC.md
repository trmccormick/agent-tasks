# Routing Logic — Quick Reference
**Last Updated**: 2026-06-17

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.
> This file is a quick-reference summary only.

---

## ⚠️ CURRENT STATE NOTICE (June 2026)

This document reflects a transitional setup following new hardware addition (Ryzen 7 16GB GPU) and GitHub Copilot model changes. The Pi 4 home server reload and Open WebUI aggregator deployment are **pending** — routing will be revisited once that infrastructure is live.

**2026-06-23 update**: qwen3.6 models deployed on both systems. M4: qwen3.6:27b (primary), Ryzen 7: qwen3.6:35b (primary for heavy work). Both validated as drop-in replacements for qwen3.5 with improved reasoning. Retiring qwen3.5:27b and qwen3.5:35b-a3b as primary executors.

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

**Route by task complexity, not by habit. 27B handles most everyday tasks on M4. Use 35B on Ryzen for heavy/complex work when available. Only reach for cloud when both local machines fail.**

---

## Dual Workstream Setup

The agent-tasks repo coordinates two concurrent workstreams across machines:

| Workstream | Primary Machine | Primary Agent | Repo |
|---|---|---|---|
| **Galaxy Game** | Intel MacBook (VS Code + Copilot) | qwen3.5:27b via M4 or qwen3.5:35b via Ryzen 7 | galaxygame |
| **WVU/Samvera** | M4 Mac (VS Code + Copilot) | qwen3.5:27b local | wvu_knapsack |

These workstreams are independent — task files in agent-tasks are namespaced by repo. Agents working on Galaxy Game tasks should never touch wvu_knapsack files and vice versa.

---

## Active Agent Stack (June 2026)

| Agent | Where | Role | Cost |
|---|---|---|---|
| **Claude (web)** | claude.ai | Planning, architecture, task file creation, session strategy | Free tier |
| **Gemini (web)** | gemini.google.com | Brainstorming, documentation, game balance / domain expertise, macro planning | Free |
| **ChatGPT (web)** | chatgpt.com | Image generation (game assets, UI concepts, world-building visuals) | Free tier |
| **Perplexity** | perplexity.ai | Agent handoff documents, external research | Free |
| **qwen3.6:27b** | M4 & Ryzen 7 (both have it) | Primary implementation executor — improved reasoning over 3.5 | Free/local |
| **qwen3.6:35b** | M4 & Ryzen 7 (both have it) | Heavy execution, complex multi-file work — validated 2026-06-23 | Free/local |
| **qwen3.6:9b** | M4 (local Ollama) | Fast lightweight tasks, planning support | Free/local |
| **Claude Haiku 4.5** | GitHub Copilot (0.33x) | Escalation when local gets stuck | 0.33x tier |

---

## Session Roles

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Claude (web) | Planning, architecture decisions, task file drafting, session strategy |
| **BRAINSTORM / DOMAIN EXPERT** | Gemini (web) | Brainstorming, documentation agent, game balance, geological/simulation expertise, macro planning |
| **IMAGE GENERATION** | ChatGPT (web) | Game assets, UI concepts, world-building visuals |
| **HANDOFF WRITER** | Perplexity (free) | Agent handoff documents between sessions |
| **EXECUTOR (Primary)** | qwen3.6:27b (M4 or Ryzen) | Implementation, multi-file reasoning, spec work |
| **EXECUTOR (Heavy)** | qwen3.6:35b (M4 or Ryzen) | Complex implementation, heavy reasoning — validated 2026-06-23 |
| **EXECUTOR (Light)** | qwen3.6:9b (M4 only) | Fast tasks, triage, STATUS.md updates |
| **ESCALATION** | Claude Haiku 4.5 (Copilot) | When local models fail after fresh session retry |

---

## Synthesis Review Workflow (New as of 2026-06-23)

**All executable tasks now follow synthesis-gating pattern:**

```
Task Creation (Claude web or Planning agent)
         ↓
Minimal Handoff to Executor (2-4 lines)
         ↓
Executor Reads Task File + Prerequisites
         ↓
Executor Creates STATUS SYNTHESIS REPORT (mandatory)
         ↓
REVIEW GATE (Synthesis posted to chat for approval)
         │
         ├─→ Tier 1 Review (User or Strategist)
         │   • Catches domain/architecture gaps
         │   • Redirects if wrong approach
         │   • Approves or requests changes
         │
         ├─→ Tier 2 Review (Gemini / Perplexity / Claude web if free time)
         │   • Deep architecture/nuance review
         │   • Finds edge cases
         │   • Suggests optimizations
         │
         └─→ Approved ✅
         ↓
Executor Implements (can't deviate from synthesis)
         ↓
Executor Reports Results (with evidence: logs, diffs)
         ↓
Spot-Check (Planning/Review agents verify work)
         ↓
Task Complete → Move to completed/
```

**Why this works**:
- ✅ Prevents fabrication: synthesis gates execution
- ✅ Enables collaborative review: multiple agents catch gaps at synthesis stage
- ✅ Clear alignment: everyone agrees on approach BEFORE coding
- ✅ Catches tier mismatches: lower-tier agents prove understanding before execution

---

## Quick Routing Table

| Task | Agent | Notes |
|---|---|---|
| **Task file creation** | Claude (web) or Strategist | Primary task author — comprehensive spec with gotchas + synthesis template |
| **Task synthesis review** | User + Tier 2 reviewers | Gemini, Perplexity, Claude web (if free time) — architecture/edge case review |
| **Architecture decisions** | Claude (web) | Strategic planning |
| **Agent handoff documents** | Perplexity (free) | Or Claude (web) if complex |
| **STATUS.md / DECISIONS.md updates** | qwen3.6:9b | Fast, low complexity |
| **Session handoff summaries** | qwen3.6:9b | Lightweight markdown |
| **Code implementation** | qwen3.6:27b | Primary executor (improved reasoning) |
| **Multi-file reasoning** | qwen3.6:27b | Proven reliable |
| **Spec writing / debugging** | qwen3.6:27b | With terminal verification |
| **Complex implementation** | qwen3.6:35b (Ryzen) | Heavy tasks, complex multi-file work |
| **Spot-check completed work** | Gemini (web) or Planning agent | Post-execution review, catch issues before merge |
| **Any task after two local failures** | Claude Haiku 4.5 (Copilot) | 0.33x escalation |

---

## Ollama Configuration Rules

**Base tags only for Copilot routing.** Do not bake `num_ctx` into Modelfiles — Copilot negotiates context length dynamically and explicit `num_ctx` settings cause response length errors on remote connections.

### Permitted Modelfile customization (collision avoidance only):
```
ollama run qwen3.6:27b
>>> /set nothink
>>> /save qwen3.6-27b-m4
>>> /bye
```

```
ollama run qwen3.6:35b
>>> /set nothink
>>> /save qwen3.6-35b-ryzen
>>> /bye
```

```
ollama run qwen3.6:9b
>>> /set nothink
>>> /save qwen3.6-9b-m4
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

### qwen3.6:27b — git mv
Does not reliably use `git mv` for task file moves. Creates a copy in destination, leaves original in place (inherited from qwen3.5).

**Mitigation:** Task files must include explicit `git mv` with full source and destination paths. Human must run `git status` after every session that moves task files. If duplicate found: `git rm -f` the stale copy, commit manually on host.

### qwen3.6 models — Long session instability
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

- **Pi 4 reload** — needed for Raspbian version upgrade and general cleanup. Jellyfin and Samba will be restored. Not blocking any current workflow.
- **Open WebUI** — post-reload decision. Originally considered as Ollama aggregator with session memory/database persistence. May not be necessary given current topology — Intel MacBook can point directly at Ryzen 7 Ollama endpoint without Pi in the middle.
- **Multi-model parallel execution** — 27B on M4 + 35B on Ryzen simultaneously, **validated 2026-06-17** in live session (storyline agent on 35B/Ryzen, planning agent on 27B/M4, no collision).
- **35B full validation** — confirmed working 2026-06-17. Galaxy Game storyline/phase reconciliation tasks completed successfully on Ryzen 7 35B. No blocking issues found.

## Ollama Endpoint Topology

| Machine | Primary Ollama | At Home Addition | Never |
|---|---|---|---|
| **M4 Mac** | localhost (27B, 9B) — mobile, works anywhere | Can optionally hit Ryzen 7 for heavy tasks | Do not depend on Pi — M4 travels |
| **Intel MacBook** | Ryzen 7 direct (no local models) | Pi/WebUI if deployed | — |
| **Ryzen 7** | localhost (35B) — serves remote requests | — | — |
